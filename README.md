# nostr-php

![CI](https://github.com/swentel/nostr-php/actions/workflows/ci.yml/badge.svg)
![Packagist PHP Version](https://img.shields.io/packagist/dependency-v/swentel/nostr-php/php)
![GitHub contributors](https://img.shields.io/github/contributors/swentel/nostr-php)
![GitHub issues](https://img.shields.io/github/issues/swentel/nostr-php)
![GitHub last commit (branch)](https://img.shields.io/github/last-commit/swentel/nostr-php/main)

This is a PHP Helper library for Nostr.
More info about Nostr: https://github.com/nostr-protocol/nostr.

## Installation

To use the package in your PHP project with Composer:

```console
$ composer require swentel/nostr-php
```

Install dependencies if you would like to test / code some things out for yourself with the code example snippets below. 

```console
$ composer install
```

## Create an event

This will create an event object with a short text message (kind 1).

```php
use swentel\nostr\Event\Event;

$note = new Event();
$note->setKind(1);
$note->setContent('Hello world!');
$note->setTags([
  ['e', $relayUrl],
  ['p', $public_key, $relayUrl],
  ['r', $relayUrl],
]);
// or use addTag()
$note->addTag(['p', $public_key, $relayUrl]);
```

## Signing an event

Generates the id and signature for an event. The 'pubkey', 'id' and 'sig' 
properties are added to the event object.

```php
use swentel\nostr\Event\Event;
use swentel\nostr\Sign\Sign;

$note = new Event();
$note->setContent('Hello world!');
$note->setKind(1);

$signer = new Sign();
$signer->signEvent($note, $private_key);
```

## Generating a message

Generate an event message : `["EVENT", <event JSON as created above with id and sig>]`

```php
use swentel\nostr\Sign\Sign;
use swentel\nostr\Message\EventMessage;

$signer = new Sign();
$signer->signEvent($note, $private_key);

$eventMessage = new EventMessage($note);
$message_string = $eventMessage->generate();
```

## Interacting with a relay

Publish an event with a note that has been prepared for sending to a relay.

```php
use swentel\nostr\Event\Event;
use swentel\nostr\Message\EventMessage;
use swentel\nostr\Relay\Relay;

$note = new Event();
$note->setContent('Hello world');
$note->setKind(1);

$signer = new Sign();
$signer->signEvent($note, $private_key);

$eventMessage = new EventMessage($note);

$relayUrl = 'wss://nostr-websocket.tld';
$relay = new Relay($relayUrl, $eventMessage);
$result = $relay->send();
```

## Generating a private key and a public key

```php
use swentel\nostr\Key\Key;

$key = new Key();

$private_key = $key->generatePrivateKey();
$public_key  = $key->getPublicKey($private_key);

```

## Converting keys

Convert bech32 encoded keys (npub, nsec) to hex.

```php
use swentel\nostr\Key\Key;

$public_key = 'npub10elfcs4fr0l0r8af98jlmgdh9c8tcxjvz9qkw038js35mp4dma8qzvjptg';
$key = new Key();
$hex = $key->convertToHex($public_key);
```

Convert hex keys to bech32 (npub, nsec).

```php
use swentel\nostr\Key\Key;

$public_key = '7e7e9c42a91bfef19fa929e5fda1b72e0ebc1a4c1141673e2794234d86addf4e';
$private_key = '67dea2ed018072d675f5415ecfaed7d2597555e202d85b3d65ea4e58d2d92ffa';
$key = new Key();
$bech32_public = $key->convertPublicKeyToBech32($public_key);
$bech32_private = $key->convertPrivateKeyToBech32($private_key);
```

## Run tests

All tests can be found in `tests`.

```console
$ php vendor/bin/phpunit
```

## Documentation with phpDocumentor

Generate documentation with [phpDocumentor](https://phpdoc.org/).

```console
$ phpdoc 
```

All documentation is saved in the `phpdoc.nostr-php.dev` directory where the `index.html` can be opened in any browser.
This directory also serves as the root directory for https://phpdoc.nostr-php.dev. 

The documentation of phpDocumentor can be found at https://docs.phpdoc.org/.

## nostr-php script (cli client)

The library ships with a simple CLI client (`bin/nostr-php`) to post a short text note to a Nostr relay.

```console
Usage:
$ bin/nostr-php --content "Hello world!" --key /home/path/to/nostr-private.key --relay wss://nostr.pleb.network
```

Note: the key arguments expects a file with your private key! Do not paste your
private key on command line.

## Roadmap

- [x] Keypair generation and validation
  - [x] Convert from hex to bech32-encoded keys
- [x] Event signing with Schnorr signatures (`secp256k1`)
- [x] Event validation (issue [#17](https://github.com/swentel/nostr-php/issues/17))
- [ ] Support NIP-01 basic protocol flow description
  - [x] Publish events
  - [ ] Request events (pr [#48](https://github.com/swentel/nostr-php/pull/48))
- [ ] Improve handling relay responses
- [ ] Support NIP-19 bech32-encoded identifiers
- [ ] Support NIP-42 authentication of clients to relays
- [ ] Support NIP-45 event counts
- [ ] Support NIP-50 search capability
- [ ] Support multi-threading for handling requests simultaneously

## Community

If you need any help, please join this Telegram group: https://t.me/nostr_php

## Funding

In May 2024 OpenSats granted Sebastian Hagens for further development of this library for one year. If you would like to support this project with a donation, you could send some lightning sats to `sebastian@lnd.sebastix.com` or on-chain to `bc1p3p6jq2sxsf650lgllv57st9h97xj37fflg5t8d265saz6yqzcdyqd7pzun`. 

## Maintainers
 
* [@sebastix](https://github.com/Sebastix)  `npub1qe3e5wrvnsgpggtkytxteaqfprz0rgxr8c3l34kk3a9t7e2l3acslezefe`
* [@swentel](https://github.com/swentel) (original author, inactive)  `npub1z8n2zt0vzkefhrhpf60face4wwq2nx87sz7wlgcvuk4adddkkycqknzjk5`
