# User Object Model

## Overview

**Current version:** 0.0.11

**Logs:**

- 2019-12-31: **Added** `multipass_token` data point.
- 2019-12-10: **Replaced** `PLATFORM_OCEP` with `PLATFORM_THINKIFIC` as an option for a user `address` `input_from`. **Updated** `verified_email` to be the correct type.
- 2019-11-01: **Replaced** `PLATFORM_OCEP` with `PLATFORM_THINKIFIC` as an option for a user `platform` name.
- 2019-08-15: **Modified** the structure of Addresses to be a map of maps. This will allow enforcement of unique IDs.
- 2019-08-13: **Modified** the structure of Organizations to be a simple map of id (key) to name (value) pairs. This will allow enforcement of unique IDs.
- 2019-08-08: **Modified** the structure of the User Platforms. Changed the `platforms` to be a map of maps. The key for each `platform` is the platform `name` (no longer included within the platform data).
- 2019-08-01: **Removed** `jwt_token` as not needed. This will be passed in the header and not stored for the user. **Added** an entry in the TODO/NOTE section to consider adding `awsc_uuid` to store the AWS Cognito uuid (which can be different from the username).
- 2019-07-25: Changed `accepts_marketing` type from `bool` to `string` (a string representation of bool). See Notes section below for details.
- 2019-07-19: **Changed** the primary identifier for a user to `email`. The reason is that if dyanmoDB (key-value store) is keyed from the email, the email is unique. All user identifier links will be updated to hold the email instead of ID. **Removed** `id` as not needed anymore. **Updated** the purpose, definitions, and json representation to be accurate.
- 2019-01-16: **Removed** `password` as not needed. **Added** `api_key` in `platform` and `jwt_token`. **Changed** `updated_at` to `last_modified`.
- 2019-07-12: Added the `id` field to a user's address because we need an identifier.
- 2019-06-28: Removed the product information (it went to its own model). Removed a confusing top level key for clarity.
- 2019-06-25: Again changed `purchased_skus` back to `purchased_products` to include needed info. Also updated organizations to objects include ID.
- 2019-06-20: Reverted back to `purchased_skus` (from `purchased_products` which had more info) after creating [the Organizations flow - see Organizations tab](https://drive.google.com/file/d/1cyy6wLrcHLeN8LrffshQYgKdVKkuexhJ/view?usp=sharing) and realizing that there was no need to contact the ecommerce store directly (duh).
- 2019-06-20: Added Organizations to the User Object Model.
- 2019-06-19: Original proposal complete. Added link to ticket and notified appropriate parties.

## Purpose

The User Object Model is the representation of the user to/from the User Service. It holds all relevant user data for business needs.

## Definitions

- `email` _(string)_: The email address of the user. **Primary Key**.
- `first_name` _(string)_: This is the user's first name.
- `last_name` _(string)_: This is the user's last name.
- `name` _(string)_: The user's first and last names.
- `verified_email` _(string representation of a bool)_: User has verified their email address.
- `phone` _(string)_: The phone number (in [E.164 format](https://en.wikipedia.org/wiki/E.164)) for the user.
- `addresses` _(object of embedded objects|map of maps)_: This is a object of address objects that belong to the user. The key for each map is:
    - `id` _(int)_: A unique identifier for the address from **Shopify** _(or potentially other future places where addresses are used)_.
    - Each platform has the following attributes:
        - `input_from` (string): Where the user originally input this address.
            - `input_from` can be any of the following options:
                - `PLATFORM_UNKNOWN` _(default)_
                - `PLATFORM_SHOPIFY`
                - `PLATFORM_DIGITAL_LIBRARY`
                - `PLATFORM_THINKIFIC`
        - `company` _(string)_: The user's company.
        - `address1` _(string)_: The user's mailing address.
        - `address2` _(string)_: An additional field for the user's mailing address.
        - `city` _(string)_: The user's city, town, or village.
        - `postal_code` _(string)_: The user's postal code, also known as zip, postcode, Eircode, etc.
        - `region` _(string)_: The user's region name. Typically a province, a state, or a prefecture.
        - `region_code` _(string)_: The code for the region of the address, such as the province, state, or district. For example QC for Quebec, Canada or CO for Colorado, US.
        - `country` _(string)_: The user's country.
        - `country_iso_2` _(string)_: The two-letter country code corresponding to the user's country.
        - `default` _(string representation of bool)_: Whether this address is the default address for the user.
- `state` _(string)_: The state of a user's general account. Options are:
    - `USER_ENABLED` _(default)_
    - `USER_DISABLED`
    - `USER_SUSPENDED`
- `note` _(string)_: A note about the user's account.
- `registered_from` _(string)_: Where the user originally registered from. Options:
    - `REGISTRATION_UNKNOWN` _(default)_
    - `REGISTRATION_SHOPIFY`
    - `REGISTRATION_DIGITAL_LIBRARY`
    - `REGISTRATION_GOOGLE`
    - `REGISTRATION_FACEBOOK`
    - `REGISTRATION_TWITTER`
    - [other platforms to be added when needed]
- `platforms` _(object of embedded objects|map of maps)_: This is a object of platform objects that the user has connected to (has accounts on). The key for each map is:
    - `name` _(string)_: This is the name of the platform.
        - `name` can be any of the following options:
            - `PLATFORM_UNKNOWN` _(default)_
            - `PLATFORM_SHOPIFY`
            - `PLATFORM_DIGITAL_LIBRARY`
            - `PLATFORM_THINKIFIC`
    - Each platform has the following attributes:
        - `platform_user_id` _(string)_: This is the user's identifier for the particular platform (if they require an ID).
        - `api_key` _(string)_: The user's API key for the particular platform.
        - `active` _(string representation of bool)_: is the user account active on this particular platform.
        - `preferences` _(object) (optional)_: user preferences for the particular platform. _**NOTE:** IGNORED IN MVP._
- `organizations` _(object|map)_: An object of `organization_id` as the `key` and its `name` as the `value`
- `multipass_token` _(string)_: A hash string of a Shopify MultiPass Token (generated by the Multipass Token Service).
- `accepts_marketing` _(string representation of bool)_: Whether the user has consented to receive marketing material via email.
- `accepts_marketing_updated_at` _(string)_: The UTC datetime (e.g. "2019-06-10 00:00:00") when the user consented or objected to receiving marketing material by email.
- `marketing_opt_in_level` _(string)_: The marketing subscription opt-in level (as described by the [M3AAWG best practices guideline](https://www.m3aawg.org/sites/maawg/files/news/M3AAWG_Senders_BCP_Ver3-2015-02.pdf) (see Section 2.1)) that the customer gave when they consented to receive marketing material by email. If the customer does not accept email marketing, then this property will be set to null. Valid values:
    - `MARKETING_UNKNOWN` _(default)_
    - `MARKETING_SINGLE`
    - `MARKETING_SINGLE_WITH_NOTIFICATION`
	- `MARKETING_CONFIRMED`
- `created_at` _(string)_: string representation of user creation UTC datetime (e.g. "2019-06-10 00:00:00").
- `last_modified` _(string)_: string representation of user updated UTC datetime (e.g. "2019-06-10 00:00:00").

## TODO (to implement)

- Possibly store the AWS Cognito UUID as something like: `awsc_uuid` _(string)_: The AWS Cognito UUID which will be sent as part of the JWT claims.

## Notes

- `string` representations of `bool`s are necessary due to the way `proto3` implements it's defaults. There are "limitations" around default values of scalar values (e.g. booleans) as defined in https://developers.google.com/protocol-buffers/docs/proto3#default _("Also note that if a scalar message field is set to its default, the value will not be serialized on the wire.")_.
    - We only use this `string` representation if the `bool` value can flip/flop.
    - We still use type `bool` for `verified_email` because it should never change back to its default value of `false`.


## JSON Representation (with example values) (minus comments)

_**NOTE:** The following is a representation of a **single** User object._
```jsonc
{
    "user": {
        "email": "johndoe@example.com",
        "first_name": "John",
        "last_name": "Doe",
        "name": "John Doe",
        "verified_email": "true",
        "phone": "111-111-1111",
        "addresses": [
            "207119551": {
                "input_from": "PLATFORM_SHOPIFY",
                "company": "Sounds True",
                "address1": "413 S Arthur Ave",  // address2 omitted
                "city": "Louisville",
                "postal_code": "80027",
                "region": "Colorado",
                "region_code": "CO",
                "country": "United States",
                "country_iso_2": "US",
                "default": "false"
            },
            "207119552": {
                "input_from": "PLATFORM_SHOPIFY",
                "company": "",
                "address1": "123 Main St",
                "address2": "Apt. 4",
                "city": "New York",
                "postal_code": "10002",
                "region": "New York",
                "region_code": "NY",
                "country": "United States",
                "country_iso_2": "US",
                "default": "true"
            }
        ],
        "state": "USER_ENABLED",
        "note": "This customer is loyal",
        "registered_from": "REGISTRATION_SHOPIFY",
        "platforms": {
            "PLATFORM_SHOPIFY": {
                "platform_user_id": "9876543210",
                "active": "true"
            },
            "PLATFORM_THINKIFIC": {
                "platform_user_id": "56789123540",
                "active": "true",
                "api_key": "2CA7DFA6-74EB-4096-B68A-7C398E827AC6"
            },
            "PLATFORM_DIGITAL_LIBRARY": {
                "platform_user_id": "johndoe@example.com",  // same as user ID (email)
                "active": "true",
                "api_key": "D437B971-178B-4E37-974F-FA0C6808900A",
                "preferences": {
                    // NOT IN MVP
                    "download_wifi_only": "false",
                    "ui_theme": "sometheme",
                    "video_quality": "high",
                    "audio_quality": "medium"
                },
            },
        },
        "organizations": {
            "101": "US Senate"
        },
        "multipass_token": "sVSwcIWAz_DKCEKWVqtzUxn5ZO3tj_DsORtNKG56soNs5mk5tIStJqIBwobSd6DQzBBj7Q9pZWDmiVHLBwWbPq8d3bcfrMF8uk_rapZGlMUF4Hhed-Iwq2d5y2i1Vl5VU3lHKmVtZJro52yXEboYxjTCZoBnM5d959k_A4LeWS6G6xHLmMzMNTnuN1v8QSU8z-V7QDY8AL6WYGLT_n8Pyg==",
        "accepts_marketing": "false",
        "accepts_marketing_updated_at": "2019-06-10 00:00:00",
        "marketing_opt_in_level": "MARKETING_UNKNOWN",
        "created_at": "2019-06-10 00:00:00",
        "last_modified": "2019-06-10 00:00:00"
    }
}
```