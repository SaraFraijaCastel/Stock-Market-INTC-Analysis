09/sep
I downloaded the csv file from the trading platform.
Part of the project is to understand the distribution of the trade duration, this time is not explicit in the csv file, so I need to calculate the time base oon the date order. For the meantime I'm gonna ignored the trades that lasted mnore than a day and only I'm gonna take the operations in which almost the same amount of stocks were bought and sold the same day. 
## 2026-09-09

### Findings
- 10:00–10:05 has the highest observed probability of an upward movement (~76%).
- 10:10–10:15 shows a lower probability but a larger median upward movement.
- 10:40–10:50 may offer an intermediate probability/magnitude trade-off.

### Next step
Compare upward and downward movements and evaluate potential TP/SL levels using ATR, MFE and MAE.