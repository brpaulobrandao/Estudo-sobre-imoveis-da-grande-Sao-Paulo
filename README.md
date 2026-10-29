# Estudo sobre imóveis de São Paulo

Análise de uma base de anúncios de casas na cidade de São Paulo, usada como ponto de partida para dois trabalhos acadêmicos:

- **Estatística Avançada e Fundamentos Probabilísticos para IA** — entender, com evidência estatística, o que explica o preço dos imóveis.
- **Observabilidade de Modelos e Prototipagem de Aplicações de IA** — construir um modelo de preço e monitorar seu comportamento como se estivesse em produção.

> 🚧 Projeto em andamento. Esta página registra a exploração inicial e os achados até agora.

## Estrutura do repositório

```
├── data/
│   └── dados_imoveis.csv          # base original
├── notebooks/
│   └── analise_exploratoria.ipynb # exploração e limpeza
└── README.md
```

## A base

| Coluna | Descrição |
|---|---|
| `Rua` | Endereço do imóvel |
| `Bairro` | Bairro |
| `Cidade` | Cidade |
| `Metragem` | Área em m² |
| `Quartos` | Número de quartos |
| `Banheiros` | Número de banheiros |
| `Vagas` | Vagas de garagem |
| `Valor` | Preço anunciado |

**10.008 anúncios** e **8 colunas**.

## Exploração inicial — o que encontramos até agora

### 1. Toda a base é de São Paulo
A coluna `Cidade` tem um único valor, então não carrega informação e pode ser descartada. Os anúncios se espalham por **701 bairros**, muitos com poucos registros — o que vai exigir cuidado nas comparações entre bairros.

### 2. O preço está armazenado como texto
A coluna `Valor` vem no formato `"R$ 1.350.000"`, e não como número. Antes de qualquer cálculo, é preciso convertê-la.

### 3. A base mistura venda e aluguel
Ao inspecionar os valores, aparecem registros como `"R$ 11.000 /Mês"`. Ou seja, parte dos anúncios é de **aluguel**, não de venda. E não é só aluguel mensal:

| Periodicidade | Anúncios |
|---|---:|
| Sem sufixo (venda) | 9.156 |
| `/Mês` | 848 |
| `/Ano` | 2 |
| `/Dia` | 2 |

Uma busca apenas por "Mês" deixaria passar os casos `/Ano` e `/Dia` — por exemplo, um aluguel de R$ 2.600 por ano seria lido como uma casa vendida por R$ 2.600. Por isso, a identificação usa qualquer valor que contenha `/`.

**Resultado: 9.156 anúncios de venda (91,5%) e 852 de aluguel (8,5%).**

### 4. Venda e aluguel são populações diferentes
Depois de converter os valores para número:

| | Anúncios | Mediana | Média | Mínimo | Máximo |
|---|---:|---:|---:|---:|---:|
| **Venda** | 9.156 | R$ 1.800.000 | R$ 3.052.203 | R$ 13.000 | R$ 65.000.000 |
| **Aluguel** | 852 | R$ 16.000 | R$ 21.219 | R$ 1.650 | R$ 120.000 |

As escalas são completamente diferentes, então os dois grupos precisam ser **analisados separadamente**. Misturá-los distorceria qualquer média, teste ou modelo.

Dois sinais já chamam atenção na venda:
- a **média é bem maior que a mediana**, indicando uma distribuição assimétrica, puxada por imóveis muito caros;
- o **mínimo de R$ 13.000** é incompatível com uma casa em São Paulo e deve ser investigado como possível erro.

## Próximos passos

- [ ] Analisar valores nulos
- [ ] Identificar e remover anúncios duplicados
- [ ] Investigar outliers (metragem, banheiros, vagas e preços impossíveis)
- [ ] Gerar bases limpas de venda e aluguel
- [ ] **Estatística:** preço por m², comparação entre bairros, testes de hipótese e regressão
- [ ] **Observabilidade:** protótipo do modelo de preço e monitoramento de drift e desempenho

## Como rodar

```bash
pip install pandas numpy matplotlib jupyter
jupyter notebook notebooks/analise_exploratoria.ipynb
```