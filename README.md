# Phenome kernel

A geometry kernel, open source under MIT, and the thin layer that bridges it to Rhino. Both are published as
NuGet packages, and a package is how they reach everything else — every consumer, ours included, restores them
rather than referencing a path into this checkout.

| | |
|---|---|
| [`src/Phenome.Geometry`](src/Phenome.Geometry) | The kernel. Types as plain data, every operation a static function; meshes, polylines, transforms, triangulation. Its [README](src/Phenome.Geometry/README.md) is the API contract — read *Why the API looks unusual* before writing against it. |
| [`src/Phenome.RhinoInterop`](src/Phenome.RhinoInterop) | RhinoCommon geometry to kernel geometry and back. Depends on the kernel and RhinoCommon, and on no Grasshopper at all. |

Both carry two target frameworks. `net10.0` is the real one; `net7.0` exists because Rhino 8 hosts .NET 7 and a
net7.0 assembly cannot reference a net10.0 one, so a plugin that wants the kernel needs it built there too.

```
dotnet build src/Phenome.Geometry
dotnet test tests/Phenome.Geometry.Tests            # 571 tests
dotnet pack src/Phenome.Geometry -c Release -o dist/nuget
dotnet pack src/Phenome.RhinoInterop -c Release -o dist/nuget
```

Inside this repository `Phenome.RhinoInterop` references the kernel by **project**, not by package: packages are
for crossing between repositories, and `pack` turns a project reference into a declared dependency by itself.

Nothing is on a feed yet. `dist/nuget` is a folder that other checkouts restore from — a stopgap, honest about
being one, standing in for nuget.org.

## What is not here

**Phenome Link**, the Grasshopper plugin and VS Code extension that put an agent and a human on the same canvas,
briefly shared a repository with this code and does not any more: nothing references anything in either
direction, and a `.gha` with a `.vsix` is not the artefact a `.nupkg` is. It is at
[theObjectCo/Phenome](https://github.com/theObjectCo/Phenome).

The four libraries above the kernel — the operation catalogue, the node graph, the parts model and the sketch
harness — are private, and reach this code the same way anybody else does.
