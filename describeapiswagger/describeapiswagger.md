# OpenAPI: Descrivere un API con Swagger

### + 200 pt

## Introduzione

### ~ 5 min

La specifica OpenAPI definisce uno standard per descrivere, consultare e produrre servizi RESTful, ovvero applicazioni che espongono una o più API.
Swagger invece può essere visto come un'implementazione della specifica, e consiste in una collezione di tool e strumenti software utili allo scopo, perlopiù open source.

:fire: _[Cos'è una REST API](https://searchapparchitecture.techtarget.com/definition/RESTful-API)

:fire: _[Che cosa sono OpenAPI e Swagger](https://swagger.io/specification/)

In questo breve tutorial vedremo come descrivere una REST API partendo da zero, utilizzando alcuni dei tool offerti da Swagger:

- [Editor](http://editor.swagger.io)
- [Codegen](https://github.com/swagger-api/swagger-codegen)

Per evitare di essere troppo ripetitivi, in certi casi useremo il termine "servizio" come sinonimo di API.

#### Di cosa avremo bisogno

- Il nostro browser Web preferito

## Descriviamo la nostra User API

### ~ 15 min

Creiamo ora una semplice descrizione di un API, utilizzando Swagger Editor

#### Che cosa realizzeremo

In questo tutorial descriveremo un API che effettuerà operazioni basilari sugli utenti (lettura, scrittura, modifica e cancellazione), che chiameremo per comodità User API.
Rappresenteremo gli utenti come semplici oggetti JSON:
```
{
  "id": "integer",
  "email": "string",
  "username": "string",
  "firstName": "string",
  "lastName": "string"
}
```
:fire: _JavaScript Object Notation ovverosia [**JSON**](https://en.wikipedia.org/wiki/JSON)

#### Creiamo la struttura di base

Per prima cosa accediamo all'editor online di Swagger: [Editor](http://editor.swagger.io), noterete subito che la schermata è suddivisa verticalmente in due parti.
A sinistra vi è l'editor di testo in formato YAML, a destra invece il risultato della generazione a partire dalla nostra definizione.

:fire: _[YAML](http://yaml.org/)

Cancelliamo l'esempio presente sull'editor YAML (dovrebbe essere la Petstore API), e incolliamo il testo seguente:
```
swagger: "2.0"
info:
  description: "This is our Moovacademy User API!"
  version: "1.0.0"
  title: "User API"
host: "localhost:8080"
basePath: "/moovacademy"
schemes:
- "http"

paths:
  /user/{username}:
    get:
      tags:
        - "user"
      summary: "Find an user by username"
      operationId: "findUserByUsername"
      produces:
      - "application/json"
      parameters:
      - name: username
        in: path
        description: "The username to search"
        required: true
        type: string
      responses:
        200:
          description: "successful operation"
          schema:
            $ref: '#/definitions/User'
        404:
          description: "not found"
          schema:
            $ref: '#/definitions/Error'

definitions:
  User:
    type: object
    properties:
      id:
        type: string
      email:
        type: string  
      username:
        type: string
      firstName:
        type: string
      lastName:
        type: string
  Error:
    type: object
    properties:
      code:
        type: string
      detail:
        type: string
```

Sulla schermata destra dello Swagger Editor dovrebbe essere apparsa la nostra nuova User API!

Allo stato attuale stiamo descrivendo un servizio molto semplice, in grado di gestire solo un operazione di lettura HTTP GET da noi definita come `findUserByUsername`.

:fire: _[Metodi di richiesta HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods)

Questo metodo, come suggerisce il nome, ci permette di trovare un utente in base ad un username arbitrario.
Se l'utente viene trovato la risposta avrà codice `200` e verrà restituito l'oggetto User in formato JSON, in caso contrario il codice sarà `404` e l'oggetto Error.

:fire: _[Codici di stato HTTP](https://it.wikipedia.org/wiki/Codici_di_stato_HTTP)

Supponendo dunque di avere una webapp in ascolto sulla nostra macchina generata a partire dal nostro swagger, con la seguente chiamata dovremmo ottenere una risposta:
```
http://localhost:8080/moovacademy/user/moovadmin
```
La stessa identica chiamata può essere lanciata dal nostro swagger: difatti **protocollo**, **host** e **basepath** sono già tutti definiti nel modello.
Per farlo, spostiamoci dall'editor al nostro swagger generato, e clicchiamo sull'unico metodo disponibile sotto il tag `user`, ovvero la chiamata GET.
<figura1>
Si aprirà una tendina, dove noterete il parametro e le risposte che abbiamo definito nell'editor. Cliccate su `Try it out`.
<figura2>
Una volta fatto, si sbloccherà il form per inserire l'username. Aggiungete `moovadmin`.
<figura3>
Cliccate su `Execute`. Dopo un breve caricamento la chiamata CURL giustamente fallirà, ma noterete che il request url combacierà con quello di partenza.

:fire: _[cos'è CURL](http://www.curl.com/)
<figura4>

#### Aggiungiamo altri metodi

Abbiamo descritto la nostra prima GET per recuperare un utente, ora passiamo alla creazione.
Aggiungiamo dunque al nostro swagger un metodo POST in grado di creare un nuovo utente.
Per farlo, copiamo il seguente testo nell'editor sotto al metodo GET.
```
/user:
    post:
      tags:
        - "user"
      summary: "Create a new user"
      operationId: "createUser"
      produces:
      - "application/json"
      responses:
        201:
          description: "successful operation"
        400:
          description: "invalid input"
          schema:
            $ref: '#/definitions/Error'
```
Dopo averlo fatto, noterete che il nuovo metodo è stato aggiunto nello swagger generato. 
Per lanciare la richiesta direttamente dallo swagger il procedimento è analogo al caso precedente, fatta eccezione che in questo caso sarà necessario aggiungere il body in formato JSON alla richiesta.
Ad esempio:
```
{
  "id": "001",
  "email": "prova@moova.it",
  "username": "pippo",
  "firstName": "pluto",
  "lastName": "paperino"
}
```
<figura 5>

Proviamo ora ad aggiungere altri due metodi, una PUT per gli aggiornamenti ed una DELETE per le rimozioni.
Aggiungiamo al modello il seguente testo, questa volta aggangiandoci al path `/user`:
```
    put:
      tags:
        - "user"
      summary: "Update an existing user"
      operationId: "updateUser"
      produces:
      - "application/json"
      responses:
        201:
          description: "successful operation"
        400:
          description: "invalid input"
          schema:
            $ref: '#/definitions/Error'
        404:
          description: "not found"
          schema:
            $ref: '#/definitions/Error'
    delete:
      tags:
        - "user"
      summary: "Delete an user"
      operationId: "deleteUser"
      produces:
      - "application/json"
      parameters:
      - name: username
        in: query
        description: "The username to search"
        required: true
        type: string
      responses:
        204:
          description: "successful operation"
        404:
          description: "not found"
          schema:
            $ref: '#/definitions/Error'
```

## Generiamo il codice

### ~ 5 min

Una volta che avremo finito di descrivere la nostra User API, ciò che manca è una webapp in grado di interfacciarsi con la stessa.

La Swagger Codegen risponde alle nostra esigenze, in quanto ci permette di generare il codice a partire dallo YAML, limitando al minimo gli errori.
Ad esempio, se il nostro applicativo deve essere scritto in Java, la Codegen creerà per noi sia le interfaccie API, che i modelli POJO. 

:fire: _[che cosa sono i POJO](https://it.wikipedia.org/wiki/Plain_Old_Java_Object)

Il metodo più veloce per utilizzare la Codegen è avvalersi delle funzionalità aggiuntive dello Swagger Editor.
Nella barra in alto clicchiamo su `Generate Server`, dopodichè selezioniamo `spring`.
<figura7>
Partirà in automatico il download del nostro codice Java autogenerato, compatibile con l'utilizzo del framework Spring o Spring Boot.
La struttura dei folder dovrebbe essere simile alla seguente
<figura8>

:fire: _[Spring framework](https://spring.io/)

Per vedere come creare un servizio Spring Boot, seguire la pillola: <link>.
