# partcad-standard-metric-m

## Interfaces

* ``m\*``

  An abstract interface which defines visualization and the parameter for rotation.
  E.g. ``m2``, ``m2.5``, ``m8``, ``m32``.

* ``m*-opening``

  An abstract interface for openings: thru or not, threaded or not (as opposed to screws, shafts etc)

* ``m*-hole-*``

  A hole of the corresponding depth (not a thru). No thread.
  E.g. ``m2-hole-8`` for an 8mm deep hole in a surface of more than 8mm thickness.

* ``m*-thru``

  A thru opening of an unspecified depth. No thread.

* ``m*-thru-*``

  A thru opening of the specified depth. No thread.
  E.g. ``m2-thru-8`` for a thru opening in an 8mm thick surface.

* ``m*-thru-*-slotted-*``

  A slotted thru opening of the specified depth and width.
  E.g. ``m2-thru-8-slotted-30`` for a 30mm wide slot in an 8mm thick surface.

* ``m*-threaded-hole-*``

  A threaded hole of the corresponding depth (not a thru).
  E.g. ``m2-threaded-hole-8`` for an 8mm deep threaded hole in a surface of more than 8mm thickness.

* ``m*-threaded-thru-*``

  A thru opening of the specified depth.
  E.g. ``m2-threaded-thru-8`` for a threaded thru opening in an 8mm thick surface.

* ``m*-shaft``

  An abstract class for shafts. No thread.

* ``m*-shaft-*``

  A shaft of the specified length. No thread.

* ``m*-screw``

  An abstract class for screws.

* ``m*-screw-*``

  A screw of the specified length. Defines the parameter to specify how deep it goes in.
  E.g. ``m4-screw-12`` for a 12mm long M4 screw.

## Parametric interfaces

The interfaces above are written out one per combination: a size, a depth or a
width that is not in the lists at the top of ``partcad.yaml`` does not exist.
The same family is also declared *parametrically*, with the values named in the
reference instead of in the name:

| Interface | Parameters | Example |
| --- | --- | --- |
| ``m`` | ``size`` | ``m;size=4`` |
| ``m-opening`` | ``size`` | ``m-opening;size=4`` |
| ``m-hole`` | ``size``, ``depth`` | ``m-hole;size=2.5,depth=7.5`` |
| ``m-thru`` | ``size``, ``depth`` | ``m-thru;size=4,depth=3`` |
| ``m-thru-slotted`` | ``size``, ``depth``, ``width`` | ``m-thru-slotted;size=4,depth=3,width=30`` |
| ``m-threaded-hole`` | ``size``, ``depth`` | ``m-threaded-hole;size=5,depth=10`` |
| ``m-threaded-thru`` | ``size``, ``depth`` | ``m-threaded-thru;size=5,depth=10`` |
| ``m-shaft`` | ``size``, ``length`` | ``m-shaft;size=8,length=45`` |
| ``m-screw`` | ``size``, ``length`` | ``m-screw;size=4,length=12`` |

```yaml
parts:
  bracket:
    type: step
    parameters:
      thickness: 3.0
    implements:
      "//pub/std/metric/m:m-thru;size=4,depth=%thickness%":
        L: [[-15.0, -27.0, 27.0], [1.0, 0.0, 0.0], 90.0]
        R: [[15.0, -27.0, 27.0], [1.0, 0.0, 0.0], 90.0]
```

Any size, depth, width or length works, whether or not it appears in the lists
-- and a part can name one of its own parameters, so a bracket asked for by
thickness implements the hole of that thickness.

Two things to know before using them:

* They need **PartCAD 0.8.77 or later**. The package does not declare that as
  its requirement, because doing so would stop an older PartCAD from loading
  the enumerated interfaces, which it handles perfectly well.
* The two families are **separate sets of interfaces**, not two spellings of
  one: ``m4-thru-3`` and ``m-thru;size=4,depth=3`` do not mate with each other.
  Use one or the other, and use the same one on both parts of a connection. The
  enumerated names are not deprecated and are not going away.
