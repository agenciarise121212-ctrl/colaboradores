# Ativar o portal de aprovação seguro

Até você rodar o SQL, o sistema continua funcionando como hoje. Depois de rodar, ele passa sozinho para o modo seguro.

1. No Supabase, abra **SQL Editor → New query**.
2. Cole todo o conteúdo de `supabase/aprovacao-e-seguranca.sql` e clique em **Run**. O resultado deve ser "Success".
3. Publique na Vercel o `index.html` novo da pasta `deploy`.
4. Abra o sistema. Cada pessoa entra uma vez com a senha e depois continua conectada.

## O que muda
- A tabela `rise_data` fica fechada ao público. Só dá para ler ou gravar os dados com uma sessão válida, criada no login.
- As senhas passam a ficar no banco, com criptografia. Há limite de 8 tentativas a cada 10 minutos.
- O link do cliente só enxerga a própria demanda. O token é longo e aleatório e pode ser desativado. Há limite de acessos e de respostas, e os comentários são limpos antes de salvar.
- Colaboradores veem só os próprios pagamentos. O financeiro completo fica restrito ao admin.
- As funções do Gemini continuam funcionando, porque usam a chave de serviço.

## Depois de rodar
- Apague o arquivo SQL do GitHub, se ele foi enviado, porque contém as senhas iniciais.
- Para trocar uma senha, peça no chat.
