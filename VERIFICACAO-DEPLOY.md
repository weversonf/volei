# Checklist obrigatório antes de publicar

1. **Firebase Console > Firestore Database > Regras**: publicar o conteúdo de `firestore.rules` da branch de testes. Isso substituirá as regras antigas: faça backup e avalie o impacto antes. O app legado depende de leitura anônima e deixará de funcionar com as novas regras.
2. **Firebase Authentication > Sign-in method**: Google e e-mail/senha já foram ativados pelo proprietário. Em **Settings > Authorized domains**, adicionar `cuidanafuga.vercel.app` (e o domínio de preview se necessário).
3. **Primeiro acesso Super Admin**: autenticar com a conta Google verificada do proprietário, endereço definido em `firestore.rules`; isso cria o perfil inicial. Não compartilhe a senha nem o login.
4. **Índice**: criar o índice composto definido em `firestore.indexes.json` ou usar o link de índice sugerido pelo erro do Firestore. Sem ele, a consulta de eventos privados pode falhar.
5. **Backup dos dados legados**: exportar `volei_events` e `app_users` antes de trocar as regras. Não importar senhas antigas. Os eventos antigos, sem `visibility`, são legíveis pela Diretoria mas não entram na consulta dos membros. Atualize cada documento para `visibility:'open'`, `invitedUids:[]` e `createdBy` com UID de um diretor, após revisão.
6. **Teste dos papéis**: visitante, pendente, membro, diretor e Super Admin. Confirmar que a leitura direta de um evento privado é negada a não convidados.
7. **Publicação**: somente após os testes, usar Vercel com repositório `weversonf/volei` e nome de projeto `cuidanafuga`; endereço desejado `cuidanafuga.vercel.app`, sujeito à disponibilidade.

## Observações
- O frontend e as regras foram **commitados**, mas a conexão atual não permite publicar regras do Firebase. Até publicar essas regras, os fluxos novos não devem ser considerados seguros.
- O app novo prioriza autenticação, agenda, presença, rateio básico e checklist. Recursos legados específicos (placar, sorteio, caronas, calculadora) precisam passar por migração posterior.
- Acesso com Google por pop-up pode ser bloqueado por navegadores; usar navegação normal e checar os domínios autorizados.
- Convites têm expiração de 7 dias. O mesmo link pode ser utilizado por mais de uma pessoa antes de expirar; não é um link de uso único. Controle de uso único precisará de operação backend atômica.
- A conta de Super Admin é identificada por e-mail **verificado** no Firebase Auth e pela política das regras. Não há senha mestra no novo HTML.
