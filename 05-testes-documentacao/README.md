# Testes e Documentação no dbt

## Testes

### 1. Testes Genéricos
```yaml
# models/schema.yml
version: 2

models:
  - name: dim_customers
    columns:
      - name: customer_id
        tests:
          - unique
          - not_null
      - name: email
        tests:
          - unique
          - not_null
          - accepted_values:
              values: ['@gmail.com', '@hotmail.com', '@yahoo.com']
              quote: false
              substring: true
      - name: status
        tests:
          - accepted_values:
              values: ['active', 'inactive', 'pending']
```

### 2. Testes Singulares
```sql
-- tests/assert_total_payment_amount_is_positive.sql
SELECT 
    order_id,
    SUM(amount) as total_amount
FROM {{ ref('fct_payments') }}
GROUP BY 1
HAVING total_amount <= 0
```

### 3. Testes com dbt_utils
```yaml
version: 2

models:
  - name: fct_orders
    tests:
      - dbt_utils.equal_rowcount:
          compare_model: ref('stg_orders')
    columns:
      - name: order_date
        tests:
          - dbt_utils.date_spine:
              datepart: day
              start_date: '2020-01-01'
              end_date: '2023-12-31'
```

### 4. Testes de Relacionamento
```yaml
version: 2

models:
  - name: fct_orders
    columns:
      - name: customer_id
        tests:
          - relationships:
              to: ref('dim_customers')
              field: customer_id
      - name: product_id
        tests:
          - relationships:
              to: ref('dim_products')
              field: product_id
```

## Documentação

### 1. Documentação de Modelos
```yaml
version: 2

models:
  - name: dim_customers
    description: >
      Dimensão de clientes contendo informações demográficas e de contato.
      Atualizada diariamente através do processo ETL.
    columns:
      - name: customer_id
        description: Identificador único do cliente
        tests:
          - unique
          - not_null
      
      - name: full_name
        description: Nome completo do cliente
      
      - name: email
        description: Endereço de email principal do cliente
        tests:
          - not_null
          - unique
      
      - name: created_at
        description: Data de criação do registro do cliente
        tests:
          - not_null
```

### 2. Documentação com Markdown
```markdown
{% docs dim_customers %}

# Dimensão de Clientes

Esta tabela contém informações detalhadas sobre os clientes.

## Granularidade
Um registro por cliente

## Frequência de Atualização
Diária (00:00 UTC)

## Pipeline
1. Extração do sistema fonte
2. Transformação e limpeza
3. Carregamento na dimensão

## Campos Principais
* customer_id: Identificador único
* full_name: Nome completo
* email: Email principal
* created_at: Data de criação

## Dependências
* stg_customers
* stg_addresses

{% enddocs %}
```

### 3. Documentação de Macros
```yaml
version: 2

macros:
  - name: generate_schema_name
    description: >
      Macro para gerar nomes de schema dinamicamente baseado
      no ambiente (dev/prod) e nome personalizado.
    arguments:
      - name: custom_schema_name
        description: Nome personalizado do schema
        type: string
      - name: node
        description: Objeto do nó dbt
        type: object
```

### 4. Documentação de Fontes
```yaml
version: 2

sources:
  - name: raw_data
    description: Dados brutos do sistema transacional
    database: raw
    schema: public
    tables:
      - name: customers
        description: Tabela de clientes do sistema fonte
        columns:
          - name: id
            description: Identificador único do cliente
          - name: name
            description: Nome do cliente
        loaded_at_field: _etl_loaded_at
        freshness:
          warn_after: {count: 12, period: hour}
          error_after: {count: 24, period: hour}
```

## Exercícios Práticos

1. **Testes Genéricos**
   - Implementar testes de unicidade
   - Criar testes de valores aceitos
   - Validar relacionamentos

2. **Testes Singulares**
   - Desenvolver teste de integridade
   - Validar regras de negócio
   - Criar testes de reconciliação

3. **Documentação**
   - Documentar modelos principais
   - Criar documentação markdown
   - Documentar macros e fontes

4. **Automação**
   - Configurar CI/CD
   - Implementar testes automatizados
   - Gerar documentação automática

## Recursos Adicionais

- [Testes no dbt](https://docs.getdbt.com/docs/build/tests)
- [Documentação](https://docs.getdbt.com/docs/collaborate/documentation)
- [dbt_utils Tests](https://github.com/dbt-labs/dbt-utils#tests)
- [Melhores Práticas](https://docs.getdbt.com/best-practices/testing-and-documentation) 