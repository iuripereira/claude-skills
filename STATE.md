# STATE.md — diário de bordo

> Andamento contínuo do trabalho: o que está em curso **agora**, o que acabou de ser feito, os
> problemas do momento e os próximos passos imediatos. Atualize com frequência dentro da própria
> sessão. **Janela rolante:** entrada antiga sai — histórico permanente é [CHANGELOG](CHANGELOG.md)
> + git; débito/pendência/lição é [DEBT.md](DEBT.md); decisão com renúncia é
> [docs/adrs/](docs/adrs/); o que vige é [specs/TRUTH.md](specs/TRUTH.md). Em conflito de merge,
> mantenha a **união das verdades** — nunca sobrescreva o progresso de outra sessão.

**Atualizado em:** 2026-07-20

## Agora
- delta-007 `registros-com-dono` em implement (branch `feat/007-registros-com-dono`): STATE.md
  vira diário de bordo, DEBT.md nasce como registro canônico (MUDA R16), templates e gate C6
  acompanham.

## Feito recentemente
- 2026-07-19 — Higiene de registros: comandos de teste do CLAUDE.md corrigidos, convenções não
  escritas registradas (escopo da delta, tag no archive, squash), LICENSE MIT materializada. (#19)
- 2026-07-19 — Backfill de ADRs: ADR-0002..0006 registram as decisões-com-renúncia que vigiam sem
  registro. (#20)
- 2026-07-19 — Varredura completa de registros do repo + histórico #1–#18 (110 agentes, achados
  verificados adversarialmente) → plano da reorganização aprovado.

## Problemas atuais
- Nenhum bloqueio. Débito durável vive no [DEBT.md](DEBT.md) (DT-001..DT-007).

## Próximos passos imediatos
- Fechar a delta-007: review → PR → merge → archive (consolida MUDA R16 + R18/R19 no TRUTH.md,
  corta `v0.4.0`).
- delta-008: skill `sdd-iuri:handoff` (própria, inspirada em mattpocock/skills, MIT).
- Infra: exigir o check `commits` no ruleset `sdd-protect-main`; atualizar description/topics do
  repo no GitHub.
