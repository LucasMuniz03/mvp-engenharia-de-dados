# MVP de Engenharia de Dados - Brasileirão ⚽📊

Fala, pessoal! Este repositório contém o MVP (Minimum Viable Product) que desenvolvi para a disciplina de Engenharia de Dados da minha pós-graduação.

A ideia aqui foi sair da teoria e botar a mão na massa construindo um pipeline de dados de ponta a ponta na nuvem.

---

## 🎯 Contexto de Negócios e Perguntas (Etapas 2 e 4.1)

Sempre ouvimos que "dinheiro não entra em campo", mas até que ponto isso é verdade num campeonato de pontos corridos? O objetivo deste projeto foi cruzar estatísticas puras do jogo (vitórias, gols, saldo) com informações de gestão de elenco (valor de mercado, média de idade, quantidade de estrangeiros) para tentar descobrir o que realmente faz um time pontuar.

Para isso, peguei uma base pública do Kaggle com dados do Campeonato Brasileiro de 2009 a 2018 (extraídos do Transfermarkt). A licença dos dados é CC0: Domínio Público (Open Data).

---

## ☁️ Carga dos Dados (Etapa 4.2)

Todo o ambiente foi montado na nuvem utilizando o **Databricks (Community Edition)**. 

A carga inicial (Ingestão) foi feita através do upload do arquivo bruto `Tabela_Clubes.csv` diretamente para o DBFS (Databricks File System) utilizando a interface visual da plataforma.

---

## 🏗️ Pipeline de Dados e Qualidade (Etapas 4.4 e 4.5)

Utilizei a **Arquitetura Medalhão** para organizar o fluxo do dado e concentrei o processo em um Notebook organizado e documentado:

*   **🥉 Camada Bronze (Extract):** Leitura do dado bruto que foi carregado na etapa anterior. A ideia é manter o dado original intacto para rastreabilidade.
*   **🥈 Camada Silver (Transform & Load):** Usei **PySpark** para a faxina geral e tratamento de qualidade. A base original tinha alguns problemas: 
    1. O ano estava defasado (adicionei +1 ao ano).
    2. A coluna de gols misturava saldo feito e sofrido na mesma string, tipo `30:47` (fiz o split criando duas colunas distintas para essas informações). 
    3. Os valores financeiros vieram como texto (fiz o cast para Double e Integer). 
    *Arrumei tudo isso via código, mudei os tipos, corrigi os nomes das colunas e salvei a tabela em formato Delta para garantir a performance, transações ACID e a segurança de schema.*
*   **🥇 Camada Gold (Análises):** Fui pro SQL puro dentro do Databricks para modelar os dados, agrupar os times e extrair as respostas de negócio, gerando gráficos direto na plataforma.

---

## 🗂️ Modelagem e Catálogo de Dados (Etapa 4.3)

A modelagem seguiu o conceito de *Flat Table* no formato Delta. Pra não deixar o dado "jogado", usei o **Catalog Explorer** do Databricks para documentar a tabela Silver. 

Aceitei a sugestão de descrição gerada por IA da própria plataforma e adicionei comentários manuais nas colunas de regra de negócio (como `Media_Valor`, `Saldo` e `Estrangeiros`), garantindo que qualquer outra pessoa do time consiga entender a base.

*(Insira a imagem do Catálogo de Dados arrastando ela para cá)*

---

## 📈 Análise de Dados e Respostas (Etapa 4.5)

Fiz três perguntas principais para a base na camada Gold:

**1. O Dinheiro compra resultado em campo?**
> A resposta é  **SIM**. O gráfico de dispersão mostrou que os times do G4 (1º ao 4º) ostentam médias de valor de elenco lá em cima, na casa dos 50 a 60 milhões. Já os times na zona de rebaixamento tem elencos mais baratos, na faixa dos 20 milhões. O investimento dita a tendência da tabela.

*<img width="1333" height="358" alt="Captura de tela 2026-09-04 210934" src="https://github.com/user-attachments/assets/429d5272-3678-40c0-8d39-76178ae874d4" />*


**2. Experiência vs. Juventude: quem entrega mais saldo de gols?**
>  **A juventude levou a melhor**. Times com média de idade entre 22 e 23 anos conseguiram segurar um saldo de gols médio positivo (fazem mais do que sofrem). Conforme a média de idade passa dos 25 anos, o saldo despenca e fica negativo. Campeonato longo exige fôlego!

*<img width="1332" height="395" alt="Captura de tela 2026-09-04 211024" src="https://github.com/user-attachments/assets/4538ca16-dfb7-45be-96b3-9d3e6fdba331" />*

**3. O Fator Gringo: vale a pena trazer estrangeiros?**
> **Sim**, os estrangeiros ajudam a puxar as vitórias pra cima. Mas o interessante é que existe um "ponto ideal". A média de vitórias sobe até atingir o pico (16.2 vitórias) nos times que têm exatamente 4 estrangeiros. Passou de 5, a média dá uma leve recuada.

*<img width="1334" height="407" alt="Captura de tela 2026-09-04 211130" src="https://github.com/user-attachments/assets/6396f99b-998e-4b04-ac68-60370e40af19" />*

---

## 🧠 Autoavaliação

Foi um baita desafio sair do zero e ver o pipeline rodando do começo ao fim. Bater cabeça com a limpeza de strings e tipos no PySpark e depois lidar com os bloqueios de segurança do formato Delta (quando o metadata da tabela não bateu após eu renomear uma coluna) foram momentos de muito aprendizado. Ver os gráficos gerando os insights lá no final compensou o esforço. Deu pra entender na prática o valor imenso que uma boa engenharia, catálogo e limpeza de dados tem antes de qualquer trabalho de análise ou BI. Como trabalhos futuros, pretendo plugar uma ferramenta externa como o Power BI consumindo esses dados da camada Gold.
