For the third session of the Ubuntu Testing Days,
[Kevin Gunn](https://plus.google.com/+KevinGunnCanonical) joined us to talk
about the Unity8 snap.

This is a thriving project with lots of things to do and bugs to uncover, so
it's the perfect candidate for newcomers eager to help Ubuntu.

If you are interested and didn't attend our meeting on Friday, click the image
below to watch the recording.

[![Alt text](https://img.youtube.com/vi/oGsA55RvqLI/0.jpg)](https://www.youtube.com/watch?v=oGsA55RvqLI)

To install Unity 8 in the Ubuntu 16.04
[cloned virtual machine](http://elopio.net/blog/pristine-vm/)
that we prepared on the past session, enter the following commands in the terminal:

    $ sudo add-apt-repository -u ppa:ci-train-ppa-service/stable-phone-overlay
    $ sudo apt install unity8-session-snap
    $ unity8-snap-install
    $ sudo reboot

After reboot, on the login screen click the Ubuntu icon next to your user name
and change the selection to Unity 8.

Kevin and his team have a
[google doc with the instructions to build and install the snap, and the
current status](https://docs.google.com/document/d/1o-jKITqUxRsujmN3OwRj3wRnn6dgblKuvrhKjeN8-Wc/edit#heading=h.q06bivmaid8f).
If you find a bug, you can talk to the developers joining the
[#ubuntu-touch IRC channel on freenode](http://webchat.freenode.net/?channels=#ubuntu-touch)

While preparing the testing environment on this session we had a crash, and
explained how easy it is to send the report to the Ubuntu developers by just
clicking the *Report problem...* button. The reports from all our users
are in the [error tracker](https://errors.ubuntu.com/), along with a frequency
graph and the links to bug reports where those crashes are being fixed.

We also showed two tricks to make faster and more pleasant the testing session
in a virtual machine:

 * [Set up a cache for deb packages](http://elopio.net/blog/setup-apt-cache/)
 * [Share the clipboard between the host and the guest](http://askubuntu.com/questions/858649/how-can-i-copypaste-from-the-host-to-a-kvm-guest)

Thanks to [Julia](https://twitter.com/la_juyis),
[Kyle](https://twitter.com/rainveil) and
[Kevin](https://plus.google.com/+KevinGunnCanonical) for the nice session.

Join us again next Friday at [Ubuntu On-Air](https://ubuntuonair.com).
