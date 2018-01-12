# eNotumLite

# Introducció

A continuació és descriu el funcionament, el cicle de vida de les notificacions, els errors així com les diferents modalitats de consum del **eNotumLite**.

## Integració PCI

Tal i com es reflecteix a la següent figura, el Backend d'**eNotumLite** s'integra dins de l'arquitectura de la Plataforma de Col·laboració Interadministrativa (en endavant _PCI_) a mode d'un nou servei accessible a través de la MTI. 

Per tant els integradors que vulguin accedir al **eNotumLite** ho hauran de fer a través de la missatgeria de la _PCI_ utilitzant l'element `<DatosEspecificos>` d'aquesta, per a més informació podeu consultar [el document d'integració de la _PCI_ aqui](https://www.aoc.cat/knowledge-base/plataforma-de-col-laboracio-administrativa-2/idservei/enotum/)

![eNotum_integracio_pci](https://github.com/ConsorciAOC/eNotum/blob/master/missatgeria/imgs/eNotum_arquitectura_pci.png)

El `<CodigoProducto>` i el `<CodigoCertificado>` de la missatgeria genèrica de _PCI_ per a l'ús d'**eNotumLite** és per ambdòs: 
* **NT-LITE**

## Cicle de vida de les notificacions
![cicle de vida](https://github.com/ConsorciAOC/eNotumLite/blob/master/estats-enotum-lite.png)

## Vida de les notificacions al sistema
El temps de vida de les notificacions al sistema serà de 2 mesos, un cop passat aquest termini és purgarà la informació del sistema i és mantindrà la mateixa en format *.zip* durant un temps addicional per al integrador que la requereixi.

## Estat de les notificacions
| Estat | Descripció | Estat previ |
| ----- | ---------- | ----------- |
| Entrada | Notificació introduïda al sistema mitjançant el servei de Crear Notificació. | Cap |
| Dipositada | Notificació dipositada automàticament mitjançant el motor d'estats. | Entrada |
| Acceptada | Notificació practicada amb motiu [ACCEPTAR] mitjançant el servei de Practicar Notificació o durant la consulta (si la notificació es de tipus [Comunicació]) | Dipositada |
| Rebutjada | Notificació practicada amb motiu [REBUTJAR] mitjançant el servei de Practicar Notificació. | Dipositada |
| Expirada | Notificació expirada automàticament, segons l'atribut [diesExpiracio]. | Dipositada |
| Error | Notificació marcada amb un error, mitjançant el motor d'estats. | Qualsevol |
| Descartada | Notificació cancel·lada mitjançant el servei de Cancel·lar Notificació. | Qualsevol |

## Modalitats de consum
### Petició
Totes les modalitats de consum WS que ofereix **eNotumLite** extenen el següent esquema de petició:
```xml
<xs:complexType name="PeticioType" abstract="true">
	<xs:sequence>
		<xs:element name="Emissor" type="xs:string"/>
	</xs:sequence>
</xs:complexType>
```
Per tant com és pot veure totes les peticions, a banda dels elements específics de cada modalitat de consum, tenen el següent element:
* `//PeticioType/Emissor` que conté el codi de l'emissor.

[Aquí podeu veure la definició complerta de l'esquema Peticio.xsd](https://github.com/ConsorciAOC/eNotumLite/blob/master/xsds/Peticio.xsd)

### Petició Crear Notificació
Aquesta petició permet la creació de notificacions dins del sistema, fins a un màxim de 100 notificacions per petició.
Conté un llistat de notificacions, que expliquem a continuació.

```xml
<xs:element name="PeticioCrearNotificacio">
	<xs:complexType>
		<xs:complexContent>
			<xs:extension base="PeticioType">
				<xs:sequence>
					<xs:element name="Notificacio" type="NotificacioType" maxOccurs="100"/>
				</xs:sequence>
			</xs:extension>
		</xs:complexContent>
	</xs:complexType>
</xs:element>
```

[Aquí podeu veure la definició complerta de l'esquema PeticioCrearNotificacio.xsd](https://github.com/ConsorciAOC/eNotumLite/blob/master/xsds/PeticioCrearNotificacio.xsd)

#### Notificació
| Camp | Descripció |
| ----- | --------- |
| `Titol` | titol de la notificació, 100 caracters com a màxim |
| `Referencia` | referencia de la notificació, 100 caracters com a màxim |
| `IdNotificacioEmissor` | id de la notificació de l'emissor, 50 caracters com a màxim, opcional |
| `TipusObjecte` | tipus de la notificació, valors possibles: NOTIFICACIO, COMUNICACIO |
| `DataCreacio` | data de creació de la notificació, opcional |
| `DiesExpiracio` | nombre de dies per expirar automàticament la notificació, opcional |
| `Destinataris` | llistat de destinataris, com a màxim 5 destinataris |
| `Idioma` | idioma de la notificació, opcional, valors possibles: ca, oc, es, en |
| `DadesOfici` | dades ofici, opcional |
| `DadesAvisos` | dades avisos, opcional |
| `Documents` | llistat de documents de la notificació, màxim 100 documents |

```xml
<xs:complexType name="NotificacioType">
	<xs:all>
		<xs:element name="Titol">
			<xs:simpleType>
				<xs:restriction base="xs:string">
					<xs:maxLength value="100"/>
				</xs:restriction>
			</xs:simpleType>
		</xs:element>
		<xs:element name="Referencia">
			<xs:simpleType>
				<xs:restriction base="xs:string">
					<xs:maxLength value="100"/>
				</xs:restriction>
			</xs:simpleType>
		</xs:element>
		<xs:element name="IdNotificacioEmissor" minOccurs="0">
			<xs:simpleType>
				<xs:restriction base="xs:string">
					<xs:maxLength value="50"/>
				</xs:restriction>
			</xs:simpleType>
		</xs:element>
		<xs:element name="TipusObjecte" type="TipusObjecteType"/>
		<xs:element name="DataCreacio" type="xs:dateTime" minOccurs="0"/>
		<xs:element name="DiesExpiracio" type="xs:positiveInteger" minOccurs="0"/>
		<xs:element name="Destinataris" type="DestinatarisType"/>
		<xs:element name="Idioma" type="Idioma" default="ca" minOccurs="0"/>
		<xs:element name="DadesOfici" minOccurs="0">
			<xs:complexType>
				<xs:sequence>
					<xs:element name="CosNotificacio" type="xs:string" minOccurs="0"/>
					<xs:element name="PeuRecurs" type="xs:string" minOccurs="0"/>
				</xs:sequence>
			</xs:complexType>
		</xs:element>
		<xs:element name="DadesAvisos" minOccurs="0">
			<xs:complexType>
				<xs:sequence>
					<xs:element name="PrimerAvis" type="xs:boolean" default="false"/>
					<xs:element name="RepeticioAvis" type="xs:boolean" default="false"/>
				</xs:sequence>
			</xs:complexType>
		</xs:element>
		<xs:element name="Documents" type="DocumentsType"/>
	</xs:all>
</xs:complexType>
```
#### Destinataris
| Camp | Descripció |
| ---- | ---------- |
| `Destinatari` | persona física o persona juridica (choice) |
| `Destinatari/Idioma` | idioma del destinatari, valors: ca, oc, es, en, opcional |
| `Destinatari/PersonaFisica` | entitat persona fisica |
| `Destinatari/PersonaFisica/BustiaCorreu` | llistat emails, string, màxim 3 busties |
| `Destinatari/PersonaFisica/Telefon` | llistat telefons, string, màxim 3 telefons |
| `Destinatari/PersonaFisica/DocumentIdentificatiu` | nif o passaport (choice) |
| `Destinatari/PersonaFisica/DocumentIdentificatiu /NIF` | nif, string, format validat |
| `Destinatari/PersonaFisica/DocumentIdentificatiu /PASSAPORT` | passaport, string, format validat |
| `Destinatari/PersonaFisica/Nom` | nom destinatari, string |
| `Destinatari/PersonaFisica/PrimerCognom` | primer cognom destinatari, string |
| `Destinatari/PersonaFisica/SegonCognom` | segon cognom destinatari, string, opcional |
| `Destinatari/PersonaJuridica` | entitat persona juridica |
| `Destinatari/PersonaJuridica/BustiaCorreu` | llistat emails, string, màxim 3 busties |
| `Destinatari/PersonaJuridica/Telefon` | llistat telefons, string, màxim 3 telefons |
| `Destinatari/PersonaJuridica/DocumentIdentificatiu` | cif o vat (choice) |
| `Destinatari/PersonaJuridica/DocumentIdentificatiu/CIF` | cif, string, format validat |
| `Destinatari/PersonaJuridica/DocumentIdentificatiu/VAT` | vat, string, format validat |
| `Destinatari/PersonaJuridica/RaoSocial` | rao social, string |
| `Destinatari/PersonaJuridica/PersonaVinculada` | persona vinculada, opcional |
| `Destinatari/PersonaJuridica/PersonaVinculada/DocumentIdentificatiu` | nif o passaport (choice) |
| `Destinatari/PersonaJuridica/PersonaVinculada/DocumentIdentificatiu /NIF` | nif, string, format validat |
| `Destinatari/PersonaJuridica/PersonaVinculada/DocumentIdentificatiu /PASSAPORT` | passaport, string, format validat |
| `Destinatari/PersonaJuridica/PersonaVinculada/Nom` | nom destinatari, string |
| `Destinatari/PersonaJuridica/PersonaVinculada/PrimerCognom` | primer cognom destinatari, string |
| `Destinatari/PersonaJuridica/PersonaVinculada/SegonCognom` | segon cognom destinatari, string, opcional |

```xml
<xs:complexType name="DestinatarisType">
	<xs:sequence>
		<xs:element name="Destinatari" type="DestinatariType" maxOccurs="5"/>
	</xs:sequence>
</xs:complexType>

<xs:complexType name="DestinatariType">
	<xs:sequence>
		<xs:choice>
			<xs:element name="PersonaFisica" type="PersonaFisicaType"/>
			<xs:element name="PersonaJuridica" type="PersonaJuridicaType"/>
		</xs:choice>
		<xs:element name="Idioma" type="Idioma" default="ca" minOccurs="0"/>
	</xs:sequence>
</xs:complexType>

<xs:complexType name="PersonaType" abstract="true">
	<xs:sequence>
		<xs:element name="BustiaCorreu" type="EmailType" minOccurs="0" maxOccurs="3"/>
		<xs:element name="Telefon" type="xs:string" minOccurs="0" maxOccurs="3"/>
	</xs:sequence>
</xs:complexType>

<xs:complexType name="PersonaFisicaType">
	<xs:complexContent>
		<xs:extension base="PersonaType">
			<xs:sequence>
				<xs:element name="DocumentIdentificatiu" type="DocumentPersonaFisicaType"/>
				<xs:element name="Nom" type="xs:string"/>
				<xs:element name="PrimerCognom" type="xs:string"/>
				<xs:element name="SegonCognom" type="xs:string" minOccurs="0"/>
			</xs:sequence>
		</xs:extension>
	</xs:complexContent>
</xs:complexType>

<xs:complexType name="PersonaJuridicaType">
	<xs:complexContent>
		<xs:extension base="PersonaType">
			<xs:sequence>
				<xs:element name="DocumentIdentificatiu" type="DocumentPersonaJuridicaType"/>
				<xs:element name="RaoSocial" type="xs:string" nillable="false" />
				<xs:element name="PersonaVinculada" type="PersonaVinculadaType" minOccurs="0"/>
			</xs:sequence>
		</xs:extension>
	</xs:complexContent>
</xs:complexType>

<xs:complexType name="PersonaVinculadaType">
	<xs:sequence>
		<xs:element name="DocumentIdentificatiu" type="DocumentPersonaFisicaType"/>
		<xs:element name="Nom" type="xs:string"/>
		<xs:element name="PrimerCognom" type="xs:string"/>
		<xs:element name="SegonCognom" type="xs:string" minOccurs="0"/>
	</xs:sequence>
</xs:complexType>
```

#### DadesOfici
| Camp | Descripció |
| ---- | ---------- |
| `DadesOfici/CosNotificacio` | cos notificació, string, opcional |
| `DadesOfici/PeuRecurs` | peu recurs, string, opcional |

```xml
<xs:element name="DadesOfici" minOccurs="0">
	<xs:complexType>
		<xs:sequence>
			<xs:element name="CosNotificacio" type="xs:string" minOccurs="0"/>
			<xs:element name="PeuRecurs" type="xs:string" minOccurs="0"/>
		</xs:sequence>
	</xs:complexType>
</xs:element>
```

#### DadesAvisos
| Camp | Descripció |
| ---- | ---------- |
| `DadesAvisos/PrimerAvis` | primer avis, boolean |
| `DadesAvisos/RepeticioAvis` | repetició avis, boolean |

```xml
<xs:element name="DadesAvisos" minOccurs="0">
	<xs:complexType>
		<xs:sequence>
			<xs:element name="PrimerAvis" type="xs:boolean" default="false"/>
			<xs:element name="RepeticioAvis" type="xs:boolean" default="false"/>
		</xs:sequence>
	</xs:complexType>
</xs:element>
```

#### Documents
| Camp | Descripció |
| ---- | ---------- |
| `Documents/Document` | detalls de cada document, complex |
| `Documents/Document/Nom` | nom del document, string |
| `Documents/Document/DigestDocument` | digest del document, string en base 64, la mida es comproba segons l'algoritme |
| `Documents/Document/DigestDocument /digestAlgorithm` | atribut (no element), algortime digest, valor: SHA-256, SHA-384, SHA-512 |
| `Documents/Document/Nom` | nom del document, string |
| `Documents/Document/DigestDocument` | digest del document, string en base 64, la mida es comproba segons l'algoritme |
| `Documents/Document/Tipus` | tipus de document,f valors possibles: Resolució, Annex |

```xml
<xs:complexType name="DocumentsType">
	<xs:sequence>
		<xs:element name="Document" maxOccurs="100">
			<xs:complexType>
				<xs:sequence>
					<xs:element name="Nom" type="xs:string"/>
					<xs:element name="DigestDocument" type="DigestDocumentType"/>
					<xs:element name="Tipus" type="TipusDocumentType"/>
				</xs:sequence>
			</xs:complexType>
		</xs:element>
	</xs:sequence>
</xs:complexType>

<xs:complexType name="DigestDocumentType">
	<xs:simpleContent>
		<xs:extension base="xs:base64Binary">
			<xs:attribute name="digestAlgorithm" type="DigestAlgorithmType"/>
		</xs:extension>
	</xs:simpleContent>
</xs:complexType>
```

[Aquí podeu veure la definició complerta d'elements importats als esquemes: Commons.xsd](https://github.com/ConsorciAOC/eNotumLite/blob/master/xsds/Commons.xsd)


### Petició Consultar Notificació
Aquesta petició facilita la consulta de notificacions, fins a un màxim de 100 notificacions per petició.
Conté un llistat de identificadors de notificació.

```xml
<xs:element name="PeticioConsulta">
	<xs:complexType>
		<xs:complexContent>
			<xs:extension base="PeticioType">
				<xs:sequence>
					<xs:element name="IdNotificacio" type="xs:integer" maxOccurs="100"/>
				</xs:sequence>
			</xs:extension>
		</xs:complexContent>
	</xs:complexType>
</xs:element>
```

[Aquí podeu veure la definició completa de l'esquema PeticioConsulta.xsd](https://github.com/ConsorciAOC/eNotumLite/blob/master/xsds/PeticioConsulta.xsd)

### Petició Cancelar Notificació
Aquesta petició permet la cancelació de notificacions dins del sistema, fins a un màxim de 100 notificacions per petició.
Conté un llistat de identificadors de notificació.

```xml
<xs:element name="PeticioCancelar">
	<xs:complexType>
		<xs:complexContent>
			<xs:extension base="PeticioType">
				<xs:sequence>
					<xs:element name="IdNotificacio" type="xs:integer" maxOccurs="100"/>
				</xs:sequence>
			</xs:extension>
		</xs:complexContent>
	</xs:complexType>
</xs:element>
```

[Aquí podeu veure la definició completa de l'esquema PeticioCancelar.xsd](https://github.com/ConsorciAOC/eNotumLite/blob/master/xsds/PeticioCancelar.xsd)

### Petició Practicar Notificació
Aquesta petició serveix per acceptar o rebutjar notificacions, fins a un màxim de 100 notificacions.
Dispara la generació d'una evidencia signada.
La seva definició es:

```xml
<xs:element name="PeticioPracticar">
	<xs:complexType>
		<xs:complexContent>
			<xs:extension base="PeticioType">
				<xs:sequence>
					<xs:element name="PracticarNotificacio" type="PracticarNotificacioType" maxOccurs="100"/>
				</xs:sequence>
			</xs:extension>
		</xs:complexContent>
	</xs:complexType>
</xs:element>
```

[Aquí podeu veure la definició completa de l'esquema PeticioPracticar.xsd](https://github.com/ConsorciAOC/eNotumLite/blob/master/xsds/
PeticioPracticar.xsd)

#### Practicar (PracticarNotificacioType)
| Camp | Descripció |
| ----- | ---------- |
| `IdNotificacio` | id de la notificació a practicar, int |
| `Decisio` | decisió del ciutadà, valors possibles: ACCEPTAR, REBUTJAR |
| `DocumentPersonaFisica` | nif o passaport (choice) |
| `DocumentPersonaFisica/NIF` | nif, string, format validat |
| `DocumentPersonaFisica/PASSAPORT` | passaport, string, format validat |
| `DocumentPersonaJuridica` | cif o vat (choice) |
| `DocumentPersonaJuridica/CIF` | cif, string, format validat |
| `DocumentPersonaJuridica/VAT` | vat, string, format validat |

```xml
<xs:complexType name="PracticarNotificacioType">
	<xs:sequence>
		<xs:element name="IdNotificacio" type="xs:integer"/>
		<xs:element name="Decisio" type="DecisioPracticarType"/>
		<xs:element name="DocumentPersonaFisica" type="DocumentPersonaFisicaType" minOccurs="0"/>
		<xs:element name="DocumentPersonaJuridica" type="DocumentPersonaJuridicaType" minOccurs="0"/>
	</xs:sequence>
</xs:complexType>
```

## Codis d'error
| Codi   | Descripció |
| -------| ---------- |
| ERR_01 | La petició rebuda no coincideix amb el format definit a l'esquema XSD |
| ERR_02 | Error no esperat pel sistema. En cas de rebre el missatge, contactar amb l'administrador de l'aplicació. Els logs del servidor mostren detalls específics de l'error genèric. |
| NOT_01 | L'emissor no existeix a la base de dades. |
| NOT_02 | Alguna dada de la notificació no compleix les restriccions de contingut especificades a l'esquema. El camp i la restricció queden indicats al missatge d'error retornat. |
| NOT_03 | El NIF no té un format vàlid. |
| NOT_04 | El CIF no té un format vàlid. |
| NOT_05 | El telèfon no té un format vàlid. |
| NOT_06 | El digest del document no té la mida esperada. La mida es calcula a partir de l'algoritme del document. |
| NOT_07 | El document identificatiu de la persona física ha de tenir com a mínim un NIF o Passaport vàlid. |
| NOT_08 | El document identificatiu de la persona jurídica ha de tenir com a mínim un CIF o VAT vàlid. |
| NOT_09 | Els documents identificatius (NIF, CIF, passaport, VAT) dels destinataris de la notificació no poden tenir duplicats. |
| CON_01 | La notificació no existeix a la base de dades. |
| CON_02 | L'emissor de la petició no coincideix amb l'emissor de la notificació. |
| PRC_01 | Si el destinatari està indicat a la petició, i la notificació té més d'un destinatari, el sistema no ha trobat el destinatari a la notificació. La cerca està basada en el document identificatiu. Si el destinatari no està indicat a la petició, i la notificació té més d'un destinatari, el sistema no pot resoldre quin es el destinatari de la petició. |
| PRC_02 | Indica que la notificació es troba en un estat diferent al de dipósit i que per tant no es pot practicar. |

## Resposta d'error
Exemple d'una resposta d'error:
```xml
<procesaResponse xmlns="http://www.openuri.org/" xmlns:ns0="http://gencat.net/scsp/esquemes/peticion">
   <RespostaCrearNotificacio xmlns="http://www.aoc.cat/eNotum">
      <ResultatCrearNotificacio>
         <Error>
            <CodiError>NOT_03</CodiError>
            <MissatgeError>NIF invàlid: 04444444H</MissatgeError>
         </Error>
      </ResultatCrearNotificacio>
   </RespostaCrearNotificacio>
</procesaResponse>
```

## Callbacks
Els callbacks s'enviaran a l'emissor de les notificacions quan aquestes passin a l'estat Dipositada i quan passin a l'estat Expirada.

L'esquema dels missatges de callback el podeu trobar a la carpeta: * [esquemes .xsd](https://github.com/ConsorciAOC/eNotumLite/tree/master/xsds)

Exemple d'un missatge de callback
```xml
<?xml version="1.0" encoding="UTF-8"?>
<ActualitzarEstat xmlns="http://www.aoc.cat/eNotum">
	<Emissor>TECSIDEL</Emissor>
	<ActualitzarEstatNotificacio>
		<Estat>DIPOSITADA</Estat>
		<DataDiposit>2017-11-06T07:49:15.735+01:00</DataDiposit>
		<Diposit>
			<![CDATA[<EvidenciaNotificacio xmlns="http://www.aoc.cat/eNotum/evidencia" Id="Id17982116381065597461669832853"/>
				<dsig:Signature xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Id="Id1170447762926575964642636045"/>
				<DadesNotificacio>
					<IdNotificacio>1</IdNotificacio>
					<Referencia>TEST-001</Referencia>
					<Titol>Prova de creacio de notificació</Titol>
					<TipusObjecte>NOTIFICACIO</TipusObjecte>
				</DadesNotificacio>
				<Documents>
					<Document>
						<Nom>Resolució.pdf</Nom>
						<DigestDocument digestAlgorithm="SHA-256">eNSt+MXuXykZl513b8S7aV3xkqxw44eyXd9q5cf3ROM=</DigestDocument>
						<Tipus>Resolució</Tipus>
					</Document>
				</Documents>
				<DadesDestinataris>
					<Destinatari>
						<NIF>12345678A</NIF>
						<Nom>Jordi</Nom>
						<PrimerCognom>CATALA</PrimerCognom>
						<SegonCognom>CATALA</SegonCognom>
						<Email>jordi.catala@example.com</Email>
					</Destinatari>
				</DadesDestinataris>
				<MotiuEvidencia>DIPOSIT</MotiuEvidencia>]]>
		</EvidenciaNotificacio>
	</Diposit>
</ActualitzarEstatNotificacio>
</ActualitzarEstat>
```

## Plantilles 
La resolució de plantilles d'e-mail i SMS fa servir el motor Mustache:
https://github.com/spullara/mustache.java#mustachejava-

Totes les dades de la notificació i destinatari estan disponibles per a mostrar-les a les plantilles.
Codi d'exemple per tal d'accedir a les dades:
```
Títol notificació: {{notificacio.titol}}
Nom destinatari: {{destinatari.nom}}
```

Codi d'exemple per tal de recórrer llistats:
``` 
{{#notificacio.documents}}
   Nom document: {{nom}}
{{/notificacio.documents}}
```

Codi d'exemple per condicionals:
``` 
{{#destinatari.raoSocial}}
  Persona juridica
{{/destinatari.raoSocial}}
{{^destinatari.raoSocial}}
  Persona fisica
{{/destinatari.raoSocial}}
```

| Descripció dada | Codi per accedir |
| --------------- | -----------------|
| Identificador de la notificació del emissor | {{notificacio.idNotificacioEmissor}} |
| Referencia de la notificació | {{notificacio.referencia}} |
| Títol de la notificació | {{notificacio.titol}} |
| Dies d'expiració notificació | {{notificacio.diesExpiracio}} |
| Idioma de la notificació | {{notificacio.idioma}} |
| Cos de la notificació | {{notificacio.cosNotificacio}} |
| Peu recurs de la notificació | {{notificacio.peuRecurs}} |
| Tipus de notificació | {{notificacio.tipusNotificacio}} |
| Estat de la notificació | {{notificacio.estat}} |
| Enviar avis de la notificació | {{notificacio.enviarAvis}} |
| Enviar recordatori de la notificació | {{notificacio.enviarRecordatori}} |
| Data de creació de la notificació | {{notificacio.dataCreacio}} |
| Data de dipòsit de la notificació | {{notificacio.dataDiposit}} |
| Data de notificació practicada | {{notificacio.dataPracticar}} |
| Codi de l'emissor de la notificació | {{notificacio.emissor.codi}} |
| Descripció de l'emissor de la notificació | {{notificacio.emissor.descripcio}} |
| Nom del document | {{#notificacio.documents}} {{nom}} {{/notificacio.documents}} |
| Digest del document | {{#notificacio.documents}} {{digest}} {{/notificacio.documents}} |
| Algoritme del document | {{#notificacio.documents}} {{digest}} {{/notificacio.documents}} |
| Tipus de document | {{#notificacio.documents}} {{digest}} {{/notificacio.documents}} |
| Idioma del destinatari de la notificació | {{#notificacio.destinataris}} {{idioma}} {{/notificacio.destinataris}} |
| Telèfons del destinatari de la notificació | {{#notificacio.destinataris.telefons}} {{telefon}} {{/notificacio.destinataris.telefons}} |
| Busties del destinatari de la notificació | {{#notificacio.destinataris.busties}} {{email}} {{/notificacio.destinataris.busties}} |
| Destinatari ha practicat la notificació | {{#notificacio.destinataris.practicada}} {{practicada}} {{/notificacio.destinataris.practicada}} |
| Evidencia de dipòsit de la notificació | {{notificacio.evidenciaDiposit.evidencia}} |
| Evidencia de notificació practicada | {{notificacio.evidenciaPracticar.evidencia}} |
| Idioma del destinatari | {{destinatari.idioma}} |
| Telèfons del destinatari | {{#destinatari.telefons}} {{telefon}} {{/destinatari.telefons}} |
| Busties del destinatari | {{#destinatari.busties}} {{email}} {{/destinatari.busties}} |
| Destinatari ha practicat la notificació | {{destinatari.practicada}} |
