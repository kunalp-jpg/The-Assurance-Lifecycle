---
assurance:
  id: t-4
  base: sha256:b2d7ff6bc880b90756609a48564dad7d68e707b580effba8fb229a7d25de8ed0
---
# Remove one product from a two-item cart and see the cart badge decrease from 2 to 1

> Prove that removing one product from a cart that already contains more than one item lowers the visible cart badge by exactly one while keeping the post-removal badge observable.

## Step 1

Open https://www.saucedemo.com and sign in with the valid credentials standard_user / secret_sauce until the products page is visible.

## Step 2

On the products page, add any two visible products and confirm the cart badge in the header shows 2.

## Step 3

Capture baseline: store the cart badge count from the header as "badge_before_removal".

## Step 4

Open the cart page and remove one of the two products that were just added.

## Step 5

Assert the cart badge in the header shows 1.

## Step 6

Confirm the current cart badge count changed by -1 compared with "badge_before_removal".

## Step 7 — assert @verifies ac-5, ac-6

Confirm delta check: -1 (changed-by) — the stated promise: After the user removes one product from a non-empty cart, the cart badge count decreases by exactly 1 from its value immediately before the removal.
