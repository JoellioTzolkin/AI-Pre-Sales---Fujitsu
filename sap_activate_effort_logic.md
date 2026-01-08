# SAP Activate – lógica conceitual de esforço (para estimativa e geração de Excel)

Este documento resume como eu uso a **metodologia SAP Activate** como “espinha dorsal” para **estruturar e calcular esforço** (homem-hora / homem-dia) por **fase**, por **workstream**, por **papel** e por **entregável**, visando gerar um Excel padronizado (estilo “grid por fases”) a partir de exports oficiais do SAP Roadmap Viewer e/ou de um Project Plan (MS Project).

> **Importante:** o SAP Activate é principalmente uma **metodologia + roadmaps (tarefas/entregáveis/aceleradores)**. O “número” de esforço normalmente não vem pronto; eu preciso **derivar** (via Project Plan quando existir) ou **modelar** (heurísticas/catálogos/multiplicadores) usando os dados exportados.

---

## 1) Estrutura do SAP Activate que importa para esforço

### 1.1 Fases e “quality gates”
O SAP Activate organiza a execução em fases. A execução principal costuma ficar entre **Prepare → Explore → Realize → Deploy** (as quatro fases “core”), com **Discover** e **Run** como fases adicionais.

**Implicação para esforço:** eu agrego esforço primeiro por fase (para fechar visão executiva) e, dentro da fase, por workstream/papel.

### 1.2 Deliverables, Tasks, Workstreams e Accelerators
Eu uso a estrutura abaixo como “modelo de dados” do projeto:

- **Phases**: estágios do projeto; ao final de cada fase existe um **quality gate** (checagem de conclusão dos entregáveis).
- **Deliverables**: resultados/outcomes entregues durante o projeto.
- **Tasks**: trabalho executável; uma ou mais tasks compõem um deliverable.
- **Workstreams**: agrupam deliverables relacionados; podem atravessar fases (não “começam/terminam” obrigatoriamente junto da fase).
- **Accelerators**: guias/templates/recomendações/links que ajudam a executar uma task; *aceleradores são ligados às tasks*.

**Implicação para esforço e rastreabilidade:**
- **Task** é minha menor unidade de cálculo (onde eu consigo atribuir esforço e papel).
- **Deliverable** é a unidade que “vende” (o que foi entregue), e eu consigo chegar nele somando tasks.
- **Accelerator** é a ponte para “fonte oficial SAP”: quando uma task tem aceleradores, eu consigo transformar isso em uma lista de entregáveis com URL/ID e rastrear por que aquela atividade existe.

---

## 2) Onde o SAP “guarda” o conteúdo que eu preciso (tarefas/entregáveis/aceleradores)

Para a POC e para automatizar geração de Excel, eu assumo que a fonte oficial é o que sai do **SAP Activate Roadmap Viewer** (normalmente via SAP for Me / Roadmap Viewer).

No topo, os roadmaps costumam trazer:
- **Overview** (visão por fase e deliverables por workstream)
- **Content** (estrutura principal: fase → deliverables → tasks)
- **Filters** (para recortar por fase, workstream, produto, tags)

---

## 3) Como cada fase “vira esforço” (drivers típicos)

Abaixo eu descrevo o que cada fase significa na prática, porque isso vira **drivers** para meu esforço.

### 3.1 Prepare (planejamento e setup)
Normalmente envolve: definir escopo e governança, preparar sandboxes/starters, iniciar projeto, identificar recursos, definir papéis e responsabilidades, e detalhar planos de gestão.

**Drivers típicos que aumentam esforço aqui:**
- nº de stakeholders e ritos de governança
- nº de equipes/fornecedores envolvidos
- requisitos de compliance / segurança / acessos / conectividade

### 3.2 Explore (Fit-to-Standard + backlog)
Aqui ocorre o Fit-to-Standard com Best Practices; eu demonstro processos padrão, identifico deltas (configuração), gaps e valores, e isso vira backlog.

**Drivers típicos:**
- nº de processos e áreas de negócio em escopo
- severidade de gaps/deltas
- nº de integrações e sistemas satélites (impacta validação e desenho)

### 3.3 Realize (build iterativo + testes + integração)
Execução incremental em sprints, construindo backlog; adiciono configuração e desenvolvimento “em cima” das Best Practices (deltas), com testes (unit/string) e integração, além de preparar conversão/carga de dados. Em geral, inclui também ciclos de teste ponta a ponta e UAT por release.

**Drivers típicos:**
- nº de deltas/configurações fora do padrão
- extensões (BTP / in-app) e requisitos de clean core
- integrações (interfaces, APIs, middleware)
- objetos e volume de migração de dados
- estratégia de testes, nº de ciclos e ambientes

### 3.4 Deploy (cutover, go-live, hypercare)
A fase garante prontidão para cutover; complexidade depende de usuários impactados e escopo. Pode exigir simulações de cutover. Após go-live, existe um período de suporte (hypercare).

**Drivers típicos:**
- nº de usuários/plantas/países
- nº de cutover rehearsals
- janela de go-live e restrições operacionais
- duração do hypercare e modelo de suporte

### 3.5 Discover e Run (fora do “core”)
- **Discover**: fase não-compromissada para avaliar ofertas/recursos e preparar estratégia.
- **Run**: “productive operations” (operação contínua / evolução).

Para esforço de projeto (pré-venda), eu normalmente foco a estimativa em Prepare–Deploy; Discover/Run podem existir como “blocos” opcionais.

---

## 4) Lógica prática para calcular esforço (POC → robusto)

### 4.1 POC (auditável e simples)
Eu priorizo cálculo determinístico a partir dos dados exportados.

**Modo A – quando existe Project Plan (MS Project XML / Assignments):**
1. Task effort = soma do **Work** das assignments da tarefa (por recurso/papel)
2. Phase effort = soma do effort das tasks pertencentes à fase
3. Role effort = soma por recurso/papel

**Modo B – quando NÃO existe Project Plan (só WBS/Duration):**
1. Task effort (heurístico) = DurationDays × horas_por_dia (ex.: 8h)
2. Role allocation:
   - se houver papéis na linha → distribuir (POC) igualmente entre os papéis
   - se não houver → marcar papel “TBD”
3. Agregar por fase/papel igual ao Modo A

### 4.2 Esforço por entregável (com rastreabilidade)
O “entregável” que é mais rastreável em POC é o **Accelerator ID** (porque ele vem ligado à task).

Regra simples:
- se uma task tem **N aceleradores**, eu **divido** o esforço da task por N e somo por Accelerator ID.
- se não tem acelerador, eu deixo como “sem deliverable associado” (ou TBD).

Isso gera:
- **top deliverables por esforço**
- lista de “entregáveis oficiais SAP” com ID/título/link (quando disponível)

### 4.3 Depois do POC (robustez)
Quando eu quiser chegar numa estimativa “de verdade” (não só POC), eu adiciono:

- **catálogo interno de esforços** por tipo de atividade/deliverable (calibrado com projetos reais)
- **multiplicadores** por complexidade (ex.: integrações, países, objetos de migração, extensões)
- buffers padrão (PMO, risco/contingência, QA, segurança)

---

## 5) Saídas que eu espero gerar (para Excel e/ou HTML)

A partir dessa lógica, eu consigo produzir um JSON/planilha com:

1. **WBS detalhado** (fase → deliverable → task), com workstream e papéis.
2. **Matriz de esforço** por fase e por papel (horas e dias).
3. **Lista de entregáveis** (Accelerators) agrupada por fase, com link/ID.
4. **Esforço por entregável** (Accelerator ID).
5. **Cost summary** (se eu tiver day rate por papel).

---

## 6) Sobre versionar o PDF “ACTIVATE” no Git

Se o objetivo é **apenas ter referência**, eu prefiro:
- manter **resumos em markdown** (como este) + links oficiais
- e **não commitar PDFs enormes** (a menos que exista necessidade de auditoria offline, ou se a licença interna permitir).

Quando for necessário versionar (por exemplo, evidência de fonte “congelada”), eu recomendo:
- dividir em partes (como vocês fizeram), para evitar diffs gigantes e facilitar navegação,
- e manter o README explicando a origem e a data.

---

## 7) Glossário rápido (para o time)

- **Quality Gate**: checkpoint ao final da fase para confirmar entregáveis.
- **Fit-to-Standard**: workshop para validar aderência aos processos padrão e capturar deltas/gaps.
- **Delta**: diferença (configuração/extensão) além do Best Practice padrão.
- **Backlog**: lista priorizada de itens (deltas/gaps) a implementar na Realize.
- **Hypercare**: período de suporte pós go-live.

---

### Checklist (para eu saber se meu estimate está “fechado”)
- [ ] Toda task pertence a uma fase
- [ ] Toda task tem esforço (Work do MSProject ou cálculo heurístico)
- [ ] Somatório por fase = somatório por tasks
- [ ] Papéis/recurso existem (ou estão marcados como TBD com justificativa)
- [ ] Deliverables (aceleradores) estão deduplicados por ID
- [ ] Se existir custo: day rate preenchido para cada papel crítico

