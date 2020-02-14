title: CK Bunker
order: 90

## CK Bunker

### Usage Ideas

- CKBunker connects via USB to computer on Tor (onion network). Later you come in via Tor and upload PSBT file. CC signs it based on complex set of business rules (HSM policy), incl. velocity rules, destination whitelist, and authorizing users. Simple.

- Remote Hands: Your coldcard could be in another country; you can lock Coldcard (boot-to-HSM™ feature). Remote hands can do power cycles if needed & keep bunker running.  Video-conference w/ them to send  6-digit code to complete PSBT auth (entered on Coldcard keypad).

- Freeze your warm wallet: the HSM policy on your Coldcard could enable spending to just one single cold-storage address (via whitelist). When your warm wallet is in danger, pull this cord to collect all UTXO and send them to safety, signed, unattended, by the Coldcard

- Meet-me-in-the-Bunker™: Time-based 2FA code from the phones of 3 of these 5 executives needed to authorize spending; Each exec connects to Bunker at same time, views proposed txn and adds their OTP code. Only the Coldcard and exec's phone knows the shared 2FA secret.

- Text message signing: you can disable PSBT signing completely and allow automatic signatures on text messages. Makes Coldcard into an HSM for Bitcoin-based auth/attestations. Can be limited to specific BIP32 subpath derivations. Same w/ address generation/derived XPUBS

- `ckcc-protocol` (command-line program) can do all these HSM features, so you won't have to run CK Bunker, if you want to cook up your own use-case/automation.
Python library and command line tool for communicating with your Coldcard over USB 
<https://github.com/Coldcard/ckcc-protocol>

- Storage Locker™:  ~400 bytes of  secret storage in the Mk3 secure element. CK Bunker uses this to hold secret that encrypts bunker's settings (when at rest) such as the private key for  Hidden Service (= Tor address). So corrupt LEA can't impersonate your bunker after capture.

- Heard you like co-signing! All the Bunker/HSM features work with multisig (P2SH / P2WSH) so maybe you're automatically co-signing a complex multisig from some @CasaHODL quorum.
