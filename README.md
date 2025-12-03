# Compare Documents with Azure AI Foundry

This solution automates the process of evaluating vendor quotes for a specific project using Azure AI Foundry and SharePoint.

**Use Case**  
Vendors submit multiple PDF documents containing their quotes. These documents are uploaded to a designated SharePoint document library.

**Workflow Overview**

1.  **Document Upload & Extraction**
    *   When a new file is added to the library, the **Logic App `02-NewFile`** is triggered.
    *   The flow retrieves the uploaded quote and a predefined *Terms and Conditions* PDF.
    *   It extracts key information from the quote and compares its compliance against the terms and conditions.

2.  **AI-Powered Compliance Analysis**
    *   An Azure AI Foundry model evaluates the quote’s compliance.
    *   The result includes a **compliance rating** and **justification**, which are stored as metadata in SharePoint.

3.  **Quote Comparison & Recommendation**
    *   The **Logic App `03-Compare`** aggregates all quotes in the *Compare* folder.
    *   It generates a Markdown table summarizing compliance scores and prices.
    *   A recommendation is provided based on the best rating and lowest price.

**Customization**  
Both flows are fully adaptable to meet specific business requirements.

