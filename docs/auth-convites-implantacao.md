# Autenticação e convites — implantação segura

Projeto Firebase existente: `volei-3f30d`. Provedores Google e e-mail/senha ativados no console (confirmados pelo proprietário em 15/11/2026).

## Requisitos
- Grupo único, sem cadastro público. Registro somente por convite válido.
- Papéis: `super_admin` (um), `director` (três), `member`.
- Estados: `pending`, `approved`, `rejected`, `blocked`.
- O convite de cadastro NÃO concede acesso antes da aprovação do Super Admin.
- Eventos: `open` (somente membros aprovados) e `private` (diretores e convidados autorizados).
- Ações privilegiadas verificadas no backend; nunca confiar em role enviado pelo navegador.
- Preservar eventos e coleções existentes; migrar sem exclusões.

## Atenção: vulnerabilidade existente
O `index.html` na main contém senha administrativa fixa e autenticação por comparação de senha no Firestore, com fallback de administrador no navegador. Remover integralmente antes de publicar a nova versão. Considerar credenciais anteriores comprometidas e trocá-las. Revisar regras atuais do Firestore, principalmente acesso anônimo a `volei_events` e `app_users`.

## Modelo de dados sugerido
- `users/{authUid}`: displayName, email, role, status, createdAt, approvedAt, approvedBy.
- `registrationInvites/{inviteId}`: tokenHash, expiresAt, maxUses, usedCount, createdBy, revokedAt.
- `events/{eventId}`: visibility, invitedUids[], createdBy e dados do evento.
- `auditLogs/{id}`: actorUid, action, targetUid, timestamp.

Tokens devem ser aleatórios, guardados apenas como hash e validados no servidor. Usar Cloud Functions/Cloud Run ou outro backend confiável para emitir convites, aprovar usuários e atribuir papéis. Se o plano Spark não comportar a infraestrutura escolhida, selecionar uma alternativa gratuita antes da implantação.

## Sequência de execução
1. Backup dos dados atuais e auditoria das regras Firestore.
2. Criar autenticação Google/e-mail e tela de cadastro por convite.
3. Implementar backend confiável de convite/aprovação, regras de segurança e atribuição inicial de Super Admin por UID verificado.
4. Migrar os eventos existentes e implementar filtros de privacidade no servidor.
5. Testar acesso não autenticado, pendente, membro, convidado privado, diretor e Super Admin.
6. Revisar deploy de homologação e só então promover para produção.

## Pendente
- Acesso autorizado ao Firebase Console/backend para configurar regras e funções.
- Identificação segura do UID do Super Admin após primeiro login.
- Domínio final da aplicação para lista de domínios autorizados no Firebase Auth.
