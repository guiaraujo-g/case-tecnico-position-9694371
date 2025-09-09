# Case Técnico - Ambiente de Desenvolvimento

Este projeto contém a configuração do ambiente Docker necessário para a realização do case técnico de Analista de Integração.

O objetivo é que você possa focar exclusivamente na criação do fluxo de automação no N8N, sem se preocupar com a instalação e configuração da ferramenta.

## Pré-requisitos

- Docker
- Docker Compose

## Como Iniciar o Ambiente

1.  **Crie o diretório para o Banco de Dados:**
    Antes de iniciar, crie uma pasta chamada `database` no mesmo diretório onde você salvou o arquivo `docker-compose.yml`. É nesta pasta que o seu fluxo deverá salvar o arquivo `logistics.sqlite`.

    ```bash
    mkdir database
    ```

2.  **Suba os contêineres:**
    No seu terminal, na raiz do projeto (onde o arquivo `docker-compose.yml` está localizado), execute o seguinte comando:

    ```bash
    docker-compose up -d
    ```

    Este comando fará o download da imagem do N8N (se for a primeira vez) e iniciará o serviço em background (`-d`).

3.  **Acesse o N8N:**
    Após a execução do comando, o editor do N8N estará disponível no seu navegador no seguinte endereço:
    [**http://localhost:5678**](http://localhost:5678)

    Na primeira vez que acessar, o N8N pedirá para você criar um usuário administrador. Sinta-se à vontade para usar qualquer informação.

## Estrutura dos Dados (JSONs)

Para guiar o desenvolvimento do fluxo, aqui estão as estruturas de dados que você encontrará em cada etapa do processo.

### 1. Payload de Entrada (Webhook)
Este é o JSON que seu webhook receberá para iniciar o fluxo.

```json
{
  "orderId": 101,
  "userId": 5,
  "status": "paid"
}
```

### 2. Resposta da API de Usuários (Exemplo)
Ao consultar a API `https://jsonplaceholder.typicode.com/users/{userId}` com `userId=5`, você receberá uma resposta com a seguinte estrutura.

```json
{
  "id": 5,
  "name": "Chelsey Dietrich",
  "username": "Kamren",
  "email": "Lucio_Hettinger@annie.ca",
  "address": {
    "street": "Skiles Walks",
    "suite": "Suite 351",
    "city": "Roscoeview",
    "zipcode": "33263",
    "geo": {
      "lat": "-31.8129",
      "lng": "62.5342"
    }
  },
  "phone": "(254)954-1289",
  "website": "demarco.info",
  "company": {
    "name": "Keebler LLC",
    "catchPhrase": "User-centric fault-tolerant solution",
    "bs": "revolutionize end-to-end systems"
  }
}
```

### 3. Objeto Final (Após a Transformação)
Este é o formato final que seu fluxo deve gerar. Este objeto deve ser salvo no banco SQLite e enviado na notificação final.

```json
{
  "order_id": 101,
  "customer_name": "Chelsey Dietrich",
  "customer_email": "Lucio_Hettinger@annie.ca",
  "delivery_address": "Skiles Walks, Suite 351, Roscoeview, 33263",
  "processed_at": "2025-09-08T21:05:45.000Z"
}
```
**Observação:** O valor do campo `processed_at` é apenas um exemplo. Seu fluxo deve gerar este valor dinamicamente com a data e hora do processamento no formato ISO 8601.

## Dicas sobre o Ambiente

-   **Caminho do Banco de Dados:** A pasta local `./database` foi mapeada para o diretório `/data` dentro do contêiner do N8N. Para o nó do SQLite, use o caminho `/data/logistics.sqlite` para o arquivo do banco.
-   **URL do Webhook:** Como o ambiente está rodando localmente (`N8N_TUNNEL_DISABLED=true`), a URL do seu webhook de teste será simples e direta, como `http://localhost:5678/webhook-test/SUA-URL-DE-TESTE`.
-   **Teste com cURL:** Você pode usar o comando abaixo para simular o envio de um pedido para o seu webhook. Lembre-se de substituir `{id-do-seu-webhook}` pelo caminho correto do seu fluxo.

    ```bash
    curl -X POST http://localhost:5678/webhook/{id-do-seu-webhook} \
    -H "Content-Type: application/json" \
    -d '{
      "orderId": 101,
      "userId": 5,
      "status": "paid"
    }'
    ```

## Ao Finalizar

Quando concluir o desenvolvimento, por favor, exporte seu workflow como um arquivo JSON (`workflow.json`) e o inclua na sua entrega, juntamente com este `README` e o `docker-compose.yml`.

Boa sorte!