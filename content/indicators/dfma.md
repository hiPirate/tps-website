---
title: "Deconstructed FMAs + Crosses"
date: 2021-07-08T06:15:00-05:00
draft: false
ShowToc: true
categories: ["pine scripts", "tradingview"]
tags: ["moving-averages"]
---

### Summary
Coming soon.

---

### Key Points
Coming soon.

---

### Preview
![DFMA](/pine/dfma/dfma.png#center)

---

### Pine Script
{{< highlight tv >}}
// © Pirate
//@version=4

study("[Pirate] Deconstructed FMAs + Crosses",shorttitle="DFMA + Crosses",overlay=true)

//---------------------------------------------------------------------------------------------------------//
//-- Global Variables
//---------------------------------------------------------------------------------------------------------//
INVISIBLE=color.new(#ffffff, 100)
GREEN=color.new(#3faac0, 0)
RED=color.new(#c0553f, 0)
GOLDEN=color.new(#fbc02d, 0)
IVORY=color.new(#eaeae0, 0)
LIMIT=500
MA_GROUP="Long/Short Moving Averages"
RATIO_GROUP="Golden Ratio = Long MA + Short MA"
BAR_GROUP="Colored Bars"
SOURCE=input(title="Source",type=input.source,defval=close)
WEIGHT=input(title="Weight",defval="Weighted",
     options=["Exponential","Simple","Running","Weighted","Volume-Weighted"])
RATIO_DEFAULT="89/144"
RATIO=input(
     title="Long/Short Ratio",
     group=MA_GROUP,
     defval=RATIO_DEFAULT, 
     options=["1/2", "2/3", "3/5", "5/8", "8/13", "13/21", "21/34", "34/55", "55/89", "89/144", "144/233"])

//---------------------------------------------------------------------------------------------------------//
//-- Menu Options
//---------------------------------------------------------------------------------------------------------//
// Toggle Attribues On/Off
Long_io=input(title="[Enable/Disable] Fast",group=MA_GROUP,type=input.bool,defval=true)
Short_io=input(title="[Enable/Disable] Slow",group=MA_GROUP,type=input.bool,defval=true)

Ratio_io=input(title="[Enable/Disable] Golden Ratio",group=RATIO_GROUP,type=input.bool,defval=true)
Gcross_io=input(title="[Enable/Disable] Golden Cross",group=RATIO_GROUP,type=input.bool,defval=true)
Dcross_io=input(title="[Enable/Disable] Death Cross",group=RATIO_GROUP,type=input.bool,defval=true)

// Set Cross Text Color
Gcross_text_color=input(title="Golden Cross Text Color:",group=RATIO_GROUP,type=input.color,defval=GOLDEN)
Dcross_text_color=input(title="Death Cross Text Color",group=RATIO_GROUP,type=input.color,defval=IVORY)

//---------------------------------------------------------------------------------------------------------//
//-- Functions
//---------------------------------------------------------------------------------------------------------//
// Set Short MA Period
var Short_period = 
     RATIO == "1/2" ? 1 :
     RATIO == "2/3" ? 2 :
     RATIO == "3/5" ? 3 :
     RATIO == "5/8" ? 5 :
     RATIO == "8/13" ? 8 :
     RATIO == "13/21" ? 13 :
     RATIO == "21/34" ? 21 :
     RATIO == "34/55" ? 34 :
     RATIO == "55/89" ? 55 :
     RATIO == "89/144" ? 89 :
     RATIO == "144/233" ? 144 :
     na
    
// Set Long MA Period
var Long_period = 
     RATIO == "1/2" ? 2 :
     RATIO == "2/3" ? 3 :
     RATIO == "3/5" ? 5 :
     RATIO == "5/8" ? 8 :
     RATIO == "8/13" ? 13 :
     RATIO == "13/21" ? 21 :
     RATIO == "21/34" ? 34 :
     RATIO == "34/55" ? 55 :
     RATIO == "55/89" ? 89 :
     RATIO == "89/144" ? 144 :
     RATIO == "144/233" ? 233 :
     na

// Set Ratio Period
var Ratio_period=((Long_period+Short_period))

// Set Fast MA Weight
Long_line=(
     WEIGHT == "Exponential" ? ema(SOURCE,Long_period) :
     WEIGHT == "Simple" ? sma(SOURCE,Long_period) :
     WEIGHT == "Running" ? rma(SOURCE,Long_period) :
     WEIGHT == "Weighted" ? wma(SOURCE,Long_period) :
     WEIGHT == "Volume-Weighted" ? vwma(SOURCE,Long_period) :
     na)

// Set Slow MA Weight
Short_line=(
     WEIGHT == "Exponential" ? ema(SOURCE,Short_period) :
     WEIGHT == "Simple" ? sma(SOURCE,Short_period) :
     WEIGHT == "Running" ? rma(SOURCE,Short_period) :
     WEIGHT == "Weighted" ? wma(SOURCE,Short_period) :
     WEIGHT == "Volume-Weighted" ? vwma(SOURCE,Short_period) :
     na)

// Set Ratio Weight
Ratio_line=(
     WEIGHT == "Exponential" ? ema(SOURCE,Ratio_period) :
     WEIGHT == "Simple" ? sma(SOURCE,Ratio_period) :
     WEIGHT == "Running" ? rma(SOURCE,Ratio_period) :
     WEIGHT == "Weighted" ? wma(SOURCE,Ratio_period) :
     WEIGHT == "Volume-Weighted" ? vwma(SOURCE,Ratio_period) :
     na)

// Cross Conditions
Gcross=crossunder(Long_line,Short_line)
Dcross=crossover(Long_line,Short_line)

// Plot Golden Cross Label
Gcross_label=(Gcross_io ? (Gcross ? label.new(
     x=bar_index,
     y=high,
     style=label.style_triangleup,
     color=GOLDEN,
     yloc=yloc.belowbar,
     textcolor=GOLDEN):na) :na)

// Plot Death Cross Label
Dcross_label=(Dcross_io ? (Dcross ? label.new(
     x=bar_index,
     y=high,
     style=label.style_triangledown,
     color=IVORY,
     yloc=yloc.abovebar,
     textcolor=IVORY):na) :na)

// Set Label Text | String
label.set_text(Gcross_label,tostring(close))
label.set_text(Dcross_label,tostring(close))

// Morph Label Text | String -> Float
Gcross_price=tonumber(label.get_text(Gcross_label))
Dcross_price=tonumber(label.get_text(Dcross_label))

// Plot Lines
Long=plot(Long_io ? Long_line : na,title="Long MA",color=RED,linewidth=2)
Short=plot(Short_io ? Short_line : na,title="Short MA",color=GREEN,linewidth=2)
Ratio=plot(Ratio_io ? Ratio_line : na,title="Ratio",color=GOLDEN,linewidth=2)
{{< /highlight >}}

---

### More Information
> [View this indicator on GitHub](https://github.com/PirateCrypto/TradingView/blob/main/Indicators/%5BPirate%5D%20Deconstructed%20FMAs%20%2B%20Crosses.pine)