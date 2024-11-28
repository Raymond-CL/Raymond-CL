# WSL-setup

setup procedure for Windows Subsystem for Linux (WSL). \
If you are reading this on Github, click on the top right button to show table of contents.

## setup WSL

0. Show a list of installed WSL distributions by running the following in `Powershell`
```bash
wslconfig /l
```
where you can unregister a distribution for a clean installation
```bash
wslconfig /u Ubuntu
```

1. List the available WSL distributions online by running 
```bash
wsl --list --online
```
We then choose to install the lastest Ubuntu distribution with
```bash
wsl --install Ubuntu
```
> [!NOTE]
> You can choose to install WSL1 or WSL2, which is not so important.
> you can find more information [here](https://learn.microsoft.com/en-us/windows/wsl/).

2. Complete the Ubuntu setup by entering a user name and password, then `Powershell` will automatically open the new Ubuntu kernel.

3. Update Ubuntu packages with the following
```bash
sudo apt update && sudo apt upgrade && sudo apt autoremove
```

4. We can 