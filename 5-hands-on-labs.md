## Hands-on Labs

### Lab 1: SAP BTP Account Setup & Configuration
**Objective**: Master SAP BTP account structure and service provisioning

**Prerequisites**: Valid SAP BTP trial or productive account

**Steps**:
1. **Global Account Configuration**
   ```bash
   # Using SAP BTP CLI
   btp login --subdomain <your-subdomain>
   btp get accounts/global-account
   ```

2. **Subaccount Creation & Management**
   - Create development, staging, and production subaccounts
   - Configure entitlements for each environment
   - Set up proper user access controls

3. **Service Instance Creation**
   ```json
   {
     "service": "destination",
     "plan": "lite",
     "parameters": {
       "HTML5Runtime_enabled": true,
       "init_data": {
         "instance": {
           "destinations": []
         }
       }
     }
   }
   ```

### Lab 2: CAP Application Development
**Objective**: Build a full-stack application using Cloud Application Programming Model

**Architecture Overview**:
- Frontend: SAP Fiori Elements / UI5
- Backend: Node.js / Java with CAP
- Database: SAP HANA Cloud
- Integration: OData services

**Implementation Steps**:
1. **Project Initialization**
   ```bash
   cds init bookshop --add java
   cd bookshop
   ```

2. **Data Model Definition**
   ```cds
   namespace my.bookshop;
   
   entity Books {
     key ID : Integer;
     title : String;
     author : String;
     price : Decimal(9,2);
     stock : Integer;
   }
   
   service CatalogService {
     entity Books as projection on my.bookshop.Books;
   }
   ```

3. **Service Implementation**
   ```javascript
   module.exports = (srv) => {
     srv.on('READ', 'Books', async (req) => {
       // Custom logic implementation
       return await cds.db.run(req.query);
     });
   };
   ```

### Lab 3: Integration Suite Configuration
**Objective**: Implement end-to-end integration scenarios

**Scenario**: Connect SAP S/4HANA to Salesforce through SAP Integration Suite

**Components**:
- API Management for gateway functionality
- Cloud Integration for process orchestration
- Event Mesh for real-time event handling

**Configuration Steps**:
1. **API Proxy Creation**
   - Import S/4HANA OData service specification
   - Configure authentication policies
   - Implement rate limiting and caching

2. **Integration Flow Development**
   ```xml
   <bpmn:process id="S4HANAToSalesforce" isExecutable="true">
     <bpmn:startEvent id="timer-start">
       <bpmn:timerEventDefinition>
         <bpmn:timeCycle>0 0/15 * * * ?</bpmn:timeCycle>
       </bpmn:timerEventDefinition>
     </bpmn:startEvent>
     <!-- Integration flow definition -->
   </bpmn:process>
   ```

### Lab 4: AI Services Implementation
**Objective**: Integrate SAP AI services into business applications

**Use Case**: Document processing with Document Information Extraction

**Implementation**:
1. **Service Configuration**
   ```python
   import requests
   
   def extract_document_info(file_path):
       url = "https://aiservices-dox.cfapps.eu10.hana.ondemand.com/document/jobs"
       headers = {
           "Authorization": "Bearer " + access_token,
           "Content-Type": "multipart/form-data"
       }
       
       with open(file_path, 'rb') as file:
           response = requests.post(url, files={'file': file}, headers=headers)
       
       return response.json()
   ```

2. **Result Processing & Integration**
   ```javascript
   // CAP service integration
   srv.on('processDocument', async (req) => {
     const result = await callAIService(req.data.documentId);
     
     await UPDATE('ProcessedDocuments').set({
       extractedData: result.extraction,
       confidence: result.confidence,
       status: 'PROCESSED'
     }).where({ID: req.data.documentId});
   });
   ```

### Lab 5: Security & Identity Management
**Objective**: Implement comprehensive security framework

**Components**:
- XSUAA service configuration
- Role-based access control
- API security implementation

**Security Configuration**:
```json
{
  "xsappname": "bookshop-app",
  "tenant-mode": "shared",
  "scopes": [
    {
      "name": "read",
      "description": "Read access to books"
    },
    {
      "name": "write", 
      "description": "Write access to books"
    }
  ],
  "role-templates": [
    {
      "name": "BookReader",
      "scope-references": ["read"]
    },
    {
      "name": "BookManager",
      "scope-references": ["read", "write"]
    }
  ]
}
```
