hidden: 1
ordering: 90
title: CLI Commands for HSM 

The `ckcc` helper program, or any other program which can speak
Coldcard's USB protocol, and be used in HSM mode. Once HSM mode is
started, the usual commands are used, with the exception of a number
of commands that are disabled.

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
{
...
 'last_refusal': "Rejected: rule #1: local operator didn't confirm, rule #2: "
                 'need user(s) confirmation, rule #3: non-whitelisted address: '
                 'mzwtncBV2T5ikNmPf1u6TuEQYys6RPsLxp',
...

% ckcc local-conf test.psbt 
Local authorization code is:

	891443

```
