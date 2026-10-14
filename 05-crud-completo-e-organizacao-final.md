# Parte 04 — CRUD Completo e Organização Final

## Objetivo

Integrar todas as partes da aplicação e implementar o CRUD através de endpoints HTTP.

---

# 1. Endpoints

Teremos:

```text
POST    /clientes

GET     /clientes

GET     /clientes/{id}

PUT     /clientes/{id}

DELETE  /clientes/{id}
```

---

# 2. POST /clientes

Responsável por cadastrar.

Entrada:

```json
{
    "nome": "Marcelo",
    "dataNascimento": "1992-05-20"
}
```

Fluxo:

```text
POST /clientes
      ↓
ClienteController
      ↓
JSON → Cliente
      ↓
ClienteService
      ↓
ClienteRepository
      ↓
Banco
```

Resposta:

```text
201 Created
```

---

# 3. GET /clientes

Responsável por listar.

```text
GET /clientes
      ↓
Controller
      ↓
Service
      ↓
Repository
      ↓
List<Cliente>
      ↓
JSON
```

Resposta:

```text
200 OK
```

---

# 4. GET /clientes/{id}

Exemplo:

```text
GET /clientes/10
```

Fluxo:

```text
URL
 ↓
Extrair ID
 ↓
Controller
 ↓
Service
 ↓
Repository
 ↓
Cliente
```

Caso não exista:

```text
404 Not Found
```

---

# 5. PUT /clientes/{id}

Exemplo:

```text
PUT /clientes/10
```

Entrada:

```json
{
    "nome": "Marcelo Sena",
    "dataNascimento": "1992-05-20"
}
```

Fluxo:

```text
ID da URL
    +
JSON
    ↓
Controller
    ↓
Service
    ↓
Repository
    ↓
Banco
```

Resposta:

```text
200 OK
```

---

# 6. DELETE /clientes/{id}

Exemplo:

```text
DELETE /clientes/10
```

Fluxo:

```text
Controller
   ↓
Service
   ↓
Repository
   ↓
Banco
```

Resposta:

```text
204 No Content
```

---

# 7. Estrutura final

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

# 8. Main

```java
import com.sun.net.httpserver.HttpServer;

import java.net.InetSocketAddress;

public class Main {

    public static void main(
            String[] args
    ) throws Exception {

        ClienteRepository repository =
                new ClienteRepositoryImpl();

        ClienteService service =
                new ClienteService(
                        repository
                );

        ClienteController controller =
                new ClienteController(
                        service
                );

        HttpServer server =
                HttpServer.create(
                        new InetSocketAddress(
                                8080
                        ),
                        0
                );

        server.createContext(
                "/clientes",
                controller
        );

        server.start();

        System.out.println(
                "API iniciada na porta 8080"
        );
    }
}
```

---

# 9. Montagem da aplicação

Primeiro:

```text
ClienteRepositoryImpl
```

Depois:

```text
ClienteRepositoryImpl
        ↓
ClienteService
```

Depois:

```text
ClienteService
      ↓
ClienteController
```

Finalmente:

```text
ClienteController
      ↓
HttpServer
```

Fluxo:

```text
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

---

# 10. Exemplo completo

Imagine:

```text
GET /clientes/5
```

O fluxo será:

```text
1. HttpServer recebe a requisição

2. ClienteController recebe a chamada

3. Controller identifica GET

4. Controller identifica o ID 5

5. Controller chama ClienteService

6. Service chama ClienteRepository

7. Repository consulta o banco

8. Repository retorna Cliente

9. Service retorna Cliente

10. Controller transforma Cliente em JSON

11. Controller devolve 200 OK
```

---

# 11. Fluxo de entrada

```text
Cliente
   ↓
HTTP
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

---

# 12. Fluxo de retorno

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

# Responsabilidades finais

```text
Controller
↓
Receber e responder HTTP

Service
↓
Aplicar regras de negócio

Repository
↓
Buscar e persistir dados

Model
↓
Representar os dados
```

---

# 🏁 Resultado final

Ao final do curso, o aluno deverá conseguir explicar o caminho:

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
Banco
```

E compreender que uma aplicação web é formada por várias responsabilidades que trabalham em conjunto.

O objetivo principal é entender **como uma requisição HTTP percorre uma aplicação Java do início ao fim**.
