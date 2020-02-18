title: User Management
order: 20
hidden: 1

[_(new in v3.1.0, requires Mk3)_](upgrade)


## User Management

To support use of the Coldcard in HSM mode, the Coldcard can hold
usernames and their shared secrets for authentication purposes. At 
present, this is only useful for use in HSM mode.

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

When authorizing a PSBT, the password response includes a hash of
the PSBT being approved. Therefore, it is possible to hash-up your
password on a remote machine, before submitting it over USB to the
Coldcard. In that sense, it is also a one-time password, and
interception on the USB-connected computer would not help attackers.

The new password is key-stretched using PBKDF2(SHA256) (with a salt
value derived from the serial number of the specific Coldcard involved) before
being transmitted over USB. When proving your knowledge of the password,
you must provide:

    HMAC(secret=PBKDF2(password, salt=SHA256(...+serial_number)), msg=SHA256(psbt_file))

Full code for the PBKDF2 step can be found in
[hash_password() in ckcc/client.py](https://github.com/Coldcard/ckcc-protocol/blob/master/ckcc/client.py) and HMAC step in
[user_auth() in cli.py](https://github.com/Coldcard/ckcc-protocol/blob/master/ckcc/cli.py)

