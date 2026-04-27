# Estudo-de-Caso-O-Problema-da-Entrega

 Estudo de Caso A1 — O Problema da Entrega Inteligente

**Aluno:** Rodrigo Duarte

---

## Questão 1

### a)

O problema da FastBite pode ser relacionado à classe **NP-Completo**, principalmente porque ele tem ligação direta com problemas clássicos como o **TSP** (Problema do Caixeiro Viajante) e o **VRP** (Problema de Roteamento de Veículos).

Ele não se encaixa em **P**, porque não existe um algoritmo conhecido que resolva esse tipo de problema de forma ótima em tempo polinomial, principalmente quando o número de pedidos e entregadores cresce.

Ao mesmo tempo, ele pode ser associado à classe **NP**, porque, se alguém apresentar uma solução pronta, é possível verificar em tempo polinomial se ela respeita as restrições. Por exemplo, dá para conferir:

- se a capacidade do entregador foi respeitada;
- se os pedidos urgentes ficaram dentro do prazo;
- se a ordem de coleta e entrega está correta;
- se o custo total da rota foi calculado corretamente.

Assim, a melhor forma de classificar no contexto da atividade é dizer que o problema está ligado à família dos **NP-Completos** (ou, de forma mais rigorosa, pode ser visto como **NP-Difícil** quando tratado como problema de otimização).

### b)

De forma intuitiva, o problema da FastBite pode ser reduzido ao **TSP** se fizermos uma simplificação.

No TSP, existe um conjunto de cidades e o objetivo é encontrar a menor rota possível para visitar todas elas. No caso da FastBite, esses pontos seriam os restaurantes e os clientes.

Uma redução simples pode ser pensada assim:

1. considerar apenas um entregador;
2. transformar restaurantes e clientes em pontos de um grafo;
3. usar a distância entre esses pontos como peso das arestas;
4. procurar a menor rota para visitar todos os pontos.

Nessa versão simplificada, o entregador teria que percorrer vários locais minimizando a distância total, o que já se parece bastante com o TSP.

A diferença é que o problema real da FastBite é mais difícil, porque ainda existem restrições adicionais, como:

- coleta antes da entrega;
- múltiplos entregadores;
- capacidade máxima;
- pedidos urgentes;
- tempo de preparo;
- trânsito.

Por isso, na prática, o problema fica ainda mais próximo do **VRP**, que é uma generalização do TSP com várias restrições.

### c)

A força bruta é inviável porque o número de combinações cresce muito rápido. O sistema precisa decidir ao mesmo tempo:

- qual entregador recebe quais pedidos;
- em que ordem cada entregador vai coletar e entregar.

Considerando um cenário com:

- 8 pedidos;
- 3 entregadores;
- cada entregador podendo carregar até 3 pedidos.

Uma distribuição possível seria:

- Entregador 1: 3 pedidos;
- Entregador 2: 3 pedidos;
- Entregador 3: 2 pedidos.

#### Distribuição dos pedidos

Uma estimativa para distribuir os pedidos seria:

```text
C(8,3) * C(5,3) * C(2,2)
```

Calculando:

```text
56 * 10 * 1 = 560
```

Ou seja, existem aproximadamente **560 formas** de distribuir os pedidos nessa configuração.

#### Organização das rotas

Como cada pedido envolve coleta e entrega, então:

- 3 pedidos geram até 6 pontos;
- 2 pedidos geram até 4 pontos.

Assim, uma estimativa para a quantidade de rotas seria:

```text
6! * 6! * 4!
```

Calculando:

```text
720 * 720 * 24 = 12.441.600
```

#### Total aproximado

Multiplicando a distribuição pelas rotas possíveis:

```text
560 * 12.441.600 = 6.967.296.000
```

Ou seja, são quase **7 bilhões de combinações**.

Esse valor ainda é simplificado, porque não considera todas as restrições reais do problema. Como a FastBite precisa tomar decisões em até **2 segundos**, testar tudo por força bruta não é viável em produção.

De forma aproximada, a complexidade cresce de maneira combinatória e fatorial, podendo ser representada por:

```text
O(n!)
```

---

## Questão 2

### a)

O algoritmo guloso funciona em duas etapas:

1. primeiro ele decide qual entregador recebe cada pedido;
2. depois ele monta a rota de cada entregador.

Na primeira etapa, para cada pedido ainda não atribuído, o sistema escolhe o entregador disponível mais próximo do restaurante.

Usando o cenário dado:

- **P1** vai para **E1**, porque ele é o mais próximo do restaurante.
- **P2** também vai para **E1**, porque ele continua sendo o mais próximo.
- **P3** vai para **E2**, porque ele está mais perto do ponto de coleta.
- **P4** também vai para **E2**.
- **P5** iria para **E2**, mas se ele já estiver cheio, pode ir para **E3**.

Assim, uma distribuição provável seria:

| Entregador | Pedidos |
|---|---|
| E1 | P1 e P2 |
| E2 | P3 e P4 |
| E3 | P5 |

Depois disso, cada entregador organiza sua rota escolhendo sempre o próximo ponto mais próximo da posição atual.

### b)

Esse algoritmo é considerado **guloso** porque ele toma a melhor decisão local em cada etapa.

Na atribuição, ele escolhe o entregador mais próximo do restaurante. Na rota, ele escolhe o próximo ponto mais próximo da posição atual.

Esse tipo de escolha é típico de algoritmos greedy: resolver o problema passo a passo, sempre pegando a opção que parece melhor naquele momento.

O problema é que a melhor decisão local nem sempre gera a melhor solução global.

### c)

Um contraexemplo seria um cenário em que:

- **E1** está muito perto de vários restaurantes;
- **E2** está um pouco mais longe dos restaurantes, mas mais bem posicionado para as entregas.

Se o algoritmo olhar apenas a distância até o restaurante, ele pode mandar muitos pedidos para o **E1**, porque ele parece a melhor escolha naquele instante.

Só que isso pode causar:

- sobrecarga no E1;
- atraso em pedido urgente;
- rota total maior;
- E2 ficando pouco aproveitado.

Uma solução melhor poderia ser:

- deixar o pedido urgente com E1;
- mandar os outros pedidos para E2.

Isso mostra que a escolha local do algoritmo guloso pode parecer boa no começo, mas não necessariamente gera o melhor resultado final.

### d)

Na etapa de atribuição, para cada um dos **n pedidos**, o algoritmo compara os **m entregadores** disponíveis.

Então essa etapa possui custo:

```text
O(n * m)
```

Na montagem das rotas, cada entregador escolhe sempre o próximo ponto mais próximo. Esse processo pode ter custo aproximado de:

```text
O(k²)
```

Considerando todos os pedidos, essa etapa pode chegar perto de:

```text
O(n²)
```

Assim, a complexidade geral pode ser representada por:

```text
O(n * m + n²)
```

Essa complexidade é muito mais viável do que a força bruta, por isso o algoritmo guloso pode ser usado em tempo real.

---

## Questão 3

### a)

A Programação Dinâmica pode ser aplicada ao problema de roteamento de **um único entregador**, assumindo que a atribuição dos pedidos já foi feita.

Se esse entregador tiver **k pedidos**, ele terá aproximadamente:

- **k pontos de coleta**;
- **k pontos de entrega**.

Ou seja, cerca de **2k pontos**.

Um subproblema da Programação Dinâmica poderia ser pensado assim:

> Qual é o menor custo para continuar a rota a partir do ponto atual, considerando os pontos que já foram visitados?

Esse raciocínio é parecido com a solução clássica do TSP com memoização.

A complexidade clássica é:

```text
O(n² * 2^n)
```

Como aqui existem aproximadamente **2k pontos**, a complexidade pode ser adaptada para:

```text
O((2k)² * 2^(2k))
```

A memória também cresce bastante:

```text
O(n * 2^n)
```

ou, adaptando:

```text
O((2k) * 2^(2k))
```

Na prática, isso funciona melhor apenas para casos pequenos, como **k = 2, 3 ou 4 pedidos**. Quando o número de pedidos aumenta, por exemplo em torno de **k = 8**, essa abordagem já começa a ficar impraticável para um sistema que precisa responder em até 2 segundos.

### b)

A técnica de **Divisão e Conquista** pode ser aplicada parcialmente ao problema da FastBite.

A ideia seria dividir a cidade em regiões menores, como:

- zonas;
- bairros;
- quadrantes;
- clusters.

Depois disso, o sistema poderia resolver cada região separadamente.

Essa abordagem funciona melhor quando:

- os pedidos estão concentrados em áreas próximas;
- os entregadores também estão próximos dessas áreas;
- existe pouca necessidade de cruzar regiões.

A principal vantagem é reduzir o tamanho do problema, deixando o processamento mais rápido.

Porém, existe uma limitação importante: as **fronteiras entre regiões**.

Pode acontecer de:

- um pedido estar em uma região;
- o melhor entregador estar em outra região.

Se a divisão for rígida demais, o sistema pode tomar uma decisão pior do que a ideal.

Por isso, Divisão e Conquista ajuda bastante como estratégia de organização, mas funciona melhor quando é combinada com heurísticas e refinamento local.

---

## Questão 4

| Critério | Greedy | Programação Dinâmica | Divisão e Conquista |
|---|---|---|---|
| Qualidade da solução | Boa em muitos casos, mas pode ser subótima | Muito alta em casos pequenos | Boa, dependendo da divisão |
| Complexidade de tempo | Baixa a moderada | Alta, com crescimento exponencial | Moderada |
| Complexidade de espaço | Baixa | Alta | Média |
| Viabilidade em tempo real | Alta | Baixa, exceto em casos pequenos | Boa |
| Escalabilidade com aumento de n | Boa | Ruim | Boa |
| Facilidade de adaptação | Alta | Baixa | Média |

A Programação Dinâmica pode produzir soluções melhores em qualidade, mas não é a melhor opção para o sistema principal da FastBite, porque seu custo cresce muito rápido.

A abordagem gulosa é mais simples, rápida e funciona bem em tempo real, mas pode gerar soluções subótimas em alguns cenários.

A Divisão e Conquista ajuda a reduzir o tamanho do problema, principalmente quando a cidade é separada em regiões, mas pode gerar dificuldades nas fronteiras entre zonas.

Por isso, a solução mais adequada seria combinar as abordagens: usar divisão geográfica, aplicar uma heurística gulosa para gerar uma solução inicial e depois fazer refinamentos locais.

---

## Questão 5

### a)

Uma **heurística** é uma estratégia usada para encontrar uma solução boa em pouco tempo, mesmo sem garantir que ela seja a melhor possível.

No caso da FastBite, isso é importante porque o sistema precisa responder rápido. Não adianta encontrar a rota perfeita se o cálculo demorar tanto que o pedido já saia atrasado.

As heurísticas são preferíveis porque:

- reduzem o tempo de processamento;
- funcionam melhor em larga escala;
- se adaptam melhor a mudanças;
- entregam soluções boas dentro do tempo disponível.

Em sistemas reais de delivery, normalmente é melhor uma solução muito boa e rápida do que uma solução perfeita que demora demais.

### b)

Uma solução real para a FastBite poderia funcionar em etapas:

1. **Dividir os pedidos por região geográfica**, reduzindo o tamanho do problema.
2. **Gerar uma solução inicial com heurística gulosa**, atribuindo pedidos com base em distância, capacidade, prioridade e tipo de veículo.
3. **Fazer refinamento local**, com pequenas melhorias, como:
   - trocar pedidos entre entregadores;
   - alterar a ordem de entrega;
   - mover um pedido para outra rota.
4. **Parar no limite de tempo**, por exemplo, em 2 segundos, usando a melhor solução encontrada até aquele momento.

Essa estratégia pode ser resumida assim:

```text
Dividir por região -> gerar solução inicial -> melhorar localmente -> parar no limite de tempo
```

Essa é uma abordagem muito mais realista para um sistema de delivery em larga escala.

### c)

Vale a pena buscar a solução ótima quando o problema é pequeno ou quando existe mais tempo disponível para calcular.

Um exemplo seria um conjunto pequeno de entregas agendadas, com poucos pedidos e apenas um entregador. Nesse caso, como não existe tanta pressão de tempo, calcular a melhor rota pode ser viável.

Mas no fluxo normal da FastBite, com muitos pedidos entrando o tempo todo, isso geralmente não compensa.

---

## Questão 6

Em sistemas de larga escala, muitas vezes uma solução boa o suficiente é a melhor decisão técnica. Isso acontece porque a solução ideal pode ter um custo computacional muito alto e demorar demais para ser útil.

No caso da FastBite, o problema cresce muito rápido conforme aumentam os pedidos e entregadores. Soluções exatas podem até encontrar a melhor resposta, mas não conseguem respeitar o limite de tempo necessário para o sistema funcionar em produção.

Por isso, o engenheiro de software precisa equilibrar qualidade da solução, tempo de resposta, uso de memória e escalabilidade. Uma solução que não é perfeita, mas é rápida e confiável, pode ser melhor do que uma solução ótima que chega tarde demais.

Nesse contexto, "bom o suficiente" não significa fazer de qualquer jeito. Significa escolher uma solução tecnicamente viável, que funcione bem dentro das restrições reais do sistema.

