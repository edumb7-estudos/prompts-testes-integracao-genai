# Análise dos prompts e dos casos

## Como cada prompt influencia os resultados

O **Prompt 1**, orientado pelo contrato, favorece amplitude e rastreabilidade. Ele organiza os testes por operação, código HTTP, validação e fluxo esperado. Isso ajuda a encontrar lacunas na cobertura de respostas e a verificar se o resultado de uma gravação pode ser lido depois. Seu limite é depender da qualidade do contrato: regras de consistência não documentadas podem ficar de fora.

O **Prompt 2**, focado em estado compartilhado e falhas, favorece profundidade. Ele direciona a LLM para idempotência, concorrência, falhas intermediárias e divergências entre pedido e estoque. Esses casos são importantes porque uma resposta HTTP aparentemente correta não garante que os componentes tenham terminado em um estado consistente. Em contrapartida, alguns casos dependem de decisões arquiteturais que precisam ser confirmadas antes de virar testes definitivos.

## Ajustes de prompt que tornam a saída mais útil

1. Informar endpoints, códigos de resposta e comportamentos esperados reduz a invenção de requisitos.
2. Exigir pré-condições, sequência de chamadas, resultado HTTP e estado final transforma sugestões genéricas em casos verificáveis.
3. Pedir explicitamente verificações nos dois componentes evita testar somente a resposta da API.
4. Separar requisitos confirmados de premissas impede tratar decisões ainda não especificadas como fatos.
5. Solicitar dados fictícios e simulação de falhas sem serviços externos facilita a repetibilidade e evita efeitos colaterais.
6. Proibir a invenção de endpoints, mecanismos de retentativa e transações melhora a aderência ao sistema real.

## Limitações e próximos passos

Os casos deste material foram estruturados como uma proposta de suíte. Para concluir a validação prática da atividade, é necessário submeter os prompts a uma LLM, comparar e revisar as saídas, adaptar os casos ao contrato escolhido e executar os testes em uma implementação ou ambiente de integração. Só depois da execução devem ser registrados números de testes aprovados, falhas encontradas e evidências.

A principal conclusão é que os prompts são complementares: o primeiro ajuda a cobrir o contrato; o segundo explora os efeitos da interação entre componentes. A revisão humana continua necessária para verificar se os testes representam requisitos reais e se as asserções estão corretas.
