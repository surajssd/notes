# Bangalore Linux Kernel Hackathon

Date: Dec 3, 2016

## Requirements:

- Linux kernel code: `git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git`
- Install `git-email` package
- Keep your gitconfig file to have send-mail

```
$ cat ~/.gitconfig
[user]
    name = Suraj Deshmukh
    email = surajssd009005@gmail.com
[color]
  diff = auto
  status = auto
  branch = auto
  interactive = auto
  ui = true
  pager = true
[diff]
    tool = vimdiff
[difftool]
    prompt = false
[sendemail]
  from = Suraj Deshmukh <surajssd009005@gmail.com>
  smtpserver = smtp.gmail.com
  smtpuser = surajssd009005@gmail.com
  smtpencryption = tls
  chainreplyto = false
  smtpserverport = 587
```


## Steps:

**NOTE**: All these steps are available in [2].

- To find easy fixes in code base, like coding style warnings, See [1]:

```bash
./scripts/checkpatch.pl -f --terse ./linux/net/mpls/af_mpls.c
```

- Fix some warning, commit the changes, see [4]:

```bash
git add <file name>
git commit -s -v
```

- Create a patch to send it

```bash
git format-patch -o /tmp/ HEAD^
```

- Verify it's okay to send

```bash
./scripts/checkpatch.pl /tmp/0001-net-af_mpls.c-add-space-before-open-parenthesis.patch
```

- Find the maintainer and ML to send the patch to

```bash
./scripts/get_maintainer.pl /tmp/0001-net-af_mpls.c-add-space-before-open-parenthesis.patch
```

- Once that is found send patch email:

```bash
git send-email --to=davem@davemloft.net --cc=netdev@vger.kernel.org --cc=linux-kernel@vger.kernel.org /tmp/0001-net-af_mpls.c-add-space-before-open-parenthesis.patch
```


## Ref:

- [0] Agenda: https://kernelmeetup.wordpress.com/2016/11/30/kernel-hackathon-agenda/
- [1] The newbie's guide to hacking the Linux kernel http://www.tuxradar.com/content/newbies-guide-hacking-linux-kernel
- [2] Slides: https://github.com/vthakkar1994/Talks/blob/master/Introduction%20to%20Linux%20Kernel%20Hacking.pdf
- [3] FirstKernelPatch https://kernelnewbies.org/FirstKernelPatch#head-
- [4] PatchPhilosophy https://kernelnewbies.org/PatchPhilosophy
- [5] Setting up git send-email with gmail https://coderwall.com/p/dp-gka/setting-up-git-send-email-with-gmail
