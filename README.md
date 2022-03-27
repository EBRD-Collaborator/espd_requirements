# Description

This Extension is proposed for Public Procurement Systems based on EU Laws and Esuropean Single Procurement Document (ESPD). This Extension allows to maintain ESPD Parts III and IV, Exclusion Grounds and Selection Criteria. This can be used in countries in full accordance with EU Procurement Laws, bu also in countries, where these Laws are not fully implemented (it can change Requirements). Main approach - the Requirements Extension is selected due to use of this model (Criterion -> Requirement Group -> Requirements) in European Bank for Reconstruction and Development digital tools, which are intended to monitor Tender procedures automatically and keep procedures transparent. This does not allow to use ESPD model directly, as it has two additional nested lewels, which can't be parsed in existring tools architecture.

This leads to use Requirement Extension, based on CCCEV model (see below) as framework, ESPD Parts III and IV, Exclusion Grounds and Selection Criteria combined. In this case, all required information can be stored and handled easily. All Criteria and Requirement Groups are taken from ESPD Parts III and IV, Exclusion Grounds and Selection Criteria parts. Requirements are proposed on the basis on NEPCO-JONEPS system (https://github.com/EBRD-Open-Government-Lab-Kyiv/nepco_qualification_requirements) and ESPD document, but its not mandatory to use Requirements from NEPCO-JONEPS system here, also list can be extended. It can be created on the basis on current EU Laws or current Public Procurements systems. Also Requirements can be transferred from Requirements Groups (as well as Requirement Groups can be taken from Criteria list) in case of absence.

Full model in table view with all connections is presented in Standard Bidding Document folder, in Excel file.

This approach allows to use existing EBRD digital tools for Tender monitoring for EU countries and Procurement Systems, where ESPD and corresponding Laws are fully implemented. The main difference with existing Requirements Extension is mandatory naming for Criteria, Requirement Groups, presented in corresponding codelists.


# Requirements

The requirements extension is based on the EU's [Core Criterion and Core Evidence Vocabulary (CCCEV)](https://joinup.ec.europa.eu/node/153001) model for communicating criteria and responses.

The extension is designed to allow procuring entities or buyers to express criteria, relating to either items being procured or bidders themselves, as structured data.

Criteria can be responded to either by bidders, buyers or procuring entities, for example a buyer may respond with information about an item whilst a procuring entity may respond with information on whether a bidder is disbarred.

Criteria list is designed on the basis of European Single Procurement Document (ESPD) Exclusion Grounds and Selection Criteria sections. This allows complete ESPD in OCDS format, all other sections can be expressed in core OCDS 1.1 fields.


## CCCEV Model

The CCCEV model defines the following concepts:

**Criterion**
A criterion represents a rule or principle used to judge, evaluate or assess either an item or bidder. A criterion is satisfied when one or more of it's requirement groups are satisfied. Criteria are taken from ESPD Eclusion Grounds and Selection criteria sections (III and IV).

**Requirement Group**
A requirement group is a collection of one or more individual requirements. A requirement group is satisfied when all of it's requirements are satisfied.

**Requirement**
An atomic requirement which can be expressed as either an expected value or a range of accepted values.

**Requirement Response**
A requirements response is an assertion that responds to a specific requirement.

Therefore the CCCEV model can be used to express both **AND** conditions, where a group of requirements must be met to satisfy a criterion, and **OR** conditions, where there are alternative requirements that can satisfy a criterion.

## Schema

The extension introduces a new building block for each of the concepts described above, these are added to the following locations in the OCDS schema:

* *tender.criteria* - an array of criteria
* *tender.criteria.requirementGroups* - an array of requirement groups
* *tender.criteria.requirementGroups.requirements* - an array of requirements
* *bids.requirementResponses* - an array of requirement responses (Note: depends on *bid* extension)
* *awards.requirementResponses* - an array of requirements responses
* *contracts.requirementResponses* - an array or requirement responses

## Example

Below is an example of requirements specified against both an item and a bidder which demonstrates both **AND** and **OR** conditions:

```json
{
  "tender": {
    "criteria": [
      {
        "id": "0001",
        "title": "General yearly turnover",
        "description": "General yearly turnover must be at least 1M EUR",
        "source": "tenderer",
        "relatesTo": "item",
        "relatedItem": "item1",
        "requirementGroups": [
          {
            "id": "0001-001",
            "title": "General Yearly Turnover for bidder",
            "requirements": [
              {
                "id": "0001-001-01",
                "title": "General Turnover for year 1",
                "description": "Value of general turnover",
                "dataType": "value",
                "value": {"amount": "100000", "currency":"EUR"}
              }
            ]
          }
        ]
      },
      {
        "id": "0002",
        "title": "Number of managerial staff",
        "description": "There are should be at least 5 managerial staff persons with minimum 3 years working experience in current role",
        "source": "tenderer",
        "relatesTo": "item",
        "relatedItem": "item1",
        "requirementGroups": [
          {
            "id": "0002-001",
            "title": "Information on managerail staff",
            "requirements": [
              {
                "id": "0002-001-01",
                "title": "Total work experience (years)",
                "description": "Total work experience (years) of managerial staff",
                "dataType": "integer",
                "pattern": "[0-9]*",
                "minValue": 3
              }
            ]
          },
          {
            "id": "0002-002",
            "title": "Average annual manpower",
            "requirements": [
              {
                "id": "0002-002-01",
                "title": "Name of candidate",
                "description": "Name of candidate",
                "dataType": "string",
                "pattern": "[a-zA-Z ]*",
                "minValue": "John Doe"
              },
              {
                "id": "0002-002-02",
                "title": "Professional qualifications :",
                "description": "list professional qualification relevant to the Contract and the proposed position",
                "dataType": "string",
                "expectedValue": "related qualifications"
              }
            ]
          }
        ]
      },
      {
        "id": "0003",
        "title": "Tools, plant or technical equipment",
        "description": "SC_Abilities_2_(Facilities,___)",
        "source": "tenderer",
        "relatesTo": "tenderer",
        "requirementGroups": [
          {
            "id": "0003-001",
            "title": "Equipment Capacity",
            "requirements": [
              {
                "id": "0003-001-01",
                "title": "Equipment type and characteristics",
                "description": "description of equipment",
                "dataType": "string",
                "pattern": "[a-zA-Z ]*",
                "expectedValue": "Type: , Characteristics: "
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Below is an example of responses which meet the above requirements:

```json
  "bids": {
      "details": [
        {
          "id": "1",
          "requirementResponses": [
            {
              "id": "T1",
              "value": { # new type
                "amount": 5541314.23,
                "currency": "EUR"
              },
              "period": {
                "startDate": "2013-04-03T00:00:00Z",
                "endDate": "2014-04-03T00:00:00Z"
              }
              "requirement": {
                "id": "T1",
                "title": "FY 1"
              }
            },
            {
              "id": "T2",
              "value": {
                "amount": 9231341.00,
                "currency": "EUR"
              },
              "period": {
                "startDate": "2014-04-03T00:00:00Z",
                "endDate": "2015-04-03T00:00:00Z"
              }
              "requirement": {
                "id": "T2",
                "title": "FY 2"
              }
            },
            {
              "id": "T3",
              "value": {
                "amount": 9941927.15,
                "currency": "EUR"
              },
              "period": {
                "startDate": "2015-04-03T00:00:00Z",
                "endDate": "2016-04-03T00:00:00Z"
              }
              "requirement": {
                "id": "T3",
                "title": "FY 3"
              }
            }
          ]
        }
      ]
    }
```

## Further extensions

The CCCEV model also defines a number of additional concepts including **formalFrameworks**, used to specify the legal instruments from criteria are derived, **evidence**, used both to specify and provide the evidence required to support a requirement response, and additional properties of *requirements* such as **certificationLevel** which are not currently implemented in this extension.

This extension does not describe formulae for calculating computed values, nor does it describe whether data should be published openly or not.


