# Azure Logic Apps

**Prerequisites**  
The Azure Logic Apps require:

*   An **Azure AI Foundry project** with a configured endpoint and API key
*   A **SharePoint document library** for storing and processing files

These components should be customized to fit your environment. A **Bicep deployment template** will be added to this repository in a future update.


## **Prerequisites**

Before deploying and running the Logic Apps in this solution, ensure the following components are available and configured:

1.  **Azure AI Foundry Project**
    *   Create an Azure AI Foundry project.
    *   Obtain the **endpoint URL** and **API key** for the deployed model (e.g., GPT-based model).
    *   Configure these values in your Logic App settings or as environment variables.

2.  **SharePoint Document Library**
    *   Set up a SharePoint document library to store vendor quotes and related documents.
    *   Ensure the Logic App has appropriate permissions to access this library (via Microsoft Graph or SharePoint connector).

3.  **Azure Logic Apps**
    *   Deploy the provided Logic Apps (`02-NewFile` and `03-Compare`) to your Azure subscription.
    *   Update connection references for SharePoint and Azure AI Foundry in each Logic App.

4.  **Authentication & Permissions**
    *   Assign necessary roles and permissions for the Logic Apps to interact with SharePoint and Azure AI services.
    *   Typically, this includes **Azure AD app registration** and granting delegated or application permissions.

5.  **Future Deployment Automation**
    *   A **Bicep template** for automated deployment will be added to this repository soon. This will simplify provisioning of all required resources.


