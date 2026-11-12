# Tiny Chess 4KB — make.fun "tiny-chess-01"

A chess engine in 4,096 bytes of Python. Negamax + alpha-beta with
iterative deepening on a fixed node budget (120,000 nodes/move), PVS,
late-move reductions, quiescence search (captures/promotions, all
evasions in check), transposition table (Zobrist), killer moves,
history heuristic, and MVV-LVA ordering. Evaluation: material +
piece-square tables, tapered king, bishop pair. 0x88 board,
incremental eval. Deterministic: fixed node budget, no clock, no
randomness. Standard library only.

`main.py` is a minified + LZMA/base85-packed build of the readable
source; the packed file is the whole engine. It talks to the env at
`MAKEFUN_ENV_URL` (POST /game, /move) and exits when every game is over.

Local practice trials vs the house gauntlet: trial-0 Elo 1,464
(house baseline sunfish 4K: 1,360).

## Cara main

Engine ini bermain dengan negamax + alpha-beta dan iterative
deepening pada anggaran node tetap (120.000 node/langkah). PVS
(principal variation search) mempersempit jendela pencarian di
langkah non-PV, dan LMR (late-move reduction) mengurangi kedalaman
langkah-langkah akhir yang terurut belakangan. Quiescence search
menenangkan posisi dengan menangkap/promosi (dan semua evasion saat
skak), transposition table berindeks Zobrist menyimpan hasil
pencarian, killer moves dan MVV-LVA mengurutkan langkah. Evaluasi:
material + piece-square tables, tapered king, bishop pair.

## Hasil

- Elo trial 1.464 vs baseline house 1.360 (trial-0/1/2 semua 9,0/16
  vs gauntlet house).
- Bench depth-5: 885.577 node.
- Semua tes lolos: perft, taktik, dan evaluasi (eval).

Ronde 2 menambahkan history heuristic: tabel history untuk
langkah quiet (bonus d*d saat beta cutoff) dipakai di move ordering
di bawah killer moves, sehingga langkah yang sering memotong
diurutkan lebih dulu. Self-play vs ronde 1 (golf): +44 Elo
(40 game, 22,5/40) dan +58 Elo (49 game, 28,5/49) pada 2.000 node.
Aspiration window (+17 Elo) dan kombinasi aspiration+history (+26)
dicoba tetapi tidak lebih baik dari history saja, jadi tidak dipakai.

## License

MIT, see LICENSE.
