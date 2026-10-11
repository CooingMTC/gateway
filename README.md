# 🚪 API Gateway - GoStreams

Ponto único de entrada (**Single Point of Entry**) para o ecossistema distribuído de microsserviços do **GoStreams**, desenvolvido com **Spring Cloud Gateway** (arquitetura reativa baseada em Project Reactor e Netty).

---

## 📌 Papel na Arquitetura

O **API Gateway** unifica e desacopla as chamadas de clientes externos (frontend e requisições HTTP diretas), eliminando a necessidade de expor múltiplos endpoints e portas públicas.

* **Porta Pública de Entrada:** `8080`
* **Roteamento Dinâmico:** Direciona tráfego com base nos predicados de caminho (`Path Predicates`).
* **Suporte a CORS Centralizado:** Habilita o tráfego seguro de credenciais e cookies com o frontend Next.js (`http://localhost:3000`).

---

## 🗺️ Mapeamento de Rotas & Portas Internas

| Microsserviço | Rota no Gateway | Destino Interno | Repositório |
|---|---|---|---|
| **Login & Autenticação** | `/auth/**` | `http://localhost:8083` | [github.com/CooingMTC/login](https://github.com/CooingMTC/login.git) |
| **Venda & Pedidos** | `/vendas/**` | `http://localhost:8081` | [github.com/CooingMTC/venda](https://github.com/CooingMTC/venda.git) |
| **Produto (Catálogo de Lives)** | `/lives/**` | `http://localhost:8082` | [github.com/CooingMTC/produto](https://github.com/CooingMTC/produto.git) |

---

## ⚙️ Pré-requisitos & Dependências

* **Java 21**
* **Maven** (ou Maven Wrapper incluso `./mvnw`)
* Os microsserviços de negócio devem estar em execução em suas respectivas portas (`8081`, `8082` e `8083`) para que o roteamento ocorra sem erros de `503 Service Unavailable`.

---

## 🚀 Como Executar o Gateway

1. Navegue até a pasta do projeto:
   ```bash
   cd gateway
   ```

2. Compile e inicialize o serviço:
   ```bash
   ./mvnw clean compile
   ./mvnw spring-boot:run
   ```

3. O servidor Netty subirá na porta **`8080`**.

---

## 🧪 Exemplos de Requisição (via Gateway na 8080)

Todas as requisições devem apontar exclusivamente para a porta **`8080`**:

### 1. Autenticação (Roteado para `8083`)
* **Registrar usuário:** `POST http://localhost:8080/auth/registro`
* **Realizar login:** `POST http://localhost:8080/auth/login`
* **Consultar usuário logado:** `GET http://localhost:8080/auth/me`

### 2. Catálogo de Lives (Roteado para `8082`)
* **Listar lives:** `GET http://localhost:8080/lives`
* **Cadastrar nova live:** `POST http://localhost:8080/lives`
  ```json
  {
    "titulo": "Workshop de Microsserviços",
    "descricao": "Transmissão ao vivo com vagas limitadas",
    "precoIngresso": 49.90,
    "capacidadeMaxima": 5
  }
  ```

### 3. Vendas & Pedidos (Roteado para `8081`)
* **Solicitar compra de ingresso:** `POST http://localhost:8080/vendas/comprar`
  ```json
  {
    "liveId": "<UUID_DA_LIVE>",
    "usuarioId": "<UUID_DO_USUARIO>",
    "quantidade": 1
  }
  ```
* **Consultar status do pedido:** `GET http://localhost:8080/vendas/pedidos/<PROTOCOLO_UUID>`
