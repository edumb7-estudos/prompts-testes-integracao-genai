# Casos de teste de integração gerados a partir dos prompts

## Prompt 1: contrato, fluxos normais e validações

### CT01 - Criar pedido com estoque disponível
- **Pré-condição:** produto P100 com 10 unidades disponíveis.
- **Ação:** enviar `POST /orders` com uma unidade de P100 e dados válidos.
- **Esperado:** HTTP `201 Created`; pedido registrado; estoque reduzido/reservado em uma unidade.
- **Verificação de integração:** consultar `GET /orders/{id}` e confirmar que o item e a quantidade correspondem à requisição.

### CT02 - Consultar pedido existente
- **Pré-condição:** pedido criado com sucesso.
- **Ação:** enviar `GET /orders/{id}` usando o identificador retornado.
- **Esperado:** HTTP `200 OK`; dados do pedido correspondem aos dados persistidos.
- **Verificação de integração:** a leitura deve refletir a gravação feita pelo fluxo de criação.

### CT03 - Consultar pedido inexistente
- **Pré-condição:** identificador que não corresponde a pedido cadastrado.
- **Ação:** enviar `GET /orders/{id}`.
- **Esperado:** HTTP `404 Not Found`; nenhum pedido novo é criado.

### CT04 - Criar pedido sem estoque suficiente
- **Pré-condição:** produto P200 com 2 unidades disponíveis.
- **Ação:** solicitar 3 unidades de P200.
- **Esperado:** HTTP `409 Conflict`; pedido não é criado e o estoque permanece em 2 unidades.
- **Verificação de integração:** consultar o estoque e a lista/consulta de pedidos por mecanismo disponível no ambiente de teste.

### CT05 - Rejeitar corpo inválido
- **Pré-condição:** ambiente limpo.
- **Ação:** enviar `POST /orders` sem um campo obrigatório ou com quantidade inválida.
- **Esperado:** HTTP `400 Bad Request`; nenhum pedido criado e nenhuma alteração no estoque.
- **Nota:** os campos obrigatórios precisam ser ajustados ao contrato real da API.

### CT06 - Pedido com vários itens
- **Pré-condição:** P100 com 8 unidades e P300 com 5 unidades.
- **Ação:** criar um pedido com 2 unidades de P100 e 3 unidades de P300.
- **Esperado:** HTTP `201 Created`; pedido contém os dois itens e as quantidades corretas; estoque de cada produto é atualizado de acordo com a reserva.
- **Verificação de integração:** a atualização de um item não deve apagar nem alterar incorretamente o outro.

## Prompt 2: estado compartilhado, idempotência e falhas

### CT07 - Repetir criação com a mesma chave de idempotência
- **Pré-condição:** P100 com 10 unidades disponíveis.
- **Ação:** enviar duas vezes a mesma requisição de criação com o mesmo `Idempotency-Key`.
- **Esperado:** o contrato didático prevê que a repetição não cria outro pedido nem reserva novamente o estoque.
- **Verificação de integração:** comparar o identificador do pedido e o saldo do estoque após as duas chamadas.
- **Nota:** o status da segunda resposta deve ser confirmado no contrato da implementação.

### CT08 - Falha do serviço de estoque
- **Pré-condição:** configurar o serviço de estoque para simular indisponibilidade.
- **Ação:** enviar `POST /orders` com dados válidos.
- **Esperado:** HTTP `503 Service Unavailable`, conforme a premissa didática; o pedido não deve ficar confirmado.
- **Verificação de integração:** consultar o estado final e garantir que não exista pedido confirmado sem reserva correspondente.

### CT09 - Estoque não deve ficar negativo
- **Pré-condição:** produto com 1 unidade disponível.
- **Ação:** executar duas solicitações concorrentes, cada uma pedindo 1 unidade.
- **Esperado:** no máximo uma solicitação pode obter a unidade; o estoque não pode ficar negativo.
- **Verificação de integração:** validar os resultados das duas chamadas e o estado final do estoque.
- **Nota:** o comportamento exato da segunda resposta depende das regras de concorrência da API.

### CT10 - Falha após reserva e antes da confirmação do pedido
- **Pré-condição:** ambiente de teste capaz de injetar falha entre reserva de estoque e confirmação do pedido.
- **Ação:** iniciar a criação e provocar a falha no ponto intermediário.
- **Esperado:** o sistema não deve manter pedido confirmado com estado incompleto. A reserva deve ser desfeita ou tratada por mecanismo de recuperação definido pela arquitetura.
- **Verificação de integração:** inspecionar pedido e estoque após a falha e após eventual rotina de recuperação.
- **Nota:** a estratégia de compensação/transação precisa ser definida pelo projeto; o teste deve ser ajustado à decisão arquitetural.

### CT11 - Pedido recusado não deve aparecer em consulta
- **Pré-condição:** produto sem estoque suficiente.
- **Ação:** tentar criar o pedido e depois consultar o pedido, caso a resposta tenha fornecido algum identificador.
- **Esperado:** não deve existir pedido confirmado para a operação recusada.
- **Verificação de integração:** consultar o estado persistido, não apenas a resposta HTTP.

### CT12 - Quantidade reservada corresponde ao pedido
- **Pré-condição:** produto P400 com 20 unidades disponíveis.
- **Ação:** criar pedido de 4 unidades.
- **Esperado:** o pedido registra 4 unidades e o estoque disponível diminui/reserva exatamente 4 unidades, de acordo com o modelo de estoque utilizado.
- **Verificação de integração:** comparar a quantidade pedida com a alteração observada no estoque.

## Observações para execução

- Use banco e serviços isolados para os testes; restaure os dados entre casos.
- Evite depender da ordem de execução.
- Para concorrência e falhas intermediárias, use ferramentas de teste ou pontos de injeção de falha disponíveis na implementação.
- Não valide apenas o status HTTP: confira os efeitos no estado compartilhado.
- Confirme o contrato real antes de transformar as premissas didáticas em asserções automatizadas.
