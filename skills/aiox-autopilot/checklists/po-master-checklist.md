# PO Master Validation Checklist — AIOX Autopilot Nirvana
# Baseado no po-master-checklist.md do AIOX Core
# Usado pelo Commander para validar artefatos entre fases

## 1. PRD VALIDATION

### Requirements Quality
- [ ] Todos os FRs têm ID sequencial (FR1, FR2, ...)
- [ ] Todos os FRs são testáveis (tem critério de aceite implícito ou explícito)
- [ ] NFRs cobrem: performance, segurança, acessibilidade, escalabilidade
- [ ] Priorização MoSCoW aplicada a todos os FRs
- [ ] Nenhum FR é vago ("o sistema deve ser bom" ← rejeitado)

### Epic Coverage
- [ ] Todo FR MUST tem pelo menos 1 epic/story mapeado
- [ ] Todo FR SHOULD tem pelo menos 1 epic/story mapeado
- [ ] Epics estão ordenados por dependência (fundacionais primeiro)
- [ ] Estimativas rough (S/M/L) atribuídas a cada epic

### UI Goals (se aplicável)
- [ ] Core screens listadas e correspondem aos FRs
- [ ] Plataformas alvo definidas
- [ ] Acessibilidade definida (nível WCAG ou "None" explícito)

## 2. ARCHITECTURE VALIDATION

### Tech Stack
- [ ] Cada tecnologia tem justificativa (não escolha arbitrária)
- [ ] Stack é coerente (e.g., não mistura React + Vue)
- [ ] Versões especificadas para frameworks principais

### Data Model
- [ ] Pelo menos 2 entidades definidas
- [ ] Relacionamentos claros (1:N, N:N documentados)
- [ ] Campos com tipos definidos

### API Design
- [ ] Endpoints cobrem todos os FRs de backend
- [ ] Auth/authorization especificados
- [ ] Response shapes definidos

### Source Tree
- [ ] Estrutura de diretórios completa
- [ ] Segue convenções do framework escolhido
- [ ] Cada componente mencionado nos epics tem lugar na árvore

## 3. CROSS-REFERENCE VALIDATION

- [ ] Todo FR no PRD → tem componente(s) na Architecture
- [ ] Toda tela na Frontend Spec → tem route na Architecture
- [ ] Todo endpoint na API → tem handler no source tree
- [ ] Data model na Architecture → corresponde a schemas mencionados no PRD
- [ ] Stack na Architecture = stack nas Technical Assumptions do PRD

## 4. BROWNFIELD EXTRAS (se aplicável)

- [ ] Codebase existente analisado (tech stack, patterns, quality)
- [ ] Áreas de impacto identificadas (affected_areas)
- [ ] Constraints de preservação documentados (must_preserve)
- [ ] Riscos de integração mapeados com mitigação
- [ ] Breaking changes explicitamente marcados (allowed/not)

## Scoring

Cada item vale 1 ponto. Score = items_passed / items_applicable × 10.
- **≥ 8.0** → PASS: pode avançar para próxima fase
- **6.0-7.9** → WARN: avançar com ressalvas documentadas
- **< 6.0** → FAIL: precisa retrabalho antes de avançar
