title: Command Line Tools
ordering: 90

_Recommended for Advanced Users Only_

## Installation

To install the `ckcc-protocol` package from PyPI, use this command:

```
% pip install "ckcc-protocol[cli]"
```

This should install a new command: `ckcc`

## See Also

- [PyPI page](https://pypi.org/project/ckcc-protocol/)
- [Github project](https://github.com/Coldcard/ckcc-protocol)


## CLI Usage

The `ckcc` command has many subcommand and options. Please use the build-in help
to find your way.

check

```
% ckcc
Usage: ckcc [OPTIONS] COMMAND [ARGS]...

Options:
  -s, --serial HEX  Operate on specific unit (default: first found)
  -x, --simulator   Connect to the simulator via Unix socket
  --help            Show this message and exit.

Commands:
  addr     Show the human version of an address
  backup   Creates 7z encrypted backup file after...
  bag      Factory: set or read bag number -- single use...
  debug    Start interactive (local) debug session
  eval     Simulator only: eval a python statement
  exec     Simulator only: exec a python script
  list     List all attached Coldcard devices
  logout   Securely logout of device (will require...
  msg      Sign a short text message
  pass     Provide a BIP39 passphrase
  reboot   Reboot coldcard, force relogin and start over
  sign     Approve a spending transaction (by signing it...
  test     Test USB connection (debug/dev)
  upgrade  Send firmware file (.dfu) and trigger upgrade...
  upload   Send file to Coldcard (PSBT transaction or...
  version  Get the version of the firmware installed
  xfp      Get the fingerprint for this wallet (master...
  xpub     Get the XPUB for this wallet (master level,...
```

Some useful commands follow.

#### Firmware Upgrade

```
% ckcc upgrade --help
Usage: ckcc upgrade [OPTIONS] FIRMWARE.dfu

  Send firmware file (.dfu) and trigger upgrade process

Options:
  -s, --stop-early  Stop just before reboot
  --help            Show this message and exit.

% ckcc upgrade firmware-signed.dfu
675328 bytes (start @ 293) to send from 'firmware-signed.dfu'
Uploading  [###---------------------------------]    8%  0d 00:01:18
```

#### Sign a text message

```
% ckcc msg --help
Usage: ckcc msg [OPTIONS] MESSAGE

  Sign a short text message

Options:
  -p, --path TEXT  Derivation for key to use
  -v, --verbose    Include fancy ascii armour
  -j, --just-sig   Just the signature itself, nothing more
  --help           Show this message and exit.

% ckcc msg "Hello world"
Hello world                       
mp2SHbLDr5hg4tvKhaZGKzkf3GmzAuQCz1
H3wh0RIKoIcyRShkbMm5SbVoKaeMYlf4up7dvVfvP1c1N5FEXoBcjiiTNxR88ybpW3XlujHGhyCvy2/hnMtSE+c=
```

#### Sign a Spending Transaction

```
% ckcc sign --help
Usage: ckcc sign [OPTIONS] PSBT_IN PSBT_OUT

  Approve a spending transaction (by signing it on Coldcard)

Options:
  -v, --verbose   Show more details
  -f, --finalize  Show final signed transaction, ready for transmission
  --help          Show this message and exit.
```

## Simulator Use

This program can also speak to the Coldcard Simulator, if it's running on the same computer.
Use the global `-x` flag, to connect to the simulator instead of the first Coldcard:

    ckcc -x xpub


## Python Library

The `ckcc-protocol` package includes the python library for USB communication with the
Coldcard. It can also be used with the simulator.


## Linux udev support file.

See [51-coinkite.rules](https://github.com/Coldcard/ckcc-protocol/blob/master/51-coinkite.rules)


