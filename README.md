### Purpose
- Help restoration-practitioners make data-driven decisions
- Restore -> Observe -> Analyze 

### Tools
- Biodiversity Index Calculator
- Reference Site(s) Comparator
- Habitat Detector

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
            Species Spread
            Species Score
            Ratio of Taxa count in Eco-region / Taxa count in Restoration Site
          Habitat Indicator              
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

