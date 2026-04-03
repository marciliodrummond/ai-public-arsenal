# QA Security Checklist — AIOX Autopilot Nirvana
# Usado pelo Guardian no check de segurança

## OWASP TOP 10 (essenciais)

### A01: Broken Access Control
- [ ] Rotas protegidas verificam autenticação
- [ ] Autorização por role/permission implementada
- [ ] CORS configurado corretamente (não wildcard em prod)
- [ ] Rate limiting em endpoints sensíveis

### A02: Cryptographic Failures
- [ ] Passwords hasheados (bcrypt/argon2, nunca MD5/SHA1)
- [ ] HTTPS enforçado
- [ ] Tokens com expiração adequada
- [ ] Secrets em environment variables (não hardcoded)

### A03: Injection
- [ ] SQL: queries parametrizadas (ORM ou prepared statements)
- [ ] XSS: output sanitizado (React faz por padrão, mas verificar dangerouslySetInnerHTML)
- [ ] Command injection: sem exec/eval com input do usuário
- [ ] NoSQL injection: queries validadas

### A07: Authentication Failures
- [ ] Login com rate limiting
- [ ] Session management seguro
- [ ] Logout invalida sessão server-side
- [ ] Password requirements adequados

### A09: Security Logging
- [ ] Login attempts logados
- [ ] Errors logados (sem expor stack traces ao usuário)
- [ ] Ações sensíveis auditadas

## INPUT VALIDATION
- [ ] Todos inputs do usuário validados (Zod/Yup/manual)
- [ ] File upload: tipo e tamanho validados
- [ ] Números: range check
- [ ] Strings: length check + sanitização
