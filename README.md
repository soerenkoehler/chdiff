ChDiff
======

A Checksum Based Diff and Backup Tool
-------------------------------------

### Overview and History ###

With increasing data volume distributed over a number of hard drives I needed
something to compare folders as well across space as also across time. The
first can be done comfortably with any existing diff tool. I prefer [KDiff](http://kdiff3.sourceforge.net/), for it is cross platform and can do
3-way-compare.

For the comparison over time I hacked together some bash scripts using `find`
and `sha256sum`.

I even had a little java app for checking larger backup copies on external
drives.

From this I developed the requirements for ChDiff:
- Run from the command line.
- Run on my Synology NAS.
- Create file hashs for entire directory trees.
- Verify the results of the above against an directory tree. Detect additions
  on the file system.
- Be compatible to `sha256sum` and friends.
- Create hashs for two directory trees and then produce a diff using hash and
  time stamp information.
- Compare a source directory against a backup folder structure and create
  incremental backups.

### Usage ###

#### create (alias: c) ####

```
usage: chdiff create [-h] [-q] [-qq] [-m {sha256,sha512,md5,size}]
                     DIR [DIR ...]

positional arguments:
  DIR                   a directorie-path to compute checksums for

optional arguments:
  -h, --help            show this help message and exit
  -q, --quiet           dont print progress info
  -qq, --very-quiet     dont print warnings
  -m, --method          the checksum method to use: sha256,sha512,md5,size
```

The `create`-command will create hash files `chdiff.{method}.txt` on top of
every given directory. The hash files itself will be skipped, since they are
changing in the process.

The method can be changed via the `-m` option.
- Beware that this option is case sensitiv.
- The default method is `sha256`.
- The method `size` is provided for quick diffs. It uses the file size
as a simple hash.

#### verify (alias: v) ####

```
usage: chdiff verify [-h] [-q] [-qq] [-m {sha256,sha512,md5,size}]
                     DIR [DIR ...]
```

The `verify`-command will verify the hash files in every given directory and
report new, deleted and modified files. The arguments and options are the same
as for `create`.

#### diff (alias: d) ####

```
usage: chdiff diff [-h] [-t] [-q] [-qq] [-m {sha256,sha512,md5,size}]
                   LEFT RIGHT

positional arguments:
  LEFT                  first directory to compare
  RIGHT                 second directory to compare

optional arguments:
  -t, --timestamps      show also files with equal checksum but different
                        timestamps
```
The `diff`-command takes exactly two direcories. In these it creates hash files
as if the `create`-command had been issued. Then it will use the hashes and
the time stamps of the files for a comparison. It will show additions,
deletions and which files are newer.

If you are interested in files with equal content but different time stamps,
use the `-t` option. All other options are the same as with `create` and
`verify`.

**Warning!** `diff` is comparion across space. It will overwrite any existing
hash file you may have kept for comparison across time.

#### backup (alias: b) ####

Still in beta. Use on your own risk ;)

### Requirements ###

* The regular version of ChDiff requires Python 3.6 or later.
* For Python 3.5 (which is the newest version on my Synology NAS) I'll maintain
  a backport branch.

https://github.com/soerenkoehler/checksum-diff/
