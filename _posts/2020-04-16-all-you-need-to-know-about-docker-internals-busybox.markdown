---
layout: post
title:  "All You need to know about Docker Internals, Busybox, Musl, Alpine"
date:   2020-04-18 00:25:35 +0530
categories: nosql
---

https://www.youtube.com/watch?v=EnJ7qX9fkcU

Docker is just a fancy way to run a process, not a virtual machine. Rather a sandbox for running few processes separately.
Within the docker if you see all processes (ps ax), you find the command with which docker was run having a pid 1. On host OS, it will be running with some other pid. So that means that docker only compartmentalized the process from other processes on the host machine. 

This compatmentalization is achieved with cgroups (control groups) which controls what the container process (pid 1 process) can do.

VMs are an abstraction of physical hardware while docker is an abstraction of apps which run on same underlying OS kernel.
https://stackoverflow.com/questions/16047306/how-is-docker-different-from-a-virtual-machine?rq=1
So linux containers cannot be run on windows directly. docker for windows will create a linux VM on which linux containers run. (From windows 10, it has a built in virtualized linux kernel) And you cannot run windows container on linux.
https://stackoverflow.com/questions/42158596/can-windows-containers-be-hosted-on-linux

Features of docker:
AUFS: A layered file system >> Another Union file system
In newwer version it uses overlay2 in place of aufs
Layers are reused and can be used by thousands of containers
Also there can be read only layers.

Add about AUFS. overlay2 vs aufs
https://medium.com/@jessgreb01/digging-into-docker-layers-c22f948ed612
union mount is combining multiple directories in one. seemingly in linux, everything is a file (even folders are files). probably thats why it works. And It cannot work directly on windows without a linux virtual machine.

In newer version overlayFS (overlay2 driver) is being used in place of AuFS because it is faster ans simpler. in AUFS, the code was not written n a good way.
How overlay driver works: https://docs.docker.com/storage/storagedriver/overlayfs-driver/

Why alpine is so small (5MB)

Alpine is based on musl & busybox

Musl is a C execution library for OS based on linux kernel. It is an alternative to glibc and uses lesser space. basically musl and glibc are 2 different implementation of libc.
But glibc is faster. Musl is more secure and better at avoiding race condition. 
http://www.etalabs.net/compare_libcs.html This tells that musl is actually faster.


Busybox intro(https://www.youtube.com/watch?v=wWA6SvzvElU)
Busybox is single large executable file with several stripped down unix tools
https://unix.stackexchange.com/questions/567126/can-i-run-busybox-without-having-an-os-installed-on-the-machine/567135#567135

Alpine  is so smalll because: it is a collection of commands. All/Most commands are just a symlink to the busybox commands. so ls in alpine is a pointer to /bin/busybox
Basically it relies on the busybox rather than the os to to handle the interaction with the kernel.
Just to be clear, Kernel is not the same thing as an OS. You need the kernel to run on a hardware to call it a computer.
You need an OS to interact with it. (https://unix.stackexchange.com/a/567135/265828) 

So while other images (other than Alpine) have a copy of entire os, alpine relies on busybox only. alpine has apk as its package manager.


Read this: https://www.slideshare.net/jpetazzo/anatomy-of-a-container-namespaces-cgroups-some-filesystem-magic-linuxcon


Just to extend a little further, dual boot helps you load an os of your choice at every boot. So when you are running linux, the linux kernel is taking over the hardware and so on. So you can use only one at a time. In VMs, virtualization program is installed on your host which probably translates the VM os commands to host OS commands. Docker containers are even simpler and can run only the kernel which is there on the host.


{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
