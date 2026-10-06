# MonstroQuest

RPG de captura de monstros que roda **inteiro dentro de um único arquivo HTML**.
Sem build, sem dependências, sem servidor — funciona offline, no celular ou no desktop.

👉 **[Jogar no GitHub Pages](https://jonjonesbr.github.io/MonstroQuest/)**

*(Se o link não abrir na primeira vez, o GitHub pode levar alguns segundos
para publicar o site na primeira execução.)*

---

## O que é

Você começa com um monstro na Vila Folha e sobe até conquistar as 4 insígnias
e enfrentar o Campeão. O caminho é: explorar → encontrar monstros selvagens →
batalhar e capturar → montar equipe → evoluir → vencer treinadores.

O jogo é um RPG de coleção no estilo GBA/portátil, com identidade própria:
os monstros, os golpes e as regras de status foram desenhados para este projeto.

## Características

- **42 espécies** com evolução, papel próprio (atacante, tanque, veloz,
  suporte, controlador, sustentação, setup) e curva de stats que sustenta o papel.
- **~59 golpes com trade-off real** — poder máximo tem custo:
  *Hiper Raio* recarrega e perde o turno seguinte, *Explosão de Fogo* causa
  recuo. Existem golpes de prioridade, múltiplos acertos, crítico, dreno,
  dano fixo, setup e debuff.
- **Status com função própria e imunidade por tipo** — queimadura corta o
  Ataque físico, veneno é dano puro, paralisia custa velocidade, e Fogo é
  imune a queimadura, Gelo a congelamento, Elétrico a paralisia.
- **IA em 4 níveis** que decide por dano esperado (STAB, efetividade,
  precisão, crítico, PP) e **troca de monstro** quando o matchup pede.
- **Captura como decisão** — a chance aparece como faixa legível
  (Difícil / Razoável / Boa / Excelente) *antes* de gastar a orbe, e nunca
  é garantida só por baixar os PS.
- **4 ginásios com uma lição cada**, rival de 3 fases que evolui junto com
  você, e um Campeão de 6 monstros com cobertura ampla de tipos.
- **Save automático** em `localStorage`, versionado, com migração de saves
  antigos e backup do save anterior.

## Controles

| Ação | Toque | Teclado |
|------|-------|---------|
| Andar | Toque no tile | Setas / WASD |
| Interagir | Toque no objeto | `A` (Enter / Z) |
| Cancelar / voltar | — | `B` (X / Esc) |
| Menu | Botão ☰ | `M` |

No celular há uma barra com **Menu**, **Som**, **Tela** (caber ou esticar) e
**Tela cheia**.

## Diagnóstico no console

Abra com `?debug` na URL e use:

| Comando | O que faz |
|---------|-----------|
| `MonstroQuestBalance.run()` | Relatório de balanceamento: BST por espécie, DPS de cada golpe, matriz de tipos, chance de captura, curva de EXP, equipes de líderes e do Campeão |
| `MonstroQuestTests.runAll()` | 52 verificações: dano, STAB, status, captura, save, mapa, party/PC |
| `GameRNG.seed(1234)` | Torna a próxima batalha reproduzível (útil para depurar) |

## Como rodar localmente

Basta abrir o `index.html` no navegador. Como não há nenhuma requisição de
rede, ele funciona direto do disco (`file://`) ou servido por qualquer
servidor estático.

## Estrutura do repositório

```
index.html   o jogo inteiro (dados, motor, UI, sprites e áudio)
README.md    este arquivo
```

Licença e afiliação: projeto original no gênero de coleta de monstros.
Sem afiliação com a Nintendo, Game Freak ou qualquer franquia.