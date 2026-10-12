# FitFindr

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, every
> command, and what to do when something breaks.
>
> Once 'python test.py' passes:
>
> '''bash
> python app.py listings --full -n 6      # read the data (Milestone 1)
> python app.py fields                    # what you can filter on
> python app.py ask 'vintage graphic tee under $30'
> '''
>
> All three tools are stubs, so that last command will do nothing useful yet.
> That's the starting position.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     This is your submission. Fill each section in as you finish the milestone
     it belongs to — don't leave it all to the end.

     Unit 3 asks for the first five sections. Unit 4 adds the five below them.
     Leave the unit 4 sections alone until then; they're here so you know
     what's coming.

     Everything is pasted as TEXT. No screenshots, no images, no video links.
     A typed block of output gets full credit; a picture of the same output
     gets none.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 3 — THE BUILD ═══════════════════════ -->

## What This Does

FitFindr takes a plain-language request like "vintage graphic tee under $30"
and pulls out the item, size, and price ceiling. It searches the listings
data, picks the best match, and suggests outfits using pieces from the user's
wardrobe. It then writes a short caption for the find. If nothing
matches, it stops early and tells the user what to change (price, size, or
wording) instead of continuing with nothing.



---

## Tool Inventory

<!-- Four lines per tool. This is worth 2 points and it's the single most
     common place students lose them.

     "Returns a list" earns NOTHING. The description has to say what is IN
     the list.

     The empty case isn't optional either — it's the thing your loop branches
     on, and if you don't decide it here you'll discover it as a crash in
     Milestone 5. -->

### search_listings

- **What it does:** searches through listing.json for listings whose title, description, or style_tags loosely match the text.
- **Inputs:** description(str), size(str), max_price(float)
- **Returns:** a list of dict, each with id, title, price, size, category, colors, style_tags, platform
- **When it has nothing:** return an empty list []

### suggest_outfit

- **What it does:** Takes one listing and the user's wardrobe, returns 1–3 outfit pairings from wardrobe items whose category, colors, or style_tags complement the new item.
- **Inputs:** new_item (dict), wardrobe (list of wardrobe item dicts)
- **Returns:** a string with one or two outfit suggestions.
- **When it has nothing:** if the wardrobe has no items, returns general styling advice (a non-empty string) instead of failing.

### create_fit_card

- **What it does:** Calls the model to write a short, postable caption for the outfit.
- **Inputs:** outfit (str), new_item (dict)
- **Returns:** a 2-4 sentence caption string.
- **When it has nothing:** if outfit is empty/None, returns None rather than calling the model on nothing.

---

## Planning Loop

<!-- Your branch rule, stated as a rule — the condition AND both paths — plus
     the file and function that holds it.

     Like this:
       "If search_listings returns an empty list, put a message in the session
        and stop. Otherwise take the first result and go to suggest_outfit."
        — agent.py::run_agent

     The grader checks your code against what you claim here, so the file and
     function have to be real. -->

**Branch rule:** If search_listings returns an empty list, put a message in
'session["error"] naming what the user could change and stop. Otherwise, take
the first result and go to suggest_outfit.

**Where it lives:** agent.py::run_agent

**How the query is parsed:** <!-- regex, string splitting, or asking the model — say which -->Regex inside run_agent, no model call.

**What moves through the session:** <!-- which fields, in what order -->parsed → search_results → selected_item →
outfit_suggestion → fit_card. Each result is written to the session and read back for the next step.

---

## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.
     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

'''
$ python app.py ask "vintage graphic tee under $30"

'''
  Found:    Y2K Baby Tee — Butterfly Print — $18.0 on depop

  Outfit:   Here are two outfit suggestions combining the new Y2K Butterfly Baby Tee with pieces from your current wardrobe:

### Outfit 1: 2000s Streetwear Contrast
* **Top:** Y2K Baby Tee (Butterfly Print)
* **Bottoms:** Baggy straight-leg jeans (dark wash)
* **Outerwear:** Black cropped zip hoodie (worn open)
* **Shoes:** Chunky white sneakers
* **Accessories:** Black crossbody bag

**Why it works:** This plays on the classic Y2K silhouette by pairing a tight, fitted baby tee with ultra-baggy denim. Layering the black cropped zip hoodie on top (left unzipped to show off the butterfly graphic) leans into the streetwear aesthetic, while the chunky white sneakers tie the look together.

### Outfit 2: Soft Earth-Tone Casual
* **Top:** Y2K Baby Tee (Butterfly Print)
* **Bottoms:** Wide-leg khaki trousers
* **Outerwear:** Vintage black denim jacket
* **Shoes:** Black combat boots
* **Accessories:** Brown leather belt

**Why it works:** This outfit balances the hyper-feminine, cottagecore-leaning butterfly print with tougher, grounded elements. Tucking the baby tee into the wide-leg khaki trousers (accented with the brown leather belt) creates a clean, intentional waistline, while the vintage black denim jacket and black combat boots add an edgy contrast that keeps the look modern.

  Fit card: Obsessed with this Y2K butterfly baby tee I just scored on Depop for only $18! 🦋✨ Thinking of styling it with baggy jeans and a zip hoodie for a streetwear vibe, or dressing it down with khaki trousers. So many ways to wear this!


**The three tools, tested one at a time**

'''
$ python -c "from tools import search_listings; print(search_listings('graphic tee', max_price=30))"

'''
[{'id': 'lst_002', 'title': 'Y2K Baby Tee — Butterfly Print', 'description': 'Super cute early 2000s baby tee with butterfly graphic. Fitted crop length. Tag says medium but fits like a small.', 'category': 'tops', 'style_tags': ['y2k', 'vintage', 'graphic tee', 'cottagecore'], 'size': 'S/M', 'condition': 'excellent', 'price': 18.0, 'colors': ['white', 'pink', 'purple'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_006', 'title': 'Graphic Tee — 2003 Tour Bootleg Style', 'description': 'Vintage-style bootleg tee with faded graphic. Slightly boxy fit. 100% cotton, soft and worn-in.', 'category': 'tops', 'style_tags': ['graphic tee', 'vintage', 'grunge', 'streetwear', 'band tee'], 'size': 'L', 'condition': 'good', 'price': 24.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_017', 'title': 'Mesh Long-Sleeve Top — Black', 'description': 'Sheer black mesh long-sleeve. Great for layering under a graphic tee or over a bralette. Stretchy material, fits true to size.', 'category': 'tops', 'style_tags': ['y2k', 'grunge', 'goth', 'layering'], 'size': 'S/M', 'condition': 'excellent', 'price': 15.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_033', 'title': 'Vintage Band Tee — Faded Grey', 'description': 'Faded grey band-style tee with distressed graphic. Crew neck. Fits boxy. Well-loved but no holes or major damage.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'band tee', 'graphic tee', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 19.0, 'colors': ['grey', 'charcoal'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_011', 'title': 'Low-Rise Cargo Pants — Khaki', 'description': 'Y2K era low-rise cargo pants. Lots of pockets. Khaki color, slightly distressed at the hems. Great for layering with a long tee.', 'category': 'bottoms', 'style_tags': ['y2k', 'cargo', '2000s', 'streetwear'], 'size': 'W29', 'condition': 'fair', 'price': 27.0, 'colors': ['khaki', 'tan'], 'brand': None, 'platform': 'poshmark'}, {'id': 'lst_015', 'title': 'Vintage Graphic Hoodie — Faded Black', 'description': 'Faded black pullover hoodie with barely-visible vintage graphic on the chest. Cozy interior. Some pilling but adds to the worn-in look.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'graphic', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 26.0, 'colors': ['black', 'charcoal'], 'brand': None, 'platform': 'depop'}]
'''
$ python -c "from tools import suggest_outfit; from utils.data_loader import get_example_wardrobe, load_listings; print(suggest_outfit(load_listings()[0], get_example_wardrobe()))"

'''
Here are two ways to style the vintage Levi's 501 jeans using pieces already in your wardrobe:

**Outfit 1: Effortless Casual Streetwear**
*   **Bottoms:** Vintage Levi's 501 Jeans (Medium Wash)
*   **Tops:** White ribbed tank top
*   **Outerwear:** Vintage black denim jacket
*   **Shoes:** Chunky white sneakers
*   **Accessories:** Black crossbody bag

*Why it works:* The medium-wash 501s and white ribbed tank create a classic, minimal base. Tossing the vintage black denim jacket on top leans into the double-denim trend while playing nicely with the vintage aesthetic, and the chunky white sneakers finish the look with a fresh streetwear edge.

**Outfit 2: Cozy Contrast**
*   **Bottoms:** Vintage Levi's 501 Jeans (Medium Wash)
*   **Tops:** Oversized grey crewneck sweatshirt
*   **Accessories:** Brown leather belt, Black crossbody bag
*   **Shoes:** Black combat boots

*Why it works:* The 501s offer a structured, straighter fit compared to your current baggy jeans, which pairs great with the slouchy volume of the oversized grey crewneck. Tucking in the sweatshirt with the brown leather belt adds a touch of intentional styling, and the black combat boots anchor the outfit with a cool, grunge-infused contrast.

'''
$ python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('jeans and white sneakers', load_listings()[0]))"

'''
Scored these vintage Levi's 501 jeans for just $38.00 on depop! They’re the absolute best medium wash. Can't wait to style them with my favorite white sneakers. ✨👖

---

## How I Used AI

<!-- Two specific moments. What you asked, what came back, what you changed.

     "I used Claude to help me code" is not enough.

     "I gave Claude my search_listings spec. It returned None on no match
     instead of an empty list, so I changed it" is the level we want. -->

**Moment 1**

- *What I asked for:* I gave Claude the field names from my listings data and asked it to draft my three tool specs for the Tool Inventory.
- *What came back:* It said suggest_outfit returns a list of outfit dicts and returns [] when the wardrobe is empty.
- *What I changed:* When I opened tools.py, the starter said suggest_outfit returns a string and gives general styling advice when the
  wardrobe is empty. So I changed my empty-wardrobe criterion to match.

**Moment 2**

- *What I asked for:* I asked Claude to write 'create_fit_card'.
- *What came back:* When I ran the tool three times on the same item, I also got identical output each time.
- *What I changed:* The identical outputs came from the cache, so I set 'AI201_CACHE=0' and got three different captions.

**Moment 3**

- *What I asked for:* Claude gave me a query to test the model-unavailable
  handler with a bad key in `.env`.
- *What came back:* The agent ran normally and printed `0 model calls this
  session, 2 served from cache`.
- *What I changed:* I turned the cache off with `set AI201_CACHE=0`, typed
  with no spaces around the `=` (my first attempt had spaces and did
  nothing), and reran so the model was actually called.

**Moment 4**

- *What I asked for:* I asked Claude to check my run log against my criteria.
- *What came back:* It counted fit card lengths with a script and marked
  every criterion MET. It also pointed out that criterion 3 could not fail,
  because the trace reads the id from the same session value that was passed
  on.
- *What I changed:* I did not call criterion 3 a clean pass. I wrote it up in
  Verdicts and Diagnoses as a target that was too easy, and named it as the
  one I would tighten.

<!-- ═══════════════════════ UNIT 4 — THE TEST ═══════════════════════

     Don't fill these in during unit 3.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Run Log — Before

<!-- Five criteria, five tries each, in this exact format.

     Five, because your criteria are written out of five. Mark each try PASS
     or FAIL, count the passes, and read that count against your target — a
     row targeting 4 of 5 with three PASS cells is MISSED (3/5).

     'python run_eval.py --label before' runs everything and writes the table
     into results/. Paste it here and fill in the verdicts. -->

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1. A matching query completes all three tools | 4 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 2. An impossible query stops before the second tool | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 3. Item in session matches item passed on | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 4. Fit card is under 280 characters | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 5. Empty wardrobe returns advice and continues | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |

**Real output from one try**, pasted as text, naming the file and function
that produced it:

'''
**Criterion 1** — produced by 'agent.py::run_agent' (query: 'vintage graphic tee under $30')

'''
[1] parse query (regex)
      in:  vintage graphic tee under $30
      out: {'description': 'vintage graphic tee', 'size': None, 'max_price': 30.0}
[2] search_listings (via MCP)
      in:  {'description': 'vintage graphic tee', 'max_price': 30.0}
      out: 10 items: Y2K Baby Tee — Butterfly Print, Graphic Tee — 2003 Tour Bootleg Style, Vintage Band Tee — Faded Grey … +7 more
[3] select first result
      out: Y2K Baby Tee — Butterfly Print ($18.0, depop)
      →    stored in session['selected_item'], id lst_002
[4] suggest_outfit
      in:  id lst_002: Y2K Baby Tee — Butterfly Print + wardrobe of 10 items
      out: Here are two outfit suggestions that pair the Y2K butterfly baby tee with items from your current wardrobe:  *…
[5] create_fit_card
      in:  outfit text + selected item
      out: Found my ultimate Y2K vibe with this butterfly baby tee for just $18.0! I can't wait to style it with baggy de…
'''

**Criterion 2** — produced by 'agent.py::run_agent' (query: 'designer ballgown size XXS under $5')

'''
[1] parse query (regex)
      in:  designer ballgown size XXS under $5
      out: {'description': 'designer ballgown', 'size': 'XXS', 'max_price': 5.0}
[2] search_listings (via MCP)
      in:  {'description': 'designer ballgown', 'size': 'XXS', 'max_price': 5.0}
      out: [] (empty)
      →    branch: empty, stopping before suggest_outfit

No listings matched 'designer ballgown'. Try raising your price limit, trying a different size or leaving size out, or using simpler, more general words.
'''

**Criterion 3** — produced by 'agent.py::run_agent' (query: 'silk slip dress in midi length under $40')

'''
[3] select first result
      out: 90s Silk Slip Dress — Floral, Midi Length ($30.0, depop)
      →    stored in session['selected_item'], id lst_013
[4] suggest_outfit
      in:  id lst_013: 90s Silk Slip Dress — Floral, Midi Length + wardrobe of 10 items
      out: Here are two outfit suggestions that seamlessly blend the feminine, 90s vintage vibe of the floral silk slip d…
'''

**Criterion 4** — produced by 'tools.py::create_fit_card' (query: '90s track jacket in size M', 171 characters)

'''
Found the ultimate 90s track jacket on Poshmark for just $45! Obsessed with styling this navy and white stripe beauty over baggy denim for effortless streetwear vibes. ✨🏃💨
'''

**Criterion 5** — produced by 'tools.py::suggest_outfit' and 'agent.py::run_agent' (query: 'denim jacket under $50', empty wardrobe)

'''
[4] suggest_outfit
      in:  id lst_007: Denim Jacket — Light Wash, Cropped + wardrobe of 0 items
      out: Since this is your first wardrobe item, this versatile light-wash denim jacket serves as a great foundational …
[5] create_fit_card
      in:  outfit text + selected item
      out: Scored this adorable cropped light-wash denim jacket for just $42 on Poshmark! It’s the ultimate versatile sta…
'''

Outfit advice returned:

'''
Since this is your first wardrobe item, this versatile light-wash denim jacket serves as a great foundational piece that leans easily into both casual streetwear and classic vintage aesthetics. Pair it with high-waisted trousers, skirts, or wide-leg cargo pants to complement the cropped cut and balance your proportions. For layering, it looks effortless thrown over simple fitted tops, cropped tanks, or cozy hoodies.
'''
'''

---

## Verdicts and Diagnoses

<!-- MET or MISSED per criterion against LAST UNIT's target, plus a sentence on
     how you decided.

     Then, for every miss: which of the four places it happened — a tool, the
     loop's branch, the session, or the model's output — AND the mechanism.

     Not a diagnosis:  "The fit card was bad."
     A diagnosis:      "The fit card criterion missed on 2 of 5 items. Both had
                        an empty brand field. My prompt puts the brand in the
                        first sentence, so the card opened with a blank and read
                        like a fragment. The tool worked; the prompt assumed a
                        field that isn't always there."

     Look for a pattern. Three misses on the same tool is one problem, not
     three. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | A matching query completes all three tools | 4 of 5 | MET (5/5) | A try passed only if the trace showed all five steps and a fit card was returned. |
| 2 | An impossible query stops before the second tool | 5 of 5 | MET (5/5) | A try passed only if the trace ended at step 2 with no 'suggest_outfit' step and the message named something to change. |
| 3 | Item in session matches item passed on | 5 of 5 | MET (5/5) | The id in the select step matched the id in the 'suggest_outfit' in every try. |
| 4 | Fit card is under 280 characters | 5 of 5 | MET (5/5) | I counted the characters in each fit card. In this scenario they ran from 171 to 219. |
| 5 | Empty wardrobe returns advice and continues | 5 of 5 | MET (5/5) | A try passed only if the trace showed 'wardrobe of 0 items', the advice was non-empty, the run did not stop early, and a fit card was returned. |

**Diagnoses**

I missed no criteria, so there is no failing tool, branch, session value, or
model output to name, and no pattern to report.

My targets were too easy in places. Criteria 2, 3, and 5 test branches decided
by plain code, not the model, so 5 of 5 was close to guaranteed. Criterion 3 is
the weakest. In 'agent.py::run_agent', 'suggest_outfit' receives
'session["selected_item"]' directly, and the trace reads the id from that same
value, so the two ids could never differ. It is the one I would tighten.

---

## Loop Trace

<!-- One full run, printed step by step, with the MCP call visible in it.

     'python app.py ask '...' --trace' once you've added the trace.step()
     calls in Milestone 2.

     Worth pasting BOTH the happy path and the empty-search path. The empty
     one should be visibly shorter, because it stops. If your two traces are
     the same length, your branch isn't working — and this is the fastest way
     anyone will ever find that out. -->

**Happy path**

'''
[1] parse query (regex)
      in:  vintage graphic tee under $30
      out: {'description': 'vintage graphic tee', 'size': None, 'max_price': 30.0}
[2] search_listings (via MCP)
      in:  {'description': 'vintage graphic tee', 'max_price': 30.0}
      out: 10 items: Y2K Baby Tee — Butterfly Print, Graphic Tee — 2003 Tour Bootleg Style, Vintage Band Tee — Faded Grey … +7 more
[3] select first result
      out: Y2K Baby Tee — Butterfly Print ($18.0, depop)
      →    stored in session['selected_item']
[4] suggest_outfit
      in:  Y2K Baby Tee — Butterfly Print + wardrobe of 10 items
      out: Here are two outfit suggestions that pair the Y2K butterfly baby tee with pieces already in your wardrobe:  **…
[5] create_fit_card
      in:  outfit text + selected item
      out: Found my new favorite Y2K butterfly baby tee for just $18 on Depop! 🦋✨ Obsessed with styling it baggy jeans or…
'''

**Empty search**

'''
[1] parse query (regex)
      in:  designer ballgown size XXS under $5
      out: {'description': 'designer ballgown', 'size': 'XXS', 'max_price': 5.0}
[2] search_listings (via MCP)
      in:  {'description': 'designer ballgown', 'size': 'XXS', 'max_price': 5.0}
      out: [] (empty)
      →    branch: empty, stopping before suggest_outfit

No listings matched 'designer ballgown'. Try raising your price limit, trying a different size or leaving size out, or using simpler, more general words.
'''

**On the MCP move:** I add 'search_listings' in mcp_server.py and changed 'agent.py::run_agent' to call it through mcp_client.call_tool instead of calling the function directly. Inputs that are `None` are left out of the call. The other two tools still run directly. The loop behaved the same: an impossible query still came back as an empty list, and the trace shows `[] (empty)` for the MCP call, so the branch still fired. Each call is slower because it starts the server every time.
<!-- what changed in your code, and whether anything
behaved differently afterwards. If the rewire didn't work, say exactly where it
broke — the error text and the last thing that worked. That earns the point in
full. -->



---

## The Improvement

<!-- What you changed, why your diagnosis pointed at it, and the after-run in
     the same table format. One change, measured properly.

     'python run_eval.py --label after' -->

**What I changed:** I added a second branch in 'agent.py::run_agent'. After
'create_fit_card' returns, the loop checks the length. If the fit card is over
280 characters, it trims the card to the last complete sentence that fits and
records a 'fit card too long' step in the trace. Otherwise it returns the card
as is.

**Which failure it was meant to fix:** None of my criteria were missed, so
there was no failure to fix.

### Run Log — After

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1. A matching query completes all three tools | 4 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 2. An impossible query stops before the second tool | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 3. Item in session matches item passed on | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 4. Fit card is under 280 characters | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 5. Empty wardrobe returns advice and continues | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |

**Criterion 1** — produced by 'agent.py::run_agent' (query: 'vintage graphic tee under $30')

'''
[1] parse query (regex)
      in:  vintage graphic tee under $30
      out: {'description': 'vintage graphic tee', 'size': None, 'max_price': 30.0}
[2] search_listings (via MCP)
      in:  {'description': 'vintage graphic tee', 'max_price': 30.0}
      out: 10 items: Y2K Baby Tee — Butterfly Print, Graphic Tee — 2003 Tour Bootleg Style, Vintage Band Tee — Faded Grey … +7 more
[3] select first result
      out: Y2K Baby Tee — Butterfly Print ($18.0, depop)
      →    stored in session['selected_item'], id lst_002
[4] suggest_outfit
      in:  id lst_002: Y2K Baby Tee — Butterfly Print + wardrobe of 10 items
      out: Here are two outfit suggestions that pair the Y2K butterfly baby tee with pieces already in the wardrobe:  ###…
[5] create_fit_card
      in:  outfit text + selected item
      out: Found this precious Y2K baby tee with the cutest butterfly print for just $18 on Depop! 🦋✨ Obsessed with styli…
'''

**Criterion 2** — produced by 'agent.py::run_agent' (query: 'designer ballgown size XXS under $5')

'''
[1] parse query (regex)
      in:  designer ballgown size XXS under $5
      out: {'description': 'designer ballgown', 'size': 'XXS', 'max_price': 5.0}
[2] search_listings (via MCP)
      in:  {'description': 'designer ballgown', 'size': 'XXS', 'max_price': 5.0}
      out: [] (empty)
      →    branch: empty, stopping before suggest_outfit

No listings matched 'designer ballgown'. Try raising your price limit, trying a different size or leaving size out, or using simpler, more general words.
'''

**Criterion 3** — produced by 'agent.py::run_agent' (query: 'silk slip dress in midi length under $40')

'''
[3] select first result
      out: 90s Silk Slip Dress — Floral, Midi Length ($30.0, depop)
      →    stored in session['selected_item'], id lst_013
[4] suggest_outfit
      in:  id lst_013: 90s Silk Slip Dress — Floral, Midi Length + wardrobe of 10 items
      out: Here are two outfit suggestions that seamlessly blend the feminine, 90s aesthetic of the silk slip dress with …
'''

**Criterion 4** — produced by 'tools.py::create_fit_card' (query: '90s track jacket in size M', 222 characters)

'''
Scored this ultimate 90s navy/white track jacket for just $45 on Poshmark! It's giving off-duty model vibes whether I style it with baggy denim or wide-leg khakis. Grab it before I change my mind and keep it for myself! 👟✨
'''

**Criterion 5** — produced by 'tools.py::suggest_outfit' and 'agent.py::run_agent' (query: 'denim jacket under $50', empty wardrobe)

'''
[4] suggest_outfit
      in:  id lst_007: Denim Jacket — Light Wash, Cropped + wardrobe of 0 items
      out: This versatile cropped denim jacket pairs effortlessly with high-waisted bottoms like wide-leg trousers, maxi …
[5] create_fit_card
      in:  outfit text + selected item
      out: Obsessed with this light wash cropped denim jacket I just snagged on Poshmark for $42! 😍 It's about to be my g…
'''

Advice returned:

'''
This versatile cropped denim jacket pairs effortlessly with high-waisted bottoms like wide-leg trousers, maxi skirts, or high-rise jeans to balance the shorter hemline. For a casual streetwear vibe, layer it over a simple cropped white tee, a ribbed tank, or a cozy oversized hoodie. Finish the look with classic sneakers, chunky loafers, or ankle boots to elevate the vintage aesthetic.
'''

**Did it help, and how do I know:**
All five criteria were MET in both runs, and the new branch never fired
in the after run, because none of the 25 fit cards was over 280 characters.
The longest card was 277 characters before and 253 after. That difference is
ordinary variation in the model's wording, not the branch, since nothing was
trimmed.
<!-- If it made things worse, say that. Honestly reported, that earns full
     credit and is more interesting than one that worked. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped where
     you did. "I ran out of time" is fine if it's true. Pretending nothing is
     left is not. -->
No criterion was missed in either run, so nothing is "still missed."


<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 3

       [ ] criteria.md has five numbered criteria, each with a target
       [ ] Each criterion has a reason underneath it
       [ ] All five unit 3 sections above have real content
       [ ] Tool Inventory: all three tools, inputs WITH TYPES, a specific
           return value, and the empty case
       [ ] Planning Loop names the branch rule and agent.py::run_agent
       [ ] Sample Run: one full query plus the three per-tool tests, as text
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN, you submit the same one
           next unit

     SUBMISSION CHECKLIST — unit 4

       [ ] mcp_server.py exists with one tool registered
           (or a written record of exactly where the rewire broke)
       [ ] Run Log — Before, five criteria, five tries each
       [ ] Real output pasted underneath, naming file and function
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, naming a place AND a mechanism
       [ ] Loop Trace, with the MCP call visible in it
       [ ] All three failure modes triggered and handled
       [ ] One improvement, with Run Log — After in the same format
       [ ] What's Still Broken
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository. Your commit history is what
     shows your criteria existed before your results did.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
