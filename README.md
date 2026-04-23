# ADAS Calibration Estimator

A web-based tool for generating OEM-sourced ADAS calibration requirements based on vehicle make, damage zones, and installed systems.

## How it works

1. User identifies vehicle (VIN decode or manual year/make/model)
2. User clicks damage zones on a vehicle diagram
3. User confirms which ADAS systems are installed (auto-detected from VIN where possible)
4. Tool generates a report with verbatim OEM trigger language from uploaded position statements and job aids

Every calibration is traced to a specific OEM document. If no source data exists for a make, the tool says so rather than fabricating content.

---

## File structure

```
adas-estimator/
├── index.html              ← main estimator page
├── car.svg                 ← top-down car image
├── makes/                  ← one file per make
│   ├── bmw.json
│   ├── ford.json
│   ├── gm.json             (covers Chevrolet, Cadillac, Buick, GMC)
│   ├── honda.json          (covers Honda, Acura)
│   ├── mazda.json
│   ├── mercedes-benz.json
│   ├── nissan.json         (covers Nissan, Infiniti, Mitsubishi)
│   ├── stellantis.json     (covers Chrysler, Jeep, Dodge, Ram)
│   ├── subaru.json
│   ├── toyota.json         (covers Toyota, Lexus)
│   └── volkswagen.json     (covers VW, Audi)
└── README.md               ← this file
```

### Makes currently included
Built from uploaded OEM job aids: BMW, Ford, GM, Honda, Nissan.
Built from OEM service info (as quoted in Opus ADAS reports): Toyota, Subaru, Mercedes-Benz, Mazda, Volkswagen.
Built from OEM scanning position statement only: Stellantis.

### Makes NOT yet included
Hyundai, Kia, Genesis, Jaguar, Land Rover, Volvo, Tesla, Tier-1-only brands.
These need trigger data sourced from AllData or OEM service portals and added as new JSON files.

---

## How to add a new make

1. Create a new file in `makes/` named after the make, lowercase, with `.json` extension (e.g., `makes/hyundai.json`).
2. Copy the structure of an existing file (e.g., `makes/honda.json`) and edit the fields.
3. Add the brand to the `BRAND_TO_MAKE_FILE` map near the top of `index.html` (find the section marked "MAKES INDEX").
4. That's it. No other code changes needed.

### JSON file structure

```json
{
  "make": "MakeName",
  "includes_brands": ["MakeName", "SubBrand1", "SubBrand2"],
  "source": {
    "document": "Full name of the OEM document this came from",
    "file": "original_filename.pdf",
    "portal": "oem-service-portal.com"
  },
  "general_requirements": "One-sentence summary of the OEM's general pre/post-scan and calibration position.",
  "components": [
    {
      "component": "Name of the component as the OEM calls it",
      "system": "front-radar",  // one of: front-radar, front-camera, front-corner-radar, bsd, rear-camera, surround, park-assist, occupant, seat-belt, other
      "location": "Where the part physically lives on the vehicle",
      "zones": ["front-bumper"],  // which damage zones trigger this component (use "*" for post-collision universal)
      "calibration_note": "Procedural notes — targets required, scan tool, etc.",
      "trigger_lead": "Calibrate this component when any of the following apply:",
      "triggers": [
        "First verbatim trigger from the OEM document",
        "Second verbatim trigger",
        "etc."
      ]
    }
  ]
}
```

### Zone IDs (for reference)
`front-bumper`, `windshield`, `liftgate`, `rear-bumper`, `rf-fender`, `rf-door`, `rr-door`, `rq-panel`, `lf-fender`, `lf-door`, `lr-door`, `lq-panel`, `*` (universal)

### System IDs (for reference)
`front-camera`, `front-radar`, `front-corner-radar`, `bsd`, `rear-camera`, `surround`, `park-assist`, `occupant`, `seat-belt`, `other`

---

## How to put this on GitHub (first time)

### 1. Create a GitHub account
Go to [github.com](https://github.com) and sign up. Free.

### 2. Create a new repository
- Click the **+** in the top right → **New repository**
- Name it something like `adas-estimator`
- Make it **Public** (required for free hosting)
- Check **Add a README file**
- Click **Create repository**

### 3. Upload the files
- On your new repo page, click **Add file** → **Upload files**
- Drag the contents of this folder (`index.html`, `car.svg`, `README.md`, and the entire `makes/` folder) into the upload area
- Scroll down and click **Commit changes**

### 4. Turn on GitHub Pages (makes it a live website)
- In your repo, click **Settings** (top bar)
- Scroll down the left sidebar to **Pages**
- Under **Source**, select **Deploy from a branch**
- Under **Branch**, select `main` and `/ (root)`, then click **Save**
- Wait 1-2 minutes. GitHub will show you a URL like `https://your-username.github.io/adas-estimator/` — that's your live website.

### 5. Editing files later
- In your repo, click any `.json` file in the `makes/` folder
- Click the pencil icon (top right) to edit
- Make changes
- Scroll down and click **Commit changes**
- Your website updates automatically in 1-2 minutes

---

## Notes on data sourcing

### Tier 1 — OEM Job Aids (highest confidence)
Honda, Ford, GM, BMW, Nissan — all trigger language comes directly from verified OEM job aids published for collision repairers.

### Tier 2 — OEM Service Info via Opus (high confidence, secondary-source)
Toyota, Subaru, Mercedes-Benz, Mazda, Volkswagen — trigger language is reproduced from OEM service information (Toyota TIS, Subaru techinfo, Xentry, Mazda SI, VW BRM) as quoted in Opus ADAS inspection reports. These are legitimate OEM texts; the Opus reports are just where we sourced them from.

### Tier 3 — Scan-tool position statements only (limited — no trigger tables)
Stellantis, Hyundai, Kia, JLR, Volvo, Infiniti, Lexus — the public OEM documents are scanning mandates that don't include component-level trigger tables. Per-component triggers must be sourced from the OEM's subscription service info portal (techauthority.com, hyundaitechinfo.com, etc.) or AllData.

### Not included (no source material yet)
Tesla, Mitsubishi (covered by Nissan file), standalone brands.

---

## VIN decoding

The tool uses NHTSA's free vPIC API for basic VIN decode and partial ADAS equipment detection.

**Limitations:**
- NHTSA returns "Standard/Optional/Not Available" for some ADAS features but not all
- Surround view, front corner radar, and parking sensors often not reported
- "Optional" means the package *could* include this system — not that this specific VIN *does*
- Always verify with visual inspection

**If you need build-sheet-level accuracy:** paid APIs exist (auto.dev at $299/mo for OEM build data, DataOne enterprise pricing). For most single-shop use, NHTSA + Step 3 toggle confirmation is sufficient.

---

## Questions?

For the shop: refer to OEM service information for any vehicle not covered by this tool. This is a starting point for documentation, not a replacement for OEM procedures.
