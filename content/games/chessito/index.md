+++
date = "2024-06-25T00:00:00+02:00"
draft = false
title = "Chessito"
toc = false
tags = ["game", "chess", "boardgame", "strategy", "rust"]
categories = ["game", "rust"]
description = "A Chess inspired game with custom game rule"
image = "thumbnail.png"
+++

[Play ChessIto on itch.io](https://mewily.itch.io/chessito){target="_blank"} 

<iframe frameborder="0" src="https://itch.io/embed-upload/10700404?color=210033" allowfullscreen="" width="960" height="560"><a href="https://mewily.itch.io/chessito">Play ChessIto on itch.io</a></iframe>

https://mewily.itch.io/chessito

{{< qr text="https://mewily.itch.io/chessito" />}}

Loading this game can take some time.

You can play it directly on [itch.io](https://mewily.itch.io/chessito){target="_blank"}

Can be played on Pc and Mobile



# Relics

Relics allow you to change some aspect of the game.

- <img src="pin.png" alt="Relic pin" class="inline-block align-text-bottom" style="height: 4rem; width: 4rem;" /> Pin / Anticipation 📌

Remove move that lead  to an imminent defeat. Also display yellow and red pin into concerned piece.

- <img src="twice.png" alt="Relic twice" class="inline-block align-text-bottom" style="height: 4rem; width: 4rem;" /> Play twice x2

Allow you to do 2 move during your turn. You can also move the same piece twice.

- <img src="explosion.png" alt="Relic explosion" class="inline-block align-text-bottom" style="height: 4rem; width: 4rem;" /> Explosion ! 💥

When a piece is captured, neighboring pieces are also captured. Regular pawn are immune to explosion.

- <img src="absorb.png" alt="Relic absorb" class="inline-block align-text-bottom" style="height: 4rem; width: 4rem;" /> ~~Kirby~~  Absorbed mode : ♞

When you capture a pawn, your piece also absorbs the pawn's move set!

*The relic system is inspired by Slay the Spire*

# Player and Cpu

Click on an icon to change the player kind :

- <img src="human.png" alt="Player human" class="inline-block align-text-bottom" style="height: 4rem; width: 4rem;" /> Human player.
- <img src="cpu_easy.png" alt="Player cpu easy" class="inline-block align-text-bottom" style="height: 4rem; width: 4rem;" /> Cpu on easy.
- <img src="cpu_normal.png" alt="Player cpu normal" class="inline-block align-text-bottom" style="height: 4rem; width: 4rem;" /> Cpu on normal.
- <img src="cpu_hard.png" alt="Player cpu hard" class="inline-block align-text-bottom" style="height: 4rem; width: 4rem;" /> Cpu on hard.

# Ui Buttons

- <img src="lightning.png" alt="Ui lightning" class="inline-block align-text-bottom" style="height: 4rem; width: 4rem;" /> Lightning ⚡ : Allow you to see all player move set !

- <img src="undo_redo.png" alt="Ui undo redo" class="inline-block align-text-bottom" style="height: 4rem; width: 9rem;" /> Undo Redo ⌛ : Go back in time to cancel a bad move.

# Rule

*ChessIto* has a few minor differences from traditional chess :

- No stalemate or draw. If you can't move, you lose.
- Promotion is forced to Queen
- No repetition rule

# Credits

The full list of credits is available in-game.

- [Modern chess sprite by Cburnett](https://commons.wikimedia.org/wiki/Template:SVG_chess_pieces)
- [Wood Texture by William Warby](https://www.flickr.com/photos/wwarby/5106733699/in/photostream/)
- [Music Stream Loops 2023/11/29 by Tallbeard Studios](https://tallbeard.itch.io/music-loop-bundle)
- [Sound Effects are from Freesound](https://freesound.org/)
- [Pixel Art Ui Icon by Thomas / Mewily](https://mewily.itch.io/hud-icon-32px)

# Code Source

This game was made in [Rust](https://www.rust-lang.org/) using [Macroquad](https://macroquad.rs/)

The code source of the game is [available on github here](https://github.com/Thomas-Mewily/chessito).
