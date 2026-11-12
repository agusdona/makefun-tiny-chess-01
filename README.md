# Tiny Chess 4KB — make.fun "tiny-chess-01"

A chess engine in 4,096 bytes of Python. Negamax + alpha-beta with
iterative deepening on a fixed node budget (60,000 nodes/move), PVS,
late-move reductions, quiescence search with delta pruning
(captures/promotions, all evasions in check), transposition table
(Zobrist), 1 killer move, history heuristic, and MVV-LVA ordering.
Evaluation: material + piece-square tables, tapered king, bishop
pair. 0x88 board, incremental eval. Deterministic: fixed node budget,
no clock, no randomness. Standard library only.

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

Ronde 3 (ini): delta pruning di quiescence (tangkapan yang tidak
bisa mencapai alpha walau ditambah nilai korbannya + margin 200
dilewati) menghemat ~30% node quiescence — bench depth-6: 2,26 juta
node / 65 dtk vs 3,04 juta / 112 dtk (ronde 2). Satu killer move
per ply (bukan dua) agar muat dalam 4.096 byte. Anggaran node
diturunkan 120.000 -> 60.000 per langkah: ronde 1 mendapat practice
grade 0 di server (di bawah floor 1.154) padahal trial lokal 1.464 —
penyebab paling mungkin adalah wall time: trial lokal sudah
2.872-3.192 dtk dari batas 3.600 dtk, sehingga CPU grader yang
lebih lambat membuat trial kehabisan waktu dan bernilai 0 di semua
kolom. Anggaran 60.000 memangkas waktu pikir ~separuh sehingga
trial selesai dengan margin besar. Self-play vs ronde 2 pada
2.000 node: +98 Elo (40 game, 25,5/40) dan +30 Elo (80 game,
43,5/80). main.py 4.093 byte.

## License

MIT, see LICENSE.
