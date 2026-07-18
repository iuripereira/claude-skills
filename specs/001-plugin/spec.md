# Δ 001 — plugin
Estado: proposta · Data: 2026-07-18 · Branch: feat/001-plugin

## Contexto (≤3 linhas)
O framework vive como recorte de `~/.claude/skills/` via allowlist no `.gitignore`, com caminhos
absolutos de máquina dentro das skills e instalação por `cp -r`. Empacotá-lo como plugin do Claude
Code resolve distribuição, acoplamento de caminho e agrupamento de uma vez — e o custo do rename é
mínimo enquanto nenhum projeto downstream existe.

## Mudanças
<!-- só o que muda; um bloco por requisito; ADICIONA/MUDA/REMOVE em relação ao TRUTH.md -->

### R1 — ADICIONA: o framework é distribuído e instalado como plugin do Claude Code
- DADO um usuário sem o framework QUANDO ele roda `/plugin install iuripereira/sdd-iuri` ENTÃO as
  cinco skills ficam disponíveis sob o namespace `sdd-iuri:`, sem cópia manual de arquivos e sem
  que o repositório precise viver dentro de `~/.claude/skills/`
- DADO o repositório do framework QUANDO o Claude Code carrega o plugin ENTÃO encontra
  `.claude-plugin/plugin.json` na raiz e as skills em `skills/<nome>/SKILL.md`

### R2 — MUDA R1 (Δ000): a skill `projeto-init` classifica o repositório e monta o `CLAUDE.md`
<!-- muda só a forma de citar a skill: o nome de invocação passa a ter um dono único (R1) -->
- DADO um repositório sem `CLAUDE.md` QUANDO a skill `projeto-init` roda ENTÃO o tipo é
  classificado pela tabela de `detection.md` e o `CLAUDE.md` contém os módulos que a matriz marca
  para o tipo, com o texto copiado de `canonical-rules.md`

### R3 — MUDA R4 (Δ000): a skill `projeto-infra` configura a infraestrutura e é idempotente
- DADO um repositório já configurado QUANDO a skill `projeto-infra` roda de novo ENTÃO ela consulta
  o que existe, preenche só as lacunas e relata no-op no restante
- DADO falha de infra (sem rede, `gh` não autenticado) QUANDO o init a invoca ENTÃO o init reporta
  e segue, sem travar

### R4 — MUDA R5 (Δ000): uma feature é uma delta spec, com numeração global ao repositório
- DADO um incremento novo QUANDO a skill `spec-feature` abre a delta ENTÃO cria `specs/NNN-nome/`
  com `NNN` = max(`specs/`, `specs/_archive/`) + 1 e a branch `tipo/NNN-nome`
- DADO uma versão maior do projeto QUANDO uma delta nova é aberta ENTÃO a numeração continua do
  maior existente e nunca reinicia

### R5 — MUDA R10 (Δ000): o ciclo aplicável varia por tipo
- DADO um projeto `site-estatico` QUANDO o ciclo roda ENTÃO é o reduzido (specify → plan →
  implement → review), com clarify e analyze sob demanda
- DADO um projeto `workspace-dados` QUANDO a skill `spec-feature` é invocada ENTÃO ela recusa com
  explicação e aponta o scaffold estático do `projeto-init`

### R6 — MUDA R14 (Δ000): a revisão adversarial da spec é um toggle opcional, distinto do analyze
- DADO uma spec que toca segurança, dados persistentes, contrato externo ou dependência nova
  QUANDO a skill `spec-review` roda ENTÃO produz achados + edições propostas em blocos
  antes/depois, sem aplicar nenhuma sem aprovação do usuário

## Requisitos não funcionais

### RNF1 — ADICIONA: portabilidade — nenhum artefato do framework depende de caminho de máquina
- Métrica: zero ocorrências de `~/.claude/skills` em `skills/**` e `.github/**`; toda invocação de
  script do framework resolve por `${CLAUDE_PLUGIN_ROOT}`
- Verificação: step no job `ci` rodando
  `! grep -rn '~/.claude/skills' skills/ .github/` (falha o PR se houver ocorrência)

## Fora de escopo
- Publicar em marketplace público — `/plugin install owner/repo` já basta para instalar direto do
  git; marketplace vira decisão separada se houver usuários além do autor.
- Vendorizar os gates no CI dos projetos gerados — a ADR-0001 segue vigente e não é reaberta aqui.
- Migrar as skills pessoais e de terceiros que dividem `~/.claude/skills/` — elas ficam onde estão,
  intocadas.
- Cortar a primeira tag de release (`v0.1.0`) — decisão registrada em aberto no `STATE.md`.

## Dependências e riscos
- **Breaking change de invocação.** `/spec-feature` passa a `/sdd-iuri:spec-feature` em todas as
  cinco skills. Pela tríade de release é MAJOR (`feat!`). O custo hoje é ~10 substituições de texto
  porque nenhum projeto downstream existe; cresce a cada projeto gerado. **A janela barata expira.**
- **A fase de migração local é destrutiva.** Remover as cinco pastas de `~/.claude/skills` enquanto
  a sessão depende delas. Ordem obrigatória: clonar para fora → instalar o plugin → verificar que as
  skills respondem pelo nome novo → só então apagar. Nunca `mv` primeiro.
- **`${CLAUDE_PLUGIN_ROOT}` está verificado na documentação, não em execução.** Validar na fase
  implement, com o plugin instalado, antes de apagar qualquer coisa.
- **Rename do repositório** (`claude-skills` → `sdd-iuri`) muda a URL de clone. O GitHub redireciona,
  mas README e `adapters.md` precisam citar a nova no mesmo PR.
- **Friction observada, não bug:** renomear um termo citado em N requisitos custa N blocos MUDA
  completos (cinco, aqui). É o preço da consolidação mecânica do archive (`cycle.md`, regra 2) —
  funcionando como projetado. Registrado para reavaliar se o padrão se repetir.
- **Decisão pendente para o clarify:** o `plugin.json` deve declarar as dependências
  (`superpowers`, `ponytail`, `max`) de alguma forma, ou elas continuam sendo verificação manual do
  passo 6 do `projeto-init`? Não inventei resposta.
