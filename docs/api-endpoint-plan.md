# RaceDay API Endpoint Plan

**Database:** RaceDayDB
**Author:** Annie
**Part of:** RaceDay PoE – Part 1 (Planning)

This document lists every planned REST API endpoint for the RaceDay Event Management system, based on the RaceDayDB schema (User, Venue, Event, Category, Enrolment, Result). This is a planning artifact 

**Roles:** None = public, no login required. Any = any logged-in user. Organiser / Participant = restricted to that role.

---

## Authentication

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| POST | /api/auth/register | Registers a new user as either an Organiser or a Participant | None | `{ name, email, password, role }` | 201 Created – user id and email returned. 409 Conflict – email already registered. |
| POST | /api/auth/login | Authenticates a user and returns an access token | None | `{ email, password }` | 200 OK – token and user role returned. 401 Unauthorized – invalid email or password. |

---

## User Profile

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/users/me | Retrieves the currently logged-in user's profile | Any | None | 200 OK – user profile returned. 401 Unauthorized – not logged in. |
| PUT | /api/users/me | Updates the currently logged-in user's name or email | Any | `{ name, email }` | 200 OK – updated profile returned. 400 Bad Request – invalid data. |
| DELETE | /api/users/me | Deletes the currently logged-in user's account | Any | None | 204 No Content – account deleted. 401 Unauthorized – not logged in. |

---

## Venues

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/venues | Lists all venues | None | None | 200 OK – array of venues. |
| GET | /api/venues/{id} | Retrieves a single venue by id | None | None | 200 OK – venue returned. 404 Not Found – venue does not exist. |
| POST | /api/venues | Creates a new venue | Organiser | `{ name, address, city }` | 201 Created – new venue returned. 400 Bad Request – missing required fields. |
| PUT | /api/venues/{id} | Updates an existing venue | Organiser | `{ name, address, city }` | 200 OK – updated venue returned. 404 Not Found – venue does not exist. |
| DELETE | /api/venues/{id} | Removes a venue | Organiser | None | 204 No Content – venue deleted. 404 Not Found – venue does not exist. |

---

## Events

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/events | Lists all events | None | None | 200 OK – array of events. |
| GET | /api/events/{id} | Retrieves a single event by id | None | None | 200 OK – event returned. 404 Not Found – event does not exist. |
| GET | /api/events/{id}/categories | Lists all categories belonging to a specific event | None | None | 200 OK – array of categories. 404 Not Found – event does not exist. |
| GET | /api/events/{id}/results | Lists all results for a specific event (leaderboard) | None | None | 200 OK – array of results. 404 Not Found – event does not exist. |
| POST | /api/events | Creates a new event, linking an organiser and a venue | Organiser | `{ venueId, name, eventDate, description }` | 201 Created – new event returned. 404 Not Found – venue does not exist. |
| PUT | /api/events/{id} | Updates an existing event | Organiser | `{ venueId, name, eventDate, description }` | 200 OK – updated event returned. 403 Forbidden – not the owning organiser. 404 Not Found – event does not exist. |
| DELETE | /api/events/{id} | Removes an event | Organiser | None | 204 No Content – event deleted. 403 Forbidden – not the owning organiser. 404 Not Found – event does not exist. |

---

## Categories

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/categories | Lists all categories | None | None | 200 OK – array of categories. |
| GET | /api/categories/{id} | Retrieves a single category by id | None | None | 200 OK – category returned. 404 Not Found – category does not exist. |
| POST | /api/categories | Adds a new category to an event | Organiser | `{ eventId, name, distanceKm }` | 201 Created – new category returned. 404 Not Found – event does not exist. |
| PUT | /api/categories/{id} | Updates an existing category | Organiser | `{ name, distanceKm }` | 200 OK – updated category returned. 404 Not Found – category does not exist. |
| DELETE | /api/categories/{id} | Removes a category | Organiser | None | 204 No Content – category deleted. 404 Not Found – category does not exist. |

---

## Event Enrolments

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/enrolments | Lists all enrolments across all events | Organiser | None | 200 OK – array of enrolments. |
| GET | /api/users/me/enrolments | Lists the logged-in participant's own enrolments | Participant | None | 200 OK – array of the participant's enrolments. |
| POST | /api/enrolments | Enrols the logged-in participant in a category | Participant | `{ categoryId }` | 201 Created – new enrolment returned. 404 Not Found – category does not exist. 409 Conflict – already enrolled in this category. |
| DELETE | /api/enrolments/{id} | Cancels an enrolment (own enrolment, or any if Organiser) | Any | None | 204 No Content – enrolment cancelled. 403 Forbidden – not the enrolment owner. 404 Not Found – enrolment does not exist. |

---

## Results

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/results | Lists all results | None | None | 200 OK – array of results. |
| GET | /api/results/{id} | Retrieves a single result by id | None | None | 200 OK – result returned. 404 Not Found – result does not exist. |
| GET | /api/users/me/results | Lists the logged-in participant's own results | Participant | None | 200 OK – array of the participant's results. |
| POST | /api/results | Captures a finish time and position for an enrolment | Organiser | `{ enrolmentId, finishTime, position }` | 201 Created – new result returned. 404 Not Found – enrolment does not exist. 409 Conflict – a result already exists for this enrolment. |
| PUT | /api/results/{id} | Updates an existing result | Organiser | `{ finishTime, position }` | 200 OK – updated result returned. 404 Not Found – result does not exist. |
| DELETE | /api/results/{id} | Removes a result | Organiser | None | 204 No Content – result deleted. 404 Not Found – result does not exist. |

---

## Summary

| Entity | Base Route | Relationships |
|---|---|---|
| User | /api/users, /api/auth | Referenced by Event (organiser), Enrolment (participant) |
| Venue | /api/venues | Referenced by Event |
| Event | /api/events | References User, Venue → referenced by Category |
| Category | /api/categories | References Event → referenced by Enrolment |
| Enrolment | /api/enrolments | References User, Category → referenced by Result |
| Result | /api/results | References Enrolment (1:1) |
