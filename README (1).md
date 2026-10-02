# Facts-Only MF Assistant

A small RAG-based FAQ assistant that answers **factual** questions about mutual fund schemes using only official public pages (AMC / SEBI / AMFI). Every answer has one source link. It never gives investment advice.

- **Product context:** Groww (assistant designed to sit on a scheme page; Groww is *not* used as a source)
- **AMC:** HDFC Mutual Fund
- **Schemes:** HDFC Flexi Cap, HDFC ELSS Tax Saver, HDFC Large Cap, HDFC Mid Cap

## Disclaimer (used in the UI)

> **Facts-only. No investment advice.** Answers come from official AMC, SEBI and AMFI documents and may be out of date. Please check the linked source. Don't share PAN, Aadhaar, account numbers, OTPs, emails or phone numbers.

## How it works

1. **Corpus:** official pages and PDFs listed in `sources.csv`, fetched and chunked.
2. **Retrieval:** top-3 chunks per question; the answer cites only the single best chunk's URL.
3. **Answer rules:** at most 3 sentences, one link, and a `Last updated from sources: <date>` line. If no chunk supports the answer, say so and link the scheme's official factsheet.
4. **Refusals:** buy/sell, "which is better", portfolio and return-comparison questions get a polite facts-only reply plus an AMFI/SEBI investor-education link.
5. **PII guard:** a regex filter blocks PAN, Aadhaar, phone, email and OTP patterns before the model is called. Nothing is stored.
6. **UI:** welcome line, 3 example questions, and the disclaimer above.

## Setup

```bash
git clone <your-repo-url>
cd <repo>
pip install -r requirements.txt   # added with the prototype code
python ingest.py                  # fetch + chunk the URLs in sources.csv
python app.py                     # start the chat UI
```

*(Prototype code is the next milestone step; this repo currently holds the scope, sources, Q&A and disclaimer.)*

## Source list

Full list in [`sources.csv`](sources.csv). Verified so far (read on 2026-10-02):

| # | Scheme | Document | URL |
|---|--------|----------|-----|
| 1 | HDFC ELSS Tax Saver | Scheme page (Direct) | https://www.hdfcfund.com/product-solutions/overview/hdfc-elss-tax-saver/direct |
| 2 | HDFC ELSS Tax Saver | Scheme page (Regular) | https://www.hdfcfund.com/product-solutions/overview/hdfc-elss-tax-saver/regular |
| 3 | HDFC ELSS Tax Saver | Scheme page (alt. path) | https://hdfcfund.com/explore/mutual-funds/hdfc-elss-tax-saver/direct |
| 4 | HDFC ELSS Tax Saver | "Weekend Bytes" note (Mar 2025) | https://files.hdfcfund.com/s3fs-public/2025-03/ELSS%20Tax%20saver%20-%20Weekend%20Bytes%20-%20Mar%2025.pdf |

**Still to add (15–25 total):** the Flexi Cap, Large Cap and Mid Cap scheme pages; SID, KIM and factsheet PDFs for each scheme; the HDFC MF charges/exit-load page; AMFI and SEBI investor-education pages; and the HDFC MF capital-gains statement guide. Only URLs opened and checked on official domains should be added.

## Sample Q&A

Answers below use only text read on the cited official page.

**Q1. What is the lock-in period of HDFC ELSS Tax Saver?**
It has a statutory lock-in of 3 years. Source: https://www.hdfcfund.com/product-solutions/overview/hdfc-elss-tax-saver/direct
*Last updated from sources: 2026-10-02*

**Q2. What tax deduction does HDFC ELSS Tax Saver offer?**
Individuals/HUFs can claim a deduction from gross total income of up to ₹1.5 lakh under section 80C of the Income Tax Act, 1961. Source: https://www.hdfcfund.com/product-solutions/overview/hdfc-elss-tax-saver/direct
*Last updated from sources: 2026-10-02*

**Q3. What is the riskometer level for HDFC ELSS Tax Saver?**
The scheme page states the risk of the scheme is "very high". Source: https://www.hdfcfund.com/product-solutions/overview/hdfc-elss-tax-saver/direct
*Last updated from sources: 2026-10-02*

**Q4. Where can I find the SID and KIM of HDFC ELSS Tax Saver?**
The scheme page links the SID and KIM, both dated 21 November 2025. Source: https://www.hdfcfund.com/product-solutions/overview/hdfc-elss-tax-saver/direct
*Last updated from sources: 2026-10-02*

**Q5. How much equity does HDFC ELSS Tax Saver invest in?**
The scheme page says it invests 80–100% of its portfolio in equity or equity-related instruments. Source: https://www.hdfcfund.com/product-solutions/overview/hdfc-elss-tax-saver/direct
*Last updated from sources: 2026-10-02*

**Q6. Should I invest in HDFC Flexi Cap or HDFC ELSS Tax Saver? (refusal)**
I can only share facts, not advice on which scheme to choose. For help with how to evaluate schemes, see AMFI's investor education pages: https://www.amfiindia.com/investor-corner *(verify this URL before publishing)*
*Last updated from sources: 2026-10-02*

**Q7. Which fund gave better returns last year? (refusal)**
I don't compare or compute returns. Please see each scheme's official factsheet on https://www.hdfcfund.com for performance data.
*Last updated from sources: 2026-10-02*

**Pending (add after fetching official pages):** expense ratio, exit load, minimum SIP and benchmark for Flexi Cap, Large Cap and Mid Cap, and "how to download a capital-gains statement".

## Known limits

- Expense ratio, exit load and minimum SIP change over time; they are answered only from the latest official documents, with the retrieval date shown.
- Some AMC pages load figures dynamically, so those answers may link the factsheet instead of quoting a number.
- Only 4 schemes of one AMC are covered.
- No advice, no return comparisons, no personal or portfolio questions.
