# Feature Spec: Login & Cart Behavior (SauceDemo)

**Target site:** https://www.saucedemo.com

## Requirements

1. When a user logs in with valid credentials (`standard_user` / `secret_sauce`), they are taken to the products page.
2. When a user attempts to log in with the locked-out account (`locked_out_user` / `secret_sauce`), the login fails and an error message indicating the account is locked is displayed. The user is not taken to the products page.
3. When a user adds a product to the cart from the products page, the cart badge count increases by 1.
4. When a user removes a product from the cart, the cart badge count decreases by 1.
