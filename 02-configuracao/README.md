# Configuração do dbt

Esta seção aborda a configuração do dbt para diferentes ambientes e casos de uso.

## Tópicos Abordados

1. Instalação
   - Requisitos do sistema
   - Instalação via pip
   - Instalação via Docker
   - Configuração do ambiente virtual

2. Configuração do Projeto
   - Estrutura do dbt_project.yml
   - Configuração de perfis (profiles.yml)
   - Variáveis de ambiente
   - Configurações específicas de banco de dados

3. Conexão com Bancos de Dados
   - Snowflake
   - BigQuery
   - Redshift
   - PostgreSQL
   - Outros adaptadores

4. Configurações Avançadas
   - Customização de schemas
   - Configuração de materializações
   - Gerenciamento de dependências
   - Configuração de hooks
   - Configuração de tags

5. Ambientes
   - Desenvolvimento
   - Homologação
   - Produção
   - CI/CD

## Exercícios Práticos

1. Configurar conexão com PostgreSQL
2. Criar diferentes perfis para dev/prod
3. Configurar materializações customizadas
4. Implementar hooks pre/post execução
5. Configurar CI/CD com GitHub Actions

## Recursos Adicionais

- [Configuração do dbt](https://docs.getdbt.com/reference/project-configs/about-project-configs)
- [Perfis e Conexões](https://docs.getdbt.com/docs/core/connection-profiles)
- [Variáveis de Ambiente](https://docs.getdbt.com/docs/build/environment-variables)
- [CI/CD](https://docs.getdbt.com/docs/deploy/ci-cd) 