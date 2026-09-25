> **New here? Read [START_HERE.md](./START_HERE.md) first.**

# 🚀 Galaxium Travels — Backend Onboarding Guide

> **Who this guide is for:** A first-year student who has never worked on a real project before.
> Every technical term is explained in plain English. Take your time — there's no rush!

---

## What This Project Does

Galaxium Travels is a pretend flight-booking app — but instead of booking flights to New York or Paris,
you book interplanetary flights to places like Mars or Europa! You can pick your seat class
(economy, business, or the fancy "galaxium" class), and the system figures out the price,
checks if seats are available, and saves your booking. The backend (the part you're working on)
is the engine running behind the scenes: it doesn't have a pretty interface itself, but it
answers questions and processes requests from the website and from AI assistants.
Think of it like the kitchen of a restaurant — the customer never sees it, but every meal comes from there.

---

## Map of Important Files

> **What is a file in this context?** Each file is like a chapter in a textbook — it handles one specific job so the code stays organised and easy to find.

| File | What it does | Analogy |
|---|---|---|
| [`server.py`](../booking_system_backend/server.py) | The main entry point — starts the app, defines all the URL addresses the app responds to, and connects everything together | Like the front desk of a hotel: it receives every request and sends it to the right department |
| [`models.py`](../booking_system_backend/models.py) | Describes the shape of data stored in the database: User, Flight, and Booking | Like the blank forms at a doctor's office — they define what information must be filled in |
| [`schemas.py`](../booking_system_backend/schemas.py) | Describes what data looks like when it travels in and out of the app (over the internet) | Like the labels on a package — they describe what's inside and in what format |
| [`db.py`](../booking_system_backend/db.py) | Sets up the connection to the database and provides a "session" (a conversation with the database) | Like dialling into a phone call with the database so you can ask it questions |
| [`seed.py`](../booking_system_backend/seed.py) | Fills the database with pretend demo data (flights, users) every time the app starts | Like stocking the shelves before a store opens |
| [`services/booking.py`](../booking_system_backend/services/booking.py) | The brain of booking logic: check seats, calculate price, save the booking | Like a booking agent who checks availability, quotes a price, and issues a ticket |
| [`services/flight.py`](../booking_system_backend/services/flight.py) | Searches and filters flights based on your criteria (origin, price, departure date, etc.) | Like a travel agent who searches through a catalogue to find flights that match what you asked for |
| [`services/user.py`](../booking_system_backend/services/user.py) | Registers new users and looks up existing ones | Like the membership desk at a gym — it signs you up and checks your record |
| [`tests/conftest.py`](../booking_system_backend/tests/conftest.py) | Sets up a pretend in-memory database used only during testing (so real data is never touched) | Like a rehearsal stage that looks exactly like the real stage but nothing on it is real |
| [`tests/test_rest.py`](../booking_system_backend/tests/test_rest.py) | Automated tests that check whether the URL endpoints (REST API) respond correctly | Like a quality-control inspector who sends requests and checks the answers |
| [`tests/test_services.py`](../booking_system_backend/tests/test_services.py) | Automated tests that check whether the business logic (services) works correctly | Like a separate inspector who tests the kitchen recipes directly, without going through the front desk |
| [`requirements.txt`](../booking_system_backend/requirements.txt) | Lists every external library the project depends on | Like an ingredient list for a recipe |
| [`Dockerfile`](../booking_system_backend/Dockerfile) | Instructions for packaging the whole app into a portable container (like a shipping crate) | Like the instructions for packing a moving box so it can run anywhere |

---

## The Data: What the App Keeps Track Of

The app stores three main **entities** (think of each as a row in a spreadsheet):

### 👤 User
| Field | Type | Meaning |
|---|---|---|
| `user_id` | number | Unique ID assigned automatically |
| `name` | text | Full name of the traveller |
| `email` | text | Email address (must be unique; stored in lowercase) |

### ✈️ Flight
| Field | Type | Meaning |
|---|---|---|
| `flight_id` | number | Unique ID |
| `origin` | text | Departure planet/moon (e.g. "Earth") |
| `destination` | text | Arrival planet/moon (e.g. "Mars") |
| `departure_time` | text | Date and time of departure (format: "YYYY-MM-DD HH:MM") |
| `arrival_time` | text | Date and time of arrival |
| `base_price` | number | Economy price in Galaxium Credits |
| `economy_seats_available` | number | Seats left in economy class |
| `business_seats_available` | number | Seats left in business class |
| `galaxium_seats_available` | number | Seats left in galaxium (premium) class |

**Price multipliers by seat class:**
- Economy: 1.0× base price
- Business: 2.5× base price
- Galaxium: 5.0× base price

### 🎫 Booking
| Field | Type | Meaning |
|---|---|---|
| `booking_id` | number | Unique ID |
| `user_id` | number | Which user made this booking |
| `flight_id` | number | Which flight was booked |
| `status` | text | `"booked"`, `"cancelled"`, or `"completed"` |
| `booking_time` | text | Timestamp when the booking was made |
| `seat_class` | text | `"economy"`, `"business"`, or `"galaxium"` |
| `price_paid` | number | Actual price charged (base × multiplier) |

---

## How a Request Travels Through the Code

Here is what happens step-by-step when someone books a flight. Think of it like following a package through a sorting centre:

```
🌐  User sends:  POST /book  { user_id: 1, name: "Ada", flight_id: 3, seat_class: "economy" }
        │
        ▼
📬  server.py  — receives the request, checks the shape of the data (schema)
        │
        ▼
🧠  services/booking.py → book_flight()
        ├─ Is the seat_class valid? (economy / business / galaxium)
        ├─ Does the flight exist in the database?
        ├─ Are there seats available in that class?
        ├─ Does the user exist AND does the name match?  ← security check!
        ├─ Calculate price = base_price × multiplier
        ├─ Subtract 1 from available seats
        ├─ Create a new Booking record
        └─ Save it to the database (db.commit)
        │
        ▼
📦  Returns BookingOut (a tidy Pydantic object)
        │
        ▼
🌐  User receives:  JSON  { booking_id: 42, status: "booked", price_paid: 150, ... }
```

---

## REST Endpoints (the "addresses" the app responds to)

> **What is an endpoint?** An endpoint is like a specific phone extension at a company. You dial the main number (the server) and then the extension (the path) to reach the right department.

| Method | Path | What it does |
|---|---|---|
| `GET` | `/` | Health check — "is the app running?" |
| `GET` | `/flights` | List flights, with optional filters (origin, price, date…) |
| `POST` | `/book` | Book a seat on a flight |
| `GET` | `/bookings/{user_id}` | Get all bookings for a specific user |
| `POST` | `/cancel/{booking_id}` | Cancel a booking |
| `POST` | `/register` | Register a new user |
| `GET` | `/user` | Look up a user by name and email |

---

## Glossary

| Term | Plain English meaning |
|---|---|
| **Backend** | The part of an app that runs on a server, not visible to the user — handles logic, data, and rules |
| **Frontend** | The part of an app the user sees and interacts with (buttons, forms, pages) |
| **API** (Application Programming Interface) | A set of agreed "questions and answers" that two programs use to talk to each other |
| **REST API** | A very common style of API that uses web addresses (URLs) and HTTP methods like GET and POST |
| **Endpoint** | A specific URL address that the server responds to, like a phone extension for one department |
| **HTTP Method** | The "action word" in a request — `GET` means "fetch data", `POST` means "send new data" |
| **Database** | An organised store of data, like a very powerful spreadsheet that can be searched and updated instantly |
| **SQLite** | A simple database that lives in a single file on disk — used here for local development |
| **ORM** (Object-Relational Mapper) | A tool that lets you work with database rows as if they were Python objects, instead of writing raw SQL |
| **SQLAlchemy** | The ORM library this project uses to talk to the database |
| **Model** | A Python class that describes one "table" in the database (e.g. `User`, `Flight`, `Booking`) |
| **Schema** | A description of the shape of data travelling in or out of the app, used for validation |
| **Pydantic** | A Python library that enforces schemas — it checks that incoming data has the right fields and types |
| **Service layer** | A group of functions that contain the business logic (rules), separated from the web layer |
| **Dependency** | A library or piece of code that another piece of code relies on to work |
| **Session** | A temporary connection to the database; opened when needed and closed when done |
| **Seed data** | Fake starting data loaded into the database so developers have something to work with immediately |
| **MCP** (Model Context Protocol) | A way for AI assistants to call functions in your app, just like a REST API but for AI tools |
| **FastAPI** | The Python web framework (toolkit) used to build the REST API |
| **FastMCP** | The library used to turn FastAPI endpoints into MCP tools automatically |
| **Union type** | A function return type that can be one of two things — e.g. `BookingOut | ErrorResponse` means "either a booking OR an error" |
| **pytest** | The testing framework used to run automated tests |
| **fixture** | A reusable setup step in a test (e.g. creating a fresh database before each test) |
| **StaticPool** | A SQLAlchemy setting that keeps a single in-memory database connection alive for the entire test run |
| **lifespan** | A FastAPI feature that runs setup code when the app starts and cleanup code when it stops |
| **Dockerfile** | A recipe for building a Docker container (a portable, self-contained package of the app) |
| **venv** | A "virtual environment" — an isolated Python installation so project dependencies don't clash with other projects |

---

## Setup Instructions

Follow these steps exactly. Each one builds on the previous.

**Step 1 — Go into the backend folder.**
This changes your terminal's "current location" to the backend directory.
```bash
cd booking_system_backend
```

**Step 2 — Create a virtual environment (a private Python workspace for this project).**
This keeps the project's libraries separate from anything else on your computer.
```bash
python3 -m venv .venv
```

**Step 3 — Activate the virtual environment.**
Think of this as "stepping into" your private workspace. Your terminal prompt will change to show `(.venv)`.
```bash
source .venv/bin/activate
```

**Step 4 — Install all the project's dependencies.**
This reads `requirements.txt` and downloads every library the project needs.
```bash
pip install -r requirements.txt
```

**Step 5 — Start the server.**
This runs the app. It will listen on port 8001. Open your browser and visit `http://localhost:8001` to see it running.
```bash
python server.py
```

> ✅ You should see the server print something like `Uvicorn running on http://0.0.0.0:8001`.
> Visit `http://localhost:8001/docs` in your browser to see an interactive list of all endpoints — great for exploring!

---

## ⚠️ Gotchas for New Contributors

These are the most important "watch out!" warnings. Each one has tripped up experienced developers — don't worry if they seem tricky at first.

1. **Demo data reloads every restart** — the app seeds fake flights and users every time it starts. If you add data and restart, it'll be reset. Set the `SEED_DEMO_DATA` environment variable to `false` to stop this.

2. **Name AND user_id must match when booking** — when you call `/book`, the `name` field must match the name registered for that `user_id`. This is intentional. If they don't match, the booking is rejected.

3. **Error checking uses the body, not HTTP status** — some endpoints return HTTP 200 (which usually means "success") even when something went wrong. Always check whether the response JSON contains an `error` field.

4. **Tests use a special fake database** — the test setup patches two places (`db.SessionLocal` AND `server.SessionLocal`). If you add a new MCP tool, make sure it uses `SessionLocal()` directly (not `Depends(get_db)`).

5. **Don't delete `booking.db` or `holds.db`** — these files are intentionally committed to the repository and are used to seed local development data.

6. **Python dependencies live inside `.venv/`** — always activate the virtual environment (`source .venv/bin/activate`) before running any Python commands.

---

## Your First Beginner Task

### ✏️ Add a `phone_number` field to the User registration schema

**What you'll learn:** How Pydantic schemas work — the "contract" for what data the app accepts.

**Why this is a good starter task:** It touches exactly one small file, introduces you to how schemas and models connect, and gives you a visible result you can test immediately.

**What to do:**

1. **Open** [`schemas.py`](../booking_system_backend/schemas.py)

2. **Find** the `UserCreate` class (the schema used when registering a new user).

3. **Add** this line inside the class:
   ```python
   phone_number: str | None = None
   ```
   This means the field is optional — users don't have to provide it. `str | None` means "either a text string or nothing".

4. **Save the file.**

**How to check it worked:**

- Start the server (`python server.py`) and visit `http://localhost:8001/docs` in your browser.
- Click on the `POST /register` endpoint and look at the **Request body** section.
- You should now see `phone_number` listed as an optional field.

**Bonus challenge:** Run the tests to make sure you haven't broken anything:
```bash
cd booking_system_backend
python -m pytest tests/ -v
```
All tests should still pass — because you only added an **optional** field, nothing that was already working will break.

---

*Guide generated by CodeBuddy • Galaxium Travels onboarding • `booking_system_backend/`*
