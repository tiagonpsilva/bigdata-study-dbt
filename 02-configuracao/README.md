# Configuração do dbt

## Estrutura do Projeto

Um projeto dbt típico tem a seguinte estrutura:

```plaintext
my_dbt_project/
├── dbt_project.yml          # Arquivo principal de configuração
├── profiles.yml             # Configurações de conexão
├── packages.yml             # Dependências de pacotes
├── models/                  # Modelos SQL
│   ├── staging/            # Modelos de staging
│   ├── intermediate/       # Modelos intermediários
│   └── marts/             # Modelos finais
├── macros/                 # Macros Jinja personalizadas
├── tests/                  # Testes personalizados
├── snapshots/             # Configurações de snapshot
└── seeds/                 # Arquivos CSV para seed
```

## Arquivo dbt_project.yml

O arquivo principal de configuração do projeto:

```yaml
name: 'my_project'
version: '1.0.0'
config-version: 2

profile: 'my_warehouse'

model-paths: ["models"]
seed-paths: ["seeds"]
test-paths: ["tests"]
macro-paths: ["macros"]
snapshot-paths: ["snapshots"]

target-path: "target"
clean-targets:
  - "target"
  - "dbt_packages"
  - "logs"

models:
  my_project:
    staging:
      +materialized: view
    intermediate:
      +materialized: table
    marts:
      +materialized: table
      +schema: marts
```

## Profiles.yml

Configuração de conexão com o data warehouse:

```yaml
my_warehouse:
  target: dev
  outputs:
    dev:
      type: snowflake
      account: xy12345
      user: "{{ env_var('DBT_USER') }}"
      password: "{{ env_var('DBT_PASSWORD') }}"
      role: transformer
      database: analytics
      warehouse: transforming
      schema: dbt_dev
      threads: 4
```

## Packages.yml

Instalação de pacotes de terceiros:

```yaml
packages:
  - package: dbt-labs/dbt_utils
    version: 0.8.0
  - package: calogica/dbt_expectations
    version: 0.5.0
```

## Configuração de Ambiente

1. **Instalação do dbt**:
   ```bash
   # Usando pip
   pip install dbt-core
   pip install dbt-snowflake  # ou outro adaptador
   
   # Ou usando conda
   conda install dbt-core
   conda install dbt-snowflake
   ```

2. **Variáveis de Ambiente**:
   ```bash
   export DBT_USER=seu_usuario
   export DBT_PASSWORD=sua_senha
   ```

3. **Inicialização do Projeto**:
   ```bash
   dbt init my_project
   cd my_project
   ```

## Configuração de Testes

Em `models/schema.yml`:

```yaml
version: 2

models:
  - name: stg_orders
    description: "Staging table for orders"
    columns:
      - name: order_id
        description: "Primary key"
        tests:
          - unique
          - not_null
      - name: status
        description: "Order status"
        tests:
          - accepted_values:
              values: ['pending', 'completed', 'cancelled']
```

## Configuração de Documentação

Em `models/schema.yml`:

```yaml
version: 2

models:
  - name: stg_orders
    description: "Staging table for orders"
    meta:
      owner: "Data Team"
      updated_at: "2024-03-15"
    columns:
      - name: order_id
        description: "Unique identifier for each order"
        tests:
          - unique
          - not_null
```

## Exercícios Práticos

1. **Configuração Inicial**
   - Configure um novo projeto dbt
   - Configure conexão com Snowflake/BigQuery
   - Instale pacotes úteis

2. **Estrutura do Projeto**
   - Organize modelos em staging/intermediate/marts
   - Configure materializações por pasta
   - Configure schemas por pasta

3. **Testes e Documentação**
   - Adicione testes genéricos
   - Crie testes personalizados
   - Configure documentação detalhada

## Recursos Adicionais

- [Configuração do dbt](https://docs.getdbt.com/reference/project-configs/about-your-project)
- [Perfis e Conexões](https://docs.getdbt.com/docs/core/connection-profiles)
- [Pacotes dbt](https://hub.getdbt.com/) 