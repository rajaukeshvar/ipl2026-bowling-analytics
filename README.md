# ipl2026-bowling-analytics
![Season Overview](page1_season_overview.png)

📥 [Download the full Power BI file](IPL2026_Bowling_Analytics.pbix) to explore interactively.


# IPL 2026 Bowling Analytics: Length & Variation Difficulty

🏏 An end-to-end analytics pipeline on the IPL 2026 season — 74 matches, 17,500+
ball-by-ball deliveries — built to answer a sharper question than most cricket
dashboards ask: **which deliveries, by length and variation, actually make batsmen
struggle?**

## What this project covers

- **Full-season bowling metrics** (economy, dot-ball %, dismissal types, phase-wise
  performance) via MySQL + Power BI, across all 74 matches
- **A bowler × phase economy heatmap** that surfaces real tactical stories — e.g. one
  of the season's bowlers is dramatically cheaper in the powerplay than at the death,
  the opposite of how teams typically deploy that skill set
- **A curated NLP layer on match commentary** tagging bowling length (yorker, good
  length, back-of-length, etc.) and variation (slower ball, seam movement, leg-break
  turn) — extracting the one thing no public ball-by-ball dataset tracks
- **An interactive Power BI dashboard** with cross-filtered visuals, a dismissal-type
  breakdown, and a phase-wise heatmap

## A finding I'm proud of: catching a data-quality issue

While validating my bowler wicket counts against the official IPL 2026 Purple Cap
result, my numbers were off by 2 for one bowler. The cause: the raw ball-by-ball data
flags *any* wicket falling on a delivery — it doesn't distinguish bowler-credited
dismissals (caught, bowled, lbw, stumped, caught-and-bowled, hit-wicket) from ones that
aren't (run-outs, retired-hurt). Once I filtered on dismissal `Kind` correctly, my
numbers matched the real result exactly (Rabada 29, Bhuvneshwar Kumar 28). Small
detail, but it's exactly the kind of domain-knowledge check that separates a
mechanical aggregation from real analysis — and it's documented end-to-end in the
SQL queries below.

## Data sources & methodology (read this before judging the numbers)

**Layer 1 — full structured ball-by-ball data (74 matches, 17,527 deliveries)**
Sourced from a community-maintained, auto-updating IPL dataset on GitHub (derived
from Cricsheet). Every legal delivery, run, wicket, and dismissal type for the entire
2026 season.

**Layer 2 — curated, NLP-tagged commentary sample (43 deliveries across 9 matches)**
No public ball-by-ball dataset tags bowling length or variation — that data only
exists inside ball-tracking systems (Hawkeye/CricViz) or implicitly inside match
commentary text. For this project, I manually read commentary for deliberately chosen
high-leverage matches (the Final, both Qualifiers, the Eliminator, plus league games
featuring the season's top wicket-takers) and extracted structured tags — length
category, variation, pace in kph where stated, line, phase, and outcome.

**This sample is a curated case study, not a random or exhaustive sample of the
season** — intentionally biased toward death-overs deliveries and star bowlers,
because that's where commentary is most descriptive. No commentary text is
reproduced anywhere in this repository — only the structured tags derived from
reading it.

## Repo structure
```
data/raw/                       full 2026 season — match_info_2026.csv, ball_by_ball_2026.csv
data/commentary_sample/         tagged_deliveries.csv (the curated Layer 2 sample)
sql/01_schema_and_load.sql      MySQL table definitions
sql/02_business_queries.sql     8 business questions in SQL (includes the bowler-credited wicket fix)
notebooks/                      EDA charts (phase_dot_wicket.png, length_outcome_mix.png)
```

## Key findings
- Powerplay has the highest dot-ball rate (39.5%) but a *lower* wicket rate (4.3%)
  than death overs (7.7%) — boundaries are rare early, but so are wickets.
- Kagiso Rabada finished as the season's leading wicket-taker with 29, edging
  Bhuvneshwar Kumar (28) — verified against the official Purple Cap result.
  Bhuvneshwar posts the better economy of the two; the wicket race went down to the
  final match.
- In the curated length sample: yorkers and good-length deliveries produced zero and
  17% boundary rates respectively, against 44% for full-length deliveries — length
  discipline suppresses scoring far more than variation alone.
- Rashid Khan is a striking phase outlier: economy of 4.50 in the powerplay vs 12.49
  at the death — a spinner who's most dangerous in overs he's rarely used in.

## Tools
Python (pandas) · MySQL · Power BI · DAX

## How to extend this
1. Scale Layer 2 by tagging more matches the same way (methodology is fully
   repeatable — read commentary, extract length/variation/pace/outcome, no verbatim
   text).
2. Load all three CSVs into MySQL Workbench using `sql/01_schema_and_load.sql`, then
   run `sql/02_business_queries.sql`.
3. Build out the Power BI dashboard further — additional pages for team-level
   comparisons, venue effects, or a predictive model for delivery outcome by length.
