\# Shopalto Self-Healing Product Scraper



Built for the Into the Scrape-Verse Hackathon (WeMakeDevs x Bright Data, Aug 2026).



\## What this is



I built this to explore Bright Data's Scraper Studio and specifically its self-healing feature, which was the whole theme of this hackathon. It pulls product data (name, price, description, rating, image) from shopalto.xyz, a demo store set up for this hackathon, and shows the results in a simple dashboard.



The more interesting part isn't the scraping itself, it's what happens when it breaks. I deliberately ran into two real extraction problems while building this, and instead of rewriting anything, I just told the AI what was wrong and let it fix the scraper in place. Both are documented below with the actual commands I ran.



\## Collector ID



`c\_mt54szkw283mlvnnru`



\## How the scraper actually got built



I didn't write any scraping code myself. I gave Bright Data's CLI a URL and a plain-English description of what I wanted, and it generated the scraper on its own:



**npx -p @brightdata/cli bdata scraper create https://shopalto.xyz/product/aurora-wireless-headphones "Extract product name, price, description and rating"**



The AI figured out the page structure and built the extraction logic itself. The only actual code I wrote by hand is `app.py`, a small Streamlit script that reads the JSON the scraper produces and displays it as a table.



\## Two self-healing moments



\*\*First one, adding a field I forgot:\*\*



I initially only asked for 4 fields. After the fact, I wanted the product image too, so instead of creating a new scraper, I healed the existing one:



**npx -p @brightdata/cli bdata scraper heal c\_mt54szkw283mlvnnru "Also capture image\_url field alongside existing name, price, description and rating" --url https://shopalto.xyz/product/aurora-wireless-headphones**



**npx -p @brightdata/cli bdata scraper approve c\_mt54szkw283mlvnnru --url https://shopalto.xyz/product/aurora-wireless-headphones --auto-save**



Ran it again afterward and `image\_url` was there, same Collector ID, nothing rebuilt.



\*\*Second one, a page that just didn't work:\*\*



When I pointed the same scraper at a different product (Mute Pro Earbuds), it came back almost empty, no name, no image, just a rating and a URL. Rather than treat that as a dead end, I used it as the actual test of self-healing:



**npx -p @brightdata/cli bdata scraper heal c\_mt54szkw283mlvnnru "Product name, price, and description are missing on this page even though the page clearly shows them" --url https://shopalto.xyz/product/mute-pro-earbuds**



**npx -p @brightdata/cli bdata scraper approve c\_mt54szkw283mlvnnru --url https://shopalto.xyz/product/mute-pro-earbuds --auto-save**



This recovered the product name and image on that page. Price and description are still missing on some pages, more on that below.



\## If you want to run this yourself



1\. Log into Bright Data through the CLI:

**npx -p @brightdata/cli bdata login --device**

2\. Create a scraper:

**npx -p @brightdata/cli bdata scraper create <URL> "<what you want extracted>"**

3\. Run it:

**npx -p @brightdata/cli bdata scraper run <COLLECTOR\_ID> <URL>**

4\. If something's missing or wrong, heal it:

**npx -p @brightdata/cli bdata scraper heal <COLLECTOR\_ID> "<what's broken>" --url <URL>**

**npx -p @brightdata/cli bdata scraper approve <COLLECTOR\_ID> --url <URL> --auto-save**

5\. To see the dashboard, install Streamlit and run it:

pip install streamlit --break-system-packages

streamlit run app.py





**## What's in this repo**



**- `app.py` — the dashboard**

**- `results.json` — the actual scraped data, 6 products from shopalto.xyz**

**- `README.md` — this file**



**## Honest note on data quality**



**Not every product has every field filled in. Price and description are missing on a few pages, that's a genuine reflection of how inconsistent page layouts can be across a site, even one built for a hackathon demo. I healed two real problems I actually hit rather than staging a clean example, and I think that's a more honest demonstration of what self-healing is actually for. Given more time, I'd keep running heal cycles per page until every field was consistently captured.**



**Only public data was used here, nothing behind a login or paywall.**

