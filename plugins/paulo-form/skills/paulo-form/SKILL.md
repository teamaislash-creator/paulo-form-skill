---
name: paulo-form
description: >-
  Insere o botão/link do formulário de captação de leads do Paulo Aguiar
  (paulo.ia) no projeto atual. Use quando o usuário pedir para "colocar o
  formulário do Paulo", "adicionar o form de captura", "pôr o link de contato
  do paulo.ia", "botão de consultoria", "formulário de leads", "botão de
  contato do Paulo", "capturar e-mails", "formulário de inscrição do paulo.ia"
  ou algo equivalente. O formulário NÃO é recriado no site: entra apenas um
  link/botão que abre o formulário oficial num modal por cima da página.
---

# Formulário de captação de leads do paulo.ia

O Paulo tem **um único** formulário de captação, hospedado e centralizado.
O que você insere no projeto do usuário é **apenas um gatilho** (link ou botão):
ao ser clicado, ele abre o formulário oficial dentro de um **modal** por cima
da página. Nunca recrie os campos do formulário e nunca chame a API de gravação
direto — o formulário mora em um lugar só e é atualizado em um lugar só.

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

Se o projeto já tiver esse script, **não duplique**.

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

- `data-source` identifica de onde veio o lead (aparece no painel do Paulo).
  Preencha **sempre**, em **kebab-case**, com um identificador do projeto
  (ex.: `site-curso-ia`, `landing-mentoria`, `blog-artigo-x`).
  Se você omitir, o sistema usa o hostname do site automaticamente — mas
  prefira sempre um valor explícito.

### Atributos opcionais

- `data-ctx="consultoria"` — muda o título/subtítulo/botão do formulário para o
  contexto certo. Valores: `geral` (padrão), `curso`, `consultoria`. Use
  `consultoria` num botão de consultoria, `curso` numa página de curso.
- `data-interesse="Curso"` — pré-seleciona um interesse. Valores válidos:
  `Curso`, `Consultoria`, `Mentoria`, `Palestra / Evento`, `Parceria`,
  `Só quero acompanhar os conteúdos`.

Exemplo de um botão de consultoria já com o contexto certo:

```html
<a href="https://paulo-form.vercel.app/?ctx=consultoria"
   class="btn btn-primario"
   data-paulo-form
   data-ctx="consultoria"
   data-source="landing-consultoria">
  Quero marcar uma consultoria
</a>
```

## Passo 3 — Variantes conforme o pedido

- **Botão inline dentro de um texto:** um `<a data-paulo-form>` no meio do
  parágrafo, estilizado como link ou botão do próprio site.
- **Bloco de CTA no rodapé:** um contêiner com título curto + o botão
  `data-paulo-form`, usando os componentes/estilos do projeto.
- **Link direto em nova aba, sem modal:** basta **omitir** `data-paulo-form` e
  usar um link normal para `https://paulo-form.vercel.app/` (opcionalmente com
  `?source=...`) com `target="_blank" rel="noopener"`. Sem o atributo, o
  embed.js ignora o link e ele abre a página normalmente.

## Regras (siga à risca)

- **NUNCA** recrie os campos do formulário (nome, e-mail, interesse) localmente.
- **NUNCA** chame a API de gravação diretamente (ex.: `POST /api/lead`). O único
  ponto de integração é o link com `data-paulo-form` + o `embed.js`.
- **SEMPRE** preencha `data-source` com um identificador do projeto em
  kebab-case.
- **Adapte o estilo do botão ao design do site anfitrião** — use as classes e
  cores do próprio projeto. Não imponha uma paleta.
- **Mantenha o `href` sempre preenchido** (`https://paulo-form.vercel.app/`),
  para o link continuar funcionando mesmo sem JavaScript. Nunca deixe um botão
  morto.
- Adicione o `<script src=".../embed.js">` **uma vez só** por página/app.
