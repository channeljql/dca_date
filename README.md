# S&P 500 DCA Timing Study

This repo explores a simple investing question: if someone dollar-cost averages into the S&P 500 once per month, does the exact buy date meaningfully change the result?

The original analysis lives in `Project_DCA.ipynb`. A cleaner, reproducible successor lives in `DCA_Strategy_Study.ipynb`, with a rendered report in `DCA_Strategy_Study.html`.

## Motivation

Dollar-cost averaging is often discussed as if the contribution schedule is mostly administrative: invest monthly, stay consistent, and avoid over-optimizing. The original notebook asked whether that assumption holds historically for the S&P 500 from 1980 through 2022.

This version keeps that intent, but makes the analysis easier to rerun and review:

- it uses committed local CSV snapshots instead of manual downloads
- it values every strategy on the same final date
- it keeps numeric returns separate from display formatting
- it tests whether conclusions survive different DCA start dates

This is a historical backtest, not financial advice.

## Approach

The main notebook loads:

- `sp500_history_1.csv`, `sp500_history_2.csv`, `sp500_history_3.csv`
- `inflation1980.csv`

It filters the S&P 500 data to `1980-01-01` through `2022-12-30`, then models one monthly contribution equal to the inflation-adjusted value of `$100` in 1980.

The notebook compares three strategy families:

- **Calendar day DCA:** buy on day `1..28` of each month, adjusted to the next available trading day.
- **Weekday/week-of-month DCA:** buy on combinations such as `1st Friday` or `3rd Monday`.
- **MA30 trigger strategy:** buy when the S&P 500 closes below its 30-day moving average, while carrying unused monthly cash forward.

To test robustness, the notebook also samples 10 random DCA start dates between `1980-01-01` and `1999-12-31` using `seed = 42`. Each fold starts investing on the first full following month and ends on `2022-12-30`.

## Findings

From the executed notebook:

- The full-period first-day monthly baseline returned `5.8658x`.
- The best full-period calendar-day strategy was `Day 1`, also `5.8658x`.
- The best full-period week/day strategy was `1st Friday`, returning `5.8642x`.
- The MA30 cross-below strategy returned `5.4787x`, including carried cash.

The start-date validation suggests the exact winning schedule is less important than the full-period ranking alone might imply:

- `Day 1` stayed very stable, with average CV family rank `1.20` and top-quartile frequency `100%`.
- `1st Friday` remained strong, with average CV family rank `2.90` and top-quartile frequency `80%`.
- The MA30 strategy ranked first within its one-strategy family by construction, but its average gap versus the first-trading-day baseline was `-0.2417x`.

The practical read: earlier-in-month DCA looks slightly better in this historical window, but the return gaps between simple monthly schedules are small. The visualizations in the notebook and HTML report are intended to make that stability, or lack of it, easier to inspect.

## How to Read or Rerun

Open the rendered report:

```bash
open DCA_Strategy_Study.html
```

Rerun the notebook:

```bash
jupyter nbconvert --to notebook --execute DCA_Strategy_Study.ipynb --output DCA_Strategy_Study.ipynb
```

Export a fresh HTML report:

```bash
jupyter nbconvert --to html DCA_Strategy_Study.ipynb --output DCA_Strategy_Study.html
```

## License

This project is licensed under the MIT License. See `LICENSE` for the full license text.
