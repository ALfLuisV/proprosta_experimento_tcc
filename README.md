# proprosta_experimento_tcc

# Plano de Experimento – Scoping e Planejamento

## 1. Identificação básica
* **1.1 Título do experimento:** Avaliação de Desempenho e Escalabilidade: Backend Low-Code vs. High-Code (Node.js) sob Alta Demanda.
* **1.2 ID / código:** EXP001-FHC-LOP.
* **1.3 Versão do documento e histórico de revisão:**
*  - v1.0 (Criação inicial do rascunho).
   - v1.0.1 (Especificações técnicas do projeto)
* **1.4 Datas:** 21/11/2025
* **1.5 Autores:** Alfredo Luis Vieira - Graduando em Engenharia de Software
* **1.6 Responsável principal:** Alfredo Luis Vieira.
* **1.7 Projeto / produto / iniciativa relacionada:** Iniciativa de Arquitetura de Referência; Estudo de Viabilidade para Utilização de softwares de prateleira.

## 2. Contexto e problema
* **2.1 Descrição do problema / oportunidade:** Há uma incerteza técnica sobre se a plataforma Low-Code adotada (ou candidata) consegue suportar picos de acesso sem degradação severa de latência, o que impede sua adoção em sistemas cuja a demanda de acessos é alta, ofertando facilidade de implementação, plataformas low code podem custar caro em questões de desempenho, o que pode acabar causando danos para sistemas onde o acessso em massa é pesado.
* **2.2 Contexto organizacional e técnico:** A organização busca acelerar o desempenho usando Low-Code, mas precisa garantir que requisitos não funcionais (performance) sejam atendidos. 
* **2.3 Trabalhos e evidências prévias:** Relatos anedóticos de lentidão em relatórios complexos na plataforma atual; benchmarks genéricos de fornecedores (geralmente enviesados).
* **2.4 Referencial teórico e empírico essencial:** Teoria de filas; Conceitos de *Throughput* (vazão) e Latência; Overhead de abstração em frameworks de software.

## 3. Objetivos e questões (Goal / Question / Metric)

### 3.1 Objetivo geral (Goal Template)
**Analisar** a implementação de uma API REST de consulta (Low-Code vs. High-Code), **com o propósito de** caracterizar a degradação de performance e eficiência de recursos sob estresse, **sob a perspectiva de** Engenheiros de Software **no contexto de** uma aplicação corporativa crítica com picos de acesso, utilizando banco de dados relacional.

### 3.2 Objetivos específicos
* **(O1) Analisar a Latência Percebida:** Determinar o tempo de resposta e a estabilidade da entrega de dados para o cliente final sob diferentes cargas.
* **(O2) Avaliar a Capacidade de Vazão (Throughput):** Identificar o volume máximo de transações que a solução suporta antes da saturação dos recursos.
* **(O3) Mensurar a Eficiência de Hardware:** Verificar o "custo computacional" (CPU/Memória) necessário para processar as requisições em cada plataforma.
* **(O4) Verificar a Confiabilidade sob Estresse:** Analisar a taxa de falhas e o comportamento de recuperação do sistema quando submetido a cargas extremas.

### 3.3 e 3.4 Matriz GQM (Goal / Question / Metric)

| Objetivo Específico | Questões de Pesquisa (Questions) | Métricas Associadas (Metrics) |
| :--- | :--- | :--- |
| **O1. Latência** | **Q1.1** Qual é a latência basal do sistema em condições normais (sem concorrência)? | **[M01]** Tempo Médio de Resposta (ART)<br>**[M02]** Desvio Padrão da Latência |
| | **Q1.2** Como a latência se comporta para os 5% dos usuários mais lentos (piores casos) sob carga? | **[M03]** Latência no Percentil 95 (p95)<br>**[M04]** Latência no Percentil 99 (p99) |
| | **Q1.3** Qual é o fator de degradação da resposta quando a carga dobra de 100 para 200 usuários? | **[M05]** Fator de Degradação de Latência<br>**[M01]** Tempo Médio de Resposta (ART) |
| **O2. Vazão** | **Q2.1** Qual é o número máximo de requisições por segundo (RPS) sustentável? | **[M06]** Max Throughput (RPS)<br>**[M07]** Taxa de Sucesso de Requisições |
| | **Q2.2** Em que ponto de concorrência o sistema atinge a saturação (latência > 2s ou erros)? | **[M08]** Ponto de Saturação (Concurrent Users)<br>**[M06]** Max Throughput (RPS) |
| | **Q2.3** O throughput se mantém estável ou oscila drasticamente durante o teste de *soak* (duração longa)? | **[M02]** Desvio Padrão da Latência<br>**[M09]** Variação de Throughput (RPS Jitter) |
| **O3. Eficiência** | **Q3.1** Qual é o consumo médio de CPU para sustentar 500 usuários simultâneos? | **[M10]** Utilização Média de vCPU (%)<br>**[M06]** Max Throughput (RPS) |
| | **Q3.2** Qual é a pegada de memória (footprint) da aplicação em repouso vs. carga máxima? | **[M11]** Consumo de Memória RAM (MB)<br>**[M12]** Pico de Memória (Peak RSS) |
| | **Q3.3** Existe vazamento de recursos (*memory leak*) perceptível após 30 minutos de teste? | **[M11]** Consumo de Memória RAM (MB)<br>**[M13]** Taxa de Crescimento de Memória/Hora |
| **O4. Confiabilidade** | **Q4.1** Qual é a porcentagem de requisições falhas (HTTP 5xx) sob estresse máximo? | **[M14]** Taxa de Erro (% Error Rate)<br>**[M15]** Contagem Total de Exceções |
| | **Q4.2** O sistema apresenta *timeouts* de conexão antes de processar a requisição? | **[M16]** Contagem de Timeouts<br>**[M03]** Latência no Percentil 95 (p95) |
| | **Q4.3** Após o término do pico de carga, quanto tempo o sistema leva para recuperar a latência normal? | **[M17]** Tempo de Recuperação (Recovery Time)<br>**[M01]** Tempo Médio de Resposta (ART) |

### Tabela de Definição das Métricas

| ID | Nome da Métrica | Descrição | Unidade |
| :--- | :--- | :--- | :--- |
| **M01** | Tempo Médio de Resposta (ART) | Média aritmética do tempo total (round-trip) da requisição. | Milissegundos (ms) |
| **M02** | Desvio Padrão da Latência | Medida de dispersão que indica a estabilidade/variabilidade das respostas. | Milissegundos (ms) |
| **M03** | Latência Percentil 95 (p95) | Valor de tempo abaixo do qual estão 95% das requisições (ignora outliers). | Milissegundos (ms) |
| **M04** | Latência Percentil 99 (p99) | Valor de tempo abaixo do qual estão 99% das requisições (piores casos). | Milissegundos (ms) |
| **M05** | Fator de Degradação | Razão entre a latência sob carga máxima e a latência basal. | Razão (x vezes) |
| **M06** | Max Throughput | Maior taxa de requisições processadas com sucesso por segundo. | Req/seg (RPS) |
| **M07** | Taxa de Sucesso | Percentual de requisições que retornaram HTTP 200 OK. | Porcentagem (%) |
| **M08** | Ponto de Saturação | Número de usuários virtuais no momento da quebra de SLA (erro ou lentidão). | Usuários Virtuais (VUs) |
| **M09** | Variação de Throughput | Diferença entre o pico e o vale de RPS durante uma carga constante. | Req/seg (RPS) |
| **M10** | Utilização Média de vCPU | Porcentagem de uso dos núcleos de processamento do servidor. | Porcentagem (%) |
| **M11** | Consumo de Memória RAM | Memória residente (RSS) utilizada pelo processo da aplicação. | Megabytes (MB) |
| **M12** | Pico de Memória | O maior valor de memória registrado durante a execução. | Megabytes (MB) |
| **M13** | Taxa Crescimento Memória | Velocidade de aumento do uso de RAM ao longo do tempo (indica leak). | MB/hora |
| **M14** | Taxa de Erro | Proporção de requisições com status HTTP 500, 502, 503 ou 504. | Porcentagem (%) |
| **M15** | Contagem de Exceções | Número absoluto de erros não tratados logados no servidor. | Inteiro (Contagem) |
| **M16** | Contagem de Timeouts | Número de requisições abortadas por exceder o tempo limite do cliente. | Inteiro (Contagem) |
| **M17** | Tempo de Recuperação | Tempo necessário para a latência voltar à baseline após o fim da carga. | Segundos (s) |

---

## 4. Escopo e contexto do experimento

### 4.1 Escopo funcional / de processo
* **Incluído:** Desenvolvimento de 1 endpoint GET parametrizável. A lógica interna envolverá: recepção da requisição, validação básica de parâmetros, consulta SQL (SELECT com WHERE e LIMIT) em tabela populada com 50.000 registros e serialização do resultado para JSON.
* **Excluído:** Interface gráfica (frontend), autenticação complexa (OAuth/SSO), cache de aplicação (Redis/Memcached) — o objetivo é testar o processamento "bruto" da plataforma e não a eficiência de cache.

### 4.2 Contexto do estudo
* **Organização:** Equipe engenheiros de software em empresa de médio porte.
* **Ambiente Técnico:** Ambiente na AWS. Ambas as aplicações (Low-Code e Node.js) rodarão em contêineres com limites de recursos idênticos (ex: 1 vCPU, 2GB RAM) para isolar a variável "eficiência do software".
* **Dados:** Banco de dados PostgreSQL dedicado, pré-populado com dados sintéticos (fictícios) para garantir consistência entre testes.

### 4.3 Premissas
* O banco de dados não será o gargalo (será superdimensionado em relação à aplicação).
* A ferramenta de geração de carga (k6/JMeter) está em uma rede interna com banda suficiente para não limitar o *throughput*.
* A plataforma Low-Code permite desabilitar logs de debug que poderiam distorcer a performance em produção.

### 4.4 Restrições
* **Orçamentária:** Limite de R$ 500,00 em créditos de nuvem para execução dos testes.
* **Técnica:** Impossibilidade de acessar o código-fonte "compilado" da plataforma Low-Code para otimizações de baixo nível; o teste avalia a plataforma "as-is" (como é fornecida).
* **Temporal:** Janela de execução de testes restrita ao período noturno para evitar concorrência de rede.

### 4.5 Limitações previstas
* **Validade Externa:** Os resultados aplicam-se a cenários de leitura (Read-Heavy). Cenários de escrita intensa ou processamento complexo de regras de negócio podem apresentar comportamentos divergentes não cobertos por este estudo.
* **Representatividade:** O teste sintético não simula o comportamento caótico de usuários reais (ex: cliques rápidos, conexões lentas de 3G), focando apenas na capacidade do servidor.

---

## 5. Stakeholders e impacto esperado

### 5.1 Stakeholders principais
* **Gerente de Engenharia:** Focado em viabilidade econômica e riscos de *vendor lock-in* técnico.
* **Arquitetos de Solução:** Interessados nos limites técnicos, escalabilidade e padrões de integração.
* **Equipe de Desenvolvimento:** Interessada na facilidade de manutenção versus a frustração potencial com lentidão da ferramenta.
* **Time de Infraestrutura/FinOps:** Interessado na eficiência de recursos (custo por transação).

### 5.2 Interesses e expectativas dos stakeholders
* **Arquitetos:** Esperam dados concretos (não marketing do fornecedor) para justificar o uso (ou o veto) da plataforma em sistemas *core*.
* **Gestores:** Querem saber se a economia de tempo de desenvolvimento no Low-Code será "devolvida" em custos excessivos de nuvem devido à baixa performance.
* **Devs:** Esperam que a plataforma não imponha latências que prejudiquem a experiência do usuário (UX).

### 5.3 Impactos potenciais no processo / produto
* **Estratégico:** Se o Low-Code falhar nos critérios de performance (ex: latência > 500ms), haverá um *pivot* mandatório para desenvolvimento tradicional (High-Code) para o novo módulo crítico.
* **Financeiro:** Necessidade de revisar o budget de Cloud caso a solução Low-Code exija 3x mais hardware para entregar a mesma performance do Node.js.

---

## 6. Riscos de alto nível, premissas e critérios de sucesso

### 6.1 Riscos de alto nível
* **Bloqueio de Segurança (WAF):** A plataforma Low-Code (se SaaS) pode identificar o teste de carga como um ataque DDoS e bloquear o IP de origem.
* **Violação de Termos:** Risco legal caso o contrato da ferramenta proíba *benchmarking* público (mitigação: manter o relatório estritamente interno).
* **Interferência ("Noisy Neighbor"):** Em ambiente Cloud compartilhado, a performance pode variar devido a outros vizinhos no mesmo hardware físico (mitigação: rodar testes múltiplas vezes e tirar a média).

### 6.2 Critérios de sucesso globais (Go / No-Go)
* Execução de todas as baterias de teste (Carga: 50, 100, 500 usuários) sem falha catastrófica das ferramentas.
* Obtenção de dados com variância estatística aceitável (Desvio padrão < 20% entre rodadas idênticas).
* Capacidade de diferenciar claramente o tempo de processamento da aplicação versus o tempo de banco de dados.

### 6.3 Critérios de parada antecipada
* Custo de nuvem projetado exceder o orçamento nas primeiras horas.
* Taxa de erro superior a 10% já na carga mínima (indica erro de configuração ou implementação, não de performance).
* Bloqueio permanente do IP da ferramenta de teste pela plataforma alvo.
