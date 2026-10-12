# Discovery definitions and access policies

Per environment (`ontwikkel`, `test`, `acceptatie`, `productie`): `discovery-service-presentation-definitions/discovery_pzp_*.json` (Discovery Service definition, loaded by the Discovery Server and all clients) and `oauth-presentation-definitions/policy_pzp_*.json` (access policy, loaded by every data holder). Both are read when the node starts.

## ca-fingerprints

The `$.issuer` of an `X509Credential` is a `did:x509` containing the SHA-256 (base64url, unpadded) of the CA that issued the UZI server certificate. The definitions allow these CAs:

| Environment | Issuing CA | ca-fingerprint |
|---|---|---|
| productie | `UZI-register Private Server CA G1` (until 12-11-2028) | `vdhg746H4rLH67NN1unhdxo6PF3shQunCA4-KQTb2Jc` |
| productie | `UZI Server - G4 PKIo Priv G-TLS SYS - 2025` (from 12-11-2026) | `sDTL__yv54TtrvSf7601TtvHgj2NZSLT2m-Okf8oqkI` |
| test / ontwikkel | `TEST UZI-register Private Server CA G1` (until 12-11-2028) | `GwlhBZuEFlSHXSRUXQuTs3_YpQxAahColwJJj35US1A` |
| test / ontwikkel | `ACCEPTATIE UZI Server - G4 Priv G-TLS SYS - 2025` (from 12-11-2026) | `svKzNxhS07V1dKkekWOZV0d6Ao7_qvjqXegWB32YjZE` |
| test / ontwikkel | `Fake UZI Root CA` (go-didx509-toolkit, for development without a CIBG test certificate) | `szqMaTpnD6GN0aRrT98eV4bhAoOgyItEZVyskYyL_Qc` |

acceptatie accepts the CIBG test and production CAs (G1 and G4), not the fake CA.

The private key of the fake CA is public in the [go-didx509-toolkit](https://github.com/nuts-foundation/go-didx509-toolkit) repository, so anyone can create a credential for any URA with it. A registration on test or ontwikkel therefore says nothing about who the participant is.

Verify:

```sh
curl -sO http://cert.pkioverheid.nl/UZIServerG4PKIoPrivGTLSSYS2025.cer
openssl dgst -sha256 -binary UZIServerG4PKIoPrivGTLSSYS2025.cer | base64 | tr '+/' '-_' | tr -d '='
```

## Truststore

The truststore (PEM bundle with the G1 and G4 CAs) and instructions are at [nuts.nl/certs](https://nuts.nl/certs).

## G1 to G4 transition

All participants, including the Discovery Server host, must have deployed the updated files before any participant uses a credential from a G4 certificate. After 12-11-2028 the G1 fingerprint can be dropped.

Background: [nuts-node#4355](https://github.com/nuts-foundation/nuts-node/issues/4355).
