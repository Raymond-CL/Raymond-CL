# WSL-setup

setup procedure for *Windows Subsystem for Linux* (WSL). \
If you are reading this on Github, click on the top right button to show table of contents. \
Most of the operations below required that you have a stable internet connection.

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

0. It is strongly recommended to install the GNU collection of compilers ans support free software.
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
   with `g++ main.cc -o example-cpp`
2. `sudo apt install gfortran` will install GFortran. (I don't know why it is not included in `build-essential`) \
   Compile the following code:
   ```fortran
   program main
     write(*,*) 'Hello World!'
   end program main
   ```
   with `gfortran main.f90 -o example-fort`
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
   g++ gsl.cc -o example-gsl `gsl-config -lib-without-cblas --cflags`
   ```