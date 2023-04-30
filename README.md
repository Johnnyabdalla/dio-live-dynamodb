# dio-live-dynamodb
Repositório para o Desafio DynamoDb do Bootcamp Banco Pan

### Serviço utilizado
  - Amazon DynamoDB
  - Amazon CLI para execução em linha de comando

### Comandos para execução do experimento:


- Criar uma tabela

```
aws dynamodb create-table \
    --table-name Shop \
    --attribute-definitions \
        AttributeName=Product,AttributeType=S \
        AttributeName=Category,AttributeType=S \
        AttributeName=Price,AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \
        AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
```

- Inserir um itemShop

```
aws dynamodb put-itemShop \
    --table-name Shop \
    --itemShop file://itemshop.json \
```

- Inserir múltiplos itens

```
aws dynamodb batch-write-itemShop \
    --request-items file://batchshop.json
```

- Criar um index global secundário baeado no Product e Category

```
aws dynamodb update-table \
    --table-name Shop \
    --attribute-definitions AttributeName=Product,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"Product-index\",\"KeySchema\":[{\"AttributeName\":\"Product\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Criar um index global secundário baseado no nome do Product e no Price

```
aws dynamodb update-table \
    --table-name Shop \
    --attribute-definitions\
        AttributeName=Product,AttributeType=S \
        AttributeName=Price,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"ProductPrice-index\",\"KeySchema\":[{\"AttributeName\":\"Product\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"Price\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Criar um index global secundário baseado no Category e Price

```
aws dynamodb update-table \
    --table-name Shop \
    --attribute-definitions\
        AttributeName=Category,AttributeType=S \
        AttributeName=Price,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"CategoryPrice-index\",\"KeySchema\":[{\"AttributeName\":\"Category\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"Price\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Pesquisar itemShop por Product

```
aws dynamodb query \
    --table-name Shop \
    --key-condition-expression "Product= :product" \
    --expression-attribute-values  '{":product":{"S":"Kinder"}}'
```
- Pesquisar itemShop por category e product

```
aws dynamodb query \
    --table-name Shop \
    --key-condition-expression "Product = :product and category = :candy" \
    --expression-attribute-values file://keyshopconditions.json
```

- Pesquisa pelo index secundário baseado na category

```
aws dynamodb query \
    --table-name Shop \
    --index-name category-index \
    --key-condition-expression "Category = :category" \
    --expression-attribute-values  '{":category":{"S":"candy"}}'
```
