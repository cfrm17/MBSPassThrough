# MBS Pass Through

Mortgage Backed Securities (MBS) are essentially interest rate derivatives, this requires both a robust interest rate model as well as a model for the prepayment behavior. The prepayment model is dynamic, since prepayments depend very strongly on the dynamics of prevailing mortgage rates. These mortgage rate dynamics are driven by the dynamics of the yield curve.

MBS cash flows depend very strongly on the prepayment assumptions of the underlying pools. The prepayments are both substantial and heavily dependent on the prevailing mortgage rates. Hence, it is very important to use a reliable prepayment model if one hopes to “accurately” capture the risk of these instruments.

The model is calibrated to market prices for Caps and Swaptions with varying strikes. There is a fair bit of flexibility in how the interest model is used, and we will focus most of our discussion on how we have chosen to use the model.

Essentially, one uses as input the current yield curve (LIBOR), and as usual this consists of spot rates at various terms less than two years. For terms two years and longer, one specifies the relevant swap rates. In addition to this market information, one has the option to use Eurodollar futures rates to define the short term discount factors. 

In addition to these rates, the model also takes in data related to caps and swaptions of various terms and tenors. These are specified as “at the money” options, and one also has the flexibility of specifying “out of the money” black volatilities. This information is used to calibrate the volatility structure of the interest rate model

The basic assumption in the model is that potentially all (specified) spot rates and swap rates follow a process, and these separate processes are somehow correlated with each other. These processes can be specified as lognormal, normal or constant elasticity volatility (CEV) with a certain α.

We start first by discussing the “short rate” dynamics, which are the fundamental rates that define the discounting process. In the system, we use the one month rate as
the short rate, and we generate Monte Carlo paths using equal time steps. We chose each time step to correspond to 365.25/12 days, in order to account for leap years. For a particular choice of the volatility σ(t) and a choice of the drift d(t), we generate the Monte Carlo paths using the usual assumption of lognormality of the process. 

We will denote these rates for a particular simulation number s at a particular time t by r(t, s). With this notation, r(0, s) = r, where r is the one month spot rate corresponding to the valuation date. In order to fix the drift rate d(t), we calibrate to the exact Monte Carlo paths generated by ensuring today’s zero coupon bond prices (or discount factors) are replicated. 

The volatility structure described above is determined by matching to market cap prices. In order to determine the volatility σ(t), some assumptions need to be made. DS assumes that the three month spot rates share the same volatility structure, and these rates are perfectly correlated. With this, we can price three month caps of a certain maturity and calibrate the volatilities σ(t) to match the market prices.  

Specifically, we start with pricing 12 month caps and assuming the volatility σ(t) is constant for the first 12 months. For a particular volatility and drift assumption dc(t)1, we have a corresponding path for the three month spot rates rc(t, s), where this is actually a series of three month forward rates at t along a particular path s. Choosing a particular σ(t) necessitates a choice of d(t) in order to match current bond prices (see https://finpricing.com/lib/FiBond.html), as explained in the preceding paragraph. This determines “discount factor” paths D(T, s).

This is simply the value of a discounted cash flow at month t + 3 where the cash flow is specified by the three month rate at time t. This determines the drift dc(t), and then we calculate the price of a 12 month at-the-money cap and compare to the market. To value the cap, we use the full three month spot rate path and discount the appropriate cash flows using the discount factor paths D(T, s), and take the expectation over all Monte Carlo paths. 





