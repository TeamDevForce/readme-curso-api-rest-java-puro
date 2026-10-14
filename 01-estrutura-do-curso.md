# API REST com Java Puro

Curso introdutório para construção de uma **API REST utilizando Java**.

Durante o curso, vamos construir uma API de clientes utilizando conceitos já estudados anteriormente, como:

- classes e objetos;
- interfaces;
- separação de responsabilidades;
- Service;
- persistência com JDBC ou Hibernate + JPA.

Agora vamos adicionar uma nova camada responsável pela comunicação HTTP:

```text
Controller
```

O objetivo é entender como uma requisição chega até nossa aplicação, passa pelas regras de negócio, acessa os dados e retorna uma resposta para quem realizou a chamada.

---

## 🎯 Objetivo do curso

Ao final do curso, o aluno deverá ser capaz de:

- compreender o que é uma API;
- compreender o conceito de REST;
- entender o funcionamento básico do HTTP;
- entender cliente e servidor;
- conhecer os principais métodos HTTP;
- conhecer os principais Status Codes;
- criar um servidor HTTP utilizando Java;
- criar rotas;
- receber requisições;
- enviar respostas;
- trabalhar com JSON;
- criar uma camada Controller;
- criar uma camada Service;
- criar uma camada Repository;
- integrar a API com persistência;
- implementar um CRUD completo;
- compreender o fluxo completo de uma API.

---

# 📚 Estrutura do curso

O curso será dividido em cinco arquivos:

```text
01-estrutura-do-curso.md
02-http-servidor-e-rotas.md
03-controller-e-json.md
04-service-repository-e-persistencia.md
05-crud-completo-e-organizacao-final.md
```

---

# Parte 1 — HTTP, servidor e rotas

## Objetivo

Revisar os conceitos básicos de comunicação entre sistemas e criar nosso primeiro servidor HTTP.

## Conteúdo

- o que é uma API;
- cliente e servidor;
- o que é HTTP;
- o que é REST;
- requisição e resposta;
- URL;
- porta;
- rota;
- métodos HTTP;
- Status Codes;
- `HttpServer`;
- `HttpHandler`;
- `HttpExchange`;
- leitura da URL;
- identificação de rotas.

---

# Parte 2 — Controller e JSON

## Objetivo

Criar uma camada responsável pela entrada e saída HTTP da aplicação.

## Conteúdo

- conceito de Controller;
- criação de `ClienteController`;
- implementação de `HttpHandler`;
- método `handle()`;
- leitura de requisições;
- identificação do método HTTP;
- leitura do ID na URL;
- leitura do corpo da requisição;
- JSON;
- conversão entre JSON e objetos;
- envio de respostas HTTP.

---

# Parte 3 — Service, Repository e Persistência

## Objetivo

Organizar as responsabilidades da aplicação separando HTTP, regras de negócio e persistência.

## Conteúdo

- revisão da camada Service;
- criação da `ClienteService`;
- conceito de Repository;
- criação de `ClienteRepository`;
- criação de `ClienteRepositoryImpl`;
- diferença entre Service e Repository;
- persistência utilizando JDBC ou Hibernate + JPA;
- injeção das dependências pelo construtor.

---

# Parte 4 — CRUD completo e organização final

## Objetivo

Integrar todas as camadas e implementar os endpoints da API.

## Endpoints

```text
POST    /clientes

GET     /clientes

GET     /clientes/{id}

PUT     /clientes/{id}

DELETE  /clientes/{id}
```

---

# Métodos HTTP

| Método | Objetivo | CRUD |
| --- | --- | --- |
| `POST` | Criar | CREATE |
| `GET` | Consultar | READ |
| `PUT` | Atualizar | UPDATE |
| `DELETE` | Excluir | DELETE |

---

# Status Codes

Utilizaremos principalmente:

| Código | Significado |
| --- | --- |
| `200` | Operação realizada com sucesso |
| `201` | Recurso criado |
| `204` | Operação realizada sem conteúdo |
| `400` | Dados inválidos |
| `404` | Recurso não encontrado |
| `405` | Método não permitido |
| `500` | Erro interno |

---

# 🗂️ Estrutura final do projeto

```text
src/main/java
└── com
    └── devforce
        └── api
            │
            ├── Main.java
            │
            ├── controller
            │   └── ClienteController.java
            │
            ├── service
            │   └── ClienteService.java
            │
            ├── repository
            │   ├── ClienteRepository.java
            │   └── ClienteRepositoryImpl.java
            │
            ├── model
            │   └── Cliente.java
            │
            ├── config
            │   └── JPAUtil.java
            │
            └── util
                └── JsonUtil.java
```

---

# Responsabilidade das camadas

```text
Controller
↓
Comunicação HTTP

Service
↓
Regras de negócio

Repository
↓
Acesso aos dados

Model
↓
Representação dos dados
```

---

# Fluxo da aplicação

A entrada:

```text
Cliente HTTP
     ↓
HttpServer
     ↓
Controller
     ↓
Service
     ↓
Repository
     ↓
Banco
```

A resposta:

```text
Banco
 ↓
Repository
 ↓
Service
 ↓
Controller
 ↓
JSON
 ↓
HTTP
 ↓
Cliente
```

---

# 🏁 Resultado final

Ao final do curso, o aluno deverá conseguir visualizar uma requisição como:

```text
POST /clientes
      ↓
HttpServer
      ↓
ClienteController
      ↓
ClienteService
      ↓
ClienteRepository
      ↓
Banco de dados
```

E entender claramente a responsabilidade de cada parte da aplicação.
