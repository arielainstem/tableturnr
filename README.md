# TableTurnr

A flexible reservation and resource management system designed for arbitrary start and end times, admin-defined availability windows, and configurable booking rules.

- [TableTurnr](#tableturnr)
  - [Overview](#overview)
  - [Problem](#problem)
  - [Solution](#solution)
  - [Features](#features)
    - [User Features](#user-features)
    - [Admin Features](#admin-features)
    - [System Features](#system-features)
  - [Architecture](#architecture)
    - [Tech Stack](#tech-stack)
    - [High-Level Structure](#high-level-structure)
  - [Data Model](#data-model)
    - [Resource](#resource)
    - [Reservation](#reservation)
    - [AvailabilityWindow](#availabilitywindow)
    - [Blockout](#blockout)
    - [User](#user)
  - [Core Logic Summary](#core-logic-summary)
  - [Roadmap](#roadmap)
  - [Setup Instructions](#setup-instructions)
  - [License](#license)

## Overview

TableTurnr is a domain-agnostic scheduling system that allows admins to create resources (rooms, tables, lab equipment, studios, etc.) and users to reserve them through a flexible, interval-based booking flow.

The system supports:

- Flexible date and time ranges
- Single-day or multi-day booking modes
- Conflict detection using interval logic
- Admin-controlled availability windows
- Resource categories and metadata
- User dashboards for upcoming reservations

## Problem

Most reservations tools are strictly coupled to their specific industry (restaurants, gyms, labs). They assume fixed time slots, fixed durations, or domain-specific workflows. This makes them difficult to extend, reuse, or adapt to different environments.

## Solution

TableTurnr takes a domain-agnostic approach:

- Admins define what a “resource” is
- Users can reserve flexible time intervals
- Conflict detection is consistent for any category
- Availability rules are configurable
- The booking logic is reusable across multiple domains

This creates a scalable, adaptable, engineering-focused scheduling platform that can support real-world use cases in labs, event spaces, co-working rooms, equipment scheduling, and more.

## Features

### User Features

- Browse resources by category
- View resource availability
- Create reservations with flexible start/end times
- See conflict warnings before submitting
- Manage upcoming and past reservations
- Edit/cancel personal reservations

### Admin Features

- Create/edit/delete resources
- Set availability windows
- Define blockout periods (maintenance, closures)
- Toggle between Single-Day Mode or Multi-Day Mode
- View all reservations in a global calendar
- Manage user roles (optional)

### System Features

- Interval-based conflict detection
- Validation against availability rules
- Domain-agnostic resource model
- Clean API layer (or Server Actions)
- PostgreSQL + Prisma
- Responsiveness and dark/light mode

## Architecture

### Tech Stack

|             |                                                     |
| ----------- | --------------------------------------------------- |
| Framework:  | Next.js (App Router)                                |
| Language:   | TypeScript                                          |
| Styling:    | Tailwind CSS                                        |
| ORM:        | Prisma                                              |
| Database:   | PostgreSQL                                          |
| State/Data: | React Query or Server Actions (currently undecided) |
| Auth:       | NextAuth or custom solution                         |
| Deployment: | Vercel + external database (Neon/Supabase)          |

### High-Level Structure

```
/app
  /admin
    /resources
    /availability
    /blockouts
    /settings (booking mode)
  /reservations
  /resources
  /dashboard
  /api (if I choose not use Server Actions)
```

## Data Model

### Resource

```
id: string
name: string
category: string
capacity?: number
location?: string
description?: string
createdAt: Date
updatedAt: Date
```

### Reservation

```
id: string
userId: string
resourceId: string
startDateTime: Date
endDateTime: Date
status: 'CONFIRMED' | 'CANCELLED'
```

### AvailabilityWindow

```
id: string
resourceId: string
dayOfWeek: number
startTime: string
endTime: string
```

### Blockout

```
id: string
resourceId: string
startDateTime: Date
endDateTime: Date
reason: string
```

### User

```
id: string
name: string
email: string
role: 'ADMIN' | 'USER'
```

## Core Logic Summary

**Interval Conflict Detection**

```ts
// conflict exists when:
newStart < existingEnd && newEnd > existingStart;
```

**Booking Mode Rules**

- Single-Day Mode: start and end must be on the same date
- Multi-Day Mode: reservation may span multiple days

**Availability Enforcement**

- Check reservation times against availability windows for that resource
- Validate blockout overlaps

## Roadmap

**MVP**

- Resource CRUD
- Availability windows
- Reservation creation
- Conflict detection
- Single-day mode
- User dashboard

**Next**

- Multi-day reservation support
- Blockouts
- Admin global calendar
- Search/filter resources
- Email notifications (optional)

**Future**

- Custom booking rules per resource type
- Custom duration constraints
- Analytics
- Exportable calendar integrations (iCal)

## Setup Instructions

```bash
git clone https://github.com/arielainstem/tableturnr
cd tableturnr

npm install

cp .env.example .env
# add database URL, auth secrets, etc.

npx prisma migrate dev
npm run dev
```

## License

This project is open-source under the MIT License.

You are free to use, modify, and distribute this software for both personal and commercial purposes, as long as the original license and copyright notice are included.

See the [LICENSE](./LICENSE) file for full details.

© 2025 Ariela Israel. Released under the MIT License.
