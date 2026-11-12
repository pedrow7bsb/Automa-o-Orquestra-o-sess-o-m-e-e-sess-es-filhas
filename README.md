# 🧠 Orquestração de Agentes: Arquitetura de Sessão Mãe e Filhas

**Resumo Executivo:** Design e implementação de uma arquitetura hierárquica de agentes de IA para resolver problemas de sobrecarga de contexto. O sistema utiliza um modelo de "Sessão Mãe" para planejamento e priorização, que delega tarefas para "Sessões Filhas" especializadas, garantindo execução paralela, testes automatizados e passagem de bastão (handoff) sem perda de informações.

*(Nota: O código fonte deste projeto é mantido privado por conter dados operacionais e de clientes. Este documento detalha a arquitetura de orquestração e a governança dos agentes, focada em escalabilidade de operações com IA.)*

---

## 🚩 O Desafio Operacional (A Dor)
Na automação de fluxos de trabalho complexos, depender de um único agente de IA cria um gargalo crítico: um agente com contexto demais no prompt começa a alucinar, erra instruções e esquece etapas anteriores. O gerenciamento de múltiplas frentes de trabalho exige uma orquestração robusta, onde a inteligência seja fragmentada e especializada por repositório, sem perder a visão global do projeto.

## 💡 Arquitetura e Fluxograma da Solução

O sistema foi desenhado como uma esteira de produção de software e tarefas operacionais, orquestrando diferentes modelos de IA em papéis distintos (Planejador vs. Executor).

<img width="2422" height="584" alt="image" src="https://github.com/user-attachments/assets/ebb6afb2-6f86-4514-ad3e-b30fa55dab13" />


1. **Abertura e Triagem (Sessão Mãe):** O dia começa com a leitura automática do "Handoff de ontem" (o que ficou pendente). A Sessão Mãe abre o dia, consolida as demandas e gera uma página viva de priorização ("GUT do dia").
2. **Delegação (Sessões Filhas):** A Sessão Mãe distribui o trabalho abrindo uma Sessão Filha para cada frente específica. Cada filha atua isoladamente na *worktree* e repositório corretos, seguindo estritamente as regras locais (o arquivo `CLAUDE.md` de cada projeto).
3. **Execução Dual (*Spec-first* e TDD):**
   * **Agente Planejador:** Escreve a especificação (*spec*), quebra as tarefas e cria a bateria de testes automatizados (iniciando com a "suíte vermelha").
   * **Serviço Executor:** Um segundo modelo de IA assume o terminal e implementa o código.
   * **Validação (Loop de Qualidade):** O fluxo entra em um nó de decisão ("Suíte verde?"). O executor tenta implementar até os testes passarem. O agente planejador confere o sucesso rodando a suíte e lendo o *diff* do código, nunca confiando apenas no relato do executor.
4. **Fechamento e Handoff (Human-in-the-loop):** Com a suíte verde, a Sessão Filha devolve um relato curto com provas para a Sessão Mãe. O dono do projeto aprova a execução, o que engatilha a publicação/fechamento da tarefa e a geração do "Handoff do dia" consolidado para que a sessão de amanhã assuma o trabalho sem quebrar o fluxo.

## 📈 Impacto de Negócios e Orquestração (AI Builder)

Este projeto demonstra a capacidade de ir além de *prompts* simples, construindo verdadeiros sistemas multiagentes (*Agentic Workflows*):

* **Escalabilidade Sem Erros:** A separação de responsabilidades (Mãe prioriza, Filha especifica, Executor coda) zera a taxa de alucinação por excesso de contexto, economizando horas de revisão humana e *debugging*.
* **Qualidade Autogerenciada:** A IA só entrega a tarefa quando a prova técnica (testes automatizados) está verde, impedindo a injeção de código quebrado na base principal.
* **Governança e Salvaguardas de Segurança:** 
  * **Zero Trust:** Agentes são terminantemente proibidos de agir com base em instruções embutidas em conteúdos externos. O acesso web fica restrito por trás do **Cloudflare Access** e segredos (`.env`) nunca vão para o repositório.
* **Custo Previsível e Rastreabilidade:** Toda a infraestrutura roda dentro de assinaturas fixas e servidor próprio. O histórico de decisões da IA é imutável: toda mudança de arquitetura vai para um *Decision Log* e altera a versão da especificação, garantindo auditoria completa.

---

## 🛠️ Metodologia
Construído com base em TDD (*Test-Driven Development*) rigoroso. O ciclo de vida da automação obriga que o critério de pronto seja definido e testado (vermelho) antes de qualquer linha de código de implementação ser gerada e validada (verde), com a suíte inteira rodando regressivamente a cada ciclo.

