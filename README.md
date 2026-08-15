# FreeCAD Optical Threads

A small FreeCAD project for generating **real, printable optical/camera/telescope threads** without manually sketching and debugging a helix every time.

## Current main version: v0.1.5

The current generator creates:

- **T2 / M42 × 0.75 internal thread**
- 60° metric-style profile
- Parametric thread/sleeve length
- Parametric wall thickness
- Practical fit presets
- Independent diametral clearance
- Independent root/valley relief
- Independent crest/summit truncation
- **Material/process shrinkage compensation**
- User-editable shrinkage/compression percentage
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

## Print-fit correction

Physical print testing showed that simply adding clearance was not enough. The printed M42 × 0.75 female thread could engage only after the sleeve was slit so it could flex outward, and comparison with a known-good metal thread showed that the generated thread valley was too shallow.

The corrected geometry therefore keeps the controls separate:

- **Nominal pitch remains 0.75 mm.**
- Fit clearance does not substitute for thread depth.
- `RootRelief` cuts the female valley/root farther outward radially.
- `CrestTruncation` removes material from the female summit/crest independently.
- `DiametralClearance` remains an independent mating-fit parameter.

For the current **FDM print** preset:

| Parameter | Value |
|---|---:|
| Diametral clearance | +0.20 mm |
| Root relief | +0.10 mm radial |
| Crest truncation | +0.05 mm radial |

For M42 × 0.75, the macro uses the ISO-style basic internal minor diameter:

`D1 = D - 1.082531754 × P`

Without material compensation, the FDM preset gives approximately:

- Basic internal minor diameter: **41.1881 mm**
- Effective female crest diameter: **41.4881 mm**
- Effective female root diameter: **42.4000 mm**
- Effective radial groove depth: **0.4560 mm**

The important design rule is that **print clearance and thread depth are separate controls**. The generator may truncate the thread summit for printability, but it still carves the valley fully enough to avoid the shallow-thread failure seen in the first printed test.

## v0.1.5 material/process compensation

A PETG print measured about **41.20 mm** at the female inner/crest diameter, versus about **41.49 mm** in the uncompensated CAD geometry. That difference is close to 0.7% at this size.

The macro therefore has a single **Material / process** selection plus an editable **Shrinkage / compression %** field.

Current material/process choices:

| Material / process | Nominal shrinkage |
|---|---:|
| No compensation | 0.0% |
| 3D PETG | 0.7% |

Selecting **3D PETG** loads **0.7%** as the nominal value, but the percentage remains editable. This lets a particular printer/material/process be calibrated empirically without changing the thread fit preset.

The compensation uses the exact inverse linear scale:

`scale = 1 / (1 - shrinkage)`

For 0.7%:

`scale = 1 / 0.993 = 1.007049345...`

This scale is applied consistently to all generated linear CAD dimensions, including:

- nominal diameter
- pitch
- thread/sleeve length
- wall thickness
- diametral clearance
- root relief
- crest truncation
- small geometric robustness allowances used by the swept cutter

The intention is that, after the assumed linear shrinkage, the cooled part returns toward the requested nominal geometry. With 0.7% compensation, the CAD pitch is about **0.75529 mm**, targeting approximately **0.750 mm** after shrinkage.

This is an empirical printing compensation, not a universal PETG material constant. Actual dimensional behavior depends on filament formulation, printer, extrusion calibration, temperature, cooling, print orientation, and slicer settings.

## Fit presets

These are practical printing/machining presets, not certified ISO tolerance classes such as 6H/6g.

| Fit | Diametral clearance | Root relief | Crest truncation |
|---|---:|---:|---:|
| Basic / nominal | 0.00 mm | 0.00 mm | 0.00 mm |
| Close | +0.05 mm | 0.00 mm | 0.00 mm |
| Normal | +0.10 mm | 0.00 mm | 0.00 mm |
| Loose | +0.20 mm | +0.05 mm radial | +0.025 mm radial |
| FDM print | +0.20 mm | +0.10 mm radial | +0.05 mm radial |
| Custom | user selected | user selected | user selected |

Printer, resin, filament, machining process, material, and the mating commercial part can all require adjustment. Short test rings are recommended before printing a final adapter.

## T2 geometry

- Designation: **M42 × 0.75**
- Nominal diameter: **42.0 mm**
- Nominal pitch: **0.75 mm**
- Thread angle: **60°**
- Default hand: right-hand

## Planned next steps

- M42 × 0.75 external
- Paired male/female generation so printed parts are generated as a matched pair
- Additional optical standards:
  - M48 × 0.75
  - M28.5 × 0.6 (1.25-inch filters)
  - C-mount 1\"-32 UN
  - SM1 / 1.035\"-40
- More material/process compensation presets as they are physically calibrated
- Entry chamfer/runout controls
- Convert the macro into a proper **Optical Threads Workbench**
- Thread library stored as data rather than hard-coded UI entries
- Optional true ISO tolerance classes where the standard provides enough information

## FreeCAD compatibility

Target: FreeCAD 1.x.

The macro uses FreeCAD's `Part` geometry API (`makeHelix` / `makeLongHelix`, sweep/pipe shell, Boolean cut) and creates a `PartDesign::FeaturePython` object.

## License

MIT.
