# rustguess — a number-guessing game as a Linux kernel module

`rustguess` is a small out-of-tree Linux kernel module written in Rust. It exposes a character device at `/dev/rustguess` where users can write number guesses and read back hints from the kernel.

This project was built for CMSI 3510 HW5 as a Rust-for-Linux character device module.

