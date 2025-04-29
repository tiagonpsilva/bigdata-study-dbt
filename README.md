# 🧠 Estudo do dbt (data build tool)

Este repositório contém explicações detalhadas e exemplos práticos dos conceitos fundamentais do dbt (data build tool), focando em transformação e modelagem de dados usando SQL com práticas de engenharia de software.

## 📋 Índice de Conceitos

1. **🌱 [Conceitos Básicos](./01-conceitos-basicos/README.md)** - Fundamentos, arquitetura e componentes do dbt
2. **⚙️ [Configuração](./02-configuracao/README.md)** - Setup do ambiente, profiles e configurações do projeto
3. **📊 [Modelos](./03-modelos/README.md)** - Criação e organização de modelos SQL
4. **🔄 [Transformações](./04-transformacoes/README.md)** - CTEs, materializações e referências entre modelos
5. **✅ [Testes e Documentação](./05-testes-documentacao/README.md)** - Testes de dados, documentação e lineage
6. **💡 [Exemplos](./06-exemplos/README.md)** - Cases práticos e integrações

## 🌟 Objetivo

Este repositório tem como objetivo proporcionar um entendimento prático do dbt, com explicações claras e exemplos de casos de uso reais. Cada conceito é explorado em detalhes, com código funcional e boas práticas de modelagem de dados.

## ⚙️ Pré-requisitos

- Python 3.7+
- PostgreSQL ou outro banco de dados compatível
- Conhecimento básico de SQL
- Familiaridade com conceitos de Data Warehouse

## 🚀 Como Usar

1. Clone o repositório
```bash
git clone https://github.com/tiagonpsilva/bigdata-study-dbt.git
```

2. Crie um ambiente virtual Python
```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
# ou
.\venv\Scripts\activate  # Windows
```

3. Instale o dbt
```bash
pip install dbt-postgres  # ou outro adaptador conforme seu banco
```

4. Configure seu profile
```yaml
# ~/.dbt/profiles.yml
bigdata_study:
  target: dev
  outputs:
    dev:
      type: postgres
      host: localhost
      user: seu_usuario
      password: sua_senha
      port: 5432
      dbname: dbt_study
      schema: dbt_study
      threads: 4
```

## 🐳 Ambiente de Desenvolvimento

### Docker Compose

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:13
    environment:
      POSTGRES_USER: dbt_user
      POSTGRES_PASSWORD: dbt_password
      POSTGRES_DB: dbt_study
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  adminer:
    image: adminer
    ports:
      - "8080:8080"
    depends_on:
      - postgres

volumes:
  postgres_data:
```

## 🔍 Estrutura do Projeto

```
bigdata-study-dbt/
├── 01-conceitos-basicos/    # Fundamentos e arquitetura
├── 02-configuracao/         # Setup e configurações
├── 03-modelos/             # Criação de modelos
├── 04-transformacoes/      # Transformações de dados
├── 05-testes-documentacao/ # Testes e docs
└── 06-exemplos/           # Cases práticos
```

Cada diretório contém:
- README com explicações detalhadas
- Exemplos práticos de código
- Exercícios para prática
- Links para documentação adicional

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