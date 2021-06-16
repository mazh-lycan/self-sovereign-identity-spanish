
# Credenciales verificables

En el siguiente documento se detalla lo encontrado sobre el **modelo de datos** y una definición formal de **Credencial Verificable**. Se presenta, a su vez, empresas competencia en el mercado u organizaciones públicas que proporcionen este servicio.

## Credencial Verificable

En inglés **Verifiable Credentials** **(VCs)**.
Una Credencial Verificable es un estándar abierto para credenciales digitales, caracterizado por representar información firmada digitalmente, así como sellada en el tiempo.

Las VCs pueden, no solo funcionar como método de gestión de identidades digitales, sino de cualquier tipo de dato, facilitando su portabilidad entre ecosistemas.

El modelo de datos actualmente está determinado por el **W3C**, conformado esencialmente por un **Issuer**, quien genera la credencial, un **Holder**, que la almacena para su uso posterior, y un **Verifier**, quien verifica la credencial y ante quien el Holder prueba una información sobre sí mismo.


# Modelo de datos

Más adelante se definirán en detalle y estructura.
- Contexto de la VC. Definido mediante la propiedad `@context`de los ficheros JSON.
- Identidad del Issuer.
- Timestamp del asunto (issue).
- Timestamp del momento de expiración.
- Tipo.
- Sujeto.
- Atributos de identidad del sujeto.
- Prueba criptográfica del sujeto para asegurar la integridad y autenticidad de la VC. Esta parte no está estandarizada. Algunas formas son:
	- Firma digital.
	- JSON Web Tokens con JSON Web Signatures.
	- Pruebas JSON-LD.
	- Pruebas zero-knowledge con esquemas como el de IBM Anonymous Credentials.

Las propiedades principales que un documento de una **DID (Decentralized Identity)** debe tener son:

```
«[
  "id" -> "example:123",
  "verificationMethod" ->  « «[
    "id": "did:example:123#keys-1",
    "controller": "did:example:123",
    "type": "Ed25519VerificationKey2018",
    "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVA"
  ] » »,
  "authentication" -> «
    "did:example:123#keys-1"
  »
]»
    
```

Entradas específicas de la representación central JSON:
```
«[ "@context" -> "https://www.w3.org/ns/did/v1" ]»
```
Un documento DID puede tener varias entradas con distintas claves públicas en distintos formatos, varias entradas con distintas formas de autenticación y varias entradas con distintos métodos de verificación.

![Entries in the DID Document map](https://www.w3.org/TR/did-core/diagrams/diagram-did-document-entries.svg)

Conversión entre representaciones: 
Los datos en una representación se pueden convertir a este modelo de datos, y de ahí a otra representación.


## Conceptos básicos para la especificación de Verifiable Credentials

**La forma de la verificación no es normativa.**

```
{
  "@context": [
    "[https://www.w3.org/2018/credentials/v1](https://www.w3.org/2018/credentials/v1)",
    "[https://www.w3.org/2018/credentials/examples/v1](https://www.w3.org/2018/credentials/examples/v1)"
  ],
  "id": "[http://example.gov/credentials/3732](http://example.gov/credentials/3732)",
  "type": ["VerifiableCredential", "UniversityDegreeCredential"],
  "issuer": "[https://example.edu](https://example.edu)",
  "issuanceDate": "2010-01-01T19:73:24Z",
  "credentialSubject": {
    "id": "did:example:ebfeb1f712ebc6f1c276e12ec21",
    "degree": {
      "type": "BachelorDegree",
      "name": "Bachelor of Science and Arts"
    }
  },  

 "credentialStatus": {
    "id": "[https://example.edu/status/24](https://example.edu/status/24)",
    "type": "CredentialStatusList2017"
  },
  "proof": {
    "type": "RsaSignature2018",
    "created": "2018-06-18T21:19:10Z",
    "proofPurpose": "assertionMethod",
    "verificationMethod": "[https://example.com/jdoe/keys/1](https://example.com/jdoe/keys/1)",
    "jws": "eyJhbGciOiJQUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19
      ..DJBMvvFAIC00nSGB6Tn0XKbbF9XrsaJZREWvR2aONYTQQxnyXirtXnlewJMB
      Bn2h9hfcGZrvnC1b6PgWmukzFJ1IiH1dWgnDIS81BH-IxXnPkbuYDeySorc4
      QU9MJxdVkY5EL4HYbcIfwKj6X4LBQ2_ZHZIu1jdqLcRZqHcsDF5KKylKc1TH
      n5VRWy5WhYg_gBnyWny8E6Qkrze53MR7OuAmmNJ1m1nN8SxDrG6a08L78J0-
      Fbas5OjAQz3c17GY8mVuDPOBIOVjMEghBlgl3nOi1ysxbRGhHLEK4s0KKbeR
      ogZdgt1DkQxDFxxn41QWDw_mmMCjs9qxg0zcZzqEJw"
  }
}
```
***context***
Su valor debe ser un conjunto ordenado cuyo primer elemento es una **URI** con el valor `https://www.w3.org/2018/credentials/v1`.
Los valores subsiguientes deben ser URIs, que de ser posible, referencien a documentos legibles por máquinas. Ejemplo JSON del context de Verifiable Credential siguiendo el modelo de W3C:
https://www.w3.org/2018/credentials/examples/v1

***id***
Debe ser un único valor.
El primer identificador refiere a la credencial verificable, y usa una **URL basada en HTTP.**
El segundo identificador refiere al sujeto de la credencial verificable y usa un **identificador descentralizado DID**.

***type***
El tipo de información. La especificación define un tipo de propiedad para la expresión de un tipo de información. Estos deben especificarse como **URI**. Los siguientes objetos deben tener su tipo especificado como se muestra en la tabla de la siguiente URL:
https://www.w3.org/TR/vc-data-model/#dfn-type
Todas las credenciales, presentaciones y objetos encapsulados deben especificar más de un type reducido (ejemplo, UniversityDegreeCredential).

***credentialSubject***
Este dato reclama la identidad del sujeto o los sujetos. Para especificar varios, se ponen uno detrás de otro dentro del campo.

***issuer***
Debe ser un único valor.
Define al issuer de la VC. 
Debe especificarse como **URI** o como un objeto conteniendo un id property.
Es posible expresar información adicional sobre el **issuer** asociando un objeto con la propiedad de issuer.

***issuanceDate***
Debe ser un único valor.
Sello temporal que determina cuándo una credencial se convierte en válida.
Debe ser un valor **String** del RFC3339. Puede ser una fecha futura. Determina el momento en el que  la propiedad credentialSubject pasa a ser válida.

***proofs***
Las firmas digitales que validan la VC. Emplean metodologías criptográficas. El ejemplo anterior emplea una prueba con firmas RSA.

***expirationDate***
Debe ser un único valor.
Determina la fecha y hora en la que la información de la credencial expira y, por lo tanto, deja de ser válida.
Debe ser un valor **String** del RFC3339.

***credentialStatus***
**Este valor no es obligatorio** 
Especifica si una credencial está revocada o suspensa. Este valor incluye un identificador **id** en formato **URL** y un **type** donde se especifica el estado. 


## Conceptos básicos para la especificación de Verifiable Presentations

Una presentación se emplea para combinar y presentar credenciales. Se pueden empaquetar para que el autor de los datos sea verificable. Normalmente poseen solamente un sujeto, pero esto no es una restricción, y pueden presentar datos sobre varios sujetos y varios asuntos de manera simultánea.

```
{
  "@context": [
    "[https://www.w3.org/2018/credentials/v1](https://www.w3.org/2018/credentials/v1)",
    "[https://www.w3.org/2018/credentials/examples/v1](https://www.w3.org/2018/credentials/examples/v1)"
  ],
  "id": "urn:uuid:3978344f-8596-4c3a-a978-8fcaba3903c5",
  "type": ["VerifiablePresentation", "CredentialManagerPresentation"],
  "verifiableCredential": [{   
	...
 }],
  "proof": [{   
	...
 }]
}
```

***id***
Esta propiedad es opcional, puede servir para proveer a la presentación de un identificador único.

***type***
Esta propiedad es requerida. Especifica el tipo de representación.  Tipos válidos son los de la tabla que aparece en la siguiente URL:
[https://www.w3.org/TR/vc-data-model/#dfn-type](https://www.w3.org/TR/vc-data-model/#dfn-type)

***verifiableCredential***
Esta propiedad es opcional. Si está presente, debe estar constituida por una o más VCs o por datos obtenidos de VCs en un formato criptográfico verificable.

***holder***
Esta propiedad es opcional. Si está presente, se ha de representar como una **URI** de la entidad que está generando la presentación.

***proof***
Esta propiedad es opcional. Si está presente, este valor asegura que la presentación es verificable. Se emplean metodologías criptográficas, como por ejemplo, la firma digital.

-----
La interacción entre los distintos componentes queda tal que así:

![Information graphs associated with a basic verifiable presentation.](https://www.w3.org/TR/vc-data-model/diagrams/presentation-graph.svg)

-----

Flujos de información de la especificación W3C para VCs:

![The roles and information flows for this specification.](https://www.w3.org/TR/vc-data-model/diagrams/ecosystemdetail.svg)

# Empresas y Organizaciones que proporcionan este servicio

Empresas:

- [**Evernym**](https://www.evernym.com): Relacionados con el Decentralized Identifier Working Group de W3C a través de **Brent Zundel** brent.zundel@evernym.com, copresidente del grupo junto a Daniel Burnett. 

	- [**Verity**](https://www.evernym.com/products/#verity): Producto que gestiona las VCs, los holders y los verifiers. [Video](https://vimeo.com/455946377?width=640&height=480)

	- [**Connect.me**](https://try.connect.me/): Wallet.

	- [**Mobile Wallet SDK**](https://www.evernym.com/blog/evernym-mobile-sdk/): SDK para implementar las funcionalidades de su wallet.

	- [**Sovrin**](https://sovrin.org/)

	- [**Hyperledger INDY**](https://www.hyperledger.org/use/hyperledger-indy)

- [**Gataca**](https://gataca.io/):

- [**Danubetech**](https://danubetech.com/technologies.html):

- [**Dominode**](http://www.dominode.com/):

- [**MATTR**](https://mattr.global/):

- [**Hyland Credentials**](https://www.hylandcredentials.com/):

- [**Spherity**](https://spherity.com/):

- [**Transmute**](https://www.transmute.industries/):

Otras empresas pueden encontrarse [aquí](https://digitaltrust.vc/startups-table/). Algunas de ellas se orientan a servicios muy concretos, como por ejemplo:

- [**HearRo**](https://www.hearro.com/): Orientado a llamadas y mensajería móvil.

- [**Authentys**](https://www.authentys.com/): Orientado a instituciones académicas y universidades.

- [**Selfkey**](https://selfkey.org/): Orientado a sistemas financieros.


### Next Generation Internet

- [**HIBI**](https://ontochain.ngi.eu/content/hibi). Ontochain. Seleccionado para la fase 2 del proyecto. Implementación de eIDAS. Empresa: Datarella.

- [**Gimly**](https://www.gimly.io/). Ontochain. No seleccionado para la fase 2 del proyecto.

- [**Solid Verif**](https://ontochain.ngi.eu/content/solid-verif). Ontochain. No seleccionado para la fase 2 del proyecto.


-------

# Safe Credentials

- Issuer y Verifier desacoplados.

	- Si el Verifier debe contactar al Issuer cada vez que el Holder use sus credenciales, el Issuer podría rastrear todos sus movimientos.

- No revelar la firma del Issuer en cada uso de las credenciales.

	- Firma de tus credenciales, sin revelar su clave privada.

- Portabilidad e interoperabilidad.

	- Evitar esto implicaría silos de datos, donde estos dejarían de poder comunicarse entre sí cuando fuese necesario.

- Flexibilidad y minimización de los datos.

	- Presentar solamente aquellas credenciales estrictamente necesarias. Evitar así, revelación de demasiada información al Provider.

- Confianza bidireccional.

	- El Holder debe poder verificarse ante el Provider al igual que el Provider debe poder verficarse ante el Holder.

# Referencias

- https://en.wikipedia.org/wiki/Verifiable_credentials#Aliases

- https://www.w3.org/TR/did-core/#data-model

- https://www.w3.org/TR/vc-data-model/

- https://www.w3.org/2018/credentials/examples/v1 

- https://www.evernym.com/blog/introducing-safe-credentials/

- https://digitaltrust.vc/startups-table/

- https://www.evernym.com/blog/introducing-safe-credentials/


--------


