---
layout: post
title: "Working with multiple private keys"
description: "Using multiple private keys to access the same host"
category: update
tags: [github, gitlab, git, bitbucket, ssh, blog, private keys]
comments: true
share: true
---

I have two accounts on [BitBucket](http://bitbucket.org) and I would like to access my git repositories through SSH. BitBucket doesn't allow me to use the same public key on two different accounts. So I have to create a new key-pair with different name. By default, git will use the default private key 
```~/.ssh/id_rsa``` and access will be refused. After a quick [googling](https://www.google.com.au/search?q=how+to+tell+git+which+ssh+key+to+use&oq=how+to+tell+ssh+to+use&aqs=chrome.3.69i57j0l3.9790j0j4&sourceid=chrome&ie=UTF-8) I found [this thread](http://superuser.com/questions/232373/tell-git-which-private-key-to-use) and that helped me to do what I needed to do.

## Create your new key-pair

As I mentioned above, you need to give it a different name (assuming you have a key-pair with the default name). Let's call it **id_rsa_bb**.

```
ssh-keygen -t rsa -C "your@emailaddress.com"
```

and you will see

```
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/yourname/.ssh/id_rsa):
```

then type in the new key file name and press enter.

```
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/yourname/.ssh/id_rsa): /Users/yourname/.ssh/id_rsa_bb
```

## Add your new public key to your remote server

On [BitBucket](http://bitbucket.org) navigate to Manage Account > SSH keys and click Add key. Or you can add your public key to any remote server you want to access.

Copy your public key to clipboard (on mac you can do the following):

```
$ pbcopy < ~/.ssh/id_rsa_bb.pub
```

and paste your public key on BitBucket and click Add key button.

## Update SSH config file

You need to update your SSH config file located in ```~/.ssh/config``` so that git knows which private key it should use.

Let's say you need to execute the following git command:

```
git clone git@bitbucket.org:bbusername/your-project.git
```

You need to add the following to SSH config file

```
Host bb # you can change this to whatever you like
        Hostname bitbucket.org
        User bbusername 
        IdentityFile ~/.ssh/id_rsa_bb
```

And the you can execute your git command like this (notice that **bitbucket.org** is replaced by **bb** or whatever that you specified)

```
git clone git@bb:bbusername/your-project.git
```

That's it. Your command should now run using specified private key.

## After thought

I expected ```~/.ssh/id_rsa``` and ```~/.ssh/id_rsa.pub``` would store multiple keys just like ```~/.ssh/known_hosts`` does. But it apparently doesn't.
