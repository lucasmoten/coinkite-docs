title: HSM Mode Features
order: 89

# HSM - Hardware Security Module

"HSM Mode" is the name we use when speaking about the Coldcard signing
transactions without a person physically present. It's an advanced
feature which requires additional software to setup and enable.
Once the Coldcard has entered "HSM Mode", it must be powered-down
before it can operate normally again.

HSM mode is enabled after a policy file has been uploaded and
confirmed by the user. That policy file establishes what the Coldcard
will and won't do in HSM mode, and what further authorization steps
may be needed for each operation.

If a Coldcard has an HSM policy already installed, you are given
an opportunity to enable it every time the Coldcard is powered,
just after the master PIN is entered. It is also available in the
main menu, as "Start HSM Mode".

In HSM Mode, the Coldcard screen looks like this:

![hsm-mode example](img/hsm-mode.gif){.snap}

No transaction information is shown on the screen, although a
progress bar and one-word description of the activity (ie. "Signing...")
is shown at times.

## Local Confirmation Codes in HSM Mode

The only interaction possible with a Coldcard in HSM mode is to
enter a local authorization code. This can be required by
specific HSM policy rules, but it doesn't required, and perhaps
not all transactions would require local authorization.

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
	361765

```

However, most will use the CK-Bunker, which reveals the local code as shown here:

![bunker shows local code](img/bunker-local-conf.png)

A new code will be required for each attempted signing (because
Coldcard changes the salt value) and for every PSBT file (because
the hash of the PSBT is a factory in this number).
