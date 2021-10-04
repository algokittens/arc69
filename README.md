---
arc: 69
title: Community Algorand Standard Asset Parameters Conventions for Digital Media Tokens
status: First draft
---

# Community Algorand Standard Asset Parameters Conventions for Digital Media Tokens

## Summary

We introduce community conventions for the parameters of Algorand Standard Assets (ASAs) containing digital media.

## Abstract

The goal of these conventions is to make it simpler for block explorers, wallets, exchanges, marketplaces, and more generally, client software to display the properties of a given ASA. This ARC differs from [ARC3](https://github.com/algorandfoundation/ARCs/blob/main/ARCs/arc-0003.md) by focusing on optimization for fetching of digital media, as well as the use of onchain metadata. This onchain metadata can be mutable if the manager address is included, or immutable if the manager address is removed.


## Specification

The key words "**MUST**", "**MUST NOT**", "**REQUIRED**", "**SHALL**", "**SHALL NOT**", "**SHOULD**", "**SHOULD NOT**", "**RECOMMENDED**", "**MAY**", and "**OPTIONAL**" in this document are to be interpreted as described in [RFC-2119](https://www.ietf.org/rfc/rfc2119.txt).

An ARC-69 ASA has an associated JSON Metadata file, formatted as specified below, that is stored on-chain in the most recent asset configuration transaction.

### ASA Parameters Conventions

The ASA parameters should follow the following conventions:

* *Unit Name* (`un`): no restriction. 
* *Asset Name* (`an`): no restriction.
* *Asset URL* (`au`): a URI pointing to digital media file. This URI:
    * **SHOULD** be persistent.
    * **SHOULD** link to a file small enough to fetch quickly in a gallery view.
    * **MUST** follow [RFC-3986](https://www.ietf.org/rfc/rfc3986.txt) and **MUST NOT** contain any whitespace character.
    * **SHOULD** specify media type with `#` fragment identifier at end of URL. This format **MUST** follow: `#i` for images, `#v` for videos, or `#h` for HTML/interactive digital media.  If unspecified, defaults to `#i`.
    * **SHOULD** use one of the following URI schemes (for compatibility and security): *https* and *ipfs*:
        * When the file is stored on IPFS, the `ipfs://...` URI **SHOULD** be used. IPFS Gateway URI (such as `https://ipfs.io/ipfs/...`) **SHOULD NOT** be used.
    * **SHOULD NOT** use the following URI scheme: *http* (due to security concerns).
* *Asset Metadata Hash* (`am`): the SHA-256 digest of the full resolution media file as a 32-byte string (as defined in [NIST FIPS 180-4](https://doi.org/10.6028/NIST.FIPS.180-4))
    * **OPTIONAL**
* *Unit Name* (`un`): no restriction.
* *Freeze Address* (`f`): 
    * **SHOULD** be empty
* *Clawback Address* (`c`): 
    * **SHOULD** be empty


There are no requirements regarding the manager account of the ASA, or the reserve account. However, if immutability is required the manager address **MUST** be removed.

### JSON Metadata File Schema

> The JSON Medata File schema follow the Ethereum Request for Comments 721 [ERC-721 Metadata URI JSON Schema](https://eips.ethereum.org/EIPS/eip-1155) with the following main differences:
> * Adding the field `standard` to signal which standard is used.
> * Adding the field `external_url` used by [OpenSea metadata format](https://docs.opensea.io/docs/metadata-standards).
> * Adding the field `mime_type` to signal the MIME type and subtype of the media included in `au`. **OPTIONAL**
> * Adding the field `media_url` which is used to link the high resolution media file. **OPTIONAL** but the sha-256 digest of the file **MUST** match the Asset Metadata Hash (`am`).


The JSON Metadata schema is as follows:

```json
{
    "title": "Token Metadata",
    "type": "object",
    "properties": {
        "standard": {
            "type": "string",
            "description": "Describes the standard used, in this case arc69"
        },
        "description": {
            "type": "string",
            "description": "Describes the asset to which this token represents"
        },
        "external_url": {
            "type": "string",
            "description": "A URI pointing to an external website presenting the asset."
        },
        "append": {
            "type": "boolean",
            "description": "A flag signaling appending replacement of appending of metadata"
        },
        "media_url": {
            "type": "string",
            "description": "A URI pointing the high resolution media file of the asset."
        },
        "attributes": {
            "type": "object",
            "description": "Arbitrary attributes (also called properties). Values may be strings, numbers, object or arrays."
        }
    }
}
```
The `standard` field is **REQUIRED**, all the other fields are **OPTIONAL**. But if provided, they **MUST** match the description in the JSON schema.

The URI field (`external_url`) in the JSON Metadata file is defined similarly as the Asset URL parameter `au`.
However, contrary to the Asset URL, it does not need to link to the digital media file.



#### MIME Type

In addition to including a MIME type in the `ua` field of the asset, the JSON Metadata schema allows indication of the MIME type and subtype.



#### Examples

##### Basic Example

An example of an ARC-3 JSON Metadata file for a song follows. The properties array proposes some **SUGGESTED** formatting for token-specific display properties and metadata.

```json

{   
    "standard": "arc69",
    "description": "arc69 theme song",
    "external_url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ",
    "mime_type":"video/mp4",
    "attributes": [
        {"trait_type":"Base",
        "value":"Groovy"
        },
        {"trait_type":"Vibes",
        "value":"Funky"
        },
        {"trait_type":"Overall",
        "value":"Good stuff"
        }
    ]
}
```

An example of possible ASA parameters would be:

* *Asset Unit*: `ARC69 theme song` for example.
* *Asset Name*: `69TS` for example.
* *Asset URL*: `ipfs://QmWS1VAdMD353A6SDk9wNyvkT14kyCiZrNDYAad4w1tKqT#v`
* *Metadata Hash*: the 32 bytes of the SHA-256 digest of the high resolution media file.
* *Total Number of Units*: 1
* *Number of Digits after the Decimal Point*: 0

## Rationale

These conventions are heavily based on Ethereum Improvement Proposal [ERC-1155 Metadata URI JSON Schema](https://eips.ethereum.org/EIPS/eip-1155) to facilitate interoperobility. 

The main differences are highlighted below:

* Asset Name and Asset Unit are specified in the ASA parameters. This is to allow wallets that are not aware of ARC-69 or that are not able to retrieve the JSON file to still display meaningful information.
* MIME type fields are added to help clients know how to display the files pointed by URI.
* All asset metadata is stored onchain.
* Metadata can be either mutable or immutable.

Valid JSON Metadata files for ERC-1155 are valid JSON Metadata files for ARC-69.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
diff --git a/arc-69.md b/arc-69.md
