hidden: 1
ordering: 90
title: CLI Commands for HSM 

The `ckcc` helper program, or any other program which can speak
Coldcard's USB protocol, and be used in HSM mode. Once HSM mode is
started, the usual signing commands are used, but without the need
for user interaction. There are a few commands that are disabled
in HSM mode, but those are not involved in PSBT or message signing.

To authorize PSBT signing, you may need to use the "auth" command,
which transmits a OTP (6 digit) code, or hashed password to the Coldcard.

If the local operator must confirm, then you will need to calculate the
appropriate code to be entered. Use `ckcc local-conf` for this.

Fetch the status of the HSM using `ckcc hsm`.

## Example Sequence

Starting with the policy file (JSON), shown
as an [example on this page](rules#example-json-policy-file),
and with the Coldcard already in HSM mode, you might sign a transaction with this
sequence of `ckcc` commands:

```sh
% ckcc hsm
{'active': True,
 'approvals': 0,
 'chain': 'XTN',
 'last_refusal': None,
 'next_local_code': 'BrstN9qZ28pEaBdrUs3K',
 'pending_auth': 0,
 'period': 240,
 'refusals': 0,
 'sl_reads': 1,
 'summary': '=-=\n'
...
            "m/84'/0'/0'/*.\n",
 'uptime': 491.477,
 'users': ['alice', 'bob']}

% ckcc auth alice 111111
Correct or queued

% ckcc sign test.psbt --visualize
232 bytes (start @ 0) to send from 'attempt.psbt'
Ok! Downloading result (95 bytes) 
0.99990000 XTN
 - to address -
mzwtncBV2T5ikNmPf1u6TuEQYys6RPsLxp

Network fee:
0.00010000 XTN

% ckcc x sign test.psbt -6
232 bytes (start @ 0) to send from 'attempt.psbt'
Waiting for OK on the Coldcard...

You refused permission to do the operation

% ckcc hsm | grep last_refusal
 'last_refusal': "User 'alice' gave wrong auth value: mismatch",

% ckcc hsm | grep pending_auth
 'pending_auth': 0,


% ckcc sign test.psbt -6
232 bytes (start @ 0) to send from 'attempt.psbt'
Waiting for OK on the Coldcard...

You refused permission to do the operation


% ckcc hsm
...
 'last_refusal': "Rejected: rule #1: local operator didn't confirm, rule #2: "
                 'need user(s) confirmation, rule #3: non-whitelisted address: '
                 'mzwtncBV2T5ikNmPf1u6TuEQYys6RPsLxp',
...

% ckcc local-conf test.psbt 
Local authorization code is:

	891443

```
