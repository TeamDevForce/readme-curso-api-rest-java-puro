# Parte 01 — HTTP, Servidor e Rotas

## Objetivo

Revisar os principais conceitos relacionados a APIs e HTTP e criar nosso primeiro servidor utilizando Java.

---

# 1. O que é uma API?

API significa:

**Application Programming Interface**

De forma simples, uma API permite que aplicações se comuniquem.

```text
Aplicação A
    ↓
   API
    ↓
Aplicação B
```

Um exemplo comum:

```text
Frontend
   ↓
API
   ↓
Backend
   ↓
Banco de dados
```

---

# 2. Cliente e servidor

## Cliente

É quem inicia a comunicação através de uma requisição.

Exemplos:

```text
Navegador
Postman
Frontend
Aplicativo
Outro sistema
```

## Servidor

É quem recebe a requisição, realiza algum processamento e envia uma resposta.

```text
Cliente
   ↓
Requisição
   ↓
Servidor
   ↓
Resposta
   ↓
Cliente
```

---

# 3. HTTP

HTTP é um protocolo utilizado para comunicação entre aplicações.

Uma requisição pode possuir:

```text
Método
URL
Headers
Body
```

Uma resposta pode possuir:

```text
Status Code
Headers
Body
```

---

# 4. O que é REST?

REST é um estilo utilizado para organização de APIs.

Uma das ideias principais é trabalhar com **recursos**.

Nossa aplicação terá o recurso:

```text
clientes
```

Por isso teremos URLs como:

```text
/clientes
```

e:

```text
/clientes/10
```

---

# 5. Métodos HTTP

Utilizaremos:

| Método | Objetivo |
| --- | --- |
| `POST` | Criar |
| `GET` | Consultar |
| `PUT` | Atualizar |
| `DELETE` | Excluir |

Relacionando com CRUD:

```text
CREATE → POST
READ   → GET
UPDATE → PUT
DELETE → DELETE
```

---

# 6. Rotas

Nossa API terá:

```text
POST    /clientes
GET     /clientes
GET     /clientes/{id}
PUT     /clientes/{id}
DELETE  /clientes/{id}
```

Uma rota pode ser entendida como:

```text
Método HTTP
+
Caminho
```

Por exemplo:

```text
GET
+
/clientes
```

forma:

```text
GET /clientes
```

---

# 7. Status Codes

Toda resposta HTTP possui um código indicando o resultado da operação.

| Código | Significado |
| --- | --- |
| `200` | OK |
| `201` | Criado |
| `204` | Sem conteúdo |
| `400` | Requisição inválida |
| `404` | Não encontrado |
| `405` | Método não permitido |
| `500` | Erro interno |

---

# 8. Criando o servidor

Utilizaremos o servidor HTTP disponível no Java.

```java
import com.sun.net.httpserver.HttpServer;

import java.net.InetSocketAddress;

public class Main {

    public static void main(
            String[] args
    ) throws Exception {

        HttpServer server =
                HttpServer.create(
                        new InetSocketAddress(
                                8080
                        ),
                        0
                );

        server.start();

        System.out.println(
                "Servidor iniciado na porta 8080"
        );
    }
}
```

Nosso servidor estará utilizando a porta:

```text
8080
```

---

# 9. HttpHandler

Precisamos de uma classe responsável por tratar as requisições.

Para isso utilizaremos:

```java
HttpHandler
```

Crie:

```java
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;

import java.io.IOException;

public class ClienteHandler
        implements HttpHandler {

    @Override
    public void handle(
            HttpExchange exchange
    ) throws IOException {

        String resposta =
                "API funcionando!";

        byte[] bytes =
                resposta.getBytes();

        exchange.sendResponseHeaders(
                200,
                bytes.length
        );

        exchange
                .getResponseBody()
                .write(bytes);

        exchange.close();
    }
}
```

Aqui temos novamente conceitos importantes de Java:

```text
interface
↓
implements
↓
@Override
↓
método
```

---

# 10. Ligando Handler ao servidor

Na `Main`:

```java
ClienteHandler clienteHandler =
        new ClienteHandler();

server.createContext(
        "/clientes",
        clienteHandler
);
```

Agora temos:

```text
HttpServer
   ↓
/clientes
   ↓
ClienteHandler
   ↓
handle()
```

---

# 11. HttpExchange

O `HttpExchange` representa a comunicação HTTP.

Através dele podemos descobrir informações sobre a requisição.

Por exemplo:

```java
String metodo =
        exchange.getRequestMethod();
```

Podemos receber:

```text
GET
POST
PUT
DELETE
```

---

# 12. Recuperando a URL

```java
String caminho =
        exchange
                .getRequestURI()
                .getPath();
```

Uma requisição como:

```text
GET /clientes/10
```

terá o caminho:

```text
/clientes/10
```

---

# 13. Separando a URL

Podemos utilizar:

```java
String[] partes =
        caminho.split("/");
```

Para:

```text
/clientes/10
```

teremos:

```text
partes[0] = ""
partes[1] = "clientes"
partes[2] = "10"
```

---

# 14. Verificando se existe ID

```java
boolean possuiId =
        partes.length > 2;
```

Se existir:

```java
Integer id =
        Integer.valueOf(
                partes[2]
        );
```

---

# 15. Identificando as rotas

Agora podemos utilizar o método HTTP e o caminho para descobrir qual operação deve ser executada.

```java
String metodo =
        exchange.getRequestMethod();

String caminho =
        exchange
                .getRequestURI()
                .getPath();

String[] partes =
        caminho.split("/");

boolean possuiId =
        partes.length > 2;

if (
        "GET".equals(metodo) &&
        !possuiId
) {

    listarTodos(exchange);

} else if (
        "GET".equals(metodo) &&
        possuiId
) {

    Integer id =
            Integer.valueOf(
                    partes[2]
            );

    buscarPorId(
            exchange,
            id
    );

} else if (
        "POST".equals(metodo) &&
        !possuiId
) {

    cadastrar(exchange);

} else if (
        "PUT".equals(metodo) &&
        possuiId
) {

    Integer id =
            Integer.valueOf(
                    partes[2]
            );

    atualizar(
            exchange,
            id
    );

} else if (
        "DELETE".equals(metodo) &&
        possuiId
) {

    Integer id =
            Integer.valueOf(
                    partes[2]
            );

    excluir(
            exchange,
            id
    );

} else {

    exchange.sendResponseHeaders(
            405,
            -1
    );

    exchange.close();
}
```

---

# Fluxo

```text
Método HTTP
    +
URL
    ↓
Identificar rota
    ↓
Executar método
```

Por exemplo:

```text
GET /clientes/5
      ↓
GET
+
possui ID
      ↓
buscarPorId(5)
```

---

# Resumo

Nesta etapa aprendemos:

- API;
- cliente;
- servidor;
- HTTP;
- REST;
- métodos HTTP;
- Status Codes;
- rotas;
- `HttpServer`;
- `HttpHandler`;
- `HttpExchange`;
- método `handle()`;
- leitura da URL;
- leitura do método HTTP;
- identificação do ID;
- roteamento básico.

Na próxima etapa, vamos transformar nosso Handler em um **Controller** e trabalhar com JSON.
