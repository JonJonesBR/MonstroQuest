# MonstroQuest — Plano de Melhorias

> Estado atual: clone de Pokémon GBA em **um único `index.html`** (~156 KB, ~3.117 linhas).
> Jogo completo: 42 espécies, ~46 golpes, 9 itens, 19 mapas, 4 ginásios, rival (2 fases), campeão, hall da fama, save em `localStorage`, controles touch + teclado, áudio WebAudio chiptune.
> Sem repositório git, sem testes, sem README.

---

## Diagnóstico (evidências no código atual)

| # | Problema | Onde | Impacto |
|---|----------|------|---------|
| 1 | Fórmula de stats duplicada **3×** (`statsOf`, `curStats`, `curStatsMon`) | linhas ~1405, ~1412, ~1763 | Divergência futura; correção precisa tocar 3 lugares |
| 2 | Código morto: `tryRun`, `battleMoveName`, `monStatus`, `waitClick`, campo `UI.mode`, `reward` em treinadores, variáveis `idx`/`gained`/`dir`/`id` nunca usadas | várias | Ruído; `reward` definido mas ignorado = recompensa real vem de `CLS_MONEY` (ex.: tr1 define 120, paga 240) |
| 3 | Dados (`RIVAL_TEAMS`, `GYM_DEFS`, `CHAMP_DEF`, `CLS_MONEY`, `HIDDEN_ITEMS`) declarados **depois** de `boot()` com marcadores `__NEXT__` | linhas ~3100+ | Funciona só por acesso preguiçoso; qualquer uso imediato quebra (TDZ); marcadores = sobra de desenvolvimento |
| 4 | Precisão de golpe só é checada para o jogador (`mv.a<101 && side==='player'`) | `execAction` | Inimigo nunca erra; assimetria de balanceamento |
| 5 | Subir de nível não cura PS: `gained=maxHp(m)-before.hp` calculado e **nunca usado** | `levelUp` | HP atual não acompanha ganho de nível |
| 6 | Sem git — único arquivo em Google Drive, sem histórico | projeto | Perda irreversível; sem diff, sem rollback |
| 7 | Save só em `localStorage`, sem export/import, sem migração de versão | `SAVE_KEY` | Troca de aparelho/limpeza do navegador = progresso perdido |
| 8 | Sem tela de resumo do monstro (só mensagens de texto); sem apelido; sem correr; sem repelir | UI | Menos profundidade que o gênero pede |
| 9 | Sem opções: mudo/volume não persistem; sem velocidade de jogo; sem pausa/desistir em batalha | AudioSys/loop | Conforto mobile |
| 10 | Sem manifest PWA / theme-color / favicon | head | Sem "Adicionar à tela inicial" decente |
| 11 | Sem testes automatizados e sem README | projeto | Regressão silenciosa; onboarding |
| 12 | `window.__game` expõe internals sempre | boot | Backdoor de debug sempre ativo |

## Status

- ✅ **Fase 0 — Fundação** (commit `0a5ba60`): git init, `.gitignore`, README.
- ✅ **Fase 1 — Higiene** (commit `2ac4…`): stats unificadas, código morto removido, dados movidos antes do `boot()`, `reward` removido (recompensa vem de `CLS_MONEY`), `window.__game` atrás de `?debug`.
- ✅ **Fase 2 — Bugs** (commit `2ac4…`): precisão do inimigo, level-up cura PS, mensagem do Doce Raro, `S.prev` pós-whiteout, e **bug crítico descoberto na verificação**: `burstFx` chamava o parâmetro `el` (um elemento DOM) como função — **toda batalha abortava no primeiro golpe com dano** ("A batalha travou: el is not a function"). Corrigido renomeando o parâmetro para `src`.
- ✅ **Rodada de QA/simulação real** (protocolo `PROMPT_MESTRE_ORQUESTRACAO.md`, commits `b628dc1`, `e8546bd`, `9dc2620`, `cb3e7b1`, `bc53564`, `44a9ae2` — ver detalhes e evidências em `TEST-PLAN.md` e histórico completo em `PLANO_MELHORIAS_PROGRESSO.md`): 12 bugs encontrados jogando de verdade (encontros selvagens mortos, save de posição não persistindo, PC perdendo monstro depositado, mensagens de batalha bloqueadas pela grade de comandos, Hall da Fama reabrindo, etc.) + 2 bugs de layout encontrados por verificação com modelo de visão (título cortado/menu estourando, caixas de HP sobrepostas na batalha) — todos corrigidos e re-testados. **Isto não é a Fase 3 do plano abaixo** (que é sobre UX/features novas) — foi endurecimento do que já existia (Fases 0-2).
- ⏳ Fases 3–6 (deste plano) pendentes (ver abaixo).

---

## Fases

### Fase 0 — Fundação (risco zero, primeiro commit)
- [ ] `git init` + commit inicial do estado atual (arquivo inalterado).
- [ ] `.gitignore` mínimo (backups do Drive, `*.tmp`, `node_modules/` caso apareça).
- [ ] README curto: o que é, como rodar (abrir o HTML), controles.
- **Verificação:** `git log` mostra commit inicial; `git status` limpo.

### Fase 1 — Higiene do código (sem mudança de comportamento)
- [ ] Unificar fórmula de stats em **uma** função (`curStatsMon` é a usada em batalha/UI; `statsOf` e `curStats` viram chamadas dela ou são removidas).
- [ ] Remover código morto: `tryRun`, `battleMoveName`, `monStatus`, `waitClick`, campo `UI.mode`, variáveis nunca usadas (`idx` em `newMonster`, `gained` em `levelUp`, `dir` em `applyStages`, `id` em `dexOpen`).
- [ ] Mover `RIVAL_TEAMS`/`GYM_DEFS`/`CHAMP_DEF`/`CLS_MONEY`/`HIDDEN_ITEMS` para antes de `boot()`; apagar marcadores `__NEXT__`.
- [ ] Decidir `trainer.reward` vs `CLS_MONEY`: usar o campo `reward` se existir (senão o dado é enganoso) ou removê-lo.
- [ ] `window.__game` só expõe com `?debug` na URL.
- **Verificação:** jogo abre e roda igual; `git diff` sem mudança de comportamento.

### Fase 2 — Correções de bugs
- [ ] Inimigo também rola precisão (`mv.a<101` para ambos os lados).
- [ ] Level-up: aplicar ganho de HP atual (`m.hp += maxHp(m)-beforeHp`, sem estourar o novo max) — usar a variável `gained` hoje morta.
- [ ] Mensagens incorretas: Doce Raro em monstro desmaiado em batalha diz "fora da batalha".
- [ ] `S.prev` após whiteout: garantir que o warp "voltar" do Centro leve ao último centro (hoje `S.prev` pode ficar obsoleto).
- **Verificação:** cenários reproduzidos antes/depois (batalha com golpe de baixa precisão do inimigo, subida de nível, uso de doce, whiteout).

### Fase 3 — UX e jogabilidade
- [ ] Tela de resumo do monstro (menu Equipe → Resumo): sprite, tipo, stats, golpes com tipo/PP, status, evolução pendente.
- [ ] Apelido na captura/primeira evolução (opcional, nome padrão = espécie).
- [ ] Correr: segurar B (ou Shift) no overworld dobra velocidade.
- [ ] Repelir: item que zera encontros por N passos.
- [ ] Autosave (toggle no menu) + exportar/importar save (texto base64 copiável/colável).
- [ ] Persistir mudo/volume em `localStorage`.
- **Verificação:** fluxos de jogo reais no navegador (abrir resumo, correr na rota, repelir, exportar/importar save).

### Fase 4 — Polimento mobile / instalação
- [ ] Manifest PWA (nome, ícone gerado, display standalone) + `theme-color` + favicon.
- [ ] Haptics (`navigator.vibrate`) em golpes críticos/captura.
- [ ] Opção de velocidade de batalha (normal/rápida) — encurta `wait`s.
- [ ] Menu de pausa em batalha: "Desistir" (conta como whiteout) para não prender o jogador.
- **Verificação:** instalar via navegador mobile, vibrar, batalha rápida, desistir.

### Fase 5 — Conteúdo (opcional, maior esforço)
- [ ] Shiny (o pipeline de sprite já aceita cores; só falta gerar paleta alternativa + chance).
- [ ] Pós-jogo: revanche dos líderes/elite com equipes mais fortes.
- [ ] Itens visíveis no chão (bolinha brilhante) em vez de só `i` escondido.
- [ ] NPCs com quests simples (buscar item, derrotar treinador).
- **Verificação:** gameplay completo + pós-jogo.

### Fase 6 — Testes (opcional)
- [ ] Extrair lógica pura (tabela de tipos, stats, fórmula de dano, fórmula de captura, `newMonster`) para módulo testável.
- [ ] Harness `node:test` + smoke test com navegador headless (boot, novo jogo, 1 batalha).
- **Verificação:** `npm test` verde; smoke test passa.

---

## Fora de escopo (decisão explícita)
- **Não** dividir em múltiplos arquivos: o "um único HTML portátil" é característica do projeto.
- **Não** reescrever o motor de batalha.
- **Não** adicionar recursos online (o jogo é 100% offline por design).

## Critérios de aceite globais
- Cada fase termina com o jogo abrindo e jogável no navegador (smoke test manual).
- Sem mudança de comportamento em fases declaradas "sem mudança de comportamento".
- Commits atômicos por fase (após Fase 0).
