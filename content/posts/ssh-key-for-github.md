---
title: "How to setup custom key for Git to authenticate against remote repo?"
date: 2023-02-08T09:15:19+05:30
draft: false
ShowToc: false
ShowRelatedContent: true
summary: "custom key to use for git auth."
tags: ["Git","Github"]
categories: ["Git"]
---
    
**Git** by default uses `~/.ssh/id_rsa` to authenticate against remote repository,
but doesn't give specific command line option to use **another key**.

If you have multiple remote repositories setup, this can be frustrating, to work around this,
you just need to export an `env variable` like this,
```shell
export GIT_SSH_COMMAND="ssh -i /path/to/your/ssh_key"
```
and after this you can happily authenticate against the repository you are intending to, and work with it.

But for working with another remote repo, you might need to run above command with other appropriate **private key**.