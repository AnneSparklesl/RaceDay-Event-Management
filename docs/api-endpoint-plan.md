# RaceDay API Endpoint Plan

**Database:** RaceDayDB
**Author:** Annie
**Part of:** RaceDay PoE – Part 1 (Planning)

This document lists the planned REST API endpoints for the RaceDay Event Management system, based on the RaceDayDB schema (User, Venue, Event, Category, Enrolment, Result). Each entity follows standard CRUD conventions. This is a planning artifact only — no API code has been implemented yet.

---

## 1. User

| Method | Route | Purpose |
|--------|-------|---------|
| GET | /api/users | List all users |
| GET | /api/users/{id} | Get a single user by id |
| POST | /api/users | Register a new user (Organiser or Participant) |
| PUT | /api/users/{id} | Update a user's details |
| DELETE | /api/users/{id} | Remove a user |

**Notes:** `role` is constrained to `Organiser` or `Participant`. Registration (POST) should hash the password server-side before storing `password_hash`.

---

## 2. Venue

| Method | Route | Purpose |
|--------|-------|---------|
| GET | /api/venues | List all venues |
| GET | /api/venues/{id} | Get a single venue by id |
| POST | /api/venues | Add a new venue |
| PUT | /api/venues/{id} | Update a venue |
| DELETE | /api/venues/{id} | Remove a venue |

---

## 3. Event

| Method | Route | Purpose |
|--------|-------|---------|
| GET | /api/events | List all events |
| GET | /api/events/{id} | Get a single event by id |
| GET | /api/events/{id}/categories | List all categories for a specific event |
| POST | /api/events | Create a new event (Organiser only) |
| PUT | /api/events/{id} | Update an event |
| DELETE | /api/events/{id} | Remove an event |

**Notes:** `organiser_id` and `venue_id` are foreign keys — creating an Event requires an existing User (Organiser) and Venue.

---

## 4. Category

| Method | Route | Purpose |
|--------|-------|---------|
| GET | /api/categories | List all categories |
| GET | /api/categories/{id} | Get a single category by id |
| POST | /api/categories | Add a category to an event |
| PUT | /api/categories/{id} | Update a category (name, distance) |
| DELETE | /api/categories/{id} | Remove a category |

---

## 5. Enrolment

| Method | Route | Purpose |
|--------|-------|---------|
| GET | /api/enrolments | List all enrolments |
| GET | /api/enrolments/{id} | Get a single enrolment by id |
| GET | /api/users/{id}/enrolments | List all enrolments for a specific participant |
| POST | /api/enrolments | Enrol a participant in a category |
| PUT | /api/enrolments/{id} | Update an enrolment (e.g. change category) |
| DELETE | /api/enrolments/{id} | Cancel an enrolment |

**Notes:** `participant_id` references User; `category_id` references Category. `enrolment_date` defaults to current timestamp.

---

## 6. Result

| Method | Route | Purpose |
|--------|-------|---------|
| GET | /api/results | List all results |
| GET | /api/results/{id} | Get a single result by id |
| GET | /api/events/{id}/results | List all results for a specific event (leaderboard) |
| POST | /api/results | Capture a finish time and position for an enrolment |
| PUT | /api/results/{id} | Update a result |
| DELETE | /api/results/{id} | Remove a result |

**Notes:** `enrolment_id` is unique on Result — each enrolment can have at most one result, enforced at the database level.

---

## Summary

| Entity | Base Route | Relationships |
|--------|-----------|----------------|
| User | /api/users | Referenced by Event (organiser), Enrolment (participant) |
| Venue | /api/venues | Referenced by Event |
| Event | /api/events | References User, Venue → referenced by Category |
| Category | /api/categories | References Event → referenced by Enrolment |
| Enrolment | /api/enrolments | References User, Category → referenced by Result |
| Result | /api/results | References Enrolment (1:1) |
