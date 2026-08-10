# paulo-form — skill de captação de leads do paulo.ia

Skill que ensina qualquer agente de código (**Claude Code** ou **Codex CLI**) a
inserir, em qualquer projeto, o **botão/link do formulário de captação de leads
do Paulo Aguiar** ([@paulo.ia](https://www.instagram.com/paulo.ia)).

O formulário **não** é recriado no site: entra apenas um gatilho (link ou botão)
com o design do próprio projeto, e ao ser clicado ele abre o formulário oficial
num **modal** por cima da página. O formulário mora num lugar só e é atualizado
num lugar só.

Depois de instalar, é só pedir em linguagem natural, por exemplo:

> "coloca o formulário do Paulo no rodapé"
> "adiciona um botão de consultoria do paulo.ia aqui"
> "põe o link de captura de leads nessa landing"

---

## Instalação no Claude Code (plugin/marketplace)

Rode dentro do Claude Code:

```
/plugin marketplace add teamaislash-creator/paulo-form-skill
/plugin install paulo-form@paulo-ia
```

Se o resumo pedir, rode `/reload-plugins` para ativar. Pronto — a partir daí é
só pedir em linguagem natural.

Para atualizar depois:

```
/plugin marketplace update paulo-ia
```

## Instalação no Codex CLI (manual)

Copie a pasta `skills/paulo-form/` deste repositório para `~/.codex/skills/`:

```bash
git clone https://github.com/teamaislash-creator/paulo-form-skill
mkdir -p ~/.codex/skills
cp -r paulo-form-skill/skills/paulo-form ~/.codex/skills/
```

(No Windows PowerShell:
`Copy-Item -Recurse paulo-form-skill\skills\paulo-form $HOME\.codex\skills\`.)

---

## O que a skill faz

1. Adiciona uma vez, antes de `</body>`, o loader do modal:
   `<script src="https://paulo-form.vercel.app/embed.js" defer></script>`
   (em Next.js, via `next/script` com `strategy="afterInteractive"`).
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

---

## Mensagem pronta para o grupo da equipe

> **Time, dá pra plugar o formulário do Paulo em qualquer projeto só pedindo
> pro agente.**
>
> **Claude Code:** rodem estes dois comandos dentro do Claude Code —
> `/plugin marketplace add teamaislash-creator/paulo-form-skill`
> e depois `/plugin install paulo-form@paulo-ia` (se pedir, `/reload-plugins`).
>
> **Codex CLI:** clonem `https://github.com/teamaislash-creator/paulo-form-skill`
> e copiem a pasta `skills/paulo-form` para `~/.codex/skills/`.
>
> Depois é só pedir em linguagem natural, tipo *"coloca o formulário do Paulo no
> rodapé"* ou *"adiciona um botão de consultoria do paulo.ia aqui"*. O agente
> põe um botão com o design do projeto que abre o form num modal. Lembrem de
> deixar o agente escolher um `data-source` que identifique o projeto (ex.:
> `landing-curso-ia`) pra gente saber de onde vêm os leads.
