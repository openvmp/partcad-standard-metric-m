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
