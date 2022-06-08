# Amp Joule: ML Demo App

## Tools Used
- Google Cloud Platform (BigQuery, AutoML Tables, Cloud Storage)
- Docker (serve the model locally)
- Curl (HTTP-based interface to make predictions)
- TensorFlow (exported model from AutoML Tables)
- SQL (data cleansing)

## Overview

- Downloaded https://opendata.dc.gov/datasets/DCGIS::building-energy-benchmarking/ in CSV format and assessed data table, decided to build a regression model with "ELECTRICITYUSE_GRID_KWH" as the target column (rather random choice without much SME expertise, just for demo purposes)

- Used GCP's BigQuery service to clean the table and remove any rows from the data where the target column was NULL since GCP's Auto ML Tables doesn't work when there are NULL values for the target column to be predicted (this is a brute force approach because of time and demo constraints, more sophisticated would be imputing missing values, etc.)

![BQ](screenshots/clean_data_table_in_gcp_bigquery.png)

- Trained model with GCP's Auto ML Tables on all 72 input columns which does a lot of data science & ML work behind the scenes (like one-hot-encoding of categorical features, feature engineering, ensemble methods, etc.) that would take tons of experimentation and time to replicate manually

![AutoML](screenshots/training_model_features_and_target.png)

![AutoML](screenshots/training_model_with_gcp_automl_tables.png)

![AutoML](screenshots/model_evaluation.png)

- Exported AutoML TensorFlow model into `model` directory

## Usage

- Clone repository and then CD into `model` folder and run this command to start the model server (this can take some time the first time around to pull the necessary Docker images):
```
docker run -v `pwd`/model-export/tbl/tf_saved_model-building_energy_benchmark:/models/default/0000001 -p 8080:8080 -it gcr.io/cloud-automl-tables-public/model_server
```

If successful, the CLI output will look like this:

![Docker](screenshots/docker_run.png)

- In another terminal window, run this command from the root directory to make predictions:
```
curl -X POST -H "Content-Type: application/json; charset=utf-8"  --data '@request.json' http://localhost:8080/predict
```

![Predictions](screenshots/predictions.png)

## Improvements
- This is just a quickly thrown together prototype to demonstrate the requested workflow including the desired features
- I used BigQuery and AutoML Tables for rapid prototyping and because I was already familiar with using those GCP services (the resulting TensorFlow model could be deployed anywhere though)
- The model probably has too many input features (72), many of them probably don't contribute to the value of the predictions and make it more difficult to inperpret so one thing to improve it is experiment which features contribute most to predict the desired target column
- Generally for any ML model, the more data, the better the predictions, so try to increase the training data would most likely result in a better model
- Training/evaluation/test data should be stored in AWS S3 buckets or GCP Cloud Storage
- To productionize this, I would create an ML pipeline that includes all the steps from data cleaning over training to making predictions in a coherent workflow, with something like https://www.kubeflow.org/ or https://aws.amazon.com/sagemaker/pipelines/ 