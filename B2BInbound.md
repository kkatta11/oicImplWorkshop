Table of Contents

[EXECUTIVE SUMMARY 1](#_Toc78540493)

[USECASE BACKGROUND 1](#_Toc78540494)

[Create an Integration 2](#create-an-integration)

[Configure the REST Adapter Trigger Connection
2](#configure-the-rest-adapter-trigger-connection)

[Configure For-Each Action 4](#configure-for-each-action)

[Configure Current Message as Tracking Variable
5](#configure-current-message-as-tracking-variable)

[Configure the B2B Translate Action
6](#configure-the-b2b-translate-action)

[Configure Mapping Actions 8](#configure-mapping-actions)

[Configure Log Activity to print Invoice Details from EDI Message
9](#configure-log-activity-to-print-invoice-details-from-edi-message)

[Add Tracking Identifiers for Integration Tracking
10](#add-tracking-identifiers-for-integration-tracking)

[Activate the Integration 10](#activate-the-integration)

[Testing the B2B Inbound Integration Scenario
11](#testing-the-b2b-inbound-integration-scenario)

# 

[]{#_Toc78540493 .anchor}**EXECUTIVE SUMMARY**

**Oracle Integration** provides native connectivity to Oracle and
non-Oracle Software as a Service (SaaS) and On-premises applications,
such as Oracle ERP Cloud, Oracle Service Cloud, HCM Cloud,
Salesforce.com, Workday, EBS, SAP, NetSuite and so on. Oracle
Integration adapters simplify connectivity by handling the underlying
complexities of connecting to applications using industry-wide best
practices. You only need to create a connection that provides minimal
connectivity information for each system

[]{#_Toc78540494 .anchor}**USECASE BACKGROUND**

Trading Partner (Dell Inc) sends an 810 Invoice EDI document to Host
ACME. ACME Corp had configured OIC B2B message exchange agreement to
receive Invoice EDI document and process the EDI document to create
Receivables Invoice for ex: in ERP/Netsuite Application.

High level step of the Integration:

  ---------------------------------------------------------------------------
  **Step**   **Description**
  ---------- ----------------------------------------------------------------
  1          Your (ACME Corp) backend application has a requirement to
             receive a business transaction (810 Invoice) from an external
             trading partner (Dell Inc). A scheduled integration- B2B Receive
             polls for a configured file pattern of Invoice document. A B2B
             action translates an Inbound Invoice EDI Message to Oracle
             Integration Message. If it is a valid Business Message invokes a
             Backend Integration which is configured in the Trading Partner
             Inbound Agreement

  2          You will design an ACME Corp backend application which receives
             a business transaction (810 Invoice) from an external trading
             partner (Dell Inc). Triggers your Inbound backend integration
             with a defined REST interface.

  2          Your backend integration instance receives the Invoice 810 EDI
             message. Using a B2B Translation activity, the Invoice 810 EDI
             (X12 or EDIFACT) message is translated into a B2B canonical XML
             format.

  3          Further, down the line integration processes the invoices for
             ex: maybe you want to create a payables invoice in the
             ERP/Netsuite for the Purchase Order raised. In this exercise you
             will create a log activity to capture some important fields from
             the translated EDI Invoice message.
  ---------------------------------------------------------------------------

  : The high-level steps involved in processing an outbound B2B message.

# Create an Integration

Let\'s create a basic, Inbound backend integration flow that receives an
XML document through a REST request, converts EDI X12 format to B2B XML
Canonical Message, and logs a few elements in the Invoice EDI document.

Note:

1.  In the navigation pane, click Integrations.

2.  On the Integrations page, click Create.

3.  Select App Driven Orchestration as the style to use. The Create New
    Integration dialog is displayed.

enter Process Inbound Vendor Invoices \<\<Student Id> as a Name of the
integration.

4.  and then click Create.

5.  Change Layout to Horizontal

## Configure the REST Adapter Trigger Connection 

On the integration canvas, click the start node and select "Sample REST
Endpoint Interface" as the trigger connection.

The Adapter Endpoint Configuration Wizard opens.

1.  On the Basic Info page, enter the following details:

    a.  In the What do you want to call your endpoint? field, enter
        "Receive-Vendor-Invoice-Msg"

    b.  Enter "/" as the endpoint\'s relative resource URI.

    c.  Select POST as the action to perform on the endpoint.

    d.  Select "configure request for this endpoint" only and click on
        Next

> ![](media/image1.png){width="5.090277777777778in"
> height="3.384301181102362in"}

2.  On the Request page:

```{=html}
<!-- -->
```
a.  Select JSON Sample in the **Select the request payload format**
    field and provide the below json sample selecting **\<\<inline>\>.**
    Click Next

{

\"type\" : \"PO\",

\"id\" : \"12345\",

\"direction\" : \"INBOUND\",

\"trading-partner\" : \"Acme\",

\"document-definition\" : \"PO_850\",

\"connectivity-properties-code\" : \"BACKENDINTG\",

\"connectivity-properties-version\" : \"01.00.0000\",

\"message\" : \[ {

\"b2b-message-reference\" : \"0AC400D117503A8246000000347849EB\"

}, {

\"b2b-message-reference\" : \"0AC400D117503A8246000000347849EA\"

} \]

}

![](media/image2.png){width="6.263888888888889in" height="4.525in"}

b.  On the Summary page select "Done"

![](media/image3.png){width="4.405698818897638in"
height="1.7497812773403325in"}

Save your integration flow

## Configure For-Each Action

Add a For Each Action

In the Create Action configure as per below and Select "Create"

  -----------------------------------------------------------------------
  Name                                Each-B2B-Doc
  ----------------------------------- -----------------------------------
  Repeating Element                   Drag and drop 'message' repeating
                                      element from the path execute -\>
                                      request-wrapper

  Current Element Name                B2B-Doc-Message
  -----------------------------------------------------------------------

![](media/image4.png){width="6.263888888888889in"
height="3.3986111111111112in"}

![](media/image5.png){width="6.263888888888889in"
height="2.5131944444444443in"}

Save your Integration Flow.

## Configure Current Message as Tracking Variable

Add an "Assign" activity within the "For Each" and name it as
"Assign-Current-Msg"

Select the "+" action and configure "tracking_var_2"

![](media/image6.png){width="6.263888888888889in" height="0.6125in"}

In the "Value" field Select the Pencil icon and provide
"b2b-message-reference" from

"B2B-Doc-Msg message" . Select Validate and Close.

![](media/image7.png){width="6.263888888888889in"
height="1.3340277777777778in"}

![](media/image8.png){width="6.263888888888889in"
height="1.9597222222222221in"}

Save your Integration Flow

## Configure the B2B Translate Action

Add a B2B action to the flow to translate EDI document to an XML
document

1.  On the right side of the canvas, click Actions ![Actions
    icon](media/image9.png){width="0.21597222222222223in"
    height="0.21597222222222223in"}, drag B2B, and drop it after the
    Assign-Current-Msg element.

> The Configure B2B Action wizard opens.

2.  On the Basic Info page, enter "Fetch-Message" as the name for the
    action and select a mode as "B2B Trading Partner mode", and click
    Next.

> ![](media/image10.png){width="6.263888888888889in"
> height="3.4604166666666667in"}

3.  Select the B2B message direction as "Inbound" and choose the
    Operation as "FetchMessage" and click Next.

> ![](media/image11.png){width="6.263888888888889in"
> height="3.123611111111111in"}
>
> Knowledge Point: This operation retrieves an already processed B2B
> business message from the Oracle Integration persistence store. It
> outputs the B2B canonical XML format for a business message,
> given b2b-message-reference as input. The canonical XML format is
> represented by the edi-xml-document element. It is accessible inside
> an inbound backend integration. You use the mapper to transform it
> into a backend application format.

4.  Select Document Definition as "Invoice Document 810 4030" and click
    on Next.

> Note: Invoice Document 810 4030 is created based on Standard 810 4030
> document
>
> ![](media/image12.png){width="6.263888888888889in"
> height="2.347916666666667in"}

5.  On the Summary page, click Done to complete the configuration and
    save your integration flow.

> Note that the corresponding mapping element is automatically added to
> the integration flow
>
> ![](media/image13.png){width="6.263888888888889in"
> height="1.6381944444444445in"}

## Configure Mapping Actions

Configure data mappings for the Fetch-Message action and B2B-Doc-Message
element to pass B2B EDI message.

![](media/image14.png){width="6.263888888888889in"
height="1.3277777777777777in"}

Click Validate and then Close. Save your integration flow.

![](media/image15.png){width="6.263888888888889in"
height="1.9479166666666667in"}

## Configure Log Activity to print Invoice Details from EDI Message

Knowledge Point: The **Fetch-Message Response** (B2B Action) provides
you with a B2B canonical XML from which to map.
Its **edi-xml-document** is the key element that contains the canonical
form of the inbound EDI document. 

1.  Add a "Logger" activity and name the Action as "Log-Invoice-Message"

2.  Select Log option as "When Trace is Enabled"

3.  Select the Pencil to open the Expression Builder to log 3 fields
    namely, Invoice Number, Purchase Order Number and Invoice Amount.

4.  Drag and drop a "concat" function from String functions category

5.  Construct the concat message as below:

> concat( \"Received an Invoice:\", BIG02, \", for the Purchase Order:
> \", BIG04, \". The invoice amount is: \", integer( TDS01) div 100.0,
> \"\")
>
> Note: It is easy to search for any field element by selecting the
> Source Message elements. Search for BIG02,BIG04,TDS01 from
> \$Fetch-Message Element and formulate the above concat expression.

![](media/image16.png){width="6.263888888888889in"
height="2.026388888888889in"}

Validate and Close the Expression Builder.

Save your Integration Flow.

![](media/image17.png){width="6.263888888888889in"
height="1.5243055555555556in"}

## Add Tracking Identifiers for Integration Tracking

Select fields from Input Schema as per below

![](media/image18.png){width="6.263888888888889in"
height="3.3993055555555554in"}

## Activate the Integration

Check for errors, save, and activate the integration flow.

1.  On the Integrations page, click on the activate button against your
    integration to activate it.

2.  Click Activate in the Activate Integration dialog and select "Enable
    Tracing" and "Include Payload" options

## Testing the B2B Inbound Integration Scenario

Note: Please complete the Trading Partner Setup before testing the B2B
Inbound Scenario. Refer the "Trading Partner Inbound Setup" document

1.  Navigate to Integrations. Your training instance is seeded with an
    Integration flow -- "OutboundEDI-XTP-Dell-Inc". Select "Run" and
    click on "Test" which will open an Integration Test Console.

> Let us use Oracle Integration Test Console to simulate a 810 Invoice
> EDI message from "Dell Inc" Supplier as per the Usecase described.
> This integration accepts an Invoice XML and pushes and 810 Invoice EDI
> message into a specific directory provided in the xml payload. In a
> real world scenario typically an ERP application (ex. ERP Cloud or
> Netsuite) generates the Receivables Invoice and sends the Invoice
> document to Acme Corp

a.  You must have received an xml file (DellIncInvoice.xml) as part of
    the artifacts, copy the data and paste it in the body of the request
    console. Before Selecting Test modify the below elements in the
    supplied XML payload

    -   InvoiceNumber -- ex: XX-INV-DE-0299

    -   XTP_FTP_DIR - /B2BWorkshop/B2BTPDELLIn

    -   XTP_FTP_FILENAME - Invoice-\<\<StudentId>\>-1.edi (Note: The
        file pattern should be similar to the configuration in the "Dell
        Inc" Trading Partner Setup for "FTP" Transport Input File value

![](media/image19.png){width="6.263888888888889in"
height="2.6305555555555555in"}

b.  You should see a response which is base64encoded

> ![](media/image20.png){width="6.263888888888889in"
> height="2.3895833333333334in"}
>
> Copy paste the base64 encoded data in any online base64 decoder we
> should see an Invoice EDI message. Here is the representational
> invoice equivalent to EDI message
>
> ![](media/image21.png){width="6.263888888888889in"
> height="2.938888888888889in"}

c.  Connect to the FTP server and check the /B2BWorkshop/B2BTPDELLIn.
    You should see an edi file (Invoice-\<\<StudentId>\>-XX.edi )
    generated by the external Trading Partner

![](media/image22.png){width="3.291255468066492in"
height="2.562179571303587in"}

2.  Navigate to Integration. Search for the B2B Integration -- "Dell Inc
    \<\<Student Id>\> FTP Receive" and Click on Run and Submit Now.

3.  Connect to the FTP server and check the /B2BWorkshop/B2BTPDELLIn.
    You should see an edi file is moved to
    /B2BWorkshop/B2BTPDELLIn_backup renamed as "\_processed". Indicating
    that the B2B integration had successfully handed over the edi
    document to Backend Integration

> ![](media/image23.png){width="2.99962489063867in"
> height="3.0100404636920386in"}
>
> Check the "/B2BWorkshop/B2BTPDELLOut" folder and we should see a 997
> Functional Ack generated
>
> ![](media/image24.png){width="2.635087489063867in"
> height="2.4788571741032372in"}
>
> Here is the representational format of Functional Acknowledgement
> which is generated.
>
> Knowledge Point: A separate Functional Ack will be generated for every
> Transaction set
>
> ![](media/image25.png){width="2.92671697287839in"
> height="2.603840769903762in"}

4.  Navigate to Monitoring B2B Tracking page. You should see 2 Business
    Messages under the Business Messages Tab for your specific Trading
    Partner.

> Tip: Select the Filter to search based on Trading Partner Name
>
> ![](media/image26.png){width="6.263888888888889in"
> height="2.472916666666667in"}

The first message is an Inbound Invoice document received by B2B
Integration (Receive Integration) and the second message is a Functional
Ack generated by B2B which is based on Trading Partner Inbound Agreement
configuration.

Click on the "View" icon and inspect Message Logs, Paylods

![](media/image27.png){width="6.263888888888889in"
height="2.8847222222222224in"}

Inspect the "Wire Messages"

![](media/image28.png){width="6.263888888888889in"
height="2.884027777777778in"}

5.  Navigate to Monitoring Integrations Dashboards. Select Activity
    Stream from "View"

> ![](media/image29.png){width="6.263888888888889in"
> height="1.8569444444444445in"}
>
> Filter the Activity Stream by searching for the Integration Name
> (Process Inbound Vendor Invoices \<\<Student Id>\>
>
> Notice the log which captures Invoice Number, Purchase Order No,
> Invoice Amount from the parsed EDI document
>
> ![](media/image30.png){width="6.263888888888889in"
> height="1.4194444444444445in"}

In conclusion, you can use Oracle Integration B2B feature to
automatically generate the B2B integration for receiving and the B2B
integration for sending. Both integrations are front-end integrations
because they directly interface with the external trading partner. These
integrations are at the heart of the transport for its runtime
functioning which allows the Integration developer to focus on creating
the backend Integrations to process the business messages.
