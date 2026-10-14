# Parte 02 — Controller e JSON

## Objetivo

Criar uma camada responsável por receber requisições HTTP, interpretar os dados enviados pelo cliente e devolver respostas.

---

# 1. O que é Controller?

O Controller representa a entrada HTTP da nossa aplicação.

Sua responsabilidade é:

```text
Receber requisição
        ↓
Interpretar dados
        ↓
Chamar Service
        ↓
Preparar resposta
        ↓
Responder HTTP
```

O Controller deve conhecer HTTP.

Ele não deve ser responsável pelo acesso direto ao banco de dados.

---

# 2. Evoluindo ClienteHandler

Nosso `ClienteHandler` agora poderá assumir claramente o papel de Controller.

```java
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;

import java.io.IOException;

public class ClienteController
        implements HttpHandler {

    private final ClienteService clienteService;

    public ClienteController(
            ClienteService clienteService
    ) {

        this.clienteService =
                clienteService;
    }

    @Override
    public void handle(
            HttpExchange exchange
    ) throws IOException {

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
    }
}
```

---

# 3. Ligando Controller ao servidor

Na `Main`:

```java
ClienteController controller =
        new ClienteController(
                clienteService
        );

server.createContext(
        "/clientes",
        controller
);
```

Fluxo:

```text
HttpServer
   ↓
/clientes
   ↓
ClienteController
   ↓
handle()
```

---

# 4. O que é JSON?

JSON é um formato utilizado para representar e trocar dados.

Exemplo:

```json
{
    "nome": "Marcelo",
    "dataNascimento": "1992-05-20"
}
```

Nossa API precisará realizar:

```text
JSON
 ↓
Objeto Java
```

e também:

```text
Objeto Java
 ↓
JSON
```

---

# 5. Conversão de dados

Podemos utilizar uma biblioteca como Jackson.

Ela ficará responsável por:

```text
JSON
 ↕
Java
```

---

# 6. JsonUtil

Podemos centralizar essa conversão.

```java
public class JsonUtil {

    private static final ObjectMapper OBJECT_MAPPER =
            new ObjectMapper();

    private JsonUtil() {
    }

    public static String toJson(
            Object objeto
    ) throws JsonProcessingException {

        return OBJECT_MAPPER
                .writeValueAsString(
                        objeto
                );
    }

    public static <T> T fromJson(
            String json,
            Class<T> tipo
    ) throws JsonProcessingException {

        return OBJECT_MAPPER
                .readValue(
                        json,
                        tipo
                );
    }
}
```

---

# 7. Fluxo de cadastro

Uma requisição:

```text
POST /clientes
```

pode enviar:

```json
{
    "nome": "Marcelo",
    "dataNascimento": "1992-05-20"
}
```

O fluxo será:

```text
HTTP
 ↓
JSON
 ↓
ClienteController
 ↓
Cliente
 ↓
ClienteService
```

---

# 8. Fluxo de resposta

Ao consultar um cliente:

```text
Cliente
 ↓
Controller
 ↓
JSON
 ↓
HTTP
```

---

# 9. Responsabilidade do Controller

O Controller deve cuidar de:

```text
Método HTTP
URL
Body
JSON
Status Code
Resposta
```

Ele não deve cuidar diretamente de:

```text
SQL
Connection
EntityManager
Regra de negócio
```

---

# Resumo

Nesta etapa aprendemos:

- Controller;
- `HttpHandler`;
- método `handle()`;
- leitura de rotas;
- JSON;
- conversão entre JSON e objetos;
- entrada HTTP;
- saída HTTP;
- separação das responsabilidades.
