# Prompts para testes de integração com GenAI

## Cenário escolhido

API REST de pedidos de um e-commerce. Ao criar um pedido, o serviço de pedidos consulta o estoque, reserva as quantidades solicitadas e registra o pedido. O fluxo integra API, serviço de pedidos e serviço de estoque. Os testes devem verificar não apenas o status HTTP, mas também os efeitos compartilhados entre os componentes.

### Contrato assumido para este exercício

Como o cenário é demonstrativo, foram definidos os seguintes comportamentos esperados:

- `POST /orders`: cria pedido e reserva estoque; retorna `201 Created`.
- `GET /orders/{id}`: consulta pedido criado; retorna `200 OK` ou `404 Not Found`.
- Se não houver estoque suficiente, `POST /orders` retorna `409 Conflict` e não cria pedido nem altera o estoque.
- Se o corpo for inválido, retorna `400 Bad Request` e não altera estado.
- A repetição da mesma requisição com o mesmo `Idempotency-Key` não deve criar um segundo pedido nem reservar estoque novamente.
- Uma falha do serviço de estoque deve impedir a confirmação do pedido e produzir uma resposta de erro controlada (`503 Service Unavailable`).
- Os testes devem usar ambiente isolado e dados fictícios, sem depender de serviços externos reais.

Esses comportamentos são premissas didáticas deste exemplo, não especificações de uma API existente.

## Arquivos

- `docs/prompts.md`: os dois prompts utilizados como modelos para gerar os casos.
- `docs/casos_teste_integracao.md`: casos de teste derivados dos prompts.
- `docs/analise.md`: análise de qualidade, diversidade e ajustes dos prompts.

## Como aproveitar o material

1. Crie um repositório público no GitHub com o nome `prompts-testes-integracao-genai`.
2. Adicione estes arquivos.
3. Confira se os comportamentos definidos aqui correspondem ao contrato da API escolhida.
4. Se implementar a API, execute os casos automatizados e registre os resultados reais. Os casos deste repositório são uma especificação de teste; não representam, por si só, testes executados.
