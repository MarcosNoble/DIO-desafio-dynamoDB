# dio-live-dynamodb
Repositório para o live coding do dia 30/09/2021 sobre o Amazon DynamoDB

### Serviço utilizado
  - Amazon DynamoDB
  - Amazon CLI para execução em linha de comando

### Comandos para execução do experimento:


- Criar uma tabela

```
aws dynamodb create-table \
    --table-name Cachorros \
    --attribute-definitions \
        AttributeName=Pedigree,AttributeType=S \
        AttributeName=Habilidade,AttributeType=S \
    --key-schema \
        AttributeName=Pedigree,KeyType=HASH \
        AttributeName=Habilidade,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
```

- Inserir um item

```
aws dynamodb put-item \
    --table-name Cachorros \
    --item file://itemcachorro.json \
```

- Inserir múltiplos itens

```
aws dynamodb batch-write-item \
    --request-items file://batchcachorro.json
```

- Criar um index global secundário baeado no pedigree

```
aws dynamodb update-table \
    --table-name Cachorros \
    --attribute-definitions AttributeName=Peso,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"Peso-index\",\"KeySchema\":[{\"AttributeName\":\"Peso\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Criar um index global secundário baseado no Pedigre e Peso

```
aws dynamodb update-table \
    --table-name Cachorros \
    --attribute-definitions\
        AttributeName=Pedigree,AttributeType=S \
        AttributeName=Peso,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"PedigreePeso-index\",\"KeySchema\":[{\"Pedigree\":\"Artist\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"Peso\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Pesquisar item por Pedigree

```
aws dynamodb query \
    --table-name Cachorros \
    --key-condition-expression "Pedigree = :pedigree" \
    --expression-attribute-values  '{":pedigree":{"S":"Doberman"}}'
```
- Pesquisar item por  Pedigre e Peso

```
aws dynamodb query \
    --table-name Cachorros \
    --key-condition-expression "Pedigre = :pedigre and Peso = :peso" \
    --expression-attribute-values file://keyconditions.json
```
