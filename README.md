---
layout: "post"
title: "Create Dataset and table using bq"
date: "2018-09-28 22:50"
---


#### Create dataset using bq mk
```
bq mk --dataset vmnamburu:PythonClientTest
```

#### Create table using bq mk
```
bq mk --table vmnamburu:PythonClientTest.us_states name:STRING,abbreviation:STRING
```
#### Load table using bq load from gcs
```
bq load --source_format=NEWLINE_DELIMITED_JSON vmnamburu:PythonClientTest.us_states gs://vmnamburu/us-states.json --autodetect
```

#### Prepare data for loading
1. create bucket
2. copy us_state.json file
3. change acl to provide read access to bq gserviceaccount
```
gsutil mb gs://vmnamburu
gsutil cp us_states.json gs://vmnamburu
gsutil acl ch -u bq-service-account@vmnamburu.iam.gserviceaccount.com:R gs://vmnamburu/us_states.json
```

#### Load data from local to gcs
```
bq load --source_format=NEWLINE_DELIMITED_JSON PythonClientTest.us_states gs://vmnamburu/us_states.json name:STRING,abbreviation:STRING
```
