# value-timing
This is a backtest on the value factor that I built using data from Ken French's Data Library, generously provided by Dartmouth College. The purpose was to test a strategy that mildly tilted towards SCV/LCV portfolios when the 'value spread' was heightened relative to its recent history. 

## Value Spread Timing: US Small and Large Cap Value

Does tilting toward value when the value spread is extreme beat simply holding a constant value tilt?

## Hypothesis
When value stocks are unusually cheap relative to growth stocks, value should outperform over the following years. The strategy holds the market by default and tilts toward large and small cap value when the value spread reaches an extreme.

## Data
All data is from the [Ken French Data Library](https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html), July 1926 to August 2026 (202608 CRSP vintage):
- **6 Portfolios Formed on Size and Book-to-Market (2x3):** value-weighted monthly returns, and the value-weighted average BE/ME table (December ME version, the standard Fama-French definition)
- **Fama/French 3 Factors:** market return (Mkt-RF + RF)

Large cap value (LCV) = `BIG HiBM`, small cap value (SCV) = `SMALL HiBM`.

The data files are not included. Download the 6 Portfolios 2x3 CSV from the library to run the notebook. Row offsets in the code match the 202608 vintage and shift as the file is updated.

## Method
1. **Value spread:** the average of ln(HiBM / LoBM) across the small and big size groups.
2. **Signal:** z-score of the spread against its rolling 60-month mean and standard deviation. Signal == True when z > 2.
3. **Lag:** the signal is shifted one month, so a reading at the end of month t is traded in month t+1.
4. **Position:** after a signal, hold 70% market / 15% LCV / 15% SCV for 60 months (a new signal restarts the clock). Otherwise hold 100% market. Weights are reset monthly.
5. **Benchmarks:** the market, and a static 70/15/15 portfolio held throughout.

## Results
	            |  StDev	| CAGR	   | Terminal Wealth | Sharpe  |
Market        |0.183305 | 0.103766 | 19717.05        | 0.454528|
Factor Timing	|0.188327 | 0.104924 | 21900.75        | 0.452086|
Static Tilt	  |0.200479	| 0.114661 | 52741.68        | 0.479930|

The factor timing strategy outperformed the market by 11.08% on a terminal wealth basis and ~11bps on a CAGR basis, but had lower risk adjusted performance than the market owing to its disproportionately higher standard deviation. The static tilt outperformed the factor timing strategy by 140.82% on a terminal wealth basis and ~98 bps on a CAGR basis, also achieving higher risk adjusted performance owing to a less than proportionately higher standard deviation. The results suggest that the factor timing strategy did not add value on a risk adjusted performance basis, and underperforms a static tilt on both a raw returns and risk adjusted performance basis.

## Limitations
- Returns are paper portfolios with no fees, trading costs, or investability screens. SCV in particular includes micro-caps that are costly to trade.
- BE/ME is fixed at portfolio formation, so the spread effectively updates once a year and the 60-month window holds about five independent observations.
- Parameters (60-month windows, 2 SD threshold, 70/15/15 weights) were chosen a priori and not sensitivity-tested.

## Future Additions
- Robustness checks
- Statistical Significance Analysis
- Trading Cost & Investability Adjustments
