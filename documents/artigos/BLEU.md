

## Como funciona a métrica BLEU ?

1. **Comparação  de n-gramas:** O BLEU compara n-gramas ([[unigramas, bigramas, trigramas]], etc.) da tradução gerada pelo modelo com n-gramas de uma ou mais traduções de referência.
2. **Cálculo de precisão**: Mede a precisão da tradução ao verificar a proporção de n-gramas da tradução gerada que também aparecem nas referências.
3. **Penalidade por frases curtas ( brevity penalty )**: Penaliza traduções muito curtas, pois uma estratégia trivial para maximizar a precisão seria simplesmente gerar frases curtas que contêm palavras comuns

- **Varia de 0 a 100:** Valorem mais altos indicam melhor **qualidade de tradução**
- Modelos de ponta (por exemplo, baseados em **transformers como o GPT-4 ou NLLB da Meta**) podem atingir BLEU próximo de 30-35.

- **Para referência**:
	- **Humano geralmente atingem BLEU > 40** (dependendo da avaliação)
	- **Sistemas baseados em Redes Neurais Transformer modernos geralmente estão entre 25-35** para esse conjunto de dados.

## BLEU é perfeito ?

<mark class="hltr-r">Não</mark>. O BLEU tem limitações:
- Não considera **sinônimos** ou **estrutura gramatical**, apenas correspondência de n-gramas
- Não avalia a **fluidez** nem a **coerência** da tradução
- Outras métricas como **[[METEOR]], [[ROUGE]], [[TER]] e [[COMET]]** tentam corrigir essas falhas


Se seu modelo atingiu **28,4 BLEU**, ele está em um nível competitivo, mas pode haver espaço para melhorias, especialmente se você quiser otimizar fluidez ou contexto. 🚀

## Como os n-gramas são usados no BLEU?

A métrica BLEU mede **quantos n-gramas da tradução gerada pelo modelo também aparecem na tradução de referência**. Quanto maior a correspondência, maior o score BLEU.

#### Exemplo prático:

#### Tradução gerada pelo modelo:

🚀 `"O carro azul está rápido"`

#### Tradução de referência:

✅ `"O carro azul anda depressa"`

**Comparação de n-gramas:**

- **Unigramas (palavras individuais)**: `"O", "carro", "azul"` aparecem na referência ✅
- **Bigramas (pares de palavras)**: `"O carro" e "carro azul"` aparecem ✅
- **Trigramas (três palavras)**: `"O carro azul"` aparece ✅, mas `"azul está rápido"` ❌ não aparece

Se a maioria dos n-gramas corresponder, o BLEU será alto. Se houver muitas palavras que não aparecem na referência, o BLEU será baixo.

==Se o modelo só acerta palavras individuais (unigramas), mas erra a estrutura da frase, o BLEU penaliza.==

