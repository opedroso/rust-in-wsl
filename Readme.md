# WSL Install on Windows 10

- Settings => Turn Windows Features On
	- check Windows Subsystem for Linux (WSL)
	- check Virtual Machine Platform (you may need to enable VM in Bios)
- wsl --list --online
- wsl --install -d kali-linux
```
downloading ...
	Installing, this may take a few minutes...
WslRegisterDistribution failed with error: 0x80004005
Error: 0x80004005 Unspecified error

Press any key to continue...

wsl --update

C:\Users\opedroso>wsl --update
Installing: Windows Subsystem for Linux
Windows Subsystem for Linux has been installed.

I will update here for insurance that everything gets commited to disk
[rebooting...]
run CMD.exe as administrator
C:\Windows\System32>wsl --install -d Ubuntu-24.04
Installing: Ubuntu 24.04 LTS
Ubuntu 24.04 LTS has been installed.
Launching Ubuntu 24.04 LTS...
Installing, this may take a few minutes...
Please create a default UNIX user account. The username does not need to match your Windows username.
For more information visit: https://aka.ms/wslusers
Enter new UNIX username: opedroso
New password:
Retype new password:
...
opedroso@PRECISIONM4700:~$ sudo apt update
[sudo] password for opedroso:
Hit:1 http://security.ubuntu.com/ubuntu noble-security InRelease
...
opedroso@PRECISIONM4700:~$ sudo apt upgrade
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## Installing Rust development environment

https://www.digitalocean.com/community/tutorials/install-rust-on-ubuntu-linux

```
curl --proto '=https' --tlsv1.3 https://sh.rustup.rs -sSf | sh
sudo apt update
sudo apt upgrade
sudo apt install build-essential
```

## Installing VsCode ide

```
sudo apt install software-properties-common apt-transport-https wget gpg
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt update
sudo apt install code
```

After doing all that, when first running code, it tells me to uninstall the WSL version of vscode and use instead the windows version.
Just 'yes' and use the WSL version- if you add this next line to your environment, code will stop asking this question.
export DONT_PROMPT_WSL_INSTALL=yeah # better yet, add it as last line to your ~/.bashrc

I suggest you add the following extensions that will be used for Rust development.
```
code --install-extension rust-lang.rust-analyzer
code --install-extension vadimcn.vscode-lldb
code --install-extension jinxdash.prettier-rust
code --install-extension yzhang.markdown-all-in-one
```

## Create new library project 

```
mkdir ~/repo
pushd ~/repo
cargo new rust-in-wsl --lib
cd rust-in-wsl
code .
```

## Create an bin target under this same project named rust-in-wsl

Using code, create a new file source file at `src/bin/main.rs` folder which you will use vscode to create.
(select src on the left panel, click on [folder+] icon, type `bin` for its name, select the new bin folder, click on [file+], type main.rs)

This is the content of `main.rs`:

```rust
extern crate rust_in_wsl; // use the name of your package here

fn main() {
    let sum = rust_in_wsl::add(5, 6);
    println!("The sum is: {}", sum);
}
```

Use the `cargo run --bin main` to build and run it, which will print this:
```
opedroso@PRECISIONM4700:~/repo/rust-in-wsl$ cargo run --bin main
    Finished dev [unoptimized + debuginfo] target(s) in 0.00s
     Running `target/debug/main`
The sum is: 11
```
IYou can create another file names `src/bin/exe2.rs`, copy the source from main.rs, making a few changes, then run it as well:
```
opedroso@PRECISIONM4700:~/repo/rust-in-wsl$ cargo run --bin exe2
    Finished dev [unoptimized + debuginfo] target(s) in 0.01s
     Running `target/debug/exe2`
The sum is: 20
```
The contents of `src/bin/exe2.rs` is this:
```rust
extern crate rust_in_wsl; // use the name of your package here

fn main() {
    let sum = rust_in_wsl::add(10, 10);
    println!("The sum is: {}", sum);
}
```

Both of these are executable that can now access the functions defined in lib.rs.

## Saving this project to the Git repository

When you create a new project using `git init ...` cargo automatically does a `git init ...` for the project directory.
We will now commit our changes to this local repository, but before some git setup:

```
git config --global user.email "opedroso@example.com"
git config --global user.name "Osiris Pedroso"
```

Once that is done, we open the `Source Control` panel which will show all the files that are changed ('M') and added ('A') at the right most of the pane. At this point if you have too many unexpected files, open your projects `.gitignore` and give it this content:

```
target/**
Cargo.lock
```

The lines will try to match the filenames encountered in directories underneat the project root.
If they are a regex match, they will not be considered to be added by Git.

Back to the `Source Control` pane, should see only files that you directly modified and were not created by the 'cargo build' or 'cargo run' commands.

Hover the mouse over the line that says `Staged Changes` and click the `+` character to stage all files for Git.
Now give it a comment what these changes entain on the field above the `Commit` button.
Something like "added readme and sample code for lib and 2 executables".
Then click the `Commit` button.