# BULK CHARGES
```php
Coderatio\PaystackMirror\Actions\BulkCharges\...
```
| Action        | Description  | Request Type  |
| ------------- |:-------------:| -----:|
| `InitializeBulkCharge`      | Send an array of objects with authorization codes and amount in kobo so paystack can process transactions as a batch. | `POST` |
| `ListBulkChargeBatches`     | This action lists all bulk charge batches created by the integration. Statuses can be `active`, `paused`, or `complete`.      |   `GET` |
| `FetchBulkChargeBatch` | This action retrieves a specific batch code. It also returns useful information on its progress by way of the `total_charges` and `pending_charges` attributes.      |    `GET` |
| `FetchChargesBatch` | This action retrieves the charges associated with a specified batch code. Pagination parameters are available. You can also filter by status. Charge statuses can be `pending`, `success` or `failed`.     |    `GET` |
| `PauseBulkChargeBatch` | Use this action to pause processing a batch     |    `GET` |
| `ResumeBulkChargeBatch` | Use this action to resume processing a batch     |    `GET` |

[Read more here...](https://developers.paystack.co/v1.0/reference#initiate-bulk-charge)
___