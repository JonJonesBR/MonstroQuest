# MonstroQuest

RPG de captura de monstros que roda inteiro em **um único arquivo HTML**.
Sem build, sem dependências, sem servidor, 100% offline.

👉 **[Jogar no GitHub Pages](https://jonjonesbr.github.io/MonstroQuest/)**

*(Se o link não responder na primeira visita, o GitHub pode levar alguns
segundos para publicar o site.)*

Inspirado no gênero de coleção de monstros, com identidade própria: as espécies,
os golpes e as regras de status são do jogo.

## Como jogar

Abra `index.html` em qualquer navegador moderno — desktop ou mobile. Não há
nada para instalar.

Se preferir servir por HTTP (recomendado no celular, para habilitar tela cheia
e o modo de depuração):

```bash
python -m http.server 8765 --bind 127.0.0.1
# abra http://127.0.0.1:8765/
```

O progresso é salvo automaticamente no `localStorage` do navegador, com
versionamento, migração de saves antigos e backup do save anterior. Nada sai do
seu aparelho.

## O que tem dentro

- **42 espécies** com evoluções, papel próprio e curva de stats que sustenta o
  papel
- **60 golpes** em 18 tipos, todos com trade-off: prioridade, recuo, recarga,
  dreno, multi-hit, crítico, setup
- **19 mapas**: 4 cidades, 4 rotas, uma caverna, centro de cura, loja,
  laboratório, casa e as salas dos 4 ginásios e do campeão
- **4 ginásios**, cada um com uma lição de estratégia diferente, mais um rival
  com 3 fases e um campeão de 6 monstros
- Batalhas por turnos com status e imunidade por tipo, estágios, STAB,
  críticos, captura com chance visível antes de gastar a bola, XP e decisão de
  evolução
- IA com 4 níveis de dificuldade que troca de monstro quando o confronto exige
- Centro de Cura, loja com compra e venda, e PC para organizar a equipe

## Controles

| Ação | Touch | Teclado |
|------|-------|---------|
| Andar | Toque no tile desejado | Setas / WASD |
| Interagir (NPC, porta, placa) | Toque no objeto | `Enter`, `Z` ou `Shift` |
| Cancelar | Botão de voltar | `X`, `Esc` ou `Backspace` |
| Menu | Botão ☰ | `M` |
| Som / Tela / Fullscreen | Botões da barra superior | — |

> Atenção: o teclado `WASD` inclui `A`, que move para a **esquerda** — não
> interage.

## Ferramentas de depuração

Abra com `?debug` na URL para expor o objeto de desenvolvimento. No console:

| Comando | O que faz |
|---------|-----------|
| `MonstroQuestTests.runAll()` | 52 verificações internas: dano, STAB, status, captura, save, mapa, IA, party/PC |
| `MonstroQuestBalance.run()` | Relatório de balanceamento: BST, DPS por golpe, matriz de tipos, curva de EXP, equipes |
| `GameRNG.seed(1234)` | Torna a próxima batalha reproduzível; `GameRNG.unseed()` desfaz |

## Créditos

Projeto original no gênero de coleta e captura de monstros. Sem afiliação com
Nintendo, Game Freak ou qualquer franquia.