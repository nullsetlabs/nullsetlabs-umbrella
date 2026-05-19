# Pantry Dashboard

A browser-based, audience-aware dashboard for food bank operations. Drop in an Excel or CSV intake file and see monthly totals, department breakdowns, donor concentration, distribution analytics, rolling trends, and side-by-side comparisons. Everything runs client-side. Nothing uploads.

Part of the Null Set Labs Open Tools track. Sibling to [Open Dashboard](https://open.nullsetlabs.org/dashboard/) (the generic Excel-to-dashboard tool); this one is specifically tailored to food bank operational data.

## Why this name

Three candidates were on the table: "Food Bank Dashboard," "Community Food Dashboard," and "Pantry Dashboard." We picked **Pantry Dashboard** because:

1. It is short enough to fit comfortably in the top-nav lockup.
2. "Pantry" is the operational unit that food bank staff actually talk about day to day (the recipient site is a pantry, the distribution flow is to pantries).
3. It is generic enough to apply to any food bank, food pantry, or hunger relief organization without sounding institutional.

The umbrella name "Food Bank Dashboard" felt too literal and too generic for a public-good tool that any organization can adopt. "Community Food Dashboard" was too broad and overlapped with the existing Open Dashboard.

## Built for

The project grew out of a data analysis support engagement with a local Pennsylvania food bank. The collaborating organization uses a JotForm intake flow that produces a wide-sparse spreadsheet: each department (Seafood, Dairy, Meat, Frozen, Produce, Bakery, Canned Goods, Beverages, Pizza, Other) fills out only its relevant columns and leaves the rest blank. Pantry Dashboard handles that pattern by design, and the same approach generalizes to any food bank using a similar intake structure.

**For research and exploratory purposes only.** Not a substitute for operational decision-making without independent verification.

Any food bank with a similar intake spreadsheet can use it without modification. The column-detection logic recognizes common header names (`Donor`, `Source`, `Partner`, `Pounds`, `Lbs`, `Distributed`, `Recipient Site`, `Households`, etc.) and assigns roles automatically.

## Pre-built views

The dashboard ships seven operational views, switched via the top tabs:

1. **Monthly Overview**. headline KPIs (pounds received, pounds distributed, households served, distinct donors), most-recent-month deltas, intake trend, received-vs-distributed monthly bars.
2. **Departments**. per-department record counts, pounds, households, and items, plus horizontal bar charts and a stacked monthly view showing department mix over time.
3. **Donor Concentration**. top-10 donors, donor diversity index (1 minus Herfindahl-Hirschman), top-10 share, repeat-donor rate, donor-type doughnut.
4. **Distribution**. per-site pounds and households, monthly distribution trend, and an intake-to-distribution gap chart.
5. **Trends**. 3-month and 6-month rolling means for intake, distribution, and households served, plus a seasonality note comparing the most recent 3 months against the prior period.
6. **Compare**. three side-by-side comparison cards (month over month, year over year, year to date) and a bar chart showing this month versus last month versus year-ago.
7. **Raw Data**. sortable, searchable, audience-filtered row-level table with CSV export.

## Audience layering

A toggle in the dashboard header switches between four audience modes. The mapping is configurable. It lives at the top of the JS code in the `AUDIENCE_CONFIG` constant; any food bank can edit it to match their reporting policies.

| Audience | What changes |
|---|---|
| Internal staff | Everything visible. All views, all columns, all rows. |
| Board / executive | Overview, departments, trends, compare. No row-level data. No identifiers. No notes. |
| Donors | Overview, departments, donors, trends. Distribution gap is hidden. Identifier columns removed from row exports. |
| Public | Overview, departments, trends. Donor names anonymized in tables. Top donor charts and donor-type charts hidden. No row-level data. |

The audience setting also drives:
- Which views appear in the view-tab strip.
- Which KPI cards render.
- Which columns appear in the raw data table and the CSV export.
- Whether donor names are anonymized as "Donor 01," "Donor 02," etc.

## Wide-sparse data handling

A JotForm-style wide-sparse intake produces many columns in total, but each row populates only the columns relevant to its department. Pantry Dashboard treats blanks in irrelevant columns as not-applicable, not as missing data.

Concretely:
- Schema detection looks at non-null values only when classifying a column as date, number, or category.
- Aggregations (`sumCol`, `countDistinct`) skip null values rather than treating them as zero or as errors.
- The donor-concentration view computes top-N over rows that actually have a donor.
- The distribution view computes its metrics over rows that actually have distribution data.
- The raw data table shows blanks as a soft en-dash glyph, not as "0" or "null," so staff can see which cells were intentionally left empty.

## Auto-detected columns

The dashboard recognizes these header patterns and assigns roles automatically. Header matching is case-insensitive.

| Role | Header patterns |
|---|---|
| Date | `date`, `datetime`, `timestamp`, `month`, `period`, `when` |
| Department | `department`, `category`, `food type`, `product type`, `item type`, `class` |
| Donor | `donor`, `source`, `partner`, `organization`, `contributor`, `sponsor` |
| Donor type | `donor type`, `source type`, `partner type`, `sector` |
| Recipient | `recipient`, `distribution site`, `site`, `location`, `pantry`, `hub`, `center`, `agency` |
| Pounds received | `pounds received`, `lbs received`, `weight received`, `pounds donated`, `received pounds` |
| Pounds distributed | `pounds distributed`, `lbs distributed`, `delivered pounds`, `pounds out` |
| Generic pounds | `pounds`, `lbs`, `weight` (fallback if no in/out distinction) |
| Items | `item count`, `units`, `quantity`, `count`, `num items` |
| Households | `households`, `families`, `hh`, `served`, `clients` |
| Notes | `note`, `comment`, `description`, `memo`, `details` |
| Identifier | `email`, `submission id`, `record id`, `response id`, `uuid` (filtered out of analytics) |

Columns that match no role fall back to type-based classification (`date`, `category`, `metric`, `dimension`, or `identifier`).

## Privacy guarantee

- All parsing happens client-side in JavaScript. No part of your file is uploaded.
- Google Analytics (GA4 ID `G-R1S9F2Z4HS`) records page views only. It does not see your data.
- A Cloudflare Web Analytics placeholder is in the HTML head, commented out, awaiting a token.
- Close the tab and the file is gone.

## Export

- **Charts to PNG.** Each chart card has an export icon in its header.
- **Filtered data to CSV.** Top-bar button. Exports the currently filtered rows in the columns the active audience is permitted to see, with donor names anonymized in Public mode.
- **One-page print handout.** Use the Print button or your browser's print command. The print stylesheet hides navigation, audience controls, and footer; renders all charts; uses a light theme.

## Deployment

This is a single static HTML file plus a sample workbook. Deployment is just file copy.

Production target: `open.nullsetlabs.org/foodbank/`

```
foodbank-dashboard/
  index.html                  # the whole app
  README.md                   # this file
  sample-data/
    pantry-sample.xlsx          # 401-row, 12-month, wide-sparse sample workbook
    _generate_sample.py       # generator script, kept alongside for reference
```

The generator script depends only on `openpyxl`. Run `python3 sample-data/_generate_sample.py` to regenerate the sample. The script uses a fixed random seed so output is reproducible.

## Customizing for another food bank

1. Edit `AUDIENCE_CONFIG` near the top of the inline `<script>` to match your reporting policies (which audience sees which view, which columns get hidden, whether donor names are anonymized).
2. The `ROLE_PATTERNS` array below it defines header-to-role mappings. Add patterns specific to your form if your headers do not match the defaults.
3. If your intake spreadsheet uses entirely different column names, you can either rename the columns in your spreadsheet (easier) or add patterns to `ROLE_PATTERNS` (more robust over time).

No build step. No bundler. Edit the HTML and reload.

## Libraries

- [SheetJS Community Edition](https://sheetjs.com/) for Excel parsing.
- [Chart.js 4](https://www.chartjs.org/) for charts.
- [chartjs-adapter-date-fns](https://github.com/chartjs/chartjs-adapter-date-fns) for date-aware axes.

All three load from public CDNs. The page has no other runtime dependencies.

## License

CC BY-NC 4.0. Open-source. Pull requests welcome at the lab's GitHub.

## Built by

Null Set Labs. A youth-led research lab. First principles. Open tools. Public good. See [nullsetlabs.org](https://nullsetlabs.org/).
