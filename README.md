# FrotaIA — Classificação Inteligente do Estado de Conservação de Frota
![FrotaIA — Capa do projeto](assets/frotaia-capa.png)

## 1. Descrição do projeto

O **FrotaIA** é um MVP desenvolvido para apoiar a gestão da manutenção de uma frota distribuída geograficamente.

A proposta parte de um problema prático: a necessidade de obter um panorama padronizado sobre o estado de conservação dos veículos, sem depender do deslocamento constante de equipes especializadas para inspeções presenciais.

No sistema proposto, mecânicos locais realizam testes simples nos componentes dos veículos, informando se cada componente está funcionando ou não. Essas informações são consolidadas em uma planilha estruturada, na qual os impactos das panes são ponderados com base em critérios definidos por especialistas.

A partir desses dados, o FrotaIA classifica o estado do veículo e gera uma explicação gerencial para apoiar decisões de uso, manutenção, recuperação, aquisição de peças e possível desfazimento.

---

## 2. Objetivo do MVP

O objetivo do MVP é demonstrar uma aplicação funcional que combina:

- regras de negócio;
- modelo de Machine Learning;
- IA Generativa local.

A aplicação busca transformar dados simples de inspeção em uma recomendação compreensível para gestores de frota.

---

## 3. Valor gerencial da solução

O FrotaIA foi desenvolvido com foco na tomada de decisão, e não apenas na construção de um modelo preditivo.

A solução traduz inspeções simples realizadas localmente em informação gerencial estruturada, permitindo que a gestão tenha uma visão mais clara sobre o estado de conservação da frota.

O projeto evidencia o papel da **Estatística Gerencial** na transformação de dados operacionais em apoio prático à decisão. A classificação dos veículos, a identificação dos componentes mais relevantes e a explicação gerencial produzida pela IA ajudam a responder questões como:

- quais veículos podem permanecer em uso;
- quais veículos devem ter o uso restringido;
- quais casos exigem manutenção corretiva ou recuperação complexa;
- quais componentes podem ser priorizados em compras;
- como planejar ações em cenários de restrição orçamentária.

Assim, o valor da solução está em alinhar modelagem estatística, regras de negócio e necessidade do cliente, produzindo uma ferramenta útil para gestão da manutenção, aquisição de peças e planejamento operacional.

---

## 4. Anonimização dos dados

A base utilizada foi anonimizada antes da modelagem.

- Os veículos não são identificados por placa, número real ou localização.
- Os componentes foram codificados como `PEC1` até `PEC43`.
- A identificação original dos veículos foi removida da base utilizada no treinamento.
- O projeto demonstra a funcionalidade da solução sem expor informações sensíveis da frota original.

---

## 5. Entrada de dados

A entrada do sistema é composta pelos resultados dos testes de componentes do veículo.

Cada veículo possui 43 variáveis:

```text
PEC1, PEC2, PEC3, ..., PEC43
```

Os valores seguem a seguinte regra:

| Valor | Interpretação |
|---:|---|
| 0 | componente funcionando corretamente |
| 1 | pane de baixo impacto |
| 2 | pane de impacto moderado |
| 3 | pane de alto impacto |
| 4 | pane de impacto crítico |

Na prática operacional, o mecânico informa o funcionamento dos componentes. A planilha utilizada pela gestão incorpora os pesos de impacto definidos por especialistas.

---

## 6. Classes de saída

| Classe | Interpretação gerencial |
|---|---|
| VD | Veículo livre para uso |
| AM | Veículo em uso com acompanhamento |
| AZ | Uso proibido até avaliação ou manutenção corretiva |
| LAR | Recuperação complexa ou necessidade de avaliação de custo-benefício |
| VM | Indicação preliminar de desfazimento ou recuperação excepcional |

---

## 7. Funcionamento da solução

O FrotaIA utiliza uma abordagem híbrida.

Primeiro, regras de negócio identificam situações críticas, como comprometimento de segurança, presença de componente crítico ou degradação severa.

Em seguida, um modelo **Random Forest** classifica os casos que não foram definidos diretamente pelas regras.

Depois da classificação, uma camada de **IA Generativa local** organiza a saída em uma explicação curta e compreensível para gestores.

---

## 8. Técnica de IA Generativa utilizada

A aplicação utiliza **Engenharia de Prompt** com um modelo generativo local da biblioteca `Transformers`.

Modelo utilizado:

```text
google/flan-t5-small
```

A IA Generativa é usada para transformar a saída técnica do classificador em uma explicação gerencial com:

- classificação final;
- situação do veículo;
- motivo principal;
- ação recomendada;
- observação de cautela.

A solução não utiliza API externa nem chave de acesso.

---

## 9. Arquivos do projeto

```text
FrotaIA/
│
├── FrotaIA_case_portfolio.ipynb
├── base_frota_anonimizada_portfolio.csv
├── README.md
└── requirements.txt
```

---

## 10. Requisitos

O projeto foi desenvolvido para Python 3.12+, preferencialmente no Google Colab.

Bibliotecas utilizadas:

```text
pandas
numpy
scikit-learn
openpyxl
matplotlib
transformers
sentencepiece
torch
```

---

## 11. Instalação

No Google Colab:

```python
!pip install transformers sentencepiece pandas scikit-learn openpyxl matplotlib torch -q
```

Em ambiente local:

```bash
pip install transformers sentencepiece pandas scikit-learn openpyxl matplotlib torch
```

---

## 12. Como executar

1. Abra o notebook `FrotaIA_case_portfolio.ipynb`.
2. Mantenha o arquivo `base_frota_anonimizada_portfolio.csv` na mesma pasta do notebook.
3. Execute as células de instalação e importação das bibliotecas.
4. Execute as células de leitura da base, preparação dos dados, regras de negócio e treinamento do modelo.
5. Execute as células de avaliação, validação cruzada e análise dos erros operacionais.
6. Ao final, execute o exemplo fictício de classificação e a explicação gerencial com IA Generativa.

Exemplo de entrada simulada:

```python
novo_veiculo = {f"PEC{i}": 0 for i in range(1, 44)}

novo_veiculo["PEC6"] = 3
novo_veiculo["PEC10"] = 2

df_novo_veiculo = pd.DataFrame([novo_veiculo])
```

---

## 13. Exemplo de saída

```text
Classificação final:
AZ — Azul

Situação do veículo:
O veículo não deve ser utilizado até passar por avaliação técnica ou manutenção corretiva.

Motivo principal:
Os testes indicaram possível comprometimento de segurança.

Ação recomendada:
Restringir o uso do veículo e priorizar a manutenção corretiva.

Origem da decisão:
Regra de segurança

Observação:
Esta classificação foi gerada pelo sistema FrotaIA com base nos testes informados. Em casos críticos ou excepcionais, a decisão final deve considerar avaliação técnica complementar.
```

---

## 14. Resultados do modelo

| Indicador | Resultado |
|---|---:|
| Acurácia no teste | 0,833 |
| F1 macro no teste | 0,774 |
| F1 ponderado no teste | 0,862 |
| Acurácia média na validação cruzada | 0,786 |
| F1 macro médio na validação cruzada | 0,733 |
| F1 ponderado médio na validação cruzada | 0,811 |

Os resultados indicam desempenho satisfatório para um MVP, especialmente considerando o tamanho reduzido da base e o desbalanceamento entre as classes.

---

## 15. Limitações

O FrotaIA depende da qualidade dos dados informados nos testes.

Algumas situações podem exigir avaliação técnica complementar, como:

- histórico de manutenção;
- avaliação visual;
- obsolescência;
- indisponibilidade de peças;
- decisão patrimonial sobre recuperação ou desfazimento.

Portanto, a aplicação deve ser entendida como ferramenta de triagem e apoio à decisão, não como substituta da avaliação técnica em casos críticos.

---

## 16. Resumo

O FrotaIA demonstra uma aplicação prática de IA Generativa integrada a um classificador híbrido para apoio à gestão da manutenção.

A solução permite:

- transformar inspeções simples em informação gerencial;
- classificar veículos de forma padronizada;
- apoiar decisões de manutenção e aquisição de peças;
- gerar explicações compreensíveis para gestores;
- preservar a anonimização dos dados originais.

Mais do que classificar veículos, o projeto evidencia o papel da **Estatística Gerencial**: transformar dados operacionais em recomendações práticas, alinhadas à necessidade do cliente e ao processo real de decisão.
