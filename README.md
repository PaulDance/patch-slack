# `patch-slack`
## Rationale

The Slack desktop client for Linux has native support for using PipeWire to
capture and share screen contents under Wayland. However, the
`WebRTCPipeWireCapturer` option for its `--enable-features` CLI flag that
enables the feature is apparently ignored on purpose in the current version.
The following command patches the installed contents to forcefully re-enable
the properly-implemented support:

```bash
sudo sed -i'.bak' -e \
    's/,"WebRTCPipeWireCapturer"/,"LebRTCPipeWireCapturer"/' \
    /usr/lib/slack/resources/app.asar
```

taken from <https://askubuntu.com/a/1492207>.


## Contents

The current project provides a very small Debian package that enables
automatically running the above command on any DPKG installation or update that
touches something under the `/usr/lib/slack/resources` directory of the
`slack-desktop` package, using the DPKG trigger system. In itself, it does not
contain anything, however.


## Installation

 * Audit the source files under `debian/` to trust that nothing malicious will
   be attempted upon installing the package.
 * Install the official `devscripts` package in order to gain access to the
   following two build dependencies.
 * Clone the current repository anywhere you like and `cd` into it.
 * Run `debuild` in order to build the package.
 * Run `sudo debi` in order to install the just-built package.


## Usage

The installed package does not provide anything to use *per se*, as it only
performs a one-shot action from time to time. In order to check that it works,
running `grep 'WebRTCPipeWireCapturer' /usr/lib/slack/resources/app.asar`
should return a non-zero status and display nothing. Also, when (re)installing
or  updating `slack-desktop`, a line of the form `Processing triggers for
patch-slack ([version]) ...` should appear at the end of APT's output. Finally,
the Slack client should be restarted if running while the current package is
installed.

Instructions on how to ensure the CLI option is always used when starting the
client through the standard desktop API can be found at:
<https://www.guyrutenberg.com/2022/03/12/slack-screen-sharing-under-wayland/>.
You can also test things independently of Slack desktop along your way of
setting up Wayland and PipeWire using another web browser by going to
<https://mozilla.github.io/webrtc-landing/gum_test.html>.
