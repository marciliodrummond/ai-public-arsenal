# Story Definition of Done — AIOX Autopilot Nirvana
# Baseado no story-dod-checklist.md do AIOX Core
# Usado pelo Builder para self-check antes de submeter para Guardian

## 1. REQUIREMENTS MET
- [ ] Todos os FRs especificados na story estão implementados
- [ ] Todos os acceptance criteria estão atendidos
- [ ] Nenhum AC foi pulado ou parcialmente implementado

## 2. CODE QUALITY
- [ ] Código segue coding-standards.md (naming, imports, structure)
- [ ] Código alinha com source tree da Architecture
- [ ] Stack correta usada (não introduziu deps não planejadas)
- [ ] API endpoints seguem design da Architecture
- [ ] Data model changes seguem schema da Architecture
- [ ] Error handling adequado (try/catch em async, error boundaries)
- [ ] Sem hardcoded secrets ou credentials
- [ ] TypeScript strict: sem any desnecessário

## 3. TESTING
- [ ] Unit tests para lógica de negócio
- [ ] Integration tests para APIs (se aplicável)
- [ ] Testes cobrem todos os acceptance criteria
- [ ] Todos os testes passando (zero failures)
- [ ] Nenhum test skip sem justificativa

## 4. BUILD & LINT
- [ ] npm run build (ou equivalente) passa
- [ ] npm run lint (ou equivalente) sem erros
- [ ] Zero warnings de TypeScript
- [ ] Bundle size dentro do budget (se definido)

## 5. DOCUMENTATION
- [ ] Funções complexas têm JSDoc/comentários
- [ ] README atualizado (se API mudou ou nova feature visível)
- [ ] File List na story atualizada com arquivos reais

## 6. NO LOOSE ENDS
- [ ] Zero TODO/FIXME deixados (ou justificados se temporários)
- [ ] Zero console.log de debug
- [ ] Zero código comentado
- [ ] Imports não utilizados removidos
