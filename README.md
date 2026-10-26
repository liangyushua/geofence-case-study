# Geospatial Boundary Resolution: Case Study

> A non-confidential overview of a proprietary workflow for turning ambiguous
> corporate location records into reviewable facility boundaries.

**Source code and operational data are not published.** This repository is a
technical case study for portfolio and interview discussion. It contains no
employer source code, production data, credentials, internal screenshots,
proprietary integrations, decision rules, or map-provider API keys.

## Project context

Satellite and geospatial workflows often need a precise area of interest for a
company facility: not just a city, a pin, or a campus, but a reviewable boundary
for the relevant building or site. A company may occupy only one or a few
buildings within a much larger industrial park or office campus, so the campus
outline alone is often too broad for downstream satellite analysis.

This work addressed the gap between publicly disclosed corporate-location
records and the building-level geometry needed by downstream geospatial analysis.
The system was developed in a proprietary setting; this document describes the
problem, review workflow, and engineering principles at a non-confidential
level.

## The two core challenges

### 1. Address and entity resolution

A company address disclosed in a public filing can be legally precise while
looking very different from the names, points of interest, or formatted
addresses returned by a map search. A query may return several plausible
candidates, and the correct one is not necessarily the first result.

The problem is therefore not simply address lookup. It is evidence-based entity
resolution: determining which candidate place corresponds to the disclosed
corporate location.

### 2. Building-level boundary resolution

Even after identifying the correct general area, map data may expose only a
campus or industrial-park outline rather than the individual building occupied
by the company. The relevant boundary must be evaluated at the appropriate
facility or building level rather than inferred from a center point or a broad
site polygon.

## High-level workflow

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
Reviewer approves, corrects, or requests follow-up
        |
        v
Produce a reviewable boundary and an audit-friendly record
```

The key design principle is simple: **a center point is not a boundary, and an
uncertain candidate is not an automatically completed result.** Uncertainty is
made explicit and escalated to a reviewer rather than hidden.

## Review workflow

The workflow separates broad automated investigation from focused human review:

| Review stage | Reviewer question | Purpose |
| --- | --- | --- |
| **Selected result** | Does the selected location correspond to the source record? | Verify the proposed boundary and supporting evidence efficiently |
| **Escalated comparison** | If not, which alternative best corresponds? | Inspect additional candidates only when the initial result is inconsistent or incomplete |
| **Decision record** | What was approved, corrected, or left unresolved? | Preserve the basis and outcome of the review |

The workflow embodies the following transferable engineering ideas:

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
- **Traceable outputs:** retain a review record that can be inspected later.

## Efficient human-in-the-loop review

The system first selects the best-supported candidate and presents its proposed
boundary with the evidence used to support that selection. In the normal review
path, a reviewer verifies only this selected result against the source record.
If the location and evidence correspond, the reviewer can approve it directly.

Only when the selected result does not correspond, or its evidence is
insufficient, does the reviewer expand the candidate set and compare additional
alternatives. The reviewer then records a correction or an unresolved outcome.

This exception-based design allows automated processing to collect and compare a
larger body of candidate evidence up front while reserving deeper manual
investigation for ambiguous cases. It avoids the inefficient alternative of
requiring a reviewer to repeatedly search and read every possible candidate.

## Boundary variants

The production workflow distinguishes several representations of the same
candidate boundary:

- **Original:** the full candidate polygon used for review.
- **Simplified:** a lower-vertex representation intended for efficient display
  while preserving the overall shape.
- **Fixed-vertex:** a normalized representation for systems that require a
  consistent vertex budget.

The workflow keeps these representations distinct. A simplified polygon is a
derived view, not a silent replacement for the original reviewed geometry.

## Review states

Each check can be saved with one of four explicit states:

| State | Meaning |
| --- | --- |
| `Approved` | The evidence and boundary are sufficient for the reviewed record. |
| `Rejected` | The selected candidate or boundary is inconsistent with the evidence. |
| `Needs review` | More evidence or a human correction is needed. |
| `Not available` | The input does not provide enough information for this check. |

This structure helps distinguish a negative conclusion from an unresolved case.

## My contributions

Within a proprietary project setting, my work focused on the following
generalizable engineering areas:

- Designing an address-to-boundary review workflow for ambiguous corporate
  facility locations.
- Improving the presentation of candidate evidence so reviewers can validate a
  selected result efficiently before escalating to a broader comparison.
- Supporting building-level boundary review, correction, and version-aware
  geometry handling.
- Building quality-control interactions that record a reviewer decision and its
  rationale for later inspection.

Specific source code, model prompts, scoring methods, data schemas, service
configuration, and deployment architecture are intentionally omitted.

## Scope and limitations

- This case study does **not** publish source code or provide a runnable copy of
  the proprietary system.
- It intentionally omits real companies, addresses, candidate results, geometry,
  screenshots, historical records, and performance or cost figures.
- It does not disclose external-service integrations, retrieval strategies,
  model prompts, ranking formulas, internal data schemas, or deployment details.
- Geometry review supports an operational workflow; it does not establish legal
  property boundaries or regulatory compliance.
- Human review remains necessary whenever evidence is ambiguous.

## Confidentiality statement

This repository is deliberately limited to a high-level description of my
professional experience. It contains no employer-owned source code, trade
secrets, credentials, production data, customer information, employee data,
internal documentation, or copied user-interface assets. The implementation is
not available because it was developed in a proprietary environment.

## Portfolio context

This project demonstrates generalizable patterns for geospatial workflow design:
ambiguous-record resolution, geometry validation, evidence-based review, and
human-in-the-loop quality control. It is deliberately separated from any
proprietary implementation or data source.
