# Hyperledger
> Estudio de tecnologías de SSI Indy && Aries, y diferencias respecto AlastriaID. Conocer si existen los mismos actores/verbos que en SSI AlastriaID, modelo de datos conforme a W3C y forma de emisión de Credenciales VErificables Cualficadas (en caso de que exista este concepto)


Hyperledger es una comunidad Open Source dedicada al desarrollo de frameworks, herramientas y librerías orientadas al desarrollo blockchain en empresas.


---------
## Hyperledger VS Ethereum VS Bitcoin

- **Bitcoin** se enfoca en **dinero** descentralizado.

- **Ethereum** se enfoca en **aplicaciones** descentralizadas.

- **Hyperledger Indy** se enfoca en **identidad** descentralizada.

En esta imagen pueden verse las diferencias entre las distintas redes Blockchain principales existentes. Estas se pueden clasificar como públicas o privadas y con o sin permisos.

![Diferencias](./BTCETHINDY.png)

Indy es una red Blockchain pública y con permisos. Esto implica que cualquier usuario puede formar parte de ella, pero solo poseerá ciertas capacidades dentro, las cuales vendrán especificadas por el tipo de permisos que posea.


-------------------------------------
## Hyperledger Indy

[Chat oficial para discutir sobre Hyperledger Indy](https://chat.hyperledger.org/channel/indy)

Ledger orientado a Identidad, aunque puede usarse para otros propósitos.

Se divide en dos componentes:

- Indy-Plenum: Protocolo de consenso y Ledger.


- Indy-Node: Depende de Indy-Plenum. Las transacciones que maneja son específicamente sobre Identidad.


Indy está escrito en Python, con dependencias de ZMQ, Indy-Crypto (Ursa) y Libsodium.
Existen varios tests para probar el ledger.
Posee arquitectura modular y message-driven


El hecho de ser una red permisionada, lleva a la existencia de distintos tipos de nodos.

- **Nodos Validadores**. Son aquellos que participan en el consenso. Poseen la capacidad de lectura y escritura en el ledger.
Basados en TCP y encriptado de autenticación en lugar de firmas digitales (*Poly1305 MAC*).
Cada nodo tiene replicado todos los ledgers, y cada uno de estos ledgers tiene un Árbol de Merkle. La mayoría de estos ledgers posee, además, un estado basado en Patricia Merkle Trie.
Poseen implementado el Plenum Consensus Protocol (RBTF) y BLS sigs para verificar los datos escritos en el Ledger, siendo así parte del consenso. 


- **Nodos Observadores**. Son aquellos que obtienen el estado del ledger de los Nodos Validadores de manera síncrona. Poseen la capacidad de lectura del ledger.


Para evitar un ataque de 51%, aquí es necesario poseer 3F+1 nodos, donde F es el número de nodos maliciosos.

Cuando se realiza una petición de **escritura**, como una transacción, esta se envía a los nodos validadores. 

El cliente que solicita la escritura de esa transacción, la firma previamente de manera múltiple con el algoritmo de firma digital Ed25519, se envía a todos los nodos validadores y la respuesta admitida es aquella dada por la mayoría de estos nodos.


Para realizar una petición de **lectura**, el solicitante no necesita firmar la transacción petición. Esta es enviada a un nodo validador, y con una única respuesta de un nodo validador es suficiente. Esta respuesta contiene agregada la firma BLS y la prueba de estado de auditoría, con el fin de que el cliente que solicitó la lectura pueda corroborar la veracidad del contenido obtenido como respuesta.


De este modo, la autenticación se realiza según la prueba presentada junto a la transacción. Si esta prueba es una firma digital, se contrasta con la clave pública almacenada en el Ledger, la cual va asociada al mismo DID de la transacción.


El hecho de que en una petición de lectura solo responda un único nodo incrementa la eficiencia y rapidez de respuesta.

La autorización asociada al rol se encuentra asociada al DID.

-----


Existen varios ledger con distinta información contenida, cada uno gestiona unos aspectos de Indy:

- Audit Ledger. Gestiona al resto de ledgers.

- Pool Ledger. Transacciones para cada uno de los nodos en la pool. Contiene lo referente a añadir, editar o eliminar nodos y su información. Es donde están las claves públicas para validar DIDs.

- Config Ledger. Configuraciones referentes a los parámetros de las pools. Es usado en la validación de transacciones.

- Domain Ledger. Datos sobre transacciones específicas referentes a identidad y datos sobre transacciones específicas referentes a aplicaciones.


El State o estado es el almacenamiento del orden de las transacciones llevadas a cabo.





### **Protocolo de consenso**


El protocolo de consenso está basado en BFT, es decir, todos los nodos desconfían de todos. Es el conocido protocolo bizantino.


Dentro de este, la variante usada es RBFT (Redundant Byzantine ault Tolerance).

Su funcionamiento es más óptimo que Proof-of-Work. Así es mejor en redes permissioned.
res fases de commit. 
El protocolo, al ser leader based, es susceptible de que el líder sea malicioso o pueda desconectarse, por lo tanto validando transacciones que no debieran ser válidas o a la inversa.
Para evitar esto, este nodo primario se va cambiando, realizando varias validaciones o views (de ahí el redundante), cada una con un nodo como primario. asi cada view tiene un primary distinto.

![Protocolo de consenso RBFT](./protocoloConsensoRBFT.png)

Cuando el contenido de un nodo Replica es igual al del Primary, su contenido se cambia, reordenando las transacciones como dictamina el primario.



La estructura del Árbol de Merkle que utiliza Indy emplea BLS aggregated signature. 
Esto significa que el nodo raíz del Patricia Merkle Trie está firmado por todos los nodos validadores.
De este modo, cuando un usuario solicita la lectura del ledger, y solo le responde un único nodo cuando se efectúa esa transacción, este puede comprobar que esa firma pertenece a la raíz del árbol.
Además, para evitar que un nodo validador maligno envíe una respuesta maliciosa, de intentar hacer eso, este valor raíz del árbol de su copia del ledger cambiaría. Así, cualquier usuario puede comprobar la raíz del árbol y si este no coincide con el que debe ser, rechazar ese resultado malicioso.
Al leer ve que la firma ta bien porque esa proof pertenece al patricia merkle tree. 


--------------------

## Hyperledger Aries

[Chat oficial para discutir sobre Hyperledger Aries](https://chat.hyperledger.org/channel/aries)




Se trata de una infraestructura para interacciones peer-to-peer basadas en blockchain.




Incluye:

- Una interfaz blockchain para crear y firmar transacciones blockchain.
- Una wallet de almacenamiento seguro para guardar la informacion criptográfica secreta necesaria para construir clientes blockchain.
- Un sistema de mensajes cifrados para interacciones fuera del ledger entre clientes, utilizando varios protocolos de transporte.
- Una implementación de Credenciales Verificables usando el **modelo de datos W3C** y siendo ZKP-capable, empleando las ZKP primitivas que se encuentran en Hyperledger Ursa.
- Una implementación de la especificación Decentralized Key Management System (DKMS), actualmente siendo incubada en Hyperledger Indy.
- Un mecanismo para construir protocolos de alto nivel y casos de uso para APIs basados en la funcionalidad de mensajería segura mencionada anteriormente.




Posee una comunidad open-source para elaborar frameworks, herramientas y librerías para la creación de entidades y credenciales propias de la Self-Sovereign Identity.
Facilita el intercambio de datos, funcionando peer-to-peer sobre la estructura de blockchain Hyperledger Indy.

De este modo puede acceder a la información almacenada en el ledger (libro de contabilidad pública) de Indy.



![Ecosistema Hyperledger](./hyperledgereco.png)


Hyperledger Aries especifica lo siguiente:
- Agentes que interaccionan, propios de la Identidad Autosoberana: Issuer, Holder y Verifier.  
- DIDcomm. 
- Protocolos. 
- Administración de claves.

Hyperledger Aries engloba lo siguiente:
- RFC's referentes a los anteriores puntos.
- Frameworks para el manejo de agentes.
- Herramientas de desarrollo.
- Librerías.


Todas estas herramientas permiten la creación de entidades cuyos DIDs puedan registrarse en el Ledger como Issuers, Holders o Verifiers.
También permite la creacion y emisión de Credenciales Verificables acorde al modelo de Identidad Autosoberana.


[Repositorio para generación de Issuer](https://github.com/bcgov/aries-vcr-issuer-controller)


[Repositorio para generación de VCs](https://github.com/bcgov/aries-vcr/tree/master/docs)


[Crear registro de credencial](https://github.com/bcgov/aries-vcr/blob/master/docs/create-new-credential-registry.md)








-----------------
## Modelo de datos usado. W3C

Como pudo verse, Hyperledger Aries implementa las VCs tal y como el modelo de W3C especifica. Incorpora a esto el uso de ZeroKnowledge Proofs (ZKP) usando Hyperledger Ursa.



--------------
## Hyperledger VS AlastriaID


Hyperledger y AlatriaID son dos ecosistemas con bastante en común y una principal diferencia en los despliegues de sus aplicaciones y subida de sus transacciones.

La estructura, en ambos casos, para hacer uso de la Identidad Autosoberana, consiste en Decentralized IDentifiers (DIDs) asociados a la información blockchain; y protocolos bizantinos para la validación de las transacciones.

Poseen también en común la estructura de la Identidad Autosoberana, tratando el mismo tipo de entidades Issuer, Holder y Verifier; y de Credenciales Verificables.


Hyperledger funciona utilizando dockers y python. AlastriaID si bien posee está opción, está todavía por desarrollarse en las librerías de python, funcionando actualmente con javascript y node.

Por otro lado, Hyperledger funciona sobre su propia red pública y permisionada de blockchain, Hyperledger Indy.
AlastriaID sin embargo, funciona con Smart Contracts solidity desplegados en la red de Ethereum. 




MENCIONAR CNIL y lo de que mejor permisioned o privadas a publicas.
CACNEA VENTAJAS DE BLOCKCHAIN PUBLICA y no permisioanda FRENTE A publica y PERMISIONADA


---------------------------
## Emisión de Credenciales Verificables Cualificadas
Como pudo verse, la implementación de Hyperledger Aries se basa en el modelo de W3C para VCs. 
Al hacer uso de este modelo, de forma derivada se pueden crear VCs cualificadas tal y como eIDAS describe para W3C, usando proofs en formato de datos enlazados.

Es por ello que el modo de generarlas sería igual, la única diferencia es a qué blockchain se suben las transacciones.



------------------------
## Identidad Digital Federada
> Como añadido, ver la implementacion de Identidad Digital FEDERADA (Ver estudio Tecnlia) tecnalia?

CACNEA
