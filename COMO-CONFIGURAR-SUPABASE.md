# Agência Rise: como deixar o sistema online e sincronizado (Supabase)

## 1. Criar a conta e o projeto
1. Acesse https://supabase.com e clique em **Start your project**. Entre com sua conta do GitHub.
2. Clique em **New project**, dê o nome `agencia-rise`, crie uma senha para o banco e escolha a região **South America (São Paulo)**.
3. Espere o projeto terminar de ser criado (cerca de 1 minuto).

## 2. Criar a tabela (copie e cole)
1. No menu lateral, abra **SQL Editor** e clique em **New query**.
2. Cole o código abaixo e clique em **Run**:

```sql
create table if not exists public.rise_data (
  id text primary key,
  data jsonb not null default '[]'::jsonb,
  updated_at timestamptz default now()
);

alter table public.rise_data enable row level security;

create policy "equipe rise acesso total" on public.rise_data
  for all using (true) with check (true);

alter publication supabase_realtime add table public.rise_data;
```

## 3. Copiar as chaves
1. Vá em **Project Settings** (ícone de engrenagem) e depois em **API** (ou **Data API / API Keys**).
2. Copie a **Project URL**, que tem o formato `https://xxxxx.supabase.co`.
3. Copie a chave **anon public**.

## 4. Colar no código
No arquivo do sistema, procure o bloco **CONFIGURAÇÃO DO SUPABASE**, perto do início da lógica:

```js
const SUPABASE_URL = 'COLE_AQUI_A_PROJECT_URL';
const SUPABASE_ANON_KEY = 'COLE_AQUI_A_ANON_KEY';
```

Troque os textos entre aspas pelas suas chaves, salve o arquivo e faça o deploy na Vercel.

## 5. Conferir
Abra o sistema. No topo do painel, o indicador deve mostrar **Online** com a bolinha verde. A partir daí, tudo o que você, a Victoria e o João Victor fizerem aparece para todos na hora.

> Atenção: a chave "anon" fica visível no navegador. Com a regra acima, qualquer pessoa que tenha o link do sistema consegue ler e alterar os dados. Não compartilhe o link fora da equipe.
