# Star Trader

Star Trader, the 1973 HP BASIC trading game by Dave Kaufman, in Dave
Hassler's 2026 solo version for Altair 680 BASIC, on the
[Picocomputer 6502](https://picocomputer.github.io) in
[BASIC](https://github.com/picocomputer/msbasic).

<!-- rp6502
preset: basic
publish: startrader.zip
frames: 960
-->
[![Play Star Trader](https://rumbledethumps.github.io/star-trader/startrader/screenshot.png)](https://rumbledethumps.github.io/star-trader/startrader/)

[Play it in your browser](https://rumbledethumps.github.io/star-trader/startrader/).

It is the 22nd century, and humanity has settled nine star systems near
Sol. With a four-year loan from the Interstellar Bank and a beat-up
merchant ship, buy goods where they are cheap, jump to systems that
want them, and sell at a profit. Pay off the loan before 2211, survive
racketeers, crooked cops, storms and the Space Patrol, and rise from
Ensign to Grand-Master Captain, Lord of Trade.

Type a command letter, then press Enter:

| Command | Does                                                     |
| ------- | -------------------------------------------------------- |
| `I`     | Cargo and ship status                                    |
| `T`     | Trade table: buy, sell, and repair the ship              |
| `M`     | System library: population, tech and agriculture levels  |
| `J`     | Hyper jump chart, then 0-9 to jump or Q to stay          |
| `B`     | Borrow or withdraw cash, at Sol or an Advanced system    |
| `D`     | Deposit cash in the bank, at Sol or an Advanced system   |
| `U`     | Unload the ship into the warehouse at Sol                |
| `L`     | Load the ship from the warehouse at Sol                  |
| `Q`     | Quit                                                     |

[docs/instructions.md](docs/instructions.md) is the full manual.

## Building and running

The `basic` preset packages BASIC with `src/startrader.bas` into one ROM,
`build/basic/startrader.rp6502`. When BASIC starts, it runs
`src/autorun.bas`, which says the game is loading and runs
`src/startrader.bas`; the load takes about 15 seconds. The configure
fetches the latest release of BASIC, and the first configure also
downloads the emulator into `tools/`.

```bash
$ cmake --preset basic
$ cmake --build --preset basic
$ tools/rp6502-emu build/basic/startrader.rp6502
```

In VS Code, choose the `basic` preset and press F5. On a Picocomputer,
`INSTALL startrader.rp6502`, then type `STARTRADER`. When a game ends,
type `RUN` to play again.

## Testing

`tests/play.txt` is an emulator script that plays a game with `--seed 1`:
it reads the instructions, trades, uses the bank and the warehouse, jumps
between systems, tries bad input at the prompts, quits, and starts a
second game with `RUN`.

```bash
$ ctest --preset basic
```

## Web player

`rp6502_web()` in `CMakeLists.txt` packages the ROM into
`build/basic/web/startrader.zip`, with the page settings in its
`CONFIG`; in VS Code, "RP6502-WEB" plays it in a browser. The comment
above the play link names the zip, and `.github/workflows/web.yml`
publishes it to GitHub Pages on each push to `main`. See
[RP6502-WEB](https://picocomputer.github.io/web.html).

## Changes for the Picocomputer

`src/startrader.bas` keeps the line numbers of the Altair version, so the
manual's line references still hold. The changes are:

- Line 1 no longer reserves string space with `CLEAR 1000`, which this
  BASIC neither needs nor accepts.
- The screen is cleared with `ESC [ H ESC [ 2 J`, since `ESC [ 2 J` alone
  leaves the cursor where it was.
- BASIC seeds `RND` from hardware entropy when it starts, so line 65 no
  longer seeds it from a 6522 timer.
- The `FOR`/`NEXT` delay loops are timed pauses with `CLOCK(0)`, at
  lines 2180-2195, so they last as long as they did on a 1 MHz machine.
  Two messages that the next screen cleared at once get a pause too.
- `CAPS 0` lets the ship's name keep its case, then `CAPS 1` keeps the
  commands in capitals, instead of asking for Caps Lock.
- The planet names in `DATA` are quoted, because this BASIC capitalizes
  unquoted `DATA`.
- Columns that relied on Altair BASIC's 14-character comma zones use
  `TAB(14)`, since this BASIC's zones are 10 characters.
- This BASIC counts the hidden part of an escape sequence toward
  `TAB()`, so the highlighted row of the trade table is adjusted to line
  up with the others.
- This BASIC prints 9 digits where Altair BASIC printed 6, so prices,
  cash, the bank balance, fuel and the stardate are rounded to
  hundredths, and the prices of ships and shields to whole credits. Fuel
  and the stardate are kept rounded, so a jump that uses exactly the
  last of the fuel arrives instead of running out mid-flight.
- Altair BASIC stopped the program on a blank Enter, where this BASIC
  gives the program an empty answer. A blank Enter continues at a "Press ENTER" prompt, and
  asks again for the ship's name, an item, a destination, or an answer
  to the outlaws, the crew or the Space Patrol.
- `J` says why when the ship is overloaded or out of fuel, where the
  Commodore 16 version beeped.
- The game tells you to type `RUN` to play again when it ends.
- The Load prompt lost a stray parenthesis, "- how much)".

And these fix bugs of the original:

- Paying off the outlaws took your rank plus one in credits, not their
  price.
- The outlaws' "now-ruined" deflector shield was never used up, and
  losing half your cargo to them never touched the food.
- Outlaw damage was fractional, so it could never be fully repaired.
- Declining the deflector shield offer installed it anyway.
- The crew's 5,000-credit bonus could not be paid with exactly 5,000.
- A debt payment took the minimum, whatever you entered. When the
  minimum was all of your cash, it had to be typed to the last fraction
  of a credit, and "Try again." could repeat forever.
- Paying off the loan with a deposit, then borrowing again, left the old
  loan unpaid, and the ship was repossessed in 2211.
- A system at tech level 19 could grow past the cap, until repairs there
  had a negative price and paid you.
- Buying 2.5 units cost 2 and delivered 2.5, selling 2.5 paid for 2 and
  took 2.5, and selling all of a fractional amount of fuel paid only for
  the whole units.
- Negative amounts to buy, sell, repair, borrow, deposit or pay made
  money from nothing, and a huge amount to buy stopped the game with
  `?OVERFLOW ERROR`.

## Credits

- Original multi-player HP BASIC game by Dave Kaufman, 1973.
- Solo version by Richard Woolcock and Cameron Duffy, in the *Commodore
  16 Games Book*, Melbourne House, 1984.
- Translated, revised and expanded for Altair 680 BASIC by Dave Hassler
  ([fishhack66/6502-Bits](https://github.com/fishhack66/6502-Bits)),
  2026.

[MIT License](LICENSE).
