# Ciclo sdd-iuri — máquina de estados e fases

## Estados da delta (vivem no cabeçalho do `spec.md`)

```
proposta ──(analyze LIBERADO + implement + review + merge)──▶ aplicada ──(consolidação)──▶ arquivada
```

- **proposta** — de specify até o fim do review. Vive em `specs/NNN-nome/`.
- **aplicada** — código mergeado; falta consolidar. Estado transitório: não pare aqui.
- **arquivada** — consolidada no `TRUTH.md` e movida para `specs/_archive/NNN-nome/`.

## Fases — critérios de entrada/saída

| Fase | Entrada | Saída (critério de pronto) | Motor |
|---|---|---|---|
| specify | pedido de feature; `TRUTH.md` lido | `specs/NNN-nome/spec.md` rascunho no template; branch `tipo/NNN-nome` criada | nativo |
| clarify | spec rascunho | ambiguidades resolvidas; spec consolidada: todo Rn com DADO/QUANDO/ENTÃO; ADRs gravados se grill-with-docs | max:grill-me / max:grill-with-docs |
| plan | spec consolidada | `plan.md` em `specs/NNN-nome/` com o cabeçalho-resumo (≤15 linhas) prependido | superpowers:writing-plans |
| tasks | plan.md | `tasks.md`: cada task com arquivos, `cobre: Rn` (ou `cobre: infra`, para task sem requisito) e verificação, ordenada por dependência | nativo (template) |
| analyze | spec + plan + tasks | `analyze.md` com veredito LIBERADO (ou ressalvas aceitas pelo usuário) | nativo (analyze.md) |
| implement | analyze liberado | todas as tasks concluídas com as verificações rodadas; TDD conforme coluna `tdd` do tipo | superpowers:executing-plans ou subagent-driven-development |
| review | implementação completa | estágio 1 (conformidade com a spec) ok; estágio 2 (qualidade) ok com delete-list do /ponytail-review tratada | superpowers + ponytail:ponytail-review |
| archive | PR mergeado | Estado: arquivada; TRUTH.md consolidado; diretório em `_archive/` | nativo (regras abaixo) |

Fim de cada fase = **commit dos artefatos na branch da delta** (regra canônica: fim de etapa =
commit). Não acumule o ciclo inteiro num commit só.

Ciclo reduzido (site-estatico): specify → plan → implement → review. clarify/analyze entram
sob demanda (spec ambígua ou toque em regra canônica).

## Triagem do clarify (escolha do motor — reporte ao usuário)

`grill-with-docs` quando a spec toca **contrato externo, modelo de dados persistente,
dependência nova ou segurança** → decisão durável em jogo → gera ADRs (contrato em adapters.md).
Caso contrário `grill-me` (stateless, menos tokens). Sem o plugin max → fallback do adapters.md.

## Consolidação entrevista → delta spec (passo nativo, ex-to-spec)

Ao fim do clarify, sintetize **da conversa já feita** — NUNCA re-entreviste:
1. Cada decisão da entrevista vira um Rn novo ou ajusta um existente, sempre com
   DADO/QUANDO/ENTÃO verificável.
2. Renúncias explícitas vão para "Fora de escopo".
3. Pendências sem resposta vão para "Dependências e riscos" — não invente resposta.
4. Decisão durável discutida sem ADR gravado? Registre o ADR agora (template do projeto,
   `docs/adrs/`), antes do plan.

## Regras de archive (consolidação no TRUTH.md)

O `TRUTH.md` vive em **`specs/TRUTH.md`**. Blocos MUDA/REMOVE da delta devem **citar o alvo
vigente** nele (ex.: "MUDA R2 (Δ001)").
`specs/TRUTH.md` inexistente (primeiro archive) → crie de `templates/TRUTH.md` antes de consolidar.

1. **ADICIONA** → o requisito entra no domínio correspondente do TRUTH.md com sufixo `(ΔNNN)`,
   cenário DADO/QUANDO/ENTÃO incluído. Recebe o **próximo número R livre do TRUTH.md** — a
   numeração do TRUTH é global e nunca reutiliza número (nem de requisito removido); o Rn
   local da delta não migra.
2. **MUDA** → substitui **integralmente** o requisito vigente (texto + cenários) pelo bloco da
   delta; o sufixo passa a `(ΔNNN)` da delta nova. Por isso o bloco MUDA deve conter a versão
   completa do requisito — cenário vigente que continua valendo é **repetido na delta**; o
   archive consolida mecanicamente, não infere intenção.
3. **REMOVE** → apaga a entrada do TRUTH.md.
4. Atualize `Estado: arquivada` no spec.md e mova `specs/NNN-nome/` → `specs/_archive/NNN-nome/`
   (com plan.md, tasks.md, analyze.md juntos — o histórico completo vive no archive).
5. **Verificação obrigatória (diff):** todo Rn ADICIONA/MUDA da delta presente no TRUTH.md
   consolidado; todo REMOVE ausente; nenhum requisito de outras deltas alterado. Perda de
   requisito no archive é o pior bug do ciclo.

Particionamento do TRUTH.md: acima de ~800 linhas ou ~10 domínios claros → dividir em
`truth/<dominio>.md` e o TRUTH.md vira índice (a regra já está no template).

## Economia de tokens (NFR de primeira classe)

Artefatos **duráveis** (spec.md, TRUTH.md) são enxutos — limites nos templates. O `plan.md` é
artefato **efêmero de execução**: verboso por design (executável por subagente sem contexto),
arquivado junto com a delta e fora do caminho depois. Não pós-processe o plan para "enxugar" —
só o cabeçalho-resumo importa para humanos e para o analyze.
