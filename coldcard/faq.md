title: Coldcard FAQ
ordering: 98


## How many seed words does it use?

Coldcard always generates 24-word BIP39 seeds. It can also
import 12, 18 and 24-word, BIP39 seeds that other
wallets may have created.

## Can I have multiple wallets in each Coldcard?

There is a single "wallet", derived from the BIP39 seed words. In
addition, we have an optional "duress" wallet, which is derived
from the wallet's seed words and is not independent. This means
it gets backed-up automatically, and the original seed words also
backup the duress wallet.

By adding a [BIP39 passphrase](passphrase) you can unlock
nearly unlimited additional wallets which derive from the original
24 seed words. The passphrase you use defines the wallet and cannot
be changed. BIP39 passphrases are not backed up or otherwise tracked,
which gives lots of freedom in terms of plausible deniability.

## Can I change my PIN?

Yes, the PIN is independent of the funds being held. It can be changed
at any time as long as you have the original PIN.

BIP39 passphrases cannot be changed because the text of the passphrase
is part of the private key.

## Which blockchains do you support?

Bitcoin and Bitcoin Testnet are supported. Coldcard does not support altcoins.


## Why does it have a MicroSD slot?

- The Coldcard can backup the seed into an encrypted file.
- New transactions to be signed can be imported from the card.
- Public key data (xpub, payment addresses) can be written onto the card.
- Firmware upgrades can be done by copying the new firmware file onto a card.
- A skeleton Electrum wallet can be created on the card which allows
  Electrum to "pair" with the Coldcard, without it ever connecting to a USB port.
- Multisig wallets can be joined using files transfered via cards.


## How do I connect to a computer?

Use the USB port at the top of the Coldcard. You must provide a standard
MicroUSB cable suitable for your computer. 

Coldcard does not enable the USB port until a correct PIN code is
entered so it will not appear on your computer until the PIN is entered.

There is no need to use the USB port (except for power) during seed
setup and when using the MicroSD card slot itself. We use the
Coldcard with USB battery packs routinely, although some battery
packs do not correctly detect the Coldcard because it uses very
little power. They may power down because it appears that nothing
is connected. Most simple battery packs and wall chargers are fine.

## Do I need to use MicroSD cards?

You don't have to use MicroSD cards with Coldcard. It works fine
over a USB connection. You can also switch later if your security
needs change.

## What is PSBT?

PSBT is an emerging standard for "Partially Signed Bitcoin Transactions"
and is described by
[BIP 174](https://github.com/bitcoin/bips/blob/master/bip-0174.mediawiki).

Coldcard is the first "PSBT Native" hardware wallet. It uses PSBT
internally, and should be able to sign most PSBT files generated
by conforming software. For completed transactions, we can output
either a PSBT (with the new signatures added) or a finalized Bitcoin
transaction, ready to send.

Bitcoin Core has recently added [HWI](https://github.com/bitcoin-core/HWI) which
supports uploading unsigned PSBT files, and receiving signed PSBT files back from
the Coldcard. All the features of the Coldcard, including message signing
and showing of addresses are already supported in HWI. This is a great way
to use your Coldcard from the CLI over USB connection. 

## How do I backup?

Insert a MicroSD card, and go to Advanced > Backups > Backup System.

You'll be shown a 12-word password to be recorded, and have to pass
a short quiz to prove you did that.

Then the file is saved as an [AES-encrypted 7Z file](https://en.wikipedia.org/wiki/7z)
on the MicroSD card.

We suggest keeping the password and file in different locations.
The backup file is useless without the 12-word passphrase.  Each
backup will have a different backup phrase, and it has no relationship
with the wallet seed words.

Backups can also be verified (checked for completeness) from the menu system.

[Read more in our docs about backups.](/docs/backups)


## Can Coldcard import an **encrypted** BIP39 seed phrase?

Yes, Coldcard [supports BIP39 passphrases](docs/passphrase).

This unlocks approximately 5.9e197 more wallets based on your seed phrase.

## Is there a factory reset?

There isn't a factory reset due to the secure element. Most of the
fields in that chip cannot be quickly reset. However, you can clear the
wallet seeds and remove secondary PIN code individually. It's a lot of
typing and all corresponding PIN codes must be already known to you.

## How do I know desktop software is showing a payment address that truly is a deposit into this Coldcard?

Coldcard can display the payment address after it has independently
calculated what it should be. Without this, it would be hard to
make a "deposit" into the wallet of the Coldcard without the
possibility of someone misleading you.

In Electrum, click on the "eye" icon shown near the payment address.
Check the value shown on the Coldcard screen, compared to the value
Electrum is showing.

This 'show address' feature is typically used online, with the
Coldcard connected on USB. To achieve a similar result off-line,
proceed as follows: choose: Advanced > Address Explorer, and follow
the instructions.

You can view ten addresses on the screen at at a time (press 9 to
see more), and also write out a CSV file with the first 250 addresses,
onto the MicroSD card.

## I found a previously-used Coldcard online, should I buy it?

You should never buy a "used" Coldcard from EBay or another online
store. A new Coldcard from the factory would arrive sealed in a
special tamper-evident bag. That's an important security feature
since it's possible to change the firmware on the Coldcard. It's
impossible to trust what you're receiving from the second-hand vendor.

All legitimate resellers should be providing the Coldcard unused and
still in it's original tamper-evident bag. As part of the first-use
sequence, you will verify the bag number matches the factory bag number.

## This random MicroSD card doesn't work?

There are so many MicroSD cards out there, it's not possible for
us to test with them all. We have tested with all the cards we can
find locally, and a few ultra-cheap ones from Aliexpress. Still there
will be some that won't work. If it's formatted as FAT32 and works
on your computer, it should work. Please try another brand of 
card and if that fails, try one of our
[SLC cards, available in our store.](https://store.coinkite.com/store/microsd-cc)


## Do you support Segwit (Segregated Witness) on the Coldcard?

Yes. We have comprehensive [segwit support](https://en.wikipedia.org/wiki/SegWit),
and strongly recommend it, but do not require it.
We will display Bech32 and P2SH (segwit wrapped) addresses appropriately.

The limiting factor is usually the wallet software generating the
PSBT files for Coldcard to sign, and the BIP32 key derivation paths involved.

For the Electrum wallet, we generate a PSBT file which will result
in Coldcard producing a segwit transaction every time (this does
not relate to use of Bech32 or P2SH addresses, just the transaction's
signatures).

Segwit is preferred since the cryptographic signature will cover
exactly the payment details that the user has previewed on the
Coldcard screen.

In order to (safely) produce a non-segwit transaction, the Coldcard
must be provided enough data in the PSBT to completely verify the
inputs and since a full copy of the transaction for all UTXO inputs
is needed, the result is a much larger PSBT file. Coldcard will
refuse to sign a PSBT file where it does not have complete information
on all inputs.

At some point in the future, we may even block non-segwit signatures
on the Coldcard, or make it disabled by default.


## Is the secure element's crypto used for Bitcoin processing?

Although the ATECC608A, and the 508A used on older versions, do
implement standard SHA-256, HMAC(SHA-256) and AES, we use those
implementations only to secure the secrets that the chip holds.

Bitcoin signatures, and all other Bitcoin-specific operations are
completed with the open-source software found
in our [open-source code](https://github.com/Coldcard/firmware).


## What kind of secure elements is used? 

The ATECC608A is a fixed-function device for private key storage.
It is not a general purpose CPU like some other secure elements. As a result, neither
Coinkite nor the chip's manufacturer can change how it works without
revising the hardware of the chip itself. It is in effect a flash ROM
(read only memory) with about 10k bits of storage. All access and updates are
predefined by the hardware and its design.
The complete Coldcard [firmware can be seen here](https://github.com/Coldcard/firmware)
and we have a [detailed white paper](https://raw.githubusercontent.com/Coldcard/firmware/master/docs/pin-entry.md)
specifically about this Secure Element, and how we use it.

## When does the PIN attempt counter reset? 

As soon as you enter the correct PIN code, the login attempt counter is
reset to zero. This means you'll still have a full 13 attempts next time.


## What happens when I can't remember my PIN?

When you've failed 3 times or more, we warn you that you are in
danger of bricking the device. The message encourages you to
double-check the PIN entered, and even gives you a peek at what you
entered, _before_ submitting it as an login attempt.

Please note that Mk3 systems will brick themselves after 13 failed
login attempts. **There is no way to reset or recover the device.**

Mk2 and earlier Coldcards will allow infinite attempts, but make
it slower and slower each time, until at one point, you have to
wait hours between each attempt.


## Where do I learn all technical details?

You can read our
secure element [white paper](https://raw.githubusercontent.com/Coldcard/firmware/master/docs/pin-entry.md),
our [online docs.](/docs) and even
the [Coldcard source code](https://github.com/Coldcard/firmware).


