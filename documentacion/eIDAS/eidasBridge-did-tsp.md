# eIDAS, DID y SSI
La evolución hacia un sistema basado en SSI y DID provoca que eIDAS deba adaptarse para regular los nuevos modos de identificación electrónica.
Esta nueva SSI debe cumplir con los principios de privacidad, seguridad, escalabilidad, transportabilidad y uniformidad. Ante todo debe permitir que los usuarios sean los verdaderos propietarios de sus datos e identidad digital.

Para que las credenciales que los usuarios (o holders) presentan ante un Provider sean realmente legales y de confianza, estas deben haber sido **emitidas y firmadas por Issuers comprobados**, sea mediante **firmas avanzadas o cualificadas**. También deberá haber una **comprobación de que la VC presentada ante un Provider haya sido comprobada legalmente.** 


Es aquí donde eIDAS entra en acción, comprobando que estas entidades tengan sus DIDs asociados a entidades cualificadas y a personas legales comprobadas.


# eIDAS Bridge
La propuesta desarrollada por eIDAS para certificar la validez de un Issuer o la veracidad sobre los datos presentados ante un Provider es **eIDAS Bridge**. 



eIDAS Bridge consiste en un proceso intermediario de verificación, donde la identidad del Issuer es contrastada con un listado de Issuers validados y cualificados por eIDAS.
De este modo, la identidad del Issuer queda confirmada y con ello la firma que valida la credencial verificable que haya emitido al Holder.

Esta comprobación consta de dos pasos.
El primero, al **generar el certificado cualificado del Issuer**.
El segundo, al **firmar o sellar VCs con la clave privada de dicho certificado cualificado**. 
En ambos pasos el Issuer es asistido por eIDAS Bridge.


Por otro lado, eIDAS Bridge también verifica el envío de las credenciales de Holders a Providers. Esta comprobación se lleva a cabo con el fin de ayudar al Provider en el proceso de verificación de una VC. Para ello, **eIDAS asiste al Provider en el momento de la extracción y validación del Certificado Cualificado**, comprobando que se encuentre en las **_EU Trusted Lists_**.

![](https://joinup.ec.europa.eu/sites/default/files/styles/wysiwyg_full_width/public/inline-images/eidas-bridge.png?itok=3pfpLm-7)


Como puede verse en el esquema final, eIDAS Bridge actúa como intermediario en todas las interacciones relacionadas con un Holder a la hora de gestionar sus Verifiable Credentials.

La firma cualificada de un Issuer aprobado por eIDAS se adjuntaría a la VC como una prueba de su validez.


# eIDAS, TSPs y sellos

Esta aprobación que demuestra la legalidad de las VCs se logra mediante el **uso de firmas electrónicas avanzadas o cualificadas (personas físicas) o sellos avanzados o cualificados (personas jurídicas).**

Aquellos Issuer considerados **Trust Service Providers cualificados** podrán hacer uso de certificados cualificados.

**Esta firma electrónica o sello podrá adjuntarse a la VC como datos enlazados (Linked Data).**

Según [eIDAS AdES Formats Decision](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&ved=2ahUKEwizweKuqdDxAhUI3xoKHY6pCCMQFjAAegQIAxAD&url=https%3A%2F%2Fwww.enisa.europa.eu%2Fpublications%2Fassessment-of-standards-related-to-eidas-i%2Fat_download%2FfullReport&usg=AOvVaw0QEzS44ifmTm8rGEPR9eJ8), los formatos aceptados para firmas digitales todavía no incluyen Linked Data, siendo los aceptados XAdES (XML), PAdES (PDF), CAdES (CMS) y ASiC Containers.
De todos modos, puede ser considerado como _Otro Formato_ en las condiciones del artículo 2 a la vez que se benefician de los aspectos legales definidos en los artículos 27 y 32 de la regulación eIDAS cuando se usan VCs en el contexto de procedimientos públicos.


En cualquier caso, estas _proofs_ serán **comprobadas usando el certificado cualificado del Issuer**, el cual debe ser accesible y útil para cualquiera de las partes.


Para que esto sea así, el documento DID del Issuer debe contener información sobre algún **repositorio (llamado _identity hub_) público** donde se almacene el certificado, y algún atributo público donde se especifique el **nivel de seguridad (_level of assurance_) de la clave.**

# eIDAS y sellos como prueba en VCs
El uso de sellos (de personas  jurídicas) o firmas digitales (personas físicas) es representado como datos enlazados (**Linked Data**). De este modo, una prueba (_proof_) adjuntada a la VC (además de la firma JWS que acompaña la transacción) sería el sello que valida la VC y a su vez demuestra que ha sido emitida por un Issuer cualificado.

Estas proofs poseen la siguiente estructura según [W3C](https://w3c-ccg.github.io/ld-proofs/#linked-data-signatures).

* **type**. Obligatorio. Es una URI que identifica el tipo de suite criptográfica empleada.
* **created**. Obligatorio. Es un valor String según la norma **_ISO8601_** que representa una fecha y hora generadas según el [Proof Algorithm](https://w3c-ccg.github.io/ld-proofs/#proof-algorithm).
* **domain**. Opcional. En caso de ser necesario restringir el uso de una VC a un dominio concreto, se especificará en este campo. Dicho dominio puede variar desde un dominio web (como `ejemplo.com`), ad-hoc (como `mycorp-level3-access`) o un valor específico de para una transacción (como `8zF6T$mqP`). También puede restringir a un tipo concreto de destinatarios.
* **nonce**. Opcional pero altamente recomendado. Valor String que debe ser usado una única vez para un dominio particular y ventana temporal. Se debe incluir en la firma digital.
* **signature value**. Obligatorio. Representación válida del valor de firma tal y como se especifica en [Proof Algorithm](https://w3c-ccg.github.io/ld-proofs/#proof-algorithm).

Un ejemplo de este tipo de pruebas es:
```
{
  "@context": [
    {"title": "https://schema.org#title"},
    "https://w3id.org/security/suites/ed25519-2020/v1"
  ],
  "title": "Hello world!",
  "proof": {
    "type": "Ed25519Signature2020",
    "created": "2020-11-05T19:23:24Z",
    "verificationMethod": "https://ldi.example/issuer#z6MkjLrk3gKS2nnkeWcmcxi
      ZPGskmesDpuwRBorgHxUXfxnG",
    "proofPurpose": "assertionMethod",
    "proofValue": "z4oey5q2M3XKaxup3tmzN4DRFTLVqpLMweBrSxMY2xHX5XTYVQeVbY8nQA
      VHMrXFkXJpmEcqdoDwLWxaqA3Q1geV6"
  }
}
```
> **Nota:** debido a que el documento W3C referente a esta normativa no está terminado y por lo tanto aún no es oficial, algunas nomenclaturas pueden variar, como es el caso de _signature value_ en la enumeración a _proofValue_ en el ejemplo.


## Bibliografía


* [joinup.ec.europe.eu - SSI Eidas Bridge](https://joinup.ec.europa.eu/collection/ssi-eidas-bridge/about)


* [SSI-eIDAS legal report final](https://joinup.ec.europa.eu/sites/default/files/document/2020-04/SSI_eIDAS_legal_report_final_0.pdf)

* [SSI eIDAS bridge by ESSIF-LAB - NGI](https://essif-lab.eu/ssi-eidas-bridge-by-validated-id-s-l/)

* [Europa - CEFDigital - EBSI](https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/EBSI)

* [Europa - CEFDigital - EBSI - Lista de representantes por país](https://ec.europa.eu/cefdigital/wiki/display/EBSIDOC/List+of+EBP+representatives)

* [W3C - Linked Data Signatures as VC Proof](https://w3c-ccg.github.io/ld-proofs/#linked-data-signatures)


>Mashenka-ad