[Travis CI](https://travis-ci.org/) offers a great continuous integration
service for the projects hosted in GitHub. With it you can run tests, deliver
artifacts and deploy services on pull requests, when they are merged, or with
some other frecuency.

Last week they
[updated the Ubuntu 14.04 (Trusty) machines](https://blog.travis-ci.com/2017-06-21-trusty-updates-2017-Q2-launch)
that run these jobs. This update came with a nice surprise for everybody
working to deliver software to Linux users, because it is now possible to
install [snaps](https://snapcraft.io) in Travis!

I've been excited all week telling people about all the doors that this opens;
but if you have been following my adventures in the Ubuntu world, by now you
can problably guess that I'm mostly thinking about all the potential this has
for automated testing. For the automation of user acceptance tests.

User acceptance tests are executed from the point of view of the user, with
your software presented as a black box to them. The tests can only interact
with the software through the entry points you define for your users. If it's
a CLI application, then the tests will call commands and subcommands and check
the outputs. If it's a website or a desktop application, the tests will click
things, enter text and check the changes on this GUI. If it's a service with
an HTTP API, the tests will make requests and check the responses. On these
tests, the closer you can get to simulate the environment and behaviour of your
real users, the better.

Snaps are great for the automation of user acceptance tests because they are
immutable and they bundle all their dependencies. With this we can make sure
that your snap will work the same on any of the operating systems and
architectures that support snaps. The snapd service takes care of hidding
the differences and presenting a consistent execution environment for the snap.
So, getting a green execution of these tests in the Trusty machine of Travis is
a pretty good indication that it will work on all the active releases of
Ubuntu, Debian, Fedora and even on a Raspberry Pi.

Let me show you an example of what I'm talking about, obviously using my
favourite snap called IPFS. There is
[more information about IPFS in my previous post](http://elopio.net/blog/ipfs-crowdtesting/).

This is the packaging metadata for the IPFS snap, a single `snapcraft.yaml`
file:

```
name: ipfs
version: master
summary: global, versioned, peer-to-peer filesystem
description: |
  IPFS combines good ideas from Git, BitTorrent, Kademlia, SFS, and the Web.
  It is like a single bittorrent swarm, exchanging git objects. IPFS provides
  an interface as simple as the HTTP web, but with permanence built in. You
  can also mount the world at /ipfs.
confinement: strict

apps:
  ipfs:
    command: ipfs
    plugs: [home, network, network-bind]

parts:
  ipfs:
    source: https://github.com/ipfs/go-ipfs.git
    plugin: nil
    build-packages: [make, wget]
    prepare: |
      mkdir -p ../go/src/github.com/ipfs/go-ipfs
      cp -R . ../go/src/github.com/ipfs/go-ipfs
    build: |
      env GOPATH=$(pwd)/../go make -C ../go/src/github.com/ipfs/go-ipfs install
    install: |
      mkdir $SNAPCRAFT_PART_INSTALL/bin
      mv ../go/bin/ipfs $SNAPCRAFT_PART_INSTALL/bin/
    after: [go]
  go:
    source-tag: go1.7.5
```

It's not the most simple snap because they use their own build tool to get the
go dependencies and compile; but it's also not too complex. If you are new to
snaps and want to understand every detail of this file, or you want to package
your own project, the
[tutorial to create your first snap](https://tutorials.ubuntu.com/tutorial/create-first-snap)
is a good place to start.

What's important here is that if you run `snapcraft` using this yaml file, you
will get the IPFS snap. If you install that snap, then you can test it from the
point of view of the user. And if the tests work well, you can push it to the
edge channel of the Ubuntu store to start the crowdtesting with your community.

We can automate all of this with Travis. The `snapcraft.yaml` for the project
must be already in the GitHub repository, and we will add there a `.travis.yml`
file. They have
[good docs to prepare your Travis account](https://docs.travis-ci.com/).
First, let's see what's required to build the snap:

```
sudo: required
services: [docker]

script:
  - docker run -v $(pwd):$(pwd) -w $(pwd) snapcore/snapcraft sh -c "apt update && snapcraft"
```

For now, we build the snap in a docker container to keep things simple. We have
work in progress to be able to install snapcraft in Trusty as a snap, so soon
this will be even nicer running everything directly in the Travis machine.

This previous step will leave the packaged .snap file in the current directory.
So we can install it adding a few more steps to the Travis script:

```
[...]

script:
  - docker [...]
  - sudo apt install --yes snapd
  - sudo snap install *.snap --dangerous
```

And once it is installed, we can run it and check that it works as expected.
Those checks are our automated user acceptance test. IPFS has a CLI
client, so we can just run commands and verify outputs with grep. Or we can get
fancier using [shunit2](https://github.com/kward/shunit2) or
[bats](https://github.com/sstephenson/bats/). But the basic idea would be to
add to the Travis script something like this:

```
[...]

script:
  [...]
  - /snap/bin/ipfs init
  - /snap/bin/ipfs cat /ipfs/QmVLDAhCY3X9P2uRudKAryuQFPM5zqA3Yij1dY8FpGbL7T/readme | grep -z "^Hello and Welcome to IPFS!.*$"
  - [...]
```

If one of those checks fail, Travis will mark the execuition as failed and stop
our release process until we fix them. If all of them pass, then this version
is good enough to put into the store, where people can take it and run
exploratory tests to try to find problems caused by weird scenarios that we
missed in the automation. To help with that we have the
`snapcraft enable-ci travis` command, and an upcoming tutorial to guide you
step by step
[setting up the continuous delivery from Travis CI](https://docs.google.com/document/d/1vPUMH9UNOP8AqjhcslMZ5LQHMV3xvMYYKb7slC4s_Gc/edit?usp=sharing).

For the IPFS snap we had for a long time a manual smoke suite, that our amazing
[community of testers](https://forum.snapcraft.io/t/call-for-testing-ipfs/97)
have been executing over and over again, every time we want to publish a new
release. I've turned it into
[a simple bash script](https://github.com/elopio/ipfs-snap/blob/master/tests/smoke_test.sh)
that from now on will be executed frequently by Travis, and will tell us if
there's something wrong before anybody gives it a try manually. With this our
community of testers will have more time to run new and interesting scenarios,
trying to break the application in clever ways, instead of running the same
repetitive steps many times.

Thanks to Travis and snapcraft we no longer have to worry about a big part of
or release process. Continuous integration and delivery can be fully automated,
and we will have to take a look only when something breaks.

As for IPFS, it will keep being my guinea pig to guide new features for
snapcraft and showcase them when ready. It has many more commands that
have to be added to the automated test suite, and it also has a web UI and
an HTTP API. Lots of things to play with! If you would like to help, and
on the way learn about snaps, automation and the decentralized web, please
let me know. You can take a look on my
[IPFS snap repo](https://github.com/elopio/ipfs-snap) for more details about
testing snaps in Travis, and other tricks for the build and deployment.

[![screenshot of the IPFS smoke test running in travis](https://archive.org/download/elopio-screenshots2/travis/ipsf-travis.png)](https://archive.org/download/elopio-screenshots2/travis/ipsf-travis.png)
