# Data Dictionary

Synthetic analytical warehouse for a digital-wallet company. Grain, columns, keys, and example values for every table.

## dim_campaign

**Grain:** One row per campaign.

| Column | Type | Description | Primary Key | Foreign Key | Example |
|---|---|---|---|---|---|
| campaign_id | string | Unique campaign identifier | PK |  | CMP000001 |
| campaign_name | string | Human-readable campaign name |  |  | New User Cashback |
| campaign_type | string | Category of campaign strategy |  |  | Acquisition |
| team | string | Owning team (Marketing or A&G) |  |  | A&G |
| objective | string | Business objective of the campaign |  |  | Acquire new customers |
| start_date | date | Campaign start date |  |  | 2025-04-10 |
| end_date | date | Campaign end date |  |  | 2025-04-30 |
| budget | decimal | Planned campaign budget (MMK) |  |  | 12000000 |
| target_customer_count | integer | Planned number of customers to reach |  |  | 3000 |
| target_transaction_count | integer | Planned number of transactions generated |  |  | 6000 |
| target_transaction_amount | decimal | Planned transaction value generated (MMK) |  |  | 150000000 |
| status | string | Operational status |  |  | Completed |

## dim_promotion

**Grain:** One row per promotion belonging to a campaign.

| Column | Type | Description | Primary Key | Foreign Key | Example |
|---|---|---|---|---|---|
| promotion_id | string | Unique promotion identifier | PK |  | PRM000001 |
| campaign_id | string | Owning campaign |  | dim_campaign.campaign_id | CMP000001 |
| promotion_name | string | Promotion display name |  |  | 10% Cashback |
| promotion_type | string | Type of promotion |  |  | Cashback |
| reward_type | string | How the reward is calculated |  |  | Percentage |
| reward_value | decimal | Reward magnitude (percent or fixed MMK) |  |  | 10 |
| minimum_transaction_amount | decimal | Minimum spend to qualify (MMK) |  |  | 5000 |
| maximum_reward_amount | decimal | Cap on reward value (MMK) |  |  | 5000 |
| start_date | date | Promotion start date |  |  | 2025-04-10 |
| end_date | date | Promotion end date |  |  | 2025-04-30 |

## dim_customer

**Grain:** One row per customer.

| Column | Type | Description | Primary Key | Foreign Key | Example |
|---|---|---|---|---|---|
| customer_id | string | Unique customer identifier | PK |  | CUS000001 |
| registration_date | date | Date the customer registered |  |  | 2024-06-12 |
| customer_segment | string | Value/behavioral segment |  |  | High Value |
| customer_type | string | Type of account |  |  | Individual |
| acquisition_channel | string | How the customer was acquired |  |  | Campaign |
| city | string | City of residence |  |  | Yangon |
| region | string | Region/state |  |  | Yangon Region |

## dim_merchant

**Grain:** One row per merchant.

| Column | Type | Description | Primary Key | Foreign Key | Example |
|---|---|---|---|---|---|
| merchant_id | string | Unique merchant identifier | PK |  | MER000001 |
| merchant_name | string | Merchant display name |  |  | Golden Coffee |
| merchant_category | string | Merchant category |  |  | Food & Beverage |
| merchant_type | string | Merchant business type |  |  | Small Business |
| city | string | Merchant city |  |  | Mandalay |
| region | string | Merchant region |  |  | Mandalay Region |

## fct_transaction

**Grain:** One row per wallet transaction.

| Column | Type | Description | Primary Key | Foreign Key | Example |
|---|---|---|---|---|---|
| transaction_id | string | Unique transaction identifier | PK |  | TXN000000001 |
| customer_id | string | Customer who transacted |  | dim_customer.customer_id | CUS000001 |
| wallet_id | string | Customer wallet identifier |  |  | WAL000001 |
| merchant_id | string | Merchant/counterparty involved |  | dim_merchant.merchant_id | MER000001 |
| transaction_date | date | Date of the transaction |  |  | 2025-04-15 |
| transaction_type | string | Type of transaction |  |  | Merchant Payment |
| amount | decimal | Transaction amount (MMK) |  |  | 25000 |
| fee | decimal | Fee charged (MMK) |  |  | 50 |
| cashback | decimal | Cashback awarded on this transaction (MMK) |  |  | 2500 |
| status | string | Transaction outcome |  |  | SUCCESS |
| currency | string | Transaction currency |  |  | MMK |

## fct_campaign_participation

**Grain:** One row per customer-campaign-promotion eligibility/participation event.

| Column | Type | Description | Primary Key | Foreign Key | Example |
|---|---|---|---|---|---|
| campaign_id | string | Campaign the customer is eligible/participated in | FK | dim_campaign.campaign_id | CMP000001 |
| promotion_id | string | Promotion assigned (if participated) | FK | dim_promotion.promotion_id | PRM000001 |
| customer_id | string | Customer | FK | dim_customer.customer_id | CUS000001 |
| eligible_flag | boolean | Whether the customer was eligible |  |  | TRUE |
| participated_flag | boolean | Whether the customer participated |  |  | TRUE |
| participation_date | date | Date of participation (if any) |  |  | 2025-04-18 |

## fct_promotion_redemption

**Grain:** One row per promotion redemption.

| Column | Type | Description | Primary Key | Foreign Key | Example |
|---|---|---|---|---|---|
| redemption_id | string | Unique redemption identifier | PK |  | RDM000000001 |
| campaign_id | string | Related campaign | FK | dim_campaign.campaign_id | CMP000001 |
| promotion_id | string | Redeemed promotion | FK | dim_promotion.promotion_id | PRM000001 |
| customer_id | string | Redeeming customer | FK | dim_customer.customer_id | CUS000001 |
| transaction_id | string | Underlying qualifying transaction | FK | fct_transaction.transaction_id | TXN000000001 |
| redemption_date | date | Redemption date |  |  | 2025-04-18 |
| reward_amount | decimal | Total reward value granted (MMK) |  |  | 2500 |
| discount_amount | decimal | Discount portion of reward (MMK) |  |  | 0 |
| cashback_amount | decimal | Cashback portion of reward (MMK) |  |  | 2500 |

## fct_customer_daily_activity

**Grain:** One row per customer per active/observed date.

| Column | Type | Description | Primary Key | Foreign Key | Example |
|---|---|---|---|---|---|
| activity_date | date | Observed date |  |  | 2025-04-20 |
| customer_id | string | Customer observed | FK | dim_customer.customer_id | CUS000001 |
| is_active | boolean | Whether the customer transacted that day |  |  | TRUE |
| transaction_count | integer | Number of successful transactions that day |  |  | 2 |
| transaction_amount | decimal | Total transaction value that day (MMK) |  |  | 45000 |
| topup_amount | decimal | Top-up value that day (MMK) |  |  | 10000 |
| payment_amount | decimal | Payment-related value that day (MMK) |  |  | 35000 |

## dim_date

**Grain:** One row per calendar date.

| Column | Type | Description | Primary Key | Foreign Key | Example |
|---|---|---|---|---|---|
| date_key | integer | YYYYMMDD surrogate key | PK |  | 20250415 |
| full_date | date | Calendar date |  |  | 2025-04-15 |
| day | integer | Day of month |  |  | 15 |
| week | integer | ISO week number |  |  | 16 |
| month | integer | Month number |  |  | 4 |
| month_name | string | Month name |  |  | April |
| quarter | integer | Calendar quarter |  |  | 2 |
| year | integer | Calendar year |  |  | 2025 |
| is_weekend | boolean | Whether the date is a weekend |  |  | FALSE |
| is_holiday | boolean | Whether the date is a Myanmar public holiday (approximate) |  |  | TRUE |

## Table Relationships

```text
dim_campaign
    |
    v
dim_promotion

dim_campaign
    |
    v
fct_campaign_participation

dim_customer
    |
    v
fct_campaign_participation

dim_promotion
    |
    v
fct_promotion_redemption

fct_transaction
    |
    v
fct_promotion_redemption

dim_customer
    |
    v
fct_transaction

dim_merchant
    |
    v
fct_transaction

dim_customer
    |
    v
fct_customer_daily_activity

```