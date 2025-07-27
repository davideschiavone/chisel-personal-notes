# chisel-personal-notes

[OpenXiangShan](https://github.com/OpenXiangShan/XiangShan/tree/master) core is built on Chisel.

The version used: `e9f6ee7644a866fadce749f5195cec83b17119ee`

Documentation used to compile Chisel into Verilog is available [here](https://docs.xiangshan.cc/zh-cn/latest/tools/xsenv/).

It uses the [Frontend Develop Environment](https://github.com/OpenXiangShan/xs-env).

```
git clone https://github.com/OpenXiangShan/xs-env
cd xs-env
sudo -s ./setup-tools.sh # use apt to install dependencies, you may modify it to use different pkg manager
source setup.sh # prepare tools, test develop env using a small project
```

If you already have `verilator`, just comment out the line in `setup-tools.sh`.


Then proceed with:

```
source update-submodule.sh
source ./env.sh # setup XiangShan environment variables
```

As the minimum memory requirement is 32 GB, but recommended 64 GB and above, if you do not have enough memory
you need to increase your SWAP size.

When Chisel is converted to Verilog, it requires [40G](https://github.com/OpenXiangShan/XiangShan/blob/master/Makefile#L71). Anyway, I set the SWAP to 64GB (as described [here](https://askubuntu.com/questions/920595/fallocate-fallocate-failed-text-file-busy-in-ubuntu-17-04)):

STEP 1: First step is to check if by chance is there any SWAP partition already created in your PC:

```
sudo swapon --show
```

STEP 2: Next, let’s see the current partition structure of your computer’s hard disk:

```
df -h
```

STEP 3: Before you start the changes disable the use of swap:

```
sudo swapoff -a
```

STEP 4: Now it's time to create the SWAP file. Make sure you have enough space on the hard disk.

```
sudo dd if=/dev/zero of=/swapfile bs=1G count=64 status=progress
```

```
sudo chmod 600 /swapfile
```

STEP 5: Mark the file as SWAP space and enable it

```
sudo mkswap /swapfile
sudo swapon /swapfile
```

STEP 6: You can now check using the same swapon command to check if SWAP is created.

```
sudo swapon --show
```


STEP 7: Also check the final partition structure again.

```
free -h
```

STEP 8: Once everything is set, you must set the SWAP file as permanent, else you will lose the SWAP after reboot. Run this command:

```
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```


### Convert to Verilog

OpenXiangShan uses [SBT](https://www.scala-sbt.org/) to handle the Scala build and dependencies flow.

You need Java and SBT. However, I do not recall exactly if I installed it like following:

```
sudo apt-get install default-jdk
echo "deb https://repo.scala-sbt.org/scalasbt/debian all main" | sudo tee /etc/apt/sources.list.d/sbt.list
echo "deb https://repo.scala-sbt.org/scalasbt/debian /" | sudo tee /etc/apt/sources.list.d/sbt_old.list
curl -sL "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x2EE0EA64E40A89B84B2DF73499E82A75642AC823" | sudo tee /etc/apt/trusted.gpg.d/sbt.asc
sudo apt-get update
sudo apt-get install sbt
```

I installed the version `1.2.6`

```
~/xs-env/XiangShan$ sbt --version
[info] 1.2.6
sbt runner version: 1.11.2
```

Then, following the section `Generate Verilog code for Xiangshan core` from [here](https://docs.xiangshan.cc/zh-cn/latest/tools/xsenv/):

```
make init
make verilog
```

### NIX

You can use the Nix environment as well:

First, install Nix and flake as written [here](https://github.com/mschwaig/howto-install-nix-with-flake-support)

then do `nix develop` as reported [here](https://docs.xiangshan.cc/zh-cn/latest/tools/xsenv/)





