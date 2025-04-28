# Testes e Documentação no dbt

Esta seção aborda as práticas de teste e documentação no dbt para garantir a qualidade e manutenibilidade dos modelos.

## Tópicos Abordados

1. Testes Genéricos
   - unique
   - not_null
   - accepted_values
   - relationships
   - Configuração e customização

2. Testes Singulares
   - Testes SQL customizados
   - Validação de regras de negócio
   - Testes de integridade
   - Testes de reconciliação

3. Documentação
   - Markdown
   - YAML
   - Descrições de colunas
   - Tags e meta dados
   - Lineage (DAG)

4. Qualidade de Dados
   - Freshness
   - Volume
   - Distribuição
   - Outliers
   - Duplicatas

5. Melhores Práticas
   - Cobertura de testes
   - Documentação padronizada
   - Automação
   - Monitoramento
   - Alertas

## Exemplos Práticos

### Testes Genéricos
```yaml
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
          - not_null
          - unique
          - accepted_values:
              values: ['@gmail.com', '@hotmail.com', '@yahoo.com']
              quote: false
              operator: 'like'
      - name: status
        tests:
          - accepted_values:
              values: ['active', 'inactive', 'pending']
```

### Teste Singular
```sql
-- tests/assert_total_amount_is_positive.sql
SELECT 
    order_id,
    total_amount
FROM {{ ref('fct_orders') }}
WHERE total_amount < 0
```

### Documentação
```yaml
version: 2

models:
  - name: dim_customers
    description: >
      Dimensão de clientes contendo informações demográficas e de contato.
      Atualizada diariamente às 00:00 UTC.
    columns:
      - name: customer_id
        description: Identificador único do cliente
        tests:
          - unique
          - not_null
      - name: full_name
        description: Nome completo do cliente
      - name: email
        description: Email principal do cliente
      - name: created_at
        description: Data de criação do registro
    meta:
      owner: "Time de Analytics"
      sla: "2 horas"
      update_schedule: "0 0 * * *"
```

## Exercícios Práticos

1. **Testes Genéricos**
   - Configurar testes básicos
   - Implementar testes de relacionamento
   - Validar valores aceitos

2. **Testes Singulares**
   - Criar teste de balanceamento
   - Validar regras de negócio
   - Testar casos específicos

3. **Documentação**
   - Documentar modelos
   - Adicionar descrições de colunas
   - Incluir meta dados

4. **Qualidade de Dados**
   - Monitorar freshness
   - Validar volumes
   - Detectar anomalias

5. **Automação**
   - Configurar CI/CD
   - Implementar alertas
   - Gerar relatórios de qualidade

## Recursos Adicionais

- [Testes no dbt](https://docs.getdbt.com/docs/build/tests)
- [Documentação](https://docs.getdbt.com/docs/collaborate/documentation)
- [Qualidade de Dados](https://docs.getdbt.com/blog/data-quality-in-dbt)
- [Melhores Práticas](https://docs.getdbt.com/best-practices) 