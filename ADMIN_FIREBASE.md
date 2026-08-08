# Configuração manual do painel administrativo

Este projeto funciona no plano gratuito do Firebase. O painel não cria usuários no Authentication: o Firebase gera o UID, e o painel usa esse UID para liberar, vincular ou bloquear o acesso.

> Importante: não publique a nova versão do aplicativo antes de cadastrar o administrador e o documento de acesso dele. Caso contrário, sua própria conta ficará aguardando liberação.

## 1. Guardar as regras atuais

1. Acesse https://console.firebase.google.com/.
2. Abra o projeto `minhas-financas-318e8`.
3. Entre em **Firestore Database > Regras**.
4. Copie as regras atuais e guarde em um arquivo de segurança.

## 2. Criar ou localizar sua conta administradora

1. No Firebase, entre em **Authentication > Users**.
2. Se seu e-mail já estiver cadastrado, clique no usuário e copie o **User UID**.
3. Se ainda não existir, clique em **Add user**, informe seu e-mail e uma senha.
4. Copie o UID gerado automaticamente pelo Firebase.

O UID de vinculação é sempre o **User UID do Firebase Authentication**. Não crie um número diferente no Firestore.

## 3. Autorizar o administrador no Firestore

1. Entre em **Firestore Database > Dados**.
2. Crie a coleção `admins`, caso ela ainda não exista.
3. Dentro dela, crie um documento cujo ID seja exatamente o seu UID.
4. Adicione os campos:

| Campo | Tipo | Valor |
|---|---|---|
| `active` | boolean | `true` |
| `email` | string | seu e-mail |

Somente quem possuir um documento ativo nessa coleção conseguirá entrar no painel.

## 4. Liberar o acesso do próprio administrador

1. Crie a coleção `access`.
2. Crie um documento com o mesmo UID do administrador.
3. Adicione:

| Campo | Tipo | Valor |
|---|---|---|
| `email` | string | seu e-mail |
| `displayName` | string | seu nome |
| `status` | string | `active` |
| `mode` | string | `personal` |
| `partnerEnabled` | boolean | `false` |
| `partnerUid` | string | vazio |
| `partnerEmail` | string | vazio |
| `partnerName` | string | vazio |

Se sua conta já possui um documento na coleção `users`, não apague nem recrie esse documento.

## 5. Publicar as regras de segurança

1. Abra o arquivo `firestore.rules` entregue junto com o projeto.
2. Copie todo o conteúdo.
3. Volte em **Firestore Database > Regras**.
4. Substitua as regras pelo conteúdo copiado.
5. Clique em **Publicar**.

As regras fazem quatro proteções: apenas administradores alteram planos; bloqueados não acessam os dados; parceiros só leem um ao outro quando o vínculo é recíproco; usuários comuns não conseguem mudar UID de parceiro pelo navegador.

## 6. Cadastrar uma pessoa para uso pessoal

1. Abra **Authentication > Users > Add user**.
2. Informe o e-mail do cliente e uma senha inicial.
3. Copie o UID gerado.
4. Entre no painel administrativo com sua conta.
5. Clique em **Novo**.
6. Informe e-mail, nome e UID.
7. Escolha **Ativo** e **Pessoal**.
8. Clique em **Salvar**.

O painel cria os documentos necessários em `access` e `users`.

## 7. Cadastrar um casal

1. Crie os dois usuários em **Authentication > Users**.
2. Copie o UID de cada pessoa.
3. No painel, clique em **Novo**.
4. Informe e-mail, nome e UID da primeira pessoa.
5. Em **Tipo de uso**, escolha **Casal**.
6. Informe e-mail, nome e UID da segunda pessoa.
7. Marque **Exibir opção de parceiro**.
8. Clique em **Salvar**.

O painel grava o vínculo nos dois sentidos. Não é necessário editar manualmente o documento da segunda pessoa.

## 8. Ativar ou desativar a opção de parceiro

1. Pesquise um integrante do casal no painel.
2. Abra o cadastro.
3. Marque ou desmarque **Exibir opção de parceiro**.
4. Salve.

Ao desmarcar, os dois continuam cadastrados, mas a área compartilhada desaparece do aplicativo. Para transformar definitivamente os dois em contas pessoais, use **Desvincular**.

## 9. Bloquear acessos

- **Bloquear usuário:** bloqueia somente a pessoa selecionada.
- **Bloquear casal:** bloqueia os dois integrantes.
- **Ativar usuário:** libera novamente apenas a pessoa selecionada.

Esse bloqueio impede a leitura e a gravação dos dados pelo aplicativo. Para impedir também que a pessoa faça login no Firebase, abra o usuário em **Authentication > Users** e desative a conta manualmente.

## 10. Usuário que se cadastrou pelo próprio aplicativo

Se alguém usar a opção **Cadastrar** do aplicativo antes da liberação, verá uma tela de acesso pendente com o próprio UID. Use esse UID no painel e salve o plano desejado. Após a ativação, o aplicativo recarrega automaticamente.

## Ordem segura para colocar no ar

1. Criar/localizar seu usuário no Authentication.
2. Criar `admins/{seu UID}`.
3. Criar `access/{seu UID}`.
4. Publicar as novas regras.
5. Testar o login administrativo.
6. Somente depois publicar a nova versão do aplicativo.

