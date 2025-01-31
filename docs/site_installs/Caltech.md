System info
-----------
    CentOS Linux release 7.6.1810 (Core) 
    Linux login1 3.10.0-957.1.3.el7.x86_64 #1 SMP Thu Nov 29 14:49:43 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
    glibc-devel-2.17-260.el7_6.3.x86_64

sdpb
====
    module load cmake/3.10.2 gcc/7.3.0 openmpi/3.0.0 boost/1_68_0-gcc730 eigen/eigen
    # Can not use openblas module
    # It is compiled with the old gcc, and that would require loading
    # two different libgfortran.so
    git clone https://gitlab.com/bootstrapcollaboration/elemental.git libelemental
    cd libelemental
    mkdir build
    cd build
    cmake .. -DCMAKE_INSTALL_PREFIX=$HOME/install -DCMAKE_CXX_COMPILER=mpicxx -DCMAKE_C_COMPILER=mpicc
    make
    make install

RapidJSON
---------
    git clone https://github.com/Tencent/rapidjson.git
    cd rapidjson
    mkdir build
    cd build
    cmake .. -DCMAKE_INSTALL_PREFIX=$HOME/install
    make
    make install

libarchive
----------
    wget http://www.libarchive.org/downloads/libarchive-3.5.1.tar.xz
    ./configure --prefix=$HOME/install
    make -j 16
    make install

sdpb
----
    ./waf configure --elemental-dir=$HOME/install --rapidjson-dir=$HOME/install --libarchive-dir=$HOME/install --prefix=$HOME/install
    ./waf # I needed to do './waf -j 1' (single threaded) to get it to compile without crashing
    ./waf install

scalar_blocks
=============

Trilinos
--------
    git clone --branch trilinos-release-12-12-branch https://github.com/trilinos/Trilinos.git
    mkdir build
    cd build
    cmake -DCMAKE_CXX_COMPILER=g++ -DCMAKE_C_COMPILER=gcc -DTrilinos_ENABLE_Sacado=ON -DTrilinos_ENABLE_Kokkos=OFF -DTrilinos_ENABLE_Teuchos=OFF -DCMAKE_INSTALL_PREFIX=$HOME/install ..
    make
    make install

scalar_blocks
-------------
    git clone https://gitlab.com/bootstrapcollaboration/scalar_blocks.git
    cd scalar_blocks
    ./waf configure --prefix=$HOME/install --trilinos-dir=$HOME/install --eigen-incdir=/software/eigen-b3f3d4950030/
    ./waf # maybe -j 1
    ./waf install

blocks_3d
=========

fmt
---
    wget https://github.com/fmtlib/fmt/releases/download/6.2.1/fmt-6.2.1.zip
    mkdir build
    cd build
    cmake -DCMAKE_CXX_COMPILER=g++ -DCMAKE_INSTALL_PREFIX=$HOME/install ..
    make
    make install
  
blocks_3d
---------
    git clone git@gitlab.com:bootstrapcollaboration/blocks_3d.git
    cd blocks_3d
    ./waf configure --prefix=$HOME/install --eigen-incdir=/software/eigen-b3f3d4950030/ --fmt-dir=$HOME/install --fmt-libdir=$HOME/install/lib64
    ./waf # maybe -j
    ./waf install

Note: If you are trying to build the "profiling" branch of `blocks_3d`, you will need to load the following modules before attempting the above:

    module purge
    module load gcc/9.2.0
    module load boost/1_76_0_gcc-9.2.0
    module load python3/3.7.0
    module load eigen/eigen

Batch scripts
-------------
    /home/wlandry/sdpb/runs/TTTT_small.sh
    /home/wlandry/scalar_blocks/runs/scalar_blocks.sh
