# Timelevels (Subcycling Case)

* **Evolution variables:** These always maintain a single timelevel. They use [`ks_source_band`/`ks_consumer_band`](https://github.com/lwJi/CarpetX/blob/30f28c604c4401567288ffe11367398ad2a48154/CarpetX/src/driver.hxx#L473) to store the data required for dense output.
* **Non-evolution variables:** These also generally use a single timelevel. However, two timelevels are required if you need to time-average prior to prolongation (i.e., when the coarse and fine grids are misaligned).
