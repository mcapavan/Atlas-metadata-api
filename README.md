# Atlas-metadata-api

Creating Custom Types and Entities in Atlas - Ingest two sample JSON files (input and output) to Atlas Metadata API and connect them with process to show case lineage in Atlas 
This is copy of the work done at [creating-custom-types-and-entities-in-atlas](https://community.hortonworks.com/articles/91237/creating-custom-types-and-entities-in-atlas.html) but using a different and simple JSON files


### Objective
Atlas, by default comes with certain types for Hive, Storm, Falcon etc. However, I need to capture some custom metadata in Atlas. This is metadata related to ETL processes, enterprise-operations etc. The article explains how to create custom Atlas types and provides some insight on establishing lineage between said types.

The eventual lineage I need to capture would look something like below:

![Alt text](/images/expectedLineage.png?raw=true "Expected Lineage")

Files:-

1. atlas_type_SampleInputDataSet.json
2. atlas_entity_SampleInputDataSet.json
3. atlas_type_SampleOutputDataSet.json
4. atlas_entity_SampleOutputDataSet.json
5. atlas_type_SampleProcess.json
6. atlas_entity_SampleProcess.json

Step 1: Create Custom Atlas SampleInputDataset Type:

```bash
curl -i -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -u admin:admin 'http://pchalla0.field.hortonworks.com:21000/api/atlas/types' -d @atlas_type_SampleInputDataSet.json
```

Step 2: Create Entity for SampleInputDataset Type:
```bash
curl -i -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -u admin:admin 'http://pchalla0.field.hortonworks.com:21000/api/atlas/entities' -d @atlas_entity_SampleInputDataSet.json
```

Step 3: Create Custom Atlas SampleOutputDataset Type:

```bash
curl -i -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -u admin:admin 'http://pchalla0.field.hortonworks.com:21000/api/atlas/types' -d @atlas_type_SampleOutputDataSet.json
```

Step 4: Create Entity for SampleOutputDataset Type:
```bash
curl -i -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -u admin:admin 'http://pchalla0.field.hortonworks.com:21000/api/atlas/entities' -d @atlas_entity_SampleOutputDataSet.json
```

Step 5: Create a sample Process Type (SampleProcess) which would complete the lineage information:
```bash
curl -i -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -u admin:admin 'http://pchalla0.field.hortonworks.com:21000/api/atlas/types' -d @atlas_type_SampleProcess.json
```

Step 6: Identify the UUID for <> by executing the below commands

```bash
curl -u admin:admin -X GET http://pchalla0.field.hortonworks.com:21000/api/atlas/entities?type=SampleInputDataset
```
*extract uuid eg: 95351d6c-18f9-4b07-90b2-c15613925a37 from the output*
```bash 
curl -u admin:admin -X GET http://pchalla0.field.hortonworks.com:21000/api/atlas/entities?type=SampleOutputDataset
```
*extract uuid eg: 07be282b-e75c-416f-895a-631f6bb7e844 from the output*

The above two uuids are required to update in **atlas_entity_SampleProcess.json** at inputs and outputs section. Once the file is updated with the correct uuids run the final step to create lineage between InuptDataset and Outputdataset

Step 7: Create Entity for SampleProcess Type:
```bash
curl -i -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -u admin:admin 'http://pchalla0.field.hortonworks.com:21000/api/atlas/entities' -d @atlas_entity_SampleProcess.json
```

We can view the Input Dataset, Output Dataset and lineage from Atlas UI as below.

![Alt text](/images/SampleInputDataset.png?raw=true "SampleInputDataset")

![Alt text](/images/SampleOutputDataset.png?raw=true "SampleOutputDataset")

![Alt text](/images/SampleProcess.png?raw=true "SampleProcess")


References:

https://community.hortonworks.com/articles/91237/creating-custom-types-and-entities-in-atlas.html

https://community.hortonworks.com/questions/42114/atlas-rest-api.html

https://atlas.apache.org/0.7.1-incubating/AtlasTechnicalUserGuide.pdf

https://atlas.apache.org/TypeSystem.html
