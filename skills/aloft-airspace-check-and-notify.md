---
name: Check airspace and file a Notify & Fly flight
description: Look up airspace advisories and weather for a location, then create a flight and end the flight notification when done, using Aloft's FAA-approved airspace surface.
api: openapi/aloft-openapi-original.json
operations:
  - 7f9bbad943443bcb2ae82013acdba86e  # GET /v1/airspace/advisories
  - 0e20bc62af934af69944b83534177aab  # GET /v1/airspace/weather
  - e7a566e3dae1139a1bf76c956a143a45  # POST /v1/account/{account_id}/flights
  - b8b4b08218e2367457b62fecf4f3dc86  # PATCH /v1/account/{account_id}/flight-notifications/{flight_notification_id}/end
---

# Check airspace and file a Notify & Fly flight

Use this skill to evaluate the airspace for a proposed drone operation and record the flight in Aloft.

## Preconditions
- Authenticate every request with the `Authorization` header carrying your Aloft Token (http bearer). See `authentication/aloft-authentication.yml`.
- You need your `account_id`; nearly all account-scoped operations are nested under `/v1/account/{account_id}/...`.

## Steps
1. **Check advisories.** Call `GET /v1/airspace/advisories` (operationId `7f9bbad943443bcb2ae82013acdba86e`) with the target `lat`/`lng` (or `POST /v1/airspace/advisories` with a GeoJSON Polygon/Point) to retrieve nearby airspace restrictions, controlled airspace classes, and LAANC ceilings.
2. **Check weather.** Call `GET /v1/airspace/weather` (operationId `0e20bc62af934af69944b83534177aab`) for the same coordinates to confirm flight conditions.
3. **Create the flight.** If clear, call `POST /v1/account/{account_id}/flights` (operationId `e7a566e3dae1139a1bf76c956a143a45`) to record the planned/active flight.
4. **End the notification.** When the operation completes, call `PATCH /v1/account/{account_id}/flight-notifications/{flight_notification_id}/end` (operationId `b8b4b08218e2367457b62fecf4f3dc86`) to close the Notify & Fly notification.

## Rules
- Handle validation errors: a `422` returns field-level errors — correct the payload and retry (see `errors/aloft-problem-types.yml`).
- A `403` means the token lacks permission for that `account_id`; a `401` means the bearer credential is missing or invalid.
- Aloft does not document an Idempotency-Key; do not assume create operations are safe to blindly retry — check for an existing flight first.
- Airspace and LAANC decisions are safety-relevant; treat advisory results as authoritative input to a human go/no-go decision.
