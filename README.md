# JioQuick: Local Grocery Deal Finder 🛒⚡

**JioQuick** is a lightweight, mobile-safe JavaScript bookmarklet that connects directly to JioMart's backend API. It bypasses the standard UI to scan, filter, and extract "Quick Commerce" grocery items available at your local store with a **48%+ discount**. 

Instead of infinitely scrolling through the app trying to find genuine deals among 3rd-party scheduled items, JioQuick does it for you in seconds and presents a clean, searchable dashboard.

## ✨ Features
* **Strict Local Lock:** Only shows items available for quick delivery from your local Reliance 1P store.
* **Priority Price Engine:** Accurately maps the lowest *valid* price for your specific Pincode, ignoring irrelevant national warehouse prices.
* **Smart Dashboard:** Clean, mobile-friendly grid UI with a category filter and instant search.
* **Auto-Weight Extraction:** Digs into the JSON attributes to show the exact weight/volume (e.g., "500g", "1L") directly in the title.
* **Nano-Minified:** Optimized to fit well within the strict character limits of mobile browser bookmarks.

---

## 🚀 How to Install & Use

**On Desktop (Chrome/Edge/Firefox):**
1. Ensure your Bookmarks Bar is visible (`Ctrl+Shift+B` or `Cmd+Shift+B`).
2. Right-click the Bookmarks Bar and select **Add Page** / **Add Bookmark**.
3. Name it `JioQuick Scan`.
4. In the **URL** field, paste the minified JavaScript code from `jioquick-nano.js`.
5. Open JioMart.com, log in, and click the bookmark!

**On Mobile (Android/iOS):**
1. Bookmark any random webpage and name it `JioQuick`.
2. Edit the bookmark you just created.
3. Replace the URL with the complete JavaScript code.
4. Open the JioMart site, type "JioQuick" into your URL address bar, and tap the bookmark suggestion that appears to run the script.

---

## 🧠 The Journey: Reverse Engineering the API
Building this wasn't just a simple DOM scraping exercise. We went through 89 iterations to figure out how JioMart's Google Retail Search API actually ticks. Here are the major roadblocks we hit and how we solved them:

### 1. The "Scheduled Junk" Problem (3rd Party Sellers)
**The Bug:** Initial API calls returned great discounts, but they were mostly random 3rd-party sellers taking 5–7 days to deliver. 
**The Fix:** We injected the exact schema required by the `/trex/search` endpoint to filter out the 3P warehouse (`inv_stores_3p`). We then added a strict client-side lock to only allow `Seller ID: 1` (Reliance Retail Pure), ensuring everything shown is a true quick-commerce/local item.

### 2. The Phantom Price Bug
**The Bug:** The script was occasionally showing prices like ₹4, but the app showed ₹9. 
**The Fix:** A single item's metadata contains multiple prices linked to different regional IDs (e.g., `TDC1`, `QCPANINDIAGROCERIES`). The scraper was naively picking the lowest integer. We built a **Priority Pricing Protocol** that ranks the prices:
1. Your exact local Store ID (Highest Priority)
2. `QCPANINDIAGROCERIES` (Quick Commerce default)
3. `PANINDIAGROCERIES` (National default)

The script now intelligently ignores a ₹4 price if it belongs to a store 1000 miles away.

### 3. The "0% Off" Ghosts
**The Bug:** Requesting `avg_discount_pct >= 48` from the server occasionally returned items with 0% discount. 
**The Fix:** The API calculates discounts based on "family averages" of a product line. We implemented a double-verification system: the server filters the bulk, and the client-side math engine recalculates the exact MRP-to-Price ratio for the specific variant, instantly dropping any false positives before rendering the UI.

### 4. The 400 Bad Request (Pagination)
**The Bug:** Trying to loop pages using standard `page: 1, 2, 3` crashed the server.
**The Fix:** Discovered the API uses strict **Offset-based pagination**. The script now automatically calculates the offset (`offset: i * 100`) and halts execution early if a request returns empty, saving bandwidth.

---

## 📜 Disclaimer
This project is for educational purposes only. It interacts with publicly accessible API endpoints using the user's own session data. It is not affiliated with, endorsed by, or sponsored by Reliance Retail or JioMart.
