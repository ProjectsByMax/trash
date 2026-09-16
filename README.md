# Trash Appeals

A working ledger for disputing Republic Services overage and Extra Yard charges
on the Coldwater Canyon Trust portfolio.

Drop the driver's photo in. Claude reads it against the appeal rubric, resolves
the geotag, checks it against the rent roll, and writes a row. You tick
**Submitted** when you file, and set **Result** when Republic answers.

---

## How a charge gets judged

Five questions, in order. Every ground that applies goes in the row, strongest
first — this app does not decide *whether* to appeal, only what the photo will
support and how strong the case is.

1. **Can you read the label on the bin?** The address painted on the container
   itself — not the building behind it. No label, or unreadable → *Can't see the label*.
2. **Sun glare?** Flare or blown highlights across the load.
3. **Blurry?** Too soft to tell material from background.
4. **Republic's own threshold.** Off their own training slide: an Extra Yard
   needs material above the lip across **more than 50% of the visible area**,
   *or*, where the view is limited, **at least 1 foot above the rim**.
5. **Above the rim at all?** Only two things prove it — the load breaks the
   silhouette against a wall, the sky or the street, or it physically lifts the
   lids off the rim.

Plus a sixth check on cab-camera frames: the geotag is resolved against LA
County's address data and compared to the rent roll. **Exact house number or
it's marked No**, and *GPS discrepancy* joins the grounds.

### The mistake this rubric exists to prevent

The truck camera sits about ten feet up and looks **down** through the opening
of the bin. Material sitting entirely inside, below the far wall, still appears
*above* the near rim in the picture. That illusion is what gets half-full bins
billed. If you can trace the bin's far wall behind the trash, the trash is
inside the bin — however high it looks.

### Where the address comes from

Only from the bin's painted label, the file name, or what you type. **Never from
GPS.** The geotag gets its own column and never touches the Address column.

---

## Setup

The page is static and holds no secrets. Everything sensitive lives behind a
Firebase function.

1. **Deploy the backend.** Setup steps are in the comment at the top of
   `firebase-function.js` (kept outside this repo). It holds the Anthropic API
   key in Google Secret Manager, stores rows in Firestore and photos in Cloud
   Storage, and is gated by a passcode.
2. **Publish this page.** Settings → Pages → Deploy from a branch → `main`,
   folder `/ (root)`.
3. **Connect them.** Open the page, expand **Connection**, paste the function
   URL and the passcode, hit **Test connection**.
4. **Paste the rent roll** into the Rent roll panel. Bin labels and file names
   are matched against it, and it is what the geotag check compares to.

Name photo files after the property where you can — `859 W 81.jpg` — and the
address fills itself in.

## Security

- No API key, passcode, rent roll or property data is in this repo. The repo is
  public; the data is not.
- Firestore and Cloud Storage are locked to `allow read, write: if false`. Only
  the function's Admin SDK gets through, and only after the passcode check.
- To cut off access: change the passcode secret and redeploy. To stop
  everything: delete the function. Neither touches the API key.

## Cost

Cloud Functions, Firestore and Storage stay inside their free tiers at this
volume. The only real charge is the Anthropic call — a fraction of a cent per
photo.
