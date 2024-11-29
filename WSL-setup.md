# WSL-setup

setup procedure for *Windows Subsystem for Linux* (WSL). \
If you are reading this on Github, click on the top right button to show table of contents. \
Most of the operations below required that you have a stable internet connection.

Table of contents:
- [WSL](WSL-setup.md#setup-wsl)
- [Git](WSL-setup.md#setup-git)
	
## setup WSL

0. Show the list of installed WSL distributions in your system by running `wslconfig /l` in *Windows Powershell*. \
   Where you can unregister a distribution for a clean installation with `wslconfig /u Ubuntu`.
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
> Please respect the installation destination, always install in `/usr`, not `/usr/local`, nor `~/`.
> Because sometimes the program might not have permission to access certain directories and might behave weirdly.
```
├── boot
├── etc
├── home
│   ├── (user)
│   │   └── (home directory)
│   └── (otheruser)
├── mnt
├── tmp
└── usr (install here)
    ├── bin (binary files)
    ├── include (header .h files)
    ├── lib (library .so files)
    ├── share (usually data files)
    └── local (don't install here)
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
   You can check whether you have a public key generated, see if `ls -al ~/.ssh` shows a `.pub` file. \
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
1. `sudo apt install build-essential` will install C++, make, etc. \
   Compile the following code:
   ```cpp
   #include <iostream>
   #include <stdlib.h>

   int main(){
     std::cout << "Hello World!" << std::endl;
     return 0;
   }
   ```
   with `g++ ex-cpp.cc -o example-cpp`
2. `sudo apt install gfortran` will install GFortran. (I don't know why it is not included in `build-essential`) \
   Compile the following code:
   ```fortran
   program main
     write(*,*) 'Hello World!'
   end program main
   ```
   with `gfortran ex-fort.f90 -o example-fort`
3. `sudo apt install libgsl-dev` will install GNU scientific library. \
   Compile the following code:
   ```cpp
   #include <gsl/gsl_sf_bessel.h>
   #include <iostream>

   int main(){
     std::cout << gsl_sf_bessel_J0(1.0) << std::endl;
     return 0;
   }
   ```
   with 
   ```bash
   g++ ex-gsl.cc -o example-gsl `gsl-config -lib-without-cblas --cflags`
   ```

## setup LHAPDF

0. It is recommended to get the C++ version (LHAPDF6) instead of the Fortran version (LHAPDF5). \
   We will create a temporary folder for the installation `mkdir $HOME/prog_lhapdf6 && cd prog_lhapdf6`.
1. Download the tarball or follow the instructions [online](https://lhapdf.hepforge.org/install.html).
   ```bash
   wget https://lhapdf.hepforge.org/downloads/?f=LHAPDF-6.5.4.tar.gz -O LHAPDF-6.5.4.tar.gz
   tar xf LHAPDF-6.5.4.tar.gz
   cd LHAPDF-6.5.4
   ```
   You can check the latest version [here](https://lhapdf.hepforge.org/downloads/)
2. It is worth noting that Ubuntu will not support Python 2 as of version 24.04. \
   This means that you will not be able to install `python` through the `apt` package manager. \
   We then need `sudo apt install python-is-python3` to replace all `python` commands with `python3`. \
   We will also need `sudo apt install cython3` to correct some C-extension codes in python, such as `PyLongObject`. \
   We might as well get `sudo apt install python3-dev` for some development libraries.
3. We can now install LHAPDF6 (need root permission) with the following:
   ```bash
   sudo ./configure --prefix=/usr
   sudo make
   sudo make install
   ```
   We can verify with `lhapdf help` and see that `listdir` and `pdfdir` has the correct directory `/usr/share/LHAPDF`.
4. We will need to install some PDF sets with `lhapdf` (need permission). \
   run `sudo lhapdf install CT18NNLO` to download the CTEQ18 NNLO PDF set to `/usr/share/LHAPDF`.
5. Verify by compiling the following code:
   ```cpp
   #include "LHAPDF/LHAPDF.h"
   #include <iostream>
   using namespace LHAPDF;
 
   int main() { 
     const PDF* pdf = mkPDF("CT18NNLO",0);
     std::cout << pdf->xfxQ(0, 0.1, 1000.) << endl;
     delete pdf;
     return 0;
   }
   ```
   with
   ```bash
   g++ ex-pdf.cc -o example-pdf `lhapdf-config --cflags --libs`
   ```

## setup FastJet

0. We will create a temporary folder for the installation `mkdir $HOME/prog_fastjet3 && cd prog_fastjet3`. \
1. Download the tarball or follow the instructions [online](https://fastjet.fr/quickstart.html).
   ```bash
   wget https://fastjet.fr/repo/fastjet-3.4.3.tar.gz
   tar xf fastjet-3.4.3.tar.gz
   cd fastjet-3.4.3.tar.gz
   ```
   You can check the latest version [here](https://fastjet.fr/all-releases.html).
2. We can now install FastJet3 (need root permission) with the following:
   ```bash
   sudo ./configure --prefix=/usr --enable-allplugins
   sudo make
   sudo make install
   ```
   It is not necessary to include the last flag, which are some uncommon jet algorithms used by D0, ATLAS and CMS. But it doesn't hurt to install all. \
   We can verify with `fastjet-config --version`.
3. Verify by compiling the following code (or the example [online](https://fastjet.fr/quickstart.html)):
   ```cpp
   #include "fastjet/ClusterSequence.hh"
   #include <iostream>
   using namespace fastjet;

   int main(){
     std::vector<PseudoJet> particles;
     particles.push_back(PseudoJet( 100.0,  0.0, 0.0, 100.0));
     particles.push_back(PseudoJet(   5.0,  0.1, 0.0,   5.0));
     particles.push_back(PseudoJet( -50.0,  0.0, 0.0,  50.0));
     particles.push_back(PseudoJet(  -5.0,  0.5, 0.0,   5.0));

     JetDefinition jetR07(antikt_algorithm,0.7), jetR04(antikt_algorithm,0.4);
     ClusterSequence cs07(particles,jetR07), cs04(particles,jetR04);
     std::vector<PseudoJet> jets;

     jets = sorted_by_pt(cs07.inclusive_jets());
     std::cout << jetR07.description() << std::endl;
     for (unsigned i = 0; i < jets.size(); i++){
       std::cout << "jet " << i+1 
                 << " with pt = " << jets[i].pt() 
                 << " and phi = " << jets[i].phi() 
                 << " containing " << jets[i].constituents().size() 
                 << " constituents.\n";
     }
   }
   ```
   with
   ```bash
   g++ ex-fjet.cc -o example-fjet `fastjet-config --cxxflags --libs`
   ```
