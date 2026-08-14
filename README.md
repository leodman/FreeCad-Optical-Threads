# FreeCAD Optical Threads

A small FreeCAD project for generating **real, printable optical/camera/telescope threads** without manually sketching and debugging a helix every time.

## Current v0.1 scope

The current generator creates:

- **T2 / M42 × 0.75 internal thread**
- 60° metric-style thread geometry
- Parametric thread/sleeve length
- Parametric wall thickness
- Practical fit presets
- Independent diametral clearance
- Independent root relief (deeper thread valley)
- Independent crest truncation
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

## v0.1.3 fit model

The v0.1.3 correction is based on print testing: clearance must not be obtained by simply making the complete thread profile shallower.

Three parameters are now separated:

- `DiametralClearance`: shifts the female thread outward for fit.
- `RootRelief`: cuts the thread valley/root deeper, radially.
- `CrestTruncation`: removes material from the thread crest/summit, radially.

Pitch and helix geometry remain unchanged.

The corrected geometry is built on the more robust v0.1.2 swept-profile implementation, including the cylinder seam workaround and a four-sided cutter with finite flats to reduce OCC sweep/Boolean failures.

### FDM print default

| Parameter | Default |
|---|---:|
| Diametral clearance | +0.20 mm diameter |
| Root relief | +0.10 mm radial |
| Crest truncation | +0.05 mm radial |

For M42 × 0.75 this gives approximately:

- Basic internal minor diameter: **41.1881 mm**
- Effective female crest diameter: **41.4881 mm**
- Effective female root diameter: **42.4000 mm**
- Effective radial groove depth: **0.4560 mm**

The practical fit presets are not certified ISO tolerance classes such as 6H/6g. Printer, resin, filament, machining process, material, and the mating commercial part can all require adjustment.

## T2 geometry

- Designation: **M42 × 0.75**
- Nominal diameter: **42.0 mm**
- Pitch: **0.75 mm**
- Thread angle: **60°**
- Default hand: right-hand

For the basic internal minor diameter the macro uses:

`D1 = D - 1.082531754 × P`

The selected print-fit corrections are then applied independently to the crest and root geometry.

## Planned next steps

- M42 × 0.75 external
- Paired male/female generation so printed parts are generated as a matched pair
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

The macro uses FreeCAD's `Part` geometry API (`makeLongHelix`/`makeHelix`, sweep/pipe shell, Boolean cut) and creates a `PartDesign::FeaturePython` object.

## License

MIT.
