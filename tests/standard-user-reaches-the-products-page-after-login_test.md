---
assurance:
  id: t-2
  base: sha256:e74adc1a727d37a93b860f673d6a8f891a2eeebe2fec9c2c58012eb862a5cde0
---
# Standard user reaches the products page after login

> Prove that the documented valid credential pair grants access to the products page.

## Step 1

Open https://www.saucedemo.com and confirm the browser is on the SauceDemo login page before any credentials are submitted.

## Step 2

capture baseline: current page is the SauceDemo login page

## Step 3

On the SauceDemo login form, submit the username `standard_user` with the password `secret_sauce`.

## Step 4

Assert the browser has transitioned away from the login page and reached the SauceDemo products page.

## Step 5 — assert @verifies ac-1

Confirm state-transition check: products page (equals) — the stated promise: Submitting the valid `standard_user` / `secret_sauce` credential pair takes the user from the login page to the products page.
