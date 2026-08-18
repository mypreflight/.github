<div align="center">

# MyPreflight

Briefing service and electronic flight board for your virtual flights.

[mypreflight.io][homepage] · [api.mypreflight.io][api]

</div>

## The platform

**MyPreflight** is a briefing service and electronic flight board app for your virtual flights, providing you realistic
figures, checklists, procedures and data to perform your flight like a real pilots do. You can customize your
experience, integrate with SimBrief and other tools.

A flight goes through the same stages it does in the real world — it gets dispatched with a schedule, an aircraft, a
crew and a passenger count, the pilot checks in and boards, the loadsheet and the timesheet are filled as the numbers
become final, and the flight is flown off-block, airborne and on-block until it is closed. The briefing carries the OFP
imported from SimBrief together with the ATIS, METAR and TAF held for departure, and the airport library — runways,
terminals, parking positions and gates — comes curated from OpenStreetMap.

While you fly, a companion app on your PC reads the aircraft position out of the simulator and reports it over HTTP to
our own ADS-B receiver. From there the position flows back into the platform and out to every subscribed client, so the
live map, the shared flight link and your Discord rich presence all follow the same aircraft in real time.

## Modules

The platform is split into six repositories — three that make up the product, and three that keep its data reliable and up-to-date.

### Our product

| Repository | What it is |
| --- | --- |
| [**flight-tracker-app**][repo-app] | The web app — the electronic flight board itself. Dispatch, briefing, check-in, boarding, live map and the public flight link, plus the airport library. A React Router SPA, installable as a PWA on desktop and phone. |
| [**flight-tracker-api**][repo-api] | The backend. Owns the flight lifecycle, computes timesheets and loadsheets, issues the briefing with weather, imports from SimBrief, republishes live positions to subscribed clients and delivers announcements through the Discord bot. NestJS, PostgreSQL, Prisma, CQRS. |
| [**flight-tracker-transponder-app**][repo-transponder] | The desktop companion for Windows. Reads your position out of Microsoft Flight Simulator and feeds it to the receiver, sets your Discord rich presence, and prints flight documents on real hardware. One file, no installer. |

### Our data layer

| Repository | What it is |
| --- | --- |
| [**adsb-receiver-api**][repo-adsb] | The ground station. A real ADS-B receiver listens on 1090 MHz; this one listens on HTTP. It accepts position reports from the transponder app, keeps every callsign's track in memory and serves it onward — as close as it gets to ADS-B over HTTP. |
| [**flight-tracker-airport-data-processor**][repo-airport] | Syncs airport infrastructure — boundary shapes, runways, terminals, parking positions and gates — from OpenStreetMap through the Overpass API into the platform. Runs as a CLI for reviewable, hand-edited datasets, or as HTTP functions for refresh on demand. |
| [**flight-tracker-etl-tools**][repo-etl] | Small single-purpose tools for the reference data nobody should have to type in by hand: reconciling operator sheets into the API, generating airline fin lists, and producing catalog-style aircraft illustrations. |

## How it fits together

```
Flight simulator
      │  position
      ▼
flight-tracker-transponder-app ──► adsb-receiver-api ──► flight-tracker-api ──► flight-tracker-app
      │                                                        ▲                      │
      └──► Discord rich presence                               │                      └──► live map,
                                                   reference and airport data               shared flight link
                                            (etl-tools, airport-data-processor)
```

[homepage]: https://mypreflight.io
[api]: https://api.mypreflight.io
[repo-app]: https://github.com/oskarbarcz/flight-tracker-app
[repo-api]: https://github.com/oskarbarcz/flight-tracker-api
[repo-transponder]: https://github.com/oskarbarcz/flight-tracker-transponder-app
[repo-adsb]: https://github.com/oskarbarcz/adsb-receiver-api
[repo-airport]: https://github.com/oskarbarcz/flight-tracker-airport-data-processor
[repo-etl]: https://github.com/oskarbarcz/flight-tracker-etl-tools
