# Business Rules

## Transaction Types

|       Type      | MerchantID | OriginalTransactionID |
|-----------------| -----------|-----------------------|
| PURCHASE        | Required   | NULL                  |
| PAYMENT         |  NULL      | NULL                  |
| REFUND          | Required   | Required              |
| REVERSAL        | Required   | Required              |
| CASH_WITHDRAWAL | NULL       | NULL                  |
| FEE             | NULL       | NULL                  |
| ADJUSTMENT      | NULL       | NULL                  |

## Transaction Relationships

| Type              | Meaning                                  |
| ----------------- | ---------------------------------------- |
| `PURCHASE`        | Purchase made using the card             |
| `PAYMENT`         | Payment made toward the customer account |
| `REFUND`          | Refund received from a merchant          |
| `REVERSAL`        | Reversal of a previous transaction       |
| `CASH_WITHDRAWAL` | Cash withdrawal                          |
| `FEE`             | Fee charged to the customer              |
| `ADJUSTMENT`      | Manual account adjustment                |

- Any transaction belongs to a card.
- MerchantID is nullable because not every transaction is merchant-related.
- Refunds and reversals reference an original purchase.
- Refund/reversal transactions use the same card and merchant as their original purchase.

## Transaction Status

| Status     | Meaning                               |
| ---------- | ------------------------------------- |
| `PENDING`  | Transaction is awaiting processing    |
| `APPROVED` | Transaction was successfully approved |
| `DECLINED` | Transaction was declined              |
| `REVERSED` | Transaction was reversed              |


## Archival Rule

Transactions older than 40 days will eventually be eligible for archival into `TransactionFlow_History`.

## Dataset

The current target workload is 5,000,000 transactions distributed across the transaction types as follows:
| Type            |         Count |
| --------------- | ------------- |
| PURCHASE        |     3,000,000 |
| PAYMENT         |       750,000 |
| REFUND          |       500,000 |
| REVERSAL        |       250,000 |
| CASH_WITHDRAWAL |       250,000 |
| FEE             |       150,000 |
| ADJUSTMENT      |       100,000 |
| **Total**       | **5,000,000** |

## Data quality & validation

Before we trust the platform, we validate:

- No orphan Card references.
- No orphan Merchant references.
- Refund/Reversal has a valid original transaction.
- Original transaction is a Purchase.
- Card and Merchant match the original.
- Related transaction happens after the original.
