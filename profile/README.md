<h1>
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/openfacility/.github/main/assets/openfacility-logo-white.png">
    <img src="https://raw.githubusercontent.com/openfacility/.github/main/assets/openfacility-logo-navy.png" alt="" height="72" align="middle">
  </picture>
  &nbsp;
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/openfacility/.github/main/assets/openfacility-wordmark-white.png">
    <img src="https://raw.githubusercontent.com/openfacility/.github/main/assets/openfacility-wordmark-navy.png" alt="OpenFacility" height="44" align="middle">
  </picture>
</h1>

[![Discord](https://img.shields.io/badge/Discord-join%20the%20chat-5865F2?logo=discord&logoColor=white)](https://discord.gg/TW3TdZkS4z)

**A knowledge graph model for research facilities.** OpenFacility is a schema.org application profile for any research facility: its instruments, projects, sessions and schedules, features of interest, datasets and publications, the people and organizations around them, and the sources they come from. It comes with SHACL validation rules, a store layout and the tools to build and check the data.

It names no scientific domain and no facility. It is built in layers:

- **core**: the generic model, shapes, mapping patterns and tools;
- **profiles**: the wording and agreed values of a community, or of a concern several communities share (e.g. scholarly publishing);
- **facility applications**: one facility's IRIs, source systems and data. Several facilities can share one store.

```mermaid
%%{init: {"flowchart": {"nodeSpacing": 30, "rankSpacing": 45, "padding": 8}}}%%
flowchart RL
  fac["<b>Facility application</b><br/>IRIs · source systems · data"]
  sub["<b>Profile</b><br/>refines another profile"]
  comm["<b>Profile</b><br/>e.g. a community"]
  schol["<b>Profile</b><br/>e.g. scholarly"]
  core["<b>OpenFacility core</b><br/>model · shapes · tools"]
  shared[("<b>Shared data</b><br/>places · organizations")]

  fac -- follows --> sub
  fac -- follows --> schol
  sub -- isProfileOf --> comm
  comm -- isProfileOf --> core
  schol -- isProfileOf --> core
  fac -- references --> shared
  shared -- built on --> core

  classDef core fill:#0f3a6e,stroke:#0f3a6e,color:#ffffff
  classDef profile fill:#e3eefa,stroke:#3b78c4,color:#1f2937
  classDef facility fill:#fdf0dc,stroke:#d99a2b,color:#1f2937
  classDef shared fill:#e3f4ea,stroke:#3a9a63,color:#1f2937
  class core core
  class sub,comm,schol profile
  class fac facility
  class shared shared
```

Arrows point to what a layer uses, and dependencies go one way only: a facility uses its profiles and the core, a profile uses its parents and the core, the core uses nothing. Profiles form a graph without cycles (W3C DXPROF `prof:isProfileOf`), not a tree. Every source is a `schema:DataCatalog`, and everything a source says about a thing is in that thing's record in the catalogue.

Everything is [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/).

## Where we are

OpenFacility is at its first shared iteration: the model is public and still moving.

```mermaid
timeline
  2026/11 : First shared iteration of OpenFacility on GitHub
```

Want to shape what comes next? [Join us on Discord](https://discord.gg/TW3TdZkS4z).

## The model in two graphs

Each box is one node: its role, its [schema.org](https://schema.org) type and an example IRI. The ids are placeholders from the [core examples](https://github.com/openfacility/openfacility-core/tree/HEAD/mapping/examples); `ex:` stands for `https://w3id.org/example/EXAMPLE-`.

### A facility: from request to data

```mermaid
%%{init: {"flowchart": {"nodeSpacing": 18, "rankSpacing": 28, "padding": 6}}}%%
flowchart TB
  prop["<b>Project</b><br/>schema:ResearchProject<br/>ex:USEROFFICE-PROP-0001"]
  req["<b>Access request</b><br/>schema:ReserveAction<br/>ex:USEROFFICE-REQ-0001"]
  ses["<b>Scheduled session</b><br/>schema:ScheduleAction<br/>ex:USEROFFICE-SCH-0001"]
  exp["<b>Experiment</b><br/>schema:AssessAction<br/>ex:DATACAT-ES-141415"]
  smp["<b>Feature of interest</b><br/>schema:Product<br/>ex:DATACAT-SAMPLE-<br/>141415-0001"]
  grant["<b>Grant</b><br/>schema:Grant<br/>ex:GRANT-XX-0001"]
  ins["<b>Instrument</b><br/>schema:IndividualProduct<br/>doi:10.0000/<br/>example-instr-0001"]
  ds["<b>Dataset</b><br/>schema:Dataset<br/>doi:10.0000/<br/>example-session-dataset-141415"]
  ali["<b>Aliases, groups</b><br/>schema:ProductGroup<br/>ex:LIB-INSTR-XX2"]
  role["<b>Role, affiliation</b><br/>schema:OrganizationRole<br/>blank node, no @id"]
  per["<b>Person</b><br/>schema:Person<br/>ex:USEROFFICE-USER-0001"]
  org["<b>Organization</b><br/>schema:Organization<br/>ror:000000000"]
  pla["<b>Place</b><br/>schema:City, schema:Country<br/>geonames:2988507"]

  req -- result --> ses -- result --> exp
  exp -- object --> smp
  exp -- result --> ds
  exp -- instrument --> ins
  req -- agent --> prop
  prop ~~~ req
  prop -- funding --> grant
  ds -- funding --> grant
  ali -- sameAs --> ins
  prop -- member --> role
  role -- creator --> per
  role -- owner --> org
  org -- location --> pla

  classDef session fill:#fde8e4,stroke:#d9573f,color:#1f2937
  classDef project fill:#fdf0dc,stroke:#d99a2b,color:#1f2937
  classDef instrument fill:#eceef2,stroke:#6b7280,color:#1f2937
  classDef dataset fill:#efe7f7,stroke:#8b5cc4,color:#1f2937
  classDef agent fill:#ffffff,stroke:#1e3a5f,color:#1f2937
  classDef place fill:#e3f4ea,stroke:#3a9a63,color:#1f2937
  class req,ses,exp,smp session
  class prop,grant project
  class ins,ali instrument
  class ds dataset
  class role,per,org agent
  class pla place
```

Colours: red, sessions and features of interest · orange, projects and grants · grey, instruments · purple, datasets · white, people and organizations · green, places.

### A publication and what it links to

This graph adds the [scholarly profile](https://github.com/openfacility/openfacility-profile-scholarly) to the core.

```mermaid
%%{init: {"flowchart": {"nodeSpacing": 18, "rankSpacing": 28, "padding": 6}}}%%
flowchart LR
  pub["<b>Publication</b><br/>schema:ScholarlyArticle<br/>doi:10.0000/<br/>example-article-0001"]
  evt["<b>Book or event</b><br/>schema:PublicationEvent<br/>doi:10.0000/<br/>example-event-0001"]
  jou["<b>Journal</b><br/>schema:Periodical<br/>issnl:0000-0000"]
  idf["<b>Identifiers</b><br/>schema:PropertyValue<br/>blank node, no @id"]
  grant["<b>Grant</b><br/>schema:Grant<br/>ex:GRANT-XX-0010"]
  ali["<b>Library alias</b><br/>schema:IndividualProduct<br/>ex:LIB-INSTR-XX2"]
  ins["<b>Instrument</b><br/>schema:IndividualProduct<br/>doi:10.0000/<br/>example-instr-0001"]
  ds["<b>Dataset</b><br/>schema:Dataset<br/>doi:10.0000/<br/>example-session-dataset-141415"]
  arole["<b>Author role</b><br/>schema:OrganizationRole<br/>blank node, no @id"]
  aut["<b>Author</b><br/>schema:Person<br/>openalex:A0000000000"]
  aff["<b>Affiliation</b><br/>schema:Organization<br/>blank node, no @id"]
  inst["<b>Institution</b><br/>schema:Organization<br/>ror:000000001"]
  city["<b>City</b><br/>schema:City<br/>geonames:3143244"]

  pub -- isPartOf --> evt
  pub -- isPartOf --> jou
  pub -- identifier --> idf
  pub -- funding --> grant
  pub -- about --> ali
  pub -- about --> ins
  ali -- sameAs --> ins
  pub -- citation --> ds
  pub -- creator --> arole
  arole -- creator --> aut
  arole -- owner --> aff
  aff -- parentOrganization --> inst
  aff -- location --> city

  classDef publication fill:#e3eefa,stroke:#3b78c4,color:#1f2937
  classDef main fill:#0f3a6e,stroke:#0f3a6e,color:#ffffff
  classDef project fill:#fdf0dc,stroke:#d99a2b,color:#1f2937
  classDef instrument fill:#eceef2,stroke:#6b7280,color:#1f2937
  classDef dataset fill:#efe7f7,stroke:#8b5cc4,color:#1f2937
  classDef place fill:#e3f4ea,stroke:#3a9a63,color:#1f2937
  class pub main
  class evt,jou,idf,arole,aut,aff,inst publication
  class grant project
  class ali,ins instrument
  class ds dataset
  class city place
```

Colours: blue, the publication and its metadata · orange, grants · grey, instruments · purple, datasets · green, places.

## Repositories

| Repository | What |
|---|---|
| [openfacility-core](https://github.com/openfacility/openfacility-core) | the model: query profile, SHACL shapes, mapping docs with validated examples, build and validation tools. **Start here.** |
| [openfacility-core-alignment](https://github.com/openfacility/openfacility-core-alignment) | the alignment of the model with W3C PROV-O and SOSA/SSN, and its checks |
| [openfacility-core-data-places](https://github.com/openfacility/openfacility-core-data-places) | shared source `places`: cities, administrative areas and countries (GeoNames, Wikidata) |
| [openfacility-core-data-organizations](https://github.com/openfacility/openfacility-core-data-organizations) | shared source `organizations`: ROR records |
| [openfacility-profile-scholarly](https://github.com/openfacility/openfacility-profile-scholarly) | profile for scholarly publishing: articles, journals, conferences, publishers, citation and journal metrics |

## Getting started

The projects are checked out side by side in one workspace folder and find each other there.

```bash
git clone https://github.com/openfacility/openfacility-core
git clone https://github.com/openfacility/openfacility-core-data-places
git clone https://github.com/openfacility/openfacility-core-data-organizations
pip install -r openfacility-core/scripts/requirements.txt
```

Then read [openfacility-core/README.md](https://github.com/openfacility/openfacility-core#readme) and the [mapping docs](https://github.com/openfacility/openfacility-core/tree/HEAD/mapping).
