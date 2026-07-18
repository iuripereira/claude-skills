# STATE.md — estado atual (as-built)

> Handoff vivo. Reflete o que o projeto **é** agora, não o que se planeja (isso vive no
> `specs/TRUTH.md` e nas deltas). Atualize no mesmo commit de toda mudança relevante. Em conflito
> de merge, mantenha a **união das verdades** — nunca sobrescreva o progresso de outra sessão.

**Atualizado em:** 2026-07-18

## O que existe

- **5 skills do framework**, versionadas por allowlist no `.gitignore`: `projeto-init`,
  `projeto-infra`, `spec-feature`, `spec-review`, `guarding-doc-integrity`. As demais pastas de
  `~/.claude/skills` são pessoais e ficam fora do git.
- **2 gates determinísticos**, ambos com `--selftest` rodado no CI:
  `spec-feature/scripts/check_cycle.py` (C1–C5 do ciclo) e
  `guarding-doc-integrity/scripts/validate_integrity.py` (C1–C3 de espelhos).
- **Infra**: ruleset `sdd-protect-main` (PR obrigatório + check `ci` verde), workflows `ci`
  (JSON/TOML/YAML, frontmatter, selftests) e `conventional-commits`.
- **Scaffold próprio**: este arquivo, `CLAUDE.md`, `CHANGELOG.md`, `docs/adrs/`, `specs/TRUTH.md`
  com backfill Δ000 do que já vige.

## O que falta

- Rodar o ciclo de verdade: a Δ001 ainda não existe. As mudanças até aqui entraram como PR direto,
  não como delta spec — o framework passou a se aplicar a si mesmo só a partir deste commit.
- CI dos gates dentro dos projetos do usuário (ver `docs/adrs/ADR-0001`).
- Backfill assistido de `TRUTH.md` em brownfield: existe como tarefa sob demanda, não como fase.
- **`deps.toml` deste repo.** O framework tem valores espelhados que hoje ninguém governa: o limiar
  de particionamento do TRUTH aparece em 7 arquivos e o de tamanho de PR em 5. É exatamente o caso
  de uso da `guarding-doc-integrity`, ainda não aplicado aqui — o bootstrap exige decidir dono e
  espelhos sancionados com o usuário.

## Decisões em aberto

- **Release inicial.** Não há tag; a versão canônica é a tag git, então o repo está formalmente
  pré-`v0.1.0`. Falta decidir se o scaffold atual já justifica cortar a primeira.
- **Vendoring dos scripts de gate** nos projetos gerados — a alternativa ao "rodar local" da
  ADR-0001, caso o gate no CI do projeto passe a ser requisito.

## Pegadinhas / débito conhecido

- **O `.gitignore` é uma allowlist** (`/*` + `!/nome/`), não uma denylist. Consequência: um arquivo
  novo na raiz **não é versionado** até ganhar sua linha `!/...`. Skill nova do framework ou
  artefato novo de scaffold exige editar o `.gitignore` no mesmo commit.
- **`check_cycle.py` é acoplado ao formato do `delta-spec.md`** (um requisito por bloco
  `### Rn — VERBO`). Spec fora do template gera "nenhum bloco encontrado" (ALTO) em vez de
  analisar — falha ruidosa, não silenciosa. Marcado com `ponytail:` no script. Corrigir quando/se
  o template mudar de forma.
- **`Δ000` é convenção, não fase.** É o rótulo do backfill pré-ciclo no `TRUTH.md`; deltas reais
  começam em `Δ001`. Nenhum diretório `specs/000-*/` existe nem deve existir.
- **Zero tags em 3 merges, contra a própria tríade de release.** A regra canônica diz "Tag = release
  a cada merge na `main`"; os PRs #2 e #3 foram `feat` e não geraram tag. Consequência: qualquer
  classificação de bump (MINOR/MAJOR) é decorativa enquanto não houver linha de base, e não existe
  ponto de retorno versionado. Corrigir antes ou logo depois da Δ001 — se antes, cortar `v0.1.0`
  cria o baseline pré-plugin. Nenhum gate percebe a violação hoje: o `analyze` checa regra canônica,
  mas não olha estado de release.
- **Renomear um termo citado em N requisitos custa N blocos MUDA completos.** Observado na Δ001:
  trocar a forma de citar as skills exigiu cinco blocos, cada um repetindo o requisito íntegro. É o
  preço da consolidação mecânica do archive (`cycle.md`, regra 2) — o archive não infere intenção,
  então o cenário que não for repetido se perde. Funcionando como projetado; reavaliar só se o
  padrão se repetir em outra delta.
- **Pendência em "Dependências e riscos" não tem gate nem destino durável.** A regra manda parquear
  ali (`cycle.md:47`), o clarify é quem deveria resolver, mas o analyze não lê riscos e o archive
  leva o `spec.md` para `_archive/` — onde vira histórico, não verdade. Pendência que sobrevive à
  delta evapora. Corrigir roteando para a seção "Decisões em aberto" **deste** arquivo no archive,
  com check no `check_cycle.py`. Candidata a Δ002.
- **Metade do gate analyze continua humana** por design (scope creep spec×plan, violação de regra
  canônica). Não é débito a corrigir — é limite reconhecido; automatizar produziria falso negativo
  confiante.

## Histórico de alterações

| Data (AAAA-MM-DD) | Mudança | Ref |
|---|---|---|
| 2026-07-18 | `projeto-init` aplicado ao próprio repo: CLAUDE.md, scaffold e TRUTH.md com backfill Δ000 | #4 |
| 2026-07-18 | `guarding-doc-integrity` integrada ao framework; regra de propagação ganha executor | #3 |
| 2026-07-18 | `check_cycle.py`: primeiro gate determinístico do ciclo | #2 |
