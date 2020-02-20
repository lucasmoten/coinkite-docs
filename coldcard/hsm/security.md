title: HSM Security Notes
ordering: 50
hidden: 1

This section documents some of our team's thinking regarding operating
the Coldcard in HSM mode and the CKBunker. These notes come from
our internal discussions while the feature was being developed.

## Human Required to Start HSM Mode

It's important that entry into HSM mode must always require a user
OK on the device screen. It would be bad if a desktop program could
program a bogus policy, and then start HSM mode and steal your funds.


## Privacy over UX

The Coldcard should not be generous and share what the current/proposed
HSM policy is. Knowing the rules allows you to stretch them... for
example, the web page, how much does it show about the policy (after
it's setup)? Or the list of usernames, is that a semi-secret, since it
identifies humans who might be pressured?

Since our model is normally that we don't trust the desktop, initial
thinking was we should only show these things on the Coldcard screen,
and maybe during setup process. However, as the CKBunker was
developed, it became obvious that this semi-private data was
pretty important, so we made this choice a setting:
[`priv_over_ux`](rules#privacy-over-ux)

## USB Commands

Some USB commands are inappropriate in HSM mode: for example,
firmware upgrade should not be possible. We use a fixed
[whitelist](protocol) to check this.


## PIN without Authority

What if: An operator has the PIN, but not the authority to spend
all the money. She reboots the Coldcard and enters PIN. Then uploads a
generous new HSM policy file that allows her to do anything. Puts
CC into HSM mode, and steals the money later. Or she simply copies
the seed words, or makes a backup, etc.

Conclusion: We assume those with the PIN have full authority over
all the funds (as we did before) but we implemented the [Boot to HSM](rules)
feature, with possibility to completely lock down operation to always
be in HSM mode.

The 30-second timeout on entering the escape code is there because
it if a local operator learns the escape code, but doesn't have the
master PIN, they shouldn't be able to escape HSM mode. A person
with knowledge of the master PIN, can safely leave the Coldcard
after unlocking it with master PIN and waiting 30 seconds.

## User Names

A policy involving user accounts that don't exist is invalid.
Therefore, deleting a user can impact HSM mode, so we don't allow
that during operation.


## SHA1 in TOTP

Why SHA1 inside our TOTP implementation? Because Google Authenticator
only supports that,
[based on this document](https://github.com/google/google-authenticator/wiki/Key-Uri-Format).

FreeOTP is better and supports SHA256, but it does not seem to be
widely used.  We also don't have space in our QR code to indicate
alternative hashing algorithms.


## Data Oversharing

- Should the XPUB USB cmd be allowed? Yes. It's just too useful.
- Should address/P2SH address commands be allowed in HSM mode? They leak privacy
  if the bunker is captured, but useful for fetching deposit addresses safely.
- So xpub+addr commands are disabled by default, but can be enabled as needed.
- Limit path used with address/p2sh commands, so you aren't sharing more than intended.

If the bunker is captured while the Coldcard is unlocked, the attacker
will learn your master xpub. We cannot disable that because the master xpub
is used to protect the USB communications privacy (MitM protection).

Therefore to stop the bad guys from finding all your transactions;
past and future, just don't use the master xpub directly, and
instead, use a derived path (like all wallets do already). 


## Brute Forcing Policy Rules

We crash the Coldcard (secure logout) if there are more than 100
refused transactions.  This is our main protection against any sort
of brute-forcing of the policy rules.


## Racing Between Users

There is a potential race condition with local user confirmation
and multiple remote users: you call in and tell the operator to do
code 378555 (which is the next code) but before you can upload your
PSBT, your evil co-worker uploads his PSBT which uses that local
confirmation code first. Your PSBT fails (wrong code) and evil
coworker has avoided the local-confirmation check.

To address this, the [local confirmation code](local-codes) is a
function of the PSBT being approved and a random value (seed/salt)
picked by the Coldcard. The PIN is effectively: 
`BE32(31 LSB) of HMAC(msg=psbt hash, key=bytes from CC, sha256) mod 1000000`


## Signing Reveals Pubkeys

`share_addrs` is effectively a proper superset of `msg_paths` because
whenever you sign a message, you are sharing the public key for
that path, and from the public key, you can deduce the segwit or
classic payment address which corresponds.


## HMAC Passwords Backup Limitation

HMAC-based passwords won't work after backup/restore to a different Coldcard.

We used the original Coldcard's serial number in the key stretching.
So although we restore that value, unless the backup is restored
onto same Coldcard, it won't be right.  TOTP users won't have same
issue because shared secret is stored raw.

There is no way to detect if this situation is happening, so the
Coldcard cannot handle this case. Please use TOTP instead of passwords
or reset user passwords after a restore.


## HSM Enabled Before USB Enabled

If we assume your USB-attached computer (CKBunker or otherwise) is
fully compromised by attackers, we might worry that it's poised and ready
to apply a number of USB commands as soon as you unlock your Coldcard,
and before you enter HSM mode.

That's why you are prompted at power-up to go into HSM mode, if a
policy file is detected. The USB port is not yet enabled at that
point, so if you do go to HSM mode, there is no window in which
unprotected USB commands can be launched.

`boot_to_hsm` mode also addresses the same concern, since it assumes
the answer is Yes to that boot-up question.


## Untrusted Remote Hands

When "boot to HSM" feature is enabled, and there are any per-period
limits, we start off with the current period active, and all maximum
amounts spent. In other words, after boot up, they will have to wait-out
the velocity period before those rules can be used.

This is needed because the "remote hands" may have access to the
master PIN code, but not have full spending authority. Therefore,
we cannot allow those hands to reset the spending period because
they could circumvent the velocity limits that way.

If the "boot to HSM" feature isn't enabled, we don't need to have
this policy because anyone who can reboot the device has the master
PIN, and could just as well disable the HSM entirely... so velocity
limits don't apply to them.

## Side Channel Attacks

Any HSM which can perform unattended signing operations is at risk
of leaking key information through side channels. Since multiple
requests can be performed, even a "statistical" leak of a bit or two
of the private key could add-up over time to be a big risk.

Even with all possible countermeasures in software, we still think
there is a risk of side channel data leaks through the USB power
or data lines. Mk3 Coldcard has a new filtering circuit, but it's
hard to cost-effectively mitigate this risk when the budget of the
receiving equipment is effectively unlimited.

Therefore, if this type of attack is a concern, we recommend putting your Coldcard HSM
into a physically-secure (locked) EMI-shielded box, such as this
[experiment box from Holland Shielding](https://hollandshielding.com/compact-shielded-experiment-box)
and isolating the USB power and signal using an adapter such as this
[5kV USB Isolator](https://www.usbgear.com/USBG-M4160.html) and a DC to DC convertor.

If there is enough interest, we will make a lockable metal box, with
suitable EMI gasketing, and specialized power isolation circuit inside for
the USB connection. Please contact <support@coinkite.com> if you'd
like more information.


