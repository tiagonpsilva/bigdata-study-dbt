# 🚀 Estudo dbt (data build tool)

Este repositório contém materiais de estudo sobre o dbt, uma ferramenta de transformação de dados que permite aos analistas e engenheiros de dados transformar dados de forma mais eficiente usando SQL.

## 📚 Conteúdo

1. [Conceitos Básicos](01-conceitos-basicos/README.md)
   - O que é dbt?
   - Arquitetura
   - Fluxo de Trabalho
   - Principais Componentes

2. [Configuração](02-configuracao/README.md)
   - Instalação
   - Configuração do Projeto
   - Conexão com Data Warehouse
   - Estrutura de Diretórios

3. [Modelos](03-modelos/README.md)
   - Estrutura de Modelos
   - Materializations
   - Referências
   - Macros
   - Tests

4. [Transformações](04-transformacoes/README.md)
   - CTEs
   - Joins
   - Agregações
   - Window Functions
   - Incremental Models

5. [Testes e Documentação](05-testes-documentacao/README.md)
   - Testes Genéricos
   - Testes Singulares
   - Documentação
   - Lineage

## 🎯 Exemplos Práticos

O diretório [exemplos](exemplos/README.md) contém projetos dbt completos para diferentes casos de uso:

1. E-commerce
   - Transformação de dados de vendas
   - Análise de comportamento do cliente
   - Métricas de performance

2. Marketing
   - Atribuição de campanhas
   - Análise de funil
   - ROI por canal

3. Financeiro
   - Reconciliação de transações
   - Análise de receita
   - Projeções financeiras

## 🛠️ Pré-requisitos

- Python 3.8+
- pip ou conda
- Acesso a um Data Warehouse (Snowflake, BigQuery, Redshift, etc.)
- Conhecimento básico de SQL
- Git

## 🚀 Como Usar

1. Clone o repositório:
```bash
git clone https://github.com/tiagopsilva/bigdata-study-dbt.git
cd bigdata-study-dbt
```

2. Crie um ambiente virtual:
```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
.\venv\Scripts\activate   # Windows
```

3. Instale o dbt:
```bash
pip install dbt-core
# Instale também o adaptador específico para seu data warehouse
# Ex: pip install dbt-snowflake
```

4. Configure sua conexão:
   - Copie o arquivo `profiles.yml.example`
   - Renomeie para `profiles.yml`
   - Atualize com suas credenciais

5. Execute os exemplos:
```bash
cd exemplos/ecommerce
dbt deps
dbt run
```

## 📝 Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para:
- Reportar bugs
- Sugerir melhorias
- Enviar pull requests

## 📚 Recursos Adicionais

- [Documentação Oficial do dbt](https://docs.getdbt.com/)
- [Blog do dbt](https://blog.getdbt.com/)
- [Discourse do dbt](https://discourse.getdbt.com/)
- [dbt Slack Community](https://community.getdbt.com/)

## 📜 Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes. 