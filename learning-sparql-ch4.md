#Learning SPARQL Chapter 4 - 
##Copying, Creating and Converting Data (and Finding Bad Data)

Suellen Stringer-Hye 2015-11-02

## Background

##Query Forms : SELECT, DESCRIBE, ASK and CONSTRUCT

###SELECT
```
# filename: ex174.rq

PREFIX ab: <http://learningsparql.com/ns/addressbook#>
PREFIX d:  <http://learningsparql.com/ns/data#>

SELECT ?person ?p ?o
WHERE 
{
  ?person ab:firstName "Craig" ;
          ab:lastName  "Ellis" ;
          ?p ?o . 
}

```
###CONSTRUCT
```
# filename: ex176.rq

PREFIX ab: <http://learningsparql.com/ns/addressbook#>
PREFIX d:  <http://learningsparql.com/ns/data#>

CONSTRUCT
{ ?person ?p ?o . }

WHERE
{
  ?person ab:firstName "Craig" ;
          ab:lastName  "Ellis" ;
          ?p ?o . 
}

```
#####CONSTRUCT With WHERE
```
# filename: ex178.rq

PREFIX cat:  <http://dbpedia.org/resource/Category:>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX gp:   <http://wifo5-04.informatik.uni-mannheim.de/gutendata/resource/people/>
PREFIX owl:  <http://www.w3.org/2002/07/owl#>
PREFIX rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>

CONSTRUCT
{  
  <http://dbpedia.org/resource/Joseph_Hocking> ?dbpProperty ?dbpValue .
  gp:Hocking_Joseph ?gutenProperty ?gutenValue .
}


WHERE
{
  SERVICE <http://DBpedia.org/sparql>
  {
    <http://dbpedia.org/resource/Joseph_Hocking> ?dbpProperty ?dbpValue .
  }

  SERVICE <http://wifo5-04.informatik.uni-mannheim.de/gutendata/sparql>
  {
    gp:Hocking_Joseph ?gutenProperty ?gutenValue . 
  }

}
```
##Copying Data
##Creating New Data
##Converting Data
##Finding Bad Data


```
<http://herbarium.unc.edu/image/089765> dsw:derivedFrom <http://bioimages.vanderbilt.edu/specimen/ncu592804>.

<http://bioimages.vanderbilt.edu/specimen/ncu
592804> dsw:derivedFrom <http://bioimages.vanderbilt.edu/uncg/39>.
```

It's doing a simple string comparison without any understanding of calendars, time zones, etc.

Datatyped dates are awesome if you are willing to go to the trouble to be careful with them and annoying of you don't care.
