### SPARQL-Queries

Die Anfrage gibt alle einzelnen Tierseuchenmeldungen bei Bienen im Jahr 2025 mit Diagnose- und Publikationsdatum, Ort sowie Krankheit und Krankheitsgruppe (ohne „Alle“) zeilenweise aus. -> 
[Direkt abfragen](https://lindas.admin.ch/sparql/#query=PREFIX%20schema%3A%20%3Chttp%3A%2F%2Fschema.org%2F%3E%0APREFIX%20cube%3A%20%3Chttps%3A%2F%2Fcube.link%2F%3E%0APREFIX%20disease%3A%20%3Chttps%3A%2F%2Fagriculture.ld.admin.ch%2Ffsvo%2Fanimal-disease%2F%3E%0APREFIX%20skos%3A%20%3Chttp%3A%2F%2Fwww.w3.org%2F2004%2F02%2Fskos%2Fcore%23%3E%0APREFIX%20xsd%3A%20%3Chttp%3A%2F%2Fwww.w3.org%2F2001%2FXMLSchema%23%3E%0A%0ASELECT%0A%20%20(%3Fdate%20AS%20%3FpublicationDate)%0A%20%20%3FdiagnosisDate%0A%20%20%3Fcanton%0A%20%20%3Ftown%0A%20%20%3FdiseasesGroup%0A%20%20%3Fdiseases%0A%20%20%3Fspecies%0AWHERE%20%7B%0A%20%20%3Chttps%3A%2F%2Fagriculture.ld.admin.ch%2Ffsvo%2Fanimal-disease%2Fobservation%2F%3E%20cube%3Aobservation%20%3Fobs%20.%0A%0A%20%20%3Fobs%20disease%3Aepidemics%2Fschema%3Aname%20%3Fdiseases%20%3B%0A%20%20%20%20%20%20%20disease%3Aanimal-specie%2Fschema%3Aname%20%3Fspecies%20%3B%0A%20%20%20%20%20%20%20schema%3AcontainedInPlace%2Fschema%3Aname%20%3Ftown%20%3B%0A%20%20%20%20%20%20%20disease%3Ainternet-publication%20%3Fdate%20%3B%0A%20%20%20%20%20%20%20disease%3Adiagnosis-date%20%3FdiagnosisDate%20%3B%0A%20%20%20%20%20%20%20disease%3Acanton%2Fschema%3AalternateName%20%3Fcanton%20.%0A%0A%20%20%23%20Krankheitsgruppe%0A%20%20%3FdiseaseIRI%20schema%3Aname%20%3Fdiseases%20%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20skos%3Abroader%2Fschema%3Aname%20%3FdiseasesGroup%20.%0A%0A%20%20%23%20species%20fix%20auf%20Bienen%0A%20%20FILTER%20(%3Fspecies%20%3D%20%22Bienen%22%40de)%0A%0A%20%20%23%20Sprache%0A%20%20FILTER%20(LANG(%3Fdiseases)%20%3D%20%22de%22)%0A%20%20FILTER%20(LANG(%3FdiseasesGroup)%20%3D%20%22de%22)%0A%0A%20%20%23%20diseasesGroup%20darf%20nicht%20%22Alle%22%20sein%0A%20%20FILTER%20(%3FdiseasesGroup%20!%3D%20%22Alle%22%40de)%0A%0A%20%20%23%20Jahr%202025%20%E2%80%93%20basierend%20auf%20diagnosis-date%0A%20%20FILTER%20(YEAR(xsd%3AdateTime(%3FdiagnosisDate))%20%3D%202025)%0A%7D%0AORDER%20BY%20DESC(%3FdiagnosisDate)%0A&endpoint=https%3A%2F%2Flindas.admin.ch%2Fquery&requestMethod=POST&tabTitle=Tierseuche&headers=%7B%7D&contentTypeConstruct=text%2Fturtle&contentTypeSelect=application%2Fsparql-results%2Bjson&outputFormat=table)

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
