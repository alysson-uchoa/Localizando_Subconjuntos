# Localizando_Subconjuntos
Dado um valor-alvo que é a soma de alguns registros de uma base, descobre quais combinações de linhas produzem exatamente aquele valor e ajuda a estreitar o resultado usando estatística e pistas de negócio.

É uma ferramenta de reconciliação de dados: você tem o resultado final (um total fechado) e precisa reconstruir o filtro que o gerou, sem saber quais linhas ou qual critério foram usados.

- O problema
Chega uma demanda do tipo:
"Esses R$ 5.063,69 saíram de algumas vendas da base. Quais?"

Sem informação sobre quantas linhas, quais linhas, ou qual filtro foi aplicado. Só o total e a base completa.

Encontrar a resposta na força bruta é inviável: para uma base de n linhas, o número de subconjuntos úteis (excluindo o conjunto vazio e os registros individuais) é: 2ⁿ − 1 − n

Para apenas 28 linhas, isso já são 268.435.427 combinações.

- Como funciona
O método tem três etapas, cada uma respondendo uma pergunta diferente.

1. Enumeração - quais combinações são possíveis?
Usa o algoritmo e divide a base em duas metades, soma todos os subconjuntos de cada lado separadamente e cruza os complementos por um dicionário. O custo cai de 2ⁿ para 2^(n/2) - de centenas de milhões para alguns milhares de operações.

2. Estatística - onde está a informação de verdade?
Sobre o conjunto de soluções encontradas, mede o poder de discriminação de cada valor de cada coluna:
- Atributo perto de 100% (aparece em quase toda solução) é estrutural, é ruído.
- Atributo perto de 50% discrimina, carrega informação.
Combinações raras entre colunas (ex.: um vendedor + um mês específico aparecendo em 1 de N soluções) são pistas de ouro.

3. Pistas de negócio - o filtro final
Cada fragmento de conhecimento (foram poucos vendedores, concentrou em alguns meses, cliente recorrente) vira um filtro aplicado sobre todas as soluções. Empilhando pistas, o espaço encolhe de dezenas para alguns para ser investigado manualmente.

A base precisa ter uma coluna numérica valor e as colunas descritivas que você quer analisar (no exemplo: categoria, data_compra, vendedor, produto, tipo_cliente).

- Formatos de regra
Cada pista é uma função aplicada aos valores daquela coluna na solução:

Objetivo                                Regra
- Quantidade de valores distintos       - lambda s: len(s) <= 2
- Presença de um valor específico       - lambda s: 'mai/26' in s
- Igualdade exata do conjunto           - lambda s: s == {'Recorrente'}

Quanto mais pistas, menor o conjunto.

- Resumo do método
O algoritmo mostra todas as combinações possivéis. A estatística mostra onde está a informação de verdade. As pistas de negócio fecham com o filtro até o que é checável. Sozinha, nenhuma das três resolve, é a sequência que transforma um número solto em poucos conjuntos investigáveis.
