# partcad-standard-metric-m

The standard M* interfaces: circular openings, holes, shafts and screws, in
every metric size.

## How it is declared

On PartCAD 0.8.78 and newer the family is declared **once**, parametrically, and
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
| ``m-tapped-opening`` | ``size`` | ``m4-tapped-opening`` |
| ``m-pilot-opening`` | ``size`` | ``m4-pilot-opening`` |
| ``m-hole`` | ``size``, ``depth`` | ``m4-hole-3`` |
| ``m-thru`` | ``size`` | ``m4-thru`` |
| ``m-thru-depth`` | ``size``, ``depth`` | ``m4-thru-3`` |
| ``m-thru-slotted`` | ``size``, ``width`` | -- |
| ``m-thru-depth-slotted`` | ``size``, ``depth``, ``width`` | ``m4-thru-3-slotted-30`` |
| ``m-threaded-thru`` | ``size`` | ``m4-threaded-thru`` |
| ``m-threaded-thru-depth`` | ``size``, ``depth`` | ``m4-threaded-thru-3`` |
| ``m-threaded-hole`` | ``size``, ``depth`` | ``m4-threaded-hole-3`` |
| ``m-tapped-hole`` | ``size``, ``depth`` | ``m4-tapped-hole-3`` |
| ``m-pilot-thru`` | ``size`` | ``m4-pilot-thru`` |
| ``m-pilot-thru-depth`` | ``size``, ``depth`` | ``m4-pilot-thru-3`` |
| ``m-pilot-hole`` | ``size``, ``depth`` | ``m4-pilot-hole-3`` |
| ``m-shaft`` | ``size`` | ``m4-shaft`` |
| ``m-shaft-length`` | ``size``, ``length`` | ``m4-shaft-10`` |
| ``m-threaded-shaft`` | ``size`` | ``m4-threaded-shaft`` |
| ``m-threaded-shaft-length`` | ``size``, ``length`` | ``m4-threaded-shaft-10`` |
| ``m-screw`` | ``size`` | ``m4-screw`` |
| ``m-screw-length`` | ``size``, ``length`` | ``m4-screw-12`` |
| ``m-bolt`` | ``size`` | ``m4-bolt`` |
| ``m-bolt-length`` | ``size``, ``length`` | ``m4-bolt-12`` |

The sketches that draw the port boundaries are parametric in the same way, and
the per-size sketch names (``m4``, ``m4-clearance``, ``m4-pilot``,
``m4-slotted-30``) are aliases of them. ``m-slotted-*`` draws an actual slot --
a rectangle with semicircular ends -- where it used to draw a plain circle and
carry a TODO about it; PartCAD's "basic" sketches have a ``slot`` outline.

**Nothing published has been withdrawn.** An alias *is* the interface it names:
the same ports under the same names, a drop-in for it, mating with whatever it
mates with. A part that says ``implements: m4-thru-3`` and an ASSY that connects
its ``3mm-thru-opening-m4`` port go on working exactly as they did -- every one
of the 11,150 names is still there, with its ports under the same names at the
same coordinates. What a clearance hole *measures* did change; that is the next
section.

### Two PartCADs, one package

Parametric interfaces and ``alias:`` arrived in PartCAD 0.8.78, and raising this
package's ``partcad:`` requirement to say so would have taken it away from
everyone who has not updated. So ``partcad.yaml`` carries **both** forms and the
template picks:

* **PartCAD 0.8.78 and newer** get the family above -- fourteen parametric
  interfaces, every published name an alias, and any size, depth, width or
  length reachable whether or not a list names it.
* **Anything older** gets exactly what this package has always published, down
  to the last port coordinate, generated the way it always was.

Neither branch is a lesser version of the other for the names they share: on
both, ``m4-thru-3`` is the same interface with the same port in the same place.

### What changed, on the newer branch only

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

Two other things moved on this branch, both of them consequences of telling a
clearance hole from a tapped one (see **Three holes, not one** below):

* **A clearance hole measures what ISO 273 says it does.** ``m4-thru`` draws a
  4.5mm circle rather than a 4mm one. Nothing else about the interface changed
  -- same name, same port, same coordinates, and everything it mated before it
  mates now -- but a projection or a viewer shows the corrected size, and it is
  a *fix*: the old circle was the one a screw could not fit through.
* **``m*-threaded-*`` descends from ``m*-tapped-opening``** rather than from
  ``m*-opening``, because a tapped hole is not a clearance hole. Port names are
  unchanged (both openings are inherited under the same instance name), and a
  screw still mates them -- ``m*-screw`` names the tapped opening as a mate, so
  a threaded hole is now a screw's mate directly rather than by inheriting one
  from a clearance hole it is not. What no longer
  holds is that ``m4-threaded-thru`` is a drop-in for ``m4-opening``: an
  interface of your own declaring ``mates: m4-opening`` used to reach it and
  does not any more. Mate ``m4-tapped-opening`` as well.
* **A thread advances by its pitch.** ``m*-screw`` and ``m*-tapped-opening``
  declare a ``threadStep``, which they never did -- see **How far a turn takes
  it** below. A connection that used to be told the thread advances nothing per
  turn is now told the ISO 261 coarse pitch.
* **``m*-screw`` means the kind that cuts its own thread.** Nothing it mated
  before stopped mating -- its published mate, the clearance hole, is still
  there and it gained the tapping drill -- but ``m*-bolt`` is now the name for
  the fastener that matches an existing thread, and it is the one to use for a
  machine screw going into a tapped hole. See **What mates what** below.

Older clients see none of this -- their openings are exactly as they were, and
their threads still advance by nothing. Carrying the pitch over to that branch
is a change of its own, and a safe one; it is left out here because that branch
is verified byte-for-byte against what was published.

### A screw may now be driven in, on both branches

This package has always declared how far a screw may travel along its own axis
(``moveZ: {max: length - 2}`` on every ``mN-screw-L``), and PartCAD has always
discarded it, because an inherited declaration used to overwrite the interface's
own. PartCAD 0.8.78 reads it, so 425 of these interfaces gain the movement they
were written to have -- on the older branch too, since it is the *reader* that
changed rather than the declaration.

``length - 2`` is negative for the 1mm and 1.5mm screws the lists also name.
The parametric branch clamps it at zero; the older branch cannot be touched
without ceasing to be what was published, so PartCAD reports those fifty and
reads them as no movement, which is what they already silently were.

## Three holes, not one

An M4 screw involves three different holes, and only one of them is 4mm across:

| The hole | For an M4 | Where the number comes from |
| --- | --- | --- |
| **Clearance** -- the screw passes through it | 4.5mm | ISO 273, medium series (H13) |
| **Tapped** -- the screw threads into it | 4mm | the thread's major diameter (ISO 261, ISO 724) |
| **Pilot** -- drilled first, then tapped | 3.3mm | ISO 2306, for the coarse pitch of ISO 261 |

Every one of them used to be drawn 4mm across, which is right for exactly one of
the three: **an M4 screw does not pass through a 4mm hole.** On PartCAD 0.8.78
and newer each kind is its own opening and measures what it should:

* ``m*-opening``, ``m*-hole-*``, ``m*-thru``, ``m*-thru-*`` and the slotted ones
  are **clearance** holes. This is the one thing about the published names that
  changed, and it is why: they are the holes a screw goes through.
* ``m*-tapped-opening``, ``m*-tapped-hole-*``, ``m*-threaded-thru``,
  ``m*-threaded-thru-*`` and ``m*-threaded-hole-*`` are **tapped**, at the
  nominal size -- unchanged, because the nominal size was always the right
  answer for these.
* ``m*-pilot-opening``, ``m*-pilot-thru``, ``m*-pilot-thru-*`` and
  ``m*-pilot-hole-*`` are the **pilot** hole. They are new.

All three inherit ``m`` under the same instance name, so a port keeps the name
it has always had -- ``m4-thru-3`` still carries ``3mm-thru-opening-m4`` -- and
a screw mates a clearance hole and a tapped one, a pilot hole mates a pilot hole
and the clearance hole something is drilled through, and a shaft mates a
clearance hole only.

ISO 273's other two series are there to be drawn, as the sketches
``m-clearance-fine`` (H12) and ``m-clearance-coarse`` (H14). The *interfaces*
use the medium one, because a fit class is a tolerance rather than a different
feature: making it a parameter would split ``m4-thru`` into three interfaces
that do not mate each other, since PartCAD registers a mate against one
parametrized instance.

### Where the numbers come from

Two tables at the top of ``partcad.yaml``, one per standard, each of them the
standard's whole table: ISO 273's clearance holes in three series, M1 to M150,
and ISO 2306's tapping drills for the coarse pitch of ISO 261, M1 to M68 --
which is where ISO 261's coarse series ends. Each column is read as one PartCAD
expression over ``size``, so a size a standard does not tabulate (an M9, an M11)
rounds **up** to the next row it does: a clearance hole is never returned
smaller than the standard's. Only the *names* stop at M64; ``m-thru;size=80``
gets ISO 273's own 86mm.

One row in each table is not the standard's, and it is the same size both times.
M32 is an ISO 261 third-choice thread this package has published all along, and
neither ISO 273 nor ISO 2306 has a row for it; leaving it to round up to M33's
would quietly loosen a size somebody is already using. Its clearances are its
neighbours' offsets -- M30 and M33 are both d+1 / d+3 / d+5 -- and its drill is
d - P for the M32x3.5 of ISO 261. Nothing else is extrapolated, except past the
end of a table, where the last band's offset simply carries on.

A hole that is none of those -- a slot in a plate that is not a fastener hole at
all, a hole reamed to something else, a thread that is not the coarse one -- is
the ``diameter`` parameter of ``m-clearance-*``, and the ``drill`` or ``pitch``
parameter of ``m-pilot``:

```shell
pc info -s "//pub/std/metric/m:m-pilot;size=12,pitch=1.5"    # an M12x1.5: 10.5
pc info -s "//pub/std/metric/m:m-pilot;size=12,drill=10.5"   # the same, by drill
pc info -s "//pub/std/metric/m:m-clearance-medium;size=12,diameter=13"
```

``pitch`` is the rule of thumb the tabulated drills are rounded from -- the hole
is ``d - P``, which leaves about three quarters of the thread depth. Checking
that against ISO 68-1's basic profile (``H = P*sqrt(3)/2``, so a tapped hole's
basic minor diameter is ``D1 = d - 1.082532*P``) is what says the table is
right: every drill it names sits between ``D1`` and the nominal size, at 74% to
79% thread engagement.

Nothing here is toleranced. ISO 965 is where a tapped hole's limits live, and a
port boundary is one circle rather than a band, so every number above is the
basic size.

### How far a turn takes it

A PartCAD interface carries a ``threadStep`` -- how far a connection made
through it advances per full turn -- which is how an assembly knows that driving
an M4 screw 6mm home is nine turns rather than a number nobody wrote down. This
package declared none, so every M-threaded connection it was used for fell back
to zero.

The two interfaces that actually have a thread now declare it, from ISO 261's
coarse pitch: ``m*-screw`` and ``m*-tapped-opening``, which is where every
``m*-threaded-*`` inherits it. A clearance hole, a shaft and a pilot hole do not
-- none of them has a thread -- and PartCAD takes the pitch from the end that
does, so bolting through a clearance hole still gets the screw's. Where both ends
declare one and they disagree, it says so, which is a thing it could not do while
neither end declared anything.

The coarse pitch is the only answer this package can give: a fine thread is a
different thread, and every name here is ``m<size>-something`` with no room to
say which. ``m-pilot`` takes a ``pitch`` for the drill, but no interface does --
a parameter with a default spells itself into an instance name, so
``m-screw;size=4`` and ``m-screw;size=4,pitch=0.7`` would be two interfaces that
do not mate each other.

This is on the newer branch only, like everything else in this section.

### What mates what

Five interfaces declare ``mates:`` and everything else inherits the ability
through its compatibility closure, so what can be connected to what is decided
entirely by which of six **kinds** each end is:

|  | clearance | tapped | pilot | screw | bolt | shaft | threaded shaft |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **clearance** | X | X | X | X | X | X | X |
| **tapped** | X | · | · | · | X | · | X |
| **pilot** | X | · | X | X | · | · | · |
| **screw** | X | · | X | · | · | · | · |
| **bolt** | X | X | · | · | · | · | · |
| **shaft** | X | · | · | · | · | · | · |
| **threaded shaft** | X | X | · | · | · | · | · |

* **clearance** -- ``m*-opening``, ``m*-thru``, ``m*-thru-*``, ``m*-hole-*``,
  ``m*-thru-*-slotted-*``
* **tapped** -- ``m*-tapped-opening``, ``m*-tapped-hole-*``,
  ``m*-threaded-thru``, ``m*-threaded-thru-*``, ``m*-threaded-hole-*``
* **pilot** -- ``m*-pilot-opening``, ``m*-pilot-thru``, ``m*-pilot-thru-*``,
  ``m*-pilot-hole-*``
* **screw** -- ``m*-screw``, ``m*-screw-*``
* **bolt** -- ``m*-bolt``, ``m*-bolt-*``
* **shaft** -- ``m*-shaft``, ``m*-shaft-*``
* **threaded shaft** -- ``m*-threaded-shaft``, ``m*-threaded-shaft-*``

``m*`` itself is abstract and mates nothing. Everything is size-scoped: an M4
screw does not mate an M5 opening.

Reading it: a clearance hole is the through-feature of a joint, so it takes
anything.

**A screw cuts its thread and a bolt finds one**, which is the whole of the
difference between them and decides where each may go. A screw is driven into a
tapping drill and makes the thread it then holds; a bolt goes into a thread that
already exists and would strip a tapping drill. So screw-pilot and bolt-tapped
are the pairs, and screw-tapped and bolt-pilot are not. The screw's mating with
the pilot hole carries ``selfScrew: true``, which is what tells PartCAD the two
ends need not agree about the thread -- and it sits on the *mating* rather than
on the screw, because the same screw cuts nothing on its way through a clearance
hole.

**Two tapped holes do not mate**, which they used to. Threads cut separately
never line up; something has to run through both, and that something is a bolt,
a stud, or the clearance hole of the part being held down.

A threaded shaft -- a stud, a length of rod -- is a shaft that finds a thread, so
it goes where a bolt goes. A plain shaft only passes through a clearance hole.
Two male features never meet, which is why screw-bolt, shaft-shaft, stud-stud
and every pairing of them is blank; and a pilot hole meets a screw, another
pilot hole, and the clearance hole something is drilled through.

That clearance-hole entry for a shaft is an approximation worth knowing as one.
ISO 273 sizes a hole for a *fastener*; the fit between a shaft and its bore is
ISO 286, and this package has no interface for one.

### Eleven sizes that were missing

ISO 273 tabulates 35 nominal sizes between M1 and M64 and this package named 24
of them, so **M1.8, M4.5, M7, M18, M22, M27, M33, M39, M45, M52 and M60** have
the whole family of names now, the way every other size does. M32 stays where it
was: it is a real thread, just not one ISO 273 has a row for.

This is on the newer branch only. An older PartCAD gets the 25 sizes that were
published, unchanged.

## Interfaces

* ``m\*``

  An abstract interface which defines visualization and the parameter for rotation.
  E.g. ``m2``, ``m2.5``, ``m8``, ``m32``.

* ``m*-opening``

  An opening a screw passes through: the ISO 273 clearance hole, thru or not
  (as opposed to screws, shafts etc). 4.5mm for an ``m4-opening``.

* ``m*-tapped-opening``

  An opening a screw threads into, at the thread's major diameter -- which is
  the nominal size, so 4mm for an ``m4-tapped-opening``. What every
  ``m*-threaded-*`` below is.

* ``m*-pilot-opening``

  The hole a tapped one is drilled as before it is tapped: the ISO 2306 tapping
  drill for the coarse pitch, so 3.3mm for an ``m4-pilot-opening``.

* ``m*-hole-*``

  A clearance hole of the corresponding depth (not a thru). No thread.
  E.g. ``m2-hole-8`` for an 8mm deep hole in a surface of more than 8mm thickness.

* ``m*-thru``

  A clearance thru opening of an unspecified depth. No thread.

* ``m*-thru-*``

  A clearance thru opening of the specified depth. No thread.
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

* ``m*-pilot-thru``, ``m*-pilot-thru-*``

  A tapping drill through an unspecified or a specified thickness.
  E.g. ``m2-pilot-thru-8`` for the 1.6mm hole an M2 thread is cut into, through
  an 8mm thick surface.

* ``m*-pilot-hole-*``

  A tapping drill of the corresponding depth (not a thru).
  E.g. ``m4-pilot-hole-8`` for the 3.3mm hole an 8mm deep M4 thread is cut into.

* ``m*-tapped-hole-*``

  A tapped hole of the corresponding depth (not a thru), at the nominal size.
  ``m*-threaded-hole-*`` is the same hole named the way this package has always
  named it.

* ``m*-shaft``

  An abstract class for shafts. No thread. Several parts may sit along one
  (``multiConnect``), which no other interface here allows.

* ``m*-shaft-*``

  A shaft of the specified length. No thread.

* ``m*-threaded-shaft``, ``m*-threaded-shaft-*``

  A stud, a threaded rod, the threaded end of anything: a shaft that can be
  screwed into what it meets rather than only passed through it. Everything a
  shaft is, plus a thread -- so it still carries several parts along it, and it
  additionally mates a tapped opening.
  E.g. ``m4-threaded-shaft-30`` for a 30mm length of M4 rod.

* ``m*-screw``

  An abstract class for screws.

* ``m*-screw-*``

  A screw of the specified length. Defines the parameter to specify how deep it goes in.
  E.g. ``m4-screw-12`` for a 12mm long M4 screw.

* ``m*-bolt``, ``m*-bolt-*``

  A bolt: a fastener that *matches* a thread where a screw *cuts* one. It goes
  into a tapped hole and never into a tapping drill, and both ends of the
  connection are checked against each other, which a screw's thread is exempt
  from. E.g. ``m4-bolt-12`` for a 12mm long M4 bolt.

## Sizes that have no name of their own

Anything the lists in ``partcad.yaml`` do not enumerate is still reachable, by
naming the values instead of the name:

```shell
pc info -i "//pub/std/metric/m:m-hole;size=2.5,depth=7.5"   # a depth no list has
pc info -i "//pub/std/metric/m:m-screw-length;size=7,length=15"
pc info -i "//pub/std/metric/m:m-thru-depth;size=4,depth=3" # and one that does
```
