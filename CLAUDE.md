# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Proyecto

Clon de Asteroids en HTML5 Canvas y JavaScript puro. Sin dependencias, sin bundler, sin tests, sin linter. El README y los textos de la interfaz están en español.

## Cómo correrlo

Abre `index.html` en el navegador, o sirve la carpeta con `npx serve .` (http://localhost:3000). No hay paso de build.

## Arquitectura

Toda la lógica vive en `game.js` (cargado desde `index.html`), organizada en secciones con comentarios:

- **Mundo fijo**: el canvas es de 800x600 (`W`, `H`); todo envuelve los bordes (espacio toroidal) mediante `wrap()`. Balas, asteroides y nave lo usan.
- **Entidades** (`Bullet`, `Asteroid`, `Ship`, `Particle`): cada clase tiene su `update(dt)` y `draw()` y usa el `ctx` global. Se eliminan con la bandera `dead`, filtrando los arreglos globales en cada frame (sin `splice` in situ).
- **Tamaños de asteroide** del 1 al 3, usados como índice en los arreglos paralelos `RADII`, `SPEEDS` y `POINTS`. `split()` devuelve dos hijos de tamaño `size - 1`.
- **Estado global** (`ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`) se reinicia con `initGame()`. `state` es una pequeña máquina de estados: `'playing'` → `'dead'` (temporizador de 2 s para reaparecer) → `'playing'`, o `'gameover'` (Espacio reinicia).
- **Input**: `keys` guarda las teclas mantenidas; `pressed(code)` es una lectura de "recién presionada" de un solo uso que consume la bandera. El disparo usa `pressed('Space')` (sin autofuego); rotación y propulsión leen `keys` directamente.
- **Loop**: `requestAnimationFrame` → `update(dt)` + `draw()`; `dt` está en segundos y limitado a 0.05 para evitar saltos grandes al cambiar de pestaña. Todas las constantes de movimiento son por segundo, excepto el rozamiento de la nave (`DRAG`), que se aplica por frame.
- Las colisiones son chequeos simples de círculos (`dist` contra radio); nave contra asteroide usa `a.radius * 0.82` como margen de tolerancia y se omite mientras `ship.invincible > 0`.
