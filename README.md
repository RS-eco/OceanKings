The Fate of the Kings - How does climate change affect the distribution
of marine top predators?
================

### Abstract

Marine top predators (cetaceans, pinnipeds, tunas, seabirds,
elasmobranchs, turtles and cephalopods) have a huge impact on marine
ecosystems, as they have an important top-down influence on marine food
webs and thus strongly influence biological productivity. With the
advancement of the Anthropocene the effects of anthropogenic activities
is steadily increasing, strongly affecting the distribution and
abundance of these organisms. Among human influences climate change is
one of the most pressing issues and a lot of effort is required in order
to limit global warming to a 1.5°C change compared to pre-industrial
levels. The global distribution of top marine predators across five
different groups (cetaceans, pinnipeds, fishes, seabirds and
elasmobranchs) was obtained from the IUCN Red List Spatial Database, the
BirdLife International Bird Species Distribution Database and the Ocean
Biogeographic Information System (OBIS). The global distribution was
modelled for present-day (1980-2010) using various modelling algorithms
(Random forests, Maximum entropy, Generalized linear models, Boosted
regression trees and Support vector machine) and environmental data
(current speed, current direction, temperature, dissolved oxygen,
primary productivity, salinity) from several global climate models
(“HadGEM2-ES”, “MIROC-ESM-CHEM”, “GFDL-ESM2M”, “IPSL-CM5ALR”)
available from the Inter-Sectoral Impact Model Intercomparison Project,
as well as bathymetry data available from the General Bathymetric Chart
of the Oceans. The change in distribution by mid-century (2036-2065) was
then assessed using the future climate projections of the four climate
models under the representative concentration pathway 2.6 (RCP2.6),
which represents a 1.5°C global warming by 2050. We will determine which
species and which group is affected the strongest by climate change and
thus needs additional conservation efforts. Furthermore, we compare the
change in distribution with the current spatial coverage of marine
protected areas to highlight if and where further conservation efforts
are needed to prevent a decline in the spatial distribution of these top
predators.

**Key words**: top predators, species distribution modelling, climate
change, protected areas, conservation

### R Setup

### Set file directory

``` r
# Specify path of file directory
filedir <- "E://Data/"
```

Note: You need to adapt filedir according to the path on your computer.

### Load and if necessary install required packages

``` r
# Install required packages not yet in library
packages <- c("ggplot2", "ggpmisc", "raster", "rgdal", "rgeos", "parallel")
new.packages <- packages[!(packages %in% installed.packages()[,"Package"])]
if(length(new.packages)) install.packages(new.packages); rm(new.packages)

# Load required packages
l <- sapply(packages, require, character.only = TRUE); rm(packages, l)
```

## Identify marine top predators

Web of Knowledge:

Topic search (Marine Top Predator) - 07.06.2017 250 newest records
(Results: 1429 Top predator\* marine, 1389 Marine\* top predator, 1389
marine top predator, 1484 marine\* top\* predator)

Topic search (Marine apex predators) - 07.06.2017 250 newest records
(Results: 329 marine\* apex predator, 333 apex predator\* marine, 332
apex\* predator\* marine, 328 marine\* apex\* predator)

## Load Web of Knowledge files

``` r
# Load search files
marine_top_pred <- read.csv2("data/marine_top_predators.csv")
marine_apex_pred <- read.csv2("data/marine_apex_predators.csv")

# Merge files to see if and how many entries overlap
top_predators <- dplyr::full_join(marine_top_pred, marine_apex_pred)

# Get title
top_predators$TI[53:100]
```

``` 
 [1] "Food provisioning increases the risk of injury in a long-lived marine top predator"                                                                                                   
 [2] "Indirect effects and prey behavior mediate interactions between an endangered prey and recovering predator"                                                                           
 [3] "Cascading effects of predation risk determine how marine predators become terrestrial prey on an oceanic island"                                                                      
 [4] "Variation in the population demographics of Scolopsis bilineatus in response to predators"                                                                                            
 [5] "Towards a balanced presentation and objective interpretation of acoustic and trawl survey data, with specific reference to the eastern Scotian Shelf"                                 
 [6] "Jellyfish as an alternative source of food for opportunistic fishes"                                                                                                                  
 [7] "Southern Elephant Seals Replenish Their Lipid Reserves at Different Rates According to Foraging Habitat"                                                                              
 [8] "Modelling food web structure using an end-to-end approach in the coastal ecosystem of the Gulf of Gabes (Tunisia)"                                                                    
 [9] "Potential role of predators on carbon dynamics of marine ecosystems as assessed by a Bayesian belief network"                                                                         
[10] "Foraging distribution overlap and marine reserve usage amongst sub-Antarctic predators inferred from a multi-species satellite tagging experiment"                                    
[11] "Global population genetic dynamics of a highly migratory, apex predator shark"                                                                                                        
[12] "Trophic ecology of the blue shark (Prionace glauca) based on stable isotopes (delta C-13 and delta N-15) and stomach content"                                                         
[13] "The biotic resistance role of fish predation in fouling communities"                                                                                                                  
[14] "The Seagrass Effect Turned Upside Down Changes the Prospective of Sea Urchin Survival and Landscape Implications"                                                                     
[15] "Seasonal changes in diet and lipid content of northern sand lance Ammodytes dubius on Fyllas Bank, West Greenland"                                                                    
[16] "Diversity of plantonic fish larvae along a latitudinal gradient in the Eastern Atlantic Ocean estimated through DNA barcodes"                                                         
[17] "Some like it hot: effect of environment on population dynamics of a small tropical seabird in the Caribbean region"                                                                   
[18] "Temporal and spatial variation of beaked and sperm whales foraging activity in Hawai'i, as determined with passive acoustics"                                                         
[19] "Determination of Trace Elements in the Melon of Indo-Pacific Humpback Dolphins (Sousa chinensis) with ICP-MS"                                                                         
[20] "Behavioral evidence suggests facultative scavenging by a marine apex predator during a food pulse"                                                                                    
[21] "Guiana Dolphins (Sotalia guianensis) and DR-CALUX for Screening Coastal Brazilian Environments for Dioxins and Related Compounds"                                                     
[22] "Molecular identification and functional characteristics of peptide transporters in the bonnethead shark (Sphyrna tiburo)"                                                             
[23] "Risk assessment and predator learning in a changing world: understanding the impacts of coral reef degradation"                                                                       
[24] "Foundation species identity and trophic complexity affect experimental seagrass communities"                                                                                          
[25] "Predator effects on host-parasite interactions in the eastern oyster Crassostrea virginica"                                                                                           
[26] "Habitat recovery and restoration in aquatic ecosystems: current progress and future challenges"                                                                                       
[27] "Coupling bio-logging with nutritional geometry to reveal novel insights into the foraging behaviour of a plunge-diving marine predator"                                               
[28] "An investigation into ciguatoxin bioaccumulation in sharks"                                                                                                                           
[29] "Brandt's cormorant diet (1994-2012) indicates the importance of fall ocean conditions for northern anchovy in central California"                                                     
[30] "Extreme Inverted Trophic Pyramid of Reef Sharks Supported by Spawning Groupers"                                                                                                       
[31] "Dietary habits of polar bears in Foxe Basin, Canada: possible evidence of a trophic regime shift mediated by a new top predator"                                                      
[32] "Individual personality differences in Port Jackson sharks Heterodontus portusjacksoni"                                                                                                
[33] "Sex-specific and individual preferences for hunting strategies in white sharks"                                                                                                       
[34] "Top-down control as important as nutrient enrichment for eutrophication effects in North Atlantic coastal ecosystems"                                                                 
[35] "Demography of the critically endangered Balearic shearwater: the impact of fisheries and time to extinction"                                                                          
[36] "Cross-seasonal foraging site fidelity of subantarctic fur seals: implications for marine conservation areas"                                                                          
[37] "The mussel path - Using the contaminant tracer, Ecotracer, in Ecopath to model the spread of pollutants in an Arctic marine food web"                                                 
[38] "Variation in ecological interaction strength with island area: theory and data from the Bahamian archipelago"                                                                         
[39] "High levels of mercury and low levels of persistent organic pollutants in a tropical seabird in French Guiana, the Magnificent frigatebird, Fregata magnificens"                      
[40] "Top predators negate the effect of mesopredators on prey physiology"                                                                                                                  
[41] "Persistent Organic Pollutants in albacore tuna (Thunnus alalunga) from Reunion Island (Southwest Indian Ocean) and South Africa in relation to biological and trophic characteristics"
[42] "Intrinsic factors influence the timing of arrival of capelin (Mallotus villosus) to spawning grounds in coastal Newfoundland"                                                         
[43] "First record of plastic debris in the stomach of Mediterranean lanternfishes"                                                                                                         
[44] "Seasonal habitat-based density models for a marine top predator, the harbor porpoise, in a dynamic environment"                                                                       
[45] "Marine reserves lag behind wilderness in the conservation of key functional roles"                                                                                                    
[46] "Variability in the summer diets of juvenile polar cod (Boreogadus saida) in the northeastern Chukchi and western Beaufort Seas"                                                       
[47] "Ultrastructural and Single-Cell-Level Characterization Reveals Metabolic Versatility in a Microbial Eukaryote Community from an Ice-Covered Antarctic Lake"                           
[48] "Processes and pathways of ciguatoxin in aquatic food webs and fish poisoning of seafood consumers"                                                                                    
```

``` r
# Get abstract
abstract <- top_predators$AB[1]

# Get keywords
head(top_predators$DE)
```

    [1] "Top predator; Bioaccumulation; Inorganic elements; Organic contaminants; Stable isotopes; Intrinsic markers" 
    [2] "Eastern Mediterranean Sea; Food web; Ecopath model; Alien species; Fishing"                                  
    [3] "Bioenergetics; Pinnipeds; Agent-based model; Climate change; Fishing pressure"                               
    [4] ""                                                                                                            
    [5] "Microplastics; Food web contamination; Seafood; Ingestion; Review"                                           
    [6] "Biodiversity assessment; Threatening processes; Risk assessment; IUCN Red list of ecosystems; Habitat mosaic"

``` r
# Check if paper is marine
marine_related <- function(x){
  a <- grepl("ocean", x)
  b <- grepl("Ocean", x)
  c <- grepl("sea", x)
  c <- grepl("Sea", x)
  d <- grepl("Marine", x)
  e <- grepl("marine", x)
  f <- grepl("reef", x)
  g <- grepl("intertidal", x)
  if(any(a,b,c,d,e,f,g)){
    z <- TRUE
  }else{
    z <- FALSE
  }
  return(z)
}

marine_abstracts <- sapply(top_predators$AB, FUN=marine_related, USE.NAMES=FALSE)
marine_titles <- sapply(top_predators$TI, FUN=marine_related, USE.NAMES=FALSE)
marine_keywords <- sapply(top_predators$DE, FUN=marine_related, USE.NAMES=FALSE)

x <- mapply(any, marine_abstracts, marine_titles, marine_keywords)
which(x==FALSE)
```

``` 
 [1]   6  12  20  46  50  58  99 115 122 124 168 192 205 237 246 288 309 314 337
[20] 340 344 351 366 379 382 408 410 415 444 452 475
```

``` r
top_predators <- top_predators[which(x == TRUE),]

# Check if paper is topic related
apex_related <- function(x){
  a <- grepl("top", x)
  b <- grepl("predator", x)
  c <- grepl("apex", x)
  if(any(a,b,c)){
    z <- TRUE
  }else{
    z <- FALSE
  }
  return(z)
}

top_predators_abstracts <- sapply(top_predators$AB, FUN=apex_related, USE.NAMES=FALSE)
top_predators_titles <- sapply(top_predators$TI, FUN=apex_related, USE.NAMES=FALSE)
top_predators_keywords <- sapply(top_predators$DE, FUN=apex_related, USE.NAMES=FALSE)

x <- mapply(any, top_predators_abstracts, top_predators_titles, top_predators_keywords)
top_predators <- top_predators[which(x == TRUE),]

# Manually look for marine species within title and abstract

# Marine species mentioned in title
species_ti <- c("albacore tuna", "", "Weddell seal", "Harbor Porpoise", "", "", "Pisaster ochraceus", "African penguin", "Magellanic penguins", "Harbor porpoise", "", "", "Pomatomus saltatrix", "", "Antarctic krill", "Antarctic petrels", "", "", "Mnemiopsis leidyi", "", "", "", "", "", "", "", "Arctocephalus gazella/Antarctic fur seal", "", "", "", "", "Short-tailed shearwater", "", "", "", "", "", "", "", "", "", "", "", "", "", "Lontra felina", "", "", "", "Scolopsis bilineatus", "", "Southern Elephant Seals", "", "", "", "", "Blue shark (Prionace glauca)", "", "", "northern sand lance Ammodytes dubius?", "", "", "Beaked and sperm whales", "Indo-Pacific Humpback Dolphins (Sousa chinensis)", "", "Guiana Dolphins (Sotalia guianensis)", "bonnethead shark (Sphyrna tiburo)", "", "", "Crassostrea virginica?", "", "", "", "northern anchovy", "Spawning Groupers", "Polar bear", "Port Jackson sharks (Heterodontus portusjacksoni)", "White shark", "", "Balearic shearwater", "subantarctic fur seals", "", "", "Magnificent frigatebird (Fregata magnificens)", "", "albacore tuna (Thunnus alalunga)", "Capelin (Mallotus villosus)", "Mediterranean lanternfishes", "Harbor porpoise", "", "Polar cod (Boreogadus saida)", "", "", "Bottlenose dolphins and seabirds", "", "Pissarrachampsa sera?", "", "", "Subantarctic fur seals", "Hake, saithe and Norway pout", "", "", "Leopard seals", "hyperbenthic shrimp (Nauicaris marionis)", "", "Freshwater morays?", "Southern Elephant Seal", "", "dingoes", "Southern Ocean fur seal and albatross", "", "", "", "", "", "African penguin", "", "", "Sea snakes", "", "Gentoo penguins", "Gentoo penguins Pygoscelis papua", "", "", "Orcas", "", "", "Red lionfish Pterois volitans", "Polar bear (Ursus maritimus)", "", "", "", "Longnose gars", "Spiny lobster?", "Harbor seal (Phoca vitulina)", "")

# Marine species mentioned in abstract
species_ab <- c()

# Species names can be extracted from the worrms package!!!
```

## Get species data

### Plot global maps of species data

## Load environmental data

### Plot maps of our environmental variables

## Climatic niche

### Extract environmental data

### Plot climatic niche

## SDM

``` r
library(sdm)

data <- sdmData(species, predictors)
model <- sdm(species ~ pr+tas, data,
             methods=c("glm", "brt", "maxent", "bioclim", ensemble),
             test.percent=30, replicates=100, replicate.method="bootstrapping")
p <- predict(model, predictors)
```
