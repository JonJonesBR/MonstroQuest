# MonstroQuest — Progresso (histórico)

> Histórico cronológico do que já foi feito no projeto. O `PLANO_MELHORIAS.md` diz o
> que falta e por quê; este arquivo registra o que já aconteceu, com commit e evidência,
> para uma sessão nova não precisar reconstruir o contexto do zero.

## 2026-08-21 — Fase 0: Fundação
- `git init`, `.gitignore` mínimo, `README.md`.
- Commit: `0a5ba60`.

## 2026-08-21 — Fase 1+2: Higiene do código e correções de batalha
- Fórmula de stats unificada (`statsOf`/`curStats` viraram chamadas de `curStatsMon`).
- Código morto removido (`tryRun`, `battleMoveName`, `monStatus`, `waitClick`, `UI.mode`,
  variáveis nunca usadas).
- Dados (`RIVAL_TEAMS`, `GYM_DEFS`, `CHAMP_DEF`, `CLS_MONEY`, `HIDDEN_ITEMS`) movidos para
  antes de `boot()`; marcadores `__NEXT__` removidos.
- `trainer.reward` removido (recompensa real vem de `CLS_MONEY`).
- `window.__game` só expõe com `?debug` na URL.
- Precisão de golpe agora rola para os dois lados (antes só o jogador errava).
- Level-up cura HP proporcional ao ganho de PS máximo.
- Bug crítico descoberto na verificação: `burstFx` chamava o parâmetro `el` (elemento DOM)
  como função — toda batalha travava no primeiro golpe com dano
  ("A batalha travou: el is not a function"). Corrigido renomeando o parâmetro para `src`.
- Commit: `77c8e2d`.

## 2026-08-29 — Rodada de QA/simulação real (protocolo de orquestração)
Aplicação do `PROMPT_MESTRE_ORQUESTRACAO.md` (Fases 0→5 do protocolo) sobre o estado
entregue pelas Fases 0-2 do plano do projeto. Não é a "Fase 3" do `PLANO_MELHORIAS.md`
(essa é sobre UX/features novas, ainda pendente) — foi endurecimento via jogo real
(navegador headless, toque sintético) + verificação visual com modelo de visão.
Detalhes completos e evidências em `TEST-PLAN.md`.

- **`b628dc1`** — Fase 3 do protocolo: 12 bugs corrigidos, achados jogando de verdade
  (não por leitura estática de código):
  - A: líder de ginásio não respondia (faltava prop `gym` nos NPCs).
  - B: posição do save nunca persistia (`S.x/S.y` fixos).
  - C: Doce Raro em batalha não subia nível.
  - D: Dreno de Sementes nunca drenava (condição no monstro errado).
  - E/E2: save corrompido deixava `party` vazia e travava o título.
  - F: encontros selvagens mortos (`S.battle===null` nunca era true de fato).
  - G: PC perdia monstro depositado (`S.box` não inicializado).
  - H/I: mensagem de batalha ficava atrás da grade de comandos (soft-lock).
  - J: Hall da Fama reabria após toda batalha pós-campeão.
  - Menores: vitória do campeão não salvava; caverna sem música.
- **`e8546bd`** — documentação do plano de teste e das evidências (`TEST-PLAN.md`).
- **`9dc2620`** — Fase 4 do protocolo (gate do Juiz, nota 96→100): `loadGame` exige
  `party` não-vazia; `NOVO JOGO` reinicializa `S` por completo (badges/flags/money/dex
  não vazavam entre partidas); líder de ginásio derrotado dá feedback ao interagir; save
  silencioso antes do Hall da Fama.
- **`cb3e7b1`** — Fase 5 do protocolo (ressalva do Juiz): `lastCenter` resetado em novo
  jogo.
- **`bc53564`** — Fase "4-visual": 2 bugs de layout encontrados por verificação com
  modelo de visão: título `MONSTROQUEST` cortado no topo e menu estourando o fundo
  (`APAGAR SAVE` fora da tela); caixas de HP da batalha sobrepostas e desalinhadas.
- **`44a9ae2`** — documentação da verificação visual (checklist antes/depois no
  `TEST-PLAN.md`).

## Pendente
Ver `PLANO_MELHORIAS.md` — Fases 3 (UX/jogabilidade), 4 (mobile/PWA), 5 (conteúdo) e
6 (testes automatizados) do plano do projeto ainda não começaram.
