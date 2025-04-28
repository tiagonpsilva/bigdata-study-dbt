# Transformações no dbt

Esta seção aborda as diferentes técnicas de transformação de dados disponíveis no dbt.

## Tópicos Abordados

1. CTEs (Common Table Expressions)
   - Estrutura básica
   - CTEs recursivas
   - Organização do código
   - Performance

2. Joins
   - Inner Join
   - Left/Right Join
   - Full Outer Join
   - Cross Join
   - Boas práticas

3. Agregações
   - GROUP BY
   - Window Functions
   - Funções de agregação
   - Pivoting
   - Unpivoting

4. Window Functions
   - ROW_NUMBER()
   - RANK() e DENSE_RANK()
   - LAG() e LEAD()
   - FIRST_VALUE e LAST_VALUE
   - Particionamento

5. Modelos Incrementais
   - Estratégias de incremento
   - Unique keys
   - Delete+Insert
   - Merge
   - Performance

## Exemplos Práticos

### CTE Básica
```sql
WITH customers AS (
    SELECT * FROM {{ ref('stg_customers') }}
),

orders AS (
    SELECT 
        customer_id,
        COUNT(*) as order_count,
        SUM(amount) as total_amount
    FROM {{ ref('stg_orders') }}
    GROUP BY 1
)

SELECT 
    c.*,
    COALESCE(o.order_count, 0) as order_count,
    COALESCE(o.total_amount, 0) as total_amount
FROM customers c
LEFT JOIN orders o USING (customer_id)
```

### Window Function
```sql
SELECT 
    order_date,
    amount,
    SUM(amount) OVER (
        PARTITION BY DATE_TRUNC('month', order_date)
        ORDER BY order_date
    ) as running_total
FROM {{ ref('stg_orders') }}
```

### Modelo Incremental
```sql
{{ config(
    materialized='incremental',
    unique_key='order_id'
) }}

SELECT 
    *,
    CURRENT_TIMESTAMP as _etl_loaded_at
FROM {{ ref('stg_orders') }}

{% if is_incremental() %}
WHERE order_date > (
    SELECT MAX(order_date) 
    FROM {{ this }}
)
{% endif %}
```

## Exercícios Práticos

1. **Análise de Vendas**
   - Criar CTE para vendas diárias
   - Calcular métricas MoM e YoY
   - Implementar rolling averages

2. **Análise de Cohort**
   - Identificar primeira compra
   - Calcular retenção por cohort
   - Visualizar matriz de cohort

3. **Análise RFM**
   - Calcular Recency
   - Calcular Frequency
   - Calcular Monetary Value
   - Segmentar clientes

4. **Modelo Incremental**
   - Implementar fato de vendas
   - Configurar estratégia incremental
   - Otimizar performance

5. **Análise de Funil**
   - Definir etapas do funil
   - Calcular conversões
   - Identificar gargalos

## Recursos Adicionais

- [Jinja e SQL](https://docs.getdbt.com/reference/dbt-jinja-functions)
- [Modelos Incrementais](https://docs.getdbt.com/docs/build/incremental-models)
- [Window Functions](https://docs.getdbt.com/blog/sql-window-functions)
- [Performance](https://docs.getdbt.com/docs/build/performance) 