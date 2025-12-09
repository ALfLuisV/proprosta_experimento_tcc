# Apresentação do Experimento: Low-Code vs. High-Code

## 1. Contexto e Problema
Atualmente no contexto da solução de problemas através do desenvolvimento, enfrentamos um dilema estratégico: a necessidade de acelerar o desenvolvimento utilizando plataformas **Low-Code** versus a incerteza técnica sobre a capacidade dessas ferramentas suportarem alta demanda. Embora o Low-Code prometa agilidade, existe o risco do custo de performance ("taxa de abstração") inviabilizar sua aplicação em sistemas críticos de acesso em massa. O experimento surge para substituir suposições por dados técnicos concretos sobre desempenho e escalabilidade.

## 2. Objetivos e Métricas (GQM)
O objetivo central é analisar comparativamente a degradação de performance entre uma solução Low-Code e uma solução tradicional em Node.js (High-Code). Utilizando a abordagem GQM (Goal-Question-Metric), mediremos três pilares fundamentais: a **Latência** (tempo de resposta percebido pelo usuário), a **Vazão** (capacidade máxima de requisições por segundo) e a **Eficiência de Hardware** (quanto de CPU e memória é consumido para realizar o mesmo trabalho).

## 3. Modelo Conceitual e Hipóteses
O modelo teórico baseia-se na premissa de que plataformas Low-Code operam sobre camadas adicionais de interpretação e frameworks genéricos, gerando um *overhead* computacional natural. As hipóteses principais testarão se, sob a mesma carga, a solução Low-Code apresenta latência média superior ($H_1$ Latência) e consome significativamente mais recursos de CPU ($H_1$ Eficiência) do que o código otimizado em Node.js.

## 4. Objetos de Estudo e Variáveis Controladas
O experimento comparará dois artefatos de software distintos que implementam **exatamente a mesma regra de negócio**: uma API de consulta de clientes com filtros e paginação. As variáveis independentes manipuladas serão a **Tecnologia** (Low-Code vs. High-Code) e a **Carga de Concorrência**, submetendo ambas as soluções a cenários escalonados de 10, 100, 500 e 1.000 usuários simultâneos para observar a curva de degradação.

## 5. Infraestrutura e Ferramentas
Para garantir um ambiente controlado e isolado, utilizaremos a infraestrutura de nuvem da AWS. O monitoramento será feito via **Datadog** (para métricas de CPU/RAM) e a geração de carga via **k6**, uma ferramenta robusta para simulação de usuários. A paridade de recursos é garantida pelo uso de containers Docker, assegurando que o Node.js tenha os mesmos limites de vCPU e memória que o ambiente Low-Code.

## 6. Procedimento Experimental (Protocolo)
A execução seguirá um protocolo rigoroso para assegurar a consistência dos dados. Cada rodada de teste inicia com o **reset e repovoamento** do banco de dados (para garantir o mesmo estado inicial), seguido por uma fase de **Warm-up** de 60 segundos (para aquecer caches e compiladores JIT). Somente então a carga controlada é aplicada, finalizando com um período de *Cool-down* para resfriamento do sistema antes da próxima bateria.

## 7. População e Amostragem
O estudo é do tipo *in silico*, onde a população refere-se às transações de leitura do sistema. Planejamos coletar uma amostra robusta de **1.000 a 10.000 requisições** por cenário de teste. Para evitar vieses de cache simples (onde o banco responde sempre a mesma coisa), utilizaremos dados sintéticos e aleatórios, forçando o sistema a processar requisições variadas durante todo o teste.

## 8. Estratégia de Análise de Dados
A análise focará na Inferência Comparativa Quantitativa. Não avaliaremos apenas as médias, que podem esconder problemas, mas sim os **percentis de cauda (p95 e p99)**, que refletem a experiência dos usuários nos piores casos de lentidão. Serão aplicados testes estatísticos (como Mann-Whitney) e análises visuais via *Boxplots* para identificar a estabilidade e o ponto de ruptura de cada tecnologia.

## 9. Ameaças à Validade e Mitigação
Identificamos e controlamos riscos que poderiam invalidar os resultados. O problema de "vizinhos barulhentos" na nuvem será mitigado rodando 3 repetições por cenário em horários distintos. A latência de rede externa será eliminada posicionando o gerador de carga na mesma rede interna (VPC) dos servidores. Além disso, o uso de fases de aquecimento (*Warm-up*) neutraliza a desvantagem da "partida a frio" típica de ambientes gerenciados.

## 10. Critérios de Sucesso
O experimento será considerado bem-sucedido e válido para tomada de decisão se houver a execução completa das baterias de teste sem falhas críticas nas ferramentas. Os dados devem apresentar consistência estatística (variância entre rodadas inferior a 20%) e deve ser possível distinguir claramente o tempo de processamento da aplicação em relação ao tempo de resposta do banco de dados.
