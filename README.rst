xmipp-binaries
===============

This repository hosts Xmipp pre-built binaries used for Scipion installation. 


Building Xmipp binaries (on CentOS 7.9)
---------------------------------------
The Scipion installation script from 

https://github.com/delarosatrevin/scipion-install

is used to generate a basic conda environment very similar to the Scipion one. This conda environment is then
used to compile Xmipp from sources and generate the binaries. In this conda environment, just some 
problematic libraries are avoided, such as HDF5, that is used from the system.

The last Xmipp compilation was done on a **CentOS 7.9** Linux machine using the following steps:


Installing compiler and other dependencies:
...........................................

.. code-block:: bash

    yum install centos-release-scl
    yum install devtoolset-11-gcc devtoolset-11-gcc-c++
    # Install openmpi 4.1.14 from source in /usr/local/opempi/4.1.14  
    # Download hdf5 binaries (version 1.12.1) for CentOS 7 from https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.12/hdf5-1.12.1/bin/unix/    


Installing conda environment (pseudo-Scipion):
..............................................

.. code-block:: bash

    python ./install.py SCIPION_XMIPP_BUILD_FOLDER --build_xmipp


Building Xmipp binary
.....................


.. code-block:: bash

    scl enable devtoolset-11 -- bash
    
    export MPI_HOME=/usr/local/opempi/4.1.14
    export CUDA_HOME=/usr/local/cuda-11.6
    export LD_LIBRARY_PATH=$MPI_HOME/lib:$CUDA_HOME/lib64:$LD_LIBRARY_PATH
    export PATH=$MPI_HOME/bin:$CUDA_HOME/bin:$PATH:/home/jdela80/work/development/tests/build_xmipp/cmake-3.17.3/bin

    source SCIPION_XMIPP_BUILD_FOLDER/bashrc
    source SCIPION_XMIPP_BUILD_FOLDER/conda/etc/profile.d/conda.sh
    cd SCIPION_XMIPP_BUILD_FOLDER/source/xmipp-bundle

    ./xmipp get_devel_sources master
    ./xmipp get_dependencies master
    ./xmipp config  # provide hdf5-1.12.1 folder lib
    ./xmipp check_config
     # Disable OPENCV and CUDA in xmipp.conf
     # Include -L and -I to hdf5 lib and include folders if necessary
      
     ./xmipp compile 8
     ./xmipp install

     # If everything works fine, a *build* directory should be created
     # Let's create a tar file from it
     # copy all from hdf5/lib to build
     tar -cvzf xmipp-v3.23.07-b01.tgz build


Uploading the tar file
......................

.. code-block:: bash

    git clone https://github.com/delarosatrevin/xmipp-binaries.git
    cd xmipp-binaries
    mv SCIPION_XMIPP_BUILD_FOLDER/source/xmipp-bundle/xmipp-v3.23.07-b01.tgz ./
    git add xmipp-v3.23.07-b01.tgz
    git commit -m "Added Xmipp binary file for version 3.23.07"
    git push

    
