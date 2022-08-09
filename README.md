# Debian Packaging for Cling

This is Debian packaging repository for the [Cling](https://root.cern/cling/)
interactive C++ interpreter. Cling requires patched versions of [LLVM](https://llvm.org/) and
[Clang](https://clang.llvm.org/) maintained by the [ROOT
Project](https://root.cern/) here:

  * LLVM:  http://root.cern/git/llvm.git/
  * Clang: http://root.cern/git/clang.git/

Debian packaging for them can be found here: https://salsa.debian.org/dimitry-ishenko/llvm-clang-9
<br>or here: https://github.com/dimitry-ishenko-cpp/llvm-clang-9.

Precompiled Debian package for Cling along with the above LLVM/Clang libraries
can be installed from
[ppa:ppa-verse/xeus-cling](https://launchpad.net/~ppa-verse/+archive/ubuntu/xeus-cling).
That PPA also contains Debian package for
[xeus-cling](https://github.com/jupyter-xeus/xeus-cling) (a C++ Jupyter kernel
built on top of Cling).

Or you can build it yourself:

```bash
p=cling b=debian # or another branch

# clone this repo
git clone -b ${b} https://salsa.debian.org/dimitry-ishenko/${p}-packaging.git ${p}
r=$(cd ${p} && git log -n1 --oneline --no-decorate `git describe --tags --abbrev=0` | cut -d/ -f2)
v=${r%-*}
v=${v#*:}

# download and unpack upstream tarball
f=${p}_${v}.orig.tar.gz
wget https://salsa.debian.org/dimitry-ishenko/${p}/-/archive/v${v}/${p}-v${v}.tar.gz -O ${f}

tar -C ${p} --strip-components=1 -xzf ${f}

# create source package
dpkg-source -b ${p}

# build .deb package locally...
sudo pbuilder build ${p}_${r}.dsc

# ...or generate .changes file and upload it to a PPA
# NB: change -sa to -sd when upgrading
(cd ${p} && dpkg-genchanges -S -sa -O../${p}_${r}_amd64.changes)

debsign ${p}_${r}_amd64.changes
dput ppa:... ${p}_${r}_amd64.changes

# share and enjoy
```
