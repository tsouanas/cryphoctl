# cryphoctl

Wrapper around bioctl(8) to control OpenBSD's crypto partitions.

## About

This is essentially a wrapper for `bioctl(8)` and `[u]mount(8)`
for the typical use case, where by "typical" I mean "my typical".
First you need to create a `cryphotab` (example provided),
to give a name (cryphoid) to each partition that has a
`softraid(4)` CRYPTO partition.
Then you use `cryphoctl` to list all known cryphoids,
as well as to lock and unlock each of them.
Once you unlock a partition, a (virtual) disk is created
(see `softraid(4)` and `bioctl(8)` for more information).
If you want to mount some partitions of the corresponding
virtual disk, you must annotate your `fstab(5)` file
with special comments (see below).

## Usage

To see a list of all known names (cryphoids) of CRYPTO partitions:
```
cryphoctl list
```

To unlock:
```
cryphoctl unlock NAME
```

To lock:
```
cryphoctl lock NAME
```

## cryphotab

The format of `cryphotab` is simple:

```
duidofdisk.X cryphoid [boot]
```
Lines beginning with `#` are ignored.
Check the example file.

## fstab annotations

To have certain mountpoints automounted as soon as you
unlock an encrypted partition, you should annotate their
fstab entries with a `C:cryphoid` comment.
See the example files provided.

## Unlock partitions during boot

Included are some files meant to help with `cryphoctl` commands
to be run during boot, via `rc(8)`:

* `/etc/rc.crypho`: main functionality lib
* `/etc/rc.crypho.up`: unlocks _boot_-marked partitions of `cryphotab`.
Add something like the following line to your `rc.local(8)` to call it:
```
[ -f /etc/rc.crypho.up ] && . /etc/rc.crypho.up
```
* Note that a corresponding `rc.crypho.down` is not really needed
because `shutdown` is gentle enough, so you don't need to place
something similar to `rc.shutdown(8)`.  In case you need one,
it should be straightforward to create it.

