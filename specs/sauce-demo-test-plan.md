# Sauce Demo Shopify Storefront - Comprehensive Test Plan

## Application Overview

Comprehensive black-box functional and UX test plan for https://sauce-demo.myshopify.com/. Coverage includes home and shared navigation, the seven-item catalog and product detail pages, available and sold-out inventory, search, cart management and checkout handoff, customer account forms, blog/static pages, social feature placeholders, accessibility, and responsive behavior. Execute each scenario independently using a fresh browser context unless a step explicitly builds state within that test; do not use real customer data or complete a live purchase.

## Test Scenarios

### 1. Home, shared layout, and navigation

**Seed:** `tests/seed.spec.ts`

#### 1.1. Home page displays the storefront and product cards

**File:** `specs/homepage-navigation.spec.ts`

**Steps:**
  1. Start with a fresh browser context, open https://sauce-demo.myshopify.com/, and wait for the page to finish loading.
    - expect: The page title identifies Sauce Demo and the brand/header renders without a blocking error.
    - expect: The home product grid shows Grey jacket (£55.00), Noir jacket (£60.00), and Striped top (£50.00), each with a product image and navigable product card.
    - expect: The initial cart badge reads My Cart (0).
  2. Inspect the top navigation, left navigation, and footer without changing page state.
    - expect: Top links include Search, About Us, Log In, and Sign up, with cart and Check Out controls.
    - expect: The left navigation includes Home, Catalog, Blog, About Us, Wish list, and Refer a friend.
    - expect: Footer content includes About Us copy, accepted-payment images, copyright, and Search/About Us links.
    - expect: Links and images have discernible accessible names or useful alternative text; keyboard focus is visible when tabbing through controls.

#### 1.2. Primary navigation links open the expected storefront pages

**File:** `specs/shared-navigation.spec.ts`

**Steps:**
  1. From a fresh home page, visit Catalog, Blog, About Us, Search, Log In, and Sign up using their visible links; return to the home page between checks.
    - expect: Catalog opens the Products collection at /collections/all.
    - expect: Blog opens the News blog at /blogs/news.
    - expect: About Us opens /pages/about-us.
    - expect: Search opens /search.
    - expect: Log In opens /account/login and Sign up opens /account/register.
    - expect: Every visited page retains shared branding and usable navigation; browser back and home navigation return to a valid page.
  2. Open the home page, press Tab repeatedly through the header and navigation, and activate a focused internal link with Enter.
    - expect: Keyboard focus proceeds in a logical order through interactive controls.
    - expect: The focused link can be activated without requiring a pointer, and focus is not trapped.

### 2. Catalog and product details

**Seed:** `tests/seed.spec.ts`

#### 2.1. Catalog lists available and sold-out products correctly

**File:** `specs/catalog-inventory.spec.ts`

**Steps:**
  1. In a fresh context, open the Catalog link or /collections/all and inspect the collection listing.
    - expect: The collection heading identifies Products and the page lists the seven observed items: Black heels (£45.00), Bronze sandals (£39.99), Brown Shades (£20.00), Grey jacket (£55.00), Noir jacket (£60.00), Striped top (£50.00), and White sandals (£25.00).
    - expect: Brown Shades and White sandals are visibly marked Sold Out; available items do not display a sold-out marker.
    - expect: Each card shows a name, price, image, and opens its corresponding product page.
  2. Open one available product from the collection, then return using browser back.
    - expect: The product page matches the card selected, including its name and displayed price.
    - expect: Returning to the collection works and the collection remains usable without a broken or empty listing.

#### 2.2. Available product detail supports adding an item to cart

**File:** `specs/product-detail.spec.ts`

**Steps:**
  1. From a fresh context, open Grey jacket from the home or catalog page.
    - expect: The product page displays Grey jacket, £55.00, a product image, a variant selector if applicable, and an enabled Add to Cart button.
    - expect: The breadcrumb or equivalent navigation provides a route back to the storefront/collection.
  2. Click Add to Cart once and inspect the result without reloading.
    - expect: The product is added successfully and the cart badge increments from zero to one.
    - expect: The page remains responsive and does not show a false failure or a duplicate item caused by one click.
  3. Refresh the product page and inspect the cart badge, then open the cart.
    - expect: Cart state remains consistent after a page reload within the same browser session.
    - expect: The cart contains the Grey jacket at £55.00 with quantity 1 and a matching line total.

#### 2.3. Sold-out products cannot be added to the cart

**File:** `specs/sold-out-product.spec.ts`

**Steps:**
  1. In a fresh context, open Brown Shades from /collections/all or navigate to /products/brown-shades.
    - expect: The product page identifies Brown Shades and displays its £20.00 price.
    - expect: The purchase control clearly shows Sold Out and is disabled; no active Add to Cart action is available.
  2. Attempt to activate the sold-out control using keyboard and pointer interaction, then open the cart.
    - expect: The unavailable product cannot be added to the cart.
    - expect: The cart remains empty and its badge remains zero.
    - expect: A sold-out state is communicated without a misleading purchase success message.
  3. Repeat the availability check for White sandals from the catalog, if its product detail page is reachable.
    - expect: White sandals is consistently marked unavailable and cannot be purchased while out of stock.

### 3. Search and product discovery

**Seed:** `tests/seed.spec.ts`

#### 3.1. Search returns results for a product keyword

**File:** `specs/search-results.spec.ts`

**Steps:**
  1. In a fresh context, use the visible header search field to submit the query jacket.
    - expect: The search results page loads and reflects the submitted query in its URL or visible search context.
    - expect: Results are displayed as product cards with names, prices, images, and working product links.
    - expect: Search results are relevant to the query; any unrelated or inconsistent product result is recorded as a defect rather than treated as expected behavior.
  2. Open a result and use browser back to return to the results.
    - expect: The chosen product detail page opens correctly.
    - expect: Returning to results preserves the query and restores a usable result list.

#### 3.2. Empty and unmatched searches have a safe, understandable state

**File:** `specs/search-empty-and-no-results.spec.ts`

**Steps:**
  1. Open /search directly without submitting a query.
    - expect: The page communicates that no search was performed and provides a usable way back to the home page or to search.
  2. Submit a unique unmatched term such as zzzz-unknown-item using the header search field.
    - expect: The search page completes without an application crash or malformed layout.
    - expect: A clear no-results state is shown and the submitted query remains identifiable.
    - expect: The user can recover by editing the query or returning to the catalog.
  3. Submit whitespace or an empty value from the search control, if the browser allows submission.
    - expect: Blank input is handled safely and does not produce a misleading match or broken page.

### 4. Cart and checkout handoff

**Seed:** `tests/seed.spec.ts`

#### 4.1. Empty cart provides a clear continue-shopping route

**File:** `specs/empty-cart.spec.ts`

**Steps:**
  1. In a fresh context with no prior cart activity, open /cart.
    - expect: The page identifies the cart as empty and displays a Continue Shopping link.
    - expect: The cart badge is zero and no product line, incorrect total, or checkout-ready item is displayed.
    - expect: Continue Shopping returns to the Products collection.

#### 4.2. Cart quantity and totals update accurately

**File:** `specs/cart-quantity-update.spec.ts`

**Steps:**
  1. In a fresh context, add Grey jacket once, open /cart, and record its displayed unit price, quantity, and line total.
    - expect: The cart contains one Grey jacket line at £55.00 with quantity 1 and total £55.00.
  2. Change the quantity to 2 and choose Update.
    - expect: The quantity is retained as 2 after update.
    - expect: The line total and cart total recalculate to £110.00, subject to the storefront's displayed currency and tax/shipping rules.
    - expect: The cart badge represents the updated number of items consistently.
  3. Try a boundary input such as 0, a negative number, non-numeric text, and an unusually large quantity one at a time, updating between attempts.
    - expect: Invalid values are rejected or safely normalized with clear behavior; no negative totals, NaN values, or broken cart state appear.
    - expect: A valid quantity change can still be submitted afterward.

#### 4.3. Cart supports item removal and order notes

**File:** `specs/cart-remove-and-note.spec.ts`

**Steps:**
  1. In a fresh context, add Grey jacket and open the cart.
    - expect: The Grey jacket line and a remove control are present.
  2. Enter a short ordinary order note, update if required, and confirm the note remains visible or is stored as indicated by the interface.
    - expect: The note is accepted without changing product quantity or price unexpectedly.
  3. Remove the only item using its remove control.
    - expect: The item is removed, the badge and totals update, and the cart shows its empty-state message with a Continue Shopping route.

#### 4.4. Cart handles multiple distinct product lines and session navigation

**File:** `specs/cart-multiple-products.spec.ts`

**Steps:**
  1. In a fresh context, add one Grey jacket and one Noir jacket from their respective product pages, then open the cart.
    - expect: Both products appear as distinct cart lines with correct names, unit prices (£55.00 and £60.00), quantities, and a combined total of £115.00.
    - expect: The cart badge and line quantities reflect two items.
  2. Navigate to another storefront page and back to the cart, then refresh the cart page.
    - expect: Cart contents and totals remain consistent across same-session navigation and refresh.
    - expect: No unintended duplicate lines or lost items are introduced.

#### 4.5. Checkout action hands off without accidental purchase completion

**File:** `specs/checkout-handoff.spec.ts`

**Steps:**
  1. In a fresh context, add one available product and open the cart; verify the item and total before proceeding.
    - expect: Cart contents, quantity, and total are correct before checkout.
  2. Activate the cart Check Out action only far enough to verify the resulting destination; do not enter payment details or submit an order.
    - expect: The action leads to the expected Shopify checkout/checkout handoff or presents a clear, non-broken availability message.
    - expect: No order is placed and no success/confirmation page is shown without intentional order submission.

### 5. Customer account flows

**Seed:** `tests/seed.spec.ts`

#### 5.1. Login form validates empty and invalid credentials safely

**File:** `specs/login-validation.spec.ts`

**Steps:**
  1. In a fresh context, open /account/login and inspect the customer login form.
    - expect: The form includes Email Address, Password, Sign In, and a Forgot your password? control.
    - expect: Inputs are labeled and password characters are masked.
  2. Submit the form empty, then submit a syntactically invalid email with a dummy password.
    - expect: Required and malformed values are rejected with clear feedback or browser validation.
    - expect: The customer remains on a usable form; there is no silent success or account-session creation.
  3. Submit syntactically valid but intentionally incorrect test credentials.
    - expect: Authentication fails with understandable feedback and does not expose whether an unrelated account exists beyond the expected policy.
    - expect: The entered email can be corrected and the user can return to the storefront.

#### 5.2. Registration form validates required fields and malformed data

**File:** `specs/registration-validation.spec.ts`

**Steps:**
  1. In a fresh context, open /account/register and inspect the Create Account form.
    - expect: The form contains First Name, Last Name, Email Address, Password, and Create controls.
    - expect: All fields have clear labels and password input is masked.
  2. Submit with all fields blank, then with malformed email and a weak or missing password.
    - expect: Required values and invalid email/password values are rejected with clear validation.
    - expect: Invalid submissions do not create an account or falsely report success.
  3. Fill structurally valid dummy data that is not tied to a real person, and submit only if the test environment explicitly permits test-account creation.
    - expect: Where account creation is enabled, the site provides a clear success/error outcome; otherwise the test is skipped before submission.
    - expect: No real customer data is used and the user can navigate back to the storefront.

#### 5.3. Forgot-password control has a usable recovery behavior

**File:** `specs/password-recovery.spec.ts`

**Steps:**
  1. Open /account/login and activate Forgot your password?.
    - expect: The control opens a recovery form or gives an understandable recovery response.
    - expect: The recovery path provides an email field and clear submit/cancel or return behavior, or any nonfunctional placeholder is documented as a defect.
    - expect: No password recovery message reveals a customer's credentials.

### 6. Content, social features, and non-functional coverage

**Seed:** `tests/seed.spec.ts`

#### 6.1. Blog index and article links render readable content

**File:** `specs/blog-content.spec.ts`

**Steps:**
  1. In a fresh context, open /blogs/news and inspect the News page.
    - expect: The blog page displays its post title, author/date information where available, and readable excerpt/content.
    - expect: The First Post title links to its article route.
  2. Open the First Post article and navigate back to the blog index.
    - expect: The article route loads readable post content and shared navigation remains available.
    - expect: Browser back returns to the News index without an error.

#### 6.2. About page and social-feature placeholders behave predictably

**File:** `specs/static-content-and-placeholders.spec.ts`

**Steps:**
  1. Open /pages/about-us and verify its heading and Sauce Demo description.
    - expect: The About Us page loads with readable content, shared site navigation, and footer.
    - expect: The Sauce link has a valid destination.
  2. Activate Wish list and Refer a friend navigation items from a fresh page, one at a time.
    - expect: Each control has a predictable, non-destructive result such as its documented feature UI or anchor behavior.
    - expect: A placeholder must not unexpectedly navigate to an error page, change cart contents, or obscure navigation without a way to dismiss.
  3. Inspect the social icons and blog feed link without submitting data or leaving the test domain unless explicitly approved.
    - expect: External destinations are clearly recognizable and links are valid or documented placeholders.
    - expect: Internal blog feed link resolves to the expected feed resource.

#### 6.3. Storefront remains usable across viewport sizes and assistive input

**File:** `specs/responsive-and-accessibility.spec.ts`

**Steps:**
  1. In a fresh context, inspect home, catalog, product detail, cart, and account pages at desktop width, then repeat at a narrow mobile viewport (for example 375px wide).
    - expect: Content remains readable, key controls are visible, and there is no unintended horizontal overflow or overlap.
    - expect: Product cards reflow appropriately and navigation remains accessible on each page.
  2. Use keyboard-only navigation on home, a product page, and cart; inspect heading hierarchy, labels, focus indicators, and image alternatives.
    - expect: Interactive elements are reachable in a logical order and activated by keyboard.
    - expect: Form fields expose labels, headings describe page sections, and meaningful product/payment imagery has appropriate alternative text.
    - expect: Color/visual state is not the sole means of communicating sold-out or validation status.

#### 6.4. Invalid routes and page reloads fail gracefully

**File:** `specs/error-resilience.spec.ts`

**Steps:**
  1. In a fresh context, open a clearly nonexistent storefront path and observe the response.
    - expect: The site returns a controlled not-found page or safe redirect, not a broken or blank application shell.
    - expect: Shared navigation or a recovery route is available when possible.
  2. Reload the home page and a product detail page; use browser back/forward between these routes.
    - expect: Each route remains loadable and navigable without duplicate cart actions or a stuck loading state.
    - expect: No unexpected page-level error prevents browsing.
