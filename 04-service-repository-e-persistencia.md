# Parte 03 — Service, Repository e Persistência

## Objetivo

Separar as regras de negócio da responsabilidade de acessar os dados.

---

# 1. Service

A Service fica responsável pelas regras da aplicação.

```text
Controller
   ↓
Service
```

Exemplos de responsabilidades:

```text
Validação
Regra de negócio
Decisão
Fluxo
```

---

# 2. ClienteService

```java
public class ClienteService {

    private final ClienteRepository repository;

    public ClienteService(
            ClienteRepository repository
    ) {

        this.repository =
                repository;
    }

    public void cadastrar(
            Cliente cliente
    ) {

        if (
                cliente.getNome() == null ||
                cliente.getNome().isBlank()
        ) {

            throw new IllegalArgumentException(
                    "Nome é obrigatório"
            );
        }

        repository.salvar(cliente);
    }

    public Cliente buscarPorId(
            Integer id
    ) {

        return repository
                .buscarPorId(id);
    }

    public List<Cliente> listarTodos() {

        return repository
                .listarTodos();
    }

    public void atualizar(
            Cliente cliente
    ) {

        repository.atualizar(
                cliente
        );
    }

    public void excluir(
            Integer id
    ) {

        repository.excluir(id);
    }
}
```

---

# 3. O que é Repository?

Repository é a camada responsável pelo acesso aos dados.

Ele fica entre:

```text
Service
```

e:

```text
Banco de dados
```

Fluxo:

```text
Service
   ↓
Repository
   ↓
Banco
```

A Service pode dizer:

```text
"Salve este cliente"
```

O Repository será responsável por realizar essa operação.

---

# 4. Por que utilizar Repository?

Queremos evitar:

```text
Service
↓
SQL
```

Também queremos evitar:

```text
Controller
↓
Banco
```

Com Repository:

```text
Controller
   ↓
Service
   ↓
Repository
   ↓
Banco
```

Cada parte possui uma responsabilidade.

---

# 5. ClienteRepository

```java
public interface ClienteRepository {

    void salvar(
            Cliente cliente
    );

    Cliente buscarPorId(
            Integer id
    );

    List<Cliente> listarTodos();

    void atualizar(
            Cliente cliente
    );

    void excluir(
            Integer id
    );
}
```

A interface define:

```text
o que pode ser feito
```

A implementação define:

```text
como será feito
```

---

# 6. Repository com Hibernate + JPA

```text
ClienteRepository
        ↑
        │ implements
        │
ClienteRepositoryImpl
        ↓
EntityManager
        ↓
Hibernate
        ↓
PostgreSQL
```

Exemplo:

```java
public class ClienteRepositoryImpl
        implements ClienteRepository {

    @Override
    public void salvar(
            Cliente cliente
    ) {

        EntityManager entityManager =
                JPAUtil.getEntityManager();

        try {

            entityManager
                    .getTransaction()
                    .begin();

            entityManager.persist(
                    cliente
            );

            entityManager
                    .getTransaction()
                    .commit();

        } finally {

            entityManager.close();
        }
    }
}
```

---

# 7. Repository com JDBC

A mesma interface poderia possuir outra implementação:

```text
ClienteRepository
        ↑
        │
ClienteRepositoryImpl
        ↓
Connection
        ↓
PreparedStatement
        ↓
PostgreSQL
```

Isso é importante porque:

```text
Controller
   ↓
Service
   ↓
ClienteRepository
```

não precisa mudar.

---

# 8. Separação das responsabilidades

```text
Controller
↓
HTTP

Service
↓
Regra de negócio

Repository
↓
Persistência

Banco
↓
Armazenamento
```

---

# 9. Injeção das dependências

A Service recebe o Repository:

```java
ClienteRepository repository =
        new ClienteRepositoryImpl();

ClienteService service =
        new ClienteService(
                repository
        );
```

Depois o Controller recebe a Service:

```java
ClienteController controller =
        new ClienteController(
                service
        );
```

Fluxo:

```text
Repository
    ↓
Service
    ↓
Controller
```

---

# Resumo

Nesta etapa aprendemos:

- Service;
- regras de negócio;
- Repository;
- interface Repository;
- implementação Repository;
- persistência;
- separação das responsabilidades;
- injeção das dependências.
