# CHARGES
Send card details or bank details or authorization code to start a charge.

## 1. TokenizePaymentInstrument Action
```php
// Class
Coderatio\PaystackMirror\Actions\Charges\TokenizePaymentInstrument::class
```
Send an array of objects with authorization codes and amount in kobo so paystack can process transactions as a batch.
### Body Params

* **email** (required) - Customer's email address
* **card*** (required) - Card to tokenize
* **card['number']** (required) - Card to tokenize
* **card['cvv']** (required) - Card security code
* **card['expiry_month']** (required) - Expiry month of card
* **card['expiry_year']** (required) - Expiry year of card
