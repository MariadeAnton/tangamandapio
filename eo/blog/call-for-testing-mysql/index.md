I promised that more interesting things were going to be available soon for
testing in Ubuntu. There's plenty coming, but today here is one of the
greatest:

    $ sudo snap install mysql --channel=8.0/beta

[![screenshot of mysql snap running](https://archive.org/download/elopio-screenshots2/Screenshot_ubuntu-xenial-20170228_2017-02-28_093217.png)](https://archive.org/download/elopio-screenshots2/Screenshot_ubuntu-xenial-20170228_2017-02-28_093217.png)

Lars Tangvald and other people at MySQL have been working on this snap for some
time, and now they are ready to give it to the community for crowd testing. If
you have some minutes, please give them a hand.

We have a
[testing guide](https://gist.github.com/elopio/c868bc68bf8640b0b49b39aeb1fae8f5)
to help you getting started.

Remember that this should run in trusty, xenial, yakkety, zesty and in all
flavours of Ubuntu. It would be great to get a diverse pool of platforms and
test it everywhere.

In here we are introducing a new concept:
[tracks](https://snapcraft.io/docs/reference/channels). Notice that we are
using `--channel=8.0/beta`, instead of only `--beta` as we used to do before.
That's because mysql has two different major versions currently active. In order
to try the other one:

    $ sudo snap install mysql --channel=5.7/beta

Please report back your results. Any kind of feedback will be highly appreciated,
and if you have doubts or need a hand to get started, I'm hanging around in
[Rocket Chat](https://rocket.ubuntu.com/channel/community).
