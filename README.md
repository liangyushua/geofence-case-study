# GeoBoundary Review Lab

> An interactive, privacy-safe demo for turning ambiguous corporate location
> records into reviewable candidate geofence boundaries.

**This is an independent portfolio project.** All cases, names, addresses,
polygons, and review decisions in this repository are synthetic. The project
contains no employer source code, production data, credentials, proprietary
integrations, or map-provider API keys.

## Why this problem matters

Satellite and geospatial workflows often need a precise area of interest for a
company facility: not just a city, a pin, or a campus, but a reviewable boundary
for the relevant building or site. A company may occupy only one or a few
buildings within a much larger industrial park or office campus, so the campus
outline alone is often too broad for downstream satellite analysis.

The source record is frequently a company address disclosed in a public filing.
That address may be legally precise while looking very different from the names,
points of interest, or formatted addresses returned by map search. A search can
also return several plausible candidates, with the correct one not necessarily
ranked first.

Even after the correct area is found, many map data sources expose a campus or
industrial-park outline rather than the footprint of the individual building
associated with the company. Resolving the candidate and deciding the appropriate
boundary therefore require both automated evidence aggregation and human review.

`GeoBoundary Review Lab` demonstrates that workflow with local, synthetic data.
It is a portfolio demonstration of the engineering pattern, not a production
geocoding service.

## The workflow

```text
Corporate location record
        |
        v
Retrieve several plausible place candidates
        |
        v
Aggregate address, name, and nearby-place evidence
        |
        v
Rank and explain candidate boundaries
        |
        v
Validate geometry and show boundary variants
        |
        v
Reviewer approves, rejects, or requests follow-up
        |
        v
Export a boundary and an audit-friendly review record
```

The key design principle is simple: **a center point is not a boundary, and an
uncertain candidate is not an automatically completed result.** The interface
makes uncertainty visible and sends it to a reviewer rather than hiding it.

## What the demo shows

The static demo is organized as a three-panel review workspace:

| Workspace | Reviewer question | Demonstrated capability |
| --- | --- | --- |
| **Case queue** | What needs attention and what is its status? | Batch progress, case selection, and review state |
| **Boundary workspace** | Is this candidate geometry plausible? | Candidate overlays, editable points, boundary variants, and geometry checks |
| **Review panel** | What evidence supports a decision? | Candidate explanations, confidence signals, reviewer decisions, and notes |

It demonstrates the following transferable engineering ideas:

- **Address and entity resolution:** compare a disclosed location description
  against several differently named candidate places.
- **Evidence aggregation:** surface address fragments, candidate labels, and
  nearby-place context instead of relying only on the first search result.
- **Building-level boundary review:** distinguish a broad site outline from a
  proposed building-level area of interest.
- **Geometry-aware data products:** inspect vertex count, area, validity, and
  simplified variants before exporting a polygon.
- **Human-in-the-loop quality control:** record approval, rejection, uncertainty,
  or missing evidence together with a reviewer note.
- **Traceable outputs:** export synthetic GeoJSON and a review record that can be
  inspected later.

## A synthetic example

The demo includes fabricated examples where a filing-style location description
and a map-style candidate label are intentionally different:

```text
Source record:  Harbor District, 18 Meridian Avenue, Tower B, Suite 1207
Candidate A:    Meridian International Center - Building B
Candidate B:    Harbor Logistics Park Visitor Center
Candidate C:    Meridian Avenue Office Plaza
```

The system selects the best-supported candidate first and presents its proposed
polygon with the evidence used to support the selection. In the normal review
path, the reviewer checks only this selected result against the source record:
if the location and evidence correspond, the reviewer can approve it directly.

Only when the selected result does not correspond, or its evidence is
insufficient, does the reviewer expand the candidate list, compare the
alternatives, and record a correction or an unresolved review outcome. This
exception-based workflow lets the system perform the broad candidate collection
and comparison work up front while reserving deeper manual investigation for
ambiguous cases. No real organization, address, or facility is represented by
this example.

## Boundary variants

Each synthetic case can expose several representations of the same candidate:

- **Original:** the full candidate polygon used for review.
- **Simplified:** a lower-vertex representation intended for efficient display
  while preserving the overall shape.
- **Fixed-vertex:** a normalized representation for systems that require a
  consistent vertex budget.

The demo keeps these representations distinct. A simplified polygon is a
derived view, not a silent replacement for the original reviewed geometry.

## Review states

Each check can be saved with one of four explicit states:

| State | Meaning |
| --- | --- |
| `Approved` | The evidence and boundary are sufficient for this demo case. |
| `Rejected` | The selected candidate or boundary is inconsistent with the evidence. |
| `Needs review` | More evidence or a human correction is needed. |
| `Not available` | The input does not provide enough information for this check. |

This structure helps distinguish a negative conclusion from an unresolved case.

## Quick start

The first version has no backend, database, external map service, or API key.

```bash
git clone https://github.com/liangyushua/geofence-demo.git
cd geofence-demo
python -m http.server 8000
```

Then open [http://localhost:8000](http://localhost:8000) in a browser.

## Planned interaction model

1. Select a synthetic location case from the left-side queue.
2. Inspect the system-selected boundary, its supporting evidence, and its
   geometry checks.
3. Approve the result directly when it corresponds to the source record.
4. If it does not correspond, expand the candidate list and compare alternatives
   before recording a correction or an unresolved review outcome.
5. Add an optional reviewer note and save the decision.
6. Export the synthetic boundary as GeoJSON together with its review record.

## Project structure

```text
geofence-demo/
├── index.html                 # Three-panel review workspace
├── styles.css                 # Independent visual design
├── app.js                     # Local interaction and export behavior
├── data/
│   └── demo-cases.js          # Synthetic cases and GeoJSON-like polygons
├── assets/
│   └── screenshot.png         # Demo screenshot (added after implementation)
└── README.md
```

## Technical approach

The demo is intentionally lightweight:

- Vanilla HTML, CSS, and JavaScript
- SVG or Canvas-based synthetic map plane (no third-party map tiles)
- Local GeoJSON-like polygon data
- Browser-only state for review decisions
- Downloadable JSON and GeoJSON exports
- No network calls after the page loads

Keeping the demo static makes the behavior easy to inspect, deploy with GitHub
Pages, and run without credentials.

## Scope and limitations

- This repository does **not** geocode real addresses or identify real companies.
- Candidate rankings and confidence signals are illustrative, not a trained or
  validated production model.
- The synthetic plane is not a real map and does not represent an actual location.
- Geometry checks support a review workflow; they do not establish legal property
  boundaries or regulatory compliance.
- A reviewer remains responsible for decisions whenever evidence is ambiguous.

## Data and privacy

- All data included in this repository is synthetic and manually authored for
  demonstration purposes.
- The demo contains no real employer records, customer records, employee data,
  filing extracts, screenshots, server configuration, or historical outputs.
- The demo does not call external geocoding, map, large-language-model, or
  satellite-data services.
- Do not add credentials, production exports, or personally identifiable
  information to this repository.

## Future extensions

Possible extensions for a separate, public-data-only version include:

- a pluggable adapter for an openly licensed geocoding data source;
- reproducible candidate-ranking experiments on a documented public benchmark;
- more geometry validation rules and unit tests;
- keyboard-accessible polygon editing; and
- a GitHub Pages deployment with a short walkthrough GIF.

## Portfolio context

This project demonstrates generalizable patterns for geospatial workflow design:
ambiguous-record resolution, geometry validation, evidence-based review, and
human-in-the-loop quality control. It is deliberately separated from any
proprietary implementation or data source.
