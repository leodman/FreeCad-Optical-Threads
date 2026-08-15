# FreeCAD Optical Threads

A FreeCAD project for generating **editable, printable optical/camera/telescope threads** using native FreeCAD Part Design features wherever possible.

## Current main version: v0.3.0

The current generator provides:

- **Verified preset:** T2 / M42 × 0.75 internal thread
- **Custom metric** diameter/pitch entry using the same engine
- Native `PartDesign::Body`
- Native additive/subtractive primitives for the sleeve
- Native conical thread lead-in
- Sketcher-based ideal 60° triangular thread profile
- Native `PartDesign::SubtractiveHelix`
- Parametric thread/sleeve length and wall thickness
- Practical fit presets
- Independent diametral clearance, root relief, and crest truncation
- Material/process shrinkage compensation
- PETG 0.7% nominal compensation, editable by the user

The generated result is not an opaque custom BRep. It remains a normal FreeCAD feature tree that can be inspected and edited directly.

## Core design rule

**Prefer native FreeCAD features over custom geometry construction.**

The project originally experimented with raw Part-workbench helix sweeps and lofted cutters. Those approaches could display and even print, but they produced fragile or invalid BRep topology in later Boolean operations.

The successful architecture is the same one used by normal FreeCAD modeling:

1. Create a `PartDesign::Body`.
2. Create the base sleeve with native primitives.
3. Create the thread profile as a Sketcher triangle.
4. Create the thread with `PartDesign::SubtractiveHelix` for internal threads.
5. Keep the entire feature tree editable.

Future development should follow this rule unless a required operation genuinely cannot be represented with native FreeCAD features.

## Parametric architecture

v0.3.0 separates three layers:

### 1. Thread standard

The standard describes the nominal thread only, for example:

- Diameter: 42.0 mm
- Pitch: 0.75 mm
- Profile angle: 60°

The current verified table contains T2 / M42 × 0.75. A `Custom metric` option allows another diameter and pitch to use the same native generator before that combination is promoted to a verified preset.

### 2. Fit correction

Fit is independent of the standard. The female-thread controls are:

| Fit | Diametral clearance | Root relief | Crest truncation |
|---|---:|---:|---:|
| Basic / nominal | 0.00 mm | 0.00 mm | 0.00 mm |
| Close | +0.05 mm | 0.00 mm | 0.00 mm |
| Normal | +0.10 mm | 0.00 mm | 0.00 mm |
| Loose | +0.20 mm | +0.05 mm radial | +0.025 mm radial |
| FDM print | +0.20 mm | +0.10 mm radial | +0.05 mm radial |
| Custom | user selected | user selected | user selected |

These are practical fit presets, not certified ISO tolerance classes.

### 3. Material/process compensation

Material shrinkage is also independent of the standard and fit.

Current process presets:

| Material / process | Nominal shrinkage |
|---|---:|
| No compensation | 0.0% |
| 3D PETG | 0.7% |

The compensation is:

`scale = 1 / (1 - shrinkage)`

For PETG at 0.7%:

`scale = 1 / 0.993 = 1.007049345...`

The scale is applied to all generated linear CAD dimensions, including pitch. Thus a nominal 0.750 mm pitch becomes about 0.75529 mm in compensated CAD, targeting approximately 0.750 mm after 0.7% linear shrinkage.

This is an empirical process compensation, not a universal PETG constant.

## Female-thread geometry

For the current metric 60° implementation, the basic internal minor-diameter relation used is:

`D1 = D - 1.082531754 × P`

Fit corrections are applied after the nominal thread definition and before material/process scaling is expressed in the generated CAD geometry.

The thread profile itself remains a simple ideal pointed 60° triangle. This intentionally follows the simple native modeling strategy that proved reliable in FreeCAD.

## Thread lead-in

v0.3.0 adds a native conical lead-in at the sleeve entrance using `PartDesign::SubtractiveCone` **before** the thread helix is created.

This avoids trying to identify or chamfer a complicated threaded edge. The helix is then cut through the already prepared entrance.

## Installation as a macro

1. Open FreeCAD.
2. Open **Macro → Macros…**.
3. Use **User macros location** to find your macro folder.
4. Copy `OpticalThreads.FCMacro` into that folder.
5. Run `OpticalThreads`.

The macro opens a parameter dialog and creates the native editable feature tree.

## FreeCAD compatibility

Target: FreeCAD 1.x.

The macro uses native FreeCAD objects including:

- `PartDesign::Body`
- `PartDesign::AdditiveCylinder`
- `PartDesign::SubtractiveCylinder`
- `PartDesign::SubtractiveCone`
- `Sketcher::SketchObject`
- `PartDesign::SubtractiveHelix`

## Planned next steps

- Matching M42 × 0.75 external thread using native `PartDesign::AdditiveHelix`
- Paired male/female generation from the same standard/fit/material definition
- Additional verified optical-thread presets
- More calibrated material/process presets
- Better runout/relief controls
- Conversion into a proper Optical Threads Workbench

## License

MIT.
