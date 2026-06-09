# gmsv_physframe

> [!WARNING]
> HERE BE DRAGONS

*Hooks into Source engine physics to provide crash mitigation / physics simulation control.*

Intercepts `PhysFrame`, `PhysIsInCallback`, `PhysOnCleanupDeleteList`, and "IVP tree expansion" to:
- Pause/unpause physics simulation from Lua (`_G.SetShouldSimulate`, `_G.GetShouldSimulate`)
- Time individual physics frames (`GetPhysFrameLength`) ([now implemented in GMod](https://github.com/Facepunch/garrysmod-requests/issues/2042))
- Suppress physics cleanup when the server is about to crash (used by [`gmsv_segfault`](https://github.com/Python1320/gmsv_segfault))
- Detect "runaway" "IVP tree expansion" and fire a Lua callback (`PhysFailed`) (false positives?)

**Also includes:**
 - **`stop_physics_damnit`**: Callable from C to prevent physics cleanup deletion during crash handling.

**Lua API (registered on successful init)**

| Lua function | C function | Description |
|---|---|---|
| `SetShouldSimulate(bool)` | `SetShouldSimulate` | Pause/resume physics simulation |
| `GetShouldSimulate()` | `GetShouldSimulate` | Returns whether physics is simulating |
| `GetPhysFrameLength()` | `GetPhysFrameLength` | Returns last physics frame time in seconds |
| `PhysFailed()` | (callback) | Called when IVP tree expansion exceeds safe limits |

**Build**
1. [Install premake](https://premake.github.io/download/)
2. Copy this repo inside the gbins alongside all the other binary modules
3. Run `build.cmd` or `build` as appropriate

**Install**
1. Put `gmsv_physframe_linux.dll` into `garrysmod/lua/bin/`
2. Install autorun lua to server.

### Notes

- Depends on symbol signatures from `server_srv.so` and `vphysics_srv.so` — x86-64 branch not supported.
- Physics pausing works by patching the `m_bPaused` field at offset `+0x58` on `CPhysicsHook`, uhoh.

## Thanks

Garry, [Metastruct](https://metastruct.github.io), FreezeBug, BlueShank, MetaMan, SpiralP, CapsAdmin, etc, etc etc
