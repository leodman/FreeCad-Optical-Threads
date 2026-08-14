# FreeCAD Optical Threads

A small FreeCAD project for generating **real, printable optical/camera/telescope threads** without manually sketching and debugging a helix every time.

## v0.1 scope

The first generator creates:

- **T2 / M42 × 0.75 internal thread**
- 60° metric-style V thread
- Parametric thread/sleeve length
- Parametric wall thickness
- Practical fit presets
- User-editable diametral clearance
- Recomputes when parameters change

The output is a normal FreeCAD solid that can be fused, cut, linked, cloned, or used as a building block in a larger model.

## Why this exists

FreeCAD's native Hole tool supports modeled internal threads, but its built-in diameter/pitch tables do not include many optical thread combinations. Telescope and camera adapters frequently use sizes such as **M42 × 0.75**, which leaves users rebuilding helical thread geometry manually.

This project puts the standard and geometry behind a simple generator.

## Installation as a macro

1. Open FreeCAD.
2. Open **Macro → Macros…**
3. Use **User macros location** to find your macro folder.
4. Copy `OpticalThreads.FCMacro` into that folder.
5. Run `OpticalThreads`.

The macro opens a small dialog. The generated object remains parametric after creation.

## v0.1 fit presets

`DiametralClearance` means the amount added to the diameters of the **female** thread.

| Fit | Diametral clearance |
|---|---:|
| Basic / nominal | 0.00 mm |
| Close | +0.05 mm |
| Normal | +0.10 mm |
| Loose | +0.20 mm |
| FDM print | +0.30 mm |
| Custom | user selected |

These are **practical fit presets**, not certified ISO tolerance classes such as 6H/6g. Printer, resin, filament, machining process, material, and the mating commercial part can all require adjustment.

For a commercial telescope/camera part, start with **Normal** and make a short test ring before printing or machining the final adapter.

## T2 geometry

- Designation: **M42 × 0.75**
- Nominal diameter: **42.0 mm**
- Pitch: **0.75 mm**
- Thread angle: **60°**
- Default hand: right-hand

For the basic internal minor diameter the macro uses:

`D1 = D - 1.082531754 × P`

The selected diametral clearance is then added to the female profile.

## Planned next steps

- M42 × 0.75 external
- A paired fit mode that creates matching male/female test coupons
- Additional optical standards:
  - M48 × 0.75
  - M28.5 × 0.6 (1.25-inch filters)
  - C-mount 1"-32 UN
  - SM1 / 1.035"-40
- Entry chamfer/runout controls
- Convert the macro into a proper **Optical Threads Workbench**
- Thread library stored as data rather than hard-coded UI entries
- Optional true ISO tolerance classes where the standard provides enough information

## FreeCAD compatibility

Target: FreeCAD 1.x.

The macro uses FreeCAD's `Part` geometry API (`makeHelix`, sweep/pipe shell, Boolean cut) and creates a `PartDesign::FeaturePython` object.

## License

MIT.
