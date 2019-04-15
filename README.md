# Paystack Mirror
The missing Paystack PHP Library. 
___
## Overview
Paystack Mirror is a clean, simple and fluent php library for paystack payment gateway. This library is birthed out of the fact that i needed something flexible than what is in existance for myself and the php community.

### What was wrong with the existing one?
The official paystack library for php is the yabacon/paystack-php on github. It's cool. Supports almost all paystack end-points and some other cool features like a dedicated Fee class, MetaDataBuilder class and Event handler class. Many people have been using the library but i wanted something that can give people freedom over what they do. I wanted something that supports multiple accounts at once. It's why i decided to work on this library. 

### What is unique about the library?
1. The conversion of paystack end-points to full fletch extendable php classes.
2. Supports for multiple accounts
3. Clean and better event handler class
4. Fluent Params Builder class
5. Nigerian money conversion e.g 1k => 1,000 naira and 1,000 naira => 100000 kobo and even (millions(xM), billions(xB), trillions(xT)) to kobo e.t.c. 

## Server Requirements
> PHP version ^7.1.3 requirement was done intentionally. Reason been that it's safer, better and faster than 5.6 and 7.0. So, if you have been using php version less than that, kindly upgrade before using this library.
* php ^7.1.3
* cURL extension enabled
* OpenSSL extension enabled

> This library has suppports for all Paystack end-points which we refers to as **Actions**. _Let's take a look at the available actions._

## List of actions groups
Below are actions groups supported by the library in alphabetical order.

1. [BULK CHARGES](docs/pages/bulk-charges.md)
2. [CHARGES](docs/pages/charges.md)
3. CONTROL PANEL
4. CUSTOMERS
5. INVOICES
6. MISCELLANEOUS
7. PAGES
8. PLANS
9. REFUNDS
10. SETTLEMENTS
11. SUB-ACCOUNTS
12. SUBSCRIPTIONS
13. TRANSACTIONS
14. TRANSFER RECIPIENTS
15. TRANSFERS
16. TRANSFERS CONTROL
17. VERIFICATIONS

## Installation
```
composer require coderatio/paystack-mirror
```

## Usage
### With Single Paystack Account

_Method One_
```php

require 'venodr/autoload.php';

use Coderatio\PaystackMirror\PaystackMirror;
use Coderatio\PaystackMirror\Actions\Transactions\ListTransactions;

$queryParams = new ParamsBuilder();
$queryParams->perPage = 10;

$result = PaystackMirror::run(string $secretKey, new ListTransactions($queryParams));

echo $result->getResponse();

```

_Method Two_
```php

require 'venodr/autoload.php';

use Coderatio\PaystackMirror\PaystackMirror;
use Coderatio\PaystackMirror\Actions\Transactions\ListTransactions;

$queryParams = new ParamsBuilder();
$queryParams->perPage = 10;

$result = PaystackMirror::run(string $secretKey, ListTransactions::class, $queryParams);

echo $result->getResponse();

```

_Method Three_
```php

require 'venodr/autoload.php';

use Coderatio\PaystackMirror\PaystackMirror;
use Coderatio\PaystackMirror\Actions\Transactions\ListTransactions;

$queryParams = new ParamsBuilder();
$queryParams->perPage = 10;

$result = PaystackMirror::setKey(string $secretKey)->mirror(new ListTransactions($queryParams));

echo $result->getResponse();

```

_Method Four_
```php

require 'venodr/autoload.php';

use Coderatio\PaystackMirror\PaystackMirror;
use Coderatio\PaystackMirror\Actions\Transactions\ListTransactions;

$queryParams = new ParamsBuilder();
$queryParams->perPage = 10;

$result = PaystackMirror::setKey(string $secretKey)->mirror(Action ListTransactions::class, $queryParams);

echo $result->getResponse();

```
>**Notice:** By default, `->getResponse()` returns a json object. But, you can chain `->asArray()` to convert the response to php `array` or `->asObject()` to conver the response to php `object` at runtime.

### With Multiple Paystack Accounts
With this library, you can mirror single action on multiple paystack accounts. This is super cool for multitenants applications or firms with multiple paystack accounts.

Let's see how to do it.
```php

// Let's use ParamsBuilder to build our data to be sent to paystack.

// First account
$firstAccountParams = new ParamsBuilder();
$firstAccountParams->first_name = 'Josiah';
$firstAccountParams->last_name = 'Yahaya';
$firstAccountParams->email = 'example1@email.com';
$firstAccountParams->amount = short_naira_to_kobo('25.5k');
$firstAccountParams->reference = PaystackMirror::generateReference();

$firstAccount = new ParamsBuilder();
$firstAccount->key = $firstAccountKey;
$firstAccount->data = $firstAccountParams;

// Second account
$secondAccountParams = new ParamsBuilder();
$secondAccountParams->first_name = 'Ovye';
$secondAccountParams->last_name = 'Yahaya';
$secondAccountParams->email = 'example2@email.com';
$secondAccountParams->amount = short_naira_to_kobo('10k');
$secondAccountParams->reference = PaystackMirror::generateReference();

$secondAccount = new ParamsBuilder();
$secondAccount->key = $secondAccountKey;
$secondAccount->data = $firstAccountParams;

$results = PaystackMirror::setAccounts([$firstAccount, $secondAccount])
    ->mirrorMultipleAccountsOn(new InitializeTransaction());
    
// OR

$results = PaystackMirror::setAccounts([$firstAccount, $secondAccount])
    ->mirrorMultipleAccountsOn(InitializeTransaction::class);

foreach ($results as $result) {
    // Do something with $result.
    
   ...
   
   // The $result variable holds two main object properties; 
   // $result->account which holds an account key and $result->response which holds the response for an account. 
   // The best thing to do is to dump the $result variable to see what's contain there in.
}
```

You can overwrite all the accounts data by providing your params on the action. When that is done, the library will use the parameters supplied on the action for all the accounts instead e.g
```php

    $actionParams = new ParamsBuilder();
    $actionParams->email = 'johndoe@email.com';
    $actionParams->amount = naira_to_kobo('1,000');
    $actionParams->reference = PaystackMirror::generateReference();

    $results = PaystackMirror::setAccounts([$firstAccount, $secondAccount])
        ->mirrorMultipleAccountsOn(new InitializeTransaction($actionParams));
        
    // OR
    
    $results = PaystackMirror::setAccounts([$firstAccount, $secondAccount])
            ->mirrorMultipleAccountsOn(InitializeTransaction::class, $actionParams);
        
```
>**Quick Note:** All the query or body params used on paystack api documentation site are all available in this library. The only different is, they must be sent as an array or as `ParamBuilder::class` object. 

## Writing your action
One good thing about this library is the ability to plug and play actions. You can replace existing actions by creating yours. 

```php
<?php

use \Coderatio\PaystackMirror\Actions\Action;

class MyCustomAction extends Action
{
    // The paystack endpoint for this action
    protected $url = '';
    
    public function handle(CurlService $curlService) : void {
        // Use the $curlService to handle this action's request.
    }
}

``` 

>Please note that `$this->data` property is an array. If you want to sent this as json to paystack, use `$this->getData()`.


>**Notice:** You can use the `ParamsBuilder::class` to build the paramters you want to send to paystack. Take a look at below:

```php

    $postParams = new ParamsBuilder();
    $postParams->email = 'example@email.com';
    $postParams->first_name = 'Josiah';
    $postParams->last_name = 'Yahaya';
    $postParams->reference = PaystackMirror::generateReference();
    $postParams->amount = PaystackMirror::shortNairaToKobo('25k');
    
    // Then pass it to your action
    
    PaystackMirror::setKey($yourKey)->mirror(new InitializeTransaction($postParams));
  
```
