There is a huge announcement coming: snaps now run in Ubuntu 14.04 Trusty Tahr.

Take a moment to note how big this is. Ubuntu 14.04 is a long-term release that
will be supported until 2019. Ubuntu 16.04 is also a long-term release that
will be supported until 2021. We have many many many users in both releases,
some of which will stay there until we drop the support. Before this snappy new
world, all those users were stuck with the versions of all their programs
released in 2014 or 2016, getting only updates for security and critical
issues. Just try to remember how your favorite program looked 5 years ago;
maybe it didn't even exist. We were used to choose between stability and cool
new features.

Well, a new world is possible. With snaps you can have a stable base system
with frequent updates for every program, without the risk of breaking your
machine. And now if you are a Trusty user, you can just start taking advantage
of all this. If you are a developer, you have to prepare only one release and
it will just work in all the supported Ubuntu releases.

Awesome, right? The Ubuntu devs have been doing a great job. snapd has already
landed in the Trusty archive, and we have been running many manual and
automated tests on it. So we would like now to invite the community to test it,
explore weird paths, try to break it. We will appreciate it very much, but all
of those Trusty users out there will love it, when they receive loads of new
high quality free software on their oldie machines.

So, how to get started?

If you are already running Trusty, you will just have to install snapd:

    $ sudo apt update && sudo apt install snapd

Reboot your system after that in case you had a kernel update pending, and to
get the paths for the new snap binaries set up.

If you are running a different Ubuntu release, you can
[Install Ubuntu in a virtual machine](http://elopio.net/blog/install-ubuntu-in-vm/).
Just make sure that you install the
[http://releases.ubuntu.com/14.04/ubuntu-14.04.5-desktop-amd64.iso](14.04 iso).

Once you have Trusty with snapd ready, try a few commands:

    $ snap list
    $ sudo snap install hello-world
    $ hello-world
    $ snap find something

[![screenshot of snaps running in Trusty](https://archive.org/download/elopio-screenshots2/trusty/trusty-snaps-bigger.png)](https://archive.org/download/elopio-screenshots2/trusty/trusty-snaps-bigger.png)

Keep searching for snaps until you find one that's interesting. Install it,
try it, and let us know how it goes.

If you find something wrong, please
[report a bug](https://bugs.launchpad.net/snapd/+filebug) with the `trusty`
tag. If you are new to the Ubuntu community or get lost on the way, come and
join us in [Rocket Chat](https://rocket.ubuntu.com/channel/community).

And after a good session of testing, sit down, relax, and get ohmygiraffe. With
love from [popey](https://twitter.com/popey):

    $ sudo snap install ohmygiraffe
    $ ohmygiraffe

[![screenshot of ohmygiraffe](https://archive.org/download/elopio-screenshots2/trusty/ohmygiraffe.png)](https://archive.org/download/elopio-screenshots2/trusty/ohmygiraffe.png)
