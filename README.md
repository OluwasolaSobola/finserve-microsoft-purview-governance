# FinServe Bank – Microsoft Purview Data Governance Implementation

**Hands-on Microsoft Purview & Azure Data Governance Portfolio Project | 2026**

## Project Overview

This project demonstrates a hands-on implementation of enterprise data governance using Microsoft Purview, Azure Data Lake Storage Gen2 (ADLS Gen2), and Azure Data Factory in a real Microsoft Azure environment.

I implemented a governed customer-data environment covering 11 banking datasets across customer master data, CRM, accounts, transactions, lending, fraud, marketing, privacy, regulatory reporting, and AI model data.

The implementation covers data discovery and cataloguing, metadata management, governance domains, governed data products, data quality profiling and controls, data classification, governance issue management, Azure Data Factory transformation, and technical data lineage.

## Technology Stack

- Microsoft Purview
- Microsoft Purview Data Map
- Microsoft Purview Unified Catalog
- Microsoft Purview Data Quality
- Azure Data Lake Storage Gen2
- Azure Data Factory
- Microsoft Azure
- Parquet
- CSV

## What I Implemented

### Data Discovery & Cataloguing
- Registered an Azure Data Lake Storage Gen2 source in Microsoft Purview.
- Configured a dedicated FinServe Customer Data collection.
- Configured managed-identity access and Azure RBAC for governed scanning.
- Scanned the data estate and discovered dataset-level assets and schemas.
- Curated business metadata at asset and column level.
- Increased discovered catalog assets from 13 to 24 following the introduction and rescan of 11 Parquet datasets.

### Governance Domain & Data Product
- Created and published the **Customer Banking** governance domain.
- Created the **Customer ID** business glossary term.
- Defined **Customer ID** as a Critical Data Element.
- Created and published the **Customer 360 Banking Data** data product.
- Configured governed access requirements including business justification, usage purpose, approval and restrictions on uncontrolled data copies.

### Azure Data Factory Transformation
- Built and executed an Azure Data Factory Copy pipeline.
- Converted 11 governed CSV datasets to Snappy-compressed Parquet in ADLS Gen2.
- Rescanned the transformed assets into Microsoft Purview.
- Used the Parquet assets to enable supported Microsoft Purview Data Quality profiling and assessment.

### Data Quality
- Profiled all 13 columns across 283 records in `Accounts.parquet`.
- Implemented six data quality controls across completeness, uniqueness, conformity and accuracy.
- The six configured Accounts controls achieved a quality score of 100.
- Identified 187 null `Interest_Rate` values (66.1%), requiring product-level applicability assessment rather than automatically treating every null as an error.
- Profiled 15 commercial CRM records.
- Implemented four CRM data quality controls.
- Identified `Contact_Email` and `Zip` completeness exceptions, each scoring 93.3%.
- Achieved 100 for populated email-format conformity and T24 customer-master identifier completeness.
- Achieved an overall CRM data quality score of 96.7.
- Investigated a Purview-generated statistical outlier action for `Last_Review_Date` and identified source date-type interpretation as a factor requiring business validation.

### Classification
- Reviewed Microsoft Purview automated classifications across the commercial CRM schema.
- Validated automated detection of email, phone, city and ZIP patterns.
- Identified a classification gap for the organisation-specific T24 customer identifier.
- Created the custom **FinServe Customer Identifier** classification.
- Applied the custom classification to the governed `T24_Customer_ID` schema element.

### Technical Lineage
- Connected Azure Data Factory to Microsoft Purview.
- Executed the transformation pipeline following lineage integration.
- Captured technical lineage in Microsoft Purview showing:

`CRM_Dynamics_Commercial.csv → Azure Data Factory Copy Activity → CRM_Dynamics_Commercial.parquet`

- Verified the lineage against the `adf-finserve-governance` data factory and `Convert_FinServe_CSV_to_Parquet` pipeline.

## Key Results

| Measure | Result |
|---|---:|
| Source banking datasets | 11 |
| CSV datasets converted to Parquet | 11 |
| Purview discovered assets after rescan | 24 |
| Accounts records profiled | 283 |
| Accounts DQ rules | 6 |
| Accounts configured-rule quality score | 100 |
| Interest Rate null values identified | 187 |
| CRM records profiled | 15 |
| CRM DQ rules | 4 |
| CRM overall quality score | 96.7 |
| Custom business classification created | FinServe Customer Identifier |
| Technical lineage | Successfully captured |

## Governance Approach

The implementation demonstrates that a high technical data-quality score should not automatically be interpreted as evidence that a dataset is free from business-quality risks.

For example, the configured Accounts controls achieved a score of 100, while profiling separately identified 187 null `Interest_Rate` values. Rather than automatically classifying all 187 records as defective, the finding was treated as requiring assessment against product and account-type business rules.

Similarly, the CRM `Last_Review_Date` finding was investigated rather than immediately marked as resolved because Purview profiled the field as numeric data and generated a statistical outlier based on that representation.

This demonstrates a governance approach of:

**Detect → Assess → Establish Ownership → Remediate → Re-profile/Re-scan → Close with Evidence**

## Project Context

FinServe Bank provides the fictional banking context and synthetic datasets used for this portfolio project. **All technical implementation documented in this repository was performed hands-on in a real Microsoft Azure and Microsoft Purview environment.**

The project is intended to demonstrate practical data governance implementation skills rather than represent a production deployment for a real financial institution.

## Repository Documentation

Supporting documentation and implementation evidence will include:

- Data asset register
- Data quality rules and findings
- Data quality remediation register
- Classification register
- Critical Data Element register
- Customer 360 Data Product Terms of Use
- Technical lineage documentation
- Azure Data Factory transformation evidence
- Microsoft Purview implementation screenshots

## Author

**Oluwasola Sobola**  
Data Governance | Data Quality | Data Management
