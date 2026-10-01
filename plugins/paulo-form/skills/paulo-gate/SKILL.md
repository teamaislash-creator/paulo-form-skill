---
name: paulo-gate
description: >-
  Trava o conteúdo do site atrás do portão de e-mail do paulo.ia (email gate):
  a página fica borrada sob um cartão que pede só o e-mail e libera o acesso na
  hora. Use quando o usuário pedir "coloca o paywall", "adiciona o portão de
  email", "bloqueia o conteúdo até a pessoa deixar o email", "email gate",
  "trava o site com captura de email", "paywall de email", "muro de email",
  "travar a página", "liberar o conteúdo só com email", "pedir o email antes de
  mostrar o conteúdo", "content gate", "lead gate", "só deixa ver depois de
  cadastrar o email" ou algo equivalente, inclusive sem citar o Paulo. NÃO use
  para botão/link que abre o formulário de leads (isso é a skill paulo-form)
  nem para paywall de pagamento/assinatura: esta skill é só para BLOQUEAR/TRAVAR
  o conteúdo até a pessoa deixar o e-mail.
---

# Portão de e-mail (email gate) do paulo.ia

O Paulo tem um portão de e-mail pronto, servido pelo **mesmo script** do
formulário dele: `https://paulo-form.vercel.app/embed.js` (serviço próprio
dele, não é terceiro). Com o portão ativado, o site aparece **borrado e
escurecido** atrás de um cartão que pede **só o e-mail**. Ao enviar, o portão
some com uma transição suave e não aparece mais naquele site, naquele
navegador. O e-mail cai no painel do Paulo, marcado como vindo do portão.

Você **não constrói nada**: insere duas coisas no `<head>` — um snippet
anti-flash (copiado exatamente) e a tag do `embed.js` com `data-paulo-gate` —
e escolhe o texto do cartão. Cartão, validação, envio, linha de consentimento
(LGPD), acessibilidade e a memória de quem já liberou são todos do script.

Para explicar ao usuário, se ele perguntar:

- O conteúdo **continua no HTML** (SEO intacto); o portão é uma camada por
  cima. Por isso **não é proteção de conteúdo pago ou sigiloso** — é captação
  de lead.
- Se algo falhar (rede, servidor fora, demora), o portão **libera o acesso**
  mesmo assim e guarda o e-mail para reenviar depois. O site nunca fica
  trancado por falha.
- Liberou uma vez → não aparece mais naquele site (em nenhuma página, nem
  voltando dias depois), naquele navegador.
- O cartão já tem a linha de consentimento ("Ao continuar, você concorda em
  receber conteúdos e novidades do paulo.ia.") e sugere correção de e-mail
  digitado errado (ex.: gmial.com → gmail.com). Não adicione nada disso.

## Antes de agir: siga direto

- "Coloca o paywall", "trava o site", "bloqueia o conteúdo", "portão de
  e-mail", "email gate", "muro de e-mail" e afins → **siga direto e
  instale**, mesmo num projeto que não cita o Paulo: nesta equipe, "paywall"
  quer dizer este portão de e-mail. Não pergunte antes. No fim, avise em uma
  linha: "usei o portão de e-mail do paulo.ia (os e-mails vão para o painel do
  Paulo); se a ideia era cobrar pelo acesso, me avise".
- Só NÃO é esta skill quando o pedido fala explicitamente em cobrar:
  pagamento, assinatura, cartão, preço, checkout, Stripe, Hotmart, plano pago.
  Aí não instale nada daqui — e não invente nenhum outro portão nem endpoint.
- **Escopo:** "no site" ou sem especificar → site inteiro (snippet e tag no
  `<head>` compartilhado). "Nessa página" / "só na landing X" → só aquelas
  páginas (veja "Portão só em algumas páginas").

## Passo 1 — Snippet anti-flash no `<head>` (copie EXATAMENTE)

Cole este script inline no `<head>`, o mais cedo possível: logo depois de
`<meta charset>` / `<meta name="viewport">` e **antes** dos CSS e scripts do
site (em Next.js, siga o exemplo da seção do Next). Copie caractere por
caractere — não reformate, não "melhore", não traduza e não mova para um
arquivo externo (precisa ser inline para rodar antes de a tela ser pintada).

```html
<script>(function(d,h){try{if(localStorage.getItem("pauloform_gate"))return}catch(e){return}h=d.documentElement;h.setAttribute("data-paulo-gate-escudo","");var c="html[data-paulo-gate-escudo]{overflow:hidden!important}html[data-paulo-gate-escudo]::after{content:'';position:fixed;top:0;right:0;bottom:0;left:0;z-index:2147483646;background:rgba(6,14,26,.5);-webkit-backdrop-filter:blur(14px);backdrop-filter:blur(14px)}";try{var f=new CSSStyleSheet;f.replaceSync(c);d.adoptedStyleSheets=[].slice.call(d.adoptedStyleSheets).concat(f)}catch(e){var s=d.createElement("style"),k=d.currentScript;if(k&&k.nonce)s.nonce=k.nonce;s.textContent=c;(d.head||h).appendChild(s)}setTimeout(function(){h.removeAttribute("data-paulo-gate-escudo")},5000)})(document)</script>
```

O que ele faz: se a pessoa ainda não liberou o portão, borra e escurece a tela
**antes** de o conteúdo aparecer (sem o "flash" do site limpo) e trava a
rolagem até o cartão abrir. Não depende de rede. Trava de segurança: se o
`embed.js` não carregar, essa camada some sozinha em 5 segundos — o site nunca
fica trancado.

## Passo 2 — A tag do `embed.js` com `data-paulo-gate` (UMA vez só)

Logo depois do snippet, ainda no `<head>`:

```html
<script src="https://paulo-form.vercel.app/embed.js" async
        data-paulo-gate
        data-source="nome-do-site"></script>
```

É só isso: os textos padrão do cartão são genéricos e servem para qualquer
projeto sem editar nada (veja o Passo 3 para adaptar, se o usuário pedir).

**Antes de inserir, procure `embed.js` no projeto inteiro:**

- **Não existe:** adicione a tag acima.
- **Já existe** (a skill paulo-form instala esse mesmo script para os botões do
  formulário): **NÃO adicione outra.** Acrescente `data-paulo-gate`
  e `data-source` **na tag existente**. Se ela estiver no fim
  do `<body>` com `defer`, funciona assim mesmo; se der, mova-a para o
  `<head>`, logo depois do snippet, trocando `defer` por `async` (o portão
  aparece mais cedo).
- **Já existe como `<Script>` do `next/script`:** remova esse `<Script>` e
  coloque no lugar uma única tag `<script async>` no `<head>`, como na seção do
  Next.js abaixo. Os botões do formulário continuam funcionando com ela.

> **O `embed.js` é UM SÓ para o formulário e para o portão.** A mesma tag faz
> os botões `data-paulo-form` abrirem o modal **e** liga o portão. Duas tags
> `embed.js` na mesma página é erro.

### Atributos

| atributo | padrão | efeito |
|---|---|---|
| `data-paulo-gate` | — | liga o portão (basta estar presente; `"off"` desliga) |
| `data-source` | hostname do site | origem que aparece no painel do Paulo (kebab-case, sem acento) |
| `data-titulo` | "Quer acessar esse conteúdo gratuito?" | título do cartão |
| `data-subtitulo` | "Basta informar seu email para continuar" | subtítulo |
| `data-botao` | "Quero acessar" | texto do botão (aparece em CAIXA ALTA: QUERO ACESSAR) |
| `data-dismissible` | `false` | `"true"` = dá para fechar sem e-mail (X, ESC e clique fora); volta na próxima sessão |

## Onde colocar, por stack

Regra geral: o snippet e a tag têm que sair **no HTML servido**, dentro do
`<head>`, e o snippet tem que continuar **inline** (sem o framework empacotar,
adiar ou transformar em módulo).

### HTML puro

Em cada página que deve ficar travada (no site inteiro: em todas):

```html
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <script>(function(d,h){try{if(localStorage.getItem("pauloform_gate"))return}catch(e){return}h=d.documentElement;h.setAttribute("data-paulo-gate-escudo","");var c="html[data-paulo-gate-escudo]{overflow:hidden!important}html[data-paulo-gate-escudo]::after{content:'';position:fixed;top:0;right:0;bottom:0;left:0;z-index:2147483646;background:rgba(6,14,26,.5);-webkit-backdrop-filter:blur(14px);backdrop-filter:blur(14px)}";try{var f=new CSSStyleSheet;f.replaceSync(c);d.adoptedStyleSheets=[].slice.call(d.adoptedStyleSheets).concat(f)}catch(e){var s=d.createElement("style"),k=d.currentScript;if(k&&k.nonce)s.nonce=k.nonce;s.textContent=c;(d.head||h).appendChild(s)}setTimeout(function(){h.removeAttribute("data-paulo-gate-escudo")},5000)})(document)</script>
  <script src="https://paulo-form.vercel.app/embed.js" async
          data-paulo-gate
          data-source="nome-do-site"></script>
  <title>...</title>
  <link rel="stylesheet" href="estilo.css">
</head>
```

### Next.js — App Router (`app/layout.tsx` ou `src/app/layout.tsx`)

No layout raiz. Mantenha tudo o que o layout já tem (imports, `metadata`,
classes no `<html>`/`<body>`, fontes, providers); só acrescente
`suppressHydrationWarning` no `<html>` e as duas tags no começo do `<head>`
(crie o `<head>` se não existir):

```tsx
// Conteúdo EXATO do snippet do Passo 1, sem as tags <script> e </script>.
const PAULO_GATE_ANTIFLASH = `(function(d,h){try{if(localStorage.getItem("pauloform_gate"))return}catch(e){return}h=d.documentElement;h.setAttribute("data-paulo-gate-escudo","");var c="html[data-paulo-gate-escudo]{overflow:hidden!important}html[data-paulo-gate-escudo]::after{content:'';position:fixed;top:0;right:0;bottom:0;left:0;z-index:2147483646;background:rgba(6,14,26,.5);-webkit-backdrop-filter:blur(14px);backdrop-filter:blur(14px)}";try{var f=new CSSStyleSheet;f.replaceSync(c);d.adoptedStyleSheets=[].slice.call(d.adoptedStyleSheets).concat(f)}catch(e){var s=d.createElement("style"),k=d.currentScript;if(k&&k.nonce)s.nonce=k.nonce;s.textContent=c;(d.head||h).appendChild(s)}setTimeout(function(){h.removeAttribute("data-paulo-gate-escudo")},5000)})(document)`;

export default function RootLayout({
  children,
}: Readonly<{ children: React.ReactNode }>) {
  return (
    <html lang="pt-BR" suppressHydrationWarning>
      <head>
        <script dangerouslySetInnerHTML={{ __html: PAULO_GATE_ANTIFLASH }} />
        <script
          async
          src="https://paulo-form.vercel.app/embed.js"
          data-paulo-gate=""
          data-source="nome-do-site"
        />
      </head>
      <body>{children}</body>
    </html>
  );
}
```

- `suppressHydrationWarning` no `<html>` é obrigatório: o snippet marca o
  `<html>` antes de o React hidratar, e sem isso o React acusa erro de
  hidratação.
- Use tags `<script>` comuns, **não** o `<Script>` do `next/script`, para as
  duas. O React 19 mantém os atributos `data-*` na tag.
- Em JSX, escreva `data-paulo-gate=""` (presença basta).
- O snippet não tem crase nem `${`, então cabe intacto entre crases.
- No HTML gerado, o React sobe a tag `async` (e os CSS) para antes do
  snippet. É esperado e funciona; não tente reordenar.
- Layout em JavaScript (`layout.js`/`.jsx`): mesmo código, sem a anotação de
  tipo. Mais de um layout raiz (grupos de rotas, cada um com seu `<html>`):
  repita nos layouts que devem ficar travados.

### Next.js — Pages Router (`pages/_document.tsx`)

Dentro do `<Head>` do `_document` (se o arquivo não existir, crie-o assim).
Se houver um `<Script src=".../embed.js">` no `_app.tsx`, remova-o: fica só
esta tag. Aqui não precisa de `suppressHydrationWarning` (o `_document` não é
hidratado), e vale a mesma observação: no HTML gerado a tag `async` aparece
antes do snippet, e está tudo certo.

```tsx
import { Html, Head, Main, NextScript } from "next/document";

// Conteúdo EXATO do snippet do Passo 1, sem as tags <script> e </script>.
const PAULO_GATE_ANTIFLASH = `(function(d,h){try{if(localStorage.getItem("pauloform_gate"))return}catch(e){return}h=d.documentElement;h.setAttribute("data-paulo-gate-escudo","");var c="html[data-paulo-gate-escudo]{overflow:hidden!important}html[data-paulo-gate-escudo]::after{content:'';position:fixed;top:0;right:0;bottom:0;left:0;z-index:2147483646;background:rgba(6,14,26,.5);-webkit-backdrop-filter:blur(14px);backdrop-filter:blur(14px)}";try{var f=new CSSStyleSheet;f.replaceSync(c);d.adoptedStyleSheets=[].slice.call(d.adoptedStyleSheets).concat(f)}catch(e){var s=d.createElement("style"),k=d.currentScript;if(k&&k.nonce)s.nonce=k.nonce;s.textContent=c;(d.head||h).appendChild(s)}setTimeout(function(){h.removeAttribute("data-paulo-gate-escudo")},5000)})(document)`;

export default function Document() {
  return (
    <Html lang="pt-BR">
      <Head>
        <script dangerouslySetInnerHTML={{ __html: PAULO_GATE_ANTIFLASH }} />
        <script
          async
          src="https://paulo-form.vercel.app/embed.js"
          data-paulo-gate=""
          data-source="nome-do-site"
        />
      </Head>
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  );
}
```

### Vite / React / Vue / SPA (`index.html`)

Faça exatamente como no HTML puro, no `<head>` do `index.html` (na raiz do
projeto no Vite; em `public/index.html` no Create React App e no Vue CLI).
Cole as tags como estão — sem `type="module"` e sem importar nada em
componentes.

### Outros

- Template base que gera o `<head>` de todas as páginas: `header.php`
  (WordPress, antes de `wp_head()`), `theme.liquid` (Shopify), `base.html`
  (Django/Jinja), layout Blade (Laravel), `src/app.html` (SvelteKit),
  `app/root.tsx` (Remix/React Router — igual ao App Router, com
  `suppressHydrationWarning` no `<html>`), `app.head` no `nuxt.config` (Nuxt).
- **Astro:** use `is:inline` nas duas tags (`<script is:inline>...`); sem
  isso, o Astro pode empacotar o script e ele roda tarde demais.
- Construtores visuais (Webflow, Wix etc.): campo de "código personalizado no
  head" — snippet primeiro, tag depois.

## Passo 3 — Texto do cartão (padrão: não mexer)

O cartão já vem com textos **genéricos**, pensados para funcionar em qualquer
projeto sem ninguém editar:

- título: **Quer acessar esse conteúdo gratuito?**
- subtítulo: **Basta informar seu email para continuar**
- botão: **QUERO ACESSAR**
- consentimento (fixo, não muda): *Ao continuar, você concorda em receber
  conteúdos e novidades do paulo.ia.*

**Na instalação padrão, NÃO coloque `data-titulo`, `data-subtitulo` nem
`data-botao`.** Não invente textos de campanha (evento, brinde, área de membros):
o cartão tem só título, subtítulo, campo de e-mail, botão e consentimento.

Use os três atributos **só quando o usuário pedir** um texto específico ou
pedir para adaptar o portão ao conteúdo daquela página. Eles sobrescrevem o
padrão um a um (os que você não puser continuam no padrão). Escreva o botão em
texto normal — ele aparece em caixa alta. Curto: título com até ~8 palavras,
subtítulo de uma frase, botão de 2 a 5 palavras. Se o usuário ditou o texto,
use exatamente o que ele disse. Exemplo, para uma página que entrega um guia:

```html
<script src="https://paulo-form.vercel.app/embed.js" async
        data-paulo-gate
        data-source="guia-de-prompts"
        data-titulo="Baixe o guia de prompts"
        data-subtitulo="Informe seu email para liberar o download"
        data-botao="Quero o guia"></script>
```

Os mesmos atributos funcionam no elemento `data-paulo-gate` de uma página
(veja "Portão só em algumas páginas"), para um texto diferente só nela.

### `data-source` — como escolher (obrigatório)

Identificador **do projeto/site**, em kebab-case, sem acentos: é assim que o
Paulo vê de onde veio o lead. Derive do **nome do produto/site**, não do nome
da pasta (`site-cafe-aurora`, `landing-mentoria`, `blog-paulo`). Use **o mesmo
valor no site todo** — inclusive o mesmo dos botões do formulário, se o site
já tiver; o painel do Paulo já separa o que veio do portão e do formulário.

### `data-dismissible` — padrão: não usar

Por padrão o portão **é obrigatório** (só some com o e-mail). Coloque
`data-dismissible="true"` **só** se o usuário pedir algo como "dá pra fechar",
"não obrigatório", "a pessoa pode pular". Aí o X, o ESC e o clique fora fecham
o cartão, e ele volta na próxima sessão.

## Portão só em algumas páginas

A configuração também pode ir num elemento da página, que vale só para ela e
sobrescreve os atributos da tag:

```html
<!-- liga o portão nesta página -->
<div data-paulo-gate data-source="nome-do-site" hidden></div>

<!-- desliga o portão nesta página (num site todo travado) -->
<div data-paulo-gate="off" hidden></div>
```

### Sites com páginas separadas (HTML puro, WordPress, templates do servidor)

- **Site todo travado, menos algumas páginas** (ex.: política de privacidade,
  termos): tag do Passo 2 no `<head>` de todas e o elemento `"off"` nas páginas
  livres. Tire também o snippet do `<head>` das páginas livres, se o `<head>`
  for por página.
- **Só algumas páginas travadas:** a tag do `embed.js` **sem** os atributos do
  portão (`<script src="https://paulo-form.vercel.app/embed.js" async></script>`,
  ou a que já existir) e, em cada página travada, o elemento que liga o portão
  mais o snippet no `<head>`.

### Next.js e SPAs (Vite/React/Vue)

A navegação acontece sem recarregar a página, então avise o `embed.js` a cada
troca de rota chamando `window.PauloForm.portao()` — essa função relê a
configuração da página atual e abre o portão se for preciso. Coloque **um**
componente no layout raiz (dentro do `<body>`, ou junto do roteador):

```tsx
"use client"; // só no Next.js App Router

import { useEffect } from "react";
import { usePathname } from "next/navigation";

type JanelaComPortao = Window & { PauloForm?: { portao: () => void } };

export function PortaoNaTrocaDeRota() {
  const pathname = usePathname();
  useEffect(() => {
    (window as JanelaComPortao).PauloForm?.portao();
  }, [pathname]);
  return null;
}
```

- **React Router:** o mesmo componente, trocando `usePathname()` por
  `useLocation().pathname` (de `react-router-dom`), renderizado dentro do
  roteador. **Next.js Pages Router:** `useRouter().asPath` (de `next/router`)
  no `_app.tsx`. **Vue Router:**
  `router.afterEach(() => nextTick(() => window.PauloForm?.portao()))` no
  arquivo onde o roteador é criado (em TypeScript, com o tipo acima:
  `(window as JanelaComPortao).PauloForm?.portao()`).
- Use o `?.`: se o `embed.js` ainda não carregou, ele mesmo lê a página quando
  carregar.
- **Só algumas rotas travadas:** tag do `embed.js` sem os atributos do portão
  no `<head>`, o elemento que liga o portão dentro das rotas travadas e o
  componente acima. **Não** ponha o snippet no `<head>` compartilhado (ele
  cobriria também as rotas livres); o portão funciona igual, só sem a
  proteção contra o "flash" no primeiro carregamento.
- **Site todo travado, menos algumas rotas:** no Next.js, Passos 1 e 2 no
  layout raiz, o elemento `"off"` nas rotas livres e o componente acima. Numa
  SPA só de cliente (Vite), prefira o caminho inverso (ligar por rota): o
  `"off"` renderizado pelo JavaScript pode chegar depois de o portão já ter
  aberto.

## Visual: variáveis CSS

O cartão fica dentro de um **shadow DOM**: nenhuma regra CSS do site entra lá,
e **não adianta tentar estilizar o interior** com seletores. A única forma de
adaptar o visual são estas variáveis, definidas no `:root` do CSS global do
site:

| variável | o que muda |
|---|---|
| `--paulo-gate-fundo` | fundo do cartão |
| `--paulo-gate-texto` | cor do título |
| `--paulo-gate-texto-suave` | subtítulo, rótulos e linhas de apoio |
| `--paulo-gate-campo` | fundo do campo de e-mail |
| `--paulo-gate-borda` | borda do campo e do cartão |
| `--paulo-gate-botao` | fundo do botão (aceita gradiente) |
| `--paulo-gate-botao-texto` | texto do botão |
| `--paulo-gate-destaque` | anel de foco |
| `--paulo-gate-fonte` | `font-family` (padrão: Google Sans, servida pelo próprio paulo-form) |
| `--paulo-gate-veu` | cor do véu sobre o site |
| `--paulo-gate-desfoque` | raio do desfoque do site (ex.: `14px`) |
| `--paulo-gate-raio` | arredondamento do cartão |

```css
:root {
  --paulo-gate-botao: linear-gradient(90deg, #7c3aed, #db2777);
  --paulo-gate-botao-texto: #ffffff;
  --paulo-gate-destaque: #a78bfa;
  --paulo-gate-raio: 20px;
}
```

- Defina só as que precisar; as outras ficam no padrão.
- Se o site tem identidade visual clara, ajuste pelo menos o botão
  (`--paulo-gate-botao`, `--paulo-gate-botao-texto`) e o foco
  (`--paulo-gate-destaque`) para a cor de destaque do site, com contraste
  legível. Mexa no resto só se o usuário pedir ou se o cartão destoar.
- Prefira valores literais (hex, rgb, nome da fonte). Se usar `var(--algo)`
  do projeto, essa variável precisa existir no próprio `:root` — variável
  definida só no `<body>` (comum com `next/font`) não vale ali.

## Convivência com o formulário (skill paulo-form)

> **As duas skills podem estar no mesmo site e na mesma página, com UM único
> `embed.js`.** A tag com `data-paulo-gate` também atende os botões
> `data-paulo-form` (modal do formulário). Quem envia o formulário pelo modal
> na mesma página tem o portão liberado automaticamente. Se depois pedirem o
> botão do formulário, só o botão é inserido — nunca uma segunda tag.

## Passo 4 — Verificar que funcionou

1. **No código:** busque `embed.js` → exatamente uma tag por página (ou uma
   no layout); busque `pauloform_gate` → o snippet está no `<head>`.
2. **No HTML servido** (não só no código-fonte), com o servidor de
   desenvolvimento rodando:
   - `curl -s http://localhost:PORTA/ | grep -c pauloform_gate` → 1 ou mais
     (o snippet saiu inline);
   - `curl -s http://localhost:PORTA/ | grep -o '<script[^>]*embed.js[^>]*>'`
     → a tag aparece com `data-paulo-gate` (se o portão foi ligado por
     elemento, confira o elemento `data-paulo-gate` na página travada).
   - No Windows PowerShell, use `curl.exe` no lugar de `curl` e
     `Select-String` no lugar de `grep`.
3. **No navegador**, numa aba anônima (ou depois de resetar, item 5): o site
   aparece borrado e escurecido, com o cartão de e-mail na frente e a rolagem
   travada. No console:
   - `window.__pauloFormEmbed === 1` → script ativo;
   - `document.querySelector('[data-paulo-gate-host]')` existe → portão aberto.
4. **Liberação, sem enviar e-mail:** simule o estado "liberado" e recarregue —
   o portão não deve aparecer:

   ```js
   localStorage.setItem('pauloform_gate', JSON.stringify({ email: 'simulado@exemplo.com', t: Date.now(), via: 'simulado' })); location.reload()
   ```

   Para ver o estado liberado: `JSON.parse(localStorage.pauloform_gate)`.
5. **Resetar para ver o portão de novo:**

   ```js
   localStorage.removeItem('pauloform_gate'); sessionStorage.removeItem('pauloform_gate_fechado'); location.reload()
   ```

**Não envie e-mail de teste real pelo portão:** cada envio vira lead no painel
do Paulo. Teste a liberação com os itens 4 e 5. Se for indispensável testar um
envio de verdade, avise o usuário antes.

Se o portão não aparecer: falta `data-paulo-gate` na tag (ou no elemento), há
um `data-paulo-gate="off"` na página, o navegador já está liberado (resete) ou
há duas tags `embed.js`. Se a tela fica borrada uns segundos **sem** o cartão
e depois volta ao normal, o `embed.js` não carregou (URL errada ou CSP).

## Notas técnicas

- **CSS do site não afeta o cartão** (shadow DOM). Não escreva CSS defensivo
  nem altere regras do site por causa dele; personalize só pelas variáveis.
- **Ambiente de desenvolvimento:** o portão também aparece em `localhost`.
  Para trabalhar no site sem ele, use a simulação do item 4 no seu navegador —
  não desligue o portão no código.
- **CSP:** se o projeto tiver Content-Security-Policy, libere
  `https://paulo-form.vercel.app` em `script-src`, `connect-src` e
  `font-src` (e em `frame-src`, se o site também tiver botões do formulário).
  O snippet é inline: se a CSP bloquear script inline, ponha nas DUAS tags
  (snippet e `embed.js`) o `nonce` que o projeto já usa. Os estilos do
  snippet e do cartão não precisam de `style-src` (usam folhas construídas,
  que a CSP não bloqueia). Sem nonce, libere o hash `sha256-...` que o navegador mostra
  no erro do console. Nunca remova o snippet para "resolver".
- **Nunca trava o site por falha:** se por qualquer motivo o cartão não
  conseguir aparecer por cima da página (CSP muito restrita, CSS exótico), o
  portão simplesmente não abre e o site segue livre — não tente "consertar"
  isso escondendo conteúdo.

## Regras (siga à risca)

- **NUNCA** construa um portão próprio (overlay, modal, campo de e-mail,
  desfoque) e **NUNCA** chame a API de gravação diretamente (ex.:
  `POST /api/lead`). O único ponto de integração é o snippet + a tag do
  `embed.js` (ou o elemento `data-paulo-gate`).
- **NUNCA** esconda ou remova o conteúdo do HTML para "proteger": nada de
  renderização condicional, nada de `display:none` ou `visibility:hidden` no
  `body`/`main`. O conteúdo fica no HTML (SEO); o portão é só uma camada.
- Copie o snippet anti-flash **exatamente** como no Passo 1, inline no
  `<head>`.
- **UMA** tag `embed.js` por página, para formulário e portão juntos.
- **SEMPRE** preencha `data-source` (kebab-case, sem acentos, igual no site
  todo).
- `data-dismissible="true"` só quando o usuário pedir.
- **NUNCA** envie e-mails de teste reais pelo portão; teste com simular/resetar.
- Textos padrão por padrão: `data-titulo`/`data-subtitulo`/`data-botao` só quando o
  usuário pedir texto específico.
