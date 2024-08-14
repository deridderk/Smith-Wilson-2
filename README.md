# This package ...
This package can be used to extrapolate forward rates, spot rates and other outputs using the method developed by EIOPA for the extrpolation of risk-free term structures. Mathematical details can be found here: https://www.eiopa.europa.eu/document/download/0ab82697-40a6-49ad-930f-211b649c1be9_en?filename=EIOPA-BoS-24-099-Report-on-the-Calculation-of-the-UFR-for-2025.pdf.
Using as input observed forward rates for the next 20 (or other value) years,
the code extrapolates these rates for a given timeframe, e.g., 80 years. The other necessary inputs are explained below.

# Code Origin
The entirety of this code was written by Kilian de Ridder.
The code reproduces the VBA code EIOPA provides for use in excel.

# Main function, inputs and outputs
The main function is:
```extrapolate_with_sw(rates_obs, ufr, t2, Tau, extrapolation_target, nrofcoup, instrument, alfamin)```

## Inputs
```rates_obs```: is a vector of observed liquid yields, e.g., for the next twenty years.
```ufr``` (ultimate forward rate): the rate the extrapolation of forward rates should ultimately converge to
```t2```: the convergence maturity, that is, the point at which fitted forward rates should have converged to the ufr, where we specify how close this convergence needs to be.
```Tau```: The convergence tolerance in basis points. The fitted forward rates are constrained to be less or equal than Tau basis points away from the ufr at the convergence maturity.
```extrapolation_target```: the maturity until which our extrapolation should be executed.
```nrofcoup```: The number of coupons payments per year, if there are any. Put 1 (or anything else) for coupon-less instruments, the nrofcoup will be set to 1 by the code.
```instrument```: arguments are "Zero", "Bond", "Swap". "Zero" is for any instruments without coupon payments, for example, payments that have to be made at one point in time without prior interest payments. "Bond" and "Swap" for those instruments with coupon payments inbetween.
```alfamin```: The minimum convergence speed, should be set to 0.05 according to EIOPA.

## Outputs
The function returns a pandas DataFrame with 7 columns and (extrapolation_target + 1) rows. The columns are:
maturities
discount factor
spot intensity
spot rate
forward intensity
forward rate cc
forward rate ac
And thus reproduce the outputs the vba code in excel also produces.

# How to use the package
1. Install the package using ```py -m pip install smithwilson2```, where some don't need ```py -m```
2. Import the package using ```import smithwilson2.smithwilson2 as sw```
3. Specify the above inputs, using ```alfamin = 0.05``` and the current ufr published by EIOPA (e.g., 0.33).
4. Call the ```sw.extrapolate_with_sw(rates_obs, ufr, t2, Tau, extrapolation_target, nrofcoup, instrument, alfamin)``` function, e.g.
```output = (rates_obs, ufr, t2, Tau, extrapolation_target, nrofcoup, instrument, alfamin)```, then
```print(output)```.
This output can then easily be written to a csv file.

# Example file
```
import smithwilson2.smithwilson2 as sw
from math import log
import numpy as np
import pandas as pd


rates_obs =  [
    3.590/100,
    3.318/100,
    3.126/100,
    2.998/100,
    2.911/100,
    2.867/100,
    2.829/100,
    2.810/100,
    2.806/100,
    2.798/100,
    2.805/100,
    2.807/100,
    2.812/100,
    2.810/100,
    2.806/100,
    2.799/100,
    2.785/100,
    2.769/100,
    2.750/100,
    2.727/100
    ]
# Enter convergence maturity
t2 = 60

# Enter ufr
ufr = 0.033

# Enter minimum alpha, for EIOPA-requirements keep this at 0.05
alfamin = 0.05

# Enter until which maturity we should extrapolate
extrapolation_target = 80


# the number of coupons
nrofcoup = 1
# for instrument case "Zero" (no coupon payments) this is set to 1 in the code no matter what we specify here

# the convergence tolerance in basis points, i.e., how far we allow our extrapolated forward rates to be away from the ufr at the convergence maturity t2
Tau = 1

instrument = "Zero"
sw.extrapolate_with_sw(rates_obs, ufr, t2, Tau, extrapolation_target, nrofcoup, instrument, alfamin)

```




