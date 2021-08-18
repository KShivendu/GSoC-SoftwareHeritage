# GSoC'21 @ Software Heritage
Report for Google Summer of Code 21 Project @ Software Heritage

| Project Details|  |
|---	|---	|
|Initial Proposal|  [Improve the Archive Search](https://docs.google.com/document/d/1pEpm7eHnTO3feNmCShuWkOCVFzo-9l2dMrja_OdjyO4/edit)|
|Repository|[swh-search](https://forge.softwareheritage.org/source/swh-search/) [swh-web](https://forge.softwareheritage.org/source/swh-web/)|
|Documentation|[Search query langauge syntax](https://docs.softwareheritage.org/devel/swh-search/query-language.html)|
|Commits| [swh-search (14)](https://github.com/SoftwareHeritage/swh-search/commits?author=KShivendu) [swh-web (4)](https://github.com/SoftwareHeritage/swh-web/commits?author=KShivendu)|
|Duration| 3m 7d (17 May'21 - 23 Aug'21)|
|Lines changed| [swh-search (+3,366 -404)](https://github.com/SoftwareHeritage/swh-search/graphs/contributors) [swh-web (+161, -20)](https://github.com/SoftwareHeritage/swh-web/graphs/contributors) |

### TLDR:
I've to make the [Archive search](http://archive.softwareheritage.org/) more expressive with the help of advanced search features like filters, sorting options, search query language (custom DSL) with autocomplete features (optional)

Goals:
- Introduce new fields in the search service (Elasticsearch), ingest data from other swh services through their gRPC APIs or through the journal service (Kafka), and create filters/sorting features.
- Design a grammar, build a parser using TreeSitter and traverse the AST to directly translate the queries into Elasticsearch queries.
- Implement autocomplete features for the query language in the web UI (optional, but I was able to complete it)

### Technical Overview:
- Ingest the following fields into Elasticsearch from the journal service(kafka) or from other swh services:
  - `nb_visits`: Number of visits of an origin
  - `last_visit_date`: Last visit date
  - `last_eventful_visit_date`: Last visit date when the snapshot id changed (i.e. change in content of the repo)
  - `last_revision_date`: Last revision (commit) date
  - `last_release_date`: Last release date (based on tags)

- Add support for `sort_by`, `limit` in origin search
- Add support for searching `license`, `programmingLangauge`, and `keywords` (repo tags + README/description) from repository metadata
- Add support for searching `date_{created,modified,published}` from repository metadata
- Implement a [search query langauge](https://docs.softwareheritage.org/devel/swh-search/query-language.html) using [TreeSitter](https://tree-sitter.github.io/tree-sitter/).
  The parsers generated from the proposed grammar serve two different purposes:
  - Translation of search queries into the elasticsearch DSL in swh.search (or any
  other search backend that we may use in the future)
  - Autocompletion of the queries in the swh.web (Archive UI)
  
Some of my recent changes like autocomplete will **take some time before they enter production**.

They will go live at the [Software Heritage Archive](http://archive.softwareheritage.org/)