## Go Template

Template para criação de projetos em Go, utilizando as melhores práticas e padrões de desenvolvimento.

#### 🖥️ Detalhes Gerais:

Especificações e detalhes gerais do projeto. 
- a
- b
- c

> Como adicional, será também possível consultar um pedido específico, através de um endpoint (GET /order/:id), um service `ListOrderById` com gRPC e uma query `listOrder` com GraphQL.

#### 🗂️ Estrutura do Projeto
    .
    ├── cmd                # Entrypoints da aplicação
    │    └── ordersystem   
    │           ├── main.go       ### Entrypoint principal
    │           ├── wire.go       ### Injeção de dependências
    │           └── .env          ### Arquivo de parametrizações globais
    ├── configs            # helpers para configuração da aplicação (viper)
    ├── internal
    │    ├── domain        # Core da aplicação
    │    │      ├── repository    ### Interfaces de repositório
    │    │      └── entity        ### Entidades de domínio
    │    ├── application   # Implementações de casos de uso e utilitários
    │    │      └── usecase       ### Casos de uso da aplicação
    │    ├── infra         # Implementações de repositórios e conexões com serviços externos
    │    │      ├── database      ### Implementações de repositório
    │    │      ├── graph         ### Implementações e códigos gerados para a API GraphQL
    │    │      ├── grpc          ### Implementações e códigos gerados para a API gRPC
    │    │      └── web           ### Implementações e códigos gerados para a API Rest
    │    └── event         # Implementações de eventos e listeners
    ├── pkg                # Pacotes reutilizáveis utilizados na aplicação
    ├── init_db.sql        # Script de inicialização do banco de dados
    └── README.md

#### 🧭 Parametrização
A aplicação servidor possui um arquivo de configuração `cmd/ordersystem/.env` onde é possível definir os parâmetros de timeout e URL's das API's para busca das informações do endereço.

```
DB_DRIVER=mysql                 # Database driver
```

#### 🚀 Execução:
Para executar a aplicação, basta utilizar o docker-compose disponível na raiz do projeto. Para isso, execute o comando abaixo:
```bash
$ docker-compose up
```

> 💡 O comando acima poderá falhar caso alguma das portas utilizadas estejam em uso. Caso isso ocorra, será necessário alterar as portas no arquivo `.env` ou encerrar os processos que estão utilizando as portas (8000, 8080, 50051, 3306, 5672 e 15672).

### 📝 Usando as API's:

#### 1. REST API:

- **Criar um pedido:**
```bash
$ curl --location 'http://localhost:8000/order' \
--header 'Content-Type: application/json' \
--data '{
    "id": "aff0-2223-8842-fe215",
    "price": 66.5,
    "tax": 1.1
}'
```

- **Listar todos os pedidos (exemplo):**
```bash
$ curl --location 'http://localhost:8000/order'
```

- **Consultar um pedido (exemplo):**
```bash
$ curl --location 'http://localhost:8000/order/<<OrderId>>'
```

#### 2. GraphQL API:

> Para utilizar a API GraphQL, é necessário acessar o playground disponível em `http://localhost:8080/`.

- **Criar um pedido (exemplo):**
```graphql
mutation createOrder {
    createOrder(input:{id: "aff0-2223-8842-fe214",Price:854.1, Tax: 0.8}){
        id
    }
}
```

- **Listar todos os pedidos (exemplo):**
```graphql
query listOrders {
    listOrders {
        id
        Price
        Tax
        FinalPrice
    }
}
```

- **Consultar um pedido (exemplo):**
```graphql
query findOrder {
    listOrder(id:"aff0-2223-8842-fe215"){
        id
        Price
        Tax
        FinalPrice
    }
}
```

#### 3. gRPC API:

> Para a utilização da API gRPC, foi utilizado o Evans gRCP client. Para instalar, siga as instruções disponíveis em: [evans - install](https://github.com/ktr0731/evans?tab=readme-ov-file#installation)


- **Iniciando Evans:**
```bash
$ evans -r repl --host localhost --port 50051
 
localhost:50051>  package pb
pb@localhost:50051>  service OrderService
```

- **Criar um pedido (exemplo):**
```bash
pb.OrderService@localhost:50051> call CreateOrder
id (TYPE_STRING) => 1
price (TYPE_FLOAT) => 100
tax (TYPE_FLOAT) => 50
{
  "finalPrice": 150,
  "id": "1",
  "price": 100
}
```

- **Listar todos os pedidos (exemplo):**
```bash
pb.OrderService@localhost:50051> call ListOrders
{
  "orders": [
    {
      "finalPrice": 150,
      "id": "1",
      "price": 100,
      "tax": 50
    }
  ]
}
```

- **Consultar um pedido (exemplo):**
```bash
pb.OrderService@localhost:50051> call ListOrderById
id (TYPE_STRING) => aff0-2223-8842-fe214
{
  "finalPrice": 854.9,
  "id": "aff0-2223-8842-fe214",
  "price": 854.1,
  "tax": 0.8
}
```