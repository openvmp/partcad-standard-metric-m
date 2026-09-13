# partcad-standard-metric-m

The standard M* interfaces: circular openings, holes, shafts and screws, in
every metric size.

## How it is declared

On PartCAD 0.8.77 and newer the family is declared **once**, parametrically, and
every name this package has ever published is an alias of one instance of it.
(Older PartCADs read the same package differently; see below.) The lists of sizes, depths and
widths at the top of ``partcad.yaml`` now say only which combinations get a
*name* of their own -- any other combination is reachable by asking for it:

```yaml
parts:
  bracket:
    type: step
    parameters:
      thickness: 3.0
    implements:
      # The published name, unchanged.
      "//pub/std/metric/m:m4-thru-3":
        L: [[-15.0, -27.0, 27.0], [1.0, 0.0, 0.0], 90.0]
      # The same interface, asked for by value - and a thickness the part
      # itself was asked for.
      "//pub/std/metric/m:m-thru-depth;size=4,depth=%thickness%":
        R: [[15.0, -27.0, 27.0], [1.0, 0.0, 0.0], 90.0]
```

| Parametric interface | Parameters | A published name of the same thing |
| --- | --- | --- |
| ``m`` | ``size`` | ``m4`` |
| ``m-opening`` | ``size`` | ``m4-opening`` |
| ``m-hole`` | ``size``, ``depth`` | ``m4-hole-3`` |
| ``m-thru`` | ``size`` | ``m4-thru`` |
| ``m-thru-depth`` | ``size``, ``depth`` | ``m4-thru-3`` |
| ``m-thru-slotted`` | ``size``, ``width`` | -- |
| ``m-thru-depth-slotted`` | ``size``, ``depth``, ``width`` | ``m4-thru-3-slotted-30`` |
| ``m-threaded-thru`` | ``size`` | ``m4-threaded-thru`` |
| ``m-threaded-thru-depth`` | ``size``, ``depth`` | ``m4-threaded-thru-3`` |
| ``m-threaded-hole`` | ``size``, ``depth`` | ``m4-threaded-hole-3`` |
| ``m-shaft`` | ``size`` | ``m4-shaft`` |
| ``m-shaft-length`` | ``size``, ``length`` | ``m4-shaft-10`` |
| ``m-screw`` | ``size`` | ``m4-screw`` |
| ``m-screw-length`` | ``size``, ``length`` | ``m4-screw-12`` |

The two sketches that draw the port boundaries, ``m`` and ``m-slotted``, are
parametric in the same way, and the per-size sketch names (``m4``,
``m4-slotted-30``) are aliases of them. ``m-slotted`` now draws an actual slot --
a rectangle with semicircular ends -- where it used to draw a plain circle and
carry a TODO about it; PartCAD's "basic" sketches have a ``slot`` outline.

**Nothing published has been withdrawn.** An alias *is* the interface it names:
the same ports under the same names, a drop-in for it, mating with whatever it
mates with. A part that says ``implements: m4-thru-3`` and an ASSY that connects
its ``3mm-thru-opening-m4`` port go on working exactly as they did -- all 11,150
names, their descriptions, their ports, their port coordinates and their freedom
of movement are unchanged.

### Two PartCADs, one package

Parametric interfaces and ``alias:`` arrived in PartCAD 0.8.77, and raising this
package's ``partcad:`` requirement to say so would have taken it away from
everyone who has not updated. So ``partcad.yaml`` carries **both** forms and the
template picks:

* **PartCAD 0.8.77 and newer** get the family above -- thirteen parametric
  interfaces, every published name an alias, and any size, depth, width or
  length reachable whether or not a list names it.
* **Anything older** gets exactly what this package has always published, down
  to the last port coordinate, generated the way it always was.

Neither branch is a lesser version of the other for the names they share: on
both, ``m4-thru-3`` is the same interface with the same port in the same place.

### One break, on the newer branch only

A slotted opening is a **through hole** now: ``m4-thru-3-slotted-30`` inherits
``m4-thru-3`` rather than standing on its own, so it mates with a screw the way
every other opening does -- which it never did before -- and it carries the
freedom of movement that slotting a hole is *for*: the bolt may sit anywhere
along what is left of the slot.

The cost is its port's name. It used to be the bare ``m4``; it is now
``slotted-30-3mm-thru-opening-m4``, named after the chain it inherits like every
other port here. **An ASSY that connects that port by name has to be updated.**
Nothing else moved: of the 11,150 published interfaces, the 8,000 slotted ones
changed in exactly that way and the other 3,150 not at all.

Older clients see none of this -- their slotted openings are exactly as they
were.

### A screw may now be driven in, on both branches

This package has always declared how far a screw may travel along its own axis
(``moveZ: {max: length - 2}`` on every ``mN-screw-L``), and PartCAD has always
discarded it, because an inherited declaration used to overwrite the interface's
own. PartCAD 0.8.77 reads it, so 425 of these interfaces gain the movement they
were written to have -- on the older branch too, since it is the *reader* that
changed rather than the declaration.

``length - 2`` is negative for the 1mm and 1.5mm screws the lists also name.
The parametric branch clamps it at zero; the older branch cannot be touched
without ceasing to be what was published, so PartCAD reports those fifty and
reads them as no movement, which is what they already silently were.

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

## Sizes that have no name of their own

Anything the lists in ``partcad.yaml`` do not enumerate is still reachable, by
naming the values instead of the name:

```shell
pc info -i "//pub/std/metric/m:m-hole;size=2.5,depth=7.5"   # a depth no list has
pc info -i "//pub/std/metric/m:m-screw-length;size=7,length=15"
pc info -i "//pub/std/metric/m:m-thru-depth;size=4,depth=3" # and one that does
```
