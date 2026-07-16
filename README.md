# 🏠 ImmoScout24 Scraper

Fast, reliable property scraper for **immobilienscout24.de** — Germany's largest real-estate marketplace. Paste any search or property URL and get clean, structured listing data back.

**Supports every ImmoScout24 URL type — apartment & house search (rent / buy), property sub-types (villa, bungalow, semi-detached, farmhouse…), land, new-development (Neubau) projects, and individual property pages.**

> No setup, no login, no browser to babysit. Filter on the ImmoScout24 website, copy the URL from your address bar, paste it in, and run.

---

## 🎯 What This Actor Extracts

Complete property intelligence from ImmoScout24 listings:

### 📋 Core Property Data
* **Property Details**: listing ID, title, property type, and canonical web link
* **Pricing**: headline price, plus (in detail mode) base rent, total rent, service charge, and price/m²
* **Specifications**: rooms, living space, floor / number of floors, construction year
* **Energy**: energy-efficiency class, energy & heating type, thermal characteristic
* **Status**: publication state, listing type, private-vs-agency, new-build flag

### 🖼️ Visual Content
* **Main Image**: primary listing photo on every result
* **Full Gallery**: the complete high-resolution image set (detail mode)

### 🏢 Location & Contact
* **Address**: full line, street, house number, postcode, quarter, region
* **GPS Coordinates**: latitude and longitude for mapping
* **Agency & Agent**: company name, agent name, logo, and profile link
* **Phone**: contact numbers where published

### 🏗️ Building & Amenities
* **Feature Flags**: balcony/terrace, cellar, lift, fitted kitchen, garden, barrier-free, pets, newly built
* **Attribute Sections**: itemized “Main criteria”, “Costs”, and “Building details & energy certificate” exactly as shown on the listing
* **Free-Text Descriptions**: full property description, furnishing, and location write-ups

### ⚡ Advanced Features
* **Automatic Filter Parsing**: location, property type & sub-type, price, rooms, living space, plot size, construction year, and equipment features are all read straight from the URL
* **New-Development (Neubau) Support**: developer projects are captured, including their individual units
* **Individual Property URLs**: a single property link always returns its full detail record
* **Two Data Modes**: fast search-only, or full detail-page enrichment
* **Configurable Results**: control max listings per search URL (or collect everything)
* **Automatic Resume**: long runs continue from where they left off after any interruption
* **Error Tracking**: unreadable URLs and expired listings are reported as clear error rows

---

## 🌐 Supported URL Types

### Search / Listing URLs
Scrape many properties from a search results page — rent, buy, or new developments:

* `https://www.immobilienscout24.de/Suche/de/hessen/frankfurt-am-main/wohnung-mieten`
* `https://www.immobilienscout24.de/Suche/de/bayern/muenchen/haus-kaufen?price=300000.0-900000.0&numberofrooms=3.0-6.0`
* `https://www.immobilienscout24.de/Suche/de/berlin/berlin/villa-kaufen`

Any filter you apply on the site — property type & sub-type, price, rooms, living space, plot size, construction year, equipment — is carried over automatically. Just copy the address bar.

### Individual Property URLs
Extract the full detail record from a single property page:

* `https://www.immobilienscout24.de/expose/167767638`

### New-Development (Neubau) Project URLs
Capture a developer project and its individual units:

* `https://www.immobilienscout24.de/neubau/<developer>/<project>/132302.html`

The actor detects which type of URL you provide and handles each accordingly. Mix any combination of URL types in a single run.

---

## 📊 Two Data Modes

### ⚡ Fast Mode — `Fetch Full Property Details` OFF (default)
Returns the listing data available from the search results — price, address, rooms, living space, energy class, agency, and main photo. Fast and cost-effective, ideal for building lists and monitoring a market.

### 🔎 Full Mode — `Fetch Full Property Details` ON
For every listing from a search URL, also fetches the complete detail page — full rent breakdown, all features, energy-certificate data, complete descriptions, full image gallery, and agent contact — via one extra request per listing. Richer, more complete data.

> Individual property URLs always return full details, regardless of this setting.

---

## ✅ Input Schema

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| **startUrls** | array | ✅ Yes | — | One or more ImmoScout24 search or property URLs. Copy straight from the browser after applying filters. |
| **maxResults** | integer | No | 25 | Max listings per search URL. Set to `0` for every available listing (bounded only by your budget). Individual property URLs always return one result. |
| **fetchPropertyDetails** | boolean | No | false | 🔎 **Fetch full property details.** OFF: fast search-result fields only. ON: also fetch each property's full detail page (complete description, features, energy certificate, agent contact, exact location, full gallery) — richer, more complete data. Individual property URLs always return full details. |

### Example Input (mixed URLs)
```json
{
  "startUrls": [
    { "url": "https://www.immobilienscout24.de/Suche/de/hessen/frankfurt-am-main/wohnung-mieten" },
    { "url": "https://www.immobilienscout24.de/Suche/de/bayern/muenchen/haus-kaufen?price=300000.0-900000.0&numberofrooms=3.0-6.0" },
    { "url": "https://www.immobilienscout24.de/expose/167767638" }
  ],
  "maxResults": 100,
  "fetchPropertyDetails": true
}
```

**How it works:**
- Each search URL returns up to `maxResults` listings
- Each individual property URL returns exactly 1 full record
- **Total output**: up to `maxResults × (number of search URLs)` + 1 per property URL

---

## 📤 Sample Output

### Fast Mode — `fetchPropertyDetails: false`
```json
{
  "type": "property",
  "listingId": "164526698",
  "url": "https://www.immobilienscout24.de/expose/164526698",
  "title": "Neubau 4-Zimmer Wohnung",
  "realEstateType": "apartmentrent",
  "price": "€3,952",
  "livingSpace": "152 m²",
  "numberOfRooms": "4 rms",
  "energyEfficiencyClass": null,
  "address": {
    "line": "Wendelsweg 66, 60599 Frankfurt, Sachsenhausen-Süd",
    "postcode": null,
    "latitude": 50.09785,
    "longitude": 8.69708
  },
  "listingType": "XL",
  "published": "7 months ago",
  "isPrivate": false,
  "agencyName": "Actris GmbH",
  "mainImage": "https://pictures.immobilienscout24.de/listings/365d83d9-...-2021190120.jpg/ORIG/resize/800x600/format/webp/quality/80",
  "images": ["https://pictures.immobilienscout24.de/listings/365d83d9-...-2021190120.jpg/..."],
  "details": null,
  "hasDetailData": false,
  "source_url": "https://www.immobilienscout24.de/Suche/de/hessen/frankfurt-am-main/wohnung-mieten",
  "_source": "immobilienscout24_scraper",
  "scrapedAt": "2026-07-16T08:36:59.316Z"
}
```

### Full Mode — `fetchPropertyDetails: true`
Everything above, plus a populated `details` object:
```json
{
  "listingId": "164526698",
  "title": "Neubau 4-Zimmer Wohnung",
  "price": "€3,952",
  "hasDetailData": true,
  "details": {
    "baseRent": 3952,
    "totalRent": 4431,
    "serviceCharge": 479,
    "livingSpace": 152,
    "numberOfRooms": 4,
    "floor": 3,
    "numberOfFloors": 4,
    "yearConstructed": 2026,
    "address": {
      "street": "Wendelsweg",
      "houseNumber": "66",
      "zipCode": "60599",
      "quarter": "sachsenhausen_süd",
      "region": "Hessen, Frankfurt_am_Main, Süd",
      "latitude": 50.09785,
      "longitude": 8.69708
    },
    "heatingType": "floor_heating",
    "condition": "first_time_use",
    "interiorQuality": "luxury",
    "features": {
      "balcony": true, "cellar": true, "lift": true, "fittedKitchen": true,
      "garden": true, "petsAllowed": null, "barrierFree": true, "newlyBuilt": true
    },
    "descriptions": {
      "Property Description": "Wohnen mit Stil und Geschichte – Ihr neues Zuhause im Henninger Park…",
      "Furnishing": "Modernes Wohnen mit Stil und Eleganz…",
      "Location": "Zu Hause in den Stadtgärten am Henninger Turm…"
    },
    "attributeSections": [
      { "title": "Main criteria", "items": [
        { "label": "Apartment type:", "text": "Flat" },
        { "label": "Floor:", "text": "3 of 4" },
        { "label": "Balcony/Terrace:", "present": true },
        { "label": "Elevator:", "present": true }
      ]},
      { "title": "Costs", "items": [
        { "label": "Net rent (plus ancillary costs):", "text": "€3,952" },
        { "label": "Ancillary costs:", "text": "€479" },
        { "label": "Total rent:", "text": "€4,431" }
      ]},
      { "title": "Building details & energy certificate", "items": [
        { "label": "Construction year:", "text": "2026" },
        { "label": "Type of heating:", "text": "Floor heating" }
      ]}
    ],
    "agent": {
      "company": "Actris GmbH",
      "name": "Eva Cuadrado",
      "profileUrl": "https://www.immobilienscout24.de/anbieter/actris-gmbh/ac16e3a0ecb504351db0039"
    },
    "phoneNumbers": [],
    "images": ["https://pictures.immobilienscout24.de/listings/365d83d9-...-2021190120.jpg/..."]
  }
}
```

### New-Development (Neubau) Project Row
```json
{
  "type": "project",
  "projectId": "132302",
  "projectName": "FOUR Frankfurt",
  "url": "https://www.immobilienscout24.de/neubau/.../132302.html",
  "priceRange": "€1,750 - €5,000",
  "sizeRange": "39 - 100 m²",
  "address": { "line": "Junghofstraße 5, 60315 Frankfurt am Main", "latitude": 50.1125, "longitude": 8.6748 },
  "projectObjects": [
    { "listingId": "167767638", "title": "FOUR: Stillvolle 2-Zimmer Wohnung mit Mainblick", "attributes": ["€2,950", "57 m²", "2 rms"] }
  ]
}
```

### Error Row
```json
{
  "_source": "immobilienscout24_scraper",
  "source_url": "https://www.immobilienscout24.de/expose/000000000",
  "_error": "Could not fetch this expose: Not found (HTTP 404) — listing may be expired or the id is invalid"
}
```

---

<!-- PRICING HIDDEN FOR FREE LAUNCH — uncomment this block to re-enable the pricing section.

## 💰 Pricing

Pay only for the listings you actually get, on a sliding scale by plan:

| Event | FREE | BRONZE | SILVER | GOLD |
|-------|------|--------|--------|------|
| **Per listing** (search result saved) | $0.0010 | $0.0007 | $0.00065 | $0.0005 |
| **Full-details add-on** (per enriched listing) | $0.0025 | $0.0010 | $0.0009 | $0.0008 |

Search-only runs are billed at the per-listing rate. With **Fetch Full Property Details** on (or for individual property URLs), the add-on applies on top per enriched listing. Set a run budget in the Console and the actor never fetches beyond what it covers.

---

-->

## 🔐 Proxy

No proxy setup is required — it's fully automatic.

* **Free users**: automatic Apify residential proxy, no configuration needed.
* **Paid users**: a premium proxy with **automatic failover** to the standard proxy — if the premium route has a hiccup, the run switches over seamlessly and keeps going, so a proxy issue never interrupts your results.

---

## 🔄 Reliability

* **Automatic resume** — after a migration, timeout, or restart, the run continues from the last completed page instead of starting over, so long jobs finish without re-paying for work already done.
* **Robust retries** — transient network issues are retried transparently.
* **Clean, typed output** — normalized fields, consistent across every run, with two ready-made dataset views: **Search results (fast)** and **Full details**.

---

## 🎯 Perfect For

* 🧑‍💼 **Real-estate agents** building and monitoring listing lists
* 📈 **Investors & analysts** tracking price, size, and yield across a region
* 🏢 **Agencies** monitoring competitors and new stock
* 🤖 **Developers** feeding a CRM, valuation model, or market dashboard
* 🗺️ **Researchers** collecting full regional datasets for analysis

---

## 📞 Support

- 🌐 **Website**: [flowextractapi.com](https://flowextractapi.com)
- 📧 **Email**: [flowextractapi@outlook.com](mailto:flowextractapi@outlook.com)
- 🙋 **Apify Profile**: [FlowExtract API](https://apify.com/dz_omar?fpr=smcx63)
- 💬 **GitHub**: [FlowExtractAPI](https://github.com/FlowExtractAPI)

## 🌟 Related Actors by FlowExtract API

### 🏠 Real Estate
- **[Idealista Scraper API](https://apify.com/dz_omar/idealista-scraper-api?fpr=smcx63)** — Spanish/Portuguese/Italian property data with API
- **[Idealista Scraper](https://apify.com/dz_omar/idealista-scraper?fpr=smcx63)** — Real estate listings extractor

### 🎬 Video & Media
- **[YouTube Transcript Extractor](https://apify.com/dz_omar/youtube-transcript-metadata-extractor?fpr=smcx63)** — Transcripts with timestamps
- **[Loom Scraper](https://apify.com/dz_omar/loom-video-scraper?fpr=smcx63)** — Loom video and transcript extraction

### 🛠️ Developer Tools
- **[Screenshot](https://apify.com/dz_omar/screenshot?fpr=smcx63)** — Fast webpage screenshots

---

### ⚖️ Legal & Compliance
- **Public Data Access**: Only processes publicly available data
- **Rate Limiting**: Respects service limits and terms of use
- **Data Protection**: No storage of personal information or unauthorized data collection
- **Commercial Use**: Suitable for business intelligence and research applications
