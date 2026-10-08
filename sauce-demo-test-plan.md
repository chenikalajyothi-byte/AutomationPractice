# Sauce Demo Storefront Test Plan

## Application Overview

This test plan covers the Sauce Demo Shopify storefront, focusing on home-page browsing, product discovery, cart behavior, account flows, and category/navigation paths. The site is a demo storefront with static product listings, social links, and basic customer account entry points, so tests emphasize usability and validation rather than deep payment processing.

## Test Scenarios

### 1. Storefront browsing and discovery

**Seed:** `tests/seed.spec.ts`

#### 1.1. Home page renders product catalog and core navigation

**File:** `tests/storefront/storefront-home.spec.ts`

**Steps:**
  1. Open the site home page at the root URL and wait for the storefront to load.
    - expect: The page title is 'Sauce Demo'.
    - expect: The header includes the brand name and a product grid with at least three visible products: Grey jacket, Noir jacket, and Striped top.
  2. Inspect the left-side navigation and top utility links.
    - expect: The left rail lists Home, Catalog, Blog, About Us, Wish list, and Refer a friend.
    - expect: The header contains Search, About Us, Log In, Sign up, My Cart (0), and Check Out links.
  3. Verify visible social and footer links.
    - expect: The social icons are rendered and linked to the expected Sauce/Shopify destinations.
    - expect: The footer includes About Us content and a copyright block.

#### 1.2. Product detail page displays pricing and add-to-cart controls

**File:** `tests/storefront/product-detail.spec.ts`

**Steps:**
  1. Click the 'Grey jacket' product card from the home page.
    - expect: The browser lands on the Grey jacket product detail page.
    - expect: The page shows the product title, price (£55.00), product image, and an 'Add to Cart' button.
  2. Check the breadcrumb and related products area.
    - expect: A breadcrumb trail is visible with Home > Frontpage > Grey jacket.
    - expect: A 'You Might Also Like...' section lists alternative products for the customer to browse.
  3. Click the 'Add to Cart' button once.
    - expect: The cart count updates from My Cart (0) to My Cart (1).
    - expect: The Add to Cart button remains enabled and the page does not throw an error.

#### 1.3. Search and catalog navigation work across the storefront

**File:** `tests/storefront/search-navigation.spec.ts`

**Steps:**
  1. Use the search field in the header with a valid term such as 'jacket'.
    - expect: The search results page loads with matching products based on the item name and the term remains in the page context.
    - expect: The result set contains the jacket products and not unrelated items.
  2. Use the Catalog link from the left rail.
    - expect: The Catalog page loads successfully and displays the storefront collection view.
    - expect: The collection continues to show product images and pricing.
  3. Navigate back to Home using the main navigation.
    - expect: The home page loads again without losing layout or branding.
    - expect: The product grid remains visible and functional.

### 2. Cart and purchase flow

**Seed:** `tests/seed.spec.ts`

#### 2.1. Customer can add one product to the cart and review it

**File:** `tests/cart/cart-add-item.spec.ts`

**Steps:**
  1. From the home page, select a product and add it to the cart.
    - expect: The cart indicator increases accordingly.
    - expect: The customer can access the My Cart or Check Out link.
  2. Open the cart page from the Check Out link.
    - expect: The selected product appears in the cart with a visible item name and unit price.
    - expect: The cart includes correct totals for the product and a cleaner cart layout.
  3. Review the cart page state before checkout.
    - expect: The customer can continue browsing or remove the item if needed.
    - expect: The page clearly communicates the shopping cart contents with no broken layout or missing product data.

#### 2.2. Cart quantity state remains consistent when adding more items

**File:** `tests/cart/cart-quantity.spec.ts`

**Steps:**
  1. Add the same product to the cart again or add a different product from the catalog.
    - expect: The cart number changes appropriately to reflect the additional item count.
    - expect: The cart page, if opened, reflects the new quantity or line item count without duplication errors.
  2. Refresh or navigate away and return to the catalog.
    - expect: The cart count remains consistent with the session state and the storefront does not reset unexpectedly.
    - expect: The customer experience remains stable across navigation.
  3. Attempt to continue to the checkout flow from the cart page.
    - expect: The checkout link works and directs the customer to the Shopify cart/checkout journey without a broken path or server error.

### 3. Account and registration flows

**Seed:** `tests/seed.spec.ts`

#### 3.1. Login and registration entry points are accessible and valid

**File:** `tests/account/account-login.spec.ts`

**Steps:**
  1. Open the site and click 'Log In' from the top navigation.
    - expect: The login page loads and presents the account sign-in form.
    - expect: The page name and form fields are clear and consistent with Shopify account conventions.
  2. Click 'Sign up' from the header and inspect the registration form.
    - expect: The registration page loads and shows the required account creation fields.
    - expect: The page gives a clear path to complete sign-up or return to login.
  3. Submit an empty or invalid form to validate user feedback.
    - expect: The page shows clear validation errors for required fields or invalid entries.
    - expect: The user remains on the form and is not redirected with a silent failure.

#### 3.2. New customer can reach account creation without blocking the storefront

**File:** `tests/account/account-guest-journey.spec.ts`

**Steps:**
  1. Begin from the home page and navigate into the account flow without adding products.
    - expect: The customer can open the sign-up page without needing to interact with the catalog or cart.
    - expect: The navigation remains consistent and the site branding is preserved.
  2. Test the return path from registration to the storefront.
    - expect: The user can abandon sign-up and return to the catalog or home page without losing the current storefront status.
    - expect: No broken links or dead-end pages appear in the account flow.
  3. Confirm the login page and sign-up page are both usable on desktop width.
    - expect: All fields, buttons, and links are visible and accessible without overlapping, truncation, or hidden controls.

### 4. Error handling and edge cases

**Seed:** `tests/seed.spec.ts`

#### 4.1. Empty or invalid search gracefully returns relevant feedback

**File:** `tests/edge/empty-search.spec.ts`

**Steps:**
  1. Enter an invalid search term such as 'zzzz-unknown-item' into the search box.
    - expect: The search results page loads successfully and shows either no results or a relevant empty-state message.
    - expect: The page does not crash or produce a broken layout.
  2. Use the search field with an empty string or whitespace.
    - expect: The UI prevents or safely handles blank search submissions.
    - expect: The user sees a controlled search result behavior instead of a malformed page or hard error.
  3. Return to the homepage from an empty-search state.
    - expect: The site returns to the catalog view without any persistence issues.
    - expect: The standard navigation and product grid remain intact.

#### 4.2. Static content and less-common entry points stay functional

**File:** `tests/edge/about-page.spec.ts`

**Steps:**
  1. Open the About Us page from the header or footer.
    - expect: The About Us page loads and shows the demo-brand messaging for Sauce.
    - expect: The page structure remains readable and the header/footer navigation remains available.
  2. Try the social and blog links in the navigation and footer.
    - expect: The links resolve to valid destinations or the expected demo routes.
    - expect: The visitors are not blocked by dead links or malformed URLs.
  3. Check the wishlist and refer-a-friend anchors.
    - expect: The anchors exist in the navigation and do not lead to non-functional dead ends.
    - expect: The storefront presents a predictable behavior even when the feature is only a placeholder.
