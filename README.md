# Análise de Microdados Enem 2023

**Autor:** Danielle Rocha Macedo da Silva

**Data:** 21/12/2025


**Matricula:**

**Dataset:** [Microdadodos Enem 2023](https://www.gov.br/inep/pt-br/acesso-a-informacao/dados-abertos/microdados/enem)

## Objetivo
Esse projeto tem como objetivo constuir um pipeline de dados usando como ferramenta o Databricks Free Edition. As etapas desse pipeline são: buscar, coletar, tratar, fazer a carga, modelar e analisar os microdados do Enem 2023 para que seja possível analisar os dados e responder algumas perguntas sobre a demografia dos participantes e a influência de fatores socieconômico no desempenho nas provas.

As perguntas a serem respondidas são: 

**1) Quantidade de inscritos:**
- **A)** Total;
- **B)** Faixa etária;
- **C)** Por sexo;
- **D)** Por raça;
- **E)** Tipo de escola de origem (rede pública e privada);
- **F)** Por estado;
- **G)** Por renda familiar

**2) Quantidade de faltantes e perfil dos faltantes.**
- **A)** Total;
- **B)** Faixa etária;
- **C)** Por sexo;
- **D)** Por raça;
- **E)** Tipo de escola de origem (rede pública e privada);
- **F)** Por estado;
- **G)** Por renda familiar


**3) Notas por UF.**

**4) Análise das média das disciplinas considerando fatores os socioeconômicos:**
- **A)** Nível de educação dos pais
- **b)** Renda

**5) Perfil dos participantes que tiraram mil na redação.**


#Detalhamento
####1. Busca pelos dados
Com base nas sugestões oferecidas na Disciplina MVP, os dados utilizados nesse projetos foram encontrados no site do GOV, mas especificamente no portal do INEP.

####2. Coleta
Conforme mencionado, os dados utilizados nesse projeto são compostos pelos microdados do Exame Nacional do Ensino Médio (ENEM) referentes ao ano de 2023, que são disponibilizados publicamente no site do Instituto Nacional de Estudos e Pesquisas Educacionais Anísio Teixeira (INEP).

Os dados foram coletados a partir do download manual do arquivo CSV com os microdados,uma vez que se tratam de públicos e estruturados. Os arquivos baixados incluem os microdados, com assim como a [documentação](https://dbc-584c76cc-ac43.cloud.databricks.com/editor/files/4433784861577730?o=3173063251617445) e o [dicionário de dados](/Workspace/Users/daniellerocha21@gmail.com/MVP_Engenharia_de_Dados_PUC_Rio_202502/Dicionário_Microdados_Enem_2023.xlsx) referente ao ano analisado.

Após o download dos arquivos, o CSV com os microdados foi armazenado na plataforma Databricks, carregado para o sistema de arquivos distribuído (DBFS) da plataforma e a partir disso foi criada a tabela bronze, com os dados brutos a serem usados no projeto.


####3. Modelagem
O modelo escolhidos para estruturar os dados deste MPV foi o Esquema Estrela.

#####   3.1 Estrutura do Esquema Estrela
    - Tabela Fato: `gold.fato_enem2023`
    - Tabelas Dimensão: `gold.dim_local` e `gold.dim_candidato` 

#####   3.2 Catálogo de Dados

     - Tabela `gold.fato_enem2023` 
     
        Essa tabela é a parte central do model estrela. Nela, estão presentes as notas dos candidatos em cada um das provas, assim como o indicador de presença nas provas. Ela se relaciona com `gold.dim_candidato` e `gold.dim_local` a partir de chaves estrageiras.

| PK/FK  | Nome da Coluna | Descrição | Datatype | Tamanho | Domínio | Relacionamento |
|-----|---------------|-----------|---------|---------|------------------|---------------|
|   | `NU_INSCRICAO` | chave única com base na inscrição do candidato | bigint | 12 caracteres| Identificador único | - |
| FK | `SK_CANDIDATO` | Chave Estrageira que se conecta a tabela `gold.dim_candidato`  | bigint | 8 bytes | Números inteiros em sequência, que foram  ferados pela função `monotonically_increasing_id()`. Exemplos: 0, 1, 2, 3, etc. | `gold.fato_enem2023.SK_CANDIDATO` =  `gold.dim_candidato.SK_CANDIDATO` |
| FK | `SK_LOCAL` | Chave Estrageira que se conecta a tabela `gold.dim_local`  | bigint | 8 bytes | Números inteiros em sequência, que foram  ferados pela função `monotonically_increasing_id()`. Exemplos: 0, 1, 2, 3, etc. | `gold.fato_enem2023.SK_LOCAL` =  `gold.dim_candidato.SK_LOCAL` |
|   | `NOTA_CN` | Nota na prova de Ciências da Natureza. | double | 8 bytes| Valores decimais. Exemplos: 0, 543.2, sendo 0 o valor mínimo e 868.4 o valor máximo no ano de 2023| - |
|   | `NOTA_CH` | Nota na prova de Ciências Humanas. | double | 8 bytes| Valores decimais. Exemplos: 0, 784.3, sendo 0 o valor mínimo e 823 o valor máximo no ano de 2023| - |
|   | `NOTA_LC` | Nota na prova de Linguagens e Códigos. | double | 8 bytes| Valores decimais. Exemplos: 0, 543.2, sendo 0 o valor mínimo e 820.8  o valor máximo no ano de 2023| - |
|   | `NOTA_MT` | Nota na prova de Nota em Matemática. | double | 8 bytes| Valores decimais. Exemplos: 0, 843.4, sendo 0 o valor mínimo e 958.6 o valor máximo no ano de 2023| - |
|   | `NOTA_REDACAO` | Nota na prova de Nota em Matemática. | double | 8 bytes| Valores decimais. Exemplos: 0, 920, sendo 0 o valor mínimo e 1000 o valor máximo| - |
|   | `PRESENCA_CN` | Indicador de presença em CN. | string | Variável (até 10 caracteres)| Ex: 'Presente', 'Faltou', 'Eliminado'.| - |
|   | `PRESENCA_CH` | Indicador de presença em CH. | string | Variável (até 10 caracteres)| Ex: 'Presente', 'Faltou', 'Eliminado'.| - |
|   | `PRESENCA_LC` | Indicador de presença em LC. | string | Variável (até 10 caracteres)| Ex: 'Presente', 'Faltou', 'Eliminado'.| - |
|   | `PRESENCA_MT` | Indicador de presença em MT. | string | Variável (até 10 caracteres)| Ex: 'Presente', 'Faltou', 'Eliminado'.| - |
|   | `TP_STATUS_REDACAO` | Indicador de presença em MT. | string | Variável (até 30 caracteres)| Ex: 'Sem problemas', 'Anulada', 'Cópia Texto Motivador', 'Em Branco'.| - |



- Tabela `gold.dim_candidato` 
   Essa tabela traz as principipais caracteristicas dos cadidatos, tanto demograficas quanto socioeconômicas. 

| PK/FK  | Nome da Coluna | Descrição | Datatype | Tamanho | Domínio | Relacionamento |
|-----|---------------|-----------|---------|---------|------------------|---------------|
| PK  | `SK_CANDIDATO` | Chave Substituta (Surrogate Key) criada para identificar cada cadidato de forma única. | bigint | 8 bytes | Números inteiros em sequência, que foram  ferados pela função `monotonically_increasing_id()`. Exemplos: 0, 1, 2, 3, etc | `gold.dim_candidato.SK_CANDIDATO` = `gold.fato_enem2023.SK_CANDIDATO` |
|     | `CANDIDATO_KEY` | Chave de Negócio gerada a partir da junção de todas as colunas de dimensão. | string | 64 caracteres | São valores em string de 64 caracteres em hexadecimal. Exemplo: 9d1c9e82f7e0a2b4c6d8e0f2a4b6c8d0e2f4a6b8c0d2e4f6a8b0c2d4e6f8a0b1'. | - |
|     | `FAIXA_ETARIA` | Faixa etária do candidato. | string | variável (até 18 caracteres) | Exemplos: 'Entre 56 e 60 anos'. | - |
|     | `SEXO` | Sexo do candidato. | string | Variável (até 9 caracteres) | 'Feminino'e 'Masculino' | - |
|     | `COR_RACA` | Cor ou Raça declarada pelo candidato. | string | variável (até 8 caracteres) | Exemplos: 'Branca', 'Parda', 'Indígena', etc. | - |
|     | `TIPO_ESCOLA` | Tipo de escola onde cursou o Ensino Médio. | string |12 caracteres | Exemplos: 'Pública', 'Privada' e 'Não Respondeu'. | - |
|     | `SG_UF_PROVA` | Estado onde foi realizada a prova. | string | 2 caracteres | Exemplos: Ex: 'SP', 'RJ', 'MG', etc. | - |
|     | `ESCOLARIDADE_PAI` | Nível de escolaridade do pai. | string | variável (8 a 87 caractes) | Exemplos: 'Completou a 4ª série/5º ano, mas não completou a 8ª série/9º ano do Ensino Fundamental' e 'Nunca estudou'. | - |
|     | `ESCOLARIDADE_MAE` | Nível de escolaridade da mãe. | string | variável (8 a 87 caractes) | Exemplos: 'Completou a 4ª série/5º ano, mas não completou a 8ª série/9º ano do Ensino Fundamental' e 'Nunca estudou'. | - |
|     | `RENDA_FAMILIAR` | Faixa de renda familiar mensal. | string | variável(até 33 caracteres) | Exemplos: 'Nenhuma Renda', 'De R$ 2.640,01 até R$ 3.300,00'. | - |



- Tabela `gold.dim_local`
Essa tabela contém as UF da federação e uma chave SK_LOCAL que se realciona com a tabela fato. 

| PK/FK  | Nome da Coluna | Descrição | Datatype | Tamanho | Domínio | Relacionamento |
|-----|---------------|-----------|---------|---------|------------------|---------------|
| PK  | `SK_LOCAL` | Chave Substituta (Surrogate Key) criada para identificar cada local de forma única. | bigint | 8 bytes | Números inteiros em sequência, que foram  ferados pela função `monotonically_increasing_id()`. Exemplos: 0, 1, 2, 3, etc | `gold.dim_candidato.SK_LOCAL` = `gold.fato_enem2023.SK_LOCAL`
|     | `SG_UF_PROVA` | Estado onde foi realizada a prova. | string | 2 caracteres | Exemplos: Ex: 'SP', 'RJ', 'MG', etc. | - |



##### 3.3 Diagrama Entidade Relacionamento

![Diagrama Entidade Relacionamento](Modelo ER.drawio.png)
    
#### 4. Carga
A etapa de carga dos dados para o Data Warehoure/ Data Lake foi realizada no Notebook **01 - Ingestão de Dados**, onde foi realizada a carga inicial dos dados bruto (camada bronze).

A etapa de transformação dos Dados foi realizada no Notebook **02 - Transformação dos Dados**, na qual foram realizadas as ações de limpeza, padronização, tratamento de tipos, mapeamento de códigos e criação da camada Silver.

A etapa de carga foi realizada no no Notebook **03 - Tabelas Gold**, onde foram criadas as tabelas fato e dimensão na camada Gold.


#### 5. Análise

##### a. Qualidade de dados
 A etapa de analise de cada atributo do conjuto de dados no que diz respeiro a qualidade dos dados foi realizada no Notebook **02 - Transformação dos Dados**

##### b. Solução do problema
A solução do problema foi realizada no notebook **04 - Solução do Problema**, onde foram feitas as análises com o intuito de responder as perguntas levantadas no objetivo deste projeto.


#### 6. Autoavaliação


    

