
# Librerías de Alastria
Esta librería debe incorporarse a los módulos de node dentro del workspace en el que se esté trabajando y se vayan a utilizar.
Al hacer uso de estas librerías, se podrán generar transacciones que se minarán en la red blockchain.
Dichas transacciones incluirán el bytecode del SmartContract  ejecutado con esta librería, así como los parámetros habituales de una transacción (transactionHash, blockNumber...).

## Transacciones
Las transacciones que se suben a la blockchain se generan una vez se tiene el token correspondiente generado. Esto se hace llamando a las funciones de ```src/transactionFactory.ts```.
Dichas funciones generan la información de la transacción a minar, es decir, parámetros como ```transaction.gasLimit```, ```transaction.to```...

Estas suben los correspondientes tokens firmados, haciendo uso de los Smart Contracts de https://github.com/alastria/alastria-identity que se hayan generado previamente. Esto incluiría el uso de las addresses de los involucrados, así como sus claves privadas e la Blockchain. 
Conviene tener esta información almacenada a parte, en ficheros de configuración o claves, preferiblemente en formato JSON, para evitar subir datos sensibles en el código de la transacción.


## Tokens
Dado que Alastria hace uso del modelo de datos basado en JSON Web Tokens, sus librerías funcionan en base a la creación de dichos tokens. Generan tokens de distintos tipos dependiendo del elemento del ecosistema a crear.
```src/tokensFactory.ts``` es el lugar donde se encuentran sus estructuras.

## decodeJWT
Función empleada por el Servide Provider o por la Subject Wallet. Decodifica el JWT al modelo JSON de datos de W3C original, tal y como se especificó en el documento de ```vcalastria```, en el apartado de *Decodificación*.
Esta función recibe el JWT como string y lo manda a decodificar con la función ```decodeToken``` de la librería ```jsontokens```


## signJWT
Recibe como entrada un token de tipo ```JwtToken``` y una clave privada de la entidad o sujeto firmante en formato keythereum. Esta ha de ser recuperada previamente con keythereum recover de un formato JSON parseado tal que:
```
{  
"address": "a9728125c573924b2b1ad6a8a8cd9bf6858ced49",  
"crypto": {  
	"cipher": "aes-128-ctr",  
	"ciphertext": "a38f4475ef7cdeb77f262cc03369061e28e37415269cc41977ddecd04d9d8429",  
	"cipherparams": {  
		"iv": "d9be09d3895821995ca234502950ad39"  
	},  
	"kdf": "scrypt",  
	"kdfparams": {  
		"dklen": 32,  
		"n": 262144,  
		"p": 1,  
		"r": 8,  
		"salt": "c2c813066974631abe6cd325f904ea28c3a01b3c39a006a75f0e3fa7e390484d"  
	},  
	"mac": "a90b79da477cbad7e3817bcdad7d921306174da490e1ae1732cc90f4805e2459"  
	},  
"id": "8f0eda33-ace1-4aec-a26b-510d924f920c",  
"version": 3  
}
```
Para poder efectuar esto hace falta extraer del archivo ```configuration.json``` el valor de ```addressPassword```. Esto es debido a que la clave se guarda cifrada y, con la ```addressPassword``` se desencripta su valor para poder usarla. *Es recomendable guardar la clave y contraseña a parte (como en dicho archivo ```configuration.json```o usando librerías externas) con el fin de que esta no aparezca en el código del contrato que se subirá como transacción.*
Firma el token recibido, tanto head como payload, con la clave privada recibida empleando el algoritmo ES256K.
En caso de que no estén ambas partes, (head y payload), firma lo recibido  (sea solo head o payload) transformándolo antes a cadena de texto en formato JSON.


## verifyJWT

Recibe una entrada jwt, en formato String o SignedToken y una clave pública.
Se aplica la función TokenVerifier sobre la entrada jwt, comprobando que la clave pública recibida es la pareada con la privada usada para firmar con el algoritmo ES256K.
En caso de usar una clave pública descomprimida, hay que añadir el valor ***04*** a la llamada tal que:
```
tokensFactory.tokens.verifyJWT(  
	signedAlastriaToken,  
	'04' + configData.entityPublicKey.substr(2)  
)
```
Este valor se añade como cabecera hexadecimal a la clave, especificando su formato como *descomprimido*.


## createAlastriaSession  
Esta función permite crear una sesión Alastria. Esto implica tanto un token Alastria como un identificador Alastria y su clave pública respectiva.
Recibe los siguientes parámetros para la creación de la Session en formato JWT:

- **context: string[]**:  Se especifica el context tal y como está definido en el modelo de datos de W3C para JWT. Los context propios de Alastria se definen más adelante por la propia función, luego no es necesario añadirlos en los parámetros de entrada.
- **iss: string**: issuer o holder, según si se está empleando junto a una VC o una VP, respectivamente. Se trata de un DID en formato URI (ejemplo: ```did:ala:quor:redT:0xb0462601a17581ed1b5dda86272aee5e49e2f5f7 ```. Tal y como se especifica en el modelo de datos de W3C para JWT.
- **kid: string**:  Indica la clave del iss, en formato URI, empleada para firmar el JWT de la credencial. Tal y como se especifica en el modelo de datos de W3C para JWT.
- **type: string[]**: Especifica el tipo de fichero tal y como se define en el modelo de datos de W3C. El type AlastriaSession se incluye en la propia función, luego solo hace falta especificar otros tipos concretos.
- **alastriaToken: string**: Token de Alastria que se debe haber **generado** con ````createAlastrianToken()``` y **firmado** con ```signJWT()``` **previamente**.
- **exp: number**: Codificado en UNIX timestamp (`NumericDate`). Es la fecha de expiración, y por lo tanto, la fecha en la que la AlastriaSession deja de ser válida.

Parámetros opcionales:
- **pku?: string**: Clave pública del administrador en formato String hexadecimal descomprimido.
- **nbf?: number**: Codificado en UNIX timestamp (`NumericDate`). Es la fecha de activación, es decir, el momento en el que la AlastriaSession comienza a ser válida. Puede ser un valor futuro. **Similar a un timestamp.**
- **jti?: string**: Identificador String del propio objeto AlastriaSession que se está generando.

Además del context introducido, esta función incorpora:

'[https://alastria.github.io/identity/artifacts/v1](https://alastria.github.io/identity/artifacts/v1 "https://alastria.github.io/identity/artifacts/v1")'

También, además de los types introducidos, esta función incorpora:
```
string[] = ['AlastriaSession']
```

En caso de haber introducido el parámetro de clave pública *pku*, este es extraído a formato `address`  con prefijo hexadecimal '0x'. Con ello se firma la cabecera y con ello queda la prueba (proof) de la validez del token.
Tras ello se genera una variable de tipo jwt, introduciendo en cada campo el respectivo de los obtenidos como entrada.
```
const jwt = {  
	header: {  
		alg: 'ES256K',  
		typ: 'JWT',  
		jwk: pku,  
		kid: kid  
	},  
	payload: {  
		'@context': requiredContext.concat(context),  
		type: requiredTypes.concat(type),  
		iss: iss,  
		iat: Math.round([Date.now](https://Date.now "https://Date.now")() / 1000),  
		exp: exp,  
		nbf: nbf,  
		alastriaToken: alastriaToken,  
		jti: jti  
	}  
}  
```
Como puede verse, la función tiene especificado ya el tipo de cifrado ES256K y el formato typ JWT, ambos valores constantes en la cabecera de toda AlastriaSession. Se añade en la carga el valor **iat**, o fecha actual, la de generación del jwt, actuando como un **timestamp** del momento de creación del token.
Tras ello, esta función devuelve la variable **jwt** creada.


## createAlastriaToken  

De manera análoga a la creación de una AlastriaSession, los parámetros de entrada de esta función corresponden a aquellos necesarios para modelar el JWT de un token Alastria.
- **iss: string**: issuer o holder, según si se está empleando junto a una VC o una VP, respectivamente. Se trata de un DID en formato URI (ejemplo: `did:ala:quor:redT:0xb0462601a17581ed1b5dda86272aee5e49e2f5f7`. Tal y como se especifica en el modelo de datos de W3C para JWT.
- **gwu: string**: URL del proveedor. Por ejemplo: "[https://regular.telsius.blockchainbyeveris.io:2000](https://regular.telsius.blockchainbyeveris.io:2000 "https://regular.telsius.blockchainbyeveris.io:2000")"
- **cbu: string**:  URL de devolución de llamada. Por ejemplo: "[https://serviceprovider.alastria.blockchainbyeveris.io/api/login/](https://serviceprovider.alastria.blockchainbyeveris.io/api/login/ "https://serviceprovider.alastria.blockchainbyeveris.io/api/login/")"
- **ani: string**: Identificador de la red. Por ejemplo, *redT*.
- **exp: number**: Codificado en UNIX timestamp (`NumericDate`). Es la fecha de expiración, y por lo tanto, la fecha en la que el token deja de ser válido.
- **kid: string**:  Indica la clave del iss, en formato URI, empleada para firmar el JWT de la credencial. Tal y como se especifica en el modelo de datos de W3C para JWT.

Parámetros opcionales

- **jwk?: string**: Clave pública de la entidad ***iss*** en formato String hexadecimal descomprimido.
- **nbf?: number**: Codificado en UNIX timestamp (`NumericDate`). Es la fecha de activación, es decir, el momento en el que el token comienza a ser válido. Puede ser un valor futuro. **Similar a un timestamp.** 
- **jti?: string**: Identificador String del propio objeto AlastriaToken que se está generando.

En caso de haber introducido el parámetro de clave pública _jwk_, este es extraído a formato `address` con prefijo hexadecimal '0x'.  Con ello se firma la cabecera y con ello queda la prueba (proof) de la validez del token.
Tras ello se genera una variable de tipo jwt, introduciendo en cada campo el respectivo de los obtenidos como entrada.

La función tiene especificado ya el tipo de cifrado ES256K y el formato typ JWT, ambos valores constantes en la cabecera. Se añade en la carga el valor **iat**, o fecha actual, la de generación del jwt, actuando como un **timestamp** del momento de creación del token.

## createCredential  
De nuevo, los parámetros de entrada de esta función corresponden a aquellos necesarios para modelar el JWT correspondiente a una Verifiable Credential.

- **iss: string**: issuer de la VerifiableCredential. Se trata de un DID en formato URI (ejemplo: `did:ala:quor:redT:0xb0462601a17581ed1b5dda86272aee5e49e2f5f7`. Tal y como se especifica en el modelo de datos de W3C para JWT.
- **context: string[]**: Se especifica el context tal y como está definido en el modelo de datos de W3C para JWT. Los context propios de Alastria se definen más adelante por la propia función, luego no es necesario añadirlos en los parámetros de entrada.
- **credentialSubject: object**: Objeto con los siguientes valores:
	- **credentialSubject[credentialKey] = credentialValue**. Donde se especifica la clave de la credential y el tipo del que es. Por ejemplo "degree", "StudentID"... Y el valor como valor único.
	- **credentialSubject.levelOfAssurance**. El nivel de garantía que posee la credencial. Debe cumplir las necesidades del Verifier, así como de otras partes interesadas dependientes. Es un número entero.

Parámetros opcionales: 
- **kid?: string**: Indica la clave del iss, en formato URI, empleada para firmar el JWT de la credencial. Tal y como se especifica en el modelo de datos de W3C para JWT.
- **sub?: string**: SubjectAlastriaID. Equivale en el modelo de datos JSON de W3C al id incluido en el campo de **subject** de la VC. Es el id del sujeto, la entidad sobre la que las aserciones (claim) son hechas. Se encuentra en formato URI. Por ejemplo:
``` "did:ala:quor:redt:0x12eeaCCA9eEbB78eB97d7cac6b" ```
- **exp?: number**: Codificado en UNIX timestamp (`NumericDate`). Es la fecha de expiración, y por lo tanto, la fecha en la que la Verifiable Credential deja de ser válida.
- **nbf?: number**: Codificado en UNIX timestamp (`NumericDate`). Es la fecha de activación, es decir, el momento en el que la Verifiable Credential comienza a ser válida. Puede ser un valor futuro. **Similar a un timestamp.** 
- **jti?: string**: Identificador String del propio objeto Verifiable Credential que se está generando.
- **jwk?: string**: Clave pública de la entidad ***iss*** en formato String hexadecimal descomprimido. 
- **type?: string[]**: Especifica el tipo de fichero tal y como se define en el modelo de datos de W3C. Los tipos VerifiableCredential y AlastriaVerifiableCredential son añadidos por la propia función. De este modo, solo es necesario especificar otros tipos específicos aquí.

La función añade los siguientes valores context a los ya introducidos:


'[https://www.w3.org/2018/credentials/v1](https://www.w3.org/2018/credentials/v1 "https://www.w3.org/2018/credentials/v1")'

'[https://alastria.github.io/identity/credentials/v1](https://alastria.github.io/identity/credentials/v1 "https://alastria.github.io/identity/credentials/v1")'  



**La función crea los siguientes valores type, tipos propios de toda VerifiableCredential de Alastria:**
```
'VerifiableCredential'
```
```
'AlastriaVerifiableCredential'  
```

La función extrae la clave pública del valor jwk como una address con prefijo hexadecimal '0x'.

Tras ello, genera el valor jwt de manera análoga a la creación de otros tokens Alastria descritos anteriormente, cifrando con ES256K y en formato JWT, añadiendo **iat** como valor fecha actual a la carga, el cual actúa como un **timestamp** marcando el momento de creación del token

## createPresentation  

De nuevo, los parámetros de entrada de esta función corresponden a aquellos necesarios para modelar el JWT correspondiente a una Verifiable Presentation. Para poder proporcionar todos, previamente se debe haber generado y firmado la VerifiableCredential a usar en la VerifiablePresentation, pues esta es uno de los parámetros obligatorios de entrada de la función. 

- **iss: string** : holder de la VerifiablePresentation. Se trata de un DID en formato URI (ejemplo: `did:alastria:quorum:redt:QmeeasCZ9jLbX...ueBJ7d7csxhb`). Tal y como se especifica en el modelo de datos de W3C para JWT.
- **aud: string**: Es un campo que especifica el destinatario de la VP. Normalmente corresponde al *verifier* al que el *holder* quiere presentar la VP. Se trata de un DID en formato URI (ejemplo: `did:alastria:quorum:redt:QmeeasCZ9jLbX...ueBJ7d7csxhb`). Tal y como se especifica en el modelo de datos de W3C para JWT.
- **context: string[]**: Se especifica el context tal y como está definido en el modelo de datos de W3C para JWT. Los context propios de Alastria se definen más adelante por la propia función, luego no es necesario añadirlos en los parámetros de entrada. 
- **verifiableCredential: string[]**: Es un array de Verifiable Credentials en formato de token base64encoded resultado de aplicar ```signJWT(presentationRequest, adminKey)```, es decir, firmar con la clave de administrador una solicitud de presentación, empleando el algoritmo ES256K. Esto da como resultado un String codificado en el siguiente formato:
```eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NksiLCJraWQiOiJkaWQ6YWxhOnF1b3I6cmVkVDowMGY0NzFjNzVjMTRjOWVlOWIxNmU0ZDY0ZjhhY2I0N2E3YmYyYzRhIn0```. CACNEA
- **procUrl: string**: Se trata de una URL externa que contiene un documento donde se especifica el propósito de los datos que el Service Provider está recibiendo. El formato es una URL tal que: ```"https://www.empresa.com/alastria/businessprocess/4583"``` 
- **procHash: string**: Se trata del resumen hash del documento  donde se especifica el propósito de los datos que el Service Provider está recibiendo. Como puede verse, es la hash del documento obtenible de la URL de procURL. Esto es con fines verificadores. El formato es un String tal que ```H398sjHd...kldjUYn475n```
- **type: string[]**: Especifica el tipo de fihero tal y como se define en el modelo de datos de W3C. Los tipos VerifiablePresentation y AlastriaVerifiablePresentation son añadidos por la propia función. De este modo, solo es necesario especificar otros tipos específicos aquí.

Parámetros opcionales:

- **kid?: string**: Indica la clave del iss, en formato URI, empleada para firmar el JWT de la credencial. Tal y como se especifica en el modelo de datos de W3C para JWT.
- **jwk?: string**: Clave pública de la entidad ***iss*** en formato String hexadecimal descomprimido. 
- **exp?: number**: Codificado en UNIX timestamp (`NumericDate`). Es la fecha de expiración, y por lo tanto, la fecha en la que la Verifiable Presentation deja de ser válida y, por lo tanto, deja ser aceptada para ser procesada.
- **nbf?: number**: Codificado en UNIX timestamp (`NumericDate`). Es la fecha de activación, es decir, el momento en el que la Verifiable Presentation comienza a ser válida. Puede ser un valor futuro. **Similar a un timestamp.** 
- **jti?: string**: Identificador String del propio objeto Verifiable Presentation que se está generando.

La función añade los siguientes valores context a los ya introducidos:


'[https://www.w3.org/2018/credentials/v1](https://www.w3.org/2018/credentials/v1 "https://www.w3.org/2018/credentials/v1")'

'[https://alastria.github.io/identity/credentials/v1](https://alastria.github.io/identity/credentials/v1 "https://alastria.github.io/identity/credentials/v1")'  



**La función crea los siguientes valores type, tipos propios de toda VerifiablePresentation de Alastria:**
```
'VerifiablePresentation'
```
```
'AlastriaVerifiablePresentation'  
```

La función extrae la clave pública del valor jwk como una address con prefijo hexadecimal '0x'.
Tras ello, genera el valor jwt de manera análoga a la creación de otros tokens Alastria descritos anteriormente, cifrando con ES256K y en formato JWT, añadiendo **iat** como valor fecha actual a la carga, el cual actúa como un **timestamp** marcando el momento de creación del token.


## createPresentationRequest  

Los parámetros de entrada de esta función corresponden a aquellos necesarios para modelar el JWT correspondiente a una Verifiable Presentation Request tal y como especifica W3C.


- **iss: string**: Es la entidad que envía la Presentation Request. Se trata de un DID en formato URI (ejemplo: `did:alastria:quorum:redt:QmeeasCZ9jLbX...ueBJ7d7csxhb`). Tal y como se especifica en el modelo de datos de W3C para JWT.
- **context: string[]**: Se especifica el context tal y como está definido en el modelo de datos de W3C para JWT. Los context propios de Alastria se definen más adelante por la propia función, luego no es necesario añadirlos en los parámetros de entrada.
- **procUrl: string**: Se trata de una URL externa que contiene un documento donde se especifica el propósito de los datos que el Service Provider está recibiendo. El formato es una URL tal que: ```"https://www.empresa.com/alastria/businessprocess/4583"```.
- **procHash: string**: Se trata del resumen hash del documento  donde se especifica el propósito de los datos que el Service Provider está solicitando. El formato es un String tal que ```H398sjHd...kldjUYn475n```.
- **data: object[]**: Es la estructura en formato de JSON Array, que contiene los datos reales de la Presentation Request. Es una estructura de la siguiente forma:
 ```
 "data": [  
	{  
		"@context": "JWT",  
		"levelOfAssurance": 3,  
		"required": true,  
		"field_name": "name"  
	},  
	{  
		"@context": "JWT",  
		"levelOfAssurance": 3,  
		"required": true,  
		"field_name": "email"  
	}  
]
```
- **cbu: string**: URL de devolución de llamada del usuario. Por ejemplo: "[https://serviceprovider.alastria.blockchainbyeveris.io/api/login/](https://serviceprovider.alastria.blockchainbyeveris.io/api/login/ "https://serviceprovider.alastria.blockchainbyeveris.io/api/login/")"
- **type: string[]**: Especifica el tipo de fihero tal y como se define en el modelo de datos de W3C. Los tipos VerifiablePresentationRequest y AlastriaVerifiablePresentationRequest son añadidos por la propia función. De este modo, solo es necesario especificar otros tipos específicos aquí.

Parámetros opcionales: 

- **kid?: string**: Indica la clave del iss, en formato URI, empleada para firmar el JWT de la credencial. Tal y como se especifica en el modelo de datos de W3C para JWT. Normalmente es el Service Provider.
- **jwk?: string**: Clave pública de la entidad ***iss*** en formato String hexadecimal descomprimido. 
- **exp?: number**: Codificado en UNIX timestamp (`NumericDate`). Es la fecha de expiración, y por lo tanto, la fecha en la que la Presentation Request deja de ser válida y, por lo tanto, deja ser aceptada para ser procesada.
- **nbf?: number**: Codificado en UNIX timestamp (`NumericDate`). Es la fecha de activación, es decir, el momento en el que la Presentation Request comienza a ser válida. Puede ser un valor futuro. 
- **jti?: string**: Identificador String del propio objeto Verifiable Presentation Request que se está generando.

La función añade los siguientes valores context a los ya introducidos:


'[https://www.w3.org/2018/credentials/v1](https://www.w3.org/2018/credentials/v1 "https://www.w3.org/2018/credentials/v1")'

'[https://alastria.github.io/identity/credentials/v1](https://alastria.github.io/identity/credentials/v1 "https://alastria.github.io/identity/credentials/v1")'  



**La función crea los siguientes valores type, tipos propios de toda PresentationRequest de Alastria:**
```
'VerifiablePresentationRequest'
```
```
'AlastriaVerifiablePresentationRequest'  
```

La función extrae la clave pública del valor jwk como una address con prefijo hexadecimal '0x'.
Tras ello, genera el valor jwt de manera análoga a la creación de otros tokens Alastria descritos anteriormente, cifrando con ES256K y en formato JWT, añadiendo **iat** como valor fecha actual a la carga, el cual actúa como un **timestamp** marcando el momento de creación del token.

## PSMHash 
Función que recibe como parámetros de entrada web3, un JWT y una Decentralized ID.
Usando Web3 genera el resumen hash SHA3 de un JSON resultado de concatenar el JWT y la DID.


## createAIC
Función que crea una Alastria Identity (AlastriaIdentityCreation)
Recibe los siguientes parámetros como entrada, los cuales emplea para modelar el JWT correspondiente.

- **context: string[]**: Se especifica el context tal y como está definido en el modelo de datos de W3C para JWT. Los context propios de Alastria se definen más adelante por la propia función, luego no es necesario añadirlos en los parámetros de entrada.
- **type: string[]**: Especifica el tipo de fichero tal y como se define en el modelo de datos de W3C. El tipo de AlastriaIdentityCreation es añadido por la propia función. De este modo, solo es necesario especificar otros tipos específicos aquí.
- **createAlastriaTX: string**: Transacción de la generación de un Token Alastria asociado a la entidad también asociada al token createAIC. 
- **alastriaToken: string**: Token Alastria Verificado. Resultado de aplicar ```signJWT(signedAT, privateKey2)```, donde:
	- **signedAT** es un Token Alastria firmado, resultado de ```signJWT(at, privateKey1)``` donde:
		- **at** es un token JWT generado mediante ```createAlastriaToken```.
		- **privateKey1** es la clave privada de la entidad creadora del Token Alastria ***at***.
	- **privateKey2** corresponde a la clave privada de la entidad que también está generando este token. De este modo la entidad creadora de la Identidad Alastria verifica el Token Alastria anterior al firmarlo.
- **publicKey: string**: Clave pública de la entidad firmante. En formato String hexadecimal. Un ejemplo sería:
```
0xa33e56a80b9dc83a4456265d877c0765cea76146e625572fc679804f8867222ca3c816433a9b6e6690b0b8e919ffa874982706e812314aae09d85fc62fc4fa3c
```

	

Parámetros opcionales: 
- **kid?: string**: Indica la clave del iss, en formato URI, empleada para firmar el JWT de la credencial. Tal y como se especifica en el modelo de datos de W3C para JWT. 
- **jwk?: string**: Clave pública de la entidad ***iss*** en formato String hexadecimal descomprimido. 
- **jti?: string**: Identificador String del propio objeto Alastria Identity Creation que se está generando.
- **iat?: number**: Fecha actual y momento de activación del token. Similar a una **timestamp**.
- **exp?: number**: Codificado en UNIX timestamp (`NumericDate`). Es la fecha de expiración, y por lo tanto, la fecha en la que la Identidad Alastria deja de ser válida.
- **nbf?: number**: Codificado en UNIX timestamp (`NumericDate`). Es la fecha de activación, es decir, el momento en el que la Identidad Alastria comienza a ser válida. Puede ser un valor futuro. 

Además de los context añadidos en la entrada, la función incluye el siguiente:
'[https://alastria.github.io/identity/artifacts/v1](https://alastria.github.io/identity/artifacts/v1 "https://alastria.github.io/identity/artifacts/v1")'  

Además de los types añadidos en la entrada, la función incluye el siguiente:
```'AlastriaIdentityCreation'``` 

La función extrae la clave pública del valor jwk como una address con prefijo hexadecimal '0x'.
Tras ello, genera el valor jwt de manera análoga a la creación de otros tokens Alastria descritos anteriormente, cifrando con ES256K y en formato JWT.


Tras la creación de un token con esta librería, se debieran enviar dos transacciones a la blockchain.
La primera prepareAlastriaID firmada, la segunda createAlastriaID. Estas funciones son propias de transactionFactory.identityManager, en txFactory. Aquí se especifican los valores propios de una transacción.


## createDID
Función que crea una identidad descentralizada. Recibe como parámetros de entrada el nombre de la red, la dirección del proxy y el identificador de la red.
Un ejemplo de cada uno de estos valores sería:

- **Network:** 'quor'
- **ProxyAddress:** AlastriaIdentity.slice(26)
- **NetworkID:** 'redT'


> Written with [StackEdit](https://stackedit.io/).
