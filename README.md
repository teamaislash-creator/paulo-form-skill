# paulo-form — skills de captação de leads do paulo.ia

Duas skills que ensinam qualquer agente de código (**Claude Code** ou **Codex
CLI**) a plugar, em qualquer projeto, a captação de leads do Paulo Aguiar
([@paulo.ia](https://www.instagram.com/paulo.ia)):

| skill | o que faz | quando usar |
|---|---|---|
| **paulo-form** | Insere um **botão/link** com o design do próprio site que abre o formulário oficial num **modal** por cima da página. | Quer um CTA: "fale comigo", "quero a consultoria", "entrar na lista". |
| **paulo-gate** | Coloca um **portão de e-mail**: o site aparece borrado atrás de um cartão que pede só o e-mail e libera o conteúdo na hora. | Quer **travar** o conteúdo até a pessoa deixar o e-mail. |

Nada é recriado no site: o formulário e o portão moram num lugar só
(`paulo-form.vercel.app`) e são atualizados num lugar só.

Depois de instalar, é só pedir em linguagem natural, por exemplo:

**Formulário (paulo-form)**

> "coloca o formulário do Paulo no rodapé"
> "adiciona um botão de consultoria do paulo.ia aqui"
> "põe o link de captura de leads nessa landing"

**Portão de e-mail (paulo-gate)**

> "coloca o paywall de email no site"
> "bloqueia o conteúdo até a pessoa deixar o email"
> "trava essa página com captura de email, com o título 'Baixe o guia de prompts'"

### As duas convivem — e o script é um só

As duas skills podem estar no mesmo site e até na mesma página. Elas usam o
**mesmo script** (`https://paulo-form.vercel.app/embed.js`), incluído **uma vez
só** por página: a mesma tag liga o portão e faz os botões abrirem o modal.
Quem envia o formulário pelo modal já fica liberado do portão.

---

## Instalação no Claude Code (plugin/marketplace)

Rode dentro do Claude Code:

```
/plugin marketplace add teamaislash-creator/paulo-form-skill
/plugin install paulo-form@paulo-ia
```

Se o resumo pedir, rode `/reload-plugins` para ativar. Pronto — o plugin
`paulo-form` já traz as duas skills (formulário e portão); a partir daí é só
pedir em linguagem natural.

Para atualizar depois (quem instalou antes da versão 1.2.0 precisa disso para
ganhar o portão de e-mail), rode no terminal:

```
claude plugin marketplace update paulo-ia
claude plugin update paulo-form@paulo-ia
```

e reinicie o Claude Code (ou rode `/reload-plugins` na sessão aberta). Dentro
do Claude Code dá para fazer o mesmo pelo menu `/plugin` → aba de instalados →
paulo-form → atualizar. Plugins de marketplaces de terceiros **não** se
atualizam sozinhos por padrão.

## Instalação no Codex CLI (manual)

Copie as pastas `skills/paulo-form/` e `skills/paulo-gate/` deste repositório
para `~/.codex/skills/`:

```bash
git clone https://github.com/teamaislash-creator/paulo-form-skill
mkdir -p ~/.codex/skills
cp -r paulo-form-skill/skills/paulo-form paulo-form-skill/skills/paulo-gate ~/.codex/skills/
```

(No Windows PowerShell:
`Copy-Item -Recurse paulo-form-skill\skills\paulo-form, paulo-form-skill\skills\paulo-gate $HOME\.codex\skills\`.)

---

## O que a skill paulo-form faz (botão do formulário)

1. Adiciona uma vez, antes de `</body>`, o loader do modal:
   `<script src="https://paulo-form.vercel.app/embed.js" defer></script>`
   (em Next.js, via `next/script` com `strategy="afterInteractive"`).
   Se a página já tem o `embed.js` (por exemplo, por causa do portão), não
   adiciona outro.
2. Insere o gatilho onde você pediu, com o **design do próprio projeto**:
   ```html
   <a href="https://paulo-form.vercel.app/" data-paulo-form data-source="nome-do-projeto">
     Clique aqui
   </a>
   ```
3. Suporta variações: botão inline num texto, bloco de CTA no rodapé, ou link
   direto em nova aba sem modal (basta omitir `data-paulo-form`).
4. Opcional: `data-ctx="curso|consultoria"` ajusta o texto do formulário e
   `data-interesse="..."` pré-seleciona um interesse.

Sem JavaScript, o link continua abrindo a página do formulário — nunca existe
botão morto.

## O que a skill paulo-gate faz (portão de e-mail)

1. Cola no `<head>` um pequeno snippet **anti-flash**: o site já carrega
   borrado, sem mostrar o conteúdo limpo por um instante antes do portão.
2. Liga o portão no mesmo `embed.js`:
   ```html
   <script src="https://paulo-form.vercel.app/embed.js" async
           data-paulo-gate
           data-source="nome-do-site"></script>
   ```
   O cartão já vem com textos genéricos, que servem para qualquer projeto
   sem editar: **"Quer acessar esse conteúdo gratuito?"**, **"Basta informar
   seu email para continuar"** e o botão **QUERO ACESSAR**, mais a linha de
   consentimento do paulo.ia. Para adaptar ao conteúdo de uma página, peça ao
   agente — ele usa `data-titulo`, `data-subtitulo` e `data-botao`:
   ```html
   <script src="https://paulo-form.vercel.app/embed.js" async
           data-paulo-gate
           data-source="guia-de-prompts"
           data-titulo="Baixe o guia de prompts"
           data-subtitulo="Informe seu email para liberar o download"
           data-botao="Quero o guia"></script>
   ```
3. Funciona no site todo ou só em algumas páginas, em HTML puro, Next.js
   (App Router e Pages Router), Vite/React/Vue e outros.
4. O visual se adapta ao site por variáveis CSS (`--paulo-gate-botao`,
   `--paulo-gate-fundo`, `--paulo-gate-fonte`...).

O conteúdo continua no HTML (SEO intacto) e, se algo falhar, o portão libera o
acesso — o site nunca fica trancado. Quem liberou uma vez não vê mais o portão
naquele site. É captação de lead, não proteção de conteúdo pago.

---

## Mensagem pronta para o grupo da equipe

> **Novidade na captação do Paulo:** além do botão do formulário, agora dá pra
> **travar o conteúdo de qualquer site até a pessoa deixar o e-mail** (o site
> fica borrado atrás de um cartão). É só pedir pro agente: *"coloca o
> paywall"* ou *"trava o site com captura de email"*. Os e-mails caem no mesmo
> painel do Paulo, marcados como "portão".
>
> **Já tem o plugin?** Peçam pro agente rodar
> `claude plugin marketplace update paulo-ia` e
> `claude plugin update paulo-form@paulo-ia`, e abram uma sessão nova.
>
> **Ainda não tem?** Peçam pro agente rodar
> `claude plugin marketplace add teamaislash-creator/paulo-form-skill` e
> `claude plugin install paulo-form@paulo-ia`.
>
> Os textos do portão já vêm prontos e genéricos; pra adaptar a uma página, é
> só pedir ("muda o título do portão pra ..."). Formulário e portão usam um
> script só e podem ficar no mesmo site.
