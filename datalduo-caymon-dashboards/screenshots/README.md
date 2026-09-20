# Dashboard Screenshots

Add Power BI screenshots here before publishing — **6 total**, named exactly as below so they render automatically in the main `README.md`.

## The four dashboard views

1. `01-moulding-overview.png` — KPI cards (Avg OEE, Avg Cycle Efficiency, Downtime %) + OEE-by-machine bar chart
2. `02-moulding-reliability-downtime.png` — the ≥90% reliability ranking and the downtime Pareto/cumulative chart
3. `03-assembly-overview.png` — KPI cards + weighted-OEE-by-machine chart
4. `04-assembly-customer-analysis.png` — the value vs frequency customer tables

## Two more worth including — these are the ones that actually demonstrate skill, not just output

5. `05-data-model.png` — the **Model view**, showing the star schema: Calendar dimension, fact tables, and the Machine/Product-Family bridge table, with relationship lines visible. This is the single best piece of evidence that you can design a data model, not just build a chart.
6. `06-dax-weighted-oee.png` — a close-up of the **DAX formula bar** showing the `Weighted Avg OEE` measure. Zoom in enough that the formula is actually readable. This is the highest-signal screenshot in the whole repo for anyone with technical Power BI knowledge — it proves the correction was deliberate, not a default aggregation.

## Before adding any screenshot

Check it against the same anonymisation already applied to the decks and text in this repo:
- No reference to the host company's name
- No reference to the specific product-family names (generalised to Family A/B/C/D everywhere else here) — check any visible `Product Family` field, slicer, or table, since a live dashboard may still show the original names even though the decks and docs don't
- Crop or blur any real customer name if you'd rather keep those private, even though the decks already show some

## Sizing tips

- Export or screenshot at a reasonable resolution (roughly 1400-1600px wide is plenty - larger just slows the README down without adding clarity)
- PNG, not JPEG - Power BI text and gridlines stay sharp
- Crop out the Power BI chrome you don't need (ribbon, page navigator) so the visual itself is the focus
- For the DAX screenshot specifically: crop tightly around just the formula bar and the measure name, not the whole screen - the formula is the point, not the surrounding UI
