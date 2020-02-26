title: Version History
ordering: 95

[Go here for details about the current version!](upgrade)

## Obsolete Versions

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
- NOTE: This is the final version to support Mk1 hardware.

## Version 3.0.5 - Nov 25, 2019

- Address explorer can show QR code for any address (Mk3 only). Press 4 to view. Once
  shown, press 1 to invert image, and 5/8 for next address. Successful scanning requires
  the best phone camera, and some patience, due to limited screen size.
- Export a command file for Bitcoin Core to create an air-gapped, watch-only wallet.
  Requires v0.18 or higher of Bitcoin Core.
  [docs/bitcoin-core-usage.md](https://github.com/Coldcard/firmware/blob/master/docs/bitcoin-core-usage.md) has been updated.
  Thanks to [@Sjors](https://github.com/Sjors) for creating this new feature!
- Paper Wallets! Creates random private key (or use your own dice for entropy), 
  completely unrelated to your seed words, and
  saves deposit address and private key (WIF format) into a text file on MicroSD. If you
  have a Mk3, it will also add a QR code inside the text file, and if you provide a 
  special PDF-like template file (example in [paperwallet.pdf](https://github.com/Coldcard/firmware/raw/master/docs/paperwallet.pdf)) then it will superimpose
  the QR codes into the template, and save the resulting ready-to-print PDF to MicroSD.
  CAUTION: Paper wallets carry MANY RISKS and should only be used for SMALL AMOUNTS.
- Adds a "Format Card" command for erasing MicroSD contents and reformatting (FAT32).
- Bugfix: Idle-timeout setting should only take effect after the login countdown.
  Thanks to [@aoeui21](https://twitter.com/aoeui21) for reporting this.


## Version 3.0.3 - Nov 6, 2019

- Add "Login Countdown" feature: once enabled, you must enter you PIN correctly,
  and then wait out a forced delay (of minutes/hours/days) while a count down
  is shown on-screen. Then enter your PIN correctly, a second time, to get in. You must
  provide continuous power to the Coldcard during this entire period!
  Go to Settings > "Login Countdown" for the time intervals to pick from. Thanks
  to [@JurrienSaelens](https://twitter.com/jurriensaelens) for this feature suggestion.
- Nickname feature: Enter a short text name for your personal Coldcard. It's displayed
  at startup time before PIN is entered. Try it out in Settings > "Set Nickname"
- Bugfix: Adding a second signature (multisig) onto a PSBT already signed by
  a different Coldcard could fail with "psbt.py:351" error.


### Version 3.0.2 - Nov 1, 2019

- New command in Danger Zone menu to view the seed words on-screen, so you can make
  another on-paper backup as needed.
- Robustness: Analyse paths used for change outputs and show a warning if they
  are not similar in structure to the inputs of that same transaction.
  These are imperfect heuristics and if you receive a false positive, or are doing
  weird things that don't suit the rules below, please send an example PSBT to
  support and we'll see if we can handle it better:
    - same derivation path length
    - shared pattern of hardened/not path components
    - 2nd-last position is one or zero (change/not change convention)
    - last position within 200 units of highest value observed on inputs
- Robustness: Improve checking on key path derivations when we encounter them as text.
    - accept 10h and 10p as if they are 10' (alternative syntax)
    - define a max depth (12) for all derivations
    - thanks to @TheCharlatan
- Security Improvement: during secure logout, wipe entire contents of serial flash,
  which might contain PSBT, signed or unsigned (for more privacy, deniability)

### Version 3.0.1 - Oct 10, 2019

- MARK3 SUPPORT!
    - Adds support for Mark 3 hardware: larger CPU and better secure element (608)
    - Many invisible changes inside the secure element (ATECC608A).
    - Mark3 will brick itself after 13 incorrect PIN codes, so lots of warning are shown.
- Contains all the features of 2.1.6 and still works on Mk1 and Mk2 hardware
- Visual changes to login process (rounded boxes, different prompts, more warnings)
- New USB command to report if Bitcoin versus Testnet setting is in effect.


### Major changes in v2.x.x:

- NEW for 2.1.6: "Address Explorer": view receive addresses on the screen of the
  Coldcard, so you can be certain your funds are going to the right place. Can
  also write first 250 addresses onto the SDCard in a simple text (CSV) format.
  Special thanks go to [@hodlwave](https://github.com/hodlwave) for creating this feature.
- Major release with [Multisig support](multisig)!
    - New menu under: Settings > Multisig Wallets
    - Lists all imported M-of-N wallets already setup
    - Export, import for air-gapped creation
    - Related settings and more
- Broad change: extended public key finger (XFP) values used to be shown in the
  wrong endian (byte swapped), and prefixed with `0x` to indicate they were a number.
  In fact, they are a byte string and should be shown in network order. Everywhere
  you might be used to seeing your XFP value has been switched, so `0x0f056943`
  becomes `4369050F` (all caps, no `0x` prefix). Affected areas include:
    - BIP39 password confirmation screen
    - Advanced > View Identity screen
    - Electrum skeleton wallet export (label of wallet)
    - Dump public data file (text in file header)
    - `xfp` command in ckcc CLI helper (can show opposite endian, if needed)
- New feature: Create seed words from D6 dice rolls (v2.1.1):
    - under "Import Existing > Dice Rolls"
    - just keep pressing 1 - 6 as you roll. At least 99 rolls are required for 256-bit security
    - seed is sha256(over all rolls, as ascii string)
    - normal seed words are shown so you can write those down instead of the rolls
    - can also "mix in" dice rolls: after Coldcard picks the seed words and shows them,
      press 4 and you can then do some dice rolls (as many or as few as desired) and get a
      new set of words, which adds those rolls as additional entropy.
- Export skeleton wallets for Wasabi Wallet <https://wasabiwallet.io/> to support air-gapped use.
- Summary file (public.txt) has been reworked to include more XPUB values and a warning about
  using addresses your blockchain-monitoring wallet might not be ready for.
- When BIP39 passphrase is given over USB, and approved, the new XFP is shown
  on-screen for reference.
- Use with Electrum will require
  [our updated plugin changes.](https://github.com/spesmilo/electrum/pull/5440)


Changes in version 2.1.6:

- NEW: "Address Explorer" feature (see above)
- Bugfix: Improve error message shown when depth of XPUB of multisig cosigner conflicts with path
  details provided in PSBT or USB 'show address' command.
- Bugfix: When we don't know derivation paths for a multisig wallet, or when all do not share
  a common path-prefix, don't show anything.


Changes in version 2.1.5:

- Bugfix: Changes to redeem vs. witness script content in PSBTs. Affects multisig change outputs,
  primarily.
- Bugfix: Import of multisig wallet from xpubs in PSBT could fail if attempted from SD Card.
- Bugfix: Improved message shown if import of multsig wallet was refused during PSBT signing.

Changes in version 2.1.4:

- Bugfix: For multisig change outputs, many cases were incorrected flagged as fraudulent.

Changes in version 2.1.3:

- Visual change: unknown components of multsig co-signer derivation paths used to be
  shown as `m/?/?/0/1` but will now be shown as `m/_/_/0/1`. The blank indicates better
  that we can't prove what is in that spot, not that we don't know what value is claimed.
- Bugfix: Some backup files would hit an error during restore (random, less than 6%). Those
  existing backup files will be read correctly by this new version of firmware.
- Bugfix: P2SH-P2WPKH change outputs incorrectly flagged as fraudulent (regression from v1.1.0)
- Bugfix: Wanted redeem script, but should be witness script for P2WSH change outputs.

Changes in version 2.1.2:

- Add extra warning screen added about forgetting your PIN.
- Remove warning screen about Testnet vs Mainnet.
- Bugfix: Change for XFP endian display introduced in 2.0.0 didn't actually correct
  endian display and it was still showing values in LE32. Correctly corrected now.
    - now showing both values in "Advanced > View Identity screen".
    - some matching changes to ckcc-protocol (CLI tool)
    - when making multisig wallets in airgap mode, you must use latest firmware on all the units
- Bugfix: Error messages would sometimes disappear off the screen quickly. Now they stay up
  until OK pressed. Text of certain messages also improved.
- Bugfix: Show a nicer message when given a PSBT with corrupted UTXO values.
- Bugfix: Block access to multisig menu when no seed phrase yet defined.
- Bugfix: Any command on multisig menu that used the MicroSD card would crash, if
  card was not present.
- Bugfix: When offline multisig signing sometimes tried to finalize PSBT, but we can't.
- Bugfix: For multi-pass-multisig signing, handle filenames better (end in -part, not -signed).


### Versions v2.0.3 &ndash; 2.0.4

[2019-05-13T1631-v2.0.4-coldcard.dfu](https://github.com/Coldcard/firmware/raw/master/releases/2019-05-13T1631-v2.0.4-coldcard.dfu) built May 13, 2019.

- Transaction signing speed improved by about 3X.
- Will warn if miner's fee is over 5% of txn amount (was 1% before). Hard limit remains 10% (configurable, can be disabled completely).
- Robustness: Tighten stack-depth checking, increase heap size, shuffle some memory.
- Bugfix: Transactions with more than 10 outputs were not summarized correctly.
- Bugfix: Consolidating transactions that move UTXO within same wallet are shown better.
- Bugfix: Better recovery from too-complex transaction errors.
- "Don't forget your PIN" warning message is more bold now.
- (in 2.0.4) Bugfix: Clearing duress PIN would lead to a error screen.
- (in 2.0.4) Bugfix: Advanced > "Lock Down Seed" command didn't work correctly.
- (in 2.0.4) Bugfix: Importing seed words manually didn't work on second try (thanks @duck1123)

### Versions 2.0.0 &ndash; 2.0.2

- BIP39 Passphrase support: enter up to 100 characters to create
    new wallets  from your existing seed words. Each is a completely
    independant wallet to Electrum and PSBT files, so please make note
    of the extended master fingerprint (eight hex digits).
- Support for Mark2 hardware, with membrane keypad replacing touch interface.
- Adds activity light during MicroSD card read/write (Mk2 only)
- New command: "Lock down seed" which converts BIP39 seed words and passphrase into the
    master xprv and saves that as new wallet secret. Locks in the passphrase, deletes seed words.
- New bootrom, version 1.2.1 with Mk2 hardware support and improved one-wire bus MitM defences.
- Bugfix: extra keypress occurs during certain interactions involving key repeat.
- (in 2.0.1) bugfix: underscore/space indicator shown on Settings > Idle Timeout menu
- (in 2.0.2) Page up/down on long text displays with 7/9 keys
- (in 2.0.2) Public summary file now includes extended master key fingerprint near top of file.
- (in 2.0.2) Bugfix: signing larger transactions could fail due to lack of memory


### Version 1.1.0

[2018-11-26T1403-v1.1.0-coldcard.dfu](https://github.com/Coldcard/firmware/raw/master/releases/2018-11-26T1403-v1.1.0-coldcard.dfu) built Nov 26, 2018.

#### Release Notes 

- Allow setting max network fee to a number of possible levels, or disable it (was
  previously fixed to 10%). Thanks to @crwatkins for this suggestion.

- Touch improvements: two new setting, which are between the old 'Least Sensitive'
  and 'Most Sensitive' settings. New menu text.

- Touch sensitivity preference is applied before login, so PIN entry is easier.

- Although we do not use the `bech32_decode()` function recently found to have
  an buffer overflow bug, we've included the fix into our fork of the affected
  library. This change, and the original bug, does not affect the Coldcard firmware
  in any way.

- Correctly include witness data in transactions when signing based on witness
  UTXO data (thanks to @SomberNight)

- Bugfix: Fix divide-by-zero if transaction sends zero amount out (only possible if 
  network fee equals 100% of inputs).


### Version 1.0.2

[2018-09-11T1428-v1.0.2-coldcard.dfu](https://github.com/Coldcard/firmware/raw/master/releases/2018-09-11T1428-v1.0.2-coldcard.dfu) built Sep 11th, 2018.

#### Release Notes
- Add support for [SLIP-132](https://github.com/satoshilabs/slips/blob/master/slip-0132.md)
    - yprv/zprv keys can now be imported
    - public.txt file includes both SLIP-132 and BIP-32 values where needed (segwit cases)
    - test cases added to match
- Can create Electrum skeleton wallet for Segwit Native and Segwit P2SH now.
    - caveat: the plugin is not ready yet for P2SH/Segwit, but Segwit native is fine
- Improvements in 'public.txt' output:
    - add SLIP-132 values where we can
    - correct names when used for Litecoin
- Improvements to backup and restore
    - can now restore cleartext backups (for devs only!)
    - fix "Unable to open ... /sd/backup.7z" error


Older releases, the source code, and much more be found
in [our repository on github](https://github.com/Coldcard/firmware/tree/master/releases).

