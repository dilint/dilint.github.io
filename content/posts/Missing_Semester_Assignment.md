+++
title = 'Missing Semester Assignment'
date = 2023-09-22T16:53:36+08:00
draft = false
tags = ['linux', 'shell', 'assignment', 'open-course']
+++


# Sheel Tools
> https://missing.csail.mit.edu/2020/shell-tools/

## Q1
### Question
Read man ls and write an ls command that lists files in the following manner

Includes all files, including hidden files
Sizes are listed in human readable format (e.g. 454M instead of 454279954)
Files are ordered by recency
Output is colorized
A sample output would look like this


```-rw-r--r--   1 user group 1.1M Jan 14 09:53 baz
 drwxr-xr-x   5 user group  160 Jan 14 09:53 .
 -rw-r--r--   1 user group  514 Jan 14 06:42 bar
 -rw-r--r--   1 user group 106M Jan 13 12:12 foo
 drwx------+ 47 user group 1.5K Jan 12 18:08 ..
 ```

### Answer

```bash
ls -alh
或者
fd | xargs -d "\n" stat --printf "%y %s %n\n"
```

## Q2
### Question
Write bash functions marco and polo that do the following. Whenever you execute marco the current working directory should be saved in some manner, then when you execute polo, no matter what directory you are in, polo should cd you back to the directory where you executed marco. For ease of debugging you can write the code in a file marco.sh and (re)load the definitions to your shell by executing source marco.sh.

### Answer
 

```bash
#!/usr/bin/env bash
marco(){
	pwd > /tmp/marco
}
polo(){
	cd $(cat /tmp/marco)
}
```


## Q3
### Question
Say you have a command that fails rarely. In order to debug it you need to capture its output but it can be time consuming to get a failure run. Write a bash script that runs the following script until it fails and captures its standard output and error streams to files and prints everything at the end. Bonus points if you can also report how many runs it took for the script to fail.

```bash
 #!/usr/bin/env bash

 n=$(( RANDOM % 100 ))

 if [[ n -eq 42 ]]; then
    echo "Something went wrong"
    >&2 echo "The error was using magic numbers"
    exit 1
 fi

 echo "Everything went according to plan"
```

### Answer
 solve.sh
 

```bash
#!/usr/bin/env bash

num=0
flag=0

echo "" > output 
echo "" > error

while [[ flag -eq 0 ]]; do
	(( n++ ))
	./a.sh 1>> output 2>> error
	if [[ $? -ne "0" ]]; then
		flag=1
	fi
done

echo $n

```

 
## Q4
### Question
As we covered in the lecture find’s -exec can be very powerful for performing operations over the files we are searching for. However, what if we want to do something with all the files, like creating a zip file? As you have seen so far commands will take input from both arguments and STDIN. When piping commands, we are connecting STDOUT to STDIN, but some commands like tar take inputs from arguments. To bridge this disconnect there’s the xargs command which will execute a command using STDIN as arguments. For example ls | xargs rm will delete the files in the current directory.

Your task is to write a command that recursively finds all HTML files in the folder and makes a zip with them. Note that your command should work even if the files have spaces (hint: check -d flag for xargs).

If you’re on macOS, note that the default BSD find is different from the one included in GNU coreutils. You can use -print0 on find and the -0 flag on xargs. As a macOS user, you should be aware that command-line utilities shipped with macOS may differ from the GNU counterparts; you can install the GNU versions if you like by using brew.

### Answer

```bash
touch foo\ {a..z}.html
fd -t f .html | xargs -d "\n" tar -czf target.zip
tar -tvf target.zip
```

## Q5
### Question
(Advanced) Write a command or script to recursively find the most recently modified file in a directory. More generally, can you list all files by recency?



### Answer

```bash
#!/usr/bin/env bash

flename=$1

fdfind -t f -p "$filename" | xargs -d "\n" stat -c "%y %n" | sort

```

# Version Control

> [课程地址](https://missing.csail.mit.edu/2020/version-control/) \
> [学习git_branch](https://learngitbranching.js.org/?locale=zh_CN) \
> [更好的答案](https://mit-6null-notes.book.triplez.cn/lec/06-version-control-git/)
## Q1
### Question
If you don’t have any past experience with Git, either try reading the first couple chapters of [Pro Git](https://git-scm.com/book/en/v2) or go through a tutorial like [Learn Git Branching](https://learngitbranching.js.org/). As you’re working through it, relate Git commands to the data model.
### Solution
略

## Q2
### Question
Clone the [repository for the class website](https://github.com/missing-semester/missing-semester).
1. Explore the version history by visualizing it as a graph.
2. Who was the last person to modify README.md? (Hint: use git log with an argument).
3. What was the commit message associated with the last modification to the collections: line of _config.yml? (Hint: use git blame and git show).
### Solution
1. `git log --all --graph --decorate --oneline`
2. `git log README.md` 
3. `git blame _config.yml | grep collections ; git show a88b4eac`
## Q3
### Question
One common mistake when learning Git is to commit large files that should not be managed by Git or adding sensitive information. Try adding a file to a repository, making some commits and then deleting that file from history (you may want to look at [this](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)).
### Solution
***Todo***
git-filter-repo不在ubuntu20.04lts资源包中
可以尝试安装jar工具[bfg](https://rtyley.github.io/bfg-repo-cleaner/)
```bash
$ bfg --delete-files YOUR-FILE-WITH-SENSITIVE-DATA
$ bfg --replace-text passwords.txt
$ git push --force
```
## Q4
### Question
Clone some repository from GitHub, and modify one of its existing files. What happens when you do git stash? What do you see when running git log --all --oneline? Run git stash pop to undo what you did with git stash. In what scenario might this be useful?
### Answer

```bash
$ echo 'Hello wordl' > modify
$ git add modify
$ git stash
Saved working directory and index state WIP on main: 9e6bd59 Delete snow directory
$ git commit
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
$ git stash pop
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   modify

Dropped refs/stash@{0} (7168746ee44d2e2d7dd8632d80862001ebba2af2)
$ git commit -m 'test the function of "git stash"'
[main fd35c4c] test the function of "git stash"
 1 file changed, 1 insertion(+)
 create mode 100644 modify
$ git log --oneline
fd35c4c (HEAD -> main) test the function of "git stash"
```
用来将staging area中内容暂时放到堆栈中
stash pop重新加入到stagin area
## Q5
### Question
Like many command line tools, Git provides a configuration file (or dotfile) called `~/.gitconfig`. Create an alias in `~/.gitconfig` so that when you run `git graph`, you get the output of `git log --all --graph --decorate --oneline`. Information about git aliases can be found [here](https://git-scm.com/docs/git-config#Documentation/git-config.txt-alias).
### Answer
在~/.gitconfig中修改
```bash
 [alias]
     graph = "log --all --graph --decorate --oneline"
```
获得结果

```bash
$ git graph
* fd35c4c (HEAD -> main) test the function of "git stash"
* 9e6bd59 (origin/main, origin/HEAD) Delete snow directory
```

## Q6
### Question
You can define global ignore patterns in `~/.gitignore_global` after running `git config --global core.excludesfile ~/.gitignore_global. `Do this, and set up your global gitignore file to ignore OS-specific or editor-specific temporary files, like .`DS_Store`.
### Solution
设置全局ignore文件 

```bash
$ echo ".DS_Store" >> ~/.gitignore_global
$ git config --global core.excludesfile ~/.gitignore_global
```

检查效果

```bash
$ touch .DS_Store
$ ls -a
.         ..        .DS_Store .git
$ git status                                                  
On branch main                                                                            
Your branch is ahead of 'origin/main' by 1 commit.                                             
(use "git push" to publish your local commits)                                                                                                                                          
nothing to commit, working tree clean
```

## Q7
### Question
Fork the [repository for the class website](https://github.com/missing-semester/missing-semester), find a typo or some other improvement you can make, and submit a pull request on GitHub (you may want to look at [this](https://github.com/firstcontributions/first-contributions)).
### Solution
无



# Meta Programming

> [课程地址](https://missing.csail.mit.edu/2020/metaprogramming/)
> [参考](https://jsinkers.github.io/notes/notebooks/missing_class/08_metaprogramming.html#)

## Q1
### Question
 Most makefiles provide a target called `clean`. This isn't intendedto produce a file called `clean`, but instead to clean up any files that can be re-built by make. Think of it as a way to "undo" all of the build steps. Implement a `clean` target for the `paper.pdf` `Makefile` above. You will have to make the target [phon](https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html). You may find the [`git ls-files`](https://git-scm.com/docs/git-ls-files) subcommand useful. A number of other very common make targets are listed [here](https://www.gnu.org/software/make/manual/html_node/Standard-Targets.html#Standard-Targets).
   ### Solution
`Makefile`文件中加入
```bash
.PHONY: clean
clean:
	git ls-files -o | xargs -d '\n' rm
```
---
   ## Q2
### Question
Take a look at the various ways to specify version requirements for dependencies in [Rust's build system](https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html). Most package repositories support similar syntax. For each one (caret, tilde, wildcard, comparison, and multiple), try to come up with a use-case in which that particular kind of requirement makes sense.
### Solution
**^:** 

```bash
 ^1.2.3 := 1.2.3
```

**~:** 
```bash
~1.2.3  := >=1.2.3, <1.3.0
~1.2    := >=1.2.0, <1.3.0
~1      := >=1.0.0, <2.0.0
```


**\*:** 

```bash
*     := >=0.0.0
1.*   := >=1.0.0, <2.0.0
1.2.* := >=1.2.0, <1.3.0
```

**<, <=, >, >=, =:**

```bash
>= 1.2.0
> 1
< 2
= 1.2.3
```

**multiple requirements** 
```bash
>= 1.2, < 1.5.
```
---
## Q3
### Question
Git can act as a simple CI system(continuous integration system) all by itself. In `.git/hooks` inside any git repository, you will find (currently inactive) files that are run as scripts when a particular action happens. Write a [`pre-commit`](https://git-scm.com/docs/githooks#_pre_commit) hook that runs `make paper.pdf` and refuses the commit if the `make` command fails. This should prevent any commit from having an unbuildable version of the paper.
### Solution
pre-commit是在执行`git commit`时被触发的脚本，用于确认提交是否成功，如果失败应该返回非0值则提交无法成功

```bash
#!/bin/sh
#
# Pre-commit script to prevent commit if the make fails

# Redirect output to stderr.

exec 1>&2

if make
then
        echo "Make successful"
else
        cat << EOF

Error: could not make pdf

EOF
exit 1
fi
```

   
   ---
## Q4
### Question
Set up a simple auto-published page using [GitHubPages](https://pages.github.com/).Add a [GitHub Action(https://github.com/features/actions) to therepository to run `shellcheck` on any shell files in that repository (here is [one way to do it](https://github.com/marketplace/actions/shellcheck)). Check that it works!
### Solution
 
注意yml文件对空格和tab极度敏感
ci.yml
```bash
# This is a basic workflow to help you get started with Actions

name: CI

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v3

      # Runs a single command using the runners shell
      - name: Run a one-line script
        run: echo Hello, world!

      # Runs a set of commands using the runners shell
      - name: Run a multi-line script
        run: |
          echo Add other actions to build,
          echo test, and deploy your project.

      - name: ShellCheck
        uses: ludeeus/action-shellcheck@master
```


   ---
## Q5
### Question
 [Build your own](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/building-actions) GitHub action to run [`proselint`](http://proselint.com/) or [`write-good`](https://github.com/btford/write-good) on all the `.md` files in the repository. Enable it in your repository, and check that it works by filing a pull request with a typo in it.
   
### Solution
给的两个proselint和write-good是两个命令行语法检查工具
可能需要现在docker仓库配置好环境 然后再使用fd .md得到的所有结果进行指令的执行


# Security and Cryptography

> [课程地址](https://missing.csail.mit.edu/2020/security/)

@[TOC]

## Q1
### Question
**Entropy**
1. Suppose a password is chosen as a concatenation of four lower-case dictionary words, where each word is selected uniformly at random from a dictionary of size 100,000. An example of such a password is `correcthorsebatterystaple`. How many bits of entropy does this have?
2. Consider an alternative scheme where a password is chosen as a sequence of 8 random alphanumeric characters (including both lower-case and upper-case letters). An example is `rg8Ql34g`. How many bits of entropy does this have?
3. Which is the stronger password?
4. Suppose an attacker can try guessing 10,000 passwords per second. On average, how long will it take to break each of the passwords?

### Solution
1.  $log_2(100000^4)=66.4385 \approx 67$
2. $log_2(62^8)=47.6335 \approx 48$
3. the first one
4. $100000^4 \div 10000=10^{16}$  and $62^8 \div 10000 \approx 21834010558$
前者破译大概需要317097919年，后者破译大概要693年。


## Q2
### Question
**Cryptographic hash functions.** Download a Debian image from a [mirror](https://www.debian.org/CD/http-ftp/) (e.g. [from this Argentinean mirror](http://debian.xfree.com.ar/debian-cd/current/amd64/iso-cd/)). Cross-check the hash (e.g. using the `sha256sum` command) with the hash retrieved from the official Debian site (e.g. [this file](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA256SUMS) hosted at `debian.org`, if you've downloaded the linked file from the Argentinean mirror).
### Solution
从mirror中下载得到系统镜像iso文件，对比官网中的sha256序列。
```bash
$ wget http://debian.xfree.com.ar/debian-cd/current/amd64/iso-cd/debian-11.3.0-amd64-netinst.iso
$ sha256sum debian-11.3.0-amd64-netinst.iso
7892981e1da216e79fb3a1536ce5ebab157afdd20048fe458f2ae34fbc26c19b  debian-11.3.0-amd64-netinst.iso
```

## Q3
### Question
**Symmetric cryptography.** Encrypt a file with AES encryption, using [OpenSSL](https://www.openssl.org/): `openssl aes-256-cbc -salt -in {input filename} -out {output filename}`. Look at the contents using `cat` or `hexdump`. Decrypt it with `openssl aes-256-cbc -d -in {input filename} -out   {output filename}` and confirm that the contents match the original using `cmp`.
### Solution
略
   ---
## Q4
### Question

1. **Asymmetric cryptography.**
    1. Set up [SSH keys](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2) on a computer you have access to (not Athena, because Kerberos interacts weirdly with SSH keys). Make sure your private key is encrypted with a passphrase, so it is protected at rest.
    1. [Set up GPG](https://www.digitalocean.com/community/tutorials/how-to-use-gpg-to-encrypt-and-sign-messages)
    1. Send Anish an encrypted email ([public key](https://keybase.io/anish)).
    1. Sign a Git commit with `git commit -S` or create a signed Git tag with `git tag -s`. Verify the signature on the commit with `git show       --show-signature` or on the tag with `git tag -v`.

### Solution
1.
 

```bash
$ ssh-keygen -t ed25519
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/wells/.ssh/id_ed25519): id_ed25519
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_ed25519
Your public key has been saved in id_ed25519.pub
The key fingerprint is:
SHA256:bi9RakjjCTXtbyaSk/HOKXruve/IhU9VsCnFNjOJQYY wells@HUIPU-Wells
The key's randomart image is:
+--[ED25519 256]--+
|       . o++o.   |
|      o E...B+   |
|     . o  ..o+.  |
|    . + . .. .   |
|     + OS+  .    |
|      O.*.+.     |
|       *+*o      |
|      o+=*       |
|    .=o.===      |
+----[SHA256]-----+

```
另可以使用ssh-copy-id向客户端发送公钥，例如

```bash
ssh-copy-id git@github.com
```

2.TODO
