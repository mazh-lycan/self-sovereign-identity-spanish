# Verifiable Credentials de salud


## Iniciativas existentes de VC de salud

>Conocer iniciativas (España/UE/Otros)  

- [**COVID-19 Credentials Initiative**](https://www.covidcreds.org/): Comunidad global _open source_ que colabora para permitir la **interoperabilidad** entre VC basadas en W3C orientadas al sector de la salud pública. Posibilidad de convertirse en un miembro de esta comunidad. Es una entidad legal con bastante impulso y credibilidad.
**Pertenecen a la Linux Foundation Public Health (LFPH).** 

- [**VCI**](https://vci.org/): Coalición voluntaria entre organizaciones públicas y privadas que colaboran por el desarrollo de **estándares** relacionados con las VC de salud y su **acercamiento a los usuarios**. Han liderado y colaborado en el desarrollo de: 
	-  [**Smart Health Cards Framework Implementation Guide**](https://smarthealth.cards/) Las cuales cumplen con los protocolos de:
		- [**W3C**](https://www.w3.org/TR/vc-data-model/).
		- [**Health Level 7 SMART en los estándares FHIR**](https://hl7.org/FHIR/).
	- [**Smart Health Cards Vaccination and Testing Implementation Guide**](http://build.fhir.org/ig/dvci/vaccine-credential-ig/branches/main/).

- [**Dock**](https://www.dock.io/): Tecnología blockchain open source **alternativa a Ethereum**, orientada en gran medida a Verifiable Credentials. Entre sus casos de uso, el más destacado es el de las VCs del sector de la salud. Utilizan el estándar de W3C para VCs, así como su modelo de datos. 
	
- [**Verified Credentials Candidate Verification Center**](https://www.verifiedcredentials.com/candidate-verification-center-cvc/). Centro que proporciona a empresas la implementación de un generador de VCs, donde sus usuarios pueden introducir su información y firmarla digitalmente. Está orientado a la búsqueda de candidatos para puestos de trabajo. 

	Este centro también posee un [servicio](https://www.verifiedcredentials.com/healthcare/) de solicitud de información y supervisión del cumplimiento normativo entorno a las VCs de salud.


- [**Verifiable**](https://verifiable.com/): Enfocada a manejar datos de los proveedores asociados, con la posibilidad de gestionar las credenciales asociadas a estos. Varias innovadoras compañías de salud asociadas. Uso de **timestamps** para cada una de las acciones que se lleven a cabo. Verificadores (entre otros) de:
	- Neuropsychiatric Inventory, _NPI_.
	- Licencias de estado.
	- Drug Enforcement Administration, _DEA_.
	- Certificaciones de Junta.
	- Número de la Seguridad Social.
	- Office of Inspector General’s, _OIG_.
	- The GSA’s System for Award Management, _SAM_.
	- National Practitioner Data Bank, _NPDB_.
	- Office of Foreign Assets Control, _OFAC_.
	
- [**Spherity**](https://spherity.com/): Producto inicialmente orientado a ayudar a compañías a cumplir el **Drug Supply Chain Security Act (DSCSA)**. También proveen de una Cloud Identity Wallet. Su Credential Issuer es LEGISYM.  
  
	- [**LEGISYM**](https://legisym.com/): Credential Issuer especializado en trato con compañías de **farmacología**.




## Existencia de estándares

>Ver si hay algún estándar desde W3C, u otros  

Todas las propuestas orientadas a Verifiable Credentials hacen uso del estándar de W3C, siendo este además impulsado por [**COVID-19 Credentials Initiative**](https://www.covidcreds.org/) buscando la uniformidad de las VCs.

HL7 es un estándar implementado y seguido por algunas de las propuestas, como es el caso de [**VCI**](https://vci.org/) en el desarrollo de sus Smart Health Cards. 


 Cuando se desarrollan VCs siguiendo incluir que es de tipo salud en el apartado de __type__ como **health**.

### Implementaciones según norma W3C

>Conocer las implementaciones de VC de salud según norma W3C  

En concreto, W3C tiene una [especificación](https://www.w3.org/TR/vc-use-cases/#healthcare) para el caso de uso de las VCs de salud. Ahí se concretan las distintas posibilidades que ofrecen las VCs de salud.

- **H.1 Prescribing**, donde se describe un caso de uso para poseer la certificación necesaria para realizar prescripciones médicas y ser Issuer de VCs de salud.
- **H.2 Online pharmacy**, donde se especifica el proceso de verificación de la autenticidad de una VC de salud firmada por un Issuer como el descrito en H.1, así como los datos del Holder.
- **H.3 Insurance claim**, donde se describe un caso de uso donde se presenta una VC de salud que ha sido previamente aprobada por el Service Provider correspondiente.
- **H.4 Traveling illness**, donde se describe un caso de uso de preservación de la privacidad de aquellos datos no necesarios, así como la minimalidad de los datos y la expiración de validez de una VC de salud transcurridos N días.
- **H.5 Proving Legal Disabiliy Status**, donde se describe un caso de uso donde se provee de una VC que describe si un individuo posee cierta característica, en este caso una discapacidad, sin especificar cuál en concreto.



### Legislación y Normativa

Además de estos estándares, al estar haciendo uso de datos sensibles, es mandatorio cumplir con la normativa implantada a la hora de tratar VCs de salud.

En este sector es imprescindible el cumplimiento de:
- Regulaciones federales.
- Protección de la seguridad y privacidad del paciente.
- Control de riesgos y minimización de responsabilidades.
- Reducir la responsabilidad de riesgos. 


La siguiente normativa aplica sobre el tratamiento que se ha de llevar en el trato con datos sobre el estado COVID-19 de un individuo, y engloba los siguientes aspectos, recogidos en __AB-2004 Medical test results: verification credentials, California Legislative Information__, pero de carácter muy general y por lo tanto aplicables fuera del estado de California.

- Es necesaria la divulgación de los resultados médicos con el fin de prevenir la extensión del foco de contagio, minimizar la transmisión y gestionar los recursos médicos adecuadamente.
- Debido a la sensibilidad de la información previamente citada, esta debe estar sujeta a regulaciones extensivas, tanto federales como estatales, con el fin de proteger los derechos individuales a la privacidad.
- **Los modelos basados en Credenciales Verificables** Criptográficas, como por ejemplo **el modelo desarrollado por W3C**, parecen prometer la provisión de seguridad, privacidad y portabilidad necesarias para comunicar información médica sensible.
- **Las Credenciales Verificables deben proteger** a los individuos de la vigilancia, discriminación y fraude, a la vez que promueven la **accesibilidad** para todos. En ningún momento deberán comprometer el derecho individual a la privacidad. Esto incluye rastreo o conocimiento del uso que hace un usuario de sus VCs.
- Aunque existen especificaciones sobre cómo la información ha de ser tratada y comunicada electrónicamente, la **aplicación práctica de estas como Credenciales Verificables debe ser clarificada**.
- Debido a la gran demanda de inmediatez que requiere la comunicación de este tipo de información, es mandatorio desarrollar un estándar para la estructura técnica, y prácticas para el uso de esta tecnología que asegure **la comunicación de los resultados médicos (incluido COVID-19) a tiempo y sin retardos**.



Las siguientes definiciones son especificadas en _SEC. 2. Section 11546.10 is added to the California_ Government Code, immediately following Section 11546.9_.
- **Verifiable Health Credential**. Registro eléctronico y portátil de un paciente, autorizado por un centro de salud **proveedor** al paciente o a un representante del paciente, tal y como se define en __Section 123105 of the Health and Safety Code__, por el cual la autenticidad del registro pueda ser verificada criptográficamente de forma inependiente.
- **Authorized Health Care Provider**. Implica al propietario de un certificado de cirugía, médico, enfermero, asistente médico, o cualquier otro proveedor de salur licenciado que esté profesionalmente autorizado en prácticas médicas bajo la jurisdicción del __Department of Consumer Affairs__ (departamento correspondiente en **California**), y cuya licencia y nombre hayan sido incluidos en el registro de un **Verifiable Issuer** sobre proveedores autorizados a emitir VCs de salud.
- **Verifiable Issuer Registry**. Este es un repositorio de las licencias actuales que representan a los proveedores que sean autoridades de cuidados sanitarios mantenidos por sus respectivas agencias de licencia, con el fin de comprobar que VCs de salud deben ser comprobadas para **confirmar la autenticidad verificando la identidad y estado de autorización del Issuer de la credencial**.
- **Law Enforcement Agency**. (Agencia de aplicación de la ley) **NO INCLUIRÁ** una Agencia de aplicación de la ley **federal**.


En dicho apartado, tras aclarar las anteriores definiciones, se da paso a una especificación sobre cómo se habrá de proceder en la búsqueda de un modelo de datos para VCs de salud, donde se pone como fecha límite a la investigación el 1 de julio de 2022. Dichas especificaciones se encuentran en esta [página, __SEC. 2. Section 11546.10__ apartado __(b)__ en adelante](https://leginfo.legislature.ca.gov/faces/billCompareClient.xhtml?bill_id=201920200AB2004&showamends=false), siendo el planning concreto desarrollado por California.


## Implementación Indy/Aries (Hyperledger)

>Conocer implementación Indy/Aries (Hyperledger)  



### Hyperledger Aries

Hyperledger Aries provee un kit de herramientas compartido, interoperable y reutilizable, el cual proporciona iniciativas y soluciones enfocadas en la gestión y creación de VCs.
Su infraestructura se sostiene en blockchain e interacciones peer-to-peer.

Va de la mano de __Hyperleger Ursa__, que provee lo necesario para gestionar la seguridad y criptografía y cifrado.

La implementación de las comunicaciones entre DIDs empleando Hyperledger Aries pueden encontrarse en este [repositorio](https://github.com/hyperledger/aries).


### Hyperledger Indy

Hyperledger Indy provee de herramientas, librerías y componentes con el fin de hacer interoperables a distintas identidades digitales desplegadas sobre redes blockchain u otros sistemas descentralizados. 

Es necesario desplegar previamente las comunicaciones con Hyperledger Aries. Las especificaciones sobre el uso de Hyperledger Indy pueden encontrarse en este [repositorio](https://github.com/hyperledger/indy-sdk/blob/master/docs/getting-started/indy-walkthrough.md).



## Posibilidad de modelar el estándar HL7 FHIR (versión 4) como Verifiable Credentials

>Ver si se puede modelar en VC el estándar HL7 (versión 4)  

Health Level 7 es un conjunto de estándares creados con el fin de facilitar el intercambio de información clínica de manera electrónica.

Dentro de sus varias versiones e implementaciones, Fast Healthcare Interoperability Resources o __FHIR__, define un conjunto de recursos que representan conceptos clínicos granulares. Este define los conceptos en formato XML o JSON, pudiendo así ser compatibles con el formato del modelo de datos definido por W3C. 

Su diseño está basado en los servicios web Representational State Transfer (RESTful).

Dentro de la [guía de implementación de HL7](https://www.hl7.org/fhir/implementationguide.html).


El uso del estándar HL7 puede facilitarse con el uso de [Smart Health Cards](https://smarthealth.cards/), cuyo desarrollo ha sido liderado por el grupo [VCI](https://vci.org/). Esta implementación puede verse en [este enlace](http://build.fhir.org/ig/dvci/vaccine-credential-ig/branches/main/)

Su framework emplea el estándar de W3C. La información que almacenan es la siguiente.
- Nombre.
- Fecha de nacimiento.
- La siguiente información clínica:
	- Tests. Fecha, quién lo ha generado y resultado.
	- Vacunas recibidas. Tipo, fecha y localización.


**NUNCA** deben incluir la siguiente información.
- Número de teléfono.
- Dirección.
- Identificador Nacional.
- Cualquier otra información sanitaria.

El uso de estas Smart Health Cards implica la existencia de JSON públicos que puedan usarse desde cualquier Health Card para verificar la autenticidad de una firma. Su accesibilidad debe ser gratuita.

La implementación del modelo de estas Smart Health Cards puede encontrarse en este [enlace](https://spec.smarthealth.cards/).


-----
### Notas:  
  
- Edumm Dred es CEO de Everymint, que patrocina Indy  
- Estamos en contacto con Nuria Sala Cano, Fundacion Unid  
- Referencia "200310_IdentidadDigital-Tecnalia.docx" y otras versiones (memoria*.docx)


----
### Bibliografía

- [Especificación Smart Health Cards](https://spec.smarthealth.cards/)

- [Github Smart Health Cards](https://github.com/dvci/vaccine-credential-ig)

- [VCI](https://vci.org/)

- [COVID-19 Credentials Initiative](https://www.covidcreds.org/)

- [Repositorio Hyperledger Indy](https://github.com/hyperledger/indy-sdk/blob/master/docs/getting-started/indy-walkthrough.md)

- [Repositorio Hyperledger Aries](https://github.com/hyperledger/aries).

--------
> Mashenka-ad
