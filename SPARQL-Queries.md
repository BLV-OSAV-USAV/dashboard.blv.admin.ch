### SPARQL-Queries

Die Anfrage gibt alle einzelnen Tierseuchenmeldungen bei Bienen im Jahr 2025 mit Diagnose- und Publikationsdatum, Ort sowie Krankheit und Krankheitsgruppe (ohne „Alle“) zeilenweise aus.

```
PREFIX schema: <http://schema.org/>
PREFIX cube: <https://cube.link/>
PREFIX disease: <https://agriculture.ld.admin.ch/fsvo/animal-disease/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT
  (?date AS ?publicationDate)
  ?diagnosisDate
  ?canton
  ?town
  ?diseasesGroup
  ?diseases
  ?species
WHERE {
  <https://agriculture.ld.admin.ch/fsvo/animal-disease/observation/> cube:observation ?obs .

  ?obs disease:epidemics/schema:name ?diseases ;
       disease:animal-specie/schema:name ?species ;
       schema:containedInPlace/schema:name ?town ;
       disease:internet-publication ?date ;
       disease:diagnosis-date ?diagnosisDate ;
       disease:canton/schema:alternateName ?canton .

  # Krankheitsgruppe
  ?diseaseIRI schema:name ?diseases ;
              skos:broader/schema:name ?diseasesGroup .

  # species fix auf Bienen
  FILTER (?species = "Bienen"@de)

  # Sprache
  FILTER (LANG(?diseases) = "de")
  FILTER (LANG(?diseasesGroup) = "de")

  # diseasesGroup darf nicht "Alle" sein
  FILTER (?diseasesGroup != "Alle"@de)

  # Jahr 2025 – basierend auf diagnosis-date
  FILTER (YEAR(xsd:dateTime(?diagnosisDate)) = 2025)
}
ORDER BY DESC(?diagnosisDate)
```
