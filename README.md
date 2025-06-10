# Teste Automatizado da API Serverest (Postman + Newman)

Este projeto contém uma suíte de testes automatizados para a API Serverest, utilizando Postman e Newman. O objetivo é garantir a qualidade dos principais fluxos da API através de testes automatizados dos endpoints de usuários, produtos e carrinhos.

## Sumário
- [Pré-requisitos](#pré-requisitos)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [Configuração do Ambiente](#configuração-do-ambiente)
- [Execução dos Testes](#execução-dos-testes)
- [Fluxos de Teste](#fluxos-de-teste)
- [Relatórios e Monitoramento](#relatórios-e-monitoramento)
- [CI/CD](#cicd)
- [Manutenção e Evolução](#manutenção-e-evolução)
- [Troubleshooting](#troubleshooting)

## Pré-requisitos

### Instalação do Node.js e NPM
1. Instale o Node.js (versão 14 ou superior):
   ```bash
   # Ubuntu/Debian
   curl -fsSL https://deb.nodesource.com/setup_14.x | sudo -E bash -
   sudo apt-get install -y nodejs

   # Verificar instalação
   node --version
   npm --version
   ```

### Instalação do Newman e Reporter
```bash
# Instalar Newman globalmente
npm install -g newman

# Instalar Newman Reporter HTML Extra
npm install -g newman-reporter-htmlextra
```

### Postman (opcional, para desenvolvimento/debug)
1. Download: [Postman](https://www.postman.com/downloads/)
2. Instalação:
   ```bash
   # Ubuntu/Debian
   sudo tar -xzf Postman-linux-x64-*.tar.gz -C /opt
   sudo ln -s /opt/Postman/Postman /usr/local/bin/postman
   ```

## Estrutura do Projeto

```
serverest-api-test/
├── serverest_collection.json     # Coleção Postman com todos os endpoints e scripts
├── serverest_dev_environment_global.json  # Variáveis de ambiente
├── newman/                      # Relatórios de execução
│   └── *.html                   # Relatórios HTML gerados
├── .github/
│   └── workflows/
│       └── api-tests.yml        # Pipeline de CI/CD
└── README.md
```

### Detalhamento dos Arquivos

1. **serverest_collection.json**:
   - Collection Postman com todos os endpoints
   - Scripts de pré-requisição para geração de dados
   - Scripts de testes para validação de respostas
   - Organização por funcionalidade (usuários, produtos, carrinhos)

2. **serverest_dev_environment_global.json**:
   - Variáveis de ambiente para diferentes contextos (dev, prod)
   - Configurações de URLs base
   - Tokens e credenciais dinâmicas
   - IDs e dados temporários dos testes

3. **newman/**:
   - Relatórios detalhados de cada execução
   - Métricas de sucesso/falha
   - Tempo de execução
   - Screenshots de erros (quando aplicável)

## Configuração do Ambiente

### Variáveis de Ambiente
As seguintes variáveis são utilizadas durante a execução dos testes:
- `baseURL`: URL base da API (ex: https://serverest.dev)
- `accessToken`: Token de autenticação gerado após login
- `nome`, `email`, `password`: Credenciais do usuário de teste
- `userId`: ID do usuário criado durante os testes
- `randomProduct`: Nome do produto gerado automaticamente
- `newProductId`: ID do produto criado durante os testes

### Personalização de Ambientes
Para criar um novo ambiente:
1. Duplique o arquivo `serverest_dev_environment_global.json`
2. Renomeie conforme o ambiente (ex: `serverest_prod_environment.json`)
3. Atualize os valores das variáveis conforme necessário

## Execução dos Testes

### Execução Completa
```bash
newman run serverest_collection.json --environment serverest_dev_environment_global.json --reporters cli,htmlextra
```

### Execução dos Testes de Workflow
O projeto está configurado para executar os testes de workflow que validam a integração entre diferentes funcionalidades da API. Para executar:

```bash
newman run serverest_collection.json --environment serverest_dev_environment_global.json --folder "workflow-tests" --reporters cli,htmlextra
```

Este comando executa a suíte de testes workflow que inclui:
1. Cadastro de usuário (com dados dinâmicos)
2. Autenticação do usuário
3. Criação de produto (com dados dinâmicos)
4. Validação do produto criado
5. Limpeza dos dados de teste

Todos os testes incluem validações de:
- Status code apropriado
- Tempo de resposta (< 300ms)
- Consistência dos dados
- Sucesso das operações
```

## Fluxos de Teste

### 1. Cadastro e Autenticação de Usuário
- **Pré-requisito:**
  - Gera nome e email aleatórios para evitar duplicidade
  - Salva informações nas variáveis de ambiente
- **Teste:**
  - Cadastro do usuário
  - Login e geração de token
  - Validações de resposta e dados

### 2. Gerenciamento de Produtos
- **Pré-requisito:**
  - Token de autenticação válido
  - Dados aleatórios do produto
- **Teste:**
  - Cadastro de produto
  - Busca por ID
  - Atualização
  - Exclusão

### 3. Operações de Carrinho
- **Pré-requisito:**
  - Usuário autenticado
  - Produto(s) cadastrado(s)
- **Teste:**
  - Criação de carrinho
  - Adição/remoção de produtos
  - Finalização de compra

## Relatórios e Monitoramento

### Relatórios HTML
- Localização: pasta `newman/`
- Conteúdo:
  - Sumário da execução
  - Detalhes de cada teste
  - Logs de erro
  - Métricas de tempo

## CI/CD com GitHub Actions

O projeto utiliza GitHub Actions para automação dos testes. O pipeline está configurado para executar apenas os testes de workflow (`workflow-tests`), que são os mais críticos e validam o fluxo completo da aplicação.

### Gatilhos de Execução
- Push para branches `main` ou `master`
- Pull Requests para `main` ou `master`
- Agendamento diário (meia-noite UTC)

### Relatórios e Artefatos
- **Geração Automática**: Relatórios HTML gerados a cada execução
- **Armazenamento**: Artefatos compactados e armazenados no GitHub
- **Retenção**: Disponíveis para download por 30 dias
- **Localização**: Acessíveis na aba Actions do repositório

### Monitoramento
- **Status**: Visível diretamente no GitHub
- **Notificações**: 
  - Falhas notificadas automaticamente
  - Status check em pull requests
  - Detalhamento de erros na interface
- **Performance**: Métricas de tempo de execução disponíveis

## Troubleshooting

### Problemas Comuns

1. **Erro de Autenticação**
   - Verifique se o token está sendo gerado corretamente
   - Confirme se o token está sendo passado no header

2. **Falha na Criação de Recursos**
   - Verifique se os dados estão no formato correto
   - Confirme se não há violação de regras de negócio

3. **Timeouts**
   - Ajuste os timeouts no arquivo de configuração
   - Verifique a conectividade com a API

## Manutenção e Evolução

### Adicionando Novos Testes
1. Identifique o fluxo adequado na collection
2. Adicione o novo request com seus pré-requisitos
3. Implemente os scripts de teste
4. Atualize a documentação

### Boas Práticas
- Mantenha os testes independentes
- Use dados dinâmicos para evitar conflitos
- Documente alterações significativas
- Mantenha os ambientes atualizados

### Ciclo de Desenvolvimento
1. Desenvolva localmente usando Postman
2. Teste com Newman
3. Atualize a documentação
4. Faça o commit das alterações
5. Verifique a execução no pipeline
