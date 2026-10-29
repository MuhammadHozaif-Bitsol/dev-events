# Evently

A Next.js app for discovering developer events (hackathons, meetups, conferences). Events are stored in MongoDB, event images are uploaded to Cloudinary, and visitors can book a spot on an event with their email address.

## Tech stack

- Next.js 16 (App Router, server actions, `use cache`, React Compiler)
- React 19, TypeScript
- MongoDB with Mongoose
- Cloudinary (image upload) and next-cloudinary
- PostHog (product analytics and error capture)
- Tailwind CSS 4, shadcn / Radix UI, lucide-react
- ogl (WebGL light-rays background)

## Features

- Home page listing featured events, newest first, cached with `cacheLife("hours")`
- Event detail page at `/events/[slug]` with a list of similar events (events sharing at least one tag)
- Booking form that saves an email for an event via a server action; one booking per email per event is enforced by a unique index, and the event must exist
- REST API:
  - `GET /api/events` - list all events
  - `POST /api/events` - create an event from multipart form data; the `image` file is uploaded to Cloudinary, `tags` and `agenda` are JSON strings
  - `GET /api/events/[slug]` - fetch one event by slug
- Event model generates the slug from the title and normalizes date (`YYYY-MM-DD`) and time (`HH:MM`) on save
- PostHog events: `events_explored`, `event_card_selected`, `event_booked`, plus exception capture in the global error boundary

## Getting started

```bash
npm install
npm run dev      # start the dev server at http://localhost:3000
npm run build    # production build
npm run start    # run the production build
npm run lint     # run ESLint
```

### Environment variables

Copy `.env.example` to `.env` and fill in:

| Name | Purpose |
| --- | --- |
| `MONGODB_URI` | MongoDB connection string |
| `NEXT_PUBLIC_POSTHOG_PROJECT_TOKEN` | PostHog project token (analytics is skipped when unset) |
| `NEXT_PUBLIC_POSTHOG_HOST` | PostHog API host |
| `CLOUDINARY_URL` | Cloudinary credentials for image upload. The code does not call `cloudinary.config()`, so the SDK reads this variable from the environment. |

## Project structure

```
app/
  page.tsx               Home page (featured events)
  events/[slug]/         Event detail page
  api/events/            Event REST routes
components/              EventCard, EventDetails, BookEvent, Navbar, LightRays
database/                Mongoose models (Event, Booking)
lib/
  actions/               Server actions for events and bookings
  mongodb.ts             Cached MongoDB connection
instrumentation-client.ts  PostHog client initialization
```
