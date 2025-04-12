### Purpose
- Help restoration practitioners make data-driven decisions to improve Restore -> Observe -> Analyze cycle

### Functional Requirements
#### In Scope
1. Restoration Practitioners (RP) can demarcate restoration sites in supported eco-regions
2. Restoration Practitioners can demarcate reference sites in supported eco-regions
3. Biodiversity Index - Derived for a restoration site in-context of the eco-region where the site belongs to
4. Restoration Site -> Reference Site(s) Comparator - restoration practioner can select reference site(s) and compare her restoration site index with that of reference site(s)
5. Habitat Indicator -> indicates how a given restoration site is evolving over-time in-terms of habitat niches

#### Out of Scope
1. Ability to add observations. RPs have to do it directly to supported CSIS, atleast in current version being built
2. Native Mobile Apps support. 

### Quality Requirements
1. 

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
          No. of Observations in Eco-region / No. of Observations at Restoration Site
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

### Sketching

```mermaid
    flowchart LR
      A[Web App] --> B[API Gateway]
      B --> C[UserService]   
      B --> D[RestorationSiteService]
      B --> E[ReferenceSiteService]
      B --> F[BiodiversityIndexer]
      B --> G[HabitatIndicator]
      F --> H[SpeciesService]
      F --> I[ObservationsService]
      G --> H
      G --> I
      J[Integrator] --> K[CSIS]
      J --> L[EcoRegionService]
      J --> M[Message Broker]
      H --> M 
      I --> M
      D --> L
      E --> L
      D --> I
      E --> I    
```
### APIs
#### EcoRegionService
```mermaid
    classDiagram
        class Region{
            <<record>>
            -id: long
            -name: String
            -geoHash: String
        }
```
##### getRegion()
```Region getRegion(lat:double, lng:double)```

