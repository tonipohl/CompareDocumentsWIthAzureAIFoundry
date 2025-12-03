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


## Test a model

~~~~
I work in the central purchasing department of my organization and am currently evaluating offers from various suppliers for a project. The offers are provided as PDF files attached. Extract the vendor, price, and terms and conditions.
~~~~


## Microsoft Foundry Model Endpoints

Use your endpoint similar as here:

https://<YOUR-PROJECT>-resource.openai.azure.com/openai/v1/responses

The authorization can be used with API key, or with a entitled app, such as a Managed Identity that has permissions to the foundry project in Azure.


## Agent Body

~~~~json
{
    "model": "gpt-5-nano",
    "input": [
        {
            "role": "user",
            "content": [
                {
                    "type": "input_text",
                    "text": "@{variables('prompt')}"
                },
                {
                "type": "input_file",
                "filename": "file1.pdf",
                "file_data": "data:application/pdf;base64,@{outputs('Compose')}"
                },
                {
                "type": "input_file",
                "filename": "ourterms.pdf",
                "file_data": "data:application/pdf;base64,@{outputs('Composeterms')}"
                }
            ]
        }
    ]
}
~~~~


## System Prompt To Extract Information from a Document

~~~~
I work in the central purchasing department of my organization and am currently evaluating offers from various suppliers for a project. The offers are provided as PDF files. Please extract the relevant data according to the JSON schema defined below with the highest possible accuracy. Pay special attention to the following points: Currency values: Preserve both the numeric amount and the currency symbol or currency name.\nNormalization of entities: Convert all data into the appropriate data types (e.g., text as string, quantities as numbers, dates in ISO-8601 format).\nFlat structure: Unpack nested entities so that all elements appear at the same level in the output.\nAlways present the contents in English. Units and quantities: Ensure that numeric values with units are correctly recognized and extracted.\nLinked data: Maintain consistency and integrity across referenced fields. Compare each individual file with the General Terms and Conditions of Purchase of ourterms.pdf and evaluate each offer based on how well it complies with the conditions in ourterms.pdf. Add a compliance rating under the key \"Rating\" with a value from 1 to 5, where:\n1 = lowest compliance\n5 = full compliance.\nUse the value 5 only if the offer is 100% compliant with the conditions in ourterms.pdf. Provide a clear justification for the rating under the key \"RatingReason\"\n.The final JSON output must accurately reflect all extracted entities, especially currency values, and conform to the expected data types. Ignore Markdown comments such as \\`\\`\\` and return only the pure JSON.\n{ \\\"Filename\\\": \\\"[filename]\\\", \\\"Vendor\\\": \\\"[company]\\\",  \\\"OfferNumber\\\": \\\"[offernumber]\\\", \\\"OfferDate\\\": \\\"[offerdate]\\\", \\\"OfferValidToDate\\\": \\\"[offervalidto]\\\", \\\"Project\\\": \\\"[project]\\\", \\\"PriceTotal\\\": \\\"[totalpricenet]\\\", \\\"Delivery\\\": \\\"[conditions]\\\", \\\"PaymentConditions\\\": \\\"[conditions]\\\", \\\"Warranty\\\": \\\"[warranty]\\\", \\\"Maintenance\\\": \\\"[maintenance]\\\", \\\"ContactPerson\\\": \\\"[contactperson]\\\", \\\"Rating\\\": \\\"[rating]\\\", \\\"RatingReason\\\": \\\"[ratingreason]\\\", \\\"UID\\\": \\\"[UIDnumber]\\\", \\\"Pages\\\": \\\"[numberofpages]\\\", \\\"Recipient\\\": \\\"[recipient]\\\" }
~~~~


## System Prompt To Compare Documents

~~~~
Compare the following data from offers. Create a table with a comparison in Markdown with all fields in this order. Each offer starts with a heading. At the end, add the best offer by the best rating, where 1 is best, and the lowest price.\n# OFFER\nFilename: @{variables('field')?['Filename']}\nVendor: @{variables('field')?['Vendor']}\nOfferNumber: @{variables('field')?['OfferNumber']}\nOfferValidToDate:  @{variables('field')?['OfferValidToDate']}\nProject: @{variables('field')?['Project']}\nPriceTotal: @{variables('field')?['PriceTotal']}\nDelivery: @{variables('field')?['Delivery']}\nPaymentConditions: @{variables('field')?['PaymentConditions']}\nWarranty: @{variables('field')?['Warranty']}\nMaintenance: @{variables('field')?['Maintenance']}\nContactPerson: @{variables('field')?['ContactPerson']}\nRating: @{variables('field')?['Rating']}\nRatingReason: @{variables('field')?['RatingReason']}\nUID: @{variables('field')?['UID']}\nPages: @{variables('field')?['Pages']}\nRecipient: @{variables('field')?['Recipient']}\n
~~~~

Happy testing!
