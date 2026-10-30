# Mise — restaurant service demonstration

A responsive, dependency-free English web application for fictional restaurant service, ingredient transparency, dietary checks, kitchen reviews and administration.

**Demonstration using fictional data. Do not use to manage real allergies without validation and restaurant procedures.**

## Run locally

From this directory:

```sh
python3 -m http.server 4173 --directory dist
```

Open http://localhost:4173. Serve the files over HTTP: opening `index.html` through `file://` is not supported because the app uses JavaScript modules.

No package installation, build, credentials or backend are needed. Menu photos load from Unsplash and fonts from Google Fonts; system fonts remain available when offline. Photo illustrations do not specify the actual composition of a dish.

## Test

Requires Node.js 20 or later:

```sh
node --test tests/engine.test.mjs
```

23 engine tests cover direct and nested matches; compound relationships; cross-contact; unknown information; allergies versus intolerances; shared dishes; reviewed modifications; unknown declarations and free text; late restrictions; recipe and supplier changes; review invalidation; fresh review requirements even when new checks have no direct match; partial submission; preparation blocks; review outcomes; cycle prevention; roles; archiving; reassignment; identifiers; and JSON persistence.

The app also includes 12 independently loadable scenarios. Use **Sample scenarios** on the overview, or **About this demo → Explore sample scenarios**. They use Table 10. Loading another case cancels that table’s previous active sample items while retaining their snapshots and review records.

## Try a service

1. In **Waitstaff**, open a table, add diners and explicitly record each declaration. Missing declarations stay **Not yet asked**.
2. Browse the menu or use **Add a dish**. Assign one or more diners, quantity, kitchen notes and only configured, reviewed recipe variants.
3. Inspect the live checks and the complete ingredient paths. Kitchen notes do not alter composition. Unstructured diner declarations require manual review.
4. Choose **Review & send**. Eligible dishes become **Received**; blocked dishes become **Awaiting review**. They can coexist in the same order.
5. Switch to **Kitchen**. Record a composition proposal or **Cannot prepare under the requested conditions**. A proposal cannot override a direct match, a potential risk or incomplete information. Canceling a dish preserves its record.
6. Correct composition or information, rerun the checks and record a fresh review when the earlier one is invalid. Only eligible dishes can progress through preparation, ready and served.
7. Switch to **Administration** to manage dishes, recipe variants, categories, availability, ingredients, compound composition, allergen associations and aliases, cross-contact equipment, fictional supplier/specification references, review status and dates, profiles and roles, tables, internal procedures and status display labels.
8. Inspect **History**, **Order snapshots** and **Review history**. The ingredient editor previews affected dishes and active items before saving.

For the recipe-change sample, load the case in Waitstaff or Administration, then select Administration and use **Apply scenario change**. The late-restriction sample can be applied in Waitstaff. Both preserve the original review and demonstrate invalidation.

## Architecture

- `dist/engine.mjs`: single shared check engine, fictional entity seed, recursive composition traversal, cycle validation, snapshots, fingerprints, permission and transition guards, review records and samples.
- `dist/app.mjs`: connected screens, local persistence, navigation, forms, role simulation, scenario loading, cross-tab synchronization and data export.
- `dist/styles.css`: responsive service workspace with mobile navigation, tablet layouts and kitchen tickets.
- `tests/engine.test.mjs`: executable checks for the critical workflows.
- `.openai/hosting.json`: static Sites hosting declaration.

Stable identifiers and structured relationships drive detection, including specific ingredient restrictions. Ingredient/allergen aliases are catalog metadata, never a substitute for structured IDs. Allergen lists displayed for a dish are calculated from its ingredient graph. Removal and substitution are only possible through administration-configured reviewed variants. Equipment and dish cross-contact data remain independent of ingredient removal.

Orders keep recipe/information snapshots when relevant information changes, plus snapshots of every kitchen review. Reviews retain author, timestamp, notes, proposal composition and the information used. Relevant changes invalidate earlier reviews, interrupt active preparation where needed and require another review. Archived allergen records remain detectable; referenced ingredients and historical orders cannot be silently deleted through the UI.

## Demonstration limits

- The selector **simulates access**. Client-side guards demonstrate behavior, but cannot provide secure authorization. Production requires server-side identity, permissions and validation of all transitions and blocks.
- There is **no real multiuser backend**. `localStorage` retains data in this browser and origin; storage events update other tabs on the same origin. Other browsers/devices do not share it. Concurrent writes are last-write-wins. A local preview and hosted site have separate stores.
- Data remains only while the browser retains its local storage. The app reports write failures and supports JSON export. **Reset demo & clear diner data** clears the current store and restores new fictional sample data. Previously downloaded exports are outside this reset.
- Simple identifiers are used instead of customer names. Do not enter real health or personal information.
- Supplier information, specification references, ingredients, risks and review dates are illustrative and unvalidated. The sample food graph is not authoritative food/allergen guidance.
- The prototype deliberately uses conservative blocks: an unknown recipe component requires review, and changes to recorded recipe metadata can invalidate earlier reviews even if the allergen result is unchanged.
- Kitchen review notes alone do not resolve unstructured declarations, unverified composition or equipment risk. Clarify declarations into structured records and update reviewed information through the appropriate role.
- Status labels are configurable. The underlying status identifiers, transitions and role permission rules remain fixed so administration cannot remove blocking rules.
- No real payments, messages, external notifications, supplier-document verification or integrations requiring credentials are implemented. Exported JSON files are ordinary local downloads.

## Validation record

Engine tests: **23 passed**. Browser checks: responsive mobile and tablet layouts, screen navigation, pre-submission summary, partial submission, kitchen preparation of an eligible dish alongside a blocked dish, review creation without overriding the block, ingredient impact preview and save, invalidation across views, retained state after reload, and menu images. No application runtime errors were captured during these checks. Large desktop CSS is implemented but was not independently exercised at a full desktop viewport.
