# Desafio Técnico – API Corporativa de Integração (.NET / ASP.NET Core)

## 📌 Contexto

Uma empresa precisa expor uma API corporativa de integração para receber pedidos de sistemas externos (portal B2B, representantes comerciais, integrações futuras com BI), sem permitir acesso direto ao ERP legado. Essa API será responsável por validar, enriquecer e controlar o fluxo de pedidos, funcionando como uma camada intermediária de negócio.

## 🚧 Status do Projeto

![Status](https://img.shields.io/badge/Status-Em%20Desenvolvimento-yellow)
![.NET](https://img.shields.io/badge/.NET%208-512BD4?style=flat&logo=dotnet&logoColor=white)
![C#](https://img.shields.io/badge/C%23-239120?style=flat&logo=c-sharp&logoColor=white)
![Swagger](https://img.shields.io/badge/Swagger-85EA2D?style=flat&logo=Swagger&logoColor=black)

O projeto está atualmente em fase de desenvolvimento. As funcionalidades core estão sendo implementadas conforme os requisitos do desafio técnico.

---

## 🛠 Tecnologias Utilizadas

- **.NET 8** (ASP.NET Core Web API)
- **C#** (Linguagem de programação)
- **Swagger / OpenAPI** (Documentação da API)
- **HttpClient** (Consumo de APIs externas)
- **Dependency Injection** (Injeção de dependências nativa)
- **XUnit** (Testes unitários - *Planejado*)
- **Git** (Controle de versão)

---

## 📂 Estrutura do Projeto

A estrutura do projeto segue os princípios de **Clean Architecture** (ou arquitetura em camadas simplificada para o contexto), visando desacoplamento e testabilidade.

```
PontePedidos.Api/
├── Controllers/       # Endpoints da API
├── Domain/            # Entidades, Enums e Interfaces
├── Services/          # Regras de Negócio e Integrações
├── DTOs/              # Data Transfer Objects (Requests/Responses)
├── Infrastructure/    # Implementações de infraestrutura (se necessário)
├── Program.cs         # Configuração inicial e DI
└── README.md          # Documentação do projeto
```

## 🎯 Objetivo

Desenvolver uma API REST em **ASP.NET Core** que receba pedidos externos, valide os dados, consulte um serviço externo para obtenção de preços de produtos, calcule os valores do pedido e permita o acompanhamento do status de processamento, **sem uso de banco de dados** (persistência apenas em memória).

---

## 🚀 Endpoints Obrigatórios

### 1. Receber Pedido Externo
**POST** `/api/v1/integracoes/pedidos`

**Request:**
```json
{
  "identificadorExterno": "REP-123456",
  "empresaOrigem": "FILIAL_SP",
  "itens": [
    {
      "codigoProduto": 1,
      "quantidade": 2
    }
  ]
}
```

**Response (201 Created):**
```json
{
  "identificadorExterno": "REP-123456",
  "status": "RECEBIDO",
  "total": 189.98
}
```

### 2. Listar Pedidos Recebidos
**GET** `/api/v1/integracoes/pedidos`

**Response (200 OK):**
```json
[
  {
    "identificadorExterno": "REP-123456",
    "empresaOrigem": "FILIAL_SP",
    "status": "RECEBIDO",
    "total": 189.98
  }
]
```

### 3. Consultar Pedido por Identificador
**GET** `/api/v1/integracoes/pedidos/{identificadorExterno}`

**Response (200 OK):**
```json
{
  "identificadorExterno": "REP-123456",
  "empresaOrigem": "FILIAL_SP",
  "status": "RECEBIDO",
  "total": 189.98,
  "itens": [
    {
      "codigoProduto": 1,
      "descricao": "Produto XYZ",
      "quantidade": 2,
      "precoUnitario": 94.99,
      "subtotal": 189.98
    }
  ]
}
```

### 4. Atualizar Status do Pedido
**PUT** `/api/v1/integracoes/pedidos/{identificadorExterno}/status`

**Request:**
```json
{
  "status": "ENVIADO_ERP"
}
```

**Response (200 OK):**
```json
{
  "identificadorExterno": "REP-123456",
  "status": "ENVIADO_ERP"
}
```

---

## 📋 Regras de Negócio

1. **Validações de Entrada:**
   - `identificadorExterno` deve ser único.
   - `empresaOrigem` é obrigatório.
   - Cada pedido deve possuir ao menos um item.
   - `quantidade` deve ser maior que zero.

2. **Cálculo de Preços:**
   - O preço do produto **não** vem no request.
   - O preço e a descrição devem ser obtidos via **API externa de catálogo**.
   - O total do pedido é calculado automaticamente (Soma de `quantidade * precoUnitario`).

3. **Fluxo de Status:**
   - Os status permitidos são:
     - `RECEBIDO`
     - `VALIDADO`
     - `ENVIADO_ERP`
     - `ERRO`
   - O fluxo correto de transição de status deve ser respeitado/validado.

4. **Resiliência:**
   - Falhas na integração externa devem retornar erro controlado, sem quebrar a API ("Graceful degradation" ou tratamento de exceção adequado).

---

## 🌐 Integração Externa

Utilizar a seguinte API pública para simular o catálogo corporativo:

**GET** `https://dummyjson.com/products/{id}`

**Campos a utilizar:**
- `title` (Nome do produto/Descrição)
- `price` (Preço)

---

## 💾 Persistência

- **Não utilizar banco de dados.**
- Armazenar dados em memória utilizando estruturas como `List<>` ou `ConcurrentDictionary<>` (Singleton service).
---

## 🚀 Como Executar Localmente

### Pré-requisitos
- [.NET SDK 8.0](https://dotnet.microsoft.com/download/dotnet/8.0) instalado.
- [Visual Studio Code](https://code.visualstudio.com/) ou [Visual Studio 2022](https://visualstudio.microsoft.com/).
- [Git](https://git-scm.com/) instalado.

### Passo a Passo

1. **Clone o repositório:**
   ```bash
   git clone https://github.com/seu-usuario/PontePedidos.Api.git
   cd PontePedidos.Api
   ```

2. **Restaure as dependências:**
   ```bash
   dotnet restore
   ```

3. **Execute a aplicação:**
   ```bash
   dotnet run
   ```

4. **Acesse a documentação (Swagger):**
   Abra o navegador e acesse:
   `http://localhost:5000/swagger` (ou a porta configurada no `launchSettings.json`).

5. **Teste os endpoints:**
   Utilize o Swagger UI ou ferramentas como Postman/Insomnia para enviar requisições para a API.
