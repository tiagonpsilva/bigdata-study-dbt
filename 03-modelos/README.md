# Modelos no dbt

## Tipos de Modelos

### 1. Staging Models
- Primeira camada de transformação
- Padronização e limpeza inicial
- Geralmente materializados como views

```sql
-- models/staging/stg_customers.sql
WITH source AS (
    SELECT * FROM {{ source('raw', 'customers') }}
),
renamed AS (
    SELECT
        customer_id,
        LOWER(first_name) as first_name,
        LOWER(last_name) as last_name,
        email,
        created_at
    FROM source
)
SELECT * FROM renamed
```

### 2. Intermediate Models
- Transformações intermediárias
- Combinação de múltiplas fontes
- Lógica de negócio inicial

```sql
-- models/intermediate/int_orders.sql
WITH orders AS (
    SELECT * FROM {{ ref('stg_orders') }}
),
order_items AS (
    SELECT * FROM {{ ref('stg_order_items') }}
),
final AS (
    SELECT 
        o.order_id,
        o.customer_id,
        o.order_date,
        COUNT(oi.item_id) as total_items,
        SUM(oi.amount) as total_amount
    FROM orders o
    LEFT JOIN order_items oi ON o.order_id = oi.order_id
    GROUP BY 1, 2, 3
)
SELECT * FROM final
```

### 3. Mart Models
- Modelos finais para consumo
- Agregações complexas
- Métricas de negócio

```sql
-- models/marts/finance/customer_revenue.sql
WITH customer_orders AS (
    SELECT * FROM {{ ref('int_orders') }}
),
final AS (
    SELECT 
        customer_id,
        COUNT(DISTINCT order_id) as total_orders,
        SUM(total_amount) as lifetime_revenue,
        AVG(total_amount) as avg_order_value,
        MAX(order_date) as last_order_date
    FROM customer_orders
    GROUP BY 1
)
SELECT * FROM final
```

## Configurações de Modelo

### 1. Materialização
```yaml
# dbt_project.yml
models:
  my_project:
    staging:
      +materialized: view
    marts:
      +materialized: table
      finance:
        +schema: finance_mart
```

### 2. Configuração Individual
```sql
{{ config(
    materialized='incremental',
    unique_key='order_id',
    schema='marts',
    tags=['daily', 'finance']
) }}

SELECT * FROM ...
```

## Referências e Fontes

### 1. Referências (ref)
```sql
-- Referenciando outro modelo
SELECT * FROM {{ ref('stg_customers') }}
```

### 2. Fontes (source)
```yaml
# models/staging/schema.yml
version: 2
sources:
  - name: raw
    database: raw_data
    schema: public
    tables:
      - name: customers
        columns:
          - name: customer_id
            tests:
              - unique
              - not_null
```

```sql
-- Usando a fonte
SELECT * FROM {{ source('raw', 'customers') }}
```

## Documentação de Modelos

```yaml
# models/marts/schema.yml
version: 2

models:
  - name: customer_revenue
    description: "Métricas financeiras agregadas por cliente"
    columns:
      - name: customer_id
        description: "Identificador único do cliente"
        tests:
          - unique
          - not_null
      - name: lifetime_revenue
        description: "Receita total do cliente"
        tests:
          - not_null
          - positive_values
```

## Exercícios Práticos

1. **Staging Models**
   - Criar modelos de staging para customers, orders e products
   - Implementar padronização de nomes e tipos
   - Adicionar testes básicos

2. **Intermediate Models**
   - Criar modelo para análise de pedidos
   - Implementar joins entre tabelas
   - Calcular métricas intermediárias

3. **Mart Models**
   - Desenvolver marts para finanças e vendas
   - Implementar agregações complexas
   - Configurar materializações apropriadas

4. **Documentação**
   - Documentar todos os modelos
   - Adicionar descrições para colunas
   - Implementar testes de qualidade

## Recursos Adicionais

- [Modelos dbt](https://docs.getdbt.com/docs/build/models)
- [Materializações](https://docs.getdbt.com/docs/build/materializations)
- [Referências e Fontes](https://docs.getdbt.com/docs/build/sources)
- [Documentação](https://docs.getdbt.com/docs/collaborate/documentation) 