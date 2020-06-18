title: Advanced Menu
ordering: 80

## Background

The advanced menu contains a number of useful features that are not required daily.

![advanced menu](img/snap-adv-menu.gif){.snap .indented}


## Advanced Functions

View Identity
: Display a few public details about your wallet, such as the XPUB and the fingerprint.

[Upgrade](upgrade)
: Upgrade the firmware on the Coldcard, using MicroSD. Also shows version numbers.

[Backup](backups)
: Save your wallet seed and settings into an encrypted MicroSD file.

[MicroSD Card](microsd)
: Manage files on the MicroSD card, and other functions related to the card.

[Paper Wallets](paper-wallets)
: Create a paper Bitcoin wallet.

Address Explorer
: View payment addresses, and optionally save them to a MicroSD card.

[Derive Entropy](bip85)
: Create and export deterministic entropy for other wallet systems (BIP-85).

Danger Zone
: Developer-only features and things you shouldn't do.


## "Danger Zone" Menu

The advanced menu contains a sub menu labeled the "Danger Zone":

![advanced menu](img/dangerzone.png){.snap .indented}

These are rarely-used commands that have the potential to affect your Bitcoin holdings.
Use them with caution.

Debug Functions
: Test some error cases, such as exceptions. Mostly harmless.

Lock Down Seed
: [Convert BIP39 seed phrase and password to a BIP32 wallet.](passphrase#related-feature-lock-down-seed)

View Seed Words
: Shows warning screen, and then displays the 24 seed words on the
  Coldcard screen. If defined, the [BIP39 passphrase](passphrase) is also shown.

Destroy Seed
: Forget the seed words, and all hope of recovering your funds! There is a confirmation screen.

I Am Developer
: Enable various debuging modes and features for MicroPython programmers and experimentors.

Wipe Patch Area
: Wipe and rebuild a small internal filesystem that can be used to store extra code/features. Harmless.

Perform Seltest
: Starts the factory self-test, which will clear the settings.

Set High-Water
: Records a new minimum version number for future upgrades and prevents downgrades below current version.


