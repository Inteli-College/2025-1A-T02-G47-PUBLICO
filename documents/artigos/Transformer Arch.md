 https://arxiv.org/pdf/1706.03762

Alguns benefícios de acordo com o artigo, fala que essa arquitetura se mostrou superior em quesitos de qualidade, ao mesmo tempo que é mais paralelizável e requer significantemente menos tempo para [[treinar]].


- Métrica **[[BLEU]]** ( Bilingual evaluation Understudy ) é uma das mais comuns para avaliar a qualidade de traduções automáticas. Ela mede o quão próxima a saída do modelo está de uma ou mais traduções de referência, usando n-gramas para comparação. A métrica foi introduzida em 2002 por [[Kishore Papineni]] e sua equipe na IBM

- Na tarefa de tradução de inglês para francês, nosso modelo estabelece uma nova pontuação BLEU de última geração de modelo único de ==41,8 após treinando por 3,5 dias em oito GPUs==


## 🔥 O que significa "Evitar a recorrência" ?

Antes do Transformer, os modelos de tradução automática mais avançados usavam **Redes Neurais Recorrentes (RNNs)** ou **LSTMs (Long Short-Term Memory)**. Esses modelos processavam a entrada **sequencialmente**, ou seja, palavra por palavra, dependendo das informações anteriores. Esse processo tinha algumas limitações:

1. **Dificuldade em capturar relações de longo prazo** — Informações no início da sequência podiam ser esquecidas à medida que o modelo processava mais palavras.
2. **Baixa paralelização** — Como cada palavra dependia das palavras anteriores, as RNNs não podiam ser processadas em paralelo, tornando o treinamento mais lento.

O **Transformer evita esse problema eliminando a recorrência** e **processando todas as palavras da sequência ao mesmo tempo** por meio de ==**self-attention**==.


## 🤖 Como o Transformer funciona?

O Transformer é baseado em um **mecanismo de atenção**, que permite que cada palavra na entrada se conecte diretamente a todas as outras palavras na sequência, sem precisar percorrê-las de maneira sequencial.

### 🔹 Principais componentes:

1. **Self-Attention (Autoatenção)** ⚡
    - Cada palavra na entrada **olha para todas as outras palavras ao mesmo tempo**, calculando **quais palavras são mais importantes** para determinar seu significado.
    - Assim, palavras podem estabelecer conexões globais, mesmo que estejam **distantes na frase**.
    
2. **Mecanismo de atenção multi-head** 🔍
    - O Transformer não usa apenas **uma única "visão"** da entrada, mas **múltiplas "cabeças de atenção"** para entender diferentes relações entre palavras ao mesmo tempo.
    
3. **Positional Encoding** 🌀
    - Como o Transformer não processa palavras sequencialmente, ele precisa de uma forma de saber a ordem das palavras na frase.
    - Ele adiciona uma **codificação de posição** (Positional Encoding) a cada palavra para manter a estrutura da frase.
    
4. **Camadas Feed-Forward e Normalização** 🏗️
    - Depois da self-attention, há camadas totalmente conectadas (**feed-forward**) que ajudam a refinar as representações aprendidas.
    
5. **Paralelização 🚀**
    - Como o Transformer processa todas as palavras **ao mesmo tempo**, ele pode usar muito mais **paralelização** (por exemplo, rodando em GPUs com eficiência), tornando o treinamento **mais rápido** do que modelos baseados em recorrência.


## O que é uma estrutura Encoder-Decoder ?

- Encoder ( Codificador ):
	- Recebe uma sequencia de entrada ( Por exemplo, uma frase em inglês ).
	- Converte essa sequencia em **representações contínuas** ( Vetores ).
	- Essas representações são chamadas de $z = (z1, ...., zn)$, e são como um "resumo" do significado da entrada.

- Decoder ( Decodificador ):
	- Usa essa representação **z** para gerar a **sequência de saída** ( Por exemplo, a tradução para alemão)
	- Ele produz a saída **palavra por palavra**, prevendo um elemento da sequência **com base nos elementos anteriores**



📌 **Exemplo de uma tradução Inglês → Alemão**  

✅ Entrada: `"The cat is sleeping"`  
✅ O **encoder** converte isso em um vetor contínuo **z**.  
✅ O **decoder** gera `"Die Katze schläft"` palavra por palavra, com base em **z**.

## O que significa "o modelo é auto-regressivo" ?

Um modelo **auto-regressivo** significa que **ele gera cada palavra com base nas palavras que já produziu**

- O **decoder não gera todas as palavras de uma vez**, mas **prevê uma de cada vez**, alimentando a próxima previsão com a palavra anterior.


📌 **Exemplo prático (modelo auto-regressivo em ação):**

1️⃣ O modelo começa com um **token especial** `<start>`  
2️⃣ Gera a primeira palavra: `"Die"`  
3️⃣ Usa `"Die"` como entrada e prevê a próxima palavra: `"Katze"`  
4️⃣ Usa `"Die Katze"` e prevê `"schläft"`  
5️⃣ Continua até gerar toda a saída ou encontrar um **token de parada** `<end>`

Isso ajuda o modelo a garantir que a saída faça sentido gramaticalmente.

- Modelos auto-regressivos permitem que a saída seja **contextual**, ou seja, a escolha de cada palavra influencia as próximas.
- No entanto, esse processo também **impede paralelização**, porque cada palavra depende da anterior.

Por isso, **o Transformer substitui esse processo** por um **mecanismo de atenção**, permitindo processar tudo em paralelo! 🚀🔥


![[Pasted image 20250214122916.png]]



## Arquitetura

![[Pasted image 20250214123003.png]]

A arquitetura do **Transformer** funciona em camadas empilhadas (N = 6) para capturar relações complexas entre as palavras. Cada camada tem **sub-módulos** responsáveis por processar a informação.

### **🔷 1. Encoder (lado esquerdo da imagem)**

- O **encoder** recebe uma sequência de entrada e a transforma em **vetores de contexto** que representam a estrutura e significado da frase.
- Ele é composto por **6 camadas idênticas**, cada uma contendo:
    1. **Multi-Head Self-Attention** – Permite que cada palavra se relacione com todas as outras palavras da entrada.
    2. **Feed-Forward Layer** – Rede totalmente conectada que processa as informações das atenções.
    3. **Residual Connection & Layer Normalization** – Mantém o fluxo de informação estável.

📌 **Positional Encoding:** Como o Transformer **não é recorrente**, ele adiciona informações de posição para que o modelo saiba a ordem das palavras.

---

### **🔷 2. Decoder (lado direito da imagem)**

- O **decoder** recebe a saída do encoder e gera a sequência de saída palavra por palavra.
- Também possui **6 camadas idênticas**, mas com um componente extra:
    1. **Masked Multi-Head Attention** – Funciona como o self-attention do encoder, mas impede que o modelo veja palavras futuras. Isso garante que a previsão para a palavra **i** só dependa das palavras anteriores.
    2. **Multi-Head Attention** (normal) – Atenção sobre a saída do encoder, permitindo que o decoder use informações do contexto de entrada.
    3. **Feed-Forward Layer** – Rede totalmente conectada que processa as informações da atenção.
    4. **Residual Connection & Layer Normalization** – Como no encoder.

📌 **Shifted Output (Deslocamento da saída)** – Para evitar que o modelo veja a palavra certa antes de gerá-la, a saída é deslocada (shifted right), ou seja, cada previsão só vê palavras passadas.

---

### **🔷 3. Como funciona a geração da saída?**

1. O modelo recebe a frase de entrada (ex.: `"The cat is sleeping"`).
2. O encoder processa e gera representações contextuais.
3. O decoder recebe a saída deslocada (ex.: `"<start> Die Katze"`).
4. O decoder prevê a próxima palavra (`"schläft"`), sem ver as palavras futuras.
5. Esse processo continua até prever `"<end>"`, indicando o fim da frase.

📌 No final, um **softmax** é aplicado para obter probabilidades sobre o vocabulário, e a palavra mais provável é escolhida.

---

### **🔎 Resumo final**

✅ O **encoder** processa a entrada usando **self-attention e redes feed-forward**.  
✅ O **decoder** gera palavras **uma por vez**, usando **máscaras para não olhar o futuro**.  
✅ O modelo usa **positional encoding** porque **não tem recorrência**.  
✅ O **softmax** escolhe a palavra mais provável em cada passo.


## Por que autoatenção ?

### Complexidade computacional

| **Camada**             | **Complexidade por camada**                          |
| ---------------------- | ---------------------------------------------------- |
| Self-Attention         | **O(n²·d)** (depende da sequência n e da dimensão d) |
| Recorrência (RNN/LSTM) | **O(n·d²)** (custo cresce linearmente com n)         |
| Convolução (CNN)       | **O(k·n·d + n·d²)** (depende do tamanho do kernel k) |

- **Self-Attention** é mais eficiente que [[RNNs]] quando n < d ( o que é comum em PLN).
- **CNNs** podem ter menos complexidade, mas precisam de várias camadas para cobrir toda a entrada.
- Self-Attention pode ser ajustada para melhorar o desempenho em sequências longas.


### Paralelização

| **Camada**             | Número de operações sequenciais                                                    |
| ---------------------- | ---------------------------------------------------------------------------------- |
| Self-Attention         | **O(1)** (todas as palavras interagem simultaneamente)                             |
| Recorrência (RNN/LSTM) | **O(n)** (processa palavra por palavra, sequencialmente)                           |
| Convolução (CNN)       | **O(n/k)** (usa múltiplos filtros simultâneos, mas ainda precisa empilhar camadas) |

- [[RNNs]] precisam processar cada palavra sequencialmente, dificultando o paralelismo.
- [[CNNs]] podem ser mais paralelizáveis que RNNs, mas ainda precisam de múltiplas camadas para capturar todo o contexto.
- Self-Attention permite computação completamente paralela, acelerando significativamente o treinamento em GRUs e [[TPUs]]

### Captura de dependência de longo alcance

| **Camada**             | Máximo comprimento do caminho                                                 |
| ---------------------- | ----------------------------------------------------------------------------- |
| Self-Attention         | **O(1)** (todas as palavras interagem diretamente)                            |
| Recorrência (RNN/LSTM) | **O(n)** (o sinal precisa passar por todas as palavras anteriores)            |
| Convolução (CNN)       | **O(n/k) ou O(logₖ(n))** (depende do tamanho do kernel e convolução dilatada) |

- **Self-Attention conecta todas as palavras diretamente**,  tornando mais fácil capturar **relações de longo prazo**
- Em **RNNs/LSTMs**, a informação precisa percorrer toda a sequência, o que causa dificuldade com frases longas.
- **CNNs** podem melhorar isso, mas precisam de múltiplas camadas para cobrir grandes distâncias.


![[Pasted image 20250214135341.png]]



## WMT e Treinamento

Workshop on Machine Translation, um evento anual onde pesquisadores testam seus modelos em benchmarks de tradução.

### Treinamento

Antes de treinar o modelo, as frases foram **convertidas para tokens numéricos** usando **Byte-Pair Encoding (BPE)**.

🔹 **O que é Byte-Pair Encoding (BPE)?**

- Um método de **tokenização subpalavra** que divide palavras em partes menores, ajudando o modelo a lidar com palavras desconhecidas.
- Funciona **aprendendo padrões frequentes em palavras e criando tokens reutilizáveis**.

📌 **Exemplo de BPE:**  
A palavra **"unhappiness"** pode ser dividida em **"un", "happiness"**, e depois "happiness" pode ser dividida em **"happi" + "ness"**. Isso ajuda a lidar com palavras novas e morfologicamente complexas.

🔹 **Vocabulário treinado:**

- **Inglês-Alemão** → 37.000 tokens
- **Inglês-Francês** → 32.000 tokens

📌 **Por que BPE é útil?**  
✅ Resolve problemas com palavras desconhecidas (OOV - Out of Vocabulary).  
✅ Melhora a eficiência do treinamento.  
✅ Reduz o tamanho do vocabulário, tornando o modelo mais leve.

####  **Organização dos Dados para Treinamento (Batching)**

🔹 **O que é um batch?**

- No treinamento de redes neurais, os dados são processados **em lotes (batches)**, em vez de treinar com uma sentença por vez.

🔹 **Como os batches foram organizados?**

- As frases foram agrupadas por **tamanho aproximado** para otimizar a eficiência computacional.
- **Cada batch continha aproximadamente 25.000 tokens de origem e 25.000 tokens de destino.**

📌 **Por que isso é importante?**  
✅ **Batching eficiente melhora o uso da memória da GPU.**  
✅ **Agrupar sentenças por tamanho reduz desperdício de computação.**  
✅ **Ajuda a treinar modelos mais rápido e de forma mais estável.**


## Regularização


Regularização é um conjunto de técnicas utilizadas para **evitar overfitting** (quando o modelo aprende muito bem os dados de treino, mas não generaliza bem para novos dados).


Nesta seção, o **Transformer** usa **três tipos principais de regularização**:

1️⃣ **Dropout Residual**  
2️⃣ **Label Smoothing**  
3️⃣ **Comparação com outros modelos em BLEU e custo computacional (FLOPs)**


### **1️⃣ Residual Dropout 🛑**

🔹 **O que é Dropout?**

- Dropout é uma técnica que **"desliga" aleatoriamente alguns neurônios** durante o treinamento para evitar que o modelo fique muito dependente de certas conexões.

🔹 **Como é aplicado no Transformer?**

- **Em cada subcamada do Transformer**, antes da normalização.
- **No embedding e no positional encoding** (no encoder e no decoder).
- **Taxa de dropout = 0.1 (10%)** para o modelo base.

📌 **Por que isso é útil?**  
✅ Evita **overfitting**, tornando o modelo mais robusto.  
✅ Ajuda a melhorar a **generalização** para novos textos.


### **2️⃣ Label Smoothing 🔄**

🔹 **O que é Label Smoothing?**

- Normalmente, modelos de PLN aprendem que a palavra correta tem **100% de probabilidade** (ex.: um modelo treinado para prever `"cachorro"` pode dar 100% de certeza para essa palavra).
- **Label Smoothing suaviza isso**, atribuindo **pequena probabilidade para outras palavras similares**.

🔹 **Como foi aplicado?**

- O Transformer usa **ϵ_ls = 0.1**, ou seja, em vez de dar **100% de certeza para a palavra correta**, ele redistribui **10%** para palavras similares.

📌 **Por que isso é útil?**  
✅ Evita que o modelo fique **muito confiante e rígido** nas previsões.  
✅ Melhora **BLEU score e acurácia** na tradução.  
✅ Pode ajudar na **generalização**, tornando o modelo mais flexível para palavras sinônimas.

📌 **Desvantagem**:

- Aumenta a **perplexidade** (o modelo parece menos confiante), mas melhora o desempenho geral.


### **3️⃣ Comparação do Transformer com outros modelos (BLEU e FLOPs)**

A tabela mostra como o Transformer supera os modelos anteriores **com menor custo computacional**.

📌 **Métricas importantes na tabela:**

- **BLEU Score** → Mede a qualidade da tradução (quanto maior, melhor).
- **Training Cost (FLOPs)** → Mede o custo computacional (quanto menor, melhor).

|**Modelo**|**BLEU (EN-DE)**|**BLEU (EN-FR)**|**Custo Computacional (FLOPs)**|
|---|---|---|---|
|ByteNet|23.75|-|-|
|GNMT + RL|24.6|39.92|2.3 × 10¹⁹|
|ConvS2S|25.16|40.46|9.6 × 10¹⁸|
|MoE|26.03|40.56|2.0 × 10¹⁹|
|Transformer (Base)|**27.3**|38.1|**3.3 × 10¹⁸**|
|Transformer (Big)|**28.4**|**41.8**|**2.3 × 10¹⁹**|

🔹 **Por que o Transformer se destaca?**  
✅ **BLEU Score mais alto** → Melhor qualidade de tradução.  
✅ **Menor custo computacional** → Treina mais rápido e com menos recursos.  
✅ **Menos tempo de treinamento** → Apenas **12h no modelo base e 3,5 dias no modelo grande**.

