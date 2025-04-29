# Transformações no dbt

## Jinja e Macros

### 1. Sintaxe Básica Jinja
```sql
-- Variáveis
{% set payment_methods = ['credit', 'debit', 'pix'] %}

-- Loops
SELECT 
    order_id,
    {% for method in payment_methods %}
    SUM(CASE WHEN payment_type = '{{ method }}' THEN amount END) as {{ method }}_amount
    {% if not loop.last %},{% endif %}
    {% endfor %}
FROM {{ ref('stg_payments') }}
GROUP BY 1
```

### 2. Macros Personalizadas
```sql
-- macros/generate_schema_name.sql
{% macro generate_schema_name(custom_schema_name, node) %}
    {%- set default_schema = target.schema -%}
    
    {%- if custom_schema_name is none -%}
        {{ default_schema }}
    {%- else -%}
        {{ default_schema }}_{{ custom_schema_name }}
    {%- endif -%}
{% endmacro %}

-- Uso em modelos
{{ config(schema=generate_schema_name('marts')) }}
```

### 3. Macros Condicionais
```sql
{% macro is_weekend(date_column) %}
    CASE
        WHEN EXTRACT(DOW FROM {{ date_column }}) IN (0, 6) THEN TRUE
        ELSE FALSE
    END
{% endmacro %}

-- Uso em modelos
SELECT 
    order_date,
    {{ is_weekend('order_date') }} as is_weekend
FROM {{ ref('stg_orders') }}
```

## CTEs e Subqueries

### 1. CTEs Básicas
```sql
WITH customers AS (
    SELECT * FROM {{ ref('stg_customers') }}
),
orders AS (
    SELECT * FROM {{ ref('stg_orders') }}
),
customer_orders AS (
    SELECT 
        customer_id,
        COUNT(*) as order_count,
        SUM(amount) as total_amount
    FROM orders
    GROUP BY 1
),
final AS (
    SELECT 
        c.*,
        COALESCE(co.order_count, 0) as order_count,
        COALESCE(co.total_amount, 0) as total_amount
    FROM customers c
    LEFT JOIN customer_orders co ON c.customer_id = co.customer_id
)
SELECT * FROM final
```

### 2. CTEs Recursivas
```sql
WITH RECURSIVE hierarchy AS (
    -- Base case
    SELECT 
        id,
        parent_id,
        name,
        1 as level
    FROM {{ ref('employees') }}
    WHERE parent_id IS NULL

    UNION ALL

    -- Recursive case
    SELECT 
        e.id,
        e.parent_id,
        e.name,
        h.level + 1
    FROM {{ ref('employees') }} e
    INNER JOIN hierarchy h ON e.parent_id = h.id
)
SELECT * FROM hierarchy
```

## Funções e Operadores

### 1. Funções de Data
```sql
SELECT 
    order_date,
    DATE_TRUNC('month', order_date) as order_month,
    EXTRACT(YEAR FROM order_date) as order_year,
    DATEDIFF('day', order_date, shipped_date) as days_to_ship
FROM {{ ref('stg_orders') }}
```

### 2. Funções de Agregação
```sql
SELECT 
    customer_id,
    COUNT(DISTINCT order_id) as total_orders,
    SUM(amount) as total_amount,
    AVG(amount) as avg_amount,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY amount) as median_amount
FROM {{ ref('stg_orders') }}
GROUP BY 1
```

### 3. Funções de Janela
```sql
SELECT 
    order_date,
    amount,
    SUM(amount) OVER (
        PARTITION BY customer_id 
        ORDER BY order_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) as cumulative_amount,
    ROW_NUMBER() OVER (
        PARTITION BY customer_id 
        ORDER BY order_date DESC
    ) as order_rank
FROM {{ ref('stg_orders') }}
```

## Packages e Utilidades

### 1. dbt_utils
```yaml
# packages.yml
packages:
  - package: dbt-labs/dbt_utils
    version: 1.1.1
```

```sql
-- Usando funções do dbt_utils
SELECT 
    customer_id,
    {{ dbt_utils.pivot(
        'payment_method',
        dbt_utils.get_column_values(ref('stg_payments'), 'payment_method'),
        agg='sum',
        then_value='amount'
    ) }}
FROM {{ ref('stg_payments') }}
GROUP BY 1
```

### 2. Audit Helper
```sql
-- Comparando resultados
{{ audit_helper.compare_relations(
    a_relation=ref('stg_orders_v1'),
    b_relation=ref('stg_orders_v2'),
    primary_key='order_id'
) }}
```

## Exercícios Práticos

1. **Macros Personalizadas**
   - Criar macro para formatação de moeda
   - Implementar macro para cálculo de métricas
   - Desenvolver macro para geração de schema

2. **CTEs Complexas**
   - Criar modelo com múltiplas CTEs
   - Implementar lógica de negócio complexa
   - Otimizar performance

3. **Funções de Janela**
   - Calcular métricas móveis
   - Implementar rankings e partições
   - Criar análises temporais

4. **Packages**
   - Instalar e configurar dbt_utils
   - Usar funções de pivoting
   - Implementar testes genéricos

## Recursos Adicionais

- [Jinja e Macros](https://docs.getdbt.com/docs/build/jinja-macros)
- [CTEs](https://docs.getdbt.com/docs/build/sql-performance)
- [Funções de Janela](https://docs.getdbt.com/blog/sql-window-functions)
- [dbt_utils](https://github.com/dbt-labs/dbt-utils)
- [Packages](https://hub.getdbt.com) 