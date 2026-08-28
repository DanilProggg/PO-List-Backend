# PO-List Backend

REST API for **PO-List**, a class-schedule and attendance/hours-tracking system built for a university department (frontend is branded for MISIS). This repository is the backend service; the companion [PO-List Frontend](../PO-List-Frontend) repo provides the admin and teacher/user web UI.

## What it does

PO-List manages the day-to-day academic scheduling and reporting for a department:

- **Schedule management** — CRUD for classrooms, disciplines (subjects), student groups, and teachers, plus "pары" (paired class periods/lessons) that tie a discipline, teacher, classroom, group, subgroup, date and period number together.
- **Bulk schedule upload** — endpoints to upload a full week's schedule for a group (`/api/v1/pare/upload`, `/upload/v2`) instead of creating lessons one by one.
- **Weekly/by-group views** — fetch lessons by date range, by group, or by group + specific day, including a variant that resolves discipline/teacher/classroom names inline.
- **Hours tracking & stats** — plan teaching hours per group/discipline and query planned vs. past (actual) stats per group, used to drive the frontend's hour-tracking dashboards.
- **Excel export** — `ExcelService` (Apache POI) renders a group's schedule for a date range into a styled `.xls` workbook (merged date headers, per-subgroup rows, borders) and streams it back as a downloadable report.
- **Authentication** — registration and session-based login (`/api/v1/auth`), backed by Spring Security with BCrypt password hashing.

## Two-role model

The API is consumed by two distinct frontend areas:
- **Admin** — manages classrooms/disciplines/groups/teachers, builds and edits the schedule, tracks hours, and generates Excel reports.
- **User/teacher** — read-only schedule browsing for their own group (most `GET` endpoints for schedule data are open via `permitAll`; everything else requires authentication).

## Tech stack

- Java 17, Spring Boot 3.2
- Spring Web (REST controllers), Spring Data JPA (Hibernate)
- Spring Security (HTTP Basic + BCrypt, CORS configured for the Angular frontend origin)
- PostgreSQL 16
- Apache POI / POI-OOXML for Excel report generation
- Lombok, Maven

## API surface

Grouped under `/api/v1`: `auth`, `classroom`, `discipline`, `group`, `pare`, `stats`, `teacher` — each controller exposes `add` / `delete` / `all` plus entity-specific queries (e.g. `pare/week`, `pare/bygroup`, `pare/report`, `stats/planed`, `stats/past`).

## Running locally

A `docker-compose.yml` and `Dockerfile` are provided (multi-stage Maven build → JRE runtime) alongside a PostgreSQL 16 container preconfigured to match the app's dev defaults (`polist` / `postgres` / `postgres`):

```bash
docker compose up --build
```

The API starts on `:8080`, backed by Postgres on `:5432`.
