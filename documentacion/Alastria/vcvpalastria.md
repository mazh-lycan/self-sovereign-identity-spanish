
# Verifiable Credentials y Verifiable Presentation Alastria 

Especificación de W3C con respecto al uso del formato JSON Web Token (JWT):

https://www.w3.org/TR/vc-data-model/#json-web-token


## Context para AlastriaVC y AlastriaVP
El que se requiere para cumplir con el estándar de W3C y el propio de [Alastria](https://alastria.github.io/identity/credentials/v1'
):
```
REQUIRED_CONTEXT: List[str] = field(default_factory=lambda: [

'https://www.w3.org/2018/credentials/v1',

'https://alastria.github.io/identity/credentials/v1'

])
```

## Types de AlastriaVC y AlastriaVP
Tipos requeridos. Para definir que es una Verifiable Credential, además de ``VerifiableCredential``, también es de tipo ``AlastriaVerifiableCredential``:

```
const requiredTypes: string[] = [

'VerifiableCredential',

'AlastriaVerifiableCredential'

])
``` 

En el caso de una Verifiable Presentation hacen falta los siguientes **types**. De nuevo, además de ``VerifiablePresentation`` está ``AlastriaVerifiablePresentation``:
```
const requiredTypes: string[] = [

'VerifiablePresentation',

'AlastriaVerifiablePresentation'

]
```


Estos valores se definen en el campo de **type** que se encuentra en el modelo de datos de W3C: https://www.w3.org/TR/vc-data-model/#types

De este modo, una VC de Alastria contiene estos valores:

- AlastriaVerifiableCredential.
	- title.
	- type.
	- required:  ["header", "payload", "signature"]
Estos valores de cabecera/header, carga/payload y signature/firma digital, se definen más adelante en *Especificaciones W3C de uso de JWT*.
  - Incorporará también la información de la transacción en el momento de subida a la blockchain.


## Proof
- Proof empleada por Alastria: JSON web tokens con firma ES256K.

Debido al uso de JSON tokens, la especificación de las VC cambia levemente.

```createAlastriaToken```
Crea token JSON de firma ES256K para firmar como issuer.


## Especificaciones W3C de uso de JWT


El uso de este tipo de dato implica una nomenclatura levemente distinta. Se define en [W3C](https://www.w3.org/TR/vc-data-model/#json-web-token) para que esta se adapte igualmente al modelo de datos estándar.

### Extensiones del formato JSON Web Token

Debido a este formato: 
La especificación introduce dos nuevas ```claim``` (aserciones sobre el sujeto), que contienen las partes de los estándares para Verifiable Credentials y Verifiable Presentations.
No hay reglas concretas para JWT. Estos objetos se encuentran en la carga, payload, del JWT:

-   `vc`: Objeto JSON object, que debe estar presente en una Verifiable Credential de formato JWT. Este objeto debe estar de acuerdo a la especificación https://www.w3.org/TR/vc-data-model/#dfn-verifiable-credentials 
-   `vp`: Objeto JSON object, que debe estar presente en una Verifiable Presentation de formato JWT. Este objeto debe estar de acuerdo a la especificación https://www.w3.org/TR/vc-data-model/#dfn-verifiable-presentations



### Codificación para el uso de JWT

Para realizar la codificación en JWT, algunas partes de esta especificación deben ir en alguna de las siguientes partes:
-   Parámetros estándar JOSE en la cabecera
-   Como nombres de JWT claim (aserciones hechas sobre el subject) registrados. 
-   Contenidas en la parte de la firma JWS.


En caso de existir una JWS, la firma digital corresponderá a:
 - Si es la JWS de una VC, al issuer de la VC.
 - Si es la JWS de una VP, al holder de la VC.

Con esto se prueba que el issuer firmó la carga del JWT y por lo tanto no hace falta un campo de *proof*.


En caso de no presentar JWS, el campo de ``proof`` es obligatorio.

---
La especificación para la cabecera JOSE de este tipo de formato incluye los siguientes parámetros:

- **alg**. Especifica el tipo de firma digital que se usa. En el caso de Alastria este es **ES256K**.
- **kid**. Debe ir especificado en el caso de que el issuer posea varias claves. Puede referir a una clave de un JWKS o de un documento DID. Es la clave usada para firmar el JWT.
- **typ**. Si este valor está presente debe ser **JWT**. Especifica el tipo del fichero.

En Alastria se especifica tal que :
```
BASE_HEADER = {

'alg': 'ES256K',

'typ': 'JWT'

}
```
Destacar que **kid** también es empleado en algunas partes de su código, como el ejemplo para crear VC.

Los siguientes campos tienen una correlación con el modelo de datos de VC JSON. En JWT poseen otra nomenclatura para evitar incompatibilidades con los procesadores JWT.

- **exp** -> **expirationDate**. Codificado en UNIX timestamp (`NumericDate`).
-  **iss** -> **issuer** si se trata de una Verifiable Credential, o **holder** si se trata de una Verifiable Presentation.
- **nbf** -> **issuanceDate**. Codificado en UNIX timestamp (`NumericDate`).
- **jti** -> **id**. Identificador de la credencial (VC) o de la presentación (VP). 
- **sub** -> **id** incluido en el campo de **subject** de la VC. Es el id del sujeto, la entidad sobre la que las aserciones (claim) son hechas. Es **credentialSubject**
- **aud** -> Es un campo que especifica el destinatario de la VP. Normalmente corresponde al *verifier* al que el *holder* quiere presentar la VP.

Además, pueden usarse otros parámetros propios de JWT tanto en la cabecera JOSE como en la carga.
En Alastria se encuentran los siguientes:

- **context** -> **context**.
- **jwk** - Campo con la clave pública (*imagino que* complementaria a la clave privada) usada para firmar la cabecera JWT. Opcional.
- **iat** - Es un campo donde se almacena el valor de la fecha en el momento de la generación de la entidad (sea una Verifiable Credential, una Verifiable Presentation...).  No confundir con **nbf**, equivalente de ``issuanceDate``, pues no permite la escritura de una fecha futura, emplea la actual obligatoriamente.


### Decodificación para el uso de JWT
A la hora de realizar la decodificación a JSON hay que tener en cuenta las equivalencias establecidas anteriormente. De este modo se logra el modelo de datos estándar descrito por W3C para JSON.


### Ejemplos

**Cabecera** en formato JOSE. Mismo formato para VC que para VP.
```
{
    "alg": "RS256",
    "typ": "JWT",
    "kid": "did:example:abfe13f712120431c276e12ecab#keys-1"
}
```

**Payload** de Verifiable Credential codificada en formato JWT usando firma JWS como *proof*. La correspondiente clave de verificación se puede obtener del parámetro de la cabecera **kid**:

```
{
  "sub": "did:example:ebfeb1f712ebc6f1c276e12ec21",
  "jti": "http://example.edu/credentials/3732",
  "iss": "https://example.com/keys/foo.jwk",
  "nbf": 1541493724,
  "iat": 1541493724,
  "exp": 1573029723,
  "nonce": "660!6345FSer",
  "vc": {
    "@context": [
      "https://www.w3.org/2018/credentials/v1",
      "https://www.w3.org/2018/credentials/examples/v1"
    ],
    "type": ["VerifiableCredential", "UniversityDegreeCredential"],
    "credentialSubject": {
      "degree": {
        "type": "BachelorDegree",
        "name": "Bachelor of Science and Arts"
      }
    }
  }
}
```

Payload de Verifiable Presentation codificada en formato JWT usando firma JWS como *proof*:


```
{
  "iss": "did:example:ebfeb1f712ebc6f1c276e12ec21",
  "jti": "urn:uuid:3978344f-8596-4c3a-a978-8fcaba3903c5",
  "aud": "did:example:4a57546973436f6f6c4a4a57573",
  "nbf": 1541493724,
  "iat": 1541493724,
  "exp": 1573029723,
  "nonce": "343s$FSFDa-",
  "vp": {
    "@context": [
      "https://www.w3.org/2018/credentials/v1",
      "https://www.w3.org/2018/credentials/examples/v1"
    ],
    "type": ["VerifiablePresentation"],
    

    "verifiableCredential": ["

"]
  }
}
```

## Librerías de Alastria

### Creación de una Verifiable Credential
Resultado de la subida como transacción a un bloque. Incluye la información de la credencial (head, payload y signature, con la especificacion JWT descrita) seguida de la de Ethereum.

Se pueden ver ejemplos en:
https://github.com/alastria/alastria-identity-example/wiki/Credentials-examples

```
------ Preparing Subject1 identity ------


 ------ Creating credential ------

The credential1 is:  { header:
   { typ: 'JWT',
     alg: 'ES256K',
     kid: 'did:ala:quor:redt:QmeeasCZ9jLbXueBJ7d7csxhb#keys-1' },
  payload:
   { jti: 'https://www.empresa.com/alastria/credentials/3734',
     iss: 'did:ala:quor:redT:05c5e87b79022b0b85280efb332f7506d9294fe0',
     sub: 'did:alastria:quorum:redt:QmeeasCZ9jLbXueBJ7d7csxhb',
     iat: 1589913750,
     exp: 1563783392,
     nbf: 1563782792,
     vc:
      { '@context': 'https://w3id.org/did/v1',
        type: [Array],
        credentialSubject: [Object] } } }
The signed token is:  eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NksiLCJraWQiOiJkaWQ6YWxhOnF1b3I6cmVkdDpRbWVlYXNDWjlqTGJYdWVCSjdkN2NzeGhiI2tleXMtMSJ9.eyJqdGkiOiJodHRwczovL3d3dy5lbXByZXNhLmNvbS9hbGFzdHJpYS9jcmVkZW50aWFscy8zNzM0IiwiaXNzIjoiZGlkOmFsYTpxdW9yOnJlZFQ6MDVjNWU4N2I3OTAyMmIwYjg1MjgwZWZiMzMyZjc1MDZkOTI5NGZlMCIsInN1YiI6ImRpZDphbGFzdHJpYTpxdW9ydW06cmVkdDpRbWVlYXNDWjlqTGJYdWVCSjdkN2NzeGhiIiwiaWF0IjoxNTg5OTEzNzUwLCJleHAiOjE1NjM3ODMzOTIsIm5iZiI6MTU2Mzc4Mjc5MiwidmMiOnsiQGNvbnRleHQiOiJodHRwczovL3czaWQub3JnL2RpZC92MSIsInR5cGUiOlsiVmVyaWZpYWJsZUNyZWRlbnRpYWwiLCJBbGFzdHJpYUV4YW1wbGVDcmVkZW50aWFsIl0sImNyZWRlbnRpYWxTdWJqZWN0Ijp7IlN0dWRlbnRJRCI6IjExMjM1ODEzIiwibGV2ZWxPZkFzc3VyYW5jZSI6ImJhc2ljIn19fQ.Eg9gma-GgY656okYOa2ozLfOFsDdhOMzsoD0zkjQNXXj8lnZh6V36P3VlEjn1pdIDBoaHj0rydp0S-9hyZksuw
The Subject1 PSMHash is  0xbea00c8c86bad7dda0f6dad2ca0e48d3b522a6b33de9811d816e70a21d577fc4
(addSubjectCredential)The transaction is:  { to: '0xbd4a2c84edb97be5beff7cd341bd63567e73f8c9',
  data:
   '0x597b2e9b0000000000000000000000007bbca11cbd86b562136d5708eba40f4bc0aa1ddc000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000600000000000000000000000000000000000000000000000000000000000000084e04ce02cbea00c8c86bad7dda0f6dad2ca0e48d3b522a6b33de9811d816e70a21d577fc40000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000000e7777772e676f6f676c652e636f6d00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
  gasLimit: 600000,
  gasPrice: 0,
  nonce: '0x0' }
(addSubjectCredential)The transaction bytes data is:  0xf9018882045580830927c094bd4a2c84edb97be5beff7cd341bd63567e73f8c980b90124597b2e9b0000000000000000000000007bbca11cbd86b562136d5708eba40f4bc0aa1ddc000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000600000000000000000000000000000000000000000000000000000000000000084e04ce02cbea00c8c86bad7dda0f6dad2ca0e48d3b522a6b33de9811d816e70a21d577fc40000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000000e7777772e676f6f676c652e636f6d000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000001ca005dcfab16b2c886252c794ff684b69ee559b57878ed0f5db274f2028a2cd3c67a003d026d33fcf86a2dfcb011065a85849545b7fcbd281b5b32c0d38236cd98fde
HASH:  0xdba9319c1db258836b26c2ea043f56184156037677a7f689f5e7b1c80350a1cd
RECEIPT: { blockHash:
   '0xd34e04483b2a78d6e08a258245bfa30b3e93b4312a540cbca2b52b4dec933089',
  blockNumber: 40131373,
  contractAddress: null,
  cumulativeGasUsed: 124843,
  from: '0x806bc0d7a47b890383a831634bcb92dd4030b092',
  gasUsed: 124843,
  logs:
   [ { address: '0x9d68DB2F1E7Bf4c65DBD31F6CC9F2adeb86D584d',
       topics: [Array],
       data:
        '0x000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000400000000000000000000000000000000000000000000000000000000000000084e04ce02cbea00c8c86bad7dda0f6dad2ca0e48d3b522a6b33de9811d816e70a21d577fc40000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000000e7777772e676f6f676c652e636f6d00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
       blockNumber: 40131373,
       transactionHash:
        '0xdba9319c1db258836b26c2ea043f56184156037677a7f689f5e7b1c80350a1cd',
       transactionIndex: 0,
       blockHash:
        '0xd34e04483b2a78d6e08a258245bfa30b3e93b4312a540cbca2b52b4dec933089',
       logIndex: 0,
       removed: false,
       id: 'log_85d0a7ba' } ],
  logsBloom:
   '0x00000000040000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000080000000010000000000000000002000000000000000000000000000000000000200000000000000000000000000000001000000000000000000000000000000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000010000000000000000000000',
  status: true,
  to: '0xbd4a2c84edb97be5beff7cd341bd63567e73f8c9',
  transactionHash:
   '0xdba9319c1db258836b26c2ea043f56184156037677a7f689f5e7b1c80350a1cd',
  transactionIndex: 0 }
(SubjectCredentialStatus) ----->  { exists: true, status: '0' }

```

### Creación de una Verifiable Presentation
Resultado de la subida como transacción a un bloque. Incluye la información de la presentación (head, payload y signature, con la especificacion JWT descrita) seguida de la de Ethereum.

Destacar el campo **verifiableCredential: [Array]**, donde se incluirían tantas VCs como sean necesarias.

Se pueden ver ejemplos en:
https://github.com/alastria/alastria-identity-example/wiki/Presentations-examples


```
createPresentation ----------> { header:
   { alg: 'ES256K',
     typ: 'JWT',
     kid: 'did:ala:quor:redt:QmeeasCZ9jLbXueBJ7d7csxhb#keys-1' },
  payload:
   { jti: 'https://www.empresa.com/alastria/credentials/3732',
     iss: 'did:alastria:quorum:redt:QmeeasCZ9jLbX...ueBJ7d7csxhb',
     aud: 'did:alastria:quorum:redt:QmeeasCZ9jLbX...ueBJ7d7csxhb',
     iat: 1589920877,
     exp: 1530735444,
     nbf: 1525465044,
     vp:
      { '@context': [Array],
        type: [Array],
        procUrl: 'https://www.empresa.com/alastria/businessprocess/4583',
        procHash: 'H398sjHd...kldjUYn475n',
        verifiableCredential: [Array] } } }
signedJWTPresentation -------------> eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NksiLCJraWQiOiJkaWQ6YWxhOnF1b3I6cmVkdDpRbWVlYXNDWjlqTGJYdWVCSjdkN2NzeGhiI2tleXMtMSJ9.eyJqdGkiOiJodHRwczovL3d3dy5lbXByZXNhLmNvbS9hbGFzdHJpYS9jcmVkZW50aWFscy8zNzMyIiwiaXNzIjoiZGlkOmFsYXN0cmlhOnF1b3J1bTpyZWR0OlFtZWVhc0NaOWpMYlguLi51ZUJKN2Q3Y3N4aGIiLCJhdWQiOiJkaWQ6YWxhc3RyaWE6cXVvcnVtOnJlZHQ6UW1lZWFzQ1o5akxiWC4uLnVlQko3ZDdjc3hoYiIsImlhdCI6MTU4OTkyMDg3NywiZXhwIjoxNTMwNzM1NDQ0LCJuYmYiOjE1MjU0NjUwNDQsInZwIjp7IkBjb250ZXh0IjpbImh0dHBzOi8vd3d3LnczLm9yZy8yMDE4L2NyZWRlbnRpYWxzL3YxIiwiSldUIl0sInR5cGUiOlsiVmVyaWZpYWJsZVByZXNlbnRhdGlvbiJdLCJwcm9jVXJsIjoiaHR0cHM6Ly93d3cuZW1wcmVzYS5jb20vYWxhc3RyaWEvYnVzaW5lc3Nwcm9jZXNzLzQ1ODMiLCJwcm9jSGFzaCI6IkgzOThzakhkLi4ua2xkalVZbjQ3NW4iLCJ2ZXJpZmlhYmxlQ3JlZGVudGlhbCI6W3t9XX19.bXyaphH_b1hIG5rdRJ-fbiypyZz6X81O12ngLSygmSswcsvHqKMo44qKCi7cF_c2JVIVZ3e1kimwy33xf49C-Q
The PSMHashSubject1 is: 0x5fbc15908149ffdf4b5396d65fdc4100d5085d88e77b6991722ceb7378b2540a
The PSMHashEntity2 is: 0x2ad4720826e5daa21b070c9c65fcdf85a94348bb3ea33cda24281c885310fa54
(subject1PresentationSigned)The transaction bytes data is:  0xf9018882045780830927c094bd4a2c84edb97be5beff7cd341bd63567e73f8c980b90124597b2e9b00000000000000000000000054d1dbfacada17ff39f2bac08e05fbdb4659f6710000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000006000000000000000000000000000000000000000000000000000000000000000844e3a5de55fbc15908149ffdf4b5396d65fdc4100d5085d88e77b6991722ceb7378b2540a0000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000000e7777772e676f6f676c652e636f6d000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000001ca040c79241b81715fd8fe58eea2800801a8f8d01a90b7cfcfaefe284bc05800de3a015cca5663c790de2947e5789a3be95374d680ed15c24d4590d3771c8ca8181df
Receipt ---------> { blockHash:
   '0x04ba69cce477c75cad0c7476571717a6885358af30fa48714af7e82f831d394f',
  blockNumber: 40134427,
  contractAddress: null,
  cumulativeGasUsed: 124581,
  from: '0x806bc0d7a47b890383a831634bcb92dd4030b092',
  gasUsed: 124581,
  logs:
   [ { address: '0x9d68DB2F1E7Bf4c65DBD31F6CC9F2adeb86D584d',
       topics: [Array],
       data:
        '0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000000000000000000000844e3a5de55fbc15908149ffdf4b5396d65fdc4100d5085d88e77b6991722ceb7378b2540a0000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000000e7777772e676f6f676c652e636f6d00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
       blockNumber: 40134427,
       transactionHash:
        '0xb88697325d37abb811303685bd0dd2b2172f3df67819d914588ee9a70d95bc4e',
       transactionIndex: 0,
       blockHash:
        '0x04ba69cce477c75cad0c7476571717a6885358af30fa48714af7e82f831d394f',
       logIndex: 0,
       removed: false,
       id: 'log_d5de6ddd' } ],
  logsBloom:
   '0x00000000040010000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000280000000000000000000000000000080000000010000000000000000002000000000000000000000000000000000000000000000000000000000000000000001100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
  status: true,
  to: '0xbd4a2c84edb97be5beff7cd341bd63567e73f8c9',
  transactionHash:
   '0xb88697325d37abb811303685bd0dd2b2172f3df67819d914588ee9a70d95bc4e',
  transactionIndex: 0 }


```




### Alastria Github para usar las librerías
Crear IDs:
https://github.com/alastria/alastria-identity-example/wiki/Create-Alastria-ID-examples

Uso de librerías:
https://github.com/alastria/alastria-identity-example



> Written with [StackEdit](https://stackedit.io/).
