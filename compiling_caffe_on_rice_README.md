COMPILING CAFFE ON RICE
=======================
This tutorial describes how to compile [Caffe](http://caffe.berkeleyvision.org) on ``rice.rcac.purdue.edu``.

Before you even attempt to install Caffe, make sure you set up you home directory as described in [here](./setting_up_rice_environment_README.mk).

Ok. Here we go. Note that we are assuming that you always start at ``$PREFIX/src``. There is no need to make a mess of your home directory.

Install snappy
--------------
This is easy:	
```
cd $PREFIX/src
git clone https://github.com/google/snappy.git
cd snappy
./autogen.sh
./configure --prefix=$PREFIX
make
make install
``` 

Install leveldb
---------------
This requires two steps. Start with this.
```
cd $PREFIX/src
git clone https://github.com/google/leveldb.git
cd leveldb
```
Then, create a file called ``./rice.mk`` containing exactly the folllowing
stuff:
```
SOURCES=db/builder.cc db/c.cc db/dbformat.cc db/db_impl.cc db/db_iter.cc db/dumpfile.cc db/filename.cc db/log_reader.cc db/log_writer.cc db/memtable.cc db/repair.cc db/table_cache.cc db/version_edit.cc db/version_set.cc db/write_batch.cc table/block_builder.cc table/block.cc table/filter_block.cc table/format.cc table/iterator.cc table/merger.cc table/table_builder.cc table/table.cc table/two_level_iterator.cc util/arena.cc util/bloom.cc util/cache.cc util/coding.cc util/comparator.cc util/crc32c.cc util/env.cc util/env_posix.cc util/filter_policy.cc util/hash.cc util/histogram.cc util/logging.cc util/options.cc util/status.cc  port/port_posix.cc
MEMENV_SOURCES=helpers/memenv/memenv.cc
CC=cc
CXX=g++
PLATFORM=OS_LINUX
PLATFORM_LDFLAGS=-pthread -L$(PREFIX)/lib -lsnappy
PLATFORM_LIBS=
PLATFORM_CCFLAGS= -std=c++0x -fno-builtin-memcmp -pthread -DOS_LINUX -DLEVELDB_PLATFORM_POSIX -DSNAPPY -I$(PREFIX)/include -L$($PREFIX)/lib -lsnappy
PLATFORM_CXXFLAGS= -std=c++0x -fno-builtin-memcmp -pthread -DOS_LINUX -DLEVELDB_PLATFORM_POSIX -DSNAPPY -I$(PREFIX)/include -L$(PREFIX)/lib -lsnappy
PLATFORM_SHARED_CFLAGS=-fPIC
PLATFORM_SHARED_EXT=so
PLATFORM_SHARED_LDFLAGS=-shared -Wl,-soname -Wl,
PLATFORM_SHARED_VERSIONED=true
```
Now, edit ``./Makefile`` delete the following lines:
```
$(shell CC="$(CC)" CXX="$(CXX)" TARGET_OS="$(TARGET_OS)" \
    ./build_detect_platform build_config.mk ./)
# this file is generated by the previous line to set build flags and thesources
include build_config.mk
```
and replace them with this line:
```
include rice.mk
```
We are ready to do:
```
make
```

Install protobuf
----------------
This is also easy:
```
cd $PREFIX/src
git clone https://github.com/google/protobuf.git
cd protobuf
./autogen.sh
./configure --prefix=$PREFIX
make
make install
cd python
python setup.py build --cpp_implementation
python setup.py install --prefix=$PREFIX
```

Install opencv
--------------
This is slightly different. Do:
```
cd $PREFIX/src
wget https://github.com/Itseez/opencv/archive/3.1.0.zip
unzip 3.1.0.zip
cd opencv-3.1.0
cmake . -DCMAKE_INSTALL_PREFIX=$PREFIX
make
make install
```

Install glog
------------
This is also easy:
```
cd $PREFIX/src
wget https://google-glog.googlecode.com/files/glog-0.3.3.tar.gz
tar zxvf glog-0.3.3.tar.gz
cd glog-0.3.3
./configure --prefix=$PREFIX
make
make install
```

Install gflags
--------------
This is easy:
```
cd $PREFIX/src
wget https://github.com/schuhschuh/gflags/archive/master.zip
unzip master.zip
cd gflags-master
cmake . -DCMAKE_INSTALL_PREFIX=$PREFIX -DBUILD_SHARED_LIBS=ON
make
make install
```

Install lmdb
------------
This is easy but requires some modification.
```
cd $PREFIX/src
git clone https://github.com/LMDB/lmdb
cd lmdb/libraries
```
Now, edit the ``./Makefile`` and change the line (line 29):
```
prefix = /usr/local
```
to
```
prefix = $(PREFIX)
```
Then, you just do:
```
make
make install
```

Install Boost
-------------
This step takes the most time.
Do:
```
cd $PREFIX/src
wget wget http://sourceforge.net/projects/boost/files/boost/1.60.0/boost_1_60_0.tar.bz2
tar jxvf boost_1_60_0.tar.bz2
./bootstrap.sh --with-libraries=python,system,filesystem,thread --prefix=$PREFIX
./b2 install
```

Remaining Dependencies
----------------------
Fortunately, the remaining dependencies are already modules of ``rice.rcac.purdue.edu``.
Simply add the following to your ``$HOME/.profile``
```
module load gcc
module load atlas
export LD_LIBRARY_PATH=$PREFIX/src/leveldb/out-shared:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/apps/rhel6/Anaconda-2.0.1/lib:$LD_LIBRARY_PATH
```

Install Caffe
-------------
Now, make sure you logout and login again so that the changes to your
``$HOME/.profile`` take place. Then:
```
cd $PREFIX/src
git clone https://github.com/BVLC/caffe.git
cd caffe
```
Do:
```
cp Makefile.config.example Makefile.config
```
Then edit ``Makefile.config`` making the following changes.

+ Uncomment the ``CPU_ONLY := 1`` line.
+ Set lines 50 and 51 to:

```
BLAS_INCLUDE := $(ATLAS)/include
BLAS_LIB := $(ATLAS)/lib
```
+ Comment out lines 64 and 65 (``PYTHON_INCLUDE``).
+ Set line 69 to:

```
ANACONDA_HOME := /apps/rhel6/Anaconda-2.0.1
```
+ Uncomment lines 70, 71, and 72 (``PYTHON_INCLUDE``).
+ Comment line 80 (``PYTHON_LIB := /usr/lib``).
+ Uncomment line 81 (``PYTHON_LIB := $(ANACONDA_HOME)/lib``).
+ Change lines 91 and 92 to:

```
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include \
                $(PREFIX)/include \
                $(PREFIX)/src/leveldb/include
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib \
                $(PREFIX)/lib \
                $(PREFIX)/src/leveldb/out-shared
```
As soon as you have done this, we are ready to compile:
```
make
make pycaffe
make runtest
```
