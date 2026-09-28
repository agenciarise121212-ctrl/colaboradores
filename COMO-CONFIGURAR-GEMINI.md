# Agência Rise · Gemini via Supabase Edge Function

## 1. O que foi criado
- Edge Function: `supabase/functions/generate-weekly-stories/index.ts`
  - `mode: "week"` gera a semana inteira de um cliente.
  - `mode: "story"` refaz um único Story. Ações aceitas: `regenerate`, `more_natural`, `shorter`, `more_commercial` e `improve_speech`.
- A função busca o cliente, o contexto para IA e o histórico recente direto no banco, com a service role. Do frontend recebe só os IDs, a semana, o número de Stories por dia e as observações.
- A resposta do Gemini chega em JSON estruturado (responseSchema) e é validada antes de voltar para o frontend.
- `supabase/config.toml`: a função fica com `verify_jwt = false`, porque o sistema ainda não usa o login do Supabase.
- `.env.example` (só com `GEMINI_API_KEY=`) e `.gitignore` (ignora `.env`).

## 2. Banco de dados
- Nenhuma tabela nova e nenhuma alteração destrutiva. Tudo continua na tabela `rise_data`.
- Nova linha `id = 'stories'` com os planejamentos por semana (cliente → semana → dias → stories) e as tarefas semanais. Ela é criada automaticamente pelo sistema.
- Dentro de cada cliente (linha `clients`) entraram os campos `ai`, `planOwner`, `execOwner` e `storyDays`.
- O RLS da `rise_data` não mudou.

## 3. Secrets (Supabase → Edge Functions → Secrets)
- `GEMINI_API_KEY`: obrigatório. É a sua chave.
- `ALLOWED_ORIGINS`: recomendado. Coloque a URL do sistema na Vercel, por exemplo `https://agencia-rise.vercel.app`. Assim outros sites não conseguem usar a função.
- `GEMINI_MODEL`: opcional. O padrão é `gemini-2.5-flash`.
- `REQUIRE_AUTH=true`: só depois que a etapa de segurança (Supabase Auth) for implementada.
- `SUPABASE_URL` e `SUPABASE_SERVICE_ROLE_KEY` já existem por padrão nas Edge Functions. Não cadastre.

## 4. Deploy
```bash
npm i -g supabase
supabase login
supabase link --project-ref hwvuyeaahowpezwwrgxh
supabase secrets set GEMINI_API_KEY=SUA_CHAVE
supabase secrets set ALLOWED_ORIGINS=https://SEU-SITE.vercel.app
supabase functions deploy generate-weekly-stories --no-verify-jwt
```
Outra opção é pelo painel: Edge Functions → Deploy a new function → nome `generate-weekly-stories`. Cole o conteúdo do `index.ts` e desative "Verify JWT".

## 5. Testar
- Pelo sistema: abra Planejamento Semanal, escolha um cliente e clique em "Gerar roteiro da semana com IA". Depois use "Refazer com IA" em um Story.
- Pelo terminal:
```bash
curl -X POST https://hwvuyeaahowpezwwrgxh.supabase.co/functions/v1/generate-weekly-stories \
  -H "apikey: sb_publishable_j1QQxCpjY7iBj1cOFu6IMg_95bfXqxe" -H "Content-Type: application/json" \
  -d '{"mode":"week","clientId":"ID_DO_CLIENTE","weekStart":"2026-10-05","perDay":4}'
```
- Logs: Edge Functions → generate-weekly-stories → Logs. Os logs trazem só códigos como `week_ok`, `gemini_error` e `retry_invalid_json`, nunca a chave.

## 6. Confirmar que a chave não está exposta
- No navegador: F12 → Network. A chamada vai para `/functions/v1/generate-weekly-stories` e nunca para `generativelanguage.googleapis.com`.
- Procure `GEMINI` e `AIza` no código-fonte do `index.html` (Ctrl+U): não deve aparecer nada.
- Rode `git grep -n "AIza"` no repositório: não deve retornar nada.
- A resposta da função traz apenas `ok`, `dias`, `story` ou `message`.

## 7. Configuração manual no painel
- Cadastrar os Secrets do item 3.
- Desativar "Verify JWT" na função, caso o deploy seja feito pelo painel.

## Limitação atual
Sem o login do Supabase, a função não tem como identificar qual usuário fez o pedido. Por enquanto a proteção é feita por três camadas: `ALLOWED_ORIGINS`, limite de requisições por IP e leitura de dados apenas no banco. Quando a etapa de segurança pendente for implementada, basta ligar `REQUIRE_AUTH=true` e `verify_jwt = true`.
