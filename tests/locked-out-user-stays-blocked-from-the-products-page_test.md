---
assurance:
  id: t-1
  base: sha256:a7c03687be5efbf855dba79e1deb6851f3aab9bca36c42085104e38e4d87e9ec
---
# Locked-out user stays blocked from the products page

> Prove that the locked-out credential pair shows the locked-account error and does not grant access to the products page.

## Step 1

Open https://www.saucedemo.com.

## Step 2

On the SauceDemo login form, submit the username `locked_out_user` with the password `secret_sauce`.

## Step 3

Assert an on-page error message indicates that the account is locked.

## Step 4

Confirm the browser has not reached the products page after the failed login attempt.

## Step 5 — assert @verifies ac-2, ac-3

Confirm presence check: error message indicating the account is locked (exists) — the stated promise: Submitting the `locked_out_user` / `secret_sauce` credential pair displays an error message indicating the account is locked.
