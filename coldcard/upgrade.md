title: Upgrade Firmware

<em><a href="#upgradehow" >Learn how to upgrade here ></a></em>

## Upgrading step-by-step

1. Download and verify the latest firmware release.
2. Save the .dfu firmware file on a SD card.
3. Plug in the SD card into your ColdCard.
4. Power up your ColdCard and unlock it with the pin.
5. Go to the `Upgrade` menu and click on `From SD Card`, after the dialog, ColdCard will upgrade and reboot.
6. Poewr up your ColdCard and type in the PIN again.
7. Go to the `Upgrade` menu and click `Bless Firmware`.

## Current Version of Coldcard Firmware &mdash; Version 3.0.6

[2019-12-19T1623-v3.0.6-coldcard.dfu](https://github.com/Coldcard/firmware/raw/master/releases/2019-12-19T1623-v3.0.6-coldcard.dfu) released Dec 19, 2019.

## Version 3.0.6 - Dec 19, 2019
            
- Security Bugfix: Fixed a multisig PSBT-tampering issue, that could allow a MitM to
  steal funds. **Please upgrade ASAP**.
- Enhancement: Sign a text file from MicroSD. Input file must have extension .TXT and
  contain a single line of text. Signing-key subpath can be provided on the second line.
- Enhancement: Now shows the change outputs of the transaction during signing
  process. This additional data can be ignored, but it is useful for those who
  wish to verify all parts of the new transaction.
- Enhancement: PSBT files on MicroSD can now be provided in base64 or hex encodings. Resulting
  (signed) PSBT will be written in same encoding as the input PSBT.
- Bugfix: crashed on entry into the Address Explorer (some users, sometimes).
- Bugfix: add blank line between addresses shown if sending to multiple destinations.
- Bugfix: multisig outputs were not checked to see if they are change (would have been
  shown as regular outputs), if the PSBT did not have XPUB data in globals section.

Older releases and their changes [are listed here](version-history),
the full source code, hardware details, and much more can be found
in [our repository on github](https://github.com/Coldcard/firmware/tree/master/releases).

---

# How To Upgrade { #upgradenow }

## Advanced: Verify Your Downloads

The release binaries may be verified using
[this clear-signed text file](https://raw.githubusercontent.com/Coldcard/firmware/master/releases/signatures.txt)
and GPG. The commands are:

    curl https://pgp.key-server.io/download/0xA3A31BAD5A2A5B10 | gpg --import
    gpg --verify signatures.txt

Please look for signing key: `[4589779ADFC14F3327534EA8A3A31BAD5A2A5B10](https://pgp.key-server.io/pks/lookup?op=get&search=0xA3A31BAD5A2A5B10)`

Don't forget to run SHA256 over the DFU files themselves, because that compares
your actual file contents to what we signed.

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

