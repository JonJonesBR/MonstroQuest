# MonstroQuest — Plano de Teste e Evidências de Simulação Real

> Sessão de auditoria: navegador headless (Chromium) via ferramenta de navegador do omp,
> servindo `index.html` via HTTP (`python -m http.server`), interação de usuário real
> (toque sintético, teclado, DOM), capturas de tela em cada estado e coleta de erros de console.

## Como rodar

```bash
cd "G:/Meu Drive/PROJETOS/MonstroQuest"
python -m http.server 8765 --bind 127.0.0.1
# abrir http://127.0.0.1:8765/ (ou ?debug para o backdoor de desenvolvimento)
```

## Fluxos testados (Fase 0 → Fase 3 do protocolo)

| # | Fluxo | Método | Resultado |
|---|-------|--------|-----------|
| T1 | Título (NOVO JOGO / CONTINUAR / APAGAR SAVE, itens desabilitados sem save) | clique real | ✅ ok |
| T1 | Intro: nome → 3 diálogos → escolha do inicial (sprites) → save automático | toque real | ✅ ok |
| T2 | Overworld: andar por toque, NPCs, placa, portas, cama, loja (comprar/vender), enfermeira (cura+save), laboratório | toque real | ✅ ok |
| T2 | Item escondido na Rota 1 (Poção em 5,10) | passo real | ✅ ok |
| T3 | Batalha selvagem: encontro, comandos, captura, fuga, vitória com XP | toque real | ✅ ok (após fix F) |
| T4 | Batalha de treinador (intro, comandos, dinheiro, sem fuga) | toque real | ✅ ok |
| T5 | Ginásio 1 (líder Ferrão) — batalha inicia | toque real | ✅ ok (após fix A) |
| T6 | Menu: Pokédex (1/42, detalhe), Equipe, Bolsa (descrição), Cartão, Salvar | toque real | ✅ ok |
| T6 | PC: retirar/depositar | toque real | ✅ ok (após fix G) |
| T7 | Save/load: salvar em interior → reload → CONTINUAR restaura posição | real | ✅ ok (após fix B) |
| T7 | Save corrompido → CONTINUAR → aviso e limpeza | real | ✅ ok (após fix E) |
| T8 | Landscape 568×320 e portrait 320×480: layout íntegro, proporção 1.5 | resize | ✅ ok |
| T9 | Campeão (equipe 6) → vitória → Hall da Fama → Continuar jogando | toque real | ✅ ok |
| — | Evolução (asperim→besourox), level-up (cura HP), aprendizado de golpe | motor + UI | ✅ ok |
| — | Captura com Poké Bola (party 2→3) | toque real | ✅ ok |
| — | Console: zero erros em todos os fluxos pós-correção | coleta global | ✅ ok |

## Bugs encontrados e corrigidos (evidência)

| Bug | Evidência (antes) | Correção | Evidência (depois) |
|-----|-------------------|----------|--------------------|
| A — líder de ginásio não responde (sem `gym` nos NPCs) | interação no gym1_int: nada (overworld, sem msg, sem erro) | `gym` prop nos 4 líderes | batalha "Ferrão" inicia (asperim/zangao/besourox) |
| B — posição do save nunca persiste (`S.x/S.y` fixos 12,13) | save em center_int → reload → P(12,13) em tile inválido, invisível e travado (`onScreen:false`) | `teleport` grava S.x/S.y/S.dir | reload → P(6,8) restaurado, caminhável |
| C — Doce Raro em batalha não sobe nível | (estático: sem `pm.lv++`; batalha selvagem inacessível pelo bug F) | `pm.lv++` | aquore lv5→lv6, HP 20/22, item consumido |
| D — Dreno de Sementes nunca drena | (estático: condição `other.leech` no monstro errado) | `other.hp>0` + mensagem correta | 2 HP drenados por turno (19→17) |
| E — save corrompido → party vazia + `chooseStarter` indefinida | CONTINUAR com JSON inválido → overworld com party EMPTY, "Bem-vindo de volta, Treinador!" | validação em `loadGame` + recovery no título | toast "Save corrompido — apagado", título restaurado |
| E2 — 1º toque após CONTINUAR reinicia o jogo | `UI.onA` do título persiste → tap abre `askName` (3 ocorrências) | `closeOverlays()` em CONTINUAR/APAGAR | tap fecha mensagem e permanece no overworld |
| F — encontros selvagens mortos (`S.battle===null` nunca é true; S.battle é `undefined`) | 50+ passos na grama: `maybeWild` nunca chamado (0 calls instrumentado) | `G.battle===null` (2 sítios) | encontro "Lagartix selvagem" no 1º passo na grama |
| G — PC perde monstro depositado (`S.box` nunca inicializado) | depositar Asperim → party "aquore", box "" (monstro sumiu) | `S.box=S.box||[]` | box "asperim" retido |
| H — mensagem de batalha nunca dispensável com grade ativa | `Input 'A'` prioriza `UI.onA` sobre `UI.msgResolve` | msgResolve tem prioridade | soft-lock eliminado |
| I — grade de comandos cobre as mensagens (z7>z6, mesmo retângulo) | "Vulcron usou Surra!" 100% oculto atrás da grade; toque no mobile impossível | `battleMsg` oculta a grade; `gridShow` restaura | mensagens visíveis/avançáveis; batalha do campeão jogável até o fim |
| J — Hall da Fama reabre após toda batalha pós-campeão | captura selvagem pós-campeão → mode "hall" | flag `hallShown` | fuga pós-campeão → overworld |
| Menores | vitória no campeão não salvava; caverna sem música | `saveGame()` antes do hall; seq `cave` | beat_champ persistido |

## Verificação visual com modelo de visão (rodada posterior)

Na rodada posterior o modelo de visão configurado respondeu normalmente; os 8 estados-chave
foram re-capturados e analisados com checklist (sobreposição, corte de texto, contraste,
alinhamento, grade oculta):

| Estado | Veredito do modelo de visão |
|--------|-----------------------------|
| Título (com save) | ❌ logo MONSTROQUEST cortado no topo; CONTINUAR sobrepondo rodapé; APAGAR SAVE fora da tela → **corrigido** |
| Overworld | ✅ pixel art íntegra, sem distorção |
| Menu principal | ✅ itens legíveis, contraste bom, painel íntegro |
| Loja | ✅ legível; layout de preço em 2 linhas (design) |
| Batalha (mensagem) | ❌ caixas HP sobrepostas (16px) e desalinhadas; nome "Aquore" com margem apertada → **corrigido** |
| Hall da fama | ✅ tudo legível, botão "Continuar jogando" visível |
| Batalha pós-fix (final) | ✅ caixas alinhadas, nomes completos, mensagem visível, grade oculta |
| Título pós-fix (final) | ✅ logo completo, 3 itens, rodapé livre, contraste bom |

Correções de layout aplicadas (commit `bc53564`):
- `#title`: layout compacto (gap/padding/fonte), `justify-content:flex-start` — logo visível
  por inteiro, 3 itens do menu dentro da área, rodapé sem sobreposição.
- `#battle .hbox`: `min-width:92px`, padding compacto, ambas as caixas `top:1.5%` —
  sem sobreposição (folga 97px), topo alinhado, nomes com margem de 10px.

## Notas de ambiente

- Modelo de visão indisponível na rodada inicial ("Budget 0 is invalid") — contornado com
  geometria de DOM + screenshots; **verificação visual completa realizada na rodada
  posterior** (tabela acima).
- Interação em dispositivo físico Android (gestos reais, áudio, haptics, PWA) não é
  automatizável neste ambiente — lista de verificação manual pendente (ver relatório).
