# Como ativar a integração com o Instagram (Meta) — Fase 1

Usa a **API oficial do Instagram com login do Instagram** (Instagram API with Instagram Login). Funciona com contas **profissionais** (Empresa ou Criador de conteúdo). Não precisa de Página do Facebook. A senha do cliente nunca passa pelo sistema.

## 1. Criar o app na Meta (uma vez)
1. Acesse https://developers.facebook.com/apps → **Criar app** → caso de uso **"Gerenciar mensagens e conteúdo no Instagram"** (ou tipo *Empresa*).
2. No painel do app: **Instagram → Configuração da API com login do Instagram**.
3. Em **Configurar login empresarial do Instagram**, cadastre o **URL de redirecionamento OAuth**:
   `https://hwvuyeaahowpezwwrgxh.supabase.co/functions/v1/meta-instagram`
4. Anote o **ID do app do Instagram** e a **Chave secreta do app do Instagram** (são diferentes do ID do app do Facebook).
5. Permissões usadas: `instagram_business_basic` e `instagram_business_manage_insights`.
6. Enquanto o app estiver em **modo de desenvolvimento**, só funcionam contas adicionadas em **Funções do app → Testadores do Instagram** (cada cliente precisa aceitar o convite em Instagram → Configurações → Apps e sites). Para conectar qualquer cliente sem convite, é preciso enviar o app para **Análise do app** da Meta e publicar.

## 2. Criar a tabela segura (uma vez)
Supabase → **SQL Editor** → cole `supabase/instagram.sql` → **Run**. A tabela `rise_ig_tokens` guarda os tokens com RLS sem políticas (ninguém lê pelo navegador).

## 3. Publicar a função
Supabase → **Edge Functions → Deploy a new function → Via Editor**
- Nome: `meta-instagram`
- Cole o conteúdo de `supabase/functions/meta-instagram/CODIGO-PARA-COLAR.txt` → **Deploy**
- Em **Details**, desative **Enforce JWT Verification** (o retorno da Meta chega sem JWT; o `state` assinado protege o fluxo).

## 4. Secrets da função (Edge Functions → Secrets)
| Nome | Valor |
|---|---|
| `META_APP_ID` | ID do app do Instagram |
| `META_APP_SECRET` | Chave secreta do app do Instagram |
| `META_REDIRECT_URI` | `https://hwvuyeaahowpezwwrgxh.supabase.co/functions/v1/meta-instagram` |
| `APP_RETURN_URL` | Endereço do sistema na Vercel, ex.: `https://agencia-rise.vercel.app/` |
| `META_GRAPH_VERSION` | opcional (padrão `v23.0`) |

## 5. Usar
Clientes → card → **•••** → Dossiê → aba **Instagram** → **Conectar Instagram**. Entre com o Instagram do cliente, autorize, e o sistema volta já com a conta vinculada e as métricas carregadas.

## O que é salvo
- Token: só em `rise_ig_tokens` (servidor). Renovado automaticamente quando faltam menos de 15 dias para expirar (token de longa duração dura ~60 dias).
- No sistema (linha `igAccounts`): @usuário, foto, seguidores, publicações, métricas agregadas de 7 e 30 dias e um histórico diário de seguidores (para comparações futuras).
- Métricas pedidas: alcance, visualizações, contas com interação, interações e toques em links do perfil. Se a Meta não oferecer alguma para a conta (ex.: contas com menos de 100 seguidores), ela aparece como indisponível, nunca com valor inventado.
