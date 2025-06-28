# Volatility-Curve-Prediction
Competition Host: NK Security Research
#Description:
    Develop a model that predicts implied volatilities for NIFTY50 index options using high-frequency market data.
    Implied volatility is more than just a number — it reflects how the market prices future movement across different strikes and maturities.

#About the Data:
    train.parquet
      The training dataset contains all historical market data.
          -timestamp: The timestamp index representing the second associated with each row.
          -underlying: The value of NIFTY50.
          -expiry: The expiry date of the option (data is comprised of weekly expiry tokens).
          -{call/put}_iv_{K}: The Black-Scholes implied volatility of a European Call/Put option with strike K.
          -X{0,...,41}: A set of anonymized market features derived from proprietary data sources.
    test.parquet
      The test dataset has the same structure as train.parquet, with the following differences:
          -timestamp: To prevent future peeking, all timestamps are masked, shuffled, and replaced with a unique ID.
          -expiry: This column is omitted from the test data. It is guaranteed that all of the test data is derived from options of the same weekly expiry.
          -{call/put}_iv_{K}: Several of these entries are masked with Nan values. These are to be predicted.
