# rcn-ee-repo
The project in this git repository creates two debian packages that we use in
order to download other packages from Robert C Nelson's debian repository for
Beaglebone Black.

The two packages are:
* rcn-ee-archive-keyring
* apt.rcn-ee

The two packages are created by the shell script `create_debian_packages`.
See comment in shell script for a description of the invocation, and the
`Jenkinsfile` for the actual invocation.

Both packages are created for target systems bionic, buster, and focal, but
they do not differ between the target systems: Name, contents, and all
checksums are the same for the three different targets.

For updates:
When updating documentation, no version number increase is required (no
documentation from this repository is added to the debian packages).  Jenkins
will recreate the packages but not upload them to the apt repository because
the same version number is already in STORAGE.

When upstream creates a new version of the keyring package, update the version
number in file `create_debian_packages`: variable `ORIGVERSION`.

## rcn-ee-archive-keyring
Robert C Nelson already provides a package named rcn-ee-archive-keyring in his
repository.  We download the version 2017.07.18~buster+20170718 with wget,
then perform the following changes:
* Add directory `/usr/share/keyrings`.  Multistrap expects apt keys in this directory.
* Copy all files named `*.gpg` from directory `/etc/apt/trusted.gpg.d/` in this package to the new directory.
* Remove any postinst script from the package.
* Append string ".mahalia" to the version number, so that our package appears newer than the original package and is not replaced by the original package later.
* We do **not** change the target architecture, it stays "all".

## apt.rcn-ee
Installs a file `/etc/apt/sources.list.d/rcn-ee.com.list` which points to
Robert C Nelson's apt repository
