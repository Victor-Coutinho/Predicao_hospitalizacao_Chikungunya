# Predição de hospitalização em casos de chikungunya

Projeto de aprendizado de máquina desenvolvido com dados públicos do Sistema de Informação de Agravos de Notificação (SINAN), disponibilizados pelo Ministério da Saúde.

O objetivo é comparar diferentes modelos de classificação para prever se um paciente com chikungunya será hospitalizado ou não, utilizando informações demográficas, sintomas iniciais e comorbidades registradas na notificação.

## Objetivo

Construir um modelo de classificação binária capaz de identificar pacientes com maior probabilidade de hospitalização após a confirmação de chikungunya.

A variável-alvo é representada da seguinte forma:

- `0`: paciente não hospitalizado;
- `1`: paciente hospitalizado.

## Fonte dos dados

Os dados utilizados pertencem ao conjunto de casos de febre de chikungunya registrados no SINAN em 2024.

O arquivo pode ser obtido no Portal de Dados Abertos do SUS:

[Sinan — Febre de Chikungunya](https://dadosabertos.saude.gov.br/dataset/arboviroses-febre-de-chikungunya)

Após o download, o arquivo compactado deve ser extraído e renomeado, caso necessário, para:

```text
CHIKBR24.csv
```

## Estrutura do projeto

Para executar o notebook sem alterar os caminhos, organize os arquivos da seguinte forma:

```text
projeto_chikungunya/
│
├── TIC_chikungunya_mesmo_padrao.ipynb
└── CHIKBR24.csv
```

Durante a execução, o notebook gera os seguintes arquivos:

```text
projeto_chikungunya/
│
├── TIC_chikungunya_mesmo_padrao.ipynb
├── CHIKBR24.csv
├── CHIKBR24_processado.csv
├── arvore_decisao_chikungunya.png
└── curva_roc_modelos_chikungunya.png
```

## Variáveis utilizadas

O projeto utiliza informações disponíveis na ficha de notificação de chikungunya.

### Características demográficas

- sexo;
- gestação;
- idade estimada.

As variáveis de sexo e gestação são transformadas em:

- `FEM_GST`: paciente do sexo feminino e gestante;
- `FEM_NAO_GST`: paciente do sexo feminino e não gestante.

Quando ambas possuem valor zero, o registro representa um paciente do sexo masculino.

A idade é estimada pela diferença entre o ano da notificação e o ano de nascimento.

### Sintomas

- febre;
- mialgia;
- cefaleia;
- exantema;
- náusea;
- dor nas costas;
- conjuntivite;
- artrite;
- artralgia;
- dor retro-orbital.

### Comorbidades

- diabetes;
- doenças hematológicas;
- doenças hepáticas;
- doença renal crônica;
- hipertensão;
- doença ácido-péptica;
- doenças autoimunes.

## Pré-processamento

O arquivo é processado em blocos de 100 mil registros para reduzir o consumo de memória.

Durante essa etapa, são aplicados os seguintes procedimentos:

1. Seleção das variáveis utilizadas no projeto.
2. Manutenção apenas de notificações individuais.
3. Remoção de registros com sexo ou gestação ignorados.
4. Manutenção apenas de casos confirmados de chikungunya.
5. Manutenção apenas de registros com informação de hospitalização.
6. Remoção de registros com valores ausentes nas variáveis selecionadas.
7. Conversão dos sintomas e comorbidades para valores binários.
8. Criação das variáveis de sexo, gestação e idade.
9. Criação da variável-alvo de hospitalização.

Ao final do processamento, é criado o arquivo:

```text
CHIKBR24_processado.csv
```

## Divisão dos dados

Os dados são divididos em:

- 70% para treinamento;
- 30% para teste.

A divisão utiliza estratificação para preservar a proporção entre pacientes hospitalizados e não hospitalizados.

A variável `IDADE` é normalizada entre 0 e 1 somente para a regressão logística.

## Modelos utilizados

São comparados quatro algoritmos de classificação:

- Regressão Logística;
- Árvore de Decisão;
- Floresta Aleatória;
- XGBoost.

Para cada modelo, é utilizado o `RandomizedSearchCV` para testar diferentes combinações de hiperparâmetros.

A escolha da melhor combinação é realizada com base no `recall`, pois o objetivo é reduzir a quantidade de pacientes hospitalizados classificados incorretamente como não hospitalizados.

## Tratamento do desbalanceamento

Como a quantidade de pacientes hospitalizados tende a ser menor, o projeto utiliza pesos diferentes para as classes durante o treinamento.

Essa estratégia permite aumentar a importância da classe minoritária sem criar ou remover registros do conjunto de dados.

## Métricas de avaliação

Os modelos são avaliados utilizando:

- acurácia;
- precisão;
- recall;
- F1-score;
- matriz de confusão;
- Curva ROC;
- área sob a Curva ROC.

A classe de maior interesse é a classe `1`, que representa os pacientes hospitalizados.

## Matriz de confusão

A matriz de confusão permite identificar:

- verdadeiros negativos: pacientes não hospitalizados classificados corretamente;
- falsos positivos: pacientes não hospitalizados classificados como hospitalizados;
- falsos negativos: pacientes hospitalizados classificados como não hospitalizados;
- verdadeiros positivos: pacientes hospitalizados classificados corretamente.

No contexto do projeto, os falsos negativos recebem maior atenção, pois representam pacientes hospitalizados que não foram identificados pelo modelo.

## Explicabilidade

A importância das variáveis é analisada de diferentes formas:

- coeficientes da Regressão Logística;
- importância das variáveis na Árvore de Decisão;
- importância das variáveis na Floresta Aleatória;
- importância das variáveis no XGBoost.

Também é gerada uma representação gráfica da melhor Árvore de Decisão.

## Tecnologias utilizadas

- Python;
- Pandas;
- NumPy;
- Scikit-learn;
- Matplotlib;
- Seaborn;
- XGBoost;
- Jupyter Notebook.

## Instalação

Para instalar as dependências principais:

```bash
pip install pandas numpy scikit-learn matplotlib seaborn xgboost jupyter
```

## Execução

Na pasta do projeto, execute:

```bash
jupyter notebook
```

Depois, abra o arquivo:

```text
TIC_chikungunya_mesmo_padrao.ipynb
```

Execute as células em ordem.

## Resultados

Após a execução, os resultados dos modelos podem ser organizados na seguinte tabela:

| Modelo | Precisão | Sensibilidade |
| :--- | :---: | :---: |
| Regressão logística | 5,73% | 75,34% |
| Árvore de decisão | 4,90% | 82,60% |
| Floresta aleatória | 6,22% | 75,09% |
| XGBoost | 4,62% | 90,99% |


## Limitações

A hospitalização não depende exclusivamente da condição clínica do paciente. Ela também pode ser influenciada por fatores como:

- disponibilidade de leitos;
- organização dos serviços de saúde;
- acesso ao atendimento;
- diferenças regionais;
- qualidade do preenchimento das notificações.

Além disso, o projeto utiliza dados administrativos, que podem apresentar informações ausentes, erros de preenchimento e diferenças na forma de registro.

## Uso responsável

Este projeto possui finalidade acadêmica e de aprendizado.

Os modelos não foram validados para uso clínico e não devem ser utilizados para realizar diagnósticos, definir internações ou substituir a avaliação de profissionais de saúde.

## Autor

**Victor Coutinho Santos**

Estudante de Ciência da Computação na Universidade Federal da Bahia e Analista de Dados na Fundação Oswaldo Cruz.
