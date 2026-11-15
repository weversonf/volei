# Auditoria de validação — 15/11/2026

## Escopo
Branch `feature/auth-convites-aprovacao`, `index.html`, `firestore.rules`, `firestore.indexes.json`, `firebase.json`, em comparação funcional com a versão anterior.

## Verificações possíveis sem credenciais de administração
- [x] Login por e-mail/senha e provedor Google integrados ao SDK Firebase no código.
- [x] Não há mais senha mestra nem `signInAnonymously` no novo `index.html`.
- [x] Controle de perfis `super_admin`, `director`, `member` e estados `pending`, `approved`, `blocked` implementado no frontend.
- [x] Controle de eventos `open`/`private` e lista de convidados no frontend e nas regras propostas.
- [x] Métodos das regras Firestore `List.removeAll`, `List.hasAll`, `Map.get` e `duration.value` conferidos na documentação oficial.
- [x] `firebase.json` aponta para o arquivo de regras e índices.
- [x] JavaScript validado quanto a sintaxe com compilador JS, depois das últimas mudanças.

## Pendências críticas (bloqueiam liberar)
- [ ] **Regras não publicadas** no Firebase. Não há acesso de administração Firebase nesta conversa.
- [ ] **Sem testes reais no Authentication/Firestore**: não foi executado login/autenticação, aprovação, rejeição, convite expirado, acesso negado, nem leitura de documentos privados com credenciais reais.
- [ ] **Sem teste no emulador Firebase**. O arquivo das regras foi inspecionado, mas ainda não compilado/testado em emulador ou Firebase Console.
- [ ] **Regressão funcional**: a nova versão substituiu módulos de placar, sorteio de equipes, carona, roteiro e calculadora de churrasco do app antigo. Não está apta a substituí-lo sem uma decisão explícita sobre a migração.
- [ ] **Dados anteriores** sem `visibility` ficam visíveis apenas à Diretoria até migração manual.
- [ ] **Google Auth** depende de domínio autorizado no Firebase.
- [ ] **Índice composto** de eventos privados precisa ser aplicado.
- [ ] **Deploy** não realizado e endereço `cuidanafuga.vercel.app` não verificado.
- [ ] **Backups** das coleções existentes ainda não verificados.
- [ ] **Convites não são de uso único**: funcionam até expiração/revogação, podendo cadastrar múltiplas pessoas pendentes.

## Veredito
**NÃO APROVADO PARA PRODUÇÃO**. Versão de desenvolvimento criada, mas validação ponta-a-ponta ainda pendente. Manter `main` e produção atuais intactas.

## Testes de aceite
1. Super Admin verificado (UID real): criação automática somente com e-mail autorizado e verificado; tentativa com outro e-mail deve falhar.
2. Cadastro por convite válido: perfil `pending`; não deve listar eventos.
3. Cadastro sem convite, expirado ou revogado: rejeitar perfil.
4. Aprovação pelo Super Admin: acesso do membro à agenda de eventos abertos.
5. Membro comum: negar consulta direta a evento privado não autorizado e negar escrita de campos administrativos.
6. Membro convidado: consultar evento privado autorizado e confirmar apenas a própria presença.
7. Diretor: criar/editar eventos; membro comum: negar criação.
8. Super Admin: promover no máximo três diretores na UI (regra robusta de cota requer backend).
9. Conta bloqueada: revogação de acesso confirmada.
10. Migrar evento legado em cópia de teste e revalidar todas as funcionalidades.
