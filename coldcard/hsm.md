title: HSM Mode Features
order: 89

[_(new in v3.1.0, requires Mk3)_](upgrade)

# HSM - Hardware Security Module


"HSM Mode" is the name we use when speaking about the Coldcard signing
transactions without a person physically present. It's an advanced
feature which requires additional software to setup and enable.

HSM mode can be enabled after a "policy file" has been uploaded and
confirmed by the user. That policy file establishes what the Coldcard
will and won't do in HSM mode, and what further authorization steps
may be needed for each spending operation.

Once the Coldcard has gone into "HSM Mode", it must be powered-down
before it can operate normally again.

If a Coldcard has an HSM policy file already installed, you are
given an opportunity to enable it each time the Coldcard is powered,
just after the master PIN is entered and before the USB connection
is enabled. It is also available in the main menu, as "Start HSM Mode".

In HSM Mode, the Coldcard screen looks like this:

![hsm-mode example](img/hsm-mode.gif){.snap}

No transaction information is shown on the screen, although a
progress bar and one-word description of current activity (ie. "Signing...")
is shown along the bottom of the screen.

!!! warning "Important"

    Your funds in the Coldcard will only be as safe as your HSM
    Policy allows! We recommend a very low velocity (ie. 1BTC/day)
    and a very limited whitelist of one or two destination addresses.

    _Design your policy working from the assumption that your CKBunker may be compromised someday._

### Further Reading

1. [Local Confirmation Codes]
2. [User Management]
3. [HSM Policy Rules]
3. [CLI Usage examples]
3. [Security Notes]

## Local Confirmation Codes

The only interaction possible with a Coldcard in HSM mode is to
enter a local authorization code. This can be required by specific
HSM policy rules, and perhaps not all transactions would require
local authorization.

As you press the 6-digit numeric code, the digits are shown in the
top right part of the screen. Press OK to apply them, or X to clear
and start over. Codes are always 6 digits. There is no indication
the code worked or failed, in part because it isn't tested until
the PSBT is given for signing, which could be some time later.

![entering local code](img/hsm-local-code.png){.snap}

The required 6-digit code is a combination of the specific bytes
of the PSBT file being approved, and also a salt value picked by
the Coldcard.  If you have the PSBT file to be approved, you can
use the `ckcc local-conf` command to show the code needed:

```sh
% ckcc local-conf debug/attempt.psbt
Local authorization code is:
	160681

```

However, most will use the CKBunker, which reveals the local code as shown here:

![bunker shows local code](img/bunker-local-conf.png)

A different code will be required for each attempted signing (because
Coldcard changes the salt value) and for every PSBT file (because
the hash of the PSBT is a factor in this number).

## Coldcard User Management

To support use of the Coldcard in HSM mode, the Coldcard can hold
usernames and their shared secrets for authentication purposes.

Two methods are offered: shared password (ie. classic "something
you know") or TOTP (time-based one-time pass) 2FA authentication,
compatible with [RFC6238](https://tools.ietf.org/html/rfc6238). Most
people will already have an app on their mobile phone to hold
the shared secrets and simplify the number-calculating process.

Creating new users can **only** be done over USB protocol with the
help of CKBunker or `ckcc` programs. However, once the user is
established, you may view it and remove them from the menu system, in 
Advanced under "User Management".

The best practise is for the Coldcard to generate the password or
TOTP secret and display it on-screen in a QR code. If you are using
a TOTP app, such as Google Authenticator or FreeOTP, then you can
scan the screen of the Coldcard to install the code. Unfortunately,
due to limited screen space, there isn't room for the meta data
such as username or specific Coldcard number: your app will only
show "CC".

It is also possible to send a user-generated shared secret (or
password) over USB, in which case, the QR code is not shown. This
requires trust of the attached computer at this stage.

The CKBunker can make creating users very easy, alternatively, and
as a sample implementation for others, the [CLI program `ckcc`](cli) can 
be used.

### Creating New Users with CLI

The `user` subcommand can be used:

```sh
% ckcc user --help
Usage: ckcc user [OPTIONS] USERNAME

  Create a new user on the Coldcard for HSM policy (also delete).

  You can input a password (interactively), or one can be picked by the
  Coldcard. When possible the QR to enrol your 2FA app will be shown on the
  Coldcard screen.

Options:
  -t, --totp              Do TOTP and let Coldcard pick secret (default)
  --pass                  Use a password picked by Coldcard
  -a, --ask-pass          Define password here (interactive)
  -s, --totp-secret TEXT  BASE32 encoded secret for TOTP 2FA method (not
                          great)
  -p, --text-secret TEXT  Provide password on command line (not great)
  -d, --delete            Remove a user by name
  -q, --show-qr           Show enroll QR contents (locally)
  --hotp                  Use HOTP instead of TOTP (dev only)
  --help                  Show this message and exit.
```

For example, a TOTP 2FA account for `alice` could be created like this:

```sh
% ckcc user alice
Done
```

On the attached Coldcard, the screen will look like this:

![QR for TOTP enrol](img/alice-qr.png){.snap}

Alice just needs to scan that screen with her phone, and subject
to local shoulder surfers, only the Coldcard and her mobile phone
will know the shared secret involved.

Bob could create his account with a basic password using this command
(nothing is shown on Coldcard screen).

```sh
% ckcc user bob -p "P@ssw0rd"
Done
```

You can test your creditials with `ckcc` as well, but only if the
Coldcard is not in HSM mode. If it's in HSM mode, the same command
merely prepares the input for checking on the next signing attempt,
and isn't immediately verified.

```sh
% ckcc auth bob -p sldkfjsdlfkj
Problem: mismatch

% ckcc auth bob -p "P@ssw0rd"
Correct or queued

% ckcc auth bob -p
Password (hidden): 
Correct or queued

% ckcc auth alice 123123
Problem: mismatch

% ckcc auth alice 872532
Correct or queued

% ckcc auth --help
Usage: ckcc auth [OPTIONS] USERNAME [TOTP]

  Indicate specific user is present (for HSM).

  Username and 2FA (TOTP, 6-digits) value or password are required. To use
  password, the PSBT file in question must be provided.

Options:
  -f, --psbt-file FILENAME
  -p, --password            Prompt for password
  -d, --debug               Show values used
  --help                    Show this message and exit.
```

Obviously, you are trusting the USB-attached computer in the password
case, but that risk does not exist for the 2FA case, as each code is
useful only once. We generally recommend 2FA instead of fixed passwords.

In HSM mode, the password response includes a hash of the PSBT being
approved. Therefore, it is possible to hash up your password on a remote
machine, before submitting it over USB to the Coldcard. In that
sense, it is also a one-time password, and interception on the
USB-connected computer would not help attackers.

The new password is key-stretching using PBKDF2(SHA256) (with a salt
value derived from the serial number of the specific Coldcard involved) before
being transmitted over USB. When proving your knowledge of the password,
you must provide
`HMAC(secret=PBKDF2(password salt=SHA256(...+serial_number)), msg=SHA256(psbt_file))`

Full code for the PBKDF2 step can be found in
[hash_password() in ckcc/client.py](https://github.com/Coldcard/ckcc-protocol/blob/master/ckcc/client.py) and HMAC step in
[user_auth() in cli.py](https://github.com/Coldcard/ckcc-protocol/blob/master/ckcc/cli.py)



## Spending Rules

The HSM policy is established by a simple JSON file, which states
the rules for which spending transactions should be signed automatically
by the Coldcard. It is uploaded to the Coldcard, which renders
summary of the policy for you to approve on-screen.

The file has two parts: global settings and a number of "rules".

