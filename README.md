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

## 📦 The Code
Click the copy button on the top right of this code block to copy the entire script:

```javascript
javascript:(async()=>{let l=localStorage,j=JSON,d=document,Q,A=[],M={QCPANINDIAGROCERIES:2,PANINDIAGROCERIES:1},N=48;try{Q=j.parse(l.nms_delivery_store_info).store_code}catch(e){try{Q=j.parse(l.nms_delivery_config_info).serviceResp.data[0].store_code}catch(e){}}Q&&(M[Q]=3);let h=d.createElement("div");h.style="position:fixed;top:0;left:0;width:100%;z-index:99999;background:#0078ad;color:#fff;padding:10px;text-align:center;font-weight:700;font-family:sans-serif",d.body.appendChild(h);let F=async t=>{try{let e=`attributes.status:ANY("active") AND attributes.category_ids:ANY("2") AND attributes.avg_discount_pct>=${N}`+(Q?` AND attributes.inv_stores_1p:ANY("ALL","${Q}")`:""),r=j.stringify({query:"",offset:t,pageSize:100,filter:e,orderBy:"attributes.avg_discount_pct desc",pageCategories:["2"],variantRollupKeys:["variantId"],facetSpecs:[{facetKey:{key:"brands"},limit:10}],searchMode:"PRODUCT_SEARCH_ONLY",visitorId:l.jio_visitor_id||"a",branch:"projects/sr-project-jiomart-jfront-prod/locations/global/catalogs/default_catalog/branches/0",userInfo:{userId:l.h_rrid||"A"}}),i=await fetch("https://www.jiomart.com/trex/search",{method:"POST",body:r,headers:{"content-type":"application/json","cache-control":"no-store"}}),a=await i.json();return(a.results||[]).map(t=>{let e=t.product?.variants?.[0],r=e?.attributes;if(!r?.buybox_mrp||!r.seller_ids?.text?.includes("1"))return;let i,a=-1;if((r.buybox_mrp.text||[]).forEach(t=>{let e=t.split("|"),r=+e[5];if(r>0){let o=M[e[0]]||0;o>a&&(a=o,i={p:r,m:+e[4],d:+e[8]||Math.round((+e[4]-r)/+e[4]*100)})}}),!i||0===a||i.d<N)return;let o=e.title||t.product.title,s=(r.net_weight?.text?.[0]||r.net_quantity?.text?.[0]||r.size?.text?.[0]||"").trim();s&&!o.toLowerCase().includes(s.toLowerCase())&&(o+=` [${s}]`);let c="Other";try{c=t.product.categories[0].split(">")[2].trim()}catch(t){}return{n:o,i:e.images?.[0]?.uri,p:i.p,m:i.m,d:i.d,c:c}}).filter(t=>t)}catch(t){return[]}};for(let t=0;t<12;t++){if(h.innerText=`Scan ${t+1}/12...`,(r=await F(100*t)).length<1&&t>2)break;A.push(...r)}var r;h.innerText=`Found ${A.length}`,A.length?setTimeout(()=>{let t=[...new Map(A.map(t=>[t.n,t])).values()].sort((t,e)=>e.d-t.d),e=window.open("","_blank");e.document.write(`<meta name="viewport" content="width=device-width,initial-scale=1"><style>body{font-family:sans-serif;margin:0;background:#f0f2f5;padding:5px}.h{position:sticky;top:0;background:#fff;padding:10px;box-shadow:0 2px 5px #0001;z-index:9}.g{display:grid;grid-template-columns:repeat(auto-fill,minmax(145px,1fr));gap:5px;margin-top:10px}.c{background:#fff;padding:8px;border-radius:8px;position:relative;display:flex;flex-direction:column;justify-content:space-between}.i{width:100%;height:100px;object-fit:contain;margin-bottom:5px}.b{position:absolute;top:5px;left:5px;background:#2e7d32;color:#fff;padding:3px 6px;border-radius:4px;font-weight:700;font-size:12px}.t{font-size:11px;height:30px;overflow:hidden;margin-bottom:5px;color:#333}.p{font-weight:700;font-size:14px}.m{text-decoration:line-through;color:#999;font-size:10px}input{width:100%;padding:8px;margin-bottom:5px;border:1px solid #ddd;border-radius:4px;box-sizing:border-box}details{border:1px solid #ddd;background:#fff}summary{padding:8px;font-weight:600}#f{max-height:200px;overflow-y:auto;padding:5px}label{display:block;padding:5px;border-bottom:1px solid #eee}</style><div class="h"><input id="s" placeholder="Search" onkeyup="F()"><details><summary>Filter</summary><div id="f"></div></details></div><div id="r" class="g"></div><script>let D=${j.stringify(t)},S=new Set(D.map(t=>t.c)),g=document.getElementById("r");function R(t){g.innerHTML=t.map(t=>\`<div class="c" onclick="navigator.clipboard.writeText('\${t.n.replace(/'/g,"\\\\'")}')"><div class="b">\${t.d}%</div><img src="\${t.i}" class="i"><div class="t">\${t.n}</div><div><span class="p">₹\${t.p}</span> <span class="m">₹\${t.m}</span></div></div>\`).join("")}function F(){let t=document.getElementById("s").value.toLowerCase();R(D.filter(e=>S.has(e.c)&&e.n.toLowerCase().includes(t)))}function U(){S.clear(),document.querySelectorAll("#f input").forEach(t=>{t.checked&&S.add(t.value)}),F()}document.getElementById("f").innerHTML=[...S].sort().map(t=>\`<label><input type="checkbox" checked value="\${t}" onchange="U()"> \${t}</label>\`).join(""),R(D)<\/script>`),e.document.close()},100):(h.innerText="No Deals",setTimeout(()=>h.remove(),2e3))})();

## 🚀 How to Install & Use

**On Desktop (Chrome/Edge/Firefox):**
1. Ensure your Bookmarks Bar is visible (`Ctrl+Shift+B` or `Cmd+Shift+B`).
2. Right-click the Bookmarks Bar and select **Add Page** / **Add Bookmark**.
3. Name it `JioQuick Scan`.
4. In the URL field, paste the JavaScript code you copied from above.
5. Open jiomart.com, log in, and click the bookmark!

**On Mobile (Android/iOS):**
1. Bookmark any random webpage and name it `JioQuick`.
2. Edit the bookmark you just created.
3. Replace the URL with the JavaScript code you copied from above.
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
