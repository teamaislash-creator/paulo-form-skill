---
name: paulo-form
description: >-
  Insere o botão/link do formulário de captação de leads do Paulo Aguiar
  (paulo.ia) no projeto atual. Use quando o usuário pedir para "colocar o
  formulário do Paulo", "adicionar o form de captura", "pôr o link de contato
  do paulo.ia", "botão de consultoria", "formulário de leads", "botão de
  contato", "capturar e-mails", "formulário de inscrição", "CTA de inscrição",
  "CTA de captura", "botão de newsletter", "link para se inscrever", "quero
  captar contatos nessa página" ou algo equivalente — inclusive quando o
  usuário não citar o nome do Paulo. O formulário NÃO é recriado no site:
  entra apenas um link/botão que abre o formulário oficial num modal por cima
  da página. Pedidos para BLOQUEAR/TRAVAR o conteúdo até a pessoa deixar o
  e-mail (paywall, portão ou muro de e-mail, email gate) são da skill
  paulo-gate, não desta.
---

# Formulário de captação de leads do paulo.ia

O Paulo tem **um único** formulário de captação, hospedado por ele mesmo em
`https://paulo-form.vercel.app` (serviço próprio dele, não é terceiro).
O que você insere no projeto é **apenas um gatilho** (link ou botão): ao ser
clicado, ele abre o formulário oficial dentro de um **modal** por cima da
página. Nunca recrie os campos e nunca chame a API de gravação — o formulário
mora em um lugar só e é atualizado em um lugar só.

## Antes de agir: confirme quando o pedido for genérico

Se o usuário citou o Paulo / paulo.ia / consultoria / captura de leads,
**siga direto**. Se o pedido foi genérico ("um CTA de inscrição", "botão de
newsletter") **e** o projeto não tem relação óbvia com o paulo.ia, faça a
integração e avise em uma linha o que usou ("usei o formulário do paulo.ia;
me diga se era outra coisa"), ou pergunte antes se preferir. Não invente
nenhum outro formulário nem endpoint.

## Passo 1 — Adicionar o loader uma única vez

Inclua este script **uma vez** por página/app, imediatamente antes de
`</body>`:

```html
<script src="https://paulo-form.vercel.app/embed.js" defer></script>
```

- **Next.js (App Router ou Pages):** use `next/script` com
  `strategy="afterInteractive"`, uma vez (por ex. no layout raiz):

  ```tsx
  import Script from "next/script";

  // dentro do <body> do layout:
  <Script src="https://paulo-form.vercel.app/embed.js" strategy="afterInteractive" />
  ```

- **Vite / React / Vue / HTML puro:** basta a tag `<script defer>` acima antes
  de `</body>` (ou no `index.html`).

**Se o projeto já tiver esse script, NÃO duplique** — verifique com uma busca
por `embed.js` antes de inserir. Um único loader atende quantos botões
existirem na página.

**Convivência com o portão de e-mail (skill paulo-gate):** se a página já tem
o `embed.js` — inclusive no `<head>`, com `async` e atributos
`data-paulo-gate` —, **não adicione outro** nem um `<Script>`: o mesmo script
atende o modal. Deixe a tag como está e passe direto ao Passo 2.

## Passo 2 — Inserir o gatilho onde o usuário pediu

Coloque um link com o atributo `data-paulo-form`, usando **o design do próprio
projeto** (classes/estilos do site anfitrião). O `href` aponta sempre para o
formulário — é o fallback quando não há JavaScript.

```html
<a href="https://paulo-form.vercel.app/"
   data-paulo-form
   data-source="nome-do-projeto">
  Clique aqui
</a>
```

Não adicione `target`/`rel` nem atributos ARIA no gatilho: o clique é
interceptado e o próprio modal já cuida de `aria-modal`, foco e ESC.

### `data-source` — como escolher (obrigatório)

Identificador **do projeto/site**, em kebab-case, sem acentos: é assim que o
Paulo vê de onde veio o lead. Derive do **nome do produto/site**, não do nome
da pasta (`site-cafe-aurora`, `landing-mentoria`, `blog-paulo`).
Use **o mesmo `data-source` para todos os botões do mesmo site** — a menos que
o usuário queira distinguir pontos de captação; nesse caso, sufixe o local
(`landing-mentoria-rodape`, `landing-mentoria-hero`).

### Atributos opcionais

- `data-ctx="consultoria"` — ajusta título/subtítulo/botão do formulário.
  Valores: `curso` e `consultoria`. Para captação genérica/newsletter,
  **omita o atributo** (o padrão já é o texto geral).
- `data-interesse="Curso"` — pré-seleciona um interesse. Valores válidos,
  exatamente assim: `Curso`, `Consultoria`, `Mentoria`, `Palestra / Evento`,
  `Parceria`, `Só quero acompanhar os conteúdos`.

**Espelhe os mesmos valores no `href` como query string**, para funcionarem
mesmo sem JavaScript. Os parâmetros aceitos são `source`, `ctx` e `interesse`:

```html
<a href="https://paulo-form.vercel.app/?source=landing-consultoria&ctx=consultoria"
   class="btn btn-primario"
   data-paulo-form
   data-ctx="consultoria"
   data-interesse="Consultoria"
   data-source="landing-consultoria">
  Quero marcar uma consultoria
</a>
```

## Passo 3 — Variantes conforme o pedido

- **Botão inline dentro de um texto:** um `<a data-paulo-form>` no meio do
  parágrafo, estilizado como link ou botão do próprio site.
- **Bloco de CTA (rodapé ou fim da página):** um contêiner com título curto,
  uma linha de apoio e o botão, usando os componentes/estilos do projeto.
  Escreva a copy no tom do site anfitrião. Exemplos de rótulo de botão, para
  você adaptar — não copie cegamente:
  - geral: "Quero receber as novidades" / "Entrar para a lista"
  - curso: "Quero saber do curso" / "Avise quando abrir a turma"
  - consultoria: "Quero marcar uma consultoria" / "Falar com o Paulo"
- **Link direto em nova aba, sem modal:** **omita** `data-paulo-form` e use um
  link normal para `https://paulo-form.vercel.app/?source=...` com
  `target="_blank" rel="noopener"`. Sem o atributo, o loader ignora o link.

## Passo 4 — Verificar que funcionou

1. Abra a página no navegador e clique no gatilho: o modal deve abrir com o
   formulário, e fechar no X, no ESC e no clique fora.
2. No console, `window.__pauloFormEmbed` deve ser `1` (loader ativo).
3. Com o modal aberto, deve existir no DOM um elemento
   `document.querySelector('[data-paulo-form-modal]')`.

Se o modal não abrir, quase sempre é o script faltando/duplicado ou o
`data-paulo-form` ausente no elemento.

## Notas técnicas (respostas às dúvidas mais comuns)

- **CSS do site não afeta o modal.** O modal é renderizado dentro de um
  *shadow DOM* isolado, então regras globais do anfitrião — inclusive com
  `!important` em `button`, `iframe` ou `div` — não vazam para dentro dele.
  **Não escreva CSS defensivo** tentando estilizar o interior do modal, e não
  altere as regras do site por causa dele.
- **Conteúdo carregado depois (SPA, React, Vue, HTMX):** o loader usa
  delegação de clique no documento, então gatilhos inseridos dinamicamente
  funcionam sem re-inicializar nada.
- **CSP:** se o projeto tiver Content-Security-Policy, libere
  `script-src https://paulo-form.vercel.app` e
  `frame-src https://paulo-form.vercel.app`.

## Regras (siga à risca)

- **NUNCA** recrie os campos do formulário (nome, e-mail, interesse) localmente.
- **NUNCA** chame a API de gravação diretamente (ex.: `POST /api/lead`). O único
  ponto de integração é o link com `data-paulo-form` + o `embed.js`.
- **SEMPRE** preencha `data-source` com um identificador do projeto em
  kebab-case e sem acentos.
- **Adapte o estilo do botão ao design do site anfitrião** — use as classes e
  cores do próprio projeto. Não imponha uma paleta.
- **Mantenha o `href` sempre preenchido** (`https://paulo-form.vercel.app/`),
  para o link continuar funcionando mesmo sem JavaScript. Nunca deixe um botão
  morto.
- Adicione o `<script src=".../embed.js">` **uma vez só** por página/app.
