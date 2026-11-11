# Sega Rally 2 Native: roadmap

State on 2026-11-09: the game boots, plays its attract mode and a whole race in
widescreen with sound and music, driven by keyboard, gamepad or (untested)
steering wheel. The code base is Star Wars Trilogy Native (SWT, commit 4f99080);
much of what is SWT-specific is switched off, not yet replaced.

## Done

- ROM layout (`romtool.py`, `rom_zip.cpp`), names, `SR2_*` variables
- Network (link) board read as absent: the game skips it
- Car controls: keyboard, gamepads, wheels via `save/wheel.ini` (experimental)
- Race HUD at the window's corners in widescreen (times and practice's laps
  top left, position top right, rev counter with its 3D needle and speed
  bottom left, car name bottom right; time left and arrow in the middle);
  `--hud-centre` keeps it in the 4:3 area. Sound gain 5 = unity
- Build scripts brought up to SWT main (find_vs, CMake 3.27 check, DLSS SDK check)
- SWT's viewport widening (it wrote to SWT RAM addresses) switched off

## 1. Things that are broken or SWT-specific (first)

1. ~~**PLAY from the main menu.**~~ Done: through SR2's main sequence
   (0x22D618 current, 0x22D61C next) and its START step (0x22436C).
2. ~~**TEST button**~~ Done: the interrupt's warm restart (rfi to 0x4C) is
   followed (WarmRestart); the TEST menu works and EXIT restarts the game.
3. ~~**Leftover SWT hooks**~~ Mostly done: SWT's scene passes, HUD layout,
   viewport widening, stats and aim hooks are off. Still to clean: the
   frontend (logo, texts, options) and SWT-only code left in the sources.
4. **Graphics glitches**: a big dark polygon in the attract mode (right side of
   the RANKING screen), and the dust cut short.
5. **Widescreen culling**: check whether objects pop in at the sides at 16:9;
   if so, find SR2's own viewport table and widen it as SWT does.

## 2. Bring over SWT's engine fixes since the base

- Renderer start on GPUs that take few images per shader, and say which step
  fails (SWT issue #36: `d2e5e40`, `f45b18b`, CMake `_few` shaders)
- Bind descriptor sets after selecting the pipeline (`2c39bf1`)
- OPTIONS > DISPLAY > WIDESCREEN from 4:3 to 48:9 (SWT issue #28, `40fd23e`)
- `--no-menu` still plays the intro video (`3b0e522`)
- UPDATE AVAILABLE button labels centred (`3ee3937`)
- Options test helper rename (`efdba08`)

## 3. Sega Rally 2 itself

- Force feedback: SR2's drive board is not SWT's joystick board; check the
  Z80 program's ports (Supermodel's WheelBoard) so the force follows the road
- Idle loops (vblank waits) hooked, for speed and frame pacing
- Records and achievements: stage times, positions, cars, championship
  results, from SR2's RAM
- Interpolation and motion check at 120/144 Hz with the cars at speed

## 4. Menus and presentation

- The SEGA logo and the attract never on screen: everything goes through the
  main menu, as in Lost World and Star Wars Trilogy (the boot skip and the
  run on to SELECT GAME already hide them; check every path: boot, game
  over, QUIT, TEST, name entry of a ranked time)
- Main menu: SR2 logo, menu music from SR2's own tracks, texts and options
  (no crosshair, sight or duel options; wheel settings; transmission)
- Gamepad button prompts for the car controls
- Achievements toasts and Records screen for SR2
- "More recomps" links between TLW, SWT and SR2

## 5. Release

- README for SR2 (controls, ROM `srally2.zip`, install). The wheel support is
  marked **experimental, never tested on a real wheel**, with a request for
  feedback from wheel owners
- news.txt, update.bat / update.ps1 pointing at the public repository
- Public repository `SegaRally2Native` (origin) beside the private `-dev`
- Later: the macOS build (SWT `98da91e`, `96dae1f`, `a5770f0`)
