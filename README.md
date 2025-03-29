# 1. Efeito de diferentes valores de `num_examples` no tamanho do vocabulário

Ao variar o parâmetro `num_examples` na função que carrega e pré-processa os dados de tradução, você controla **quantos pares de sentenças** serão utilizados no treinamento. Isso afeta diretamente:

- **Quantidade de palavras distintas** no idioma de origem.
- **Quantidade de palavras distintas** no idioma de destino.

## 1.2. Exemplos de Saída com Diferentes Valores de `num_examples`

Abaixo estão alguns exemplos de saída do `train_iter` retornado pela função `load_data_nmt` do D2L, variando o parâmetro `num_examples`. Observa-se que os tensores `X` e `Y` representam, respectivamente, as sequências tokenizadas de origem e destino, enquanto `X_valid_len` e `Y_valid_len` indicam o comprimento real de cada sequência (sem contar *padding*).

### 1.2.1. `num_examples=600`

```text
X: tensor([[ 83, 163,   2,   4,   5,   5,   5,   5],
        [ 99,  59,   2,   4,   5,   5,   5,   5]], dtype=torch.int32)
valid lengths for X: tensor([4, 4])
Y: tensor([[100, 171,   6,   2,   4,   5,   5,   5],
        [ 15,   0,   4,   5,   5,   5,   5,   5]], dtype=torch.int32)
valid lengths for Y: tensor([5, 3])
```

### 1.2.2. `num_examples=1000`

```text

X: tensor([[111,   7,   2,   5,   6,   6,   6,   6],
        [248, 134,   2,   5,   6,   6,   6,   6]], dtype=torch.int32)
valid lengths for X: tensor([4, 4])
Y: tensor([[149, 269,  96,   6,   2,   4,   5,   5],
        [195,  35, 320,  86,   2,   4,   5,   5]], dtype=torch.int32)
valid lengths for Y: tensor([6, 6])

```

### 1.2.3. `num_examples=1500`

```text
X: tensor([[161, 194, 376,   2,   5,   6,   6,   6],
        [ 57,  15,   2,   5,   6,   6,   6,   6]], dtype=torch.int32)
valid lengths for X: tensor([5, 4])
Y: tensor([[416, 242,   6,   2,   4,   5,   5,   5],
        [424,   6,   0,   4,   5,   5,   5,   5]], dtype=torch.int32)
valid lengths for Y: tensor([5, 4])
```

### 1.2.4. `num_examples=2000`

```text
X: tensor([[ 70, 269, 199,   2,   5,   6,   6,   6],
        [187, 320, 392,   2,   5,   6,   6,   6]], dtype=torch.int32)
valid lengths for X: tensor([5, 5])
Y: tensor([[ 34, 518,  86,   2,   5,   6,   6,   6],
        [248, 558, 514,   2,   5,   6,   6,   6]], dtype=torch.int32)
valid lengths for Y: tensor([5, 5])
```

Em geral:

- **Quanto maior o `num_examples`**: mais sentenças são lidas, aumentando a chance de encontrar palavras diferentes. Consequentemente, o vocabulário (tanto de origem quanto de destino) tende a **crescer**.
- **Quanto menor o `num_examples`**: menos sentenças são lidas, reduzindo a chance de encontrar palavras raras ou menos frequentes. Assim, o vocabulário tende a **diminuir**.

---

# 2. Tokenização em idiomas sem espaços (chinês, japonês)

Em idiomas como **chinês** e **japonês**, não há separadores de palavras explícitos (como espaços). Nesse contexto, **a tokenização em nível de palavra “tradicional” não é adequada**.

1. **Ausência de delimitadores claros:** Diferentemente de idiomas ocidentais, onde o espaço separa palavras, em chinês e japonês múltiplos caracteres podem representar uma única palavra sem nenhum espaço entre eles.
2. **Ambiguidade na segmentação**: Mesmo usando algoritmos de segmentação (por exemplo, *jieba* para chinês ou *MeCab* para japonês), pode haver casos em que uma sequência de caracteres seja dividida de diferentes maneiras dependendo do contexto.
3. **Alternativas práticas**

   - **Tokenização de subpalavras** (Byte-Pair Encoding, SentencePiece etc.): é uma abordagem que aprende, de forma estatística, os pedaços de texto mais frequentes e lida melhor com idiomas sem espaços.
   - **Tokenização em nível de caractere**: cada ideograma pode ser considerado um token. Embora seja simples, gera sequências muito longas, o que pode dificultar o treinamento.

Por isso, **não** se recomenda a tokenização em nível de “palavra” no estilo ocidental para chinês ou japonês. Em vez disso, costuma-se usar **segmentação especializada** ou **métodos de subpalavras**, que são mais adequados à natureza dessas línguas.

---
