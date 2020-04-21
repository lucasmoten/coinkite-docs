title: Upgrade Firmware

<em><a href="#upgradenow" >Learn how to upgrade here ></a></em>

## Current Version of Coldcard Firmware &mdash; Version 3.1.2

[2020-02-27T1355-v3.1.2-coldcard.dfu](https://github.com/Coldcard/firmware/raw/master/releases/2020-02-27T1355-v3.1.2-coldcard.dfu) released Feb 27, 2020.

_**NOTE**: Releases 3.1.0 and later are NOT COMPATIBLE with Mk1 hardware. They will brick Mk1 Coldcards._

## Version 3.1.2 - Feb 27, 2020

- Enhancement: New setting to enable a scrambled numeric keypad during PIN login.
- Enhancement: Press 4 when viewing a payment address (triggered by USB command) to
  see the QR code on-screen (Mk3 only).
- Enhancement: Can enter non-zero account numbers when exporting wallet files for Electrum
  and Bitcoin Core. This makes importing seeds from other systems easier and safer.
- Enhancement: Dims the display when entering HSM Mode.
- Bugfix: Trust PSBT setting (for multisig wallets) was being ignored. Thanks to @CasaHODL
  for reporting this.
- Bugfix: XPUB values volunteered in the global section of a PSBT for single-signer files would
  cause errors (but ok in multisig). Coldcard will now handle this, although it doesn't need them.
- Bugfix: 3.1.1 had a bug which broke the new "non-zero account export" feature.

## Version 3.1.0 - Feb 20, 2020

- HSM (Hardware Security Module) mode: give Coldcard spending rules, including whitelisted
  addresses, velocity limits, subsets of authorizing users ... and Coldcard can sign with
  no human present. Requires companion software to setup (ckbunker or ckcc-protocol),
  and disabled by default, with multi-step on-screen confirmation required to enable. Mk3 only.
- Enhancement: New "user management" menu. Advanced > User Management shows a menu
  with usernames, some details and a 'delete user' command. USB commands must be used to
  create user accounts and they are only used to authenticate txn approvals in HSM mode.
- Enhancement: PSBT transaction can be "visualized" over USB, meaning you can view what
  the Coldcard will show on the screen during approval process, as text, downloaded over USB.
  That text can be signed (always with root key) to prove authenticity.
- Enhancement: Sending large PSBT files, and firmware upgrades over USB should be a little faster.
- **IMPORTANT**: This release is NOT COMPATIBLE with Mk1 hardware. It will brick Mk1 Coldcards.

Older releases and their changes [are listed here](version-history),
the full source code, hardware details, and much more can be found
in [our repository on github](https://github.com/Coldcard/firmware/tree/master/releases).

## Mark 1 Hardware (late 2017 / early 2018)

The Mk1 hardware is obsolete and no further updates will be made. The final
version of firmware for the Mk1 is
[3.0.6 (2019-12-19T1623-v3.0.6)](https://github.com/Coldcard/firmware/raw/master/releases/2019-12-19T1623-v3.0.6-coldcard.dfu). Do not load any newer firmware version,
as it will brick the device.

---

# How To Upgrade { #upgradenow }

## Upgrading Step By Step

1. Download and verify the [latest firmware release](https://github.com/Coldcard/firmware/raw/master/releases).
2. Save the `20...-coldcard.dfu` firmware file onto a SD card.
4. Power up your ColdCard and unlock it with your PIN.
5. Go to the `Advanced > Upgrade` menu and click on `From SD Card`. 
6. After the confirmation dialog, ColdCard will upgrade and reboot (slow).
7. Type in your PIN again. Verify new version running with:<br>
   `Advanced > Upgrade > Show Version`
8. If you powered down during this process, to get a green light again,
   you may need to use: `Bless Firmware` in that menu.


## Advanced: Verify Your Downloads

The release binaries may be verified using
[this clear-signed text file](https://raw.githubusercontent.com/Coldcard/firmware/master/releases/signatures.txt)
and GPG. The commands are:

    curl https://keyserver.ubuntu.com/pks/lookup?op=get&search=0xA3A31BAD5A2A5B10 | gpg --import
    gpg --verify signatures.txt

The first command imports the public key [`4589779ADFC14F3327534EA8A3A31BAD5A2A5B10`](https://keyserver.ubuntu.com/pks/lookup?op=get&search=0xA3A31BAD5A2A5B10) and the second verifies the file's
signature vs. file contents.

Don't forget to run SHA256 over the DFU files themselves, because that compares
the actual file contents to what we have signed.

    sha256sum 2019-12-19T1623-v3.0.6-coldcard.dfu

Github.com is also protecting us because it verifies on all commits
against the developer's public keys, and keeps a history of changes.


---

## Background

The upgrade menu allows you to load updated firmware onto the Coldcard.

![advanced menu](img/snap-upgrade.png){.snap .indented}

The menu allows loading an upgrade file from a MicroSD card, but it can 
also be done using the [command line tool](cli), or from the Electrum plugin.

## How to Upgrade

Show Version
: Displays the version numbers that you have already.

From MicroSD
: Select an upgrade file from MicroSD card and start the process.

Bless Firmware
: Mark the contents of flash memory as "approved" and light the green "Genuine" light.

## Upgrade Files

You need a `DFU` file for upgrades. It's about 690k in size and should have the
extension `.dfu`.

The latest firmware will always be available in Github:

[github.com/Coldcard/.../releases]({{RELEASES}}){.indented}

All upgrade files must be signed by a Coinkite Inc. approved key, or
the Coldcard will refuse to load and run them.

## Bless Firmware

This command is not typically needed, but can be used to set the
genuine/caution lights to green. Note that only the main PIN holder
can do this. A normal firmware upgrade sequence does not require
this action, but if the unit is powered down between installing the
upgrade and the first successful login, then the light will be red,
and will stay red until this command is used.

## Downgrade Protection

In general, it may not be advisable to downgrade (return to an older
release). Some releases will set a "high water mark" so the bootloader
that will block any downgrade to earlier versions. We will do this
if a bug or security problem with an obsolete release is identifed.

## Need extra help?

Watch this [Video: Secure Upgrade Firmware of ColdCard Mark 2 - Max Hillebrand](https://www.youtube.com/watch?v=JCZzugnfQPs)

