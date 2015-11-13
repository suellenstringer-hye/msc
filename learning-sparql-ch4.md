##Learning SPARQL Chapter 4 - 
#Copying, Creating and Converting Data (and Finding Bad Data)

Suellen Stringer-Hye 2015-11-02

##Query Forms : SELECT, DESCRIBE, ASK and CONSTRUCT

##Copying Data

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
````

##Creating New Data
````
# filename: ex184.rq

PREFIX dc: <http://purl.org/dc/elements/1.1/>
CONSTRUCT
{
  <http://learningsparql.com/ns/data/book312> dc:title "Jabez Easterbrook" . 
}
WHERE
{}
```

```
# filename: ex185.rq

PREFIX ab: <http://learningsparql.com/ns/addressbook#> 

CONSTRUCT
{
  ?person ?p ?o ;
          ab:areaCode ?areaCode . 
}
WHERE
{
  ?person ab:homeTel ?phone ;
          ?p ?o . 
  BIND (SUBSTR(?phone,2,3) as ?areaCode)
}
```



##Converting Data
```
# filename: ex194.rq

PREFIX ab: <http://learningsparql.com/ns/addressbook#> 
PREFIX v:  <http://www.w3.org/2006/vcard/ns#>

CONSTRUCT
{
 ?s v:given-name  ?firstName ;
    v:family-name ?lastName ;
    v:email       ?email ;
    v:homeTel     ?homeTel . 
}
WHERE
{
 ?s ab:firstName ?firstName ;
    ab:lastName  ?lastName ;
    ab:email     ?email .
    OPTIONAL 
    { ?s ab:homeTel ?homeTel . }
}
```

```
# filename: ex196.rq

PREFIX cat:  <http://dbpedia.org/resource/Category:>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX gp:   <http://wifo5-04.informatik.uni-mannheim.de/gutendata/resource/people/>
PREFIX owl:  <http://www.w3.org/2002/07/owl#>
PREFIX rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX d:    <http://learningsparql.com/ns/data#>
CONSTRUCT
{  
  d:HockingJoseph ?dbpProperty ?dbpValue ;
                  ?gutenProperty ?gutenValue .

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
##Finding Bad Data

###Defining Rules with SPARQL
```
# filename: ex199.rq

PREFIX dm: <http://learningsparql.com/ns/demo#> 

ASK WHERE
{
  ?s dm:location ?city .
  FILTER (!(isURI(?city)))
}
```
```
# filename: ex201.rq

PREFIX dm:  <http://learningsparql.com/ns/demo#> 
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

ASK WHERE
{
  ?item dm:amount ?amount .
  FILTER ((datatype(?amount)) != xsd:integer) 
}
```
```
# filename: ex202.rq

PREFIX dm:  <http://learningsparql.com/ns/demo#> 

ASK WHERE
{
  ?item dm:cost ?cost ;
        dm:amount ?amount .
  OPTIONAL 
  {
    ?item dm:approval ?approvingEmployee . 
    ?approvingEmployee dm:jobGrade ?grade . 
  }

  BIND (?cost * ?amount AS ?totalCost) .
  FILTER ((?totalCost > 100) &&
          ( (!(bound(?grade)) || (?grade < 5 ) )))   
}
```

###Generating Data About Broken Rules
```
# filename: ex203.rq

PREFIX dm: <http://learningsparql.com/ns/demo#> 
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

CONSTRUCT
{
  ?s dm:problem dm:prob29 .
  dm:prob29 rdfs:label "Location value must be a URI." . 
}

WHERE
{
  ?s dm:location ?city .
  FILTER (!(isURI(?city)))
}
```
```
# filename: ex205.rq

PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX dm:  <http://learningsparql.com/ns/demo#> 
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

CONSTRUCT
{
  ?item dm:problem dm:prob32 .
  dm:prob32 rdfs:label "Amount must be an integer." . 
}

WHERE
{
  ?item dm:amount ?amount .
  FILTER ((datatype(?amount)) != xsd:integer) 
}
```
```
# filename: ex207.rq

PREFIX dm:  <http://learningsparql.com/ns/demo#> 
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

CONSTRUCT
{
  ?item dm:problem dm:prob44 .
  dm:prob44 rdfs:label "Expenditures over 100 require grade 5 approval." . 
}

WHERE
{
  ?item dm:cost ?cost ;
        dm:amount ?amount .
  OPTIONAL 
  {
    ?item dm:approval ?approvingEmployee . 
    ?approvingEmployee dm:jobGrade ?grade . 
  }

  BIND (?cost * ?amount AS ?totalCost) .
  FILTER ((?totalCost > 100) &&
          ( (!(bound(?grade)) || (?grade < 5 ) )))   
}
```

####Using Existing SPARQL Rules Vocabularies
```
# filename: ex211.rq

PREFIX sch: <http://purl.org/net/schemarama#>
PREFIX dm:  <http://learningsparql.com/ns/demo#> 
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

CONSTRUCT
{
  [] rdf:type sch:Error;
        sch:message "location value should be a URI";
        sch:implicated ?s.

} 
WHERE
{
  ?s dm:location ?city .
  FILTER (!(isURI(?city)))
}
```
```
# filename: ex212.rq

PREFIX spin: <http://spinrdf.org/spin#> 
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#> 
PREFIX dm:   <http://learningsparql.com/ns/demo#> 

CONSTRUCT
{
    _:b0 a spin:ConstraintViolation .
    _:b0 rdfs:comment "Location value must be a URI" .
    _:b0 spin:violationRoot ?this .

}
WHERE
{
    ?this dm:location ?city .
    FILTER (!isURI(?city)) .
}
```

##Asking for a Description of a Resource
```
# filename: ex213.rq

DESCRIBE  <http://learningsparql.com/ns/data#course59>

```
```
# filename: ex215.rq

DESCRIBE <http://dbpedia.org/resource/Joseph_Hocking>
```
```
# filename: ex216.rq

PREFIX d:  <http://learningsparql.com/ns/data#>
PREFIX ab: <http://learningsparql.com/ns/addressbook#>

DESCRIBE ?course WHERE
{ d:i0432 ab:takingCourse ?course . }

```
