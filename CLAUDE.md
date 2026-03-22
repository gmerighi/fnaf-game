# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

**5 Noites na Fabby's** — jogo estilo FNAF (Five Nights at Freddy's) para crianças, rodando no browser.
Arquivo único: `index.html` (HTML + CSS + JS vanilla, sem build). Abra diretamente no browser.

## Arquitetura

Todo o jogo vive em `index.html`. Estrutura interna:

- **Estado global `G`** — reinicializado a cada noite via `initState(night)`. Contém: `night`, `time`, `power`, `phase`, `cameraOpen`, `leftDoor`, `rightDoor`, `leftLight`, `rightLight`, `chars`.
- **Loop principal** — `setInterval(gameTick, 1000)` dispara 1x/segundo. Drena energia, move personagens, checa condições de vitória/derrota.
- **IA dos personagens** — cada personagem tem um `path[]` de salas e um `timer` que avança por segundo. Quando `timer >= speed`, o personagem avança um passo no path. Na última posição (porta), tenta atacar; se a porta estiver fechada, tem 35% de chance de recuar.

## Personagens e Paths

```
freddy → stage → dining → left_hall → left_door   (ataca pela ESQUERDA)
bonnie → stage → kitchen → right_hall → right_door (ataca pela DIREITA)
chica  → dining → left_hall → left_door            (noite 3+, ataca pela ESQUERDA)
```

## Velocidades (segundos entre movimentos por noite)

| Personagem | N1  | N2 | N3 | N4 | N5 |
|------------|-----|----|----|----|----|
| Freddy     | 999 | 65 | 45 | 28 | 18 |
| Bonnie     | 85  | 55 | 38 | 22 | 13 |
| Chica      | —   | —  | 48 | 30 | 18 |

`999` = personagem não se move (noite muito cedo para ele).

## Drenagem de Energia (% por segundo)

| Sistema    | N1     | N2     | N3     | N4     | N5     |
|------------|--------|--------|--------|--------|--------|
| Base       | 0.035  | 0.055  | 0.075  | 0.100  | 0.130  |
| Câmera     | +0.07  | +0.07  | +0.07  | +0.07  | +0.07  |
| Porta (×2) | +0.10  | +0.10  | +0.10  | +0.10  | +0.10  |
| Luz (×2)   | +0.07  | +0.07  | +0.07  | +0.07  | +0.07  |

## Fluxo de Telas

```
screen-menu → screen-nightintro (2.8s) → screen-game → screen-gameover
                                                     → screen-nightwin → (próxima noite)
                                                     → screen-gamewin  (após noite 5)
```

## Como Modificar

- **Adicionar personagem:** defina em `CHAR_DEF`, adicione em `MOVE_SPEED`, inclua em `G.chars` dentro de `initState()`.
- **Adicionar sala:** defina em `ROOMS`, adicione em `CAM_LIST`, inclua no `path[]` do personagem.
- **Ajustar dificuldade:** edite `MOVE_SPEED` (maior = mais lento = mais fácil) ou `BASE_DRAIN` (maior = energia acaba mais rápido).
- **Duração da noite:** `NIGHT_DURATION` (segundos). `SECS_PER_HOUR = NIGHT_DURATION / 6`.
