# Experiment 1

## Information gathering using Google

## Aim

To use advanced search operators to collect publicly indexed information and understand how search results help identify an organisation's public exposure.

## Theory

Reconnaissance gathers information about a subject before a security assessment. Open-source intelligence uses publicly available material such as websites, documents and search-engine results. Google-based reconnaissance queries a search engine's index. Opening a result then contacts the destination website and can produce access logs.

Search operators reduce irrelevant results by constraining the domain, file format or location of a keyword. Combining an operator with an exact phrase can make a query more specific. An indexed result may be stale, incomplete or incorrectly described, so its presence alone does not establish a vulnerability. An exposed directory may be intentional. A login page is an entry point to document, rather than evidence of a successful compromise.

| Operator | Purpose |
| --- | --- |
| `site:` | Restrict results to a domain or site scope |
| `filetype:` | Restrict results by file format |
| `intitle:` / `allintitle:` | Match a term or several terms in page titles |
| `inurl:` / `allinurl:` | Match terms in URLs |
| `intext:` / `allintext:` | Match terms in indexed body text |
| `"exact phrase"` | Request an exact phrase |

## Requirements

A browser, internet access and an agreed scope of public information to examine.

## Queries extracted from the PDF

These are search-box queries, not shell commands. They are transcribed from PDF pages 5–9. Typographic quotation marks have been normalised to ASCII quotes. Wildcard forms are retained as source examples and should be checked against the results actually returned.

```text
site:wikipedia.org artificial intelligence
site:github.com inurl:login
site:*.gov.in filetype:pdf
site:nasa.gov filetype:pdf
filetype:xls "budget"
intitle:"index of" "parent directory"
allintitle:annual report 2025
intitle:"login page" site:*.edu
inurl:admin site:*.in
allinurl:wp-content uploads
inurl:php?id=
site:*.gov filetype:pdf intext:"budget"
allintext:username password filetype:log
intext:"confidential" filetype:pdf
```

## Procedure

1. Choose a permitted domain and record the purpose of the search.
2. Run a broad keyword query, then add `site:` and compare relevance.
3. Apply a file-format operator and inspect the returned document types.
4. Compare title, URL and body-text operators using the same topic.
5. Record the query, date, result title, URL and relevance. Treat result counts as approximate.
6. Describe possible exposure and appropriate controls, such as access control, removal of unintended public files and restricted directory listings.

## Observations and result

More specific queries should reduce unrelated results, although an operator can also return no matches. The exercise demonstrates how indexed information supports passive reconnaissance. Record actual findings rather than assuming that the example queries return the same results on every date.

## Conclusion

Advanced search operators provide a systematic way to organise publicly indexed information and identify an organisation's visible online presence. The exercise connects focused searches with the documentation of possible information exposure. Findings must be verified in context because an indexed page or document alone does not establish a security weakness.

## Viva questions

- How does querying a search engine differ from scanning a host?
- What is the difference between `intitle:` and `intext:`?
- Why does a public search result require verification before it is reported as a security issue?

## Sources

`COM311_Exp1_InformationGathering.pdf`, PDF pages 2–9. Page references use the PDF page position. Existing practical evidence is available in [Experiment1.pdf](Experiment1.pdf).
