# ATRLatest

```
#
# TD Ameritrade IP Company, Inc. (c) 2014-2018
#
# Uses basic most recent value from ATR study then 
# displays value in a colorized label
#
# Modified by Nate Geslin (teamtomkins23@gmail.com)

declare upper;

input length = 21;
input yellowThreshold = 0.025;
input greenThreshold = 0.05;
input averageType = AverageType.WILDERS;

def ATR = MovingAverage(averageType, TrueRange(high, close, low), length);
def currentAtr = ATR[0];

AddLabel(
    1, 
    "ATR (" + length + "): " + Astext(currentAtr, NumberFormat.TWO_DECIMAL_PLACES), 
    if currentAtr > (close * yellowThreshold) then color.YELLOW else if currentAtr > (close * greenThreshold) then color.GREEN else color.GRAY
);
```

# IVRankPercentileAndStdDeviation

```java
# Plots colorized IVRank
# Provdes labels for STD Deviations on Day, Week and Month 
# 
# script taken from:
# http://iv-rank.blogspot.com/
# https://www.youtube.com/watch?v=lkxVvyBMGkE
#
# Line colorization implemented by Nate Geslin (teamtomkins23@gmail.com)

declare lower;
declare hide_on_intraday;

#IVPercentile
def vol = imp_volatility();
input DisplayIVPercentile = yes;
input DisplayImpVolatility= yes;
input DisplayDaily1StandardDev = yes;
input DisplayWeekly1StandardDev = yes;
input DisplayMonthly1StandardDev = yes;

input TimePeriod = 252;
# hint 
# - 21 = 1 month
# - 42 = 2 months
# - 63 = 3 months
# - 252 = 1 year

def data = if !isNaN(vol) then vol else vol[-1];
def hi = highest(data, TimePeriod);
def lo = lowest(data, TimePeriod);
plot Percentile = (data – lo) / (hi – lo) * 100;
def lowend = Percentile < 25;
def highend = Percentile > 50;

#Labels
addlabel(DisplayImpVolatility, concat(“ImpVolatility: “,aspercent(vol)), if lowend then color.red else if highend then color.green else color.yellow);

addlabel(DisplayIVPercentile , concat(“IV Rank: “,aspercent(Percentile / 100)), if lowend then color.red else if highend then color.green else color.yellow);

def ImpPts = (vol / Sqrt(252)) * close;
AddLabel(DisplayDaily1StandardDev , Concat(“Daily 1 SD +/- $”, Astext( ImpPts, NumberFormat.TWO_DECIMAL_PLACES)), if lowend then color.red else if highend then color.green else color.yellow); ;

def ImpPts2 = (vol / Sqrt(52)) * close;
AddLabel(DisplayWeekly1StandardDev, Concat(“Weekly 1 SD +/- $”, Astext( ImpPts2, NumberFormat.TWO_DECIMAL_PLACES)), if lowend then color.red else if highend then color.green else color.yellow); ;

def ImpPts3 = (vol / Sqrt(12)) * close;
AddLabel(DisplayMonthly1StandardDev, Concat(“Monthly 1 SD +/- $”, Astext( ImpPts3, NumberFormat.TWO_DECIMAL_PLACES)), if lowend then color.red else if highend then color.green else color.yellow); ;

plot LowVol = 25;
plot HighVol = 50;

LowVol.SetDefaultColor(GetColor(5));
Percentile.AssignValueColor(if Percentile > HighVol then Color.GREEN else Color.RED);
```

# StochasticFullDoubleOver
```
#
# TD Ameritrade IP Company, Inc. (c) 2008-2018
#
# StochasticFullDoubleOver
# 
# Provides standard StochasticFull with two additional overbought/oversold plots
#
# Modified by Nate Geslin (teamtomkins23@gmail.com)

declare lower;

input over_bought = 80;
input pre_over_bought = 70;
input pre_over_sold = 30;
input over_sold = 20;
input KPeriod = 10;
input DPeriod = 10;
input priceH = high;
input priceL = low;
input priceC = close;
input slowing_period = 3;
input averageType = AverageType.SIMPLE;
input showBreakoutSignals = {default "No", "On FullK", "On FullD", "On FullK & FullD", "Only Double Over"};

def lowest_k = Lowest(priceL, KPeriod);
def c1 = priceC - lowest_k;
def c2 = Highest(priceH, KPeriod) - lowest_k;
def FastK = if c2 != 0 then c1 / c2 * 100 else 0;

plot FullK = MovingAverage(averageType, FastK, slowing_period);
plot FullD = MovingAverage(averageType, FullK, DPeriod);

plot OverBought = over_bought;
plot PreOverBought = pre_over_bought;
plot PreOverSold = pre_over_sold;
plot OverSold = over_sold;

def upK = FullK crosses above OverSold;
def upD = FullD crosses above OverSold;
def upKpre = FullK crosses above PreOverSold;
def upDpre = FullD crosses above PreOverSold;
def downKpre = FullK crosses below PreOverBought;
def downDpre = FullD crosses below PreOverBought;
def downK = FullK crosses below OverBought;
def downD = FullD crosses below OverBought;

plot UpSignal;
plot DownSignal;
switch (showBreakoutSignals) {
case "No":
    UpSignal = Double.NaN;
    DownSignal = Double.NaN;
case "On FullK":
    UpSignal = if upK then OverSold else Double.NaN;
    DownSignal = if downK then OverBought else Double.NaN;
case "On FullD":
    UpSignal = if upD then OverSold else Double.NaN;
    DownSignal = if downD then OverBought else Double.NaN;
case "On FullK & FullD":
    UpSignal = if upK or upD then OverSold else Double.NaN;
    DownSignal = if downK or downD then OverBought else Double.NaN;
case "Only Double Over":
    # if FullK crosses FullD and between PreOverSold and OverSold
    # if FullK crosses FullD and between PreOverBought and OverBought 
    UpSignal = double.NaN;
    DownSignal = double.NaN;
}

UpSignal.setHiding(showBreakoutSignals == showBreakoutSignals."No");
DownSignal.setHiding(showBreakoutSignals == showBreakoutSignals."No");

FullK.SetDefaultColor(GetColor(5));
FullD.SetDefaultColor(GetColor(0));
OverBought.SetDefaultColor(GetColor(1));
PreOverBought.SetDefaultColor(color.DARK_GRAY);
PreOverBought.SetStyle(Curve.SHORT_DASH);
PreOverSold.SetDefaultColor(color.DARK_GRAY);
PreOverSold.SetStyle(Curve.SHORT_DASH);
OverSold.SetDefaultColor(GetColor(1));
UpSignal.SetDefaultColor(Color.UPTICK);
UpSignal.SetPaintingStrategy(PaintingStrategy.ARROW_UP);
DownSignal.SetDefaultColor(Color.DOWNTICK);
DownSignal.SetPaintingStrategy(PaintingStrategy.ARROW_DOWN);
```

# TVI
```
#
# TD Ameritrade IP Company, Inc. (c) 2007-2018
#
# Implements TradeVolumnIndex with colorized plot for positive/negative values
#
# Implemented by Nate Geslin (teamtomkins23@gmail.com)

declare lower;

input minTickValue = 0.125;

assert(minTickValue >= 0, "'min tick value' must not be negative: " + minTickValue);

def diff = close - close[1];
def direction = if diff > minTickValue then 1 else if diff < -minTickValue then -1 else direction[1];

plot TVI = TotalSum(direction * volume);
plot ZeroLine = 0;

TVI.AssignValueColor(if TVI > ZeroLine then Color.GREEN else Color.RED);
ZeroLine.SetDefaultColor(GetColor(5));
```