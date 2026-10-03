# Salesforce Insurance Policy & Claim Automation System

An end-to-end Salesforce Insurance Management application featuring custom object modeling, dynamic Lightning Web Components (LWC), Apex controllers with test coverage, automated quoting screen flows, and record-triggered claim routing flows.

---

## 📸 Key Features

- **Custom Object Data Architecture**: Custom `Policy__c` and `Claim__c` objects with Record Types, Field Sets, and custom fields[cite: 19, 21, 25, 26, 27].
- **Automated Policy Quoting Flow**: Multi-screen flow capturing basic customer details, vehicle-specific information, calculating premium via Apex, and creating draft policy records[cite: 1, 13, 16].
- **Claim Management & Routing**: Record-triggered flow to automatically route claims to dedicated queues (Auto, Property, Life) based on Policy Record Type[cite: 11, 12].
- **Assigned Claims Dashboard (LWC)**: Custom Lightning Web Component allowing adjusters to filter claims by policy type and view assigned workload in a responsive grid layout[cite: 6].
- **Apex Backend & Unit Testing**: Controller implementation with complete Apex test classes using `@testSetup`.

---

## 🛠️ Data Model & Schema

### 1. Custom Objects

#### **Policy (`Policy__c`)**[cite: 27]
* **Record Types**: `Auto`, `Life`, `Property`[cite: 26]
* **Fields**:
  * `Customer__c` (Lookup to Contact)[cite: 24]
  * `Beneficiary_Name__c` (Text)[cite: 24]
  * `Model_Year__c` (Text)[cite: 24]
  * `VIN__c` (Text)[cite: 23]
  * `Square_Footage__c` (Number)[cite: 23]
  * `Year_Built__c` (Text)[cite: 23]
  * `Policy_Start_Date__c` (Date)[cite: 23]
  * `Policy_Term_Months__c` (Number)[cite: 23]
  * `Premium__c` (Currency)[cite: 23]
* **Field Sets**: `Life`, `Property`, `Vehicle`[cite: 25]

#### **Claim (`Claim__c`)**[cite: 22]
* **Record Types**: `Accident`, `Life`, `Property`[cite: 21]
* **Fields**:
  * `Adjuster__c` (Lookup to User)[cite: 19]
  * `Approval_Status__c` (Picklist)[cite: 19]
  * `Claim_Amount__c` (Currency)[cite: 19]
  * `Date_of_Loss__c` (Date/Time)[cite: 19]
  * `Description__c` (Long Text Area)[cite: 19]

---

## ⚙️ Flows & Automation Architecture

### 1. AutoQuotingFlow (Screen Flow)
- **Purpose**: Interactive quoting process for generating auto insurance policies[cite: 1].
- **Flow Steps**:
  1. **Get Record Type**: Retrieves `Get Vehicle RT ID`[cite: 16].
  2. **Screen 1**: `New Auto Policy Quote - Basic Information`[cite: 16].
  3. **Screen 2**: `Vehicle-Specific Details`[cite: 16].
  4. **Apex Action**: `Calculate Premium Action` (invokes backend calculation engine)[cite: 1, 13].
  5. **Create Records**: `Create Draft Policy`[cite: 1].

### 2. Claim Routing Flow (Record-Triggered Flow)
- **Trigger**: When a `Claim__c` record is created[cite: 12].
- **Logic**:
  - Fetches associated policy details (`Get Policy RT`).
  - Decisions branch (`Route by Policy Type`)[cite: 11]:
    - **Auto Claim Route** ➔ Assign to `Auto Queue`[cite: 11]
    - **Property Claim Route** ➔ Assign to `Property Queue`[cite: 11]
    - **Life / Default Route** ➔ Assign to `Life Queue`[cite: 11]

---

## 💻 Lightning Web Components (LWC)

### `claimsDashboardLwc`
- **Description**: Displays all assigned claims for the logged-in user with policy type filtering capabilities[cite: 6].
- **Target Configurations**:
  - Target API Version: `67.0`[cite: 10]
  - Supported Locations: `lightning__AppPage`, `lightning__HomePage`[cite: 10]
- **Child Components**: `claimTileLwc`[cite: 6]

```html
<!-- Example Template Layout -->
<lightning-card title="My Assigned Claims Dashboard" icon-name="standard:claim">
    <lightning-combobox
        name="policyFilter"
        label="Filter by Policy Type"
        value="All"
        options={policyTypeOptions}
        onchange={handleFilterChange}>
    </lightning-combobox>
    
    <template if:true={visibleClaims}>
        <div class="slds-grid slds-wrap">
            <template for:each={visibleClaims} for:item="claim">
                <c-claim-tile-lwc key={claim.claimId} claim-data={claim}></c-claim-tile-lwc>
            </template>
        </div>
    </template>
</lightning-card>
