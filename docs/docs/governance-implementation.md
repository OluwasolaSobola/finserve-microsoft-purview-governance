# FinServe Bank – Microsoft Purview Governance Implementation

## 1. Purpose

This document describes the hands-on implementation of data governance for the FinServe Bank portfolio project using Microsoft Azure, Microsoft Purview, Azure Data Lake Storage Gen2 (ADLS Gen2), and Azure Data Factory.

The implementation demonstrates how governance concepts including data discovery, metadata management, data quality, classification, governed data products, access governance and technical lineage can be implemented within a real Microsoft Azure and Microsoft Purview environment.

## 2. Project Context

FinServe Bank provides the fictional banking context and synthetic datasets used for this portfolio project.

All technical configuration, scanning, transformation, profiling, classification and lineage activities documented here were performed hands-on in a real Microsoft Azure and Microsoft Purview environment.

The implementation is a portfolio demonstration and does not represent a production deployment for a real financial institution.

## 3. Data Estate

Eleven synthetic banking datasets were stored in Azure Data Lake Storage Gen2:

1. Accounts
2. AI Model Scores
3. CRM Dynamics Commercial
4. CRM Salesforce Retail
5. Customer Master T24
6. Fraud Alerts
7. Loan Applications
8. Marketing Campaigns
9. PII Inventory
10. Regulatory Reports
11. Transactions

These datasets represent multiple business and governance areas including customer data, accounts, lending, financial crime, marketing, privacy, regulatory reporting and AI/model risk.

## 4. Azure Data Lake Storage and Purview Registration

An ADLS Gen2 environment was used as the governed data source.

A dedicated **FinServe Customer Data** collection was configured in Microsoft Purview.

The ADLS Gen2 source was registered with Microsoft Purview and managed-identity access was configured using Azure role-based access control (RBAC).

The Purview managed identity was granted the required read access to support scanning and metadata discovery.

A connection test was successfully completed before the source was scanned.

## 5. Data Discovery and Cataloguing

An initial Microsoft Purview Data Map scan was executed against the governed ADLS Gen2 source.

The scan successfully discovered the source datasets and associated metadata.

The initial scan recorded **13 discovered assets**.

Following the later creation of Parquet versions of the 11 source datasets, the data source was rescanned.

The subsequent full scan completed successfully with **24 discovered assets**, reflecting the expanded governed data estate and associated catalog objects.

Business descriptions were added to selected assets and schema elements to improve metadata quality and business understanding.

## 6. Governance Domain

A governance domain named **Customer Banking** was created and published.

The domain represents customer-related data across customer master, CRM, account and related banking systems.

The governance domain provides a business-oriented structure for organising governed customer information and assigning governance responsibilities.

## 7. Business Glossary and Critical Data Element

A business glossary term named **Customer ID** was created to represent the enterprise customer identifier used across governed banking systems.

Customer ID was also defined as a **Critical Data Element (CDE)** because of its importance to:

- Customer identity resolution
- Customer-account relationships
- Cross-system reconciliation
- Master data management
- Customer 360 analysis
- Data quality monitoring

The CDE was defined as part of the governance model. Technical column mapping remained subject to the capabilities available within the portfolio environment.

## 8. Customer 360 Banking Data Product

A governed data product named **Customer 360 Banking Data** was created and published within the Customer Banking governance domain.

The product combines selected customer-master, CRM and account assets to provide a governed foundation for:

- Customer identification
- Customer 360 analysis
- Customer-account relationship analysis
- Cross-system reconciliation
- Master data management
- Duplicate and identity-resolution analysis
- Data quality monitoring
- Downstream analytics

The published data product contains six governed data assets.

A governed access policy was configured requiring an approved usage purpose and business justification.

The configuration also restricts uncontrolled data copies and provides an approval mechanism for access requests.

The data product was configured with a daily update frequency and achieved a **Healthy data quality score of 98.3** during the implementation.

A dedicated Terms of Use document was created to support governed consumption of the data product.

## 9. CSV Data Quality Compatibility Challenge

The original banking datasets were stored as CSV files.

During implementation, Microsoft Purview Data Quality could not be used to profile the CSV assets directly in the required workflow.

Rather than stopping at the cataloguing stage, the architecture was adapted to introduce a supported analytical file format.

Azure Data Factory was used to transform the governed CSV datasets into Parquet.

This allowed the project to progress from metadata discovery into practical data profiling and data quality rule implementation.

## 10. Azure Data Factory Transformation

An Azure Data Factory environment was configured for the project.

A Copy pipeline named:

`Convert_FinServe_CSV_to_Parquet`

was created.

The pipeline read the source CSV datasets from ADLS Gen2 and wrote Snappy-compressed Parquet versions back to the governed data lake.

All **11 source CSV datasets were successfully converted to Parquet**.

Azure RBAC was configured so that the Azure Data Factory managed identity had the required storage permissions.

A wildcard source filter of:

`*.csv`

was used to ensure that the Copy activity processed only source CSV files and did not attempt to reprocess generated Parquet outputs.

## 11. Accounts Data Quality Assessment

`Accounts.parquet` was profiled using Microsoft Purview Data Quality.

The dataset contained:

- 283 records
- 13 columns

Six governed data quality controls were implemented across the following dimensions:

- Completeness
- Uniqueness
- Conformity
- Accuracy

The controls included:

1. Account ID Completeness
2. Account ID Uniqueness
3. Customer ID Completeness
4. Currency Code Conformity
5. Duplicate Account Record Check
6. Account Status Validity

All six configured controls achieved a score of **100**.

However, profiling separately identified **187 null Interest_Rate values**, representing **66.1%** of the 283 account records.

These values were not automatically classified as defects because interest-rate applicability can depend on the underlying product or account type.

The finding was therefore treated as requiring business-rule assessment rather than uncontrolled remediation.

This demonstrates the distinction between technical rule compliance and broader business data quality.

## 12. Commercial CRM Data Quality Assessment

`CRM_Dynamics_Commercial.parquet` was also profiled.

The dataset contained **15 records**.

Four data quality controls were implemented:

1. Contact Email Completeness
2. Contact Email Format Conformity
3. ZIP Code Completeness
4. T24 Customer ID Completeness

Results included:

- Contact Email Completeness – **93.3**
- Contact Email Format Conformity – **100**
- ZIP Code Completeness – **93.3**
- T24 Customer ID Completeness – **100**

The overall CRM data quality score was **96.7**.

The results identified genuine completeness exceptions requiring steward review rather than presenting the dataset as universally error-free.

## 13. Data Quality Issue Management

Microsoft Purview generated governance actions from profiling results.

For Accounts, a high-null-count action was generated for `Interest_Rate`.

For the commercial CRM dataset, an outlier action was generated for `Last_Review_Date`.

Investigation showed that all 15 CRM values were populated, but Purview had profiled the field as a numeric value rather than a date.

The finding was therefore treated as requiring validation of source date semantics and data typing before closure.

The CRM action was moved to **In progress** to demonstrate governed issue investigation rather than simply dismissing or closing the finding.

The approach used throughout the project was:

**Detect → Assess → Establish Ownership → Remediate → Re-profile/Re-scan → Close with Evidence**

## 14. Data Classification

Microsoft Purview automated classifications were reviewed across the commercial CRM schema.

Automated classifications identified patterns including:

- Full names
- Email addresses
- U.S. phone numbers
- U.S. ZIP codes
- World cities

A governance gap was identified for the organisation-specific `T24_Customer_ID` field because a standard system classification did not represent its business meaning.

A custom classification named:

**FinServe Customer Identifier**

was therefore created.

The custom classification was manually applied to the governed `T24_Customer_ID` schema element.

This demonstrates the use of both automated system classifications and organisation-specific governance classifications.

## 15. Technical Data Lineage

Azure Data Factory was connected to Microsoft Purview to enable technical lineage capture.

Following the integration, the transformation pipeline was executed and lineage was successfully captured in Microsoft Purview.

The verified lineage path included:

`CRM_Dynamics_Commercial.csv → Azure Data Factory Copy Activity → CRM_Dynamics_Commercial.parquet`

The lineage process was associated with:

- Azure Data Factory: `adf-finserve-governance`
- Pipeline: `Convert_FinServe_CSV_to_Parquet`

This provides traceability from the source CSV asset, through the transformation process, to the governed Parquet target.

## 16. Governance Outcomes

The implementation produced the following outcomes:

| Governance Area | Outcome |
|---|---|
| Governed source datasets | 11 |
| CSV datasets converted to Parquet | 11 |
| Purview discovered assets after rescan | 24 |
| Governance domain | Customer Banking |
| Governed data product | Customer 360 Banking Data |
| Data product assets | 6 |
| Data product quality score | 98.3 |
| Accounts records profiled | 283 |
| Accounts DQ controls | 6 |
| Accounts configured-rule score | 100 |
| CRM records profiled | 15 |
| CRM DQ controls | 4 |
| CRM overall DQ score | 96.7 |
| Custom classification | FinServe Customer Identifier |
| ADF-to-Purview technical lineage | Successfully captured |

## 17. Skills Demonstrated

This implementation demonstrates practical experience in:

- Microsoft Purview
- Microsoft Purview Data Map
- Microsoft Purview Unified Catalog
- Microsoft Purview Data Quality
- Data discovery and cataloguing
- Metadata management
- Business glossary development
- Critical Data Elements
- Governance domains
- Governed data products
- Data quality profiling
- Data quality rules and controls
- Data quality issue investigation
- Data classification
- Custom classifications
- Azure Data Lake Storage Gen2
- Azure role-based access control
- Managed identities
- Azure Data Factory
- CSV-to-Parquet transformation
- Technical data lineage
- Data stewardship
- Governance documentation

## 18. Implementation Statement

FinServe Bank and the banking datasets provide a fictional and synthetic portfolio context.

**The Microsoft Azure, Azure Data Factory and Microsoft Purview implementation described in this document was performed hands-on in a real Microsoft Azure environment.**

This project demonstrates project-based practical experience and is not presented as a production deployment for a real financial institution.

---

**Author:** Oluwasola Sobola  
**Project:** FinServe Bank – Microsoft Purview Data Governance Implementation  
**Document Status:** Approved for Portfolio Demonstration  
**Year:** 2026
