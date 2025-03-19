This is a reproducer (unfortunately not minimal) for https://gitlab.haskell.org/ghc/ghc/-/issues/25872.

I have managed to strip it down to 57k lines from the origianl ~250k lines. This was incredibly tedious
and time consuming because the commit from the original project is from Oct 2024 and the code depends
on older stuff, but also requires newer libs in order to build with ghc-9.12.

On Debian, the probable only dependency can be installed with:
```
apt get install libsodium-dev
```

Reprodusing this is as simple as:
```
git clone https://github.com/erikd/ghc-reproducer-25872
cd ghc-reproducer-25872
cabal build all
```

Currently, `ghc-9.10.1` builds fine, while `ghc-9.12.2` fails with:
```
src/Cardano/Ledger/Shelley/Tx/Internal.hs:394:3: error: [GHC-64725]
    • Cannot satisfy: Cardano.Ledger.Binary.Version.MinVersion <= ProtVerHigh
                                                                    era0
    • In the ambiguity check for ‘hashMultiSigScript’
      To defer the ambiguity check to use sites, enable AllowAmbiguousTypes
      In the type signature:
        hashMultiSigScript :: forall era. (EraScript era,
                                           Script era ~ MultiSig (EraCrypto era)) =>
                                          MultiSig (EraCrypto era) -> ScriptHash (EraCrypto era)
    |
394 |   forall era.
```
