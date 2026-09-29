# Agência Rise · Como ativar a segurança

Faça os passos **nesta ordem**. Leva cerca de 10 minutos. Nenhum dado é apagado.

## 1. Criar os logins (Supabase → Authentication → Users)
Clique em **Add user → Create new user**, marque **Auto Confirm User** e crie os 3 logins:

| Perfil | E-mail | Senha |
|---|---|---|
| Cleverton | cleverton@agenciarise.app | escolha uma senha nova (mín. 6 caracteres) |
| Victoria | victoria@agenciarise.app | escolha uma senha nova |
| João Victor | joao@agenciarise.app | escolha uma senha nova |

As senhas antigas saíram do código. Recomendo senhas novas; passe a cada um a sua.

## 2. Configurar o login (Authentication → Sign In / Providers → Email)
- **Confirm email**: desativado (os e-mails são internos e não recebem mensagem).
- **Allow new users to sign up**: ativado. É o que permite você criar usuários pela aba Equipe. Ninguém de fora consegue usar o sistema mesmo se criar uma conta, porque o acesso só é liberado para quem está na lista de usuários criada no passo 3.

## 3. Rodar o SQL (SQL Editor → New query)
Cole todo o conteúdo de `supabase/seguranca.sql` e clique em **Run**. Deve aparecer "Success".

## 4. Edge Function do Gemini (se já estiver publicada)
- Edge Functions → Secrets: crie `REQUIRE_AUTH` com valor `true`.
- Na função `generate-weekly-stories`, ative **Enforce JWT Verification**.

## 5. Publicar
Suba o novo `deploy/index.html` no GitHub/Vercel no lugar do anterior.

## Como conferir
1. Abra o sistema: os 3 perfis aparecem. Entre com a senha nova de cada um.
2. No login da Victoria ou do João: a aba Financeiro não aparece e "Meus pagamentos" mostra só os pagamentos dele.
3. Abra um link de aprovação numa janela anônima: o cliente vê o material e consegue aprovar sem login.
4. Sem login, o banco não entrega nenhum dado (a tela inicial só recebe nome, cargo e foto).

## O que mudou
- Senhas validadas pelo Supabase; nenhuma senha no código.
- Tabela `rise_data` sem acesso anônimo.
- Financeiro, PIX, pagamentos, contratos, propostas e métricas de relatório: só o administrador lê e grava.
- Colaborador recebe apenas os próprios pagamentos e o próprio PIX.
- Link de aprovação: o cliente só vê e decide a demanda daquele link.
- Criar e remover usuários: só o administrador. Remover bloqueia o acesso na hora.
- Os dados não ficam mais guardados no navegador; ao sair, tudo é limpo da memória.

Usuários criados antes desta etapa pela aba Equipe precisam ser criados de novo (eles não tinham login no Supabase).
