# Punjab Kings Store QA Findings

Date: 2026-03-11
Base URL: `https://store.punjabkingsipl.in/`
Execution mode: Playwright desktop crawl + mobile smoke pass + cart sanity flow

## Scope covered

- Home page
- Product listing page
- Categories / Collections / Brands pages
- Multiple product detail pages
- Static footer pages: About Us, Contact Us, Privacy Policy, Shipping Policy, Return Policy, Terms & Conditions
- Order Tracking page
- Cart flow smoke test
- Mobile smoke for Home, Products, and one PDP

## Coverage summary

- Desktop pages audited: 31
- Mobile pages spot-checked: 3
- Evidence bundle: `report.html`, `summary.json`, `screenshots/`, `screenshots-mobile/`

## Critical and high-impact findings

| Severity | Area | URL / Location | Issue | Evidence |
| --- | --- | --- | --- | --- |
| Critical | Homepage CTA / route | `https://store.punjabkingsipl.in/products/match-jersey` | The homepage "View Match Jersey" CTA leads to a blank 404 page. | `screenshots/bug-home-jersey-cta-source.png`, `screenshots/store-punjabkingsipl-in-products-match-jersey.png` |
| Critical | Homepage CTA / route | `https://store.punjabkingsipl.in/products/training-jersey` | The homepage "View Training Jersey" CTA leads to a blank 404 page. | `screenshots/store-punjabkingsipl-in-products-training-jersey.png` |
| Critical | Homepage collection tiles | `https://store.punjabkingsipl.in/collection` | The homepage collection destination is broken. The page returns 404 and renders as an empty shell. This affects the "COLLECTIBLES", "ACCESSORIES", and "FAN GEAR" entry points. | `screenshots/store-punjabkingsipl-in-collection.png` |
| Critical | PDP to cart flow | `https://store.punjabkingsipl.in/product/pbks-varsity-jacket-17632075` | Add-to-cart is blocked by a mandatory delivery location check, but the location field sits below the CTA and the requirement is not clearly enforced before click. The UI shows the error: "Please enter a valid location before Add to Cart/Buy Now". | `screenshots/flow-after-add-to-cart.png` |
| Critical | Cart / checkout | `https://store.punjabkingsipl.in/cart/bag` | After the add-to-cart attempt, the cart still shows as empty. Checkout CTA is not available, so the purchase path is blocked end to end. | `screenshots/flow-cart-open.png` |
| High | Navigation consistency | Homepage category links | Some homepage category links navigate to `punjab-kings.fynd.io` instead of staying on `store.punjabkingsipl.in`, which creates domain/session inconsistency and a trust risk. | Verified from crawl output in `summary.json` |
| High | Frontend stability | Multiple pages | The site throws repeated minified React runtime errors and aborted same-origin GraphQL/service-worker requests across key pages, which indicates frontend instability even on pages that still render. | `summary.json` |

## Medium-severity observations

| Severity | URL / Location | Observation | Evidence |
| --- | --- | --- | --- |
| Medium | `https://store.punjabkingsipl.in/` | The home page emits repeated React runtime errors and aborted GraphQL/service worker requests during load. | `summary.json` |
| Medium | `https://store.punjabkingsipl.in/products` | Product listing loads, but the page also emits repeated same-origin request failures and frontend runtime errors. | `summary.json`, `screenshots/store-punjabkingsipl-in-products.png` |
| Medium | `https://store.punjabkingsipl.in/order-tracking` | Order tracking page opens, but the page still logs same-origin request failures during load. | `screenshots/store-punjabkingsipl-in-order-tracking.png`, `summary.json` |
| Medium | Mobile PDP smoke | The mobile PDP renders, but the buy path still depends on the same delivery-location gating observed on desktop. | `screenshots-mobile/store-punjabkingsipl-in-product-pbks-varsity-jacket-17632075-mobile.png` |

## QA sanity checklist

| Test case | Result | Notes |
| --- | --- | --- |
| Homepage loads | Pass | Main hero and core sections render. |
| Global header navigation opens top-level pages | Partial | Main nav pages load, but some homepage CTA routes are broken. |
| Product listing page loads | Pass | `/products` renders product cards and filters. |
| Product detail pages load | Pass | Multiple PDPs rendered successfully. |
| Variant / size selection | Pass | Size selection was available on tested PDP. |
| Add to Cart | Fail | Blocked by location validation error before item can be added. |
| Buy Now | Fail | Same location validation dependency blocks the CTA. |
| Cart reflects added product | Fail | Cart remained empty after add attempt. |
| Quantity increase in cart | Blocked | Could not verify because the cart never contained an item. |
| Remove from cart | Blocked | Could not verify because the cart never contained an item. |
| Checkout CTA visible | Fail | Checkout CTA not visible on the empty cart state. |
| Order Tracking page opens | Pass | Page loaded and accepted input. |
| Contact Us page opens | Pass | Form loads. |
| About Us page opens | Pass | Content loads. |
| Policy pages open | Pass | Privacy, Shipping, Return, and Terms pages loaded. |
| Mobile home smoke | Pass | Home page rendered in mobile viewport. |
| Mobile product listing smoke | Pass | Product listing rendered in mobile viewport. |
| Mobile PDP smoke | Partial | PDP rendered, but buy path still blocked by location validation. |

## Recommended fixes

1. Fix the broken homepage destinations for:
   - `View Match Jersey`
   - `View Training Jersey`
   - collection tiles pointing to `/collection`
2. Rework the purchase flow so the delivery-location dependency is either:
   - collected before CTA enablement, or
   - validated inline without letting the user think the add-to-cart action succeeded.
3. Verify cart state persistence after add-to-cart and restore a visible checkout CTA when the cart has items.
4. Remove or correct cross-domain navigations that send users to `punjab-kings.fynd.io`.
5. Investigate the repeated React runtime errors and aborted GraphQL/service-worker requests.

## Files delivered

- `report.html`: browsable report
- `summary.json`: structured crawl output
- `screenshots/`: desktop evidence
- `screenshots-mobile/`: mobile evidence

