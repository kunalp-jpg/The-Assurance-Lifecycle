---
assurance:
  id: t-3
  base: sha256:156a021e0297fd55dbfb4932182d47b17083ec0406b1ebe1909273a985d55cbb
---
# Add one product from the products page and see the cart badge increase by 1

> Prove that adding one product from the products page raises the visible cart badge by exactly one from its starting count.

## Step 1

Open https://www.saucedemo.com

## Step 2

On the SauceDemo login form, submit the username `standard_user` with the password `secret_sauce` and reach the products page.

## Step 3

capture baseline: cart badge count on the SauceDemo products page header; if no cart badge is visible yet, record the baseline as 0

## Step 4

On the SauceDemo products page inventory list, add one visible product to the cart.

## Step 5 — assert @verifies ac-4

Confirm delta check: 1 (changed-by) — the stated promise: Adding a product to the cart from the products page increases the cart badge count by exactly 1.
