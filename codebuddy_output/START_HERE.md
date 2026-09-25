# 👋 Start Here — Galaxium Travels

> **You are in the right place.** Read this first. It takes about 2 minutes.
> When you're done, follow the link at the bottom for the detailed guide.

---

## 1. The Big Picture

Imagine a travel agency that books flights — but to other planets.
Galaxium Travels is that agency, built as a software project.
The part you're working on is the invisible engine behind the scenes:
it remembers customers, finds available flights, calculates prices, and saves bookings.
Think of it as the kitchen of a restaurant — the customer never walks in, but every meal comes from there.

---

## 2. What Happens When Someone Uses It

Here's the story of one booking, told in plain English:

1. A traveller visits the website and picks a seat on the Mars Express.
2. The website sends that choice to the engine (that's the code you're looking at).
3. The engine checks: does this person exist? Is there a seat free? What's the price?
4. If everything checks out, the engine saves the booking and says "you're confirmed!"
5. The traveller gets back a booking number and the price they paid.

No jargon needed — that's really all it does.

---

## 3. Look at Only These 3 Files First

- `booking_system_backend/server.py` — this is the front door; every request comes in here first.
- `booking_system_backend/services/booking.py` — this is where the "is a seat available?" decision actually happens.
- `booking_system_backend/schemas.py` — this defines what information the app expects to receive and send back.

---

## 4. Five Words You'll Hear

- **Endpoint** — a specific address the app responds to, like a phone extension (e.g. "press 3 for bookings").
- **Schema** — a description of the shape of data, like the blank fields on a paper form.
- **Model** — a description of how data is stored, like the columns in a spreadsheet.
- **Service** — a file that contains the actual rules and decisions, like a policy manual.
- **Database** — where all the data lives permanently, like a filing cabinet that never forgets.

---

## 5. Your Next Step

**Try this:** Open `booking_system_backend/schemas.py` and find the `UserCreate` class.
Notice what fields it has — `name` and `email`. That's the "form" a new user fills in to register.

Ready for more? The full guide is here:
👉 **[ONBOARDING_GUIDE.md](./ONBOARDING_GUIDE.md)**
