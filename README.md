---
layout: "post"
title: "Create Dataset and table using bq"
date: "2018-10-02 22:50"
---


#### Create dataset using bq mk
```
bq mk --dataset vmnamburu:PythonClientTest
```

#### Create table using bq mk with inline schema
```
bq mk --table vmnamburu:PythonClientTest.us_states name:STRING,abbreviation:STRING
```

### Create a JSON Schema file
```
[
  {
    "name": "name",
    "type": "STRING",
    "mode": "REQUIRED"
  },
  {
    "name": "abbreviation",
    "type": "STRING",
    "mode": "REQUIRED"
  }
]

```


#### Create table using bq mk with json schema
```
bq mk --table vmnamburu:PythonClientTest.us_states ./us_states_schema.json
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

#### Load table using bq load from gcs
```
bq load --source_format=NEWLINE_DELIMITED_JSON vmnamburu:PythonClientTest.us_states gs://vmnamburu/us-states.json --autodetect
```

#### Load data from local to gcs
```
bq load --source_format=NEWLINE_DELIMITED_JSON PythonClientTest.us_states gs://vmnamburu/us_states.json name:STRING,abbreviation:STRING
```

### Load data using a query
```
bq query --destination_table vmnamburu:PythonClientTest.us_states_cp --use_legacy_sql=false 'select name,abbreviation from PythonClientTest.us_states'
```

### Append data using a query using --append_table write disposition
```
bq query --destination_table vmnamburu:PythonClientTest.us_states_cp --append_table --use_legacy_sql=false 'select name,abbreviation from PythonClientTest.us_states where name like "A%" '
```

### Append data using a query using --replace write disposition
```
bq query --destination_table vmnamburu:PythonClientTest.us_states_cp --replace --use_legacy_sql=false 'select name,abbreviation from PythonClientTest.us_states where name like "A%" '
```

#### Show shcema of a table
```
bq show --schema --format=prettyjson vmnamburu:PythonClientTest.us_states > us_states_schema.json
```


## Table Metadata

```
bq query 'select * from PythonClientTest.__TABLES_SUMMARY__'
```

```
bq show --format=pretty PythonClientTest.__TABLES_SUMMARY__
```
