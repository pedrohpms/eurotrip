# ✈️ EuroTrip 2026 — app de viagem com base única no Supabase

App de apoio à viagem da família pela Europa (22/07–15/08/2026), publicado em **https://pedrohpms.github.io/eurotrip**. O repositório contém **apenas o shell** (`index.html`, sem nenhum dado pessoal). Os dados vivem numa **base única no Supabase**, acessível só pelos 2 adultos mediante login — e ficam **espelhados nos 2 celulares**: uma edição feita num aparelho aparece no outro em até ~60 s (ou ao reabrir/focar o app).

## 🧱 Arquitetura

| Peça | Onde | O que guarda |
|---|---|---|
| `index.html` | GitHub Pages (público) | Só código: login, leitura cache-first, escrita direta via REST |
| Tabela `viagem` | Supabase | JSONB com o que quase não muda: família, seguro, hospedagens, transportes/pernas, textos das cidades |
| Tabela `planos` | Supabase | Uma linha por plano do dia (data, cidade, texto, busca do Maps, ordem) — editável no app |
| Tabela `desafios` | Supabase | Desafios das crianças (semente + personalizados) e progresso |
| `localStorage` | Cada celular | Cache da última leitura (leitura offline) + sessão do login |
| `setup-supabase.sql` | **Só local — NUNCA no GitHub** | Criação das tabelas, RLS e seed com os dados pessoais |

- **Leitura (cache-first):** o app abre na hora com o cache e refaz a leitura completa em segundo plano — ao abrir, ao voltar o foco, ao evento `online` e a cada ~60 s.
- **Escrita (direta, exige conexão):** cada edição faz upsert/delete imediato no Supabase (1 retentativa automática). Sem conexão, o app avisa "Sem conexão — alteração não salva" e **não finge sucesso**. Sem fila offline, por decisão de projeto.
- **Concorrência:** last-write-wins por linha (2 usuários, dezenas de linhas — suficiente).
- **Indicador:** 🟢 conectado / 🔴 sem conexão (somente leitura) no topo.
- **Segurança:** anon key é pública por design; a proteção é o **RLS** — toda leitura/escrita exige usuário autenticado **e** e-mail na lista fixa (função `eh_autorizado()` no SQL). Signups públicos desativados.

## 🛠️ Setup (fazer uma vez)

1. **Criar o projeto** em [supabase.com](https://supabase.com) → New project. Região: **Europa** (ex.: Frankfurt `eu-central-1` ou Londres `eu-west-2`), para menor latência durante a viagem.
2. **Editar o `setup-supabase.sql`** (local): trocar os 2 e-mails de `eh_autorizado()` pelos e-mails reais do casal.
3. **Rodar o SQL**: dashboard → SQL Editor → colar o arquivo inteiro → Run. Isso cria as 3 tabelas, liga o RLS e faz o seed com os dados da viagem.
4. **Criar os 2 usuários**: Authentication → Users → Add user (e-mail + senha, os MESMOS e-mails do passo 2, marcar "Auto confirm").
5. **Desativar signups públicos**: Authentication → Sign In / Providers → desligar "Allow new users to sign up".
6. **Configurar o app**: em Project Settings → API, copiar a **Project URL** e a **anon/publishable key** para as constantes `SUPABASE_URL` e `SUPABASE_ANON_KEY` no topo do `<script>` do `index.html`; commitar e publicar (GitHub Pages).
7. **Testar nos 2 celulares**: abrir https://pedrohpms.github.io/eurotrip, fazer login, conferir os dados. Dica: menu ⋮ → "Adicionar à tela inicial".

**Free tier:** projetos gratuitos são **pausados após ~7 dias sem atividade** de banco; o uso diário na viagem evita isso. Se pausar depois da viagem, basta Restore no dashboard (dados preservados). Detalhes: [Project Pausing](https://supabase.com/docs/guides/platform/free-project-pausing).

## ✍️ Edição dos planos do dia

- Cada plano no cronograma tem **✏️** (editar texto, mover para outra data, mudar a busca do Maps) e, quando o dia tem mais de um plano, **▲▼** para reordenar.
- **➕ Adicionar plano** no rodapé de cada dia. Se a busca do Maps ficar vazia, o app usa o texto + a cidade do dia; o botão "🔎 Usar o texto" faz o mesmo no editor.
- Excluir pede confirmação.
- A aba **Guia** lista o "Roteiro & Atrações" direto da tabela `planos` (mesma fonte do cronograma — sem duplicação). Segredos, armadilhas e dicas continuam no JSONB (somente leitura nesta versão).
- Transportes, hospedagens, seguro e passaportes são somente leitura no app; para ajustar, edite `viagem.dados` no dashboard (Table Editor) — a Fase 2 (pós-viagem) trará edição completa e cadastro de novas viagens.

## ✅ Teste de aceitação

1. Editar um plano no celular A → aparece no B em até ~60 s (ou ao reabrir/focar o app no B).
2. Mover um plano para outra data no A → reflete no cronograma e no Guia do B.
3. Marcar desafio e criar desafio novo no B → aparece no A.
4. **Modo avião:** o app abre normalmente com os dados do cache e indicador 🔴; tentar editar exibe "Sem conexão — alteração não salva. Tente novamente." sem quebrar nem fingir sucesso.
5. Excluir um plano no A → some do B no próximo refetch.
6. **RLS:** `curl 'https://SEU-PROJETO.supabase.co/rest/v1/planos?select=*' -H "apikey: ANON_KEY" -H "Authorization: Bearer ANON_KEY"` **sem login** deve retornar `[]` (nenhuma linha) — a anon key não lê nada.
7. SOS → Conta & Backup → **Sair** → o app volta para a tela de login (e limpa o cache local).

## 🔒 GitHub — o que pode e o que não pode subir

| Arquivo | Pode? |
|---|---|
| `index.html`, `README.md`, `manifest.json`, ícones | ✅ (a URL e a anon key do Supabase podem ser públicas — o RLS protege) |
| `setup-supabase.sql` | ❌ **NUNCA** — contém passaportes, vouchers, endereços e reservas |
| `dados-viagem.xml` (legado) | ❌ **NUNCA** — mesmos dados pessoais |
| `backup-eurotrip2026*.json` | ❌ |

O `.gitignore` já cobre todos os proibidos.

## 💾 Backup (plano B)

SOS → Conta & Backup → **Exportar backup** gera um JSON com o retrato atual (viagem + planos + desafios) para copiar/baixar. Restauração, se um dia for preciso, é manual via dashboard do Supabase — as alterações do dia a dia já ficam salvas na base.

## 🗂️ Legado

O fluxo antigo (importação de `dados-viagem.xml` por aparelho) foi substituído pela base única. O XML permanece na pasta apenas como referência histórica dos dados.
