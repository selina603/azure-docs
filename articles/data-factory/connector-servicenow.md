---
title: Copy data from ServiceNow using Azure Data Factory | Microsoft Docs
description: Learn how to copy data from ServiceNow to supported sink data stores by using a copy activity in an Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/28/2018
ms.author: jingwang

---
# Copy data from ServiceNow using Azure Data Factory

This article outlines how to use the Copy Activity in Azure Data Factory to copy data from ServiceNow. It builds on the [copy activity overview](copy-activity-overview.md) article that presents a general overview of copy activity.

## Supported capabilities

You can copy data from ServiceNow to any supported sink data store. For a list of data stores that are supported as sources/sinks by the copy activity, see the [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) table.

Azure Data Factory provides a built-in driver to enable connectivity, therefore you don't need to manually install any driver using this connector.

## Getting started

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

The following sections provide details about properties that are used to define Data Factory entities specific to ServiceNow connector.

## Linked service properties

The following properties are supported for ServiceNow linked service:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property must be set to: **ServiceNow** | Yes |
| endpoint | The endpoint of the ServiceNow server (`http://<instance>.service-now.com`).  | Yes |
| authenticationType | The authentication type to use. <br/>Allowed values are: **Basic**, **OAuth2** | Yes |
| username | The user name used to connect to the ServiceNow server for Basic and OAuth2 authentication.  | Yes |
| password | The password corresponding to the user name for Basic and OAuth2 authentication. Mark this field as a SecureString to store it securely in Data Factory, or [reference a secret stored in Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| clientId | The client ID for OAuth2 authentication.  | No |
| clientSecret | The client secret for OAuth2 authentication. Mark this field as a SecureString to store it securely in Data Factory, or [reference a secret stored in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| useEncryptedEndpoints | Specifies whether the data source endpoints are encrypted using HTTPS. The default value is true.  | No |
| useHostVerification | Specifies whether to require the host name in the server's certificate to match the host name of the server when connecting over SSL. The default value is true.  | No |
| usePeerVerification | Specifies whether to verify the identity of the server when connecting over SSL. The default value is true.  | No |

**Example:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## Dataset properties

For a full list of sections and properties available for defining datasets, see the [datasets](concepts-datasets-linked-services.md) article. This section provides a list of properties supported by ServiceNow dataset.

To copy data from ServiceNow, set the type property of the dataset to **ServiceNowObject**. There is no additional type-specific property in this type of dataset.

**Example**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## Copy activity properties

For a full list of sections and properties available for defining activities, see the [Pipelines](concepts-pipelines-activities.md) article. This section provides a list of properties supported by ServiceNow source.

### ServiceNow as source

To copy data from ServiceNow, set the source type in the copy activity to **ServiceNowSource**. The following properties are supported in the copy activity **source** section:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the copy activity source must be set to: **ServiceNowSource** | Yes |
| query | Use the custom SQL query to read data. For example: `"SELECT * FROM Actual.alm_asset"`. | Yes |

Note the following when specifying the schema and column for ServiceNow in query:

- **Schema:** specify the schema as `Actual` or `Display` in the ServiceNow query, which you can look at it as the parameter of `sysparm_display_value` as true or false when calling [ServiceNow restful APIs](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Column:** the column name for actual value under `Actual` schema is `[columne name]_value`, while for display value under `Display` schema is `[columne name]_display_value`. Note the column name need map to the schema being used in the query.

**Sample query:**
`SELECT col_value FROM Actual.alm_asset`
OR 
`SELECT col_display_value FROM Display.alm_asset`

**Example:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## Next steps
For a list of data stores supported as sources and sinks by the copy activity in Azure Data Factory, see [supported data stores](copy-activity-overview.md#supported-data-stores-and-formats).
