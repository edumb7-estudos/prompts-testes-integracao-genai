# Prompts para testes de integração com GenAI

## Cenário

API REST de pedidos de e-commerce integrada a um serviço de estoque. Criar um pedido deve reservar os itens e permitir que o pedido seja consultado posteriormente.

## Prompt 1: geração de cenários a partir do contrato

> Atue como engenheiro(a) de testes de software com experiência em testes de integração de APIs REST. Considere o seguinte contrato didático:
>
> - `POST /orders` cria um pedido e reserva estoque, retornando `201 Created`.
> - `GET /orders/{id}` retorna `200 OK` para pedido existente e `404 Not Found` para pedido inexistente.
> - Estoque insuficiente resulta em `409 Conflict`.
> - Corpo inválido resulta em `400 Bad Request`.
> - A criação deve atualizar o estoque e deixar o pedido disponível para consulta.
>
> Gere casos de teste de integração que cubram fluxos de sucesso, validações, limites e códigos de resposta. Para cada caso, informe: ID, objetivo, pré-condições, requisição, preparação dos dados, resultado HTTP esperado, verificações no estado do pedido, verificações no estoque e critério de aprovação.
>
> Inclua casos positivos e negativos. Não invente endpoints, campos ou códigos de resposta além dos informados. Quando uma informação estiver ausente, identifique-a como premissa a confirmar. Use apenas dados fictícios e não dependa de serviços externos reais.

## Prompt 2: consistência de estado, repetição e falhas entre componentes

> Atue como engenheiro(a) de qualidade especializado(a) em testes de integração e consistência de dados. No mesmo cenário de e-commerce, `POST /orders` conversa com um serviço de estoque e `GET /orders/{id}` consulta o pedido registrado.
>
> Analise o fluxo entre os componentes e gere casos para detectar inconsistências que não seriam identificadas verificando apenas o status HTTP. Considere:
>
> 1. O pedido criado deve aparecer na consulta posterior.
> 2. A quantidade reservada deve corresponder aos itens do pedido.
> 3. Estoque insuficiente não pode criar pedido nem reduzir estoque.
> 4. A repetição da mesma requisição com o mesmo `Idempotency-Key` não pode duplicar pedido nem reservar estoque novamente.
> 5. Se o serviço de estoque falhar, o pedido não pode ficar confirmado parcialmente.
> 6. Uma falha no meio do fluxo não deve deixar estado inconsistente.
>
> Para cada caso, descreva a sequência de chamadas, os dados iniciais, o resultado esperado em cada componente e como verificar o estado final. Diferencie o que pode ser comprovado pelo contrato do que depende de uma premissa de implementação. Sugira simulações de falha em ambiente de teste, sem acessar serviços reais. Não presuma mecanismos de transação, compensação ou retentativa que não foram especificados; sinalize-os como decisões a validar.

## Observação sobre uso

Os prompts foram estruturados para servir de entrada a uma LLM. Antes de afirmar que os resultados foram executados, é necessário rodá-los em uma LLM, revisar as saídas e validar os casos contra uma implementação ou ambiente de teste.
