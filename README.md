[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg)](LICENSE)

This package is the continuations of the abandoned package, [pwparsons/paygate](https://packagist.org/packages/pwparsons/paygate). It provides an easy way to integrate PayGate's PayWeb3 API with Laravel.

PayGate's official documentation can be found [here](http://docs.paygate.co.za/#payweb-3).

 Compatibility Chart
--------------------------------------------------------------------------------

| Package Version | Laravel      | PHP  |
|-----------------|--------------|------|
|    **0.0.1**    | 10.0+        | 8.2+ |

## Installation

You can install this package via composer using:

```bash
composer require kajuzi/laravel-paygate-payweb-3
```
Add the following variables to your `.env` file and give them correct values for your PayGate account and setup:

``` 
PAYGATE_ID=
PAYGATE_SECRET=
PAYGATE_RETURN_URL=
PAYGATE_NOTIFY_URL=
```

Optionally, you may also set:

```
PAYGATE_CURRENCY=
PAYGATE_COUNTRY=
PAYGATE_LOCALE=
PAYGATE_RETURN_URL=
```

The package will automatically register itself.

If you want to publish the config file to `config/paygate.php` run:

```bash
php artisan vendor:publish --provider="PWParsons\PayGate\PayGateServiceProvider"
```

## Usage

After you've installed the package and filled in the values in the config file working with this package will be a breeze. All the following examples use the facade.

### Creating a transaction

```php
// Initiate transaction
$http = PayGate::initiate()
               ->withReference('pgtest_123456789')
               ->withAmount(32.99)
               ->withEmail('email@example.com')
               ->withCurrency('USD') // Optional: defaults to ZAR
               ->withCountry('USA') // Optional: defaults to ZAF
               ->withLocale('en-us') // Optional: defaults to 'en'
               ->withReturnUrl('https://website.com/return_url')
               ->withNotifyUrl('https://website.com/notify_url') // Optional
               ->create();

if ($http->fails()) {
    dump($http->getErrorCode());
    dump($http->getErrorMessage());
    dump($http->all());
}

// Redirect to PayGate's payment page
return PayGate::redirect();
```

An example of the initiate response can be found in the [documentation](http://docs.paygate.co.za/#response).

### Querying a transaction

```php
$http = PayGate::query()
               ->withPayRequestId('YOUR_PAY_REQUEST_ID_HERE')
               ->withReference('pgtest_123456789')
               ->create();

if ($http->fails()) {
    dump($http->getErrorCode());
    dump($http->getErrorMessage());
    dump($http->all());
}

dd($http->all());
```

An example of the query response can be found in the [documentation](http://docs.paygate.co.za/#response-2).

### Tip

Paygate will post to the RETURN_URL and NOTIFY_URL. Exclude these URI's from CSRF verification by adding them to the VerifyCsrfToken middleware. E.g.

```php
class VerifyCsrfToken extends Middleware
{
    protected $except = [
        'return_url',
        'notify_url',
    ];
}
```

### Helpful Methods

The `with` magic method allows you to set a string after the word 'with' provided within the object it is being called on. This works in exactly the same way as the magic getter except it sets field values and returns the object so that you can chain setters, for example:

```php
$object->withReference('pgtest_123456789')
       ->withAmount(32.99)
       ->withEmail('email@example.com')
       ->withReturnUrl('https://my.return.url/page');
```

Will result in the following:

```json
{
    "REFERENCE": "pgtest_123456789",
    "AMOUNT": "3299",
    "EMAIL": "email@example.com",
    "RETURN_URL": "https://my.return.url/page"
}
```

The `get` magic method allows you to call any string after the word 'get' and it will return that value, for example:

```json
{
    "PAYGATE_ID": "10011072130",
    "PAY_REQUEST_ID": "23B785AE-C96C-32AF-4879-D2C9363DB6E8",
    "REFERENCE": "pgtest_123456789"
}
```

Getting data from the object:

```php
echo $object->getPaygateId();       // 10011072130
echo $object->getPayRequestId();    // 23B785AE-C96C-32AF-4879-D2C9363DB6E8
echo $object->getReference();       // pgtest_123456789
```

## Change log

Please see the [changelog](CHANGELOG.md) for more information on what has changed recently.

## Contributing

Please see [contributing.md](CONTRIBUTING.md) for details and a todolist.

## Credits

- [Peter Parsons](https://github.com/pwparsons)
- [All Contributors](../../contributors)

## License

The MIT License (MIT). Please see the [license file](LICENSE.md) for more information. 
