---
title: "CozyHosting | HTB Write-Up"
summary: Write-up for the 'CozyHosting' HackTheBox
date: 2023-11-05
series: ["HTB"]
weight: 1
aliases: ["/cozyhosting"]
tags: ["HTB"]
author: "Zaki"
---

## Intro

- Write-up for the 'CozyHosting' HackTheBox

---

## Walkthrough

Once connected to the box one can start with running a basic nmap scan to identify any active services

 `nnmap -sV`

> sV : server version

> oA : for ouput

> -sC : script version aut

Make sure you install latest version of **`hugo(>=0.83.0)`**.

After you have created a new site, at [Step 3](https://gohugo.io/getting-started/quick-start/#step-3-add-a-theme) follow the steps:

### Initial Foothold

Inside the folder of your Hugo site, run:

```bash
git clone https://github.com/adityatelange/hugo-PaperMod themes/PaperMod --depth=1
```

**Note**: You may use ` --branch v5.0` to end of above command if you want to stick to specific release.

> Updating theme :
>
> ```bash
> cd themes/PaperMod
> git pull
> ```

### User Flag

you can use as [submodule](https://www.atlassian.com/git/tutorials/git-submodule) with

```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive # needed when you reclone your repo (submodules may not get cloned automatically)
```


**Note**: You may use ` --branch v5.0` to end of above command if you want to stick to specific release.

> Updating theme :
>
> ```bash
> git submodule update --remote --merge
> ```

### Root Flag

Or you can Download as Zip from Github Page and extract in your themes directory

Direct Links:

- [Master Branch (Latest)](https://github.com/adityatelange/hugo-PaperMod/archive/master.zip)
- [v6.0](https://github.com/adityatelange/hugo-PaperMod/archive/v6.0.zip)
- [v5.0](https://github.com/adityatelange/hugo-PaperMod/archive/v5.0.zip)
- [v4.0](https://github.com/adityatelange/hugo-PaperMod/archive/v4.0.zip)
- [v3.0](https://github.com/adityatelange/hugo-PaperMod/archive/v3.0.zip)
- [v2.0](https://github.com/adityatelange/hugo-PaperMod/archive/v2.0.zip)
- [v1.0](https://github.com/adityatelange/hugo-PaperMod/archive/v1.0.zip)

### Method 4

 - Install [Go programming language](https://go.dev/doc/install) in your operating system.

 - Intialize your own hugo mod
 
```
hugo mod init YOUR_OWN_GIT_REPOSITORY
```

 - Add PaperMod in your `config.yml` file

```
module:
  imports:
  - path: github.com/adityatelange/hugo-PaperMod
```
 - Update theme

```
hugo mod get -u
```

### Finally ...

Add in `config.yml`:

```yml
theme: "PaperMod"
```

---



```shell
hugo new --kind post <name>
```

---
