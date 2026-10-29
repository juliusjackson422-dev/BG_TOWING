# B.G. Towing & Repair — website

A static site. No build step, no server, no framework. Everything it needs is in this folder.

```
index.html           the whole site — markup, CSS and JS in one file
404.html             not-found page (keeps the phone number in front of people)
robots.txt           allows crawling, points at the sitemap
sitemap.xml          one URL; add entries here if pages are added
prelaunch-check.sh   run this before launch — lists everything still blocking
bg-img/              8 photographs + 2 video loops (4.2 MB)
```

---

## 0. Right now this is a DEMO deploy, not a launch

It is live at **https://juliusjackson422-dev.github.io/BG_TOWING/** so the owner can
look at it on a phone. While
it sits there, `index.html` carries `<meta name="robots" content="noindex, nofollow">`.

That tag is deliberate and it is load-bearing. Without it, Google can index a copy
of a real business's site at a URL the business does not own, carrying a 24/7 claim
nobody has confirmed. **`robots.txt` does not help here** — on a `github.io/PROJECT/`
URL, crawlers only ever read `github.io/robots.txt`, which is not ours. The meta tag
is the only control that actually applies.

**When the real domain is ready**, in this order:

1. Work through section 1 below until `./prelaunch-check.sh` exits clean.
2. Delete the `<meta name="robots">` line and the STAGING comment above it in `index.html`.
3. Point the domain at the host, and turn the GitHub Pages deploy **off**
   (Settings → Pages → Source → None) so the demo URL stops serving a second copy.
4. Re-run `./prelaunch-check.sh`. It must print `CLEAR TO LAUNCH.`

```bash
./prelaunch-check.sh
```

---

## 1. Before this goes live on a real domain

Six things on this site are **not verified**. They are marked `[CONFIRM]` in `index.html` —
search the file for that string to find every one.

| # | What | Why it matters |
|---|------|----------------|
| 1 | **24/7 towing** | Promised in the header strip, the hero and the footer. Yelp lists "24 hr Towing/Service Calls" but directory hours say Mon–Fri 9–5. This is the most load-bearing claim on the page. |
| 2 | **Second phone number** | BBB also lists (419) 601-3615. Only (419) 352-2626 is used. Is the other one dispatch, a cell, or dead? |
| 3 | **Rates** | The pricing section explains *how* a tow is priced and deliberately carries no figures. Adding the real numbers would make it the strongest part of the site. |
| 4 | **Reviews** | Yelp 3.5, Birdeye 4.3, trust-mechanics 4.2 — the platforms disagree, so no rating is shown and the three review frames are empty on purpose. Paste in real reviews with a name and month. Never invent them. |
| 5 | **Service areas** | The town list was inferred from the shop's location, not a published service map. |
| 6 | **Photography** | Licensed Unsplash stock. Their own trucks and shop would do more work here than on most sites. |

**Verified and safe to keep:** address 1089 N Main St Unit 3, Bowling Green OH 43402 · phone (419) 352-2626 ·
owner Dion Springstead · operating since 2013 · BBB accredited since Dec 2013 · USDOT 2505852 · MC-919533 ·
authorised U-Haul dealer. (Sources: BBB, FMCSA, Yelp, uhaul.com.)

---

## 2. Things that are not wired up yet

**The contact form has no backend.** It validates, assembles the request, and then says plainly that it
isn't connected. It never pretends a message was sent.

To connect it, open `index.html`, find:

```js
var FORM_ENDPOINT = "";
```

and put in a real endpoint — [Formspree](https://formspree.io), Cloudflare Pages Functions, or any handler
that accepts a JSON `POST`. The form then shows proper success and error states with no other changes.

**Open Graph URLs are absolute** and currently point at `https://bgtowingandrepair.com`. If the site is
served from any other domain, update `og:url`, `og:image`, `twitter:image` and `rel="canonical"` in the
`<head>`, plus the `<loc>` in `sitemap.xml` and the `Sitemap:` line in `robots.txt`.

---

## 3. Deploying to Cloudflare Pages

1. Put this folder in its own GitHub repository (the repo is what gets handed over later — see §4).
2. Cloudflare dashboard → **Workers & Pages** → **Create** → **Pages** → **Connect to Git**.
3. Pick the repo. Build settings:
   - **Framework preset:** None
   - **Build command:** *(leave empty)*
   - **Build output directory:** `/`
4. Deploy. You get a `*.pages.dev` URL immediately.
5. **Custom domain** → add `bgtowingandrepair.com` and `www.bgtowingandrepair.com`.

### About this domain specifically

`bgtowingandrepair.com` is **already registered and already on Cloudflare** — nameservers
`jimmy.ns.cloudflare.com` and `elisa.ns.cloudflare.com`, resolving to 104.21.8.58 / 172.67.138.153.
It currently serves a dead hosting-panel page.

So nobody needs to buy a domain. **Find out which Cloudflare account holds that zone before planning
anything** — if it is already the client's, there is no domain move at all and the custom domain is a
two-click job. If it sits in a previous web developer's account, it has to be moved (see §4, step 5).

---

## 4. Handing the site over to the client

**Cloudflare Pages projects cannot be transferred between accounts.** There is no API and no button.
The project has to be recreated in the client's own account. That sounds worse than it is: Pages reads
everything from the repository, so the real handover happens on GitHub, which *does* support transfers
natively.

1. **Get paid first.** Once the repo leaves your GitHub account you cannot take it back yourself.
2. **Confirm every `[CONFIRM]` item in §1.** You are handing them a site that speaks for their business.
3. **Client creates a free GitHub account** — the transfer needs a destination.
4. **Transfer the repo:** repo → Settings → General → Danger Zone → **Transfer ownership**.
   This is the moment the files become theirs.
5. **Client creates a free Cloudflare account** and connects the transferred repo to a new Pages project
   using the build settings in §3. Nothing carries over from your project — settings are re-entered by hand.
6. **Point the domain at their project.**
   - Domain already in *their* Cloudflare account → just add the custom domain. Done.
   - Domain in *your* account → it must move account-to-account. Export the DNS records first (without
     this, Cloudflare imports proxied records and the domain can throw a 1000 error), remove DNSSEC and
     any paid add-ons, add the domain to their account, update the nameservers at the registrar to the new
     ones Cloudflare assigns, then **Re-check now**. SSL certificates are reissued, not transferred.
   - Registered through **Cloudflare Registrar**? That needs a separate manual request to Cloudflare.
7. **Verify on the real domain** — not the `pages.dev` URL. Check the phone links, the form, the images
   and both video loops.
8. **Delete your Pages project and your copy of the repo.** Last, and only once their domain is confirmed
   live. A leftover duplicate can end up outranking the real site.

Cloudflare's own documentation for the domain move:
<https://developers.cloudflare.com/fundamentals/manage-domains/move-domain/>

---

## 5. Reusing this for the next business

Everything specific to this client is text in `index.html`. A new local-business site is mostly a
find-and-replace plus new photography:

| Find | Replace |
|------|---------|
| `(419) 352-2626` | the new phone number (display form) |
| `+14193522626` | the new phone number (`tel:` form — digits only, with country code) |
| `B.G. Towing` / `B.G. Towing & Repair` | the business name |
| `1089 N Main St, Unit 3` / `Bowling Green, OH 43402` | the address |
| `bgtowingandrepair.com` | the new domain (head meta, sitemap, robots) |
| `USDOT 2505852 · MC-919533` | their registration, or delete the block |
| `2013` (in `data-since`) | the founding year — the years badge counts up from it |

Then: swap `bg-img/`, rewrite the services, the triage cases in the `CASES` array, the FAQ, and the
service-area list. Change the three colour tokens at the top of the `<style>` block (`--amber`, `--cool`,
`--asphalt`) and the two font families to move it away from this brand.

**Do not reuse the review frames with text in them, and do not carry claims between clients.**
Every business gets its own `[CONFIRM]` list.

---

## 6. Performance notes

- Hero video is 1.1 MB, the service-area video 2.2 MB.
- The **hero video** loads on all screens but backs off on `prefers-reduced-motion` and `saveData`.
- The **service-area video** is desktop-only (≥900px), also gated on `saveData`, and lazy-loads when it
  scrolls near view. People use a towing site while stranded on a bad mobile connection; they should not
  download 2 MB of decoration to find a phone number.
- Both videos sit over a still image that is the real fallback. The page never depends on video arriving.
- All animation respects `prefers-reduced-motion`. The hero entrance only arms itself when it can
  actually run, so the page is never left blank waiting for a frame that doesn't come.
