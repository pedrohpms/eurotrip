# ✈️ App Viagem Europa 2026

App offline de apoio a uma viagem em família pela Europa. É um único arquivo (`index.html`) que funciona 100% sem internet — cronograma dia a dia, guia das cidades, desafios das crianças e informações de emergência (SOS).

> **Importante:** o `index.html` vai **"em branco"** — nenhum dado pessoal ou de roteiro fica no código, para que ele possa ser publicado no GitHub. Todos os dados (família, passaportes, vouchers, hotéis, bilhetes, roteiro e desafios) vivem **apenas** no arquivo local `dados-viagem.xml`, que é carregado no app pelo botão **Importar dados**.

## 🔒 GitHub — o que pode e o que não pode subir

| Arquivo | Pode ir para o GitHub? |
|---|---|
| `index.html` | ✅ Sim — não contém nenhum dado pessoal |
| `README.md` | ✅ Sim |
| `dados-viagem.xml` | ❌ **NUNCA** — contém passaportes, vouchers e endereços |

Se for criar o repositório, adicione um `.gitignore` com a linha:

```
dados-viagem.xml
```

## 📱 Instalação no celular (Android / Chrome)

1. Copie **os dois arquivos** — `index.html` e `dados-viagem.xml` — para o celular, por WhatsApp, e-mail, cabo USB ou pelo app do OneDrive (use "Salvar no dispositivo"). Recomendado: pasta **Downloads** do armazenamento interno.
2. Abra o app **Arquivos** (ou "Files"), localize o `index.html` e toque nele.
3. Se perguntar com qual app abrir, escolha **Chrome**.
4. O app abre vazio, com o aviso "Nenhum dado carregado". Toque em **📂 Importar dados** e selecione o `dados-viagem.xml`. Isso é feito **uma única vez** — os dados ficam salvos no aparelho.
5. Pronto! Dica: no Chrome, toque em ⋮ → **Adicionar à tela inicial** para criar um atalho com cara de aplicativo.

> **Importante:** abra sempre pelo **mesmo caminho/arquivo**. O progresso dos desafios fica salvo no navegador vinculado àquele arquivo — se abrir por outro caminho (ou reenviar o arquivo), o progresso aparece zerado.

## ✅ Teste rápido após instalar (com os dados já importados)

1. Ative o **modo avião** e recarregue a página — tudo deve continuar funcionando (o app não usa internet e os dados importados ficam no aparelho).
2. Na aba **🏆 Desafios**, marque um desafio e cadastre um novo.
3. Feche o Chrome por completo (dispense-o das apps recentes), reabra o arquivo e confira que o progresso e o novo desafio continuam lá.
4. Se **não** persistirem (pode acontecer quando o Android abre o arquivo via `content://`), use o plano B: na aba **🆘 SOS → Dados & Backup**, toque em **Exportar backup**, copie o texto para as Notas/WhatsApp e restaure depois com **Importar backup**. Ou mova o `index.html` para o armazenamento interno e abra sempre por lá.

## 🗂️ Arquivos

| Arquivo | Para que serve |
|---|---|
| `index.html` | O app "em branco" — a casca pública, sem dados |
| `dados-viagem.xml` | **Todos os dados da viagem** — obrigatório importar no primeiro uso |
| `README.md` | Este guia |

## ✏️ Como atualizar os dados da viagem

Edite o `dados-viagem.xml` num editor de texto, envie-o ao celular e importe de novo em **🆘 SOS → Dados & Backup → Importar dados**. O progresso dos desafios é preservado (a mesclagem é feita pelo `id` de cada desafio); os desafios cadastrados pelo app também são mantidos.

Regras do XML:

- Mantenha a estrutura da raiz `<viagem>` (familia, seguro, hospedagens, transportes, cidades, desafios) e datas no formato `AAAA-MM-DD`.
- **Passaportes:** preencha em cada `<membro>` os atributos `passaporte="NÚMERO"` e `validadePassaporte="AAAA-MM-DD"`. Eles aparecem no card **🛂 Passaportes** da aba SOS — com alerta automático se a validade vencer a menos de 6 meses do fim da viagem (exigência comum na imigração).
- **Reservas:** em cada `<transporte>`, o atributo `pnr="CÓDIGO"` guarda o localizador/código de reserva (TAP, Eurostar, DB…) e `<assentos>` o vagão/assentos — ambos aparecem na gaveta "[+ info]" do cronograma e no card Transportes da aba SOS. `<mapsPartida>` (transportes) e `<mapsQuery>` (hospedagens) criam o botão "📍 Abrir no Maps"; `<aviso>` mostra alertas de horário em destaque.
- Itens de cidade com atributo `data="AAAA-MM-DD"` aparecem também no cronograma do dia; sem o atributo, ficam só no guia da cidade.
- **Localização das atrações — um plano por lugar:** cada `<item>` representa **um** lugar, com sua `data` e sua busca `maps="consulta do Google Maps"`. Vários itens na mesma data viram vários planos naquele dia, cada um com o próprio botão "📍" e o próprio ✏️ de edição. (O formato legado `<local nome="..." maps="..."/>` dentro de um item ainda é aceito, mas prefira itens separados.)
- O atributo opcional `emoji` em `<membro>` personaliza o ícone nos desafios e passaportes.

## ✍️ Editar os planos de cada dia (no próprio app)

Cada card de dia no cronograma tem um **✏️** ao lado de cada plano e um botão **➕ Adicionar plano** no rodapé. O editor permite mudar o texto, mover o plano para outro dia e definir a **busca do Maps**:

- O botão **🔎 Usar o texto** preenche a busca com o texto do plano + a cidade do dia — assim a busca acompanha o que você editou.
- Em planos novos, se a busca ficar vazia, o app usa automaticamente o texto + cidade.
- Se o plano tinha vários locais (📍) vindos do XML, alterar a busca os substitui por uma busca única (o app avisa antes).

As edições ficam salvas **no aparelho** (localStorage) e entram no **Exportar backup** (junto com os desafios). Atenção: **Importar dados** (XML) substitui os planos do roteiro — edições feitas no app sobre itens do XML são perdidas; apenas os planos **criados** no app (➕) são preservados. Antes de reimportar o XML, exporte um backup.

## 🧭 Uso rápido

- **🏠 Cronograma** — filtros "Hoje" (com contagem regressiva antes do embarque), "Próximos Dias" e "Ver Tudo".
- **🗺️ Guia** — abas por cidade: roteiro, segredos locais, armadilhas a evitar e dicas de transporte.
- **🏆 Desafios** — gincana da Olívia e do Lucas, com barra de progresso e cadastro de novos desafios.
- **🆘 SOS** — telefones do seguro (tocáveis), vouchers, endereços das hospedagens, bilhetes e backup.
- **🌙/☀️** — botão no topo alterna tema claro/escuro (a escolha fica salva).

Boa viagem! 🇬🇧🇫🇷🇧🇪🇩🇪🇵🇹
