# dynamodb-local-docker
DynamoDB Local on Docker using Alpine

## Pre-Requisites:

```
$ pip install awscli
$ aws configure
AWS Access Key ID [None]: key
AWS Secret Access Key [None]: secret
Default region name [None]: localhost
Default output format [None]: json
```

## Build The Image and Run the Container:

```
$ docker build -t ddb_local .
$ docker run --name dynamodb_local -p 8000:8000 -idt ddb_local
```

## List the Container:

```
$ docker ps
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                                                                              NAMES
e8e13e28f21c        ddb01                "sh /opt/ddb.sh"         3 seconds ago       Up 2 seconds        8000/tcp                                                                           dynamo_local
```

## List Table

```
$ aws dynamodb list-tables --endpoint-url http://localhost:8000
{
    "TableNames": []
}
```

## Create Table:

```
$ aws dynamodb create-table --table-name MusicCollection --attribute-definitions AttributeName=Artist,AttributeType=S AttributeName=SongTitle,AttributeType=S --key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 --endpoint-url http://172.17.0.8:8000

{
    "TableDescription": {
        "TableArn": "arn:aws:dynamodb:ddblocal:000000000000:table/MusicCollection",
        "AttributeDefinitions": [
            {
                "AttributeName": "Artist",
                "AttributeType": "S"
            },
            {
                "AttributeName": "SongTitle",
                "AttributeType": "S"
            }
        ],
        "ProvisionedThroughput": {
            "NumberOfDecreasesToday": 0,
            "WriteCapacityUnits": 1,
            "LastIncreaseDateTime": 0.0,
            "ReadCapacityUnits": 1,
            "LastDecreaseDateTime": 0.0
        },
        "TableSizeBytes": 0,
        "TableName": "MusicCollection",
        "TableStatus": "ACTIVE",
        "KeySchema": [
            {
                "KeyType": "HASH",
                "AttributeName": "Artist"
            },
            {
                "KeyType": "RANGE",
                "AttributeName": "SongTitle"
            }
        ],
        "ItemCount": 0,
        "CreationDateTime": 1496829882.046
    }
}
```

## List the Table Again:

```
$ aws dynamodb list-tables --endpoint-url http://172.17.0.8:8000
{
    "TableNames": [
        "MusicCollection"
    ]
}
```

## Prepare the PutItem.json:

```
{
    "Item": {
        "Artist": {
            "S": "U2"
        },
        "SongTitle": {
            "S": "Vertigo"
        },
        "year": {
            "S": "2004"
        }
    }
}
```

## Do a PutItem:

```
$ aws dynamodb put-item --table-name MusicCollection --endpoint-url http://172.17.0.8:8000 --item file://PutItem.json
```

## Do a GetItem:

```
$ aws dynamodb get-item --table-name MusicCollection --endpoint-url http://172.17.0.8:8000 --key '{"Artist": {"S": "U2"},"SongTitle": {"S": "Vertigo"}}'
{
    "Item": {
        "Artist": {
            "S": "U2"
        },
        "SongTitle": {
            "S": "Vertigo"
        },
        "year": {
            "S": "2004"
        }
    }
}
```
