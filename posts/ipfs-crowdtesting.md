Here at Ubuntu we are working hard on the future of free software distribution.
We want developers to release their software to any Linux distro in a way
that's safe, simple and flexible. You can read more about this at
[snapcraft.io](https://snapcraft.io/).

This work is extremely fun because we have to work constantly with a wild
variety of free software projects to make sure that the tools we write are
usable and that the workflow we are proposing makes sense to developers and
gives them a lot of value in return. Today I want to talk about one of those
projects: IPFS.

IPFS is the permanent and decentralized web. How cool is that? You get a
peer-to-peer distributed file system where you store and retrieve files. They
have a nice demo [in their website](https://ipfs.io/), and you can give it a
try on Ubuntu Trusty, Xenial or later by running:

    $ sudo snap install ipfs

[![screenshot of the IPFS peers](https://archive.org/download/elopio-screenshots2/ipfs-peers.png)](https://archive.org/download/elopio-screenshots2/ipfs-peers.png)

So, here's one of the problems we are trying to solve. We have millions of users
on the Trusty version of Ubuntu, released during 2014. We also have millions of
users on the Xenial version, released during 2016. Those two versions are stable
now, and following the Ubuntu policies, they will get only security updates for
5 years. That means that it's very hard, almost impossible, for a young project
like IPFS to get into the Ubuntu archives for those releases. There will be no
simple way for all those users to enjoy IPFS, they would have to use a
[Personal Package Archive](https://en.wikipedia.org/wiki/Personal_Package_Archive)
or install the software from a
[tarball](https://en.wikipedia.org/wiki/Tar_(computing)). Both methods are
complex with high security risks, and both require the users to put a lot of
trust on the developers, more than what they should ever trust anybody.

We are closing the Zesty release cycle which will go out in April, so it's
too late there too. IPFS could make a
[deb](https://en.wikipedia.org/wiki/Deb_(file_format)), put it into Debian,
wait for it to sync to Ubuntu, and then it's likely that it will be ready for
the October release. Aside from the fact that we have to wait until October,
there are a few other problems. First, making a deb is not simple. It's not
too hard either, but it requires quite some time to learn to do it right.
Second, I mentioned that IPFS is young, they are on the 0.4.6 version. So, it's
very unlikely that they will want to support this early version for such a long
time as Debian and Ubuntu require. And they are not only young, they are also
fast. They add new features and bug fixes every day and make new releases almost
every week, so they need a feedback loop that's just as fast. A 6 months release
cycle is way too slow. That works nicely for some kinds of free software
projects, but not for one like IPFS.

They have been kind enough to let me play with their project and use it as a
test subject to verify our end-to-end workflow. My passion is testing, so I have
been focusing on continuous delivery to get happy early adopters and constant
feedback about the most recent changes in the project.

I started by making a
[snapcraft.yaml](https://github.com/elopio/ipfs-snap/blob/master/snapcraft.yaml)
file that contains all the metadata required for the snap package. The file is
pretty simple and to make the first version it took me just a couple of minutes,
true story. Since then I've been slowly improving and updating it with small
changes. If you are interested in doing the same for your project, you can read
[the tutorial to create a snap](https://tutorials.ubuntu.com/tutorial/create-first-snap).

I built and tested this snap locally on my machines. It worked nicely, so I
pushed it to the edge channel of the Ubuntu Store. Here, the snap is not visible
on user searches, only the people who know about the snap will be able to
install it. I told a couple of my friends to give it a try, and they came back
telling me how cool IPFS was. Great choice for my first test subject, no
doubt.

At this point, following the pace of the project by manually building and
pushing new versions to the store was too demanding, they go too fast. So, I
started working on continuous delivery by translating everything I did manually
into scripts and hooking them to travis-ci. After a few days, it got pretty
fancy, take a look at
[the github repo of the IPFS snap](https://github.com/elopio/ipfs-snap) if you
are curious. Every day, a new version is packaged from the latest state of the
master branch of IPFS and it is pushed to the edge channel, so we have a constant
flow of new releases for hardcore early adopters. After they install IPFS from
the edge channel once, the package will be automatically updated in their
machines every day, so they don't have to do anything else, just use IPFS as
they normally would.

Now with this constant stream of updates, me and my two friends were not enough
to validate all the new features. We could never be sure if the project was
stable enough to be pushed to the stable channel and make it available to the
millions and millions of Ubuntu users out there.

Luckily, the Ubuntu community is huge, and they are very nice people. It was
time to use the wisdom of the crowds. I invited the most brave of them to keep
the snap installed from edge and I defined a simple pipeline that leads to the
stable release using the four available channels in the Ubuntu store:

 * When a revision is tagged in the IPFS master repo, it is automatically pushed
   to edge channel from travis, just as with any other revision.
 * Travis notifies me about this revision.
 * I install this tagged revision from edge, and run a super quick test to make
   sure that the IPFS server starts.
 * If it starts, I push the snap to the beta channel.
 * With a couple of my friends, we run a
   [suite of smoke tests](https://gist.github.com/elopio/7492a28bd1aef6c4a86b5dcf5d5cb65b#file-ipfs-smoke-tests-md).
 * If everything goes well, I push the snap to the candidate channel.
 * I notify the community of Ubuntu testers about a new version in the candidate
   channel. This is were the magic of crowd testing happens.
 * The Ubuntu testers run the smoke tests in all their machines, which gives us
   the confidence we need because we are confirming that the new version works
   on different platforms, distros, distro releases, countries, network
   topologies, you name it.
 * This candidate release is left for some time in this channel, to let the
   community run thorough exploratory tests, trying to find weird usage
   combinations that could break the software.
 * If the tag was for a final upstream release, the community also runs
   [update tests](https://gist.github.com/elopio/7492a28bd1aef6c4a86b5dcf5d5cb65b#file-ipfs-update-tests-md)
   to make sure that the users with the stable snap installed will get this new
   version without issues.
 * After all the problems found by the community have been resolved or at least
   acknowledged and triaged as not blockers, I move the snap from candidate
   to the stable channel.
 * All the users following the stable channel will automatically get a very well
   tested version, thanks to the community who contributed with the testing and
   accepted a higher level of risk.
 * And we start again, the never-ending cycle of making free software :)

Now, let's go back to the discussion about trust. Debian and Ubuntu, and most of
the other distros, rely on maintainers and distro developers to package and
review every change on the software that they put in their archives. That is
a lot of work, and it slows down the feedback loop a lot, as we have seen. In
here we automated most of the tasks of a distro maintainer, and the new
revisions can be delivered directly to the users without any reviews. So the
users are trusting directly their upstream developers without intermediaries,
but it's very different from the previously existing and unsafe methods. The
code of snaps is installed read-only, very well constrained with access only to
their own safe space. Any other access needs to be declared by the snap, and
the user is always in control of which access is permitted to the application.

This way upstream developers can go faster but without exposing their users to
unnecessary risks. And they just need a simple snapcraft.yaml file and to define
their own continuous delivery pipeline, on their own timeline.

By removing the distro as the intermediary between the developers and their
users, we are also making a new world full of possibilities for the Ubuntu
community. Now they can collaborate constantly and directly with upstream
developers, closing this quick feedback loop. In the future we will tell our
children of the good old days when we had to report a bug in Ubuntu, which
would be copied to Debian, then sent upstream to the developers, and after 6
months, the fix would arrive. It was fun, and it lead us to where we are today,
but I will not miss it at all.

Finally, what's next for IPFS? After this experiment we got more than 200
unique testers and almost 300 test installs. I now have great confidence on
this workflow, new revisions were delivered on time, existing Ubuntu
testers became new IPFS contributors and I now can safely recommend IPFS
users to install the stable snap. But there's still plenty of work ahead.
There are still manual steps in the pipeline that can be scripted, the smoke
tests can be automated to leave more free time for exploratory testing, we can
release also to armhf and arm64 architectures to get IPFS into the
[IoT world](https://www.ubuntu.com/internet-of-things), and well, of course the
developers are not stopping, they keep releasing new interesting features. As I
said, plenty of opportunities for us as distro contributors.

[![screenshot of the IPFS snap stats](https://archive.org/download/elopio-screenshots2/ipfs-stats.png)](https://archive.org/download/elopio-screenshots2/ipfs-stats.png)

I'd like to thank everybody who tested the IPFS snap, specially the following
people for their help and feedback:

 * freekvh
 * urcminister
 * Carla Sella
 * casept
 * Colin Law
 * ventrical
 * cariboo
 * howefield

<3

If you want to release your project to the Ubuntu store, take a look at the
[snapcraft docs](https://snapcraft.io/), the
[Ubuntu tutorials](https://tutorials.ubuntu.com/), and come talk to us in
[Rocket Chat](https://rocket.ubuntu.com/channel/snapcraft).