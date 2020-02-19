title: USB Protocol for HSM Mode
ordering: 99
hidden: 1

# New Commands

There are some new commands added to support HSM mode. Check out the latest
version (1.0 or higher) of `ckcc-protocol` for the details and command-line example
code. In summary, new commands include:

- `hsms` - HSM Start (after uploading JSON policy file)
- `hsts` - Return HSM Status (gives JSON values, see below)
- `nmur` - Create a new user
- `rmur` - Delete a user
- `user` - Provide authorization details for a user
- `gslr` - Read contents of Storage Locker

Only specific USB commands are whitelisted for use once HSM mode has been
activated:

- `logo`, `ping`, `vers`: harmless/boring
- `upld`, `sha2`, `dwld`, `stxn`: upload/download/sign PSBT needed; firmware blocked
- `mitm`, `ncry`:  USB encryption setup
- `smsg`: limited by policy `msg_paths`
- `blkc`, `hsts`: report status values
- `stok`, `smok`: completion check: sign txn or msg
- `xpub`: limited by policy `allow_xpub` but `m` always allowed
- `msck`: multisig wallet quick check
- `p2sh`, `show`: limited by HSM policy: `allow_addrs`
- `user`:  auth HSM user, other user cmds not allowed
- `gslr`:  read storage locker; hsm mode only, limited usage

All other USB commands will fail during HSM mode.

# Unchanged Commands

The key commands for uploading and signing a PSBT file are not
changed. This means you could use Electrum with a Coldcard in HSM
mode.

# HSM Status Response

You can query the state of the HSM features in the Coldcard at any time,
and should expect a simple JSON document as the response.

When the HSM is not enabled, you will get a short response like this:

```sh
% ckcc hsm
{'active': False,
 'chain': 'XTN',
 'policy_available': False,
 'users': [],
 'wallets': []}
```

Once enabled, the result depends heavily on the [`priv_over_ux`](rules) setting,
and the many other values in your HSM policy.

```sh
% ckcc hsm
{'active': True,
 'approvals': 0,
 'chain': 'XTN',
 'last_refusal': None,
 'pending_auth': 0,
 'period': 720,
 'refusals': 0,
 'sl_reads': 0,
 'summary': 'Transactions:\n'
            '- Rule #1: Up to 0.00001 XTN per txn will be approved\n'
            '\n'
            'Velocity Period:\n'
            ' 720 minutes\n'
            ' = 12 hrs\n'
            '\n'
            'Message signing:\n'
            "- Allowed if path matches: m/84/0'/0' OR m/3\n"
            '\n'
            'Other policy:\n'
            '- MicroSD card will receive log entries.\n'
            '- Storage Locker will be updated, and can be read 2 times.\n'
            '- XPUB values will be shared, if path matches: m OR m/3.\n',
 'uptime': 3284,
 'users': ['alice', 'bob', 'baz']}
```

With `priv_over_ux` enabled, the status response is minimal:

```sh
% ckcc hsm
{'active': True,
 'approvals': 0,
 'chain': 'XTN',
 'last_refusal': None,
 'refusals': 0}
```

### Understanding the Status Response

Here are the fields of the status response.

- `active`: is the HSM mode activated?
- `chain`: either BTC or XTN (testnet)
- `last_refusal`: short text explanation of why the last request failed
- `approvals`, `refusals`: counts of number of PSBT or msg signings done
- `period`: number of minutes in the period (from policy file)
- `uptime`: number of seconds the Coldcard has been running
- `users`: list of defined Coldcard users
- `next_local_code`: if defined, one or more rules needs local confirmation, and this
   value is a factor in the PIN calculation. 15 bytes encoded in Base64.
- `summary`: text of the policy as shown to user during confirmation step
- `sl_reads`: number of reads of the storage locker
- `period_ends`: number of seconds until the current period ends
- `has_spent`: a list of amount spent in this period, by each rule (in order by rule)
- `pending_auth`: count of auth details already submitted before PSBT received

Not all will be present depending on policy and system state.
