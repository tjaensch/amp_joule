# amp_joule
ML demo app

- Downloaded https://opendata.dc.gov/datasets/DCGIS::building-energy-benchmarking/ in CSV format and assessed data table, decided to build a regression model with "ELECTRICITYUSE_GRID_KWH" as the target column (rather random choice without much SME expertise, just for demo purposes)
- Used GCP's BigQuery service to clean the table and remove any rows from the data where the target column was NULL since GCP's Auto ML Tables doesn't work when there are NULL values for the target column to be predicted (this is a brute force approach because of time and demo constraints, more sophisticated would be imputing missing values, etc.)
- Train model with GCP's Auto ML tables on all 72 input columns which does a lot of data science & ML work behind the scenes (like one-hot-encoding of categorical features, feature engineering, ensemble methods, etc.) that would take tons of experimentation and time to replicate manually
