---
arc: 69
title: Community Algorand Standard Asset Parameters Conventions for Digital Media Tokens
status: First draft
---

# Community Algorand Standard Asset Parameters Conventions for Digital Media Tokens

## Summary

We introduce community conventions for the parameters of Algorand Standard Assets (ASAs) containing digital media.

## Abstract

The goal of these conventions is to make it simpler for block explorers, wallets, exchanges, marketplaces, and more generally, client software to display the properties of a given ASA. This ARC differs from ARC3 by focusing on optimization for fetching of digital media, as well as the use of onchain mutable metadata.


## Specification

The key words "**MUST**", "**MUST NOT**", "**REQUIRED**", "**SHALL**", "**SHALL NOT**", "**SHOULD**", "**SHOULD NOT**", "**RECOMMENDED**", "**MAY**", and "**OPTIONAL**" in this document are to be interpreted as described in [RFC-2119](https://www.ietf.org/rfc/rfc2119.txt).

An ARC-3 ASA has an associated JSON Metadata file, formatted as specified below, that is stored on-chain in the most recent asset configuration transaction.

### ASA Parameters Conventions

The ASA parameters should follow the following conventions:

* *Unit Name* (`un`): no restriction 
* *Asset Name* (`an`): no restriction
* *Asset URL* (`au`): a URI pointing to digital media file. This URI:
    * **SHOULD** be persistent.
    * **SHOULD** link to a medium resolution media file (?)
    * **MUST** follow [RFC-3986](https://www.ietf.org/rfc/rfc3986.txt) and **MUST NOT** contain any whitespace character
    * **SHOULD** use one of the following URI schemes (for compatibility and security): *https* and *ipfs*:
        * When the file is stored on IPFS, the `ipfs://...` URI **SHOULD** be used. IPFS Gateway URI (such as `https://ipfs.io/ipfs/...`) **SHOULD NOT** be used.
    * **SHOULD NOT** use the following URI scheme: *http* (due to security concerns).
* *Asset Metadata Hash* (`am`): the SHA-256 digest of the high resolution digital media file as a 32-byte string (as defined in [NIST FIPS 180-4](https://doi.org/10.6028/NIST.FIPS.180-4))
    * **OPTIONAL**
* *Unit Name* (`un`): no restriction 
* *Freeze Address* (`f`): 
    * **SHOULD** be empty
* *Clawback Address* (`c`): 
    * **SHOULD** be empty


There are no requirements regarding the manager account of the ASA, or its the reserve account.

### JSON Metadata File Schema

> The JSON Medata File schema follow the Ethereum Request for Comments 721 [ERC-721 Metadata URI JSON Schema](https://eips.ethereum.org/EIPS/eip-1155) with the following main differences:
> * Support for mimetype fields for any file pointed by the URI field. (need to rephrase this)
> * Adding the fields `external_url` used by [OpenSea metadata format](https://docs.opensea.io/docs/metadata-standards).
> * Adding the field `info_type` to signal inclusion into rarity metrics (better wording and better field needed)
> * Adding the field `append` to signal replacement of appending of metadata. If excluded assumed to be False.
> * Adding the field `mime_type` to signal mime type of the media included in `au`. **MUST** be included if the mime type != image. 



The JSON Metadata schema is as follows:

```json
{
    "title": "Token Metadata",
    "type": "object",
    "properties": {
        "description": {
            "type": "string",
            "description": "Describes the asset to which this token represents"
        },
        "external_url": {
            "type": "string",
            "description": "A URI pointing to an external website presenting the asset."
        },
        "atributes": {
            "type": "object",
            "description": "Arbitrary attributes (also called properties). Values may be strings, numbers, object or arrays." (?)
        }
    }
}
```

All the fields are **OPTIONAL**. But if provided, they **MUST** match the description in the JSON schema.

The URI field (`external_url`) in the JSON Metadata file is defined similarly as the Asset URL parameter `au`.
However, contrary to the Asset URL, it does not need to link to the digital media file.



#### MIME Type Files

Compared to ERC-1155, the JSON Metadata schema allows to indicate the MIME type of the files pointed by the `au` URI field.
This is to allow clients to display appropriately the resource without having to first query it to find out the MIME type.

It is **REQUIRED** to specify the `mimetype` field if the file is not an image. #suggested ?



#### Examples

##### Basic Example

An example of an ARC-3 JSON Metadata file for a song follows. The properties array proposes some **SUGGESTED** formatting for token-specific display properties and metadata.

```json
{   "description": "arc69 theme song",
    "external_url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ",
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
    }
    ]
}
```


An example of possible ASA parameters would be:

* *Asset Unit*: `ARC69 theme song` for example
* *Asset Name*: `69TS`
* *Asset URL*: `ipfs://QmWS1VAdMD353A6SDk9wNyvkT14kyCiZrNDYAad4w1tKqT`
* *Metadata Hash*: the 32 bytes of the SHA-256 digest of the high resolution media file
* *Total Number of Units*: 100
* *Number of Digits after the Decimal Point*: 0

The above parameters define a fractional NFT with 100 shares.


##### Appending Example

Example using two asset configuration transaction notes to extend metadata beyond 1kb.


Most recent asset config transaction:

```json
{   "description": "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Eget aliquet nibh praesent tristique magna sit amet purus. Gravida dictum fusce ut placerat. Augue lacus viverra vitae congue. Neque volutpat ac tincidunt vitae semper quis. Aenean et tortor at risus viverra adipiscing at. Sed enim ut sem viverra aliquet eget sit amet. Tortor posuere ac ut consequat semper viverra. Enim facilisis gravida neque convallis a cras semper auctor neque. Lobortis feugiat vivamus at augue eget. Tincidunt tortor aliquam nulla facilisi cras fermentum odio eu. Odio pellentesque diam volutpat commodo sed egestas egestas fringilla.

Vulputate enim nulla aliquet porttitor. Porta nibh venenatis cras sed. Cras semper auctor neque vitae. Nunc faucibus a pellentesque sit amet porttitor eget dolor morbi. Amet tellus cras adipiscing enim.",
    "append": "True"
}
```
Previous asset config transaction:

```json
{   "external_url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ",
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



> still need to write this:

## Rationale

These conventions are heavily based on Ethereum Improvement Proposal [ERC-1155 Metadata URI JSON Schema](https://eips.ethereum.org/EIPS/eip-1155) to facilitate interoperobility. 

The main differences are highlighted below:

* Asset Name and Asset Unit can be optionally specified in the ASA parameters. This is to allow wallets that are not aware of ARC-3 or that are not able to retrieve the JSON file to still display meaningful information.
* MIME type fields are added to help clients know how to display the files pointed by URI.


Valid JSON Metadata files for ERC-1155 are valid JSON Metadata files for ARC-3.

Valid JSON Metadata files for ERC-1155 are valid JSON Metadata files for ARC-69(?).



However, it is highly recommended that users always include the additional RECOMMENDED fields, such as the integrity fields.


## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
