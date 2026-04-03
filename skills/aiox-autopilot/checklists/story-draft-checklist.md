# Story Draft Checklist — AIOX Autopilot Nirvana
# Baseado no story-draft-checklist.md do AIOX Core
# Usado pelo Story Forge para validar cada story antes de liberar para dev

## 1. GOAL & CONTEXT CLARITY
- [ ] Story goal/purpose é claramente declarado
- [ ] Relação com o epic é evidente
- [ ] Como a story se encaixa no fluxo geral está explicado
- [ ] Dependências de stories anteriores estão identificadas
- [ ] Contexto de negócio e valor estão claros

## 2. TECHNICAL IMPLEMENTATION GUIDANCE
- [ ] Arquivos-chave a criar ou modificar estão listados
- [ ] Escolhas tecnológicas estão especificadas (quando não-óbvias)
- [ ] Pontos de integração com código existente identificados
- [ ] Data models ou API contracts definidos ou referenciados
- [ ] Patterns não-padrão estão documentados

## 3. ACCEPTANCE CRITERIA QUALITY
- [ ] Pelo menos 2 ACs por story
- [ ] Todos ACs em formato Given/When/Then
- [ ] ACs são testáveis (pode-se escrever um teste automatizado)
- [ ] ACs cobrem happy path E edge cases relevantes
- [ ] Nenhum AC é vago ("funciona corretamente" ← rejeitado)

## 4. SCOPE BOUNDARIES
- [ ] O que está IN scope está claro
- [ ] O que está OUT scope está explícito
- [ ] Não há feature creep (story faz UMA coisa)
- [ ] Estimativa é ≤ 4h (se maior, deve ser quebrada)

## 5. SELF-CONTAINED
- [ ] Dev pode implementar SEM ler o PRD inteiro
- [ ] Technical notes incluem trechos da Architecture (não só referências)
- [ ] API endpoints necessários estão descritos na story
- [ ] Data model changes estão na story

## Scoring
Cada item vale 1 ponto. Score = items_passed / 20 × 10.
- **≥ 8.0** → PASS: story pronta para dev
- **6.0-7.9** → WARN: pode ir para dev com notas
- **< 6.0** → FAIL: reescrita necessária
