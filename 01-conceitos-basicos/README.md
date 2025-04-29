# Conceitos Básicos do dbt

## O que é dbt?

dbt (data build tool) é uma ferramenta de transformação que permite aos analistas e engenheiros de dados:

- Escrever transformações de dados usando SQL puro
- Versionar e gerenciar código SQL como código de aplicação
- Criar e gerenciar dependências entre transformações
- Testar e documentar transformações
- Criar pipelines de dados modulares e reutilizáveis

## Arquitetura

```plaintext
                   +----------------+
                   |                |
                   |  Data Sources  |
                   |                |
                   +-------+--------+
                           |
                           v
                   +-------+--------+
                   |                |
                   |   dbt Core     |
                   |                |
                   +-------+--------+
                           |
                           v
               +----------+-----------+
               |                      |
               |   Data Warehouse    |
               |                      |
               +----------------------+
```

### Componentes Principais:

1. **dbt Core**:
   - Compilador SQL
   - Gerenciador de dependências
   - Sistema de templates (Jinja)
   - CLI para execução de comandos

2. **Data Warehouse**:
   - Onde as transformações são executadas
   - Suporte para diversos bancos (Snowflake, BigQuery, Redshift)
   - Armazena tabelas e views resultantes

## Fluxo de Trabalho

1. **Desenvolvimento**:
   ```sql
   -- models/staging/stg_orders.sql
   SELECT
     order_id,
     customer_id,
     order_date,
     status
   FROM raw.orders
   ```

2. **Compilação**:
   - dbt compila o SQL com templates Jinja
   - Resolve referências e macros
   - Gera SQL executável

3. **Execução**:
   ```bash
   dbt run
   ```
   - SQL é executado no data warehouse
   - Tabelas/views são criadas/atualizadas

## Principais Conceitos

### 1. Modelos

- Arquivos SQL que definem transformações
- Podem referenciar outros modelos
- Suportam diferentes materializações:
  ```yaml
  # dbt_project.yml
  models:
    my_project:
      staging:
        materialized: view
      marts:
        materialized: table
  ```

### 2. Sources

- Definem tabelas de origem:
  ```yaml
  # models/staging/sources.yml
  sources:
    - name: raw
      database: raw_data
      tables:
        - name: orders
        - name: customers
  ```

### 3. Refs

- Referenciam outros modelos:
  ```sql
  SELECT * FROM {{ ref('stg_orders') }}
  ```

### 4. Macros

- Funções reutilizáveis em SQL:
  ```sql
  {% macro clean_string(column_name) %}
    TRIM(LOWER({{ column_name }}))
  {% endmacro %}
  ```

## Exercícios Práticos

1. **Configuração Básica**
   - Instale o dbt
   - Configure conexão com data warehouse
   - Inicialize um projeto

2. **Primeiro Modelo**
   - Crie um modelo staging
   - Use diferentes materializações
   - Execute o modelo

3. **Sources e Refs**
   - Configure sources
   - Crie modelos dependentes
   - Use refs para referências

## Recursos Adicionais

- [Introdução ao dbt](https://docs.getdbt.com/docs/introduction)
- [Melhores Práticas](https://docs.getdbt.com/guides/best-practices)
- [Tutoriais](https://docs.getdbt.com/guides/getting-started) 