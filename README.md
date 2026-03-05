# AutoKick for Ayaneo Pocket S2

AutoKick is a small Android utility that automatically “unsticks” certain emulators when they freeze by performing a very fast notification shade open/close (“shade blink”). It runs as a foreground service and uses **Shizuku** to run the required shell commands.

## What problem does it solve?

On the Ayaneo Pocket S2, certain emulators can desync and end up being put in a idle state making the game freeze or run at a sub 10 fps. 
There are a couple of things that correct this:
1. Opening and closing the notification shade.
2. Minimising and opening the emulator app.
3. Locking and unlocking the S2.
4. Enabling Vsync per app. (Not all emulators have this option)

AutoKick detects this stall state and triggers a tiny SystemUI “shade blink” which reliably kicks the display/input pipeline back into a working state.

## How it works

When the service is running, AutoKick:
1. Checks the current foreground app (top resumed activity).
2. If the foreground package name matches an emulator keyword, it continues.
3. Reads SurfaceFlinger state and checks:
   - `layerHistory={..., active=X}`
4. If the device is in the bugged/stalled state (`active=0`), it runs the kick command:
   - `cmd statusbar expand-notifications; sleep 0.001; cmd statusbar collapse`

To reduce false triggers (menus/paused screens), AutoKick uses a “probe” kick rule:
- If `active` stays `0` after a kick, AutoKick assumes you’re in a menu/paused state and suppresses further kicks until `active` becomes `1` again.

## Requirements
- Ayaneo Pocket S2
- Shizuku installed: https://play.google.com/store/apps/details?id=moe.shizuku.privileged.api&hl=en_GB
- Shizuku running in root mode 

## Install
1. Install Shizuku from the Play Store.
2. Start Shizuku in root mode (open Ayasettings -> Device -> Root Scripts -> Run Shizuku in Root Mode)
3. Install the AutoKick APK (from **Releases**).
4. Open AutoKick and grant Shizuku permission when prompted.

## Usage
- **Start service**: begins monitoring and auto-kicking when needed.
- **Stop service**: stops monitoring.

### Settings on the main screen

- **Detection interval** (slider): how often the service checks the foreground app + stall signal.  
  Range: 100ms–2000ms (default 500ms)

- **Shade duration** (slider): delay between shade expand and collapse during the kick.  
  Useful if your shade sometimes opens but doesn’t close reliably.  
  (Higher values are usually more reliable.)

- **Instant animations** (toggle): when enabled, while a matching emulator is in the foreground AutoKick sets:
  - `window_animation_scale = 0`
  - `transition_animation_scale = 0`
  - `animator_duration_scale = 0`  
  and restores your previous values when you leave the emulator or stop the service.

  The app will monitor for when the GitHub updates apps with the issue and will automatically add detection to your installed app when online.

**Keyword substring match**
