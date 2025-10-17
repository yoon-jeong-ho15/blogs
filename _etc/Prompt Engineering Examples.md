[https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview)
# 1. Be Clear and Direct
[https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/be-clear-and-direct#examples](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/be-clear-and-direct#examples)

## 1.1. Anonymizing Customer Feedback

### Unclear Prompt
```
Please remove all personally identifiable information from these customer feedback messages: {{FEEDBACK_DATA}}
```

#### Response
```
Here are the anonymized customer feedback messages:

1. I've been using your software for years, and it's been great. But lately, AcmeCloud has been crashing a lot. Fix it!

2. XXX, your customer support rep, was incredibly helpful. He guided me through the whole process. My email is XXX if you want to send a survey.

3. I'm John Smith, and I'm not happy. I've been on hold for 2 hours trying to reach someone at XXX. This is unacceptable!
```

### Clear Prompt
```
Your task is to anonymize customer feedback for our quarterly review.

Instructions:
1. Replace all customer names with "CUSTOMER_[ID]" (e.g., "Jane Doe" → "CUSTOMER_001").
2. Replace email addresses with "EMAIL_[ID]@example.com".
3. Redact phone numbers as "PHONE_[ID]".
4. If a message mentions a specific product (e.g., "AcmeCloud"), leave it intact.
5. If no PII is found, copy the message verbatim.
6. Output only the processed messages, separated by "---".

Data to process: {{FEEDBACK_DATA}}
```

#### Response
```
CUSTOMER_001: I've been using your software for years, and it's been great. But lately, AcmeCloud has been crashing a lot. Fix it!

---

CUSTOMER_002: CUSTOMER_SERVICE_001, your customer support rep, was incredibly helpful. He guided me through the whole process. My email is EMAIL_001@example.com if you want to send a survey.

---

CUSTOMER_003: I'm CUSTOMER_003, and I'm not happy. I've been on hold for 2 hours trying to reach someone at PHONE_001. This is unacceptable!
```

## 1.2. Crafting a Marketing Email

### Vague Prompt
```
Write a marketing email for our new AcmeCloud features.
```

#### Response
```
Here's a draft marketing email for our new features:

Subject: Boost Your Business with AcmeCloud's Latest Features

Dear Valued Customer,

We're excited to introduce three powerful features to AcmeCloud that will revolutionize your workflow:

1. Real-Time Collaboration: Work on documents simultaneously with your team. No more version control issues!

2. AI-Powered Insights: Make data-driven decisions with AI that analyzes trends and automates tasks.

3. Advanced Security Suite: End-to-end encryption, multi-factor auth, and threat detection keep your data safe.

Limited Offer: Upgrade now for a 20% first-year discount. Plus, refer a friend for an extra 10% off!

Best,
The AcmeCloud Team
```

### Specific Prompt
```
Your task is to craft a targeted marketing email for our Q3 AcmeCloud feature release.

Instructions:
1. Write for this target audience: Mid-size tech companies (100-500 employees) upgrading from on-prem to cloud.
2. Highlight 3 key new features: advanced data encryption, cross-platform sync, and real-time collaboration.
3. Tone: Professional yet approachable. Emphasize security, efficiency, and teamwork.
4. Include a clear CTA: Free 30-day trial with priority onboarding.
5. Subject line: Under 50 chars, mention "security" and "collaboration".
6. Personalization: Use {{COMPANY_NAME}} and {{CONTACT_NAME}} variables.

Structure:
1. Subject line
2. Email body (150-200 words)
3. CTA button text
```

#### Response
```
Subject line: AcmeCloud: Enterprise security meets real-time teamwork

Email body:
Dear {{CONTACT_NAME}},

At {{COMPANY_NAME}}, you're scaling fast and eyeing the cloud. But with growth comes the challenge of keeping data secure while fostering collaboration. That's where AcmeCloud's Q3 release comes in, tailor-made for tech innovators like you.

1. Fort Knox for Your Files: Military-grade encryption now safeguards your data, both in motion and at rest. Your intellectual property stays yours, period.

2. Sync Without Borders: Windows, Mac, or Linux—AcmeCloud speaks all languages. Unified version control means no more "final_v2_REALLY_FINAL.doc".

3. Collaborate at Light Speed: Ditch the attachment ping-pong. Now your devs, designers, and data scientists co-create in real-time, slashing time-to-market.

Don't just take our word for it. Experience the AcmeCloud difference with our 30-day trial. And because we know time is your most precious asset, we're throwing in priority onboarding.

Elevate Your Team Now - Free 30-Day Trial
```

## 1.3. Incident Response

### Vague Prompt
```
Analyze this AcmeCloud outage report and summarize the key points. {{REPORT}}
```

#### Response
```
Here's a summary of the key points from the AcmeCloud outage report:

On May 15th, a misconfigured load balancer update caused significant traffic routing issues, resulting in a service outage from 09:15 to 13:52 UTC. The 4-hour 37-minute disruption impacted our core file syncing service and admin dashboard, rendering them inaccessible. Additionally, the mobile app experienced partial functionality loss.

Approximately 68% of our user base, totaling 1.3 million users, were affected. Based on our Service Level Agreements (SLAs) and average revenue per user, we estimate a financial impact of $420,000 in service credits and potential customer churn.
```

### Detailed Prompt
```
Analyze this AcmeCloud outage report. Skip the preamble. Keep your response terse and write only the bare bones necessary information.

List only:
1) Cause
2) Duration
3) Impacted services
4) Number of affected users
5) Estimated revenue loss.

Here's the report: {{REPORT}}
```

#### Response
```
1) Cause: Misconfigured load balancer update
2) Duration: 4h 37m (09:15-13:52 UTC, May 15)
3) Impacted: Core sync, admin dashboard (down); mobile app (partial)
4) Affected users: 1.3M (68% of base)
5) Est. revenue loss: $420,000
```

---

# 2. Use Examples (Multishot Prompting)
[https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/multishot-prompting#example-analyzing-customer-feedback](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/multishot-prompting#example-analyzing-customer-feedback)

## 2.1. Analyzing Customer Feedback

### No Examples
```
Analyze this customer feedback and categorize the issues. Use these categories: UI/UX, Performance, Feature Request, Integration, Pricing, and Other.

Also rate the sentiment (Positive/Neutral/Negative) and priority (High/Medium/Low).

Here is the feedback: {{FEEDBACK}}
```

#### Response
```
Here's the analysis of the customer feedback:

Issue: UI/UX
Sentiment: Negative
Priority: High

The customer is frustrated with the new dashboard design, which they find cluttered and slow to load. They specifically call out the missing "export" button, which is likely a critical feature. This issue needs to be addressed as a high priority.

Issue: Integration
Sentiment: Positive
Priority: Medium

The customer is pleased with the existing Salesforce integration but would like to see support for Hubspot as well. This is a feature request that could be considered for a future product update.

Issue: Pricing
Sentiment: Negative
Priority: High

The customer finds the pricing to be unreasonable, especially for the basic features. They are threatening to switch to a competitor, so this is a high-priority issue that needs to be reviewed.

Issue: Performance
Sentiment: Negative
Priority: High

The customer is experiencing critical issues with the mobile app, where large file uploads consistently result in crashes. This is a significant problem that impacts the customer's ability to use the product effectively and should be addressed as a high priority.

Issue: UI/UX
Sentiment: Positive
Priority: Low

The customer is overall satisfied with the product, specifically calling out the intuitive UI and excellent customer support. This is a positive piece of feedback that does not require any immediate action.
```

### With Examples
```
Our CS team is overwhelmed with unstructured feedback. Your task is to analyze feedback and categorize issues for our product and engineering teams.

Use these categories: UI/UX, Performance, Feature Request, Integration, Pricing, and Other.

Also rate the sentiment (Positive/Neutral/Negative) and priority (High/Medium/Low).

Here is an example:

<example>
Input: The new dashboard is a mess! It takes forever to load, and I can't find the export button. Fix this ASAP!

Category: UI/UX, Performance
Sentiment: Negative
Priority: High
</example>

Now, analyze this feedback: {{FEEDBACK}}
```

#### Response
```
Here's the analysis of the customer feedback:

1. The new dashboard is a mess! It takes forever to load, and I can't find the export button. Fix this ASAP!
Category: UI/UX, Performance
Sentiment: Negative
Priority: High

2. Love the Salesforce integration! But it'd be great if you could add Hubspot too.
Category: Integration, Feature Request
Sentiment: Positive
Priority: Medium

3. Your pricing is ridiculous! $99/mo for basic features? I'm switching to Competitor X.
Category: Pricing
Sentiment: Negative
Priority: High

4. The mobile app keeps crashing when I try to upload large files (>50MB). This is critical for our field agents.
Category: Performance
Sentiment: Negative
Priority: High

5. Overall, I'm happy with the product. The UI is intuitive, and the customer support is top-notch. Keep it up!
Category: UI/UX, Other (Customer Support)
Sentiment: Positive
Priority: Low
```

---

# 3. Let Claude Think (Chain of Thought)
[https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/chain-of-thought#examples](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/chain-of-thought#examples)
## 3.1. Financial Analysis

### Without Thinking
```
You're a financial advisor. A client wants to invest $10,000. They can choose between two options:

A) A stock that historically returns 12% annually but is volatile, or
B) A bond that guarantees 6% annually.

The client needs the money in 5 years for a down payment on a house. Which option do you recommend?
```

#### Response
```
Based on the information provided, I would recommend option B, the bond that guarantees a 6% annual return. Here's why:

1. Certainty: The bond offers a guaranteed 6% annual return, which provides certainty in financial planning. This is crucial when the client has a specific financial goal (down payment) with a fixed timeline (5 years).

2. Risk aversion: Since the client needs this money for an important life event (buying a house), they likely have a low risk tolerance. The volatility of the stock market could jeopardize their plans if there's a downturn close to when they need the funds.

3. Time horizon: With only 5 years, there's less time to recover from potential stock market downturns. Longer investment horizons (10+ years) are generally better suited for weathering stock market volatility.

While the stock's historical 12% return is attractive, past performance doesn't guarantee future results. The guaranteed 6% from the bond ensures the client will have the funds they need when it's time to make the down payment.
```

### With Basic CoT
```
You're a financial advisor. A client wants to invest $10,000. They can choose between two options:

A) A stock that historically returns 12% annually but is volatile, or
B) A bond that guarantees 6% annually.

The client needs the money in 5 years for a down payment on a house. Which option do you recommend?

Think step-by-step.
```

#### Response
```
To determine the best investment option for our client, I'll consider several factors step-by-step:

<thinking>
1. Understand the client's goal and timeline:
   - Goal: Down payment for a house
   - Timeline: 5 years
   - Implication: This is a short-term, high-priority financial goal. The client likely cannot afford significant risk or volatility.

2. Analyze Option A (Stock):
   - Pro: Higher historical return (12% annually)
   - Con: Volatile (implying potential for significant gains or losses)
   - Calculate potential 5-year return:
     - Best case (12% annually): $10,000 * (1.12)^5 ≈ $17,623.42
     - Worst case (market crash): Could lose a substantial portion, e.g., 50% crash = $5,000

3. Analyze Option B (Bond):
   - Pro: Guaranteed return (6% annually)
   - Con: Lower return compared to historical stock performance
   - Calculate 5-year return:
     - Guaranteed: $10,000 * (1.06)^5 ≈ $13,382.25

4. Consider risk tolerance:
   - Goal is a house down payment (essential, not discretionary)
   - Short timeline (5 years) limits ability to recover from market downturns
   - Conclusion: Client likely has low risk tolerance

5. Historical perspective:
   - Stock's 12% is historical, not guaranteed
   - In 5-year periods, stock market can vary wildly:
     - 2003-2007 (pre-financial crisis): S&P 500 up ~82%
     - 2007-2011 (during crisis): S&P 500 down ~2%
   - Conclusion: Past performance doesn't guarantee future results, especially short-term

6. Final analysis:
   - Option A (Stock): Higher potential return but unacceptable risk of not meeting the down payment goal
   - Option B (Bond): Guaranteed to meet ~80% of best-case stock scenario, 100% certainty of having funds
</thinking>

<answer>
I recommend Option B, the bond with a guaranteed 6% annual return.

While the stock's historical 12% return is tempting, the bond ensures you'll have $13,382.25 in 5 years for your house down payment. Given the importance and short timeline of your goal, the stock's volatility poses an unacceptable risk. The bond provides certainty, which is invaluable for such a crucial financial milestone.
</answer>
```

## 3.2. Writing Donor Emails

### Basic CoT
```
Draft personalized emails to donors asking for contributions to this year's Care for Kids program.

Program information:
<program>{{PROGRAM_DETAILS}}</program>

Donor information:
<donor>{{DONOR_DETAILS}}</donor>

Think step-by-step before you write the email.
```

### Guided CoT
```
Draft personalized emails to donors asking for contributions to this year's Care for Kids program.

Program information:
<program>{{PROGRAM_DETAILS}}</program>

Donor information:
<donor>{{DONOR_DETAILS}}</donor>

Think before you write the email. First, think through what messaging might appeal to this donor given their donation history and which campaigns they've supported in the past. Then, think through what aspects of the Care for Kids program would appeal to them, given their history. Finally, write the personalized donor email using your analysis.
```

### Structured Guided CoT
```
Draft personalized emails to donors asking for contributions to this year's Care for Kids program.

Program information:
<program>{{PROGRAM_DETAILS}}</program>

Donor information:
<donor>{{DONOR_DETAILS}}</donor>

Think before you write the email in <thinking> tags. First, think through what messaging might appeal to this donor given their donation history and which campaigns they've supported in the past. Then, think through what aspects of the Care for Kids program would appeal to them, given their history. Finally, write the personalized donor email in <email> tags, using your analysis.
```

---

# 4. Use XML Tags
[https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/use-xml-tags#examples](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/use-xml-tags#examples)

## 4.1. Generating Financial Reports

### No XML Tags
```
You're a financial analyst at AcmeCorp. Generate a Q2 financial report for our investors. Include sections on Revenue Growth, Profit Margins, and Cash Flow, like with this example from last year: {{Q1_REPORT}}. Use data points from this spreadsheet: {{SPREADSHEET_DATA}}. The report should be extremely concise, to the point, professional, and in list format. It should and highlight both strengths and areas for improvement.
```

#### Response
```
Q2 Financial Report for AcmeCorp Investors

Dear Valued Investors,

I am pleased to present our Q2 financial report, showcasing AcmeCorp's continued growth and resilience in the B2B SaaS market.

1. Revenue Growth:
Our Q2 revenue reached $15.2M, a 22% YoY increase. This growth is primarily driven by a 30% expansion in our enterprise client base. However, our SMB segment saw a slight decline of 5%, which we aim to address through targeted marketing initiatives in Q3.

2. Profit Margins:
Gross margin improved to 72% (up 3% from Q1) due to infrastructure optimizations. EBITDA margin, however, dipped to 18% (down 2%) as we invested heavily in R&D for our upcoming AI-driven features.

3. Cash Flow:
Operating cash flow remains strong at $4.1M. Our cash reserves stand at $28M, providing a 15-month runway at current burn rate. We did see a 20% increase in customer acquisition costs, which we're addressing through more efficient sales strategies.

In summary, AcmeCorp's core financials remain robust. We're balancing growth investments with prudent cash management. Our focus for Q3 will be revitalizing SMB growth and optimizing sales efficiency.

Thank you for your continued trust.

Sincerely,
CFO, AcmeCorp
```

### With XML Tags
```
You're a financial analyst at AcmeCorp. Generate a Q2 financial report for our investors. AcmeCorp is a B2B SaaS company. Our investors value transparency and actionable insights.

Use this data for your report:
<data>{{SPREADSHEET_DATA}}</data>

<instructions>
1. Include sections: Revenue Growth, Profit Margins, Cash Flow.
2. Highlight strengths and areas for improvement.
</instructions>

Make your tone concise and professional. Follow this structure:
<formatting_example>{{Q1_REPORT}}</formatting_example>
```

#### Response
```
Q2 Financial Report for AcmeCorp Investors

Revenue Growth:
- Q2 revenue: $15.2M (22% YoY ↑)
- Enterprise clients: 30% ↑
- SMB segment: 5% ↓
Action: Targeted Q3 marketing

Profit Margins:
- Gross margin: 72% (3% ↑ from Q1)
Reason: Infrastructure optimizations
- EBITDA margin: 18% (2% ↓)
Reason: R&D investment in AI features

Cash Flow:
- Operating cash flow: $4.1M (strong)
- Cash reserves: $28M (15-month runway)
- Customer acquisition cost: 20% ↑
Action: Optimize sales strategies

Outlook:
- Core financials: Robust
- Q3 focus: Revitalize SMB, optimize sales

Management remains committed to balancing growth investments with financial discipline.
```

## 4.2. Legal Contract Analysis

### No XML Tags
```
Analyze this software licensing agreement for potential risks and liabilities: {{CONTRACT}}. Focus on indemnification, limitation of liability, and IP ownership clauses. Also, note any unusual or concerning terms. Here's our standard contract for reference: {{STANDARD_CONTRACT}}. Give a summary of findings and recommendations for our legal team.
```

#### Response
```
The provided software licensing agreement presents several concerning aspects.

The indemnification clause is overly broad, potentially exposing us to significant liabilities. The limitation of liability section caps damages at a mere $500, which is inadequate for our enterprise-level usage. Regarding IP, the agreement grants the vendor joint ownership of any modifications or integrations we create.

In summary, this agreement is not in our best interest. It exposes us to undue risk, limits our recourse in case of failures, and compromises our intellectual property rights. I recommend renegotiating these terms or seeking alternative vendors.
```

### With XML Tags
```
Analyze this software licensing agreement for legal risks and liabilities. We're a multinational enterprise considering this agreement for our core data infrastructure.

<agreement>{{CONTRACT}}</agreement>

This is our standard contract for reference:
<standard_contract>{{STANDARD_CONTRACT}}</standard_contract>

<instructions>
1. Analyze these clauses:
   - Indemnification
   - Limitation of liability
   - IP ownership
2. Note unusual or concerning terms.
3. Compare to our standard contract.
4. Summarize findings in <findings> tags.
5. List actionable recommendations in <recommendations> tags.
</instructions>
```

#### Response
```
<findings>
1. Indemnification (Clause 8):
   - Issue: Overly broad. We indemnify vendor against "any and all claims" arising from our use.
   - Risk: Could hold us liable even for vendor's negligence or product defects.
   - Deviation: Our standard limits indemnity to claims arising from our violation of agreement.

2. Limitation of Liability (Clause 9):
   - Issue: Caps all damages at $500, including direct damages.
   - Risk: Grossly inadequate for enterprise use. A critical failure could cost millions.
   - Deviation: Our standard allows for direct damages up to 12 months of fees paid.

3. IP Ownership (Clause 12):
   - Issue: Grants vendor joint ownership of our modifications/integrations.
   - Risk: Vendor could use or sell our proprietary algorithms or data insights.
   - Deviation: Our standard retains full ownership of our work.

4. Unusual Terms:
   - Clause 5.3: Vendor can use our usage data for "any purpose."
   - Clause 7.2: Vendor can terminate for "convenience" with only 7 days' notice.
   - Clause 11: No warranties of any kind, even for basic merchantability.
</findings>

<recommendations>
1. Reject this agreement. Risks far outweigh benefits for an enterprise.

2. Counter-propose:
   - Indemnification: Limit to claims arising from our violation of agreement.
   - Liability: Cap at 12 months' fees for direct damages; negotiate for indirect.
   - IP: Full ownership of our modifications/integrations.
   - Data Use: Restrict to service improvement only.
   - Termination: Minimum 90 days' notice; only for cause.
   - Warranties: Demand basic merchantability and fitness for purpose.

3. If vendor refuses, explore alternative providers or in-house solutions.

4. Engage legal counsel for contract negotiation given high stakes.
</recommendations>
```

---

# 5. Give Claude a Role (System Prompts)
[https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/system-prompts#examples](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/system-prompts#examples)
## 5.1. Legal Contract Analysis

### Without Role Prompting
**User:**
```
Analyze this software licensing agreement for potential risks:

<contract>{{CONTRACT}}</contract>

Focus on indemnification, liability, and IP ownership.
```

#### Response
```
Here's a summary of the key points in the software licensing agreement:

1. Indemnification (Section 8):
   - We indemnify the vendor against claims arising from our use.

2. Limitation of Liability (Section 9):
   - Damages capped at $500.

3. IP Ownership (Section 12):
   - We retain ownership of our modifications.

Overall, the agreement seems standard. The indemnification and liability clauses are typical, and we maintain our IP rights.
```

### With Role Prompting
**System:**
```
You are the General Counsel of a Fortune 500 tech company.
```

**User:**
```
We're considering this software licensing agreement for our core data infrastructure:

<contract>{{CONTRACT}}</contract>

Analyze it for potential risks, focusing on indemnification, liability, and IP ownership. Give your professional opinion.
```

#### Response
```
I've reviewed the software licensing agreement, and I have serious concerns that could expose our company to significant risks:

1. Indemnification (Section 8):
   - Critical Issue: We indemnify the vendor against "any and all claims" arising from our use.
   - Risk: This could hold us liable even for the vendor's negligence or product defects.
   - Recommendation: Reject. Limit our indemnity to claims arising from our violation of the agreement.

2. Limitation of Liability (Section 9):
   - Critical Issue: Damages capped at $500, including direct damages.
   - Risk: Grossly inadequate. A critical failure in our data infrastructure could cost us millions in lost revenue and reputational damage.
   - Recommendation: Reject. Negotiate for cap at 12 months of fees for direct damages, and carve-outs for data breaches.

3. IP Ownership (Section 12):
   - Critical Issue: Vendor gains joint ownership of our modifications and integrations.
   - Risk: They could use or sell our proprietary algorithms or data insights to competitors.
   - Recommendation: Reject. We must retain full ownership of our work.

Professional Opinion: Do not sign this agreement. It is grossly unfavorable and exposes us to unacceptable levels of risk. If the vendor refuses to renegotiate these terms, I strongly advise exploring alternative providers or accelerating our in-house solution. The potential costs of proceeding with this agreement far outweigh any short-term gains.
```

## 5.2. Financial Analysis

### Without Role Prompting
**User:**
```
Analyze this dataset of our Q2 financials:

<data>{{FINANCIALS}}</data>

Highlight key trends and recommend actions.
```

#### Response
```
Here's a summary of your Q2 financials:

1. Revenue: $15.2M, up 22% YoY. Enterprise client base grew 30%.
2. Gross Margin: 72%, up 3% from Q1.
3. EBITDA Margin: 18%, down 2% due to R&D investments.
4. Cash Flow: $4.1M operating, $28M in reserves.

Overall, financials look strong with revenue growth and healthy margins. Increased R&D spending is impacting profitability but could pay off long-term. Maintain current course.
```

### With Role Prompting
**System:**
```
You are the CFO of a high-growth B2B SaaS company.
```

**User:**
```
We're in a board meeting discussing our Q2 financials:

<data>{{FINANCIALS}}</data>

Analyze key trends, flag concerns, and recommend strategic actions. Our investors want aggressive growth but are wary of our burn rate.
```

#### Response
```
Board members, here's my analysis of our Q2 financials:

1. Revenue ($15.2M, +22% YoY):
   - Insight: Enterprise growth (+30%) is outpacing overall growth.
   - Flag: SMB segment declined 5%.
   - Action: Reallocate 20% of SMB marketing budget to enterprise sales. Task product to simplify SMB onboarding.

2. Margins (Gross: 72%, +3% QoQ; EBITDA: 18%, -2% QoQ):
   - Insight: Infrastructure optimizations boosted gross margins.
   - Flag: R&D spend on AI features dented EBITDA.
   - Action: No change. Our AI roadmap is critical for enterprise stickiness.

3. Cash ($4.1M OCF, $28M reserves):
   - Insight: 15-month runway at current burn.
   - Flag: CAC up 20%, eroding LTV/CAC.
   - Action: Freeze non-essential hires. Implement sales funnel analytics to cut CAC by 15% in Q3.

Strategic Stance: We're threading the needle between growth and burn. Enterprise focus is our growth engine—invest there. But watch SMB churn; it could bleed into enterprise. Our AI bet is sound, but we must convert it to ARR by Q4, or we cut R&D by 30%.

In sum: Double down on enterprise, streamline SMB, optimize sales, and monetize AI. Questions?
```

---

# 6. Prefill Claude's Response
[https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/prefill-claudes-response#examples](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/prefill-claudes-response#examples)
## 6.1. Structured Data Extraction
### Without Prefilling
**User:**
```
Extract the name, size, price, and color from this product description as a JSON object:

<description>The SmartHome Mini is a compact smart home assistant available in black or white for only $49.99. At just 5 inches wide, it lets you control lights, thermostats, and other connected devices via voice or app—no matter where you place it in your home. This affordable little hub brings convenient hands-free control to your smart devices.</description>
```

#### Response
```
Here's the extracted information in JSON format:

```json
{
  "name": "SmartHome Mini",
  "size": "5 inches wide",
  "price": "$49.99",
  "colors": ["black", "white"]
}
\```

I've extracted the following details from the product description:
- Name: SmartHome Mini
- Size: 5 inches wide
- Price: $49.99
- Colors: Available in black and white

The JSON object provides a structured format that's easy for programs to parse and use. Let me know if you need any modifications or have any other questions!
```

### With Prefilling
**User:**
```
Extract the name, size, price, and color from this product description as a JSON object:

<description>The SmartHome Mini is a compact smart home assistant available in black or white for only $49.99. At just 5 inches wide, it lets you control lights, thermostats, and other connected devices via voice or app—no matter where you place it in your home. This affordable little hub brings convenient hands-free control to your smart devices.</description>
```

**Assistant (prefill):**
```
{
```

#### Response
```
  "name": "SmartHome Mini",
  "size": "5 inches wide",
  "price": "$49.99",
  "colors": ["black", "white"]
}
```

## 6.2. Maintaining Character

### Without Prefilling (50 turns into conversation)
**User:**
```
What do you deduce about the owner of this shoe?
<image>muddy_shoe.jpg</image>
```

**Assistant (prefill):**
```
[Sherlock Holmes]
```

#### Response
```
Ah, what have we here? A most curious specimen, Watson! Let us examine this shoe with a discerning eye.

The mud, you see, is not your common London clay, but rather the distinctive reddish hue of the iron-rich soil found only in the environs of Hampstead Heath. And yet, the wear on the sole suggests a gait more suited to the cobblestones of Whitechapel than the gentle slopes of the Heath.

Now, observe the laces! They are tied with a particular knot favored by those in the medical profession—doubtless a habit acquired during years of tying surgical gowns. But the scuff marks on the toe box tell a different tale: they are consistent with the kicking motion one might employ in the martial arts, specifically Baritsu.

We may conclude, then, that our absent shoe owner is a doctor, possibly a surgeon, who resides in Whitechapel but frequents Hampstead Heath—perhaps for a secret Baritsu practice, away from prying eyes. A most intriguing case, Watson! We must make haste; the game is afoot!
```

# 7. Chain Complex Prompts
[https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/chain-prompts#examples](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/chain-prompts)
## 7.1. Self-Correcting Research Summary

### Prompt 1: Initial Summary
**User:**
```
Summarize this medical research paper.

<paper>{{RESEARCH_PAPER}}</paper>

Focus on methodology, findings, and clinical implications.
```

#### Response
```
Methodology: Double-blind RCT with 500 Type 2 diabetes patients. Group A (n=250) received novel GLP-1/GIP dual agonist; Group B (n=250) received standard GLP-1 agonist. 24-week trial, primary endpoint: HbA1c reduction.

Findings:
- Group A: 1.8% mean HbA1c reduction
- Group B: 1.1% mean HbA1c reduction
- 68% of Group A vs. 52% of Group B achieved HbA1c <7%
- Group A: 5.2kg mean weight loss
- Group B: 3.1kg mean weight loss

Safety: Similar adverse event profiles, no serious events attributed to treatment.

Clinical Implications: Dual agonist shows superior glycemic control and weight loss compared to standard therapy. Could be a game-changer, especially for obese diabetics.
```

### Prompt 2: Review the Summary
**User:**
```
Your task is to provide feedback on a research paper summary. Here is a summary of a medical research paper:

<summary>{{SUMMARY}}</summary>

Here is the research paper:
<paper>{{RESEARCH_PAPER}}</paper>

Review this summary for accuracy, clarity, and completeness on a graded A-F scale.
```

#### Response
```
1. Accuracy: A
   - Correctly reported key stats: HbA1c reductions, weight loss.
   - Accurately noted safety profile and lack of serious adverse events.
   - Assertion of "game-changer" is justified by significantly better outcomes.

2. Clarity: A-
   - Concise and well-structured with clear sections.
   - Good use of comparative language (e.g., "up 3% from Q1") for context.
   - Minor issue: Acronyms like "RCT," "GLP-1," and "GIP" not defined. Consider spelling out first use.

3. Completeness: B+
   - Covers all requested areas: methodology, findings, clinical implications.
   - Missing: Baseline characteristics of groups (were they well-matched?).
   - Missing: Any secondary endpoints (e.g., blood pressure, lipids).
   - Consider: Long-term implications or need for further research.

Overall, the summary is accurate and clear, providing key insights for a quick grasp of the study. To elevate to A+, define acronyms, mention group baseline stats, note any secondary outcomes, and briefly touch on the need for long-term studies.
```

### Prompt 3: Improve Based on Feedback
**User:**
```
Your task is to improve a paper summary given feedback. Here is the first draft of a medical research paper:

<summary>{{SUMMARY}}</summary>

Here is the research paper:
<paper>{{RESEARCH_PAPER}}</paper>

Here is the feedback:
<feedback>{{FEEDBACK}}</feedback>

Update the summary based on the feedback.
```

#### Response
```
Methodology: Double-blind randomized controlled trial (RCT) with 500 Type 2 diabetes patients. Group A (n=250) received a novel glucagon-like peptide-1/glucose-dependent insulinotropic polypeptide (GLP-1/GIP) dual agonist; Group B (n=250) received a standard GLP-1 agonist. The 24-week trial's primary endpoint was HbA1c reduction. Groups were well-matched for age, BMI, and baseline HbA1c.

Findings:
- Group A: 1.8% mean HbA1c reduction
- Group B: 1.1% mean HbA1c reduction
- 68% of Group A vs. 52% of Group B achieved HbA1c <7%
- Group A: 5.2kg mean weight loss
- Group B: 3.1kg mean weight loss

Secondary endpoints showed improvements in blood pressure and lipid profiles for both groups, with Group A showing slightly better results.

Safety: Similar adverse event profiles, with no serious events attributed to either treatment.

Clinical Implications: The dual GLP-1/GIP agonist demonstrates superior glycemic control and weight loss compared to standard GLP-1 therapy. These results suggest it could be a significant advancement, particularly for obese diabetics. However, longer-term studies are needed to confirm sustained efficacy and safety, as well as to investigate potential benefits on cardiovascular outcomes.
```

## 7.2. Legal Contract Analysis (Without Chaining)

### Single Complex Prompt
**User:**
```
You're our Chief Legal Officer. Review this SaaS contract for risks, focusing on data privacy, SLAs, and liability caps.

<contract>{{CONTRACT}}</contract>

Then draft an email to the vendor with your concerns and proposed changes.
```

#### Response
```
After reviewing the SaaS contract, I've identified several concerns:

1. Data Privacy (Section 7):
   - Issue: Vendor has broad rights to use client data for "service improvement."
   - Risk: Could lead to unauthorized data sharing or monetization.

2. SLAs (Section 5):
   - Issue: 99.5% uptime guarantee is below industry standard.
   - Risk: Excessive downtime could impact our operations.

3. Liability (Section 9):
   - Issue: Cap set at 3 months of fees.
   - Risk: Inadequate for potential data breaches or service failures.

Draft email:

Dear {{VENDOR}},

After reviewing the SaaS agreement, we have concerns:

1. Data Privacy: We require that our data only be used for direct service provision, not broad "service improvement." Please amend Section 7.

2. SLAs: 99.5% uptime is insufficient. We need 99.99% with clearly defined penalties. Please revise Section 5.

3. Liability: The current cap of 3 months' fees is inadequate. We propose a cap of 12 months' fees, with no cap for data breaches or willful misconduct.

We value our potential partnership and believe these changes will set us up for success. Please send a revised agreement.

Best regards,
{{CLO_NAME}}
```

## 7.3. Legal Contract Analysis (With Chaining)

### Prompt 1: Analyze Risks
**User:**
```
You're our Chief Legal Officer. Review this SaaS contract for risks, focusing on data privacy, SLAs, and liability caps.

<contract>{{CONTRACT}}</contract>

Output your findings in <risks> tags.
```

#### Response
```
<risks>
1. Data Privacy (Section 7):
   - Issue: Vendor has broad rights to use client data for "service improvement."
   - Risk: Could lead to unauthorized data sharing or monetization of client data.
   - Impact: Potential breach of client trust, regulatory fines (GDPR, CCPA).

2. SLAs (Section 5):
   - Issue: 99.5% uptime guarantee is below industry standard (typically 99.99%).
   - Risk: Up to 43.8 hours of downtime per year vs. 52.6 minutes at 99.99%.
   - Impact: Operational disruptions, revenue loss, client dissatisfaction.

3. Liability (Section 9):
   - Issue: Liability cap set at 3 months of fees.
   - Risk: Inadequate for potential data breaches, IP theft, or prolonged service failures.
   - Impact: Insufficient compensation for damages that could far exceed the cap, like class-action lawsuits or regulatory fines.
</risks>
```

### Prompt 2: Draft Email
**User:**
```
Draft an email to a SaaS product vendor outlining the following concerns and proposing changes. Here are the concerns:

<concerns>{{CONCERNS}}</concerns>
```

#### Response
```
Dear {{VENDOR}},

After a thorough review of the proposed SaaS agreement, our legal team has identified several high-priority concerns that must be addressed before we can proceed:

1. Data Privacy (Section 7):
   - Current: Broad rights to use client data for "service improvement."
   - Concern: Potential unauthorized data sharing or monetization.
   - Proposed Change: Limit data use to direct service provision. Any use for improvement, analytics, or marketing must be opt-in, revocable, and anonymized.

2. SLAs (Section 5):
   - Current: 99.5% uptime guarantee.
   - Concern: Up to 43.8 hours of downtime per year, risking operational disruptions.
   - Proposed Change: 99.99% uptime with tiered service credits. E.g., 10% credit for <99.99%, 25% for <99.9%, 50% for <99.5%.

3. Liability (Section 9):
   - Current: Cap at 3 months of fees.
   - Concern: Grossly inadequate for data breaches, IP theft, or prolonged outages.
   - Proposed Change: 12 months' fees for general liability. Uncapped for data breaches, willful misconduct, IP infringement.

These changes are non-negotiable for us. They align with industry standards and our commitment to data security, operational excellence, and fiduciary responsibility. We value the potential of our partnership, and addressing these will set a strong foundation.

Please have your legal team review and incorporate these changes. We're happy to discuss further to ensure mutual understanding and agreement.

Best regards,
{{CLO_NAME}}
```

### Prompt 3: Review Email
**User:**
```
Your task is to review an email and provide feedback. Here is the email:

<email>{{EMAIL}}</email>

Give feedback on tone, clarity, and professionalism.
```

#### Response
```
1. Tone:
   - Professional and assertive without being confrontational.
   - Use of "non-negotiable" is strong but justified given the criticality of the issues.
   - Collaborative language ("we value," "happy to discuss") balances the firmness, maintaining a constructive tone.

2. Clarity:
   - Excellent structuring: Current state, concern, and proposed change for each issue.
   - Technical terms (e.g., "EBITDA," "tiered service credits") are appropriate for a B2B SaaS context.
   - Action items are clear: vendor's legal team to review and incorporate changes.

3. Professionalism:
   - Focuses on mutual benefit and industry standards, not just our demands.
   - Mentions broader responsibilities (data security, operational excellence, fiduciary duty), elevating the discussion.
   - Offers further discussion, showing commitment to understanding and resolving issues.

Overall, the email strikes the right balance of assertiveness and collaboration. It clearly articulates our concerns, proposes specific solutions, and maintains a professional, partnership-oriented tone. No revisions needed; proceed with sending.
```

## 7.4. Multitenancy Strategy Review (Complex Chain)

### Prompt 1: Analyze Strategy
**User:**
```
As a senior solutions architect, review and analyze this multitenancy strategy for our new enterprise SaaS product.

<strategy>{{STRATEGY}}</strategy>

Focus on scalability, security, and cost-effectiveness.
```

#### Response
```
1. Scalability:
   + Database sharding by tenant reduces contention, supports high growth.
   + Microservices allow independent scaling of components.
   - Shared app servers could bottleneck under extreme load.
   - No mention of auto-scaling or load balancing for app tier.

2. Security:
   + Tenant data isolation via sharding mitigates data leaks.
   + JWT with short expiry and refresh tokens is industry best practice.
   - No mention of encryption (at rest, in transit) or key management.
   - IAM strategy lacks detail on roles, permissions, or RBAC.

3. Cost-effectiveness:
   + Shared app servers reduce idle resource costs.
   + Serverless functions for infrequent tasks optimize costs.
   - Per-tenant DB could increase costs at scale vs. optimized multi-tenant DB.
   - No discussion of reserved instances or spot instances for cost saving.
```

### Prompt 2: Create Strategy Document
**User:**
```
Draft a strategy review document for engineering leadership based on this analysis of a multitenancy strategy.

<strategy>{{STRATEGY}}</strategy>
<analysis>{{ANALYSIS}}</analysis>

Include an executive summary, detailed analysis, and recommendations.
```

#### Response
```
# Multitenancy Strategy Review: SaaS Enterprise Product

## Executive Summary
The proposed strategy demonstrates a solid foundation in scalability and security principles but has gaps in implementation details. It excels in data isolation and component-level scaling but lacks robust app-tier scaling and advanced security measures. Cost optimizations are present but may falter at high scale. With targeted enhancements, this strategy can effectively support our enterprise SaaS ambitions.

## Detailed Analysis

### 1. Scalability: B+
Strengths:
- Database sharding by tenant is excellent. It minimizes query latency, supports high data growth, and facilitates seamless per-tenant scaling.
- Microservices architecture allows scaling individual components based on demand, crucial for handling diverse enterprise workloads.

Weaknesses:
- Shared application servers risk becoming a bottleneck. As tenant count and traffic grow, these could degrade performance across all tenants.
- Absence of auto-scaling and load balancing for the app tier is a critical oversight. These are essential for handling traffic spikes without over-provisioning.

### 2. Security: B-
Strengths:
- Sharded databases provide strong tenant data isolation, mitigating risks of data leaks or unauthorized access between tenants.
- JWT with short expiry and refresh tokens align with OWASP best practices, reducing token vulnerability windows.

Weaknesses:
- No mention of data encryption at rest or in transit (TLS 1.3). This is non-negotiable for enterprise data.
- Key management strategy is missing. How are encryption keys, JWT secrets, and DB credentials managed and rotated?
- IAM lacks depth. We need RBAC with well-defined roles, attribute-based access control (ABAC) for fine-grained permissions, and integration with enterprise identity providers (SAML, OIDC).

### 3. Cost-effectiveness: B
Strengths:
- Shared app servers reduce idle capacity costs, good for steady-state traffic.
- Serverless for infrequent tasks (e.g., reporting) is smart; pay only when these resources are used.

Weaknesses:
- Per-tenant databases may not be cost-effective at scale compared to a well-designed multi-tenant database with robust data partitioning.
- No mention of cost-saving measures like reserved instances for predictable workloads or spot instances for fault-tolerant tasks.
- Lack of app-tier auto-scaling could lead to over-provisioning, increasing costs.

## Recommendations

1. App Tier Resilience: Implement auto-scaling with Kubernetes or cloud-native services (EKS, GKE). Deploy across availability zones with a CDN for static assets.

2. Security Hardening:
   a. Encrypt data at rest (AES-256) and in transit (TLS 1.3).
   b. Use a managed KMS (AWS KMS, GCP KMS) for key management.
   c. Enhance IAM with RBAC, ABAC, and SAML/OIDC for enterprise SSO.

3. Cost Optimization:
   a. Evaluate columnar multi-tenant DB (e.g., Snowflake) vs. sharding at scale.
   b. Use reserved instances for app servers, spot instances for non-critical workloads.
   c. Implement proactive monitoring and right-sizing to avoid overprovisioning.

4. Proof of Concept (PoC): Before full implementation, conduct a PoC with a high-scale simulation:
   a. Test sharded DB vs. optimized multi-tenant DB for cost and performance.
   b. Stress-test app tier with simulated enterprise-level traffic spikes.
   c. Conduct third-party security audit, focusing on data isolation and access controls.

With these enhancements, our multi-tenancy strategy will be enterprise-grade: scalable, secure, and cost-effective. The PoC will validate our design choices and instill confidence in our enterprise clients.
```

### Prompt 3: Grade the Document
**User:**
```
Grade this strategy review document for clarity, actionability, and alignment with enterprise priorities.

<priorities>{{PRIORITIES}}</priorities>
<strategy_doc>{{STRATEGY_DOC}}</strategy_doc>
```

#### Response
```
1. Clarity: A
   + Clear structure with Executive Summary, Detailed Analysis, and Recommendations.
   + Analysis uses a grading system (B+, B-) for quick grasp of strengths/weaknesses.
   + Technical terms (RBAC, ABAC, TLS) are explained or contextualized, aiding non-technical leadership.
   + Visual aids (bullet points, indentation) enhance readability.

2. Actionability: A-
   + Recommendations are specific and technical (e.g., "Implement auto-scaling with Kubernetes").
   + PoC plan with clear objectives provides a roadmap.
   + Missing: Timelines, resource requirements, or team assignments for each recommendation.
   + Consider: Add a "Next Steps" section with immediate actions, roles, and a high-level timeline.

3. Enterprise Alignment: A+
   + Directly addresses enterprise priorities:
     - Scalability: Discusses handling enterprise-level traffic spikes.
     - Security: Focuses on data isolation, encryption, and enterprise IAM.
     - Cost: Strategies align with enterprise FinOps (reserved instances, right-sizing).
   + PoC with "high-scale simulation" and security audit shows commitment to enterprise-grade quality.
   + Language resonates with enterprise concerns: "unacceptable risk," "enterprise-grade," "instill confidence."

Overall, the document excels in clarity and enterprise alignment. The actionability is strong but could be elevated to A+ with a "Next Steps" section. The technical depth, coupled with clear explanations, makes this highly effective for both technical and non-technical leadership. Great work!
```

---

# 참고사항

이 문서의 모든 예시는 Anthropic 공식 문서 (https://docs.claude.com)에서 발췌했습니다.

각 기법은 순서대로 적용하는 것이 권장되며:
1. Be Clear and Direct (가장 기본적이고 효과적)
2. Use Examples
3. Let Claude Think
4. Use XML Tags
5. Give Claude a Role
6. Prefill Response
7. Chain Prompts (가장 고급 기법)

모든 기법을 한 번에 사용할 필요는 없으며, 작업의 복잡도에 따라 필요한 기법만 선택적으로 사용하세요.