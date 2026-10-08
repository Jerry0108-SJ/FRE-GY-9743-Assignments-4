# HW4: Yield Curves from Instantaneous Forward Rates

NYU FRE-GY 9743, Fall 2026.

Build a yield-curve model directly from instantaneous forward rates (IFR),
price a fixed cashflow, and calculate analytic sensitivities to the model's
internal IFR parameters. Sample data are provided in the notebook; no external
market data or instrument calibration is required.

## Getting started

Use Python 3.11. From the project root, install the dependencies and open
[hw4_ifr_yield_curve.ipynb](hw4_ifr_yield_curve.ipynb):

```sh
python -m pip install -r requirements.txt
python -m jupyter notebook hw4_ifr_yield_curve.ipynb
```

Select a Python 3.11 kernel with these dependencies installed. Complete the
TODOs below, then run the notebook from top to bottom. After editing library
`.py` files, restart the kernel and rerun the notebook to load your changes.

The student version contains unfinished functions and notebook expressions;
it will not run to completion until they are filled in. Saved outputs come
from the completed example and are not evidence that the current TODOs run.

## Tasks

Complete these in order, keeping the provided function signatures:

1. In [fixedincomelib/utilities/numerics.py](fixedincomelib/utilities/numerics.py),
   implement the four TODO methods in `Interpolator1DPCP`: `interpolate`,
   `integrate`, `gradient_wrt_ordinate`, and
   `gradient_of_integrated_value_wrt_ordinate`. Use piecewise-constant
   left-continuous interpolation and flat extrapolation. The class docstring
   gives examples of the interval convention.
2. In [fixedincomelib/yield_curve/yield_curve_model.py](fixedincomelib/yield_curve/yield_curve_model.py),
   complete `YieldCurveModelComponent.discount_factor` using its IFR
   interpolator, then `YieldCurve.discount_factor` by recursively following
   component references and combining discount factors. Follow the inline hints.
3. In notebook Part 5.3, complete the analytic risk TODOs for both components.
   Differentiate with respect to IFR node values while holding node times and
   the cashflow amount fixed. Do not use bumping or apply 1 bp scaling.

The data containers, build methods, model setup, public APIs, product creation,
and price calculation are provided.

## Model and notebook workflow

The notebook builds two components from the registered IFR conventions:

| Component | Data convention | Default input |
|---|---|---|
| `SOFR-1B` | `SOFR-1B-IFR` | IFR nodes from 1Y to 10Y |
| `SOFR-1B-FLAT` | `SOFR-1B-FUNDING-IFR` | One 1Y node with zero IFR spread |

`SOFR-1B-FLAT` references `SOFR-1B`. Its complete discount factor combines
the reference curve with its own spread component. Trivial calibration copies
the input IFR values into component state. Rates are annual decimals:
`0.03` means 3%.

Notebook Parts 1-3 load the data, define build methods, and create the model.
Part 4 queries discount factors through `qfDiscountFactor`. Part 5 contains
three separate code cells: create a `ProductFixedAccruedCashflow`, calculate
its price, and calculate its analytic risk.

The fixed payment is `product.notional * product.accrued`; `accrued` is a year
fraction. The example product uses ACT/360, while curve times use ACT/ACT
(ISDA). The default risk vector contains 10 SOFR IFR sensitivities and one
funding IFR sensitivity. Include the funding parameter even though its current
value is zero.
