# WSL-setup

setup procedure for *Windows Subsystem for Linux* (WSL). \
If you are reading this on Github, click on the top right button to show table of contents. \
Most of the operations below required that you have a stable internet connection.

Table of contents:
[WSL](WSL-setup.md#setup-wsl),
[Git](WSL-setup.md#setup-git),
[compilers](WSL-setup.md#setup-compilers),
[Root](WSL-setup.md#setup-root),
[HepMC](WSL-setup.md#setup-hepmc),
[LHAPDF](WSL-setup.md#setup-lhapdf),
[FastJet](WSL-setup.md#setup-fastjet),
	
## setup WSL

0. Show the list of installed WSL distributions in your system by running `wsl --list` or `wslconfig /l` in *Windows Powershell*. \
   Where you can unregister a distribution for a clean installation with `wsl --unregister Ubuntu` or `wslconfig /u Ubuntu`.
1. List the available WSL distributions online by running `wsl --list --online`. \
   We then choose to install the lastest Ubuntu distribution with `wsl --install Ubuntu`. \
   You can find more information [here](https://learn.microsoft.com/en-us/windows/wsl/).
2. Complete the Ubuntu setup by entering a user name and password, then *Powershell* will automatically open the new Ubuntu kernel.
3. We can verify the install by showing the installation version with `hostnamectl`.
4. Update Ubuntu packages with the following `sudo apt update && sudo apt upgrade && sudo apt autoremove`.
5. It is then recommended to code using Visual Studio Code. \
   If you don't have it installed, get the Windows version from [here](https://code.visualstudio.com/Download). \
   Then simply enter `code .` in WSL to open remote connect from Windows to WSL via VScode.
6. To access WSL files in Windows, simply enter `explorer.exe .` in WSL to open a connection. \
   Depending on your WSL version, it will either be in the user directory, or a localhost network.
7. From now on, we will run WSL via VScode using a remote connection. \
   Open VScode in Windows, then click the bottom left blue button, or `ctrl+shift+P` and enter WSL.

> [!NOTE]
> Please respect the installation destination, always install in `/usr`, not `/usr/local`, nor `$HOME/`.
> Because sometimes the program might not have permission to access certain directories and might behave weirdly.
```
├── home
│   └── $(whoami) [home directory]
│       ├── examples
│       ├── prog_lhapdf6
│       ├── prog_pythia8
│       └── prog_root6
├── mnt
└── usr [install here]
    ├── bin [binary files]
    ├── include [header .h files]
    ├── lib [library .so files]
    ├── share [usually data files]
    └── local [don't install here !!!]
        ├── bin
        ├── include
        ├── lib
        └── share
```

## setup Git

0. Git should already be installed in the latest Ubuntu, to verify, run `git --version`.
1. Before we configure git, you should already have a Github account. We then configure using
   ```bash
   git config --global user.name "username"
   git config --global user.email "user@email"
   ```
2. We want to connect to Git using SSH protocol. Check that you have SSH installed: `ssh -V`. \
   You can check whether you have a public key generated, see if `ls -al $HOME/.ssh` shows a `.pub` file. \
   If not, generate one using: `ssh-keygen -t ed25519 -C "comment"`, where we usually put our email address in the comment. \
   Follow the instructions (just skip the passphrase) will create a public key for you. (ED25519 is better than RSA, so I've heard)
3. We then need to install Github CLI. For Ubuntu, we need to run the following lines found [here](https://github.com/cli/cli/blob/trunk/docs/install_linux.md).
   ```bash
   (type -p wget >/dev/null || (sudo apt update && sudo apt-get install wget -y)) \
	&& sudo mkdir -p -m 755 /etc/apt/keyrings \
	&& wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
	&& sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
	&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
	&& sudo apt update \
	&& sudo apt install gh -y
   ```
   Verify with: `gh --version`
4. Now that we have a Github account, an SSH key, and Github CLI, we need to establish a secure authentication connection with `gh auth login`. \
   Use *GitHub.com*, use *SSH* protocol, use the *.pub* public key generated, and choose login with web browser, enter the code and we are done. \
   We can test the connection by cloning this repository `gh repo clone Raymond-CL/Raymond-CL`. \
   In VScode, source control should automatically detect Git and it will display commit options. Make some minor modification to this repo and try to commit&push (need comment). 

## setup compilers

0. It is strongly recommended to install the GNU collection of compilers and support free software.
1. Install C++ and Fortran compilers `sudo apt install build-essential gfortran`.
2. Install Python environment `sudo apt install python3 python3-dev python3-numpy cython3 python-is-python3`. \
   It is worth noting that Ubuntu will not support Python 2 as of version 24.04. \
   This means that you will not be able to install `python` through the `apt` package manager.
3. Install some utilities `sudo apt install binutils cmake dpkg-dev htop mpich`. \
   optimize with `alias htop='htop -d 20 -u $(whoami) -s Command'`

## setup Root

0. Root require some libraries before installation, the core libraries can be install with
   ```bash
   sudo apt install libssl-dev libx11-dev libxext-dev libxft-dev libxpm-dev libtbb-dev libvdt-dev libgif-dev
   ```
   Then there are other optional packages (but we'll install them anyway) that can be install with
   ```bash
   sudo apt install libavahi-compat-libdnssd-dev libblas-dev libcfitsio-dev libedit-dev \
   libfftw3-dev libftgl-dev libgl2ps-dev libglew-dev libglu1-mesa-dev libgraphviz-dev libgsl-dev \
   libjpeg-dev libjpeg-dev libkrb5-dev libldap2-dev liblz4-dev liblzma-dev libmysqlclient-dev \
   libpcre2-dev libpcre3-dev libtiff-dev libtiff-dev libxml2-dev libxxhash-dev libzstd-dev \
   nlohmann-json3-dev postgresql qtwebengine5-dev sqlite3 terminfo
   ```
   These dependencies are a bit different from the official recommendations [here](https://root.cern/install/dependencies/).
1. It is recommended to build from [source](https://root.cern/install/#build-from-source). \
   ```bash
   mkdir $HOME/prog_root6 && cd $HOME/prog_root6
   git clone --branch latest-stable --depth=1 https://github.com/root-project/root.git ./root6_src
   mkdir ./root6_build && cd ./root6_build
   cmake -DCMAKE_INSTALL_PREFIX=/usr \
     -Dgnuinstall=ON \
     -Dbuiltin_xrootd=ON \
     ../root6_src
   sudo make -j8 install
   ```
   Here, I'm installing ROOT to `/usr` so that we don't have to set path. \
   There are some libraries that are ignored, such as CUDA-compiler, `cudnn`, PostgreSQL, SQLite, Davix, OCaml, RapidYAML. But they are not necessary. \
   Because the make process might take an hour, I'm using 8 cores to build those binaries.
2. We can then verify the installation with `root --version` and show features with `root-config --features`.
3. We now test an example, copy the codes from [this](https://root.cern.ch/root/htmldoc/guides/primer/ROOTPrimer.html#a-more-complete-example) online example to `$HOME/examples/ex-root.C`. Then compile with
   ```bash
   g++ $HOME/examples/ex-root.C -o $HOME/examples/ex-root `root-config --cflags --libs`
   ```
   which should produce a `.pdf` file and can be opened with VScode.

## setup HepMC

0. It is recommended to install ROOT before installing HEPMC3 (So that `ROOTIO` can be of use).
1. Check [here](https://hepmc.web.cern.ch/hepmc/index.html) for the latest release version of HEPMC3 and copy the link. \
   Download and install with:
   ```bash
   mkdir $HOME/prog_hepmc3 && cd $HOME/prog_hepmc3
   wget https://hepmc.web.cern.ch/hepmc/releases/HepMC3-3.3.0.tar.gz -O hepmc3.tar.gz
   tar -xf hepmc3.tar.gz
   mkdir ./hepmc3_build && cd ./hepmc3_build
   cmake -DCMAKE_INSTALL_PREFIX=/usr \
     -DHEPMC3_ENABLE_ROOTIO:BOOL=ON \
     -DHEPMC3_ENABLE_PROTOBUFIO:BOOL=OFF \
     -DHEPMC3_ENABLE_TEST:BOOL=ON \
     -DHEPMC3_INSTALL_INTERFACES:BOOL=ON \
     -DHEPMC3_BUILD_STATIC_LIBS:BOOL=OFF \
     -DHEPMC3_BUILD_DOCS:BOOL=ON \
     -DHEPMC3_BUILD_EXAMPLES:BOOL=ON \
     -DHEPMC3_ENABLE_PYTHON:BOOL=ON \
     -DHEPMC3_PYTHON_VERSIONS=3.12 \
     -DHEPMC3_Python_SITEARCH=/usr/lib/python3.12/site-packages \
     ../HepMC3-3.3.0
   sudo make -j8 install
   ```
   Here, I'm installing ROOT to `/usr` so that we don't have to set path. \
   which is a bit different from the procedure given [here](https://gitlab.cern.ch/hepmc/HepMC3), but no matter.
2. We can then verify the installation with `HepMC3-config --version` and show features with `HepMC3-config --features`.
3. We now test an example, copy the codes from [this](https://hepmc.web.cern.ch/hepmc/basic_tree_8cc-example.html) online example to `$HOME/examples/ex-hepmc.cc`. \
   Then compile with
   ```bash
   g++ $HOME/examples/ex-hepmc.cc -o $HOME/examples/ex-hepmc `HepMC3-config --cflags --ldflags`
   ```

## setup LHAPDF

0. It is recommended to get the C++ version (LHAPDF6) instead of the Fortran version (LHAPDF5). \
   Note that it does require Python, install Python first.
1. Check [here](https://lhapdf.hepforge.org/downloads/) for the latest release version of LHAPDF6. \
   Download and install with:
   ```bash
   mkdir $HOME/prog_lhapdf6 && cd $HOME/prog_lhapdf6
   wget https://lhapdf.hepforge.org/downloads/?f=LHAPDF-6.5.4.tar.gz -O lhapdf6.tar.gz
   tar -xf lhapdf6.tar.gz
   cd LHAPDF-6.5.4
   sudo ./configure --prefix=/usr
   sudo make
   sudo make install
   ```
   Here, I'm installing ROOT to `/usr` so that we don't have to set path. \
   You can check out the recommended installation guide [here](https://lhapdf.hepforge.org/install.html).
2. We can verify the installation with `lhapdf --version`. \
   We can also see that `listdir` and `pdfdir` when entered `lhapdf --help` has the correct default directory `/usr/share/LHAPDF`.
3. We will need to install some PDF sets with `lhapdf` (need permission). \
   run `sudo lhapdf install CT18NNLO` to download the CTEQ18 NNLO PDF set to `/usr/share/LHAPDF`.
4. We now test an example, copy the codes from [this](https://lhapdf.hepforge.org/_2examples_2testpdf_8cc-example.html) online example to `$HOME/examples/ex-lhapdf.cc`. \
   Then compile and run with (takes about half a minute):
   ```bash
   g++ $HOME/examples/ex-lhapdf.cc -o $HOME/examples/ex-lhapdf `lhapdf-config --cflags --libs`
   $HOME/examples/ex-lhapdf CT18NNLO 0
   ```

## setup FastJet

0. Note that it does require Python, install Python first.
1. Check [here](https://fastjet.fr/all-releases.html) for the latest release version of FastJet3. \
   Download and install with:
   ```bash
   mkdir $HOME/prog_fastjet3 && cd $HOME/prog_fastjet3
   wget https://fastjet.fr/repo/fastjet-3.4.3.tar.gz -O fastjet3.tar.gz
   tar -xf fastjet3.tar.gz
   cd fastjet-3.4.3
   sudo ./configure --prefix=/usr --enable-allplugins --enable-pyext
   sudo make
   sudo make install
   ```
   Here, I'm installing ROOT to `/usr` so that we don't have to set path. \
   It is not necessary to include the last 2 flags, which are some uncommon jet algorithms used by D0, ATLAS and CMS. But it doesn't hurt to install all. \
   You can check out the recommended installation guide [here](https://fastjet.fr/quickstart.html).
2. We can then verify the installation with `fastjet-config --version` and show plugins with `fastjet-config --list-plugins`.
3. We now test an example, copy the codes from [this](https://fastjet.fr/quickstart.html) online example to `$HOME/examples/ex-fastjet.cc`. \
   Then compile with:
   ```bash
   g++ $HOME/examples/ex-fastjet.cc -o $HOME/examples/ex-fastjet `fastjet-config --cxxflags --libs`
   ```
