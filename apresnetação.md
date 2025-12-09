# Apresentação do Experimento: Low-Code vs. High-Code

## 1. Contexto e Problema
Quando falamos sobre solução de problemas a partir de sistemas, as organizações enfrentam atualmente um dilema estratégico: a necessidade de acelerar o desenvolvimento utilizando plataformas **Low-Code** versus a incerteza técnica sobre a capacidade dessas ferramentas suportarem alta demanda. Embora o Low-Code prometa agilidade, existe o risco do custo de performance ("taxa de abstração") inviabilizar sua aplicação em sistemas críticos de acesso em massa. O experimento surge para substituir suposições por dados técnicos concretos sobre desempenho e escalabilidade.

## 2. Objetivos e Métricas (GQM)
O objetivo central é analisar comparativamente a degradação de performance entre uma solução Low-Code e uma solução tradicional em Node.js (High-Code). Abaixo, a matriz detalhada de Objetivos, Perguntas e Métricas definida para o estudo:

| Objetivo Específico | Perguntas Chave (Questions) | Métricas (Metrics) |
| :--- | :--- | :--- |
| **(O1) Analisar Latência**<br>Determinar o tempo de resposta percebido pelo cliente. | **Q1:** Qual é a latência basal em condições normais?<br>**Q2:** Como a latência se comporta para os 5% piores casos (cauda)? | **M01:** Tempo Médio de Resposta (ART)<br>**M03:** Latência p95 (Percentil 95)<br>**M05:** Fator de Degradação |
| **(O2) Avaliar Vazão**<br>Identificar o volume máximo de transações suportado. | **Q1:** Qual o máximo de requisições/segundo sustentável?<br>**Q2:** Em que ponto o sistema satura? | **M06:** Max Throughput (RPS)<br>**M08:** Ponto de Saturação (Usuários Simultâneos) |
| **(O3) Eficiência**<br>Mensurar o custo computacional da solução. | **Q1:** Qual o consumo de CPU para sustentar 500 usuários?<br>**Q2:** Existe vazamento de memória? | **M10:** Utilização Média de vCPU (%)<br>**M11:** Consumo de Memória RAM (MB) |
| **(O4) Confiabilidade**<br>Verificar estabilidade sob estresse. | **Q1:** Qual a taxa de falhas sob carga máxima?<br>**Q2:** Ocorrem timeouts de conexão? | **M14:** Taxa de Erro (%)<br>**M16:** Contagem de Timeouts |

## 3. Modelo Conceitual e Hipóteses
### 3.1 Modelo Conceitual (A Premissa da "Taxa de Abstração")
* **Camadas Adicionais:** O modelo assume que plataformas Low-Code possuem um custo de performance inerente devido à existência de camadas extras de software (interpretadores de modelos visuais, *runtimes* proprietários e frameworks genéricos), gerando *overhead* computacional.
* **Comparativo de Arquitetura:**
    * **High-Code (Node.js):** Fluxo direto e otimizado (Entrada $\rightarrow$ Runtime V8 $\rightarrow$ Lógica $\rightarrow$ Saída).
    * **Low-Code:** Fluxo com intermediários (Entrada $\rightarrow$ Motor de Interpretação $\rightarrow$ Tradução do Modelo Visual $\rightarrow$ Lógica $\rightarrow$ Saída).
* **Resultado Esperado:** A arquitetura do Low-Code deve resultar em maior consumo de ciclos de CPU/Memória, levando a uma latência mais alta e saturação precoce (menor vazão) conforme a concorrência aumenta.

### 3.2 Hipóteses Formais de Teste ($H_1$ - Alternativas)
As hipóteses buscam confirmar a direção da diferença de desempenho entre Low-Code (LC) e High-Code (HC):

**Para Latência (O1):**
* **$H_{0_L}$ (Nula):** Não há diferença estatisticamente significativa na latência média entre as soluções. ($\mu_{LC\_lat} = \mu_{HC\_lat}$)
* **$H_{1_L}$ (Alternativa):** A latência média da solução Low-Code é superior à do High-Code. ($\mu_{LC\_lat} > \mu_{HC\_lat}$)

**Para Vazão/Throughput (O2):**
* **$H_{0_T}$ (Nula):** Não há diferença significativa no throughput máximo (RPS) suportado. ($\mu_{LC\_rps} = \mu_{HC\_rps}$)
* **$H_{1_T}$ (Alternativa):** O throughput máximo da solução Low-Code é inferior ao do High-Code. ($\mu_{LC\_rps} < \mu_{HC\_rps}$)

**Para Eficiência de CPU (O3):**
* **$H_{0_E}$ (Nula):** O consumo de CPU é equivalente para processar a mesma carga de trabalho. ($\mu_{LC\_cpu} = \mu_{HC\_cpu}$)
* **$H_{1_E}$ (Alternativa):** A solução Low-Code consome significativamente mais CPU para processar a mesma carga. ($\mu_{LC\_cpu} > \mu_{HC\_cpu}$)


**Para Confiabilidade (O4):**
* **$H_{0_E}$ (Nula):** Não existe uma diferença notavel em falhas entre as duas implementações 
* **$H_{1_E}$ (Alternativa):** A solução Low-Code possui uma taxa maior de falhas do que a solução High-Code
 
## 4. Objetos de Estudo e Variáveis Controladas
O experimento comparará dois artefatos de software distintos que implementam **exatamente a mesma regra de negócio**: uma API de consulta de clientes com filtros e paginação. 

### 4.1 Variáveis independentes (fatores) e seus níveis
Este é um experimento fatorial com dois fatores principais controlados:
* **Fator A: Tecnologia (Variável Qualitativa Nominal)**
    * Nível 1: Plataforma Low-Code.
    * Nível 2: High-Code (Node.js).
* **Fator B: Carga de Concorrência (Variável Quantitativa Ordinal)**
    * Nível 1: Carga Baixa (10 VUs).
    * Nível 2: Carga Média (100 VUs).
    * Nível 3: Carga Alta (500 VUs).
    * Nível 4: Estresse (1.000 VUs).
 
  ### 4.2 Variáveis dependentes (respostas)
As medidas de resultado observadas diretamente dos instrumentos:
1.  **Latência (Response Time):** Tempo total de resposta em milissegundos (ms).
2.  **Vazão (Throughput):** Taxa de Requisições por Segundo (RPS).
3.  **Taxa de Erro:** Porcentagem de códigos de status HTTP não-200 (5xx, 4xx).
4.  **Consumo de Recursos:** Porcentagem de uso de vCPU e Megabytes de Memória RAM (coletados via agente de monitoramento no servidor).

### 4.3 Variáveis de controle / bloqueio
Fatores mantidos constantes para evitar ruído nos dados e garantir comparabilidade:
* **Hardware do Servidor:** Mesma família de instância (ex: AWS t3.medium) e limites de container (CPU/RAM) para ambos.
* **Banco de Dados:** Mesma instância RDS PostgreSQL, mesmos índices, mesma massa de dados (100k registros).
* **Localização de Rede:** Ambos os testes rodam na mesma região (ex: us-east-1) e mesma VPC.
* **Tamanho do Payload:** O JSON de resposta terá estrutura e tamanho idênticos (~2KB) em ambas as soluções.

## 5. Infraestrutura e Ferramentas
Para garantir um ambiente controlado e isolado, utilizaremos a infraestrutura de nuvem da AWS. O monitoramento será feito via **Datadog** (para métricas de CPU/RAM) e a geração de carga via **k6**, uma ferramenta robusta para simulação de usuários. A paridade de recursos é garantida pelo uso de containers Docker, assegurando que o Node.js tenha os mesmos limites de vCPU e memória que o ambiente Low-Code.

## 6. Procedimento Experimental (Protocolo)
A execução seguirá um protocolo rigoroso para assegurar a consistência dos dados. Cada rodada de teste inicia com o **reset e repovoamento** do banco de dados (para garantir o mesmo estado inicial), seguido por uma fase de **Warm-up** de 60 segundos (para aquecer caches e compiladores JIT). Somente então a carga controlada é aplicada, finalizando com um período de *Cool-down* para resfriamento do sistema antes da próxima bateria.

## 7. População e Amostragem
O estudo é do tipo *in silico*, onde a população refere-se às transações de leitura do sistema. Planejamos coletar uma amostra robusta de **1.000 a 10.000 requisições** por cenário de teste. Para evitar vieses de cache simples (onde o banco responde sempre a mesma coisa), utilizaremos dados sintéticos e aleatórios, forçando o sistema a processar requisições variadas durante todo o teste.

## 8. Estratégia de Análise de Dados
A análise focará na Inferência Comparativa Quantitativa. Não será avaliado apenas as médias, que podem esconder problemas, mas sim os **percentis de cauda (p95 e p99)**, que refletem a experiência dos usuários nos piores casos de lentidão. Serão aplicados testes estatísticos e análises visuais via *Boxplots* para identificar a estabilidade e o ponto de ruptura de cada tecnologia.

## 9. Ameaças à Validade e Mitigação
Identificamos e controlamos riscos que poderiam invalidar os resultados. O problema de "vizinhos barulhentos" (Em nuvem pública, outra VM no mesmo hardware físico pode roubar ciclos de CPU) na nuvem será mitigado rodando 3 repetições por cenário em horários distintos. Além disso, o uso de fases de aquecimento (*Warm-up*) neutraliza a desvantagem da "partida a frio" típica de ambientes gerenciados.

## 10. Critérios de Sucesso
O experimento será considerado bem-sucedido e válido para tomada de decisão se houver a execução completa das baterias de teste sem falhas críticas nas ferramentas. Os dados devem apresentar consistência estatística (variância entre rodadas inferior a 20%) e deve ser possível distinguir claramente o tempo de processamento da aplicação em relação ao tempo de resposta do banco de dados.
