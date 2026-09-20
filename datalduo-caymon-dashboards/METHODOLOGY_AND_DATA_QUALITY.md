# Methodology & Data Quality

This document covers the analytical process — the data model, the corrections that were necessary before the numbers could be trusted, and the reasoning behind each one. It's included because the process was where most of the actual analytical work happened, not just the finished dashboard.

## 1. Data model

Three YTD extracts were provided with no data dictionary and no shared key linking them cleanly:

- **Moulding Machine data** (~22,500 rows) — one row per machine, per shift, per mould/colour combination
- **Assembly Overview** (~4,300 rows) — one row per assembly line, per shift
- **Assembly Bookings** (~2,400 rows) — one row per booking transaction against a customer order

A fourth reference file (mould-to-machine mapping) and direct clarification from the mentor were used to build a proper star schema: a shared Calendar dimension, a Machine/Product-Family dimension built manually from the mentor's own description of the business, and separate fact tables for each of the three source extracts.

## 2. Data quality issues found and resolved

| Issue | Resolution |
|---|---|
| Machine numbers inconsistent across files (numeric IDs in Moulding, named lines in Assembly Overview, different codes again in Assembly Bookings) | Built an explicit machine/resource/product-family mapping table, confirmed directly with the mentor rather than assumed |
| A machine number was recorded as "120" where every other value was 1–66 | Confirmed as a data entry typo (should read 20) and corrected |
| A mould number appeared with a mismatched cross-reference (9536 vs 9563 for the same part) | Flagged and confirmed with the mentor rather than guessed |
| Duplicate text values differing only by trailing whitespace or letter case (e.g. two spellings of the same part description) | Identified by comparing trimmed/uppercased values against raw values; corrected in Power Query |
| Time fields stored as `Time` type rather than `Duration`, causing standard duration functions to fail silently or error | Diagnosed by testing a minimal passthrough formula to isolate the failure, then converted using `Time.Hour`/`Time.Minute`/`Time.Second` arithmetic |
| A booking reference number appeared in two capitalisations | Checked whether it ever pointed to more than one customer or part number (it never did) before concluding it was safe to treat as reliable and continuing to use it as the order-level key |
| A machine showed a 10x jump in downtime hours between its rank and the next rank down | Traced to ranking by *rate* instead of *absolute hours* — the ranking measure was corrected to use total stopped hours, which is the correct basis for a Pareto/concentration analysis |
| OEE and Waste % fields both showed values exceeding 100% on individual rows | Not treated as errors — confirmed directly with the mentor that these formulas compare actual performance against a fixed standard, and can legitimately exceed 100% when a machine outperforms that standard |
| A "Plan To Run" scheduling field looked like it should determine which shifts to include, but many "not planned" shifts still showed real output | Confirmed with the mentor that this field reflects an outdated scheduling system and should be disregarded entirely, rather than used as an inclusion/exclusion filter |

## 3. The OEE weighting correction

This was the most consequential methodological issue in the project, and it appeared in two different forms across the two divisions.

**The underlying problem:** a simple average of a per-shift efficiency percentage treats every shift as equally important, regardless of how much that shift actually produced. A shift that made 2 units and a shift that made 20,000 units both count as "one data point." Since many shifts are partial (changeovers, run tails, idle time), a simple average is pulled down by shifts that represent almost no actual production.

**In Moulding**, roughly 41% of all recorded shifts had zero output. These shifts correctly show 0% OEE — they aren't errors — but including them in a plant-wide average conflates *machine capability* with *capacity utilisation*, which are different management questions. The fix applied was to report both figures side by side, explicitly labelled: the all-shifts average (42.2%, a utilisation measure) and the producing-shifts-only average (71.6%, a performance measure).

**In Assembly**, the same root problem existed but in a more continuous form — rather than a clean split between zero and non-zero shifts, output sizes varied smoothly from very small to very large. Filtering out zero-output rows wasn't enough on its own (52.2%); a genuinely fair average required **weighting each shift's OEE by its own output volume**, so a 2-unit shift barely moves the plant average and a 20,000-unit shift dominates it appropriately:

```
Weighted OEE = Σ(shift OEE × shift output) / Σ(shift output)
```

This raised the Assembly average further, to 68.1%. The two different fixes for what is fundamentally the same problem — reflect a real methodological lesson: the *right* correction depends on the actual shape of the bias in the data, not a one-size-fits-all rule.

## 4. The reject-rate data gap

One Assembly line recorded zero rejects on every shift across the whole period, while simultaneously showing non-zero waste in kilograms and a populated Waste % field with plausible-looking values. Taken at face value, the zero-reject line would rank as the best-performing line in the plant on quality — which directly contradicted the waste data for the same line. This was treated as a measurement gap (the reject count isn't being captured) rather than a genuine result, and the line was excluded from reject-rate comparisons rather than presented as a top performer. This is the clearest example in the project of why a number needs to be cross-checked against a related number before it's trusted, not just checked for whether it's technically well-formed.

## 5. Validating an anomaly against real-world context

A sharp week-long dip in Moulding OEE, visible in the trend chart, initially looked consistent with a public holiday shutdown based on the calendar dates alone. Raised directly with the mentor rather than asserted as fact, the actual cause turned out to be an unplanned compressed-air supply failure — a different, more operationally significant event than a planned closure. This is included here specifically because it was a case where the most plausible *data-only* explanation was wrong, and only checking with someone who had the operational context caught it.
