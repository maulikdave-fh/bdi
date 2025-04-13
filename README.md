### Purpose
- Build online platform to help restoration practitioners make data-driven decisions to improve "Restore -> Observe -> Analyze" cycle

### Functional Requirements
#### In Scope
1. Restoration Practitioners (RP) can demarcate restoration sites in supported eco-regions
2. Restoration Practitioners can demarcate reference sites in supported eco-regions
3. Biodiversity Index - Derived for a restoration site in-context of the eco-region where the site belongs to
4. Restoration Site -> Reference Site(s) Comparator - restoration practioner can select reference site(s) and compare her restoration site index with that of reference site(s)
5. Habitat Indicator -> indicates how a given restoration site is evolving over-time in-terms of habitat niches

#### Out of Scope
1. Ability to add observations. RPs have to do it directly to supported Crowdsourced Species Identification System (CSIS), atleast in current version being built
2. Native Mobile Apps support
3. Discussion Forum

### Quality Requirements
#### Scalability
1. Millions of observations coming from CSIS(s) in the first fetch
2. Thousands of observations coming from CSIS(s) in subsequent fetches (depending on the fetch frequency & number of eco-regions supported)
3. Thousands of RPs - most RPs spending 1-2 hours/day
#### Availability
1. Aim for 99.9% uptime
#### Performance
1. < 2secs of web-page load-time at 95pt

### Brainstorming
```mermaid
    mindmap
      root((bdi))
        Eco-region
          North Western Ghats Montane Rainforest
          North Western Ghats Moist Decidious Forest
        Habitat
        Observation
          Location
          Observation Date
          Species            
        Species
          Eco-region
          Habitat
          Keystone Species
          IUCN Status
          isInvasive
          isIntroduced
          Position in Food-chain                    
        Crowdsourced Species Identification System (CSIS)
          iNaturalist
          eBird
        Reference Site
          Boundaries
          Obeservations
          Eco-region
        Restoration Site
          Boundaries
          Observations
          Eco-region
        Scores
          Biodiversity Index
            Species Density            
            Species Score
                Species Spread
                No. of Observations in Eco-region / No. of Observations at Restoration Site
               Ratio of Taxa count in Eco-region / Taxa count in Restoration Site
          Habitat Indicator              
```

### Domain Model / Concept Model
```mermaid
    flowchart LR
        A([Observation Service]) --> |fetches observations| B([CSIS])
        A --> |determines eco-region| C([Eco-region Service])
        D([Species Service]) --> |extracts species info| A
        E([Biodiversity Indexer]) --> |uses| A
        E([Biodiversity Indexer]) --> |uses| D
        F([Habitat Indicator]) --> |uses| D
        F --> |uses| A
        G([Restoration Practitioner]) --> |creates| H([Restoration Site])
        G --> |identifies| I([Reference Site])
        H --> |uses| E
        H --> |uses| F
        I --> |uses| F
        I --> |uses| E
```

### System's APIs
```mermaid
    sequenceDiagram
        participant RP as Restoration Practitioner
        participant BDI as BDI Platform
        participant CSIS
        BDI ->> CSIS : fetchObservations(eco-region coordinates)
        CSIS -->> +BDI : Observations
        BDI -->> -BDI : Store Observations, Update species
        RP ->> +BDI : sign_up(fullname, username, password, etc)
        BDI -->> -RP : Authentication Token + userId
        RP ->> +BDI : get_homepage(useId)
        BDI -->> -RP: Homepage + Restoration Sites, Reference Sites
        RP ->> +BDI : Create / View Restoration site / Reference site
        BDI -->> -RP : confirmation
        RP ->> +BDI : get_biodiversity_index(userId, siteId)
        BDI -->> BDI : Calculate Biodiversity Index
        BDI -->> -RP : biodiversity index
        RP ->> +BDI : get_habitat_indicator(userId, siteId)
        BDI -->> BDI : Calculate habitat indicator
        BDI -->> -RP : habitat indicator
        RP ->> BDI : login(username, password)
        BDI -->> RP : auth token, userId
        RP ->> BDI : logout(userId)
        BDI -->> RP : confirmation
             
```
#### System APIs Listing
1. sign_up(fullname, username, password, email, profileimage) -> auth token, userId
2. login(username, password) -> auth token, userId
3. logout(userId) -> confirmation
4. get_homepage(userId) -> homepage + RP's restoration and reference sites
5. create_site(userId, polygon, siteType) -> siteId. siteType - restoration | reference
6. update_site(userId, siteId, polygon, siteType) -> siteId
7. delete_site(userId, siteId) -> confirmation
8. get_biodiversity_index(userId, siteId) -> biodiversity index
9. get_habitat_indicator(userId, siteId) -> habitat indicator

### System Architecture (WIP)
```mermaid
    flowchart LR
        RP([Restoration Practitioner]) <--> APIGateway([API Gateway])
        APIGateway <--> WebApp([Web App Service])
        APIGateway <--> RPS([Restoration Practitioners Service])
        RPS <--> RPSSQL[(NoSQL<br/> Document Store)]
        RPS --> |Profile Images| ObjStore[(Object Store)]
        APIGateway <--> Site([Restoration / Reference<br/> Site Service])
        Site <--> RSSSQL[(SQL Database)]
        APIGateway <--> ObjStore
```
#### Notes
1. CDN will be leveraged to reduce latency.
2. All services will have multiple instances and will be behind a load-balancer
3. Databases will be sharded & will have database replication
