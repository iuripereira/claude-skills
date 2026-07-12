# Detecção de tipo de projeto → módulos aplicáveis

Classifique o projeto inspecionando a pasta alvo, depois escolha o conjunto de módulos de
`canonical-rules.md` e os arquivos de scaffold. Na dúvida entre dois tipos, **pergunte**.

## Como classificar (primeiro match vence)

| Sinal na pasta | Tipo |
|---|---|
| `package.json` com `next`/`react`/`vue`/`svelte` (+ `src/`, componentes) | **app-web** |
| `pyproject.toml`/`requirements.txt`, ou `package.json` de servidor sem UI | **backend** |
| Só `index.html` + `css/js` estáticos (sem framework, sem build de app) | **site-estatico** |
| Predomínio de `.csv`/`.xlsx`/`.md` de dados, **sem** build/`package.json` de app | **workspace-dados** |
| Estrutura de `skills/`, `scripts/`, plugins, ferramentas de dev | **tooling** |
| Nada conclusivo | **pergunte ao usuário** |

Comandos úteis: `ls -a`, `cat package.json` (scripts/deps), `find . -maxdepth 2 -type f`,
`git rev-parse --is-inside-work-tree`. Preencha placeholders de comando (`{{npm run test}}` etc.)
a partir dos scripts reais do `package.json`/Makefile.

## Matriz módulo × tipo

Legenda: ✅ incluir · ⚠️ versão leve/subset · ❌ pular.

| Módulo (canonical-rules.md) | app-web | backend | site-estatico | workspace-dados | tooling |
|---|:--:|:--:|:--:|:--:|:--:|
| header | ✅ | ✅ | ✅ | ✅ | ✅ |
| core | ✅ | ✅ | ✅ | ✅ | ✅ |
| release-triad | ✅ | ✅ | ⚠️ | ❌ | ✅ |
| git-workflow | ✅ | ✅ | ✅ | ⚠️ | ✅ |
| co-author | opcional | opcional | opcional | opcional | opcional |
| docs-sdd | ✅ | ✅ | ⚠️ | ❌ (STATE só) | ⚠️ |
| sdd-ciclo | ✅ | ✅ | ⚠️ (reduzido) | ❌ | ✅ |
| clean-code | ✅ | ✅ | ✅ | ⚠️ | ✅ |
| testing | ✅ | ✅ | ❌ | ❌ | ⚠️ |
| security | ✅ | ✅ | ⚠️ | ⚠️ (PII) | ⚠️ |
| i18n-format | ✅ | ⚠️ | ✅ | ⚠️ | ❌ |
| architecture | ✅ | ✅ | ❌ | ❌ | ❌ |
| data-workspace | ❌ | ❌ | ❌ | ✅ | ❌ |

## Matriz do ciclo × tipo (governa `/spec-feature`, TDD e `projeto-infra`)

| Tipo | `ciclo` | `tdd` | `infra` |
|---|---|---|---|
| app-web | completo | duro | completo (rulesets main+develop, husky/commitlint, release-please, CI, CodeRabbit) |
| backend | completo | duro | completo |
| tooling | completo | recomendado (dispensável por task com justificativa no plan.md) | mínimo (ruleset main, CI lint/test; sem develop) |
| site-estatico | reduzido (specify → plan → implement → review) | off → verificação visual + build | mínimo |
| workspace-dados | nenhum | off → asserts/validação de dados | nenhum |

`ciclo` decide se o módulo `sdd-ciclo` entra no CLAUDE.md e se o scaffold usa `specs/` (ciclo)
em vez de `docs/specs/` estático. `tdd` é repassado ao implement do `/spec-feature`. `infra`
é o perfil oferecido ao invocar `projeto-infra`.

## Arquivos de scaffold × tipo

Só crie o arquivo se **não existir** (nunca sobrescreva — ver SKILL.md).

| Arquivo | app-web | backend | site-estatico | workspace-dados | tooling |
|---|:--:|:--:|:--:|:--:|:--:|
| `CLAUDE.md` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `CHANGELOG.md` | ✅ | ✅ | ⚠️ | ❌ | ✅ |
| `STATE.md` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `.gitignore` (append secrets/PII) | ✅ | ✅ | ✅ | ✅ | ✅ |
| `docs/adrs/` + `ADR-TEMPLATE.md` | ✅ | ✅ | ⚠️ | ❌ | ⚠️ |
| `specs/` + `TRUTH.md` (ciclo — substitui `docs/specs/` estático) | ✅ | ✅ | ⚠️ | ❌ | ✅ |
| `docs/epics/` (vazio + README) | ✅ | ✅ | ❌ | ❌ | ❌ |
| `GLOSSARY.md` | ✅ | ✅ | ⚠️ | ⚠️ | ❌ |
| `DATA_DICTIONARY.md` | ✅ | ✅ | ❌ | ✅ | ❌ |

Regra prática (alinhada à matriz acima): **workspace-dados** recebe `CLAUDE.md` (com módulo
`data-workspace`) + `STATE.md` + `DATA_DICTIONARY.md` (schema dos dados) + `.gitignore` de PII —
**sem** CHANGELOG, docs SDD (adrs/specs/epics) ou testes. `GLOSSARY.md` só se houver termos de
domínio além do schema. **site-estatico** recebe a tríade leve, sem `testing`/`architecture`.

Nos tipos com ciclo, o scaffold de specs é `specs/` + `specs/TRUTH.md` (copie o template de
`~/.claude/skills/spec-feature/references/templates/TRUTH.md`; o template delta-spec assume o
papel do antigo SPEC-TEMPLATE). `docs/adrs/`, STATE, CHANGELOG e GLOSSARY seguem inalterados —
o `plan.md` do ciclo gera ADR em `docs/adrs/` quando a decisão for durável. **Não crie**
`docs/specs/` + `SPEC-TEMPLATE.md` nesses tipos (repos existentes com `docs/specs/` ficam como
estão — não migre sem pedido).
