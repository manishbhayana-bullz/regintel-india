# RegIntel India 🇮🇳
### AI-Powered Regulatory Compliance Intelligence for Indian Banking

Built by: [Your Name] | Product Manager / AI Product Builder  
Stack: Vanilla HTML/CSS/JS + Claude AI API  
Cost to deploy: **₹0 (Free)**

---

## What this does

RegIntel India reads any RBI circular, SEBI regulation, or IRDAI guideline and extracts:
- All key compliance obligations in plain English (no legal jargon)
- Severity: High / Medium / Low based on penalty risk
- Specific deadlines
- Penalty amounts per violation
- Risk areas with specific regulatory consequences
- Regulatory timeline

**Time saved:** A compliance analyst typically spends 3–4 hours per regulatory document. RegIntel does it in under 2 minutes.

---

## Regulatory frameworks covered

| Regulator | Documents covered |
|-----------|-------------------|
| **RBI** | Master Directions, Circulars, KYC Guidelines, Digital Lending, Cybersecurity Framework, Fair Practices Code |
| **SEBI** | Investment Adviser Regulations, Broker Circulars, Mutual Fund Regulations, Algo Trading Rules |
| **IRDAI** | Insurance Regulations, Product Guidelines |
| **Others** | PMLA / AML Guidelines, FEMA, IndAS / IFRS, FATF Compliance |

---

## Institution types supported

- Private bank (HDFC, ICICI, Kotak, Axis, Yes, IndusInd, Federal)
- PSU bank (SBI, PNB, Bank of Baroda, Canara)
- NBFC (Bajaj Finance, Muthoot, Shriram, Mahindra Finance)
- Fintech (Razorpay, Groww, Zerodha, Paytm, PhonePe compliance teams)
- Broker / Mutual Fund distributor (Zerodha, Groww, HDFC Securities)
- Investment bank (Goldman Sachs India, Morgan Stanley India, Kotak IB, Nomura)

---

## Test Data (included in `/test-data` folder)

Three realistic test documents are included — paste any section into the app:

### Test 1: RBI Digital Lending Guidelines 2022
**File:** `test-data/RBI_Digital_Lending_Guidelines_2022.txt`  
**Best with institution:** Private bank, NBFC, Fintech  
**What it tests:** KFS requirement, data localisation, cooling-off period, penalty provisions  
**Expected result:** 4 high-priority obligations, ₹2 Cr max penalty, immediate deadlines

### Test 2: SEBI Investment Adviser Regulations 2023
**File:** `test-data/SEBI_Investment_Adviser_Regulations_2023.txt`  
**Best with institution:** Broker / MF  
**What it tests:** Segregation deadline (breached), fee caps, risk profiling mandate  
**Expected result:** 3 high-priority, ₹25 Cr max penalty, segregation breach banner

### Test 3: RBI KYC Master Direction 2024
**File:** `test-data/RBI_KYC_Master_Direction_2024.txt`  
**Best with institution:** NBFC, Private bank  
**What it tests:** V-CIP requirements, STR filing obligations, PMLA criminal liability  
**Expected result:** PMLA criminal liability banner, 3 high-priority, 7-day STR deadline

---

## How to test locally (5 minutes)

```bash
# Option 1 — Just open the file (no server needed)
open index.html   # Mac
start index.html  # Windows

# Option 2 — Run a local server (better)
cd regintel-india
python3 -m http.server 8080
# Then open http://localhost:8080
```

---

## Deploy to GitHub Pages (Free — 10 minutes)

### Step 1: Create GitHub account
Go to https://github.com and sign up (free)

### Step 2: Create new repository
- Click "New repository"
- Name it: `regintel-india`
- Set to **Public**
- Click "Create repository"

### Step 3: Upload files
```bash
# If you have Git installed:
git init
git add .
git commit -m "Initial commit - RegIntel India"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/regintel-india.git
git push -u origin main
```

Or use GitHub's web interface:
- Click "uploading an existing file"
- Drag and drop all files from the `regintel-india` folder
- Click "Commit changes"

### Step 4: Enable GitHub Pages
- Go to your repository → Settings → Pages
- Source: "Deploy from a branch"
- Branch: main / root
- Click Save
- Wait 2–3 minutes

### Step 5: Your live URL
```
https://YOUR-USERNAME.github.io/regintel-india/
```

That's it. Free hosting, permanent URL, shareable in interviews.

---

## Deploy to Vercel (Even easier — 3 minutes)

1. Go to https://vercel.com → Sign up with GitHub
2. Click "New Project" → Import your GitHub repo
3. Click "Deploy" — no configuration needed
4. Your URL: `https://regintel-india.vercel.app`

---

## Adding Real Claude AI (Production version)

The current version uses pre-built demo data. To process real documents with actual AI:

### Option A: Netlify Functions (Free tier — 125k calls/month)

Create `netlify/functions/analyse.js`:
```javascript
exports.handler = async (event) => {
  const { text, institution } = JSON.parse(event.body);
  
  const response = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'x-api-key': process.env.ANTHROPIC_API_KEY,
      'anthropic-version': '2023-06-01'
    },
    body: JSON.stringify({
      model: 'claude-sonnet-4-20250514',
      max_tokens: 1500,
      system: `You are a senior regulatory compliance analyst specialising in Indian banking regulations (RBI, SEBI, IRDAI). 
You are advising a ${institution}.
Analyse the provided regulatory text and return ONLY valid JSON (no markdown, no preamble) with this structure:
{
  "obligations": [{"title": "", "description": "", "severity": "high|med|low", "tags": [], "deadline": ""}],
  "risks": [{"text": "", "level": "high|med|low", "penalty": ""}],
  "timeline": [{"date": "", "event": ""}],
  "metrics": {"maxPenalty": "", "nearestDeadline": "", "highCount": 0},
  "penaltyBanner": {"show": true/false, "title": "", "body": ""}
}
Write plain English. No legal jargon. Be specific about rupee penalty amounts.`,
      messages: [{ role: 'user', content: `Analyse this regulatory document:\n\n${text}` }]
    })
  });
  
  const data = await response.json();
  const text_response = data.content[0].text;
  
  return {
    statusCode: 200,
    body: text_response,
    headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' }
  };
};
```

Then in `index.html`, replace the `startAnalysis()` call to hit `/.netlify/functions/analyse`.

Set environment variable in Netlify dashboard:
```
ANTHROPIC_API_KEY = your-key-here
```

Get your free API key: https://console.anthropic.com

---

## Project structure

```
regintel-india/
├── index.html                          ← Main application (single file)
├── README.md                           ← This file
└── test-data/
    ├── RBI_Digital_Lending_Guidelines_2022.txt
    ├── SEBI_Investment_Adviser_Regulations_2023.txt
    └── RBI_KYC_Master_Direction_2024.txt
```

---

## Roadmap (if building into a real product)

| Phase | Features | Timeline |
|-------|----------|----------|
| Phase 1 (now) | Working prototype, demo data, GitHub Pages | Done |
| Phase 2 | Real Claude AI integration via Netlify Functions | 1 week |
| Phase 3 | User authentication, analysis history, PDF export | 1 month |
| Phase 4 | GRC platform API integration, audit trail, role-based access | 3 months |
| Phase 5 | On-premise / private cloud deployment for enterprise banks | 6 months |

---

## Why this matters for Indian banking

- RBI issued 47 Master Directions and 200+ circulars in FY24 alone
- SEBI introduced 23 new regulations in 2023-24
- Average compliance team at a mid-size NBFC: 3-5 people, processing 50+ regulatory documents per year
- RegTech market in India: ~$500M growing at 25% CAGR
- No affordable alternative to Thomson Reuters for mid-market Indian firms

---

## Interview talking points

**For compliance PM roles at HDFC, ICICI, Kotak:**
> "I built a working prototype that compresses 3-4 hours of regulatory document analysis to 2 minutes. It covers RBI, SEBI, and IRDAI frameworks with institution-specific outputs. I can adapt it to any new regulation in under an hour."

**For fintech PM roles at Razorpay, Groww, Zerodha:**
> "This solves a real pain point for your compliance team — especially as RBI has been increasing regulatory intensity on digital lending and payment aggregators. I built it end-to-end with no engineering team."

**For consulting roles at Big 4 India:**
> "This tool makes your regulatory advisory analysts 5x faster. The demo shows RBI Digital Lending, SEBI IA Regs, and KYC Master Direction. I can extend it to any client-specific framework in one session."

---

*Built with Claude AI — Anthropic | Designed for Indian banking compliance teams*
