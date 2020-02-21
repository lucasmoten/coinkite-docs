title: Spending Rules
ordering: 30
hidden: 1

## HSM Policy JSON File

The HSM policy is established by a simple JSON file, which configures various
security settings, and the rules for which transactions should be signed automatically
by the Coldcard. This JSON file is uploaded to the Coldcard, which parses it,
and creates a text version of the policy, for you to approve on-screen.

The file has two parts: global settings and a variable number of "rules". The
global (top-level) values are as follows:

- `notes`: free-form text, shown at top of confirmation message, up to 80 chars long
- `period`: (integer) velocity period, in minutes, shared across all the rules
- `must_log`: (bool) fail anything we can't log to SD card
- `never_log`: (bool) disable all log generation (even if SD card inserted)
- `warnings_ok`: (bool) normally will fail a PSBT that has any warnings, this allows it

- `msg_paths`: enable message signing, but only using provided derivation paths (a list) use `any` to allow any path
- `share_xpubs`: same for xpubs sharing, but `m` is always shared regardless
- `share_addrs`: same for addrs, but keyword `p2sh` in list also allows p2sh address calcs

- `set_sl`: (text string) load the storage locker with a value: max 416 chars
- `allow_sl`: (integer) number of times the storage locker can be read per boot-up

- `boot_to_hsm`: if defined, six-digit numeric code used to escape boot-to-HSM feature
- `priv_over_ux`: (bool) reduce chattiness of status responses in HSM mode, making UX harder

- `rules`: list of objects, see below. Each rule is checked in order given in this list.

Each rule consists of these values:

- `whitelist`: a list of specific addresses which are allowed as destinations
- `per_period`: total BTC that can move thru this rule in the period
- `max_amount`: max BTC per transaction, that this rule can apply to (independent of period)
- `users`: a list of usernames that are able to approve (N)
- `min_users`: number of users (M) needed to approve (from list of users for rule, not system), if missing, assume all users required (M=N)
- `local_conf`: bool flag, local user must (also) approve (via 6-digits entered on keypad)
- `wallet`: either name of a multisig wallet, or `"1"` indicating rule only applies to non-multisig

When an element of the rule is missing or has value `null`, then
the restriction does not apply. For example, if `whitelist` is
missing, then the Coldcard will not consider the destination address
when considering the rule.

If no rules are defined, then no PSBT will be signed. This can be
useful for text message signing applications. On the other hand,
an empty rule, meaning just `{}`, allows any transaction to be
signed, so be careful!

# Spending Period (Velocity)

To implement spending limits based on time, the Coldcard requires you to define
a period. This period, expressed in minutes, applies to all rules. Any rule with
a defined `per_period` value, will be affected. 

The period starts when it is first used. There is no absolute concept of
time on the Coldcard (it doesn't have a real time clock). There is only one
period, so it will begin as soon as any rule using a `per_period` limit
is applied successfully.

At the end of the period, the totals are reset to zero.

# Spending Rules

Multiple spending rules can be defined. The system scans the rules
starting from the first one, and will test each rule. The first
rule that is satisfied is applied and following rules are not considered.

We recommend putting the most narrow rules first. Catch-all rules, which 
might move more money should be later in the list.

## Max Transaction Amount 

`max_amount` per transaction is less useful because a number of transactions
could be put together to "work around" this rule. However, if there is natural
rate-limiting in your system, for example, by using a
[local operator to enter a code](local-codes) each time,
then this is still helpful. Use `local_conf=True` to enable
the local code when the rule is applied.

## Per-Period Limit

`per_period` is the total amount, in Satoshis, that can be spent
using this rule in the period. 

## Authorizing Users

You can list username in the `users` field. If defined the `min_users` controls
how many of those are required. By default (if `min_users` isn't defined), all users
listed must confirm the operation. You can achieve 2-of-5 and similar setups
using `min_users`. All users listed must already be defined on the Coldcard
before the policy is activated.

The `local_conf` boolean enables the [local PIN code](local-codes),
and requires it for transactions
that take advantage of the rule. It can be combined with the user list, or used
by itself.

## Limit to Named Wallet

The `wallet` field can be omitted, or set to the  name of a multisig wallet. If set
to the string `"1"`, it indicates this rule only applies to the non-multisig wallet.

## Whitelist Address

You may specify a list of addresses in the `whitelist` field. The Coldcard will
only apply the rule if all destination addresses of the PSBT transaction are
included in the whitelist. This is a powerful feature when your target wallets
that you control in the whitelist, such as emergency cold wallets.


# Global Policy Values

## Logging to MicroSD Card

Two setting affect logging: `must_log` and `never_log`. By default,
the Coldcard will log if a card is inserted. It does not fail if
the card is missing. If that is an issue for you, then set `must_log`
and transactions will be refused if the card isn't installed and
working. `never_log` is useful when you don't want to keep records
at the Coldcard's location.

## Warnings Okay?

This boolean allows the Coldcard to sign PSBT files that have
warnings. Typically this is overly-large fees or weird path
derivations. Since we don't expect warnings, any transactions with
a warning is normally refused.

## Message Signing

To enable text message signing, list one or more BIP32 derivation paths in `msg_paths`.
You can use the special value `"any"` to allow all signing. You may also use a star
in the last position of a path, like these examples:

- `m/84'/0'/0/*`
- `m/84'/0'/0'/*'`
- `m/9984/*`

The star allows any number in the final position (only). It does not allow deeper paths.

## Sharing Xpubs

The Coldcard can calculate XPUB values for derived paths, if
`share_xpubs` is defined. You can limit this feature by giving a
list of permitted paths, or the keyword `"any"` to allow any subpath.
The master xpub (`m`) is always available over USB protocol and cannot be disabled.

## Sharing Addresses

Similarly, the Coldcard can calculate wallet addresses, if `share_addrs`
contains a list of whitelisted derivation paths. Star patterns, and the keyword `"any"` can be used,
as well as the keyword `"p2sh"` which allows addresses in multisig
wallets to be shared.

In the case of multisig wallets, we do not check the script provided,
beyond the normal checks for inclusion into a known multisig wallet.

## Storage Locker

The storage locker is small number of bytes held in the secure
element of the Coldcard (Mk3 only). The HSM policy file can be used
to write a text value to this area, using `set_sl`. (That part of
the policy file is forgotten after it's committed into the secure
element.) The storage locker is protected by the master PIN, and
has the same level of protection as the master seed.

The storage locker is readable over the USB connection in HSM mode.
However, the number of times it can be read is limited by the
`allow_sl` value.  You may set this to one, so that when your
companion software is started, it has one opportunity to read the
locker. If it is restarted, or other (unauthorized) software on your
USB-connected machine reads the locker, it will fail. Further access
will require reboot of the Coldcard, and knowledge of the master PIN.

CKBunker manages the Storage Locker for it's own purposes: it
stores a 32-byte secret to unlock a NaCL secret box holding all
CKBunker settings.

## Boot-to-HSM

This feature forces the Coldcard to start in HSM mode immediately
after boot up (and entry of the master PIN). It is enabled 
if `boot_to_hsm` is defined.

If you specify a 6-digit numeric code for `boot_to_hsm`, and if
that code is provided in the first 30 seconds after startup, the Coldcard
will leave HSM mode. (The HSM policy file is erased in this process.)

But if you set the `boot_to_hsm` value to a non-numeric value which
cannot be entered by the keypad, the Coldcard will never be able
to leave HSM mode.

!!! warning "Bricking Hazard"

    No changes to firmware, HSM policy, Coldcard settings will be possible&mdash;ever again.
    <br>
    Not even the master PIN holder can change HSM policy nor escape HSM
    mode! Firmware upgrades are not possible.

## Privacy over UX

During development of the HSM feature, we found there were numerous
status and informational values being shared over USB that, to some
degree, assist attackers. However, those values are needed to provide
a usable interface and a nice user experience (UX).

If you set `priv_over_ux` to true, the following values will not be
shared over [USB in the HSM status response](protocol):

- text summary of the spending policy
- count of approvals / refusals
- the number of time the storage locker has been read
- the period length
- when the period will end
- how much each rule has spent in current period
- system uptime
- list of usernames
- number of users which have provided auth credentials for current PSBT

The CKBunker can operate in either mode, but you will find it harder
to use, as it's not possible to know where you stand in terms of 
velocity spending and user authorization.

# Example JSON Policy File

Here is a sample policy file, ready to be uploaded into a Coldcard.

It has three rules:

- local user can authorize up to 1BTC per txn (by themselves)
- either Alice or Bob can authorize up to 1BTC per 4 hour period
- allow any txn that sends to address `bc1qar0srrr7xfkvy5l643lydnw9re59gtzzwf5mdq`

```json
{
  "never_log": true,
  "must_log": false,
  "priv_over_ux": false,
  "boot_to_hsm": null,
  "period": 240,
  "set_sl": "my secret here",
  "allow_sl": 13,
  "rules": [
    {
      "whitelist": [],
      "per_period": null,
      "max_amount": 100000000,
      "users": [],
      "local_conf": true,
      "wallet": null
    },
    {
      "whitelist": [],
      "per_period": 100000000,
      "max_amount": null,
      "users": [
        "alice",
        "bob"
      ],
      "min_users": 1,
      "local_conf": false,
      "wallet": null
    },
    {
      "whitelist": [
        "bc1qar0srrr7xfkvy5l643lydnw9re59gtzzwf5mdq"
      ],
      "per_period": null,
      "max_amount": null,
      "users": [],
      "local_conf": false,
      "wallet": null
    }
  ],
  "msg_paths": [
    "any"
  ],
  "share_xpubs": [
    "m/84'/0'/0'/*"
  ],
  "share_addrs": [
    "m/84'/0'/0'/*"
  ],
  "notes": "Semper Fi"
}
```

The Coldcard will show this text to summarize the policy:

```coldstyle
=-=
Semper Fi
=-=

Transactions:
- Rule #1: Up to 1 XTN per txn will be approved if local user confirms
- Rule #2: Up to 1 XTN per period may be authorized by any one user: alice OR bob
- Rule #3: Any amount will be approved provided it goes to: bc1qar0srrr7xfkvy5l643lydnw9re59gtzzwf5mdq

Velocity Period:
 240 minutes
 = 4 hrs

Message signing:
- Allowed if path matches: (any path)

Other policy:
- No logging.
- Storage Locker will be updated, and can be read 13 times.
- XPUB values will be shared, if path matches: m OR m/84'/0'/0'/*.
- Address values values will be shared, if path matches: m/84'/0'/0'/*.
```

