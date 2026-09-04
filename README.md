MVP de Engenharia de Dados - Brasileirão ⚽📊

Fala, pessoal! Este repositório contém o MVP (Minimum Viable Product) que desenvolvi para a disciplina de Engenharia de Dados da minha pós-graduação.

A ideia aqui foi sair da teoria e botar a mão na massa construindo um pipeline de dados de ponta a ponta na nuvem.

🎯 O Problema e o Contexto

Sempre ouvimos que "dinheiro não entra em campo", mas até que ponto isso é verdade num campeonato de pontos corridos? O objetivo deste projeto foi cruzar estatísticas puras do jogo (vitórias, gols, saldo) com informações de gestão de elenco (valor de mercado, média de idade, quantidade de estrangeiros) para tentar descobrir o que realmente faz um time pontuar.

Para isso, peguei uma base pública do Kaggle com dados do Campeonato Brasileiro de 2009 a 2018 (extraídos do Transfermarkt).

🏗️ Arquitetura e o Pipeline (Medalhão)

Todo o ambiente foi montado no Databricks (Community Edition), utilizando a arquitetura medalhão para organizar o fluxo do dado:

🥉 Camada Bronze (Ingestão): Fiz o upload do arquivo Tabela_Clubes.csv bruto direto pro DBFS. A ideia é manter o dado original intacto aqui.

🥈 Camada Silver (Limpeza): Usei um notebook com PySpark pra dar aquela faxina geral. A base original tinha alguns problemas: o ano tava defasado, a coluna de gols misturava saldo feito e sofrido (tipo 30:47), e os valores financeiros tavam como texto. Arrumei tudo isso via código, mudei os tipos, corrigi os nomes das colunas e salvei a tabela em formato Delta (pra garantir a performance e a segurança de schema).

🥇 Camada Gold (Análise): Fui pro SQL puro dentro do Databricks para agrupar os times e extrair as respostas de negócio, gerando gráficos direto na plataforma.

🗂️ Governança e Catálogo de Dados

Pra não deixar o dado "jogado", usei o Catalog Explorer do Databricks para documentar a tabela Silver. Aceitei a sugestão de descrição gerada por IA da própria plataforma e adicionei comentários manuais nas colunas de regra de negócio (como Valor_total e Saldo), garantindo que qualquer outra pessoa do time consiga entender a base. (Print do catálogo na pasta de imagens do repositório).

📈 Análises e Insights

Fiz três perguntas principais para a base:

1. O Dinheiro compra resultado em campo?

A resposta é um belo SIM. O gráfico de dispersão mostrou que os times do G4 (1º ao 4º) ostentam médias de valor de elenco lá em cima, na casa dos 50 a 60 milhões. Já os times na zona de rebaixamento amargam elencos mais baratos, na faixa dos 20 milhões. O investimento dita a tendência da tabela.

2. Experiência vs. Juventude: quem entrega mais saldo de gols?

Surpresa aqui! A juventude levou a melhor. Times com média de idade entre 22 e 23 anos conseguiram segurar um saldo de gols médio positivo (fazem mais do que sofrem). Conforme a média de idade passa dos 25 anos, o saldo despenca e fica negativo. Campeonato longo exige fôlego!

3. O Fator Gringo: vale a pena trazer estrangeiros?

Sim, os estrangeiros ajudam a puxar as vitórias pra cima. Mas o interessante é que existe um "ponto ideal". A média de vitórias sobe até atingir o pico (16.2 vitórias) nos times que têm exatamente 4 estrangeiros. Passou de 5, a média dá uma leve recuada.

(Os gráficos comprovando essas análises também estão salvos aqui no repositório!)

🧠 Autoavaliação

Foi um baita desafio sair do zero e ver o pipeline rodando do começo ao fim. Bater cabeça com a limpeza de strings e tipos no PySpark e depois lidar com os bloqueios de segurança do formato Delta (quando o metadata da tabela não bate) foram momentos de muito aprendizado. Ver os gráficos gerando os insights lá no final compensou o esforço. Deu pra entender na prática o valor imenso que uma boa engenharia e limpeza de dados tem antes de qualquer trabalho de análise ou BI.
