# Description of VMD 1.9.3 compilation

```
# install dependencies
sudo apt install tcl tcl-dev libtcl8.6 tk tk-dev libfltk1.3 libfltk1.3-dev netcdf-bin libnetcdf-dev

# download vmd-1.9.3.src.tar.gz to 'vmd' directory
# tachyon-0.99b6.tar.gz from http://jedi.ks.uiuc.edu/~johns/raytracer/files/ to 'vmd' directory
# actc actc-1.1.tar.gz from http://plunk.org/~grantham/public/actc/ to 'vmd' directory
# clone repository with patches and installation instructions to 'vmd' directory.
cd vmd
git init
git remote add origin https://github.com/lumik/vmd-compilation.git
git fetch
git reset --hard origin/master

# exctract packages
tar -xzvf vmd-1.9.3.src.tar.gz
tar -xzvf tachyon-0.99b6.tar.gz
tar -xzvf actc-1.1.tar.gz

# compile plugins
cd plugins
export PLUGINDIR=/usr/local/lib/vmd/plugins
sudo -E mkdir -p $PLUGINDIR`
# edit Make-arch file to include -ltcl8.6 in selected architecture
make TCLINC=-I/usr/include/tcl TCLLIB=-L/usr/lib LINUX
sudo -E make distrib

# compile tachyon
cd ../tachyon/unix
make linux-thr-ogl

# compile ACTC
cd ../../actc-1.1
# edit Makefile
# LIBDEST =../vmd-1.9.3/lib/actc/lib_LINUX/
# INCDEST =../vmd-1.9.3/lib/actc/include/
# change cp tc.h $(LIBDEST) to cp tc.h $(INCDEST)
make
make install

# compile vmd
cd ../../vmd-1.9.3

# copy tachyon to its place in vmd
mkdir lib/tachyon/include
cp ../tachyon/src/*.h lib/tachyon/include
mkdir lib/tachyon/lib_LINUX
cp ../tachyon/compile/linux-thr-ogl/libtachyon.a lib/tachyon/lib_LINUX
cp ../tachyon/compile/linux-thr-ogl/tachyon lib/tachyon/tachyon_LINUX

# download appropriate version of vmd and copy surf and stride to its
# position in lib directory

# apply patches in the order from ../patches/series file
patch configure < ../patches/set-paths.patch
patch configure < ../patches/fix-tk-tcl-version.patch
patch configure < ../patches/fix-configure.patch
patch configure < ../patches/fix-python-version.patch
patch configure < ../patches/use-bash-script.patch
patch bin/vmd.sh < ../patches/stride.patch

# configure
cp ../cofigure.options .
./configure

# compile
cd src
make veryclean
make
```
