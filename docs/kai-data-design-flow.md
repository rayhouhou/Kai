---
layout: default
title: Kai - Data Design and Flow
description: Detailed overview of Kai's data structure and flow for KYB processes
nav_order: 3
permalink: /data-design-flow
last_modified_date: 2024-10-12
---

# Kai: Data Design and Flow

This document outlines the data design and flow within Kai, our KYC AI Investigator, specifically for Know Your Business (KYB) processes. It covers data categories, generation guidelines, and how data moves through the system to support agentic KYB workflows.

## 1. Data Categories

### 1.1 Product Data
Products are categorized by type and associated risk level. For example:
```json
{
  "Lending": [
    {"name": "Overdraft", "risk": "Medium"},
    {"name": "Trade Finance", "risk": "High"}
  ],
  "Account Services": [
    {"name": "Current Account", "risk": "Low"},
    {"name": "Savings Account", "risk": "Low"}
  ],
  "FX": [
    {"name": "Currency Swaps", "risk": "Medium"},
    {"name": "Currency Options", "risk": "High"},
    {"name": "Currency Exchange", "risk": "Low"}
  ]
}
```

### 1.2 Geographical Data
Countries are associated with regulatory regimes and risk levels:
```json
[
  {"name": "United Kingdom", "regime": "UK", "risk": "Low"},
  {"name": "France", "regime": "EU", "risk": "Low"},
  {"name": "Croatia", "regime": "EU", "risk": "High"},
  {"name": "Italy", "regime": "EU", "risk": "Medium"}
]
```

### 1.3 Business Categories
Industries and their associated risk levels:
```json
{
  "Hospitality and Retail": [
    {"name": "Restaurant", "risk": "Low"},
    {"name": "Luxury Goods Retailers", "risk": "High"}
  ],
  "Financial Institutions": [
    {"name": "Electronic Money Institution", "risk": "Low"},
    {"name": "Money Remitters", "risk": "High"}
  ],
  "Gaming and Gambling": [
    {"name": "Video Gaming Companies", "risk": "Low"},
    {"name": "Online Casinos (Unregulated)", "risk": "High"}
  ]
}
```

### 1.4 Corporate Structures
Allowed corporate structures by country:
```json
[
  {"country": "United Kingdom", "structures": ["Limited Company", "Public Limited Company"]},
  {"country": "France", "structures": ["Société à responsabilité limitée (SARL)", "Société anonyme (SA)"]},
  {"country": "Croatia", "structures": ["Društvo s ograničenom odgovornošću (d.o.o.)", "Dioničko društvo (d.d.)"]},
  {"country": "Italy", "structures": ["Società a responsabilità limitata (S.r.l.)", "Società per Azioni (S.p.A.)"]}
]
```

## 2. Data Generation Guidelines

Client profiles are generated using the following process:

1. Randomly select a country from the geographical data.
2. Based on the country, randomly select a corporate structure.
3. Randomly select an industry and business category.
4. Assign 1-3 products from different categories.

Example client profile:
```json
{
  "clientId": "BUS001",
  "companyName": "TechRetail Solutions",
  "country": "France",
  "corporateStructure": "Société à responsabilité limitée (SARL)",
  "industry": "Hospitality and Retail",
  "businessCategory": "Luxury Goods Retailers",
  "products": ["Current Account", "Currency Exchange", "Trade Finance"]
}
```

### 2.1 Risk Scenarios

Various risk scenarios are created by combining different elements:

1. **Low Risk**: Companies in low-risk countries, low-risk business categories, with low-risk products.
2. **Medium Risk**: Mix of medium-risk factors or combination of low and high-risk factors.
3. **High Risk**: Companies in high-risk countries, high-risk business categories, or with multiple high-risk products.

Example high-risk scenario:
```json
{
  "clientId": "BUS002",
  "companyName": "Adriatic Gaming Ltd",
  "country": "Croatia",
  "corporateStructure": "Društvo s ograničenom odgovornošću (d.o.o.)",
  "industry": "Gaming and Gambling",
  "businessCategory": "Online Casinos (Unregulated)",
  "products": ["Current Account", "Currency Options"]
}
```

### 2.2 Edge Cases

Edge cases are included to test the AI agent's decision-making capabilities:

1. Companies with mixed risk indicators (e.g., low-risk country but high-risk business).
2. Businesses requesting products that may not align with their typical operations.
3. Companies close to the "No Business" policy.

Example edge case:
```json
{
  "clientId": "BUS003",
  "companyName": "EuroTech Payments",
  "country": "France",
  "corporateStructure": "Société anonyme (SA)",
  "industry": "Financial Institutions",
  "businessCategory": "Investment Firms",
  "products": ["Current Account", "Trade Finance"],
  "additionalInfoNarrative": "Provides payment processing for businesses including some cryptocurrency exchanges"
}
```

## 3. Data Flow in KYB Process

1. **Client Application**: 
   - Generated client profile enters the system as a new application.

2. **Initial Risk Assessment**:
   - System calculates initial risk based on country, industry, and products.
   - Risk score = (Client Risk * 0.4) + (Geographical Risk * 0.3) + (Highest Product Risk * 0.3)
   - Risk levels: Low (< 1.5), Medium (1.5 - 2.4), High (≥ 2.5)

3. **Due Diligence Questionnaire**:
   - System generates tailored questions based on risk level and client profile.
   - Example question: 
     ```json
     {
       "questionId": "DD001",
       "question": "Please provide details of all beneficial owners with 25% or more ownership.",
       "answerType": "text",
       "riskRelevance": "high"
     }
     ```

4. **Document Collection**:
   - System requests standard documents (e.g., certificate of incorporation, financial statements).
   - For high-risk clients, additional documents are requested.

5. **Negative News Screening**:
   - Automated screening against news sources.
   - Results categorized as Low, Medium, or High impact.
   - Example alert:
     ```json
     {
       "clientId": "001",
       "articleId":"NEWS001",
       "event":"Existing Client Screening Alert - Negative News",
       "headline": "Croatian Regulator Fines Multiple Online Casinos for AML Violations",
       "content": "The Croatian Financial Services Supervisory Agency (HANFA) has imposed fines totaling €5 million on several online casino operators for breaches of anti-money laundering regulations...",
       "publicationDate": "2024-03-15",
       "source": "European Gaming Industry News",
       "relevantEntities": ["Online Casinos", "Croatia"],
       "articleRiskScore": 8,
       "eventRiskClassification":"Non Material"
     }
     ```

6. **Sanctions and PEP Screening**:
   - Automated screening against multiple sanctions lists and PEP databases.
   - Any matches trigger immediate review and potential escalation.

7. **Risk Rating Calculation**:
   - Final risk rating calculated based on all collected data.
   - Determines level of ongoing due diligence and monitoring.

8. **Decision Making**:
   - AI agent recommends accept, reject, or escalate based on all data points.
   - For high-risk or edge cases, human review is required.

9. **Ongoing Monitoring**:
   - Periodic reviews scheduled based on risk level (annual for low-risk, quarterly for high-risk).
   - Continuous screening for negative news and sanctions.

## 4. Data Storage and Management

- Client profiles and related data stored in Postgres database.
- Event-driven updates processed through Kafka for real-time risk assessment.
- Strict access controls and encryption to ensure data privacy and security.

## Conclusion

This data design and flow enables Kai to process complex KYB scenarios efficiently, balancing regulatory compliance with operational efficiency. The system's ability to handle diverse client profiles, assess risk dynamically, and adapt to changing circumstances makes it a powerful tool for modern KYC processes in the financial sector.

