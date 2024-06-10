# FinOps

FinOps is all about bridging the gap between the cloud spend and the revenue per project, application, etc. by bringing in accountability.

Are we getting value for our spend?
Are we being good stewards of the money?

## Capital Expenditure (CapEx)

- old way (on premises)
- user/team would make request for what they need
- finance control + management would look into it
- request approved or denied
- budget provided if approved and the infra was bought
- fixed cost
- over many years
- slow

## Operational Expenditure (OpEx)

- new way (cloud)
- consumption based
- provision when needed
- decentralized finance
    - no control?
    - no insight?
    - chaos?
    - does not have to be!
        - POINT OF FINOPS
- variable cost
- fast

## FinOps Characteristics

- put a good set of guardrails in place for finance
- a bit like RBAC for security
- =/= cost optimisation!
- bring stakeholders together
    - business
    - technical
    - finance
- accountability
    - why is that resource there?
    - do i need to have the resource?
    - what is the resource part of?
    - how much revenue is the resource generating?
    - is the resource worth having?
- help drive business growth
    - saving costs is not everything
    - spending money is not necessarily bad
        - wasting is bad
    - imagine $1 spend generates $10 revenue
    - know, why, what, got = value?
- visibility & accountability
    - in order to link value + why, what, how, etc.
        - needs to be visible fast
            - NOT 30 days later
        - needs to be visible to all stakeholders (business, technical, finance)
- structure is important
    - naming resources correctly
    - management groups and subscriptions
        - also for RBAC, budgets, policies, etc.
    - resource groups
    - structure around business units or geography or environments, etc.
- tags
    - metadata for resources
    - business unit, costcenter, project, application, owner, environment, etc.

### Azure Cost Management

- really powerful platform in Azure to view cost per resource
- smart views, filters, etc.
- can group by or filter by management group, subscription, resource group, resources, tags, etc.
- supports API to pull cost data and do your own thing
- apply monitoring & tracking
- apply budgets & alerting
    - can be set at many different levels: per subscription + tag + resource group, etc.
- trends, forecasts
- 

### Azure Policy

- enforce guardrails
- deny, add if not exists, remediation (e.g. copy tag from parent rg)
- huge amount of built-in policies available

## Optimization

- Azure Advisor
    - reduce waste
    - sustainability
    - various recommendations
        - Azure Savings Plan
        - Reserved Instances
        - Spot Instances
        - sizing
        - operations
        - clean up unused resources
        - optimize auto scaling
        - ...

## Azure FinOps Review Assessment

Are you doing the right things to understand where you are today? Do the test here:

https://aka.ms/finops/review