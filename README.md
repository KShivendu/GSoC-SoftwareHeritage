# GSoC'21 @ Software Heritage
Report for Google Summer of Code 21 Project @ Software Heritage

| Project Details|  |
|---	|---	|
|Initial Proposal|  [Advanced search features for the SWH Archive](https://docs.google.com/document/d/1pEpm7eHnTO3feNmCShuWkOCVFzo-9l2dMrja_OdjyO4/edit)|
|Repository|[swh-search](https://forge.softwareheritage.org/source/swh-search/) and [swh-web](https://forge.softwareheritage.org/source/swh-web/)|
|Mentors| [Valentin Lorentz](https://github.com/Progval) and [Vincent Sellier](https://github.com/vsellier)|
|Contributions| [swh-search](https://github.com/SoftwareHeritage/swh-search/commits?author=KShivendu) and [swh-web](https://github.com/SoftwareHeritage/swh-web/commits?author=KShivendu)|
|Documentation|[Search query language syntax](https://docs.softwareheritage.org/devel/swh-search/query-language.html)|
|Duration| 3m 7d (17 May'21 - 23 Aug'21)|

### About Software Heritage:
[Software Heritage](https://www.softwareheritage.org/) is on a mission to collect, preserve, and share all the publicly available software with its source code and development history. The archive periodically crawls GitHub, GitLab, Debian, PyPI, etc. It has preserved more than 11 billion unique source code files with 2.3 billion commits spanning more than 163 million software projects.

The archive has a search feature to find repositories based on the repository URL or the metadata. This metadata includes the package name, version, description, license, etc. My GSoC project was all about improving the archive search. (back-end as well front-end)

### TLDR:
I made the [Archive search](http://archive.softwareheritage.org/) more expressive with the help of advanced search features like filters, sorting options, search query language (custom DSL) with autocomplete features (was optional)

Tasks completed:
- Introduced new fields in the search service (based on Elasticsearch), ingested data from other swh services through their RPC APIs or through the journal service (Kafka), and built filters/sorting features.
- Designed a grammar, built a parser and a translator (using TreeSitter) that traverses the AST to translate the DSL queries into Elasticsearch queries.
- Implemented autocomplete features for the query language in the Web UI. (almost completed, needs some improvements before merging)

### Technical overview and diffs:
- Ingested the following fields into Elasticsearch from the journal service(Kafka) or from other swh services:
  - `nb_visits`: Number of visits of an origin ([D5824](https://forge.softwareheritage.org/D5824))
  - `last_visit_date`: Last visit date ([D5824](https://forge.softwareheritage.org/D5824))
  - `last_eventful_visit_date`: Last visit date when the snapshot id changed (i.e. change in the content of the repo) ([D5878](https://forge.softwareheritage.org/D5878))
  - `last_revision_date`: Last revision (commit) date ([D5883](https://forge.softwareheritage.org/D5883))
  - `last_release_date`: Last release date (based on tags) ([D5883](https://forge.softwareheritage.org/D5883))

- Introduced `sort_by`, `limit` in origin search ([D5918](https://forge.softwareheritage.org/D5918))
- Added support for searching `license`, `programmingLanguage`, and `keywords` (repo tags + README/description) from the repository metadata ([D5949](https://forge.softwareheritage.org/D5949), [D5963](https://forge.softwareheritage.org/D5963))
- Added support for searching `date_created`, `date_modified`, and `date_published` from the repository metadata ([D5964](https://forge.softwareheritage.org/D5964))
- Implemented a [search query language](https://docs.softwareheritage.org/devel/swh-search/query-language.html) using [TreeSitter](https://tree-sitter.github.io/tree-sitter/). ([D5990](https://forge.softwareheritage.org/D5990), [D6005](https://forge.softwareheritage.org/D6005))
  
  The search query language has parsers (compiled as .so and .wasm files) that serve two different purposes:
  - Translation of search queries into the elasticsearch DSL in swh.search (or any
  other search backend that we may use in the future) ([D6025](https://forge.softwareheritage.org/D6025), [D6046](https://forge.softwareheritage.org/D6046))
  - Autocompletion of the queries in the swh.web (Archive UI) ([D6091](https://forge.softwareheritage.org/D6091)) (Needs some UX improvements before merging) 

### Future aspects:
- Making the autocomplete suggestions dynamic using data from other swh services.
- Fixing the swh-indexers to mine repository data (currently some of the filters work on the basis of metadata which can be wrong or unavailable)
- Improving the search UI to support the search DSL (Advanced mode) as well as UI components (Basic mode). (Similar to [Jira query language](https://issues.apache.org/jira/browse/ZOOKEEPER-4345?jql=))


Most of my changes will **take some time before they enter production**.
They will go live at the [Software Heritage archive](http://archive.softwareheritage.org/) very soon.
