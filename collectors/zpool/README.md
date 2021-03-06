# Zpool

Uses the `zpool` CLI to report on the state of ZFS pools. Written
and tested on Solaris, but will likely work on FreeBSD and, if you
must, Linux.

It tries to be flexible with the columns in the `zpool` output, so
new fields being added ought not to break things, though they won't
necessarily be displayed without amending the collector.

## Options

* **`fields`**: a list of metrics you wish to collect. For all
  metrics, use the magic value `__all__`.
* **`count`**: the collector can report the number of `filesystem`s,
  `snaphost`s, and `volume`s in a pool. Provide a list of any or all
  of those if you wish to have them.

## Statistics

Collecting field information on four pools on an 8-core i7 system takes
around 15ms. Collecting the `count` metrics can take far longer. For
instance, on a pool with around 1500 snapshots, the `count.snapshot`
metric takes almost two seconds. This is a limitation of the `zfs`
command.

## Metric Paths

By default it reports all the columns in the default `zpool` output.
Namely:

`zpool.<pool>.alloc` (bytes)
`zpool.<pool>.cap` (float)
`zpool.<pool>.dedup` (float)
`zpool.<pool>.free` (bytes)
`zpool.<pool>.size` (bytes)

on Solaris 11.3, and

`zpool.<pool>.alloc` (bytes)
`zpool.<pool>.cap` (bytes)
`zpool.<pool>.dedup` (float)
`zpool.<pool>.expandsz` (bytes)
`zpool.<pool>.frag` (float)
`zpool.<pool>.free` (bytes)
`zpool.<pool>.size` (float)

on SmartOS 20160901.

You can also get

`zpool.<pool>.count.filesystem`
`zpool.<pool>.count.snapshot`
`zpool.<pool>.count.volume`

You can choose any subset of these via the `fields` configuration
setting, or use standard Diamond whitelist/blacklisting.

Additionally, the health of the pool can be reported as an integer.

`zpool.<pool>.health` (int)

The values for this metric are:

* 0: `ONLINE`
* 1: `DEGRADED`
* 2: `SUSPENDED`
* 3: `UNAVAIL`
* 4: uknown state
