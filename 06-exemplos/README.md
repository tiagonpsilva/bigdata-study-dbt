# Exemplos Práticos dbt

Este diretório contém exemplos práticos de projetos dbt para diferentes casos de uso.

## 1. E-commerce Analytics

### Estrutura
```
ecommerce/
├── models/
│   ├── staging/
│   │   ├── stg_orders.sql
│   │   ├── stg_customers.sql
│   │   ├── stg_products.sql
│   │   └── schema.yml
│   ├── marts/
│   │   ├── dim_customers.sql
│   │   ├── dim_products.sql
│   │   ├── fct_orders.sql
│   │   └── schema.yml
│   └── reporting/
│       ├── daily_sales.sql
│       ├── customer_metrics.sql
│       └── schema.yml
├── macros/
│   ├── generate_daily_metrics.sql
│   └── clean_amount.sql
└── dbt_project.yml
```

### Exemplos de Modelos

**1. Staging - stg_orders.sql**
```sql
WITH source AS (
    SELECT * FROM {{ source('raw', 'orders') }}
),

cleaned AS (
    SELECT
        order_id,
        customer_id,
        order_date,
        status,
        {{ clean_amount('total_amount') }} as total_amount
    FROM source
    WHERE order_date >= '2023-01-01'
)

SELECT * FROM cleaned
```

**2. Dimensão - dim_customers.sql**
```sql
WITH customers AS (
    SELECT * FROM {{ ref('stg_customers') }}
),

addresses AS (
    SELECT * FROM {{ ref('stg_addresses') }}
),

final AS (
    SELECT
        c.customer_id,
        c.first_name,
        c.last_name,
        c.email,
        a.city,
        a.state,
        a.country,
        c.created_at,
        c.updated_at
    FROM customers c
    LEFT JOIN addresses a 
        ON c.address_id = a.address_id
)

SELECT * FROM final
```

**3. Fato - fct_orders.sql**
```sql
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
        o.status,
        COUNT(DISTINCT oi.product_id) as total_products,
        SUM(oi.quantity) as total_quantity,
        SUM(oi.amount) as total_amount
    FROM orders o
    LEFT JOIN order_items oi 
        ON o.order_id = oi.order_id
    GROUP BY 1, 2, 3, 4
)

SELECT * FROM final
```

**4. Relatório - daily_sales.sql**
```sql
{{ 
    generate_daily_metrics(
        ref('fct_orders'),
        'order_date',
        ['total_amount', 'total_quantity']
    )
}}
```

## 2. Marketing Analytics

### Estrutura
```
marketing/
├── models/
│   ├── staging/
│   │   ├── stg_campaigns.sql
│   │   ├── stg_ad_spend.sql
│   │   └── schema.yml
│   └── marts/
│       ├── campaign_performance.sql
│       ├── channel_attribution.sql
│       └── schema.yml
└── dbt_project.yml
```

### Exemplos de Modelos

**1. Campaign Performance**
```sql
WITH campaigns AS (
    SELECT * FROM {{ ref('stg_campaigns') }}
),

ad_spend AS (
    SELECT * FROM {{ ref('stg_ad_spend') }}
),

final AS (
    SELECT
        c.campaign_id,
        c.campaign_name,
        c.channel,
        c.start_date,
        c.end_date,
        SUM(a.spend) as total_spend,
        SUM(a.impressions) as total_impressions,
        SUM(a.clicks) as total_clicks,
        SUM(a.conversions) as total_conversions,
        ROUND(SUM(a.spend) / NULLIF(SUM(a.conversions), 0), 2) as cost_per_conversion
    FROM campaigns c
    LEFT JOIN ad_spend a 
        ON c.campaign_id = a.campaign_id
    GROUP BY 1, 2, 3, 4, 5
)

SELECT * FROM final
```

## 3. Análise Financeira

### Estrutura
```
finance/
├── models/
│   ├── staging/
│   │   ├── stg_transactions.sql
│   │   ├── stg_accounts.sql
│   │   └── schema.yml
│   └── marts/
│       ├── daily_balances.sql
│       ├── monthly_statements.sql
│       └── schema.yml
└── dbt_project.yml
```

### Exemplos de Modelos

**1. Daily Balances**
```sql
WITH transactions AS (
    SELECT * FROM {{ ref('stg_transactions') }}
),

daily_movements AS (
    SELECT
        account_id,
        transaction_date,
        SUM(CASE 
            WHEN type = 'credit' THEN amount
            ELSE -amount
        END) as daily_change
    FROM transactions
    GROUP BY 1, 2
),

final AS (
    SELECT
        account_id,
        transaction_date,
        daily_change,
        SUM(daily_change) OVER (
            PARTITION BY account_id
            ORDER BY transaction_date
            ROWS UNBOUNDED PRECEDING
        ) as running_balance
    FROM daily_movements
)

SELECT * FROM final
```

## Exercícios Práticos

1. **E-commerce**
   - Implementar cálculo de LTV
   - Criar análise de cohort
   - Desenvolver dashboard de vendas

2. **Marketing**
   - Calcular ROAS por canal
   - Implementar atribuição multi-touch
   - Analisar funil de conversão

3. **Financeiro**
   - Calcular métricas contábeis
   - Implementar DRE automatizado
   - Criar relatórios gerenciais

## Recursos Adicionais

- [dbt Project Structure](https://docs.getdbt.com/guides/best-practices/how-we-structure/1-guide-overview)
- [Modelos de Exemplo](https://github.com/dbt-labs/jaffle_shop)
- [Padrões de Modelagem](https://docs.getdbt.com/guides/best-practices/how-we-structure/2-staging)
- [Métricas e KPIs](https://docs.getdbt.com/docs/build/metrics) 