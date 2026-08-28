# Ryuumon01 Client

A modular, client-side QoL/visual Fabric mod for Minecraft 1.21.1. Dark ClickGUI
(RIGHT_SHIFT to open), draggable HUD, sound-reactive UI, and cosmetic modules
(Zoom, Fullbright, MotionBlur, NoRender toggles).

This does **not** include combat-assist modules (aimbot, killaura, reach, hitbox
expansion, anti-knockback, ESP, autoclicker). Those give an unfair advantage over
other players and most servers treat them as bannable cheats — they were left out
on purpose.

## What's here

- `com.ryuumon01.client.Ryuumon01Client` — client entrypoint, keybind + tick wiring
- `com.ryuumon01.client.module` — Module base class, Category, ModuleManager
- `com.ryuumon01.client.module.render` — HUD, Fullbright, Zoom, MotionBlur, NoRender
- `com.ryuumon01.client.setting` — Boolean/Number/Mode/Keybind/Color settings
- `com.ryuumon01.client.gui.Ryuumon01ClickGUI` — the ClickGUI screen
- `com.ryuumon01.client.sound.SoundManager` — hover/click/scroll UI sound engine
- `com.ryuumon01.client.mixin.GameRendererMixin` — FOV hook for Zoom

## Before you build

1. **Sound files are placeholders.** Drop real `.ogg` files at
   `src/main/resources/assets/ryuumon01-client/sounds/ui/{hover,click,scroll}.ogg`.
   See the README.txt in that folder for guidance. Without them the SoundEvents
   will register but silently fail to play.
2. **Icon.** `fabric.mod.json` references `assets/ryuumon01-client/icon.png` — add
   a 128x128 PNG there or remove that line.
3. **Verify the mixin target.** `GameRendererMixin` targets `GameRenderer#getFov`,
   whose exact method name/descriptor depends on the Yarn mappings build in
   `gradle.properties`. Fabric's mappings occasionally rename this — if the build
   fails on the mixin, open the decompiled `GameRenderer` class (Loom generates
   these under `.gradle`/your IDE's library sources) and confirm the method
   signature matches, adjusting the `@ModifyVariable` target if not.
4. **Fabric API version.** Double check `fabric_version` in `gradle.properties`
   against https://fabricmc.net/develop/ for the latest patch matching 1.21.1 —
   API builds get patched frequently and an old one can fail to resolve.
5. **MotionBlur shader wiring.** `MotionBlurModule` exposes a blend-factor value
   but the actual `PostEffectProcessor`/`ShaderEffect` hookup (loading
   `motion_blur.json`, updating its `BlendFactor` uniform each frame, and writing
   an accompanying GLSL fragment shader) is left as a scaffold — that plumbing is
   render-pipeline-version-specific and is the one piece you'll want to finish
   yourself against whatever exact Minecraft/Fabric API build you compile against.

## Building the jar

1. Install a JDK 21.
2. From the project root:
   ```
   ./gradlew build
   ```
   (First run downloads Minecraft/Yarn/Fabric API — needs internet access.)
3. The compiled jar lands in `build/libs/ryuumon01-client-1.0.0.jar`.
4. Drop it into your `.minecraft/mods` folder alongside Fabric Loader + Fabric API
   for 1.21.1, then launch.

## Extending it

- Add new modules under `module/movement`, `module/player`, or `module/utility`
  the same way the render ones are structured, then register them in
  `ModuleManager#registerAll()`.
- The ClickGUI automatically picks up any registered module — no GUI code changes
  needed for a new module, only for a new *setting type*.
