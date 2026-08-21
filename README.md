# MonstroQuest GBA

Clone de Pokémon em **um único arquivo HTML** (sem build, sem dependências, 100% offline).

## Como jogar

Abra `index.html` em qualquer navegador (desktop ou mobile). O save é salvo automaticamente no `localStorage` do navegador.

## Controles

| Ação | Touch | Teclado |
|------|-------|---------|
| Andar | Toque no tile desejado | Setas / WASD |
| Interagir (NPC, porta, sinal) | Toque no objeto | A (Enter / Z) |
| Cancelar | — | B (X / Backspace) |
| Menu | Botão ☰ ou toque em um NPC | M |
| Som / Tela / Fullscreen | Botões da barra superior | — |

## Conteúdo

- 42 espécies com evoluções, 18 tipos, ~46 golpes
- 19 mapas: 4 cidades, 4 rotas, caverna, ginásios, centro Pokémon, loja, laboratório, sala do campeão
- 4 líderes de ginásio, rival (2 fases), campeão e hall da fama
- Batalhas por turnos: status, estágios, STAB, críticos, captura, XP, evolução
- Sprites e áudio 100% procedurais (canvas + WebAudio)

## Estrutura

- `index.html` — jogo inteiro (motor, dados, UI, sprites, áudio)
- `PLANO-MELHORIAS.md` — plano de melhorias por fases

## Créditos

Fan game inspirado na série Pokémon (GBA). Sem afiliação com a Nintendo/Game Freak.
