---
name: Import a drone flight log and attach media
description: List flights, import a flight from a drone flight log, attach a media file, and export the flight path as KML using the Aloft fleet API.
api: openapi/aloft-openapi-original.json
operations:
  - bafdf102e8e7154e8f38eeb107254958  # GET /v1/account/{account_id}/flights
  - afffbdaf3275de31c3d7a713b103ed6d  # POST /v1/account/{account_id}/flights/import
  - 90b3b1bc69e727654650e69340ad682b  # POST /v1/account/{account_id}/flights/{flight_id}/media
  - 8ff7c1bb99f0339327e6580d21f13928  # GET /v1/account/{account_id}/flights/{flight_id}/flight-path
---

# Import a drone flight log and attach media

Use this skill to ingest a completed drone flight into Aloft's fleet records and enrich it.

## Preconditions
- Authenticate with the `Authorization` bearer header (Aloft Token). See `authentication/aloft-authentication.yml`.
- Know your `account_id`.

## Steps
1. **List existing flights** (optional dedupe): `GET /v1/account/{account_id}/flights` (operationId `bafdf102e8e7154e8f38eeb107254958`). Supports `page` / `page_length` and `order_by` / `order`; opt into related data with `appends[]`.
2. **Import from a flight log:** `POST /v1/account/{account_id}/flights/import` (operationId `afffbdaf3275de31c3d7a713b103ed6d`) to create a flight from an uploaded drone log.
3. **Attach media:** `POST /v1/account/{account_id}/flights/{flight_id}/media` (operationId `90b3b1bc69e727654650e69340ad682b`) to associate a photo/video with the flight.
4. **Export the path:** `GET /v1/account/{account_id}/flights/{flight_id}/flight-path` (operationId `8ff7c1bb99f0339327e6580d21f13928`) to download the flight path as KML.

## Rules
- Paginate large flight lists with `page`/`page_length`; do not assume a single-page response.
- Use `appends[]` to expand related sub-resources instead of issuing extra calls.
- On `422`, read the validation errors and fix the payload (`errors/aloft-problem-types.yml`); `401`/`403` indicate auth/permission problems.
