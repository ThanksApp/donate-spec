# The Donate Spec

This is a specification for programatically defining how contibutors to open source software prefer to receive donations to their projects. The aim is to be platform agnostic and as flexible as possible, supporting projects ranging from individual developers to large projects where responsibilities may be split.

- [Examples](#examples)
  * [1. Simple donate spec](#1-simple-donate-spec)
  * [2. Muliple recipients](#2-muliple-recipients)
  * [3. Multiple platforms](#3-multiple-platforms)
  * [4. Reward Tiers](#4-reward-tiers)
- [Flow Type Defintion](#flow-type-defintion)
- [Try It Out](#try-it-out)
  * [NPM](#npm)
  * [All other software](#all-other-software)
- [Other bits](#other-bits)
  * [File precedence](#file-precedence)
  * [Weights](#weights)
  * [Common Platform Strings](#common-platform-strings)
- [Contributing](#contributing)

## Examples

### 1. Simple donate spec

The simplest spec in a package.json would look like this:
```json
{
  "name": "alice-bob-crypto",
  "version": "0.0.1",
  "description": "The only encrypted messaging suite you'll ever need",
  "donate": "givethanks.app/u/alice"
}
```
Donations would be sent to the user "alice" on [givethanks.app](https://givethanks.app)

### 2. Muliple recipients
If you'd like to split donations between multiple parties, you can define recipient objects:
```json
  ...
  "donate": {
    "recipients": [
      {
        "weight": 70,
        "platform": "Thanks",
        "address": "https://givethanks.app/u/alice"
      },
      {
        "weight": 30,
        "platform": "Thanks",
        "address": "https://givethanks.app/u/bob"
      }
    ]
  }
```
Note that if you do not define a `weight`, payments are split evenly between all recipients. Any `platform` string is valid, but it's up the donation system that uses the spec to handle it. Ideally the donation is split between all platforms, but a donating user is of course free to donate to -either- Alice, Bob, or both. Additionally, not all donation systems may support all payment platforms, for example, givethanks.app doesn't support bitcoin just yet and the donation would go to Alice.

### 3. Multiple platforms
A single user can specify many platforms (only one platform per user is ever selected by the payment system, and priority is given in the order they're listed)
```json
  ...
  "donate": {
    "recipients": [
      {
        "name": "Alice Liddell",
        "weight": 70,
        "platforms": [
          {
            "platform": "Thanks",
            "address": "https://givethanks.app/u/alice"
          },
          {
            "platform": "Bitcoin",
            "address": "3QJmV3qfvL9SuYo34YihAf3sRCW3qSinyC"
          }
        ]
      },
      {
        "name": "Bob Loblaw",
        "weight": 30,
        "platform": "Bitcoin",
        "address": "3QJmV3qfvL9SuYo34YihAf3sRCW3qSinyC"
      }
    ]
  }
```

### 4. Reward Tiers
A project can specify reward tiers as an incentive to donate. The donation system can show these tiers on the project's page, but the rewards should ultimately be fulfilled by the project owners.
```json
  ...
  "donate": {
    "platform": "Thanks",
    "address": "https://givethanks.app/u/alice",
    "reward": {
      "currency": "USD",
      "tiers": [
        {
          "threshold": 5,
          "name": "Backer",
          "frequency": "any",
          "description": "If we ever meet in person, your drink is on me!"
        },
        {
          "threshold": 50,
          "name": "Gold Sponsor",
          "frequency": "monthly",
          "description": "Stickers, plus your name (or your company's name) listed in the credits on our README!"
        }
      ]
    }
  }
```

## Flow Type Defintion
This [flow type](https://flow.org/) definition documents every possible combination of valid parameters in a donate spec.

```flow
type simple = string

type explicit = {
  platform: string,
  address: string,
  name?: string,
  email?: string,
  weight?: number,
  reward?: reward
}

type explicitMultiPlatform = {
  platforms: Array<{
    platform: string,
    address: string
  }>,
  name?: string,
  email?: string,
  weight?: number,
  reward?: reward
}

type multiRecipient = {
  recipients: Array<simple|explicit|explicitMultiPlatform>
}

type reward = {
  currency?: string,
  tiers?: Array<rewardTiers>
}

type rewardTier = {
  threshold: number,
  name: string,
  frequency: 'any'|'monthly',
  description": string
}

export type donateSpecType = simple|explicit|multiRecipient|explicitMultiPlatform
```

## Try It Out

#### NPM
If your software is published on NPM, once you've added the `donate` field to your package.json, you'll need to publish your lastest version. Almost immediately, your donate info should be live at `https://givethanks.app/donate/npm/PACKAGE_NAME`

#### All other software
Once your `package.json` or `.donate.json` is pushed up to GitHub, you can see your updated project immediately at `https://givethanks.app/donate/github/AUTHOR/REPO`

## Other bits

### File precedence
- If you have a `package.json`, the `"donate"` field will always be used.
- If you do not have a `package.json` or it doesn't have a `"donate"` field, you can add a `.donate.json` file.

- If your package is published on NPM, the spec found there takes precedence over the one found in the project's GitHub, so don't forget to publish a new version if you update your donate spec.

### Weights
Weights can be defined arbitrarily, and do not have to total any specific amount. For example, a 70 / 30 split is just as valid as 16 / 9. The parser is ultimately responsible for calculating weights as a fraction of the total weight of all recipients.

### Common Platform Strings
While all platform strings are valid, this is a list of commonly used ones for ease of parsing.


| Platform Name | String |
|------------------------------------------|--------------------|
| [GiveThanks.app](https://givethanks.app) | `'Thanks'`         |
| Bitcoin                                  | `'Bitcoin'`        |
| PayPal                                   | `'PayPal'`         |
| Patreon                                  | `'Patreon'`        |
| Open Collective                          | `'OpenCollective'` |
| Liberapay                                | `'Liberapay'`      |
| Buy me a coffee                          | `'BuyMeACoffee'`   |

## Contributing
If you have ideas on how to improve this spec, please feel free to submit a pull request!
