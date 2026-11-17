# tiny-chess-01 entry

A chess engine in a single `main.py` of at most 4,096 bytes (Python 3.13,
standard library only). It plays the make.fun tiny-chess-01 gauntlet through
the env at `MAKEFUN_ENV_URL` (POST /game, /move) and is fully deterministic:
fixed node budget, no clock, no unseeded randomness, so replays match.

## Design

- 0x88 board, precomputed knight/king/slider ray tables, incremental
  Zobrist hash, make/unmake with full state restore (perft-verified).
- Negamax alpha-beta with iterative deepening, transposition table,
  quiescence search (captures, promotions, check evasions), null-move
  pruning, PVS, MVV-LVA and killer-move ordering.
- Repetition-aware: the game history is replayed at each move and any
  position already seen scores as a draw inside the search.
- Evaluation: material plus procedural piece-square terms (pawn
  advancement, piece centrality, rook on the seventh rank).
- The shipped `main.py` packs the engine source as an lzma + base85
  payload with a two-line unpacker (the same packing the rules allow;
  payload and unpacker together are exactly within the 4,096-byte cap).
