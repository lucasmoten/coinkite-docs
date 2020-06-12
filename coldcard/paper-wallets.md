title: Paper Wallets
ordering: 100

This feature creates a new random private key, completely unrelated
to your seed words, and saves deposit address and private key (WIF
format) into a text file on MicroSD. That file is a ready to use
paper wallet!

You may also use your dice rolls as an entropy source instead of the Coldcard's TRNG
(true random number generator).

If you have a Mk3, it will also add a QR code inside the text file, and if you provide a 
special PDF-like template file
(example in [paperwallet.pdf](https://github.com/Coldcard/firmware/raw/master/docs/paperwallet.pdf)) then it will superimpose
the QR codes into the template, and save the resulting ready-to-print PDF to MicroSD.

!!! warning "Caution"

    Paper wallets carry **MANY RISKS** and should only be used for **SMALL AMOUNTS**.

    For gifting Bitcoins to new people, we recommend using
    [Opendime](https://opendime.com).
    For long-term cold storage, we recommend Coldcard with a BIP39 seed written to paper.

### Settings and Operation

![paper wallet menu](img/snap-paper-wallets.png){.snap}

The first menu item ("Don't make PDF" in this example) allows you to select
a PDF template from the MicroSD. Those templates can be created
using our open-source
tool called [Templator](https://github.com/Coldcard/coldcard-paper-wallet-templator)
and you may also find existing templates shared in that project. You can easily design 
your own paper wallet templates, for your own personal and seasonal needs, by
giving that program a PDF to use as background, and some information about where
you want the QR and text to be inserted.

The second menu item ("Classic Address" in this example) allows you to
select between classic Base58 Bitcoin payment addresses (starts
with one) or Segwit/Bech32 addresses.

The third choice starts the process to pick a wallet using dice
rolls. Be sure to use enough rolls, and never less than 50. When
you are done the rolls, press OK and the wallet is created and saved immediately.

Choose the "GENERATE WALLET" item to make the wallet, using the
TRNG. The file is named for the payment address (either base58 or
bech32 format) and contains the private key and payment address in
a number of forms for ease of use. If you are using a PDF template,
the same text notes are saved along side the new PDF file.

You can create as many wallets as you wish before leaving this menu;
each is saved to the MicroSD as you go along.


### Example Wallet

Here's an example of the wallet file, saved in this case, into:
    13ZcQHhwgu2mrpn54JVLn7x9xgDtGdf14d.txt

On a Mk2 Coldcard, the "QR Codes" section will not be present.

```textfile
Coldcard Generated Paper Wallet

Deposit address:

  13ZcQHhwgu2mrpn54JVLn7x9xgDtGdf14d

Private key (WIF=Wallet Import Format):

  L3ZeFQJAgAfmPeNEZwyriuh1djs8Lq6fj9psjafBEjmY564SBBmg

Private key (Hex, 32 bytes):

  bd473a280dd7b2c418216282e2adeb2a6dcc1c90e448f90f5eb539b32875db76

Bitcoin Core command:

  bitcoin-cli importmulti '[{"desc":
    "pkh(L3ZeFQJAgAfmPeNEZwyriuh1djs8Lq6fj9psjafBEjmY564SBBmg)#kynnrk4r",
    "timestamp": 1574277000}]'

# OR (more compatible, but slower)

  bitcoin-cli importprivkey "L3ZeFQJAgAfmPeNEZwyriuh1djs8Lq6fj9psjafBEjmY564SBBmg"



--- QR Codes ---   (requires UTF-8, unicode, white background)



Deposit address:

        ██████████████  ██      ████  ████████████  ██  ██  ██████████████
        ██          ██  ██        ██  ██    ████  ████████  ██          ██
        ██  ██████  ██      ██        ██  ██    ████████    ██  ██████  ██
        ██  ██████  ██  ████████    ██  ██    ██████████    ██  ██████  ██
        ██  ██████  ██          ██  ██          ██    ██    ██  ██████  ██
        ██          ██    ██  ██████      ██  ████  ██  ██  ██          ██
        ██████████████  ██  ██  ██  ██  ██  ██  ██  ██  ██  ██████████████
                        ██████            ██    ████████                  
        ██  ████  ██████  ██  ██  ██          ██████    ██  ██    ██  ████
        ██    ██  ██      ██  ██    ████    ████  ██████      ██  ██      
                    ██████    ██    ██  ████          ██  ██    ████  ██  
          ██      ██  ██████    ██████  ██  ██          ██████  ████  ████
        ██        ██████  ██  ██      ██    ████  ██  ████████      ██████
          ████  ████  ████████          ██  ██      ██████  ██    ██    ██
            ██  ██  ██  ██  ██      ████████      ██  ██  ██████████  ██  
            ████  ██    ████  ██        ██    ████    ██  ██████    ██████
            ██  ██████████    ██      ██      ██████        ████        ██
                        ██  ██  ██  ██    ██████  ██        ████  ████    
        ██    ████████  ████████████      ██    ██  ██  ██      ██████    
        ██  ██    ██  ████  ██    ████████      ████  ██████████  ██  ████
          ██  ██  ██████    ██    ██  ██    ██  ██    ██████    ██  ████  
        ██  ██        ████      ██    ██    ██          ████    ████      
              ████  ██████          ██  ████      ██████████    ██████  ██
          ██████████  ██  ██  ██  ██    ██    ██  ████      ████  ████    
        ██  ██  ██  ██  ██████████  ██████  ██      ██  ██████████  ██    
                        ██  ████  ██      ██          ████      ██████  ██
        ██████████████  ██  ██    ██          ██    ██████  ██  ██      ██
        ██          ██  ██      ████████      ██  ████████      ████    ██
        ██  ██████  ██        ██████      ██    ██      ██████████  ████  
        ██  ██████  ██  ██████████████    ████████    ████    ████        
        ██  ██████  ██  ██                ██  ██    ██        ████████    
        ██          ██    ██        ██████    ██  ██████  ██    ████      
        ██████████████  ██        ██  ████  ██  ██    ██████████  ██  ██  

        13ZcQHhwgu2mrpn54JVLn7x9xgDtGdf14d



Private key:

        ██████████████  ██  ████  ████  ██████        ████  ██████████████
        ██          ██  ██████  ████  ████  ██████████  ██  ██          ██
        ██  ██████  ██      ██  ██        ██  ██  ████████  ██  ██████  ██
        ██  ██████  ██  ██  ██      ██████      ████████    ██  ██████  ██
        ██  ██████  ██      ████  ████    ██    ██    ██    ██  ██████  ██
        ██          ██    ████      ██    ████████      ██  ██          ██
        ██████████████  ██  ██  ██  ██  ██  ██  ██  ██  ██  ██████████████
                        ██  ██████████          ████████                  
        ██  ████  ██████  ██  ██          ██████  ██        ██    ██  ████
            ██    ██        ██  ██      ██      ████████  ████  ████  ██  
        ██    ████  ██              ██    ██  ████    ██  ██  ██  ██    ██
        ██████  ████        ████  ████████  ██  ██    ██  ██████  ██  ██  
        ██      ████████████████      ██    ████  ██    ██████      ██  ██
            ████████            ██      ██  ██      ██████  ██      ██  ██
        ████      ████████████████  ██████  ██        ██  ████████████    
        ████    ██    ██  ████    ██    ████  ████    ██    ████      ████
              ██    ██████  ██  ████████████████████      ██████  ████    
        ████      ██        ████████  ██  ██    ████  ████    ██  ████████
        ██████  ██████      ██  ██████    ██    ██  ██████      ██  ████  
            ████████  ██    ██  ██  ██████    ██████  ████████████████  ██
          ██    ██  ██      ████████████    ██  ██    ██████    ██  ██  ██
        ████            ██  ██  ██  ████  ████          ████    ████  ████
            ██████  ████  ████  ████    ██  ██    ████████  ██  ██  ██  ██
          ██  ████    ██  ██  ████  ██  ████████  ████        ██    ██████
        ██    ██    ████  ████████████████████  ██      ██████████      ██
                        ████      ██████  ██  ██      ████      ██  ██████
        ██████████████  ████    ██    ████          ██  ██  ██  ██████████
        ██          ██  ████    ████  ████    ██  ████  ██      ██████████
        ██  ██████  ██      ██████  ██    ██    ██    ████████████      ██
        ██  ██████  ██  ██  ██    ████      ██████      ██████  ████      
        ██  ██████  ██  ██  ██                ██    ██  ████  ██    ██    
        ██          ██      ████████  ████  ████  ██████  ██  ██████      
        ██████████████  ██      ██    ██  ████  ██    ████████        ██  

        L3ZeFQJAgAfmPeNEZwyriuh1djs8Lq6fj9psjafBEjmY564SBBmg

```

