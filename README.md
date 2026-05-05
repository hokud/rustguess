# rustguess

An interactive number-guessing game built as a Linux kernel module in Rust.

## Description

`rustguess` is a small out-of-tree Linux kernel module written in Rust. It exposes a character device at `/dev/rustguess` where users can write number guesses and read back hints from the kernel.

This project was built for CMSI 3510 HW5 as a Rust-for-Linux character device module. It demonstrates the basic workflow for building, loading, testing, and unloading a safe Rust Linux kernel module. The module keeps a small amount of game state in the kernel, parses guesses written from user space, and returns feedback through normal file operations.


# Getting Started

## Dependencies

This project should be built inside a Linux virtual machine, not directly on a personal host machine.

Required setup:

* Ubuntu 26.04 LTS VM, such as a Multipass VM
* Rust-enabled Linux kernel
* Linux kernel headers
* Rust compiler version expected by the kernel, such as `rustc-1.93`
* Rust source package, such as `rust-1.93-src`
* `bindgen`
* `kmod`
* `build-essential`
* `make`

Install dependencies inside the VM:

~~~bash
sudo apt update
sudo apt install -y build-essential linux-headers-$(uname -r) kmod tree
sudo apt install -y rustc-1.93 rust-1.93-src bindgen
sudo update-alternatives --install /usr/bin/rustc rustc /usr/bin/rustc-1.93 100
~~~

Verify the toolchain:

~~~bash
uname -r
rustc --version
ls /lib/modules/$(uname -r)/build/rust
~~~


## Installing

Clone this repository inside the Linux VM:

~~~bash
git clone https://github.com/hokud/rustguess.git
cd rustguess
~~~

Build the module:

~~~bash
make clean && make
~~~

After a successful build, the compiled kernel module should appear as:

~~~text
rustguess.ko
~~~


## Executing program

Load the module:

~~~bash
sudo insmod rustguess.ko
~~~

Check that the character device was created:

~~~bash
ls -la /dev/rustguess
~~~

Read the welcome message:

~~~bash
sudo cat /dev/rustguess
~~~

Make a guess:\
<sub>Replace N with the number you're guessing</sub>
~~~bash
echo N | sudo tee /dev/rustguess > /dev/null
sudo cat /dev/rustguess
~~~

Unload the module when finished:

~~~bash
sudo rmmod rustguess
~~~

Confirm the module is unloaded:

~~~bash
ls -la /dev/rustguess
~~~

Expected output:
~~~bash
ls: cannot access '/dev/rustguess': No such file or directory
~~~


## Help

Common issues:

* If `make` fails with a file-name related error, make sure the `Makefile` uses `obj-m += rustguess.o` and the source file is named `rustguess.rs`.
* If `dmesg` does not show your module logs, run it with `sudo`.
* If `/dev/rustguess` does not exist, make sure the module loaded successfully with `sudo insmod rustguess.ko`.
* If `insmod` says the module is already loaded, unload it first:

~~~bash
sudo rmmod rustguess
~~~

* If the game is already won and you want to restart it, unload and reload the module:

~~~bash
sudo rmmod rustguess
sudo insmod rustguess.ko
~~~

* If you see messages like `loading out-of-tree module taints kernel` or `module verification failed`, that is expected for this assignment. The module is built outside the official kernel tree and is not signed with a trusted kernel signing key.

## Authors

Nahokulani Dennis  
GitHub: [@hokud](https://github.com/hokud)

## License

This project is licensed under the GPL-2.0 License - see the `LICENSE` file for details.

## Acknowledgments

* CMSI 3510 course materials on Linux kernel modules and Rust-for-Linux
* Rust-for-Linux examples and documentation
* Linux kernel `samples/rust/` examples
