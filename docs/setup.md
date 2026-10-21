# Setting up Ubuntu in VMware

You have **VMware Workstation Pro** on a Windows 11 laptop. The puzzle helper will run in an **Ubuntu** guest inside that virtual machine. Your Windows desktop stays as it is. Ubuntu is a second computer that lives in a window.

This page gets you to a working Python environment and a copy of this repository. There is no puzzle program to run after that. The next step is the book, in [the roadmap](../ROADMAP.md).

**You are done with setup when** this command prints `ok` inside the virtual environment:

```bash
python -c "import cv2, numpy, PIL; print('ok')"
```

## What you are installing

- Ubuntu Desktop LTS, as a virtual machine
- open-vm-tools, so Ubuntu can share the clipboard, drag-and-drop, and folders with Windows
- Python 3, pip, venv, and git
- A connection to GitHub
- This repository, plus a virtual environment with the starter libraries in [requirements.txt](../requirements.txt)

The starter libraries are OpenCV, NumPy, Pillow, and Jupyter. The phone web page comes in a later phase, so Flask and FastAPI are left out until then.

## 1. Download Ubuntu

Download **Ubuntu Desktop LTS** from <https://ubuntu.com/download/desktop>.

As of October 2026 the long-term release is **Ubuntu 26.04 LTS** (the "Resolute Raccoon" series, currently 26.04.1). Choose the Intel/AMD 64-bit image. The file is large (about 6 GB) and ends in `.iso`. Interim releases such as 26.10 are fine for experiments and a poor fit for a machine you want to keep stable. Stay on the LTS.

You can delete the ISO from Windows after Ubuntu is installed, if you want the disk space back.

## 2. Create the virtual machine

1. Open VMware Workstation Pro.
2. Choose **File > New Virtual Machine**.
3. Choose **Typical**, then Next.
4. Choose **Installer disc image file (iso)** and browse to the Ubuntu ISO.
5. If Workstation offers Easy Install, enter your full name (`John Sullivan`), a Linux username you will type often (for example `john`), and a password. That password is what Ubuntu asks for when a command uses `sudo`. It will not appear as you type it.
6. Name the virtual machine something you will recognize, such as `Ubuntu Puzzle`. The default folder on the Windows disk is fine.
7. Give the disk **40 GB** or more. One file for the virtual disk is fine. The disk grows as Ubuntu uses space, up to that limit.
8. On the summary page, choose **Customize Hardware** before you finish:
   - **Memory:** Ubuntu Desktop asks for about 6 GB. If the laptop has 16 GB of RAM or more, give the VM **8 GB**. If the laptop has 8 GB, give the VM **4 GB** and close heavy Windows programs while you work.
   - **Processors:** 2 is a comfortable start.
   - **Network Adapter:** **NAT**. This shares the laptop's internet connection and is the right setting while you install packages and use git. The phone cannot see a NAT address. You will switch this later, in the web-app phase. The steps are in [How the phone reaches Ubuntu](#how-the-phone-reaches-ubuntu-later) below.
   - **USB Controller:** leave it present (USB 3.x if that is the choice offered). You need it later for a webcam or an Arduino. You do not need to plug either of those in now.
9. Finish, and power the VM on.

Easy Install usually completes Ubuntu by itself. If it stops or never starts the installer, power on from the ISO and use Ubuntu's own **Install Ubuntu** button. The result is the same machine.

If Workstation says virtualization (VT-x or AMD-V) is disabled, enable it in the laptop's BIOS or UEFI setup, then try again. The manufacturer's support page for your laptop lists the key to press at boot (often F2, Del, or F10).

After Ubuntu boots to a desktop, open the **Terminal** app. You will do the rest of this guide there.

## 3. Install open-vm-tools

Ubuntu's packages are the right VMware tools for a current Ubuntu. Install them from apt, which is Ubuntu's installer:

```bash
sudo apt update
sudo apt upgrade
sudo apt install open-vm-tools open-vm-tools-desktop
sudo reboot
```

`sudo apt update` refreshes the list of packages. `sudo apt upgrade` installs updates. It may ask you to type `Y`, and on a fresh VM it can take several minutes. The reboot makes the tools active.

After the reboot, the Ubuntu window should resize when you resize the Workstation window. Turn on clipboard sharing and drag-and-drop if they are off:

1. With the VM selected, open **VM > Settings**.
2. Open the **Options** tab.
3. Open **Guest Isolation**.
4. Enable **drag and drop** and **copy and paste**.

Copy a short sentence in Windows and paste it in the Ubuntu terminal. If it pastes, the tools are working.

Then take a snapshot, so you can return to this moment later: **VM > Snapshot > Take Snapshot**. Name it `fresh Ubuntu`. A snapshot is a save point for the whole virtual machine.

## 4. Move photos in: shared folders or drag-and-drop

The everyday input is a phone photo. Until the web page exists, the picture travels phone → Windows → Ubuntu.

### Drag and drop

Drag a `.jpg` from Windows File Explorer onto the Ubuntu desktop or into the Ubuntu file manager. If the file appears, you are done with this section. Move it into the project `photos/` folder once the repo is cloned (step 8).

### Shared folder

A shared folder is better when you will drop many photos in the same place.

1. On Windows, create a folder such as `C:\Users\<your Windows name>\Pictures\puzzle`.
2. In Workstation: **VM > Settings > Options > Shared Folders**.
3. Set folder sharing to **Always enabled**.
4. Add the Windows folder. Give it a short name, such as `puzzle-photos`.

In Ubuntu:

```bash
ls /mnt/hgfs
```

You should see `puzzle-photos`. Copy a file into the project with `cp` once the repo exists:

```bash
cp /mnt/hgfs/puzzle-photos/section-A-001.jpg ~/puzzle-assistant/photos/
```

Copying into the project folder gives the file normal Ubuntu permissions. Leaving the working copy on `/mnt/hgfs` can be awkward, because those files are owned by the VMware share.

If `/mnt/hgfs` is missing or empty after a reboot, create the mount and make it permanent. Run this once. A second run would add a duplicate line to `/etc/fstab`.

```bash
sudo mkdir -p /mnt/hgfs
echo 'vmhgfs-fuse /mnt/hgfs fuse defaults,allow_other,_netdev 0 0' | sudo tee -a /etc/fstab
sudo mount /mnt/hgfs
ls /mnt/hgfs
```

Another fix that sometimes wakes a share up: in **VM > Settings > Options > Shared Folders**, set sharing to **Disabled**, accept, then set it back to **Always enabled**.

## 5. Install Python, pip, venv, and git

```bash
sudo apt install python3 python3-pip python3-venv git
python3 --version
git --version
```

`python3 --version` should print 3.10 or newer. Ubuntu 26.04 ships a newer 3.x, and that is what you want.

You will often type `python3` rather than `python` until a virtual environment is active. Inside the virtual environment, `python` works and means that environment's Python.

## 6. Tell Git your name

Git labels every commit with a name and an email. Use the name you want on the project, and the email on your GitHub account. GitHub can show a private `noreply` address under **Settings > Emails** if you would rather keep your real address off the commits. The help page is <https://docs.github.com/en/account-and-profile/how-tos/email-preferences/setting-your-commit-email-address>.

```bash
git config --global user.name "John Sullivan"
git config --global user.email "you@example.com"
git config --global --list
```

Replace `you@example.com` before you run the second command.

## 7. Connect to GitHub

Do **one** of the following. The SSH key is the one to do now: `git` is already installed, and SSH rides along with it. The GitHub CLI is the other way, and it is worth knowing about.

### Option A: an SSH key

Create a key. Press Enter to accept the default file location. A passphrase is optional. It is a password for the key itself. If you set one, Ubuntu will ask for it when you push.

```bash
ssh-keygen -t ed25519 -C "your-github-email"
cat ~/.ssh/id_ed25519.pub
```

The `cat` command prints a single line that starts with `ssh-ed25519`. Copy that whole line.

In a browser, open GitHub → **Settings → SSH and GPG keys → New SSH key**. Paste the line, give it a title such as `vmware-ubuntu`, and save.

Back in the terminal:

```bash
ssh -T git@github.com
```

The first time, SSH asks if you trust `github.com`. Type `yes`. A success message greets you by your GitHub username. It also says GitHub does not provide shell access. That greeting is the success.

More detail, if you want the official pictures: <https://docs.github.com/en/authentication/connecting-to-github-with-ssh>.

### Option B: the GitHub CLI

```bash
sudo apt install gh
gh auth login
```

Choose GitHub.com, HTTPS, and log in with the browser when it asks. After this, cloning and pushing over HTTPS can use that login.

If `apt` says it cannot find `gh`, use the install steps at <https://github.com/cli/cli/blob/trunk/docs/install_linux.md> and then run `gh auth login` again.

## 8. Clone this repository

SSH (option A above):

```bash
cd
git clone git@github.com:Rom12-1-2/puzzle-assistant.git
cd puzzle-assistant
```

HTTPS (option B, or any time you are already logged in):

```bash
cd
git clone https://github.com/Rom12-1-2/puzzle-assistant.git
cd puzzle-assistant
```

`cd` with no folder returns you to your home directory, `/home/<your Linux username>`. The project then lives at `/home/<your Linux username>/puzzle-assistant`.

## 9. Create a virtual environment and install the libraries

A virtual environment is a private folder of Python libraries for this project. Ubuntu protects its own Python (you may see the phrase "externally managed environment" if you `pip install` outside the environment). The private folder is the intended place to install OpenCV and the rest.

```bash
cd ~/puzzle-assistant
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

The prompt grows a `(.venv)` prefix when the environment is active. Every **new** terminal starts without it. Run these two lines again in that terminal:

```bash
cd ~/puzzle-assistant
source .venv/bin/activate
```

Check the install:

```bash
python -c "import cv2, numpy, PIL; print('ok', cv2.__version__)"
```

`requirements.txt` installs four things:

| Package | What you will use it for |
| --- | --- |
| `opencv-python` | Finding pieces in a photo |
| `numpy` | The number arrays those photos become |
| `pillow` | Opening and saving images the way the book teaches |
| `jupyter` | Notebooks for experiments, one step at a time |

Jupyter pulls in a lot of small packages. That is expected. Try it once:

```bash
jupyter notebook
```

Open the URL it prints, using the web browser **inside Ubuntu**. Stop it with Ctrl+C in the terminal.

When the import check prints `ok`, take another snapshot: **VM > Snapshot > Take Snapshot**, named `python ready`.

## How the phone reaches Ubuntu later

Skip this until [Phase 5 on the roadmap](../ROADMAP.md) (the phone web page). You do not need it to install Python or to study photos you copied in by hand.

The page will be served by Ubuntu, and your phone will open it on the home Wi-Fi. With the **NAT** adapter from step 2, the phone cannot see the VM. Two ways to fix that when the time comes:

1. **Bridged networking (the straightforward one).** Shut the VM down. **VM > Settings > Network Adapter > Bridged**. Power on. In Ubuntu, run `hostname -I` and note the address (four numbers, like `192.168.1.50`). From the phone, on the same Wi-Fi, open `http://` that address, plus the port the program prints (Flask often uses `5000`). Anyone on that Wi-Fi can open the page too. That is acceptable for a home puzzle project, and it is a reason to keep the page off the open internet.
2. **Stay on NAT and forward a port.** Workstation can forward a port on the Windows laptop to a port on the VM (**VM > Settings > Network Adapter > NAT > Advanced > Port Forwarding**). The phone then uses the Windows laptop's Wi-Fi address. Bridged mode is easier to explain, which is why it is listed first.

If the phone cannot connect and `sudo ufw status` says the firewall is active, allow the port you chose (for example `sudo ufw allow 5000/tcp`). Ubuntu Desktop often leaves this firewall inactive.

## Webcam and Arduino, later

Software phases use phone photos. A webcam that watches continuously is Phase 8. Lasers or an Arduino are Phase 9. When you reach them, USB works like this:

1. Plug the device into the Windows laptop.
2. In the Workstation menu bar, choose **VM > Removable Devices**.
3. Find the webcam or the Arduino, and choose **Connect** (the menu may say it will disconnect the device from the host).
4. Ubuntu now has the device. Windows does not, until you choose **Disconnect** on that same menu.

A connected webcam usually appears as `/dev/video0`. An Arduino usually appears as `/dev/ttyUSB0` or `/dev/ttyACM0`. You can list them with `ls /dev/video* /dev/ttyUSB* /dev/ttyACM*`.

The device is attached to one computer at a time: Windows, or the virtual machine.

## A Raspberry Pi, someday

A Raspberry Pi with its own camera can eventually sit beside the puzzle and run the finished program. That avoids keeping the laptop in the loop. It is a Phase 9 decision, after phone photos, the web page, and the feedback log already work. Learning Python happens here, in the VMware guest.

## If something goes wrong

**`pip` says the environment is externally managed.** You are outside the virtual environment. Run `source .venv/bin/activate` from `~/puzzle-assistant` and try again.

**`import cv2` says `libGL.so.1` is missing.** Install the graphics library the OpenCV package expects, then import again:

```bash
sudo apt install libgl1 libglib2.0-0
```

**`python` is not found, and `python3` works.** Activate the virtual environment. Inside it, `python` exists.

**SSH says permission denied.** The public key in GitHub's settings must match `~/.ssh/id_ed25519.pub`. The clone URL for a key is the `git@github.com:...` one.

**The shared folder is empty after a reboot.** Use the `fstab` commands in the shared-folder section, or toggle sharing off and on. If `mount` complains that `allow_other` is not permitted, open `/etc/fuse.conf` and remove the `#` from the start of the `user_allow_other` line, then run `sudo mount /mnt/hgfs` again.

**Drag and drop does nothing.** Check Guest Isolation (step 3), and confirm `open-vm-tools-desktop` is installed (`dpkg -l open-vm-tools-desktop`).

**A command prints a long error.** Copy the last few lines into your [learning log](learning-log.md), including the command you typed. That note is what you will want when you ask for help.

## WSL2, briefly

Windows 11 can also run Ubuntu through WSL2, with `wsl --install` in an Administrator PowerShell window. This guide follows VMware Workstation Pro, because that is the virtual machine you already have. You can leave WSL2 unused. USB cameras and Arduino boards are a different procedure under WSL2, and you do not need either of them for the software phases.
