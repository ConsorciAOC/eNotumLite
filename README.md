# eNotumLite

## Cicle de vida de les notificacions
![cicle de vida](https://github.com/ConsorciAOC/eNotumLite/blob/master/estats-enotum-lite.png)

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

[Aquí podeu veure la definició completa de l'esquema Peticio.xsd](https://github.com/ConsorciAOC/eNotumLite/blob/master/xsds/api/Peticio.xsd)

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

[Aquí podeu veure la definició completa de l'esquema PeticioCrearNotificacio.xsd](https://github.com/ConsorciAOC/eNotumLite/blob/master/xsds/api/PeticioCrearNotificacio.xsd)

#### Notificació (NotificacioType)
| Camp | Descripció |
| ----- | ---------- |
| `Titol` | titol de la notificació, string, 100 caracters com a màxim |
| `Referencia` | referencia de la notificació, string, 100 caracters com a màxim |
| `IdNotificacioEmissor` | id de la notificació de l'emissor, string, 50 caracters com a màxim, opcional |
| `TipusObjecte` | tipus de la notificació, enum, valors possibles: NOTIFICACIO, COMUNICACIO |
| `DataCreacio` | data de creació de la notificació, data, opcional |
| `DiesExpiracio` | dies per expirar automàticament la notificació, int, opcional |
| `Destinataris` | llistat de destinataris, com a màxim 5 destinataris |
| `Destinataris/Destinatari` | persona física o persona juridica (choice) |
| `Destinataris/Destinatari/Idioma` | idioma del destinatari, enum, valors ca, oc, es, en, opcional |
| `Destinataris/Destinatari /PersonaFisica` | entitat persona fisica |
| `Destinataris/Destinatari /PersonaFisica/BustiaCorreu` | llistat emails, string, màxim 3 busties |
| `Destinataris/Destinatari /PersonaFisica/Telefon` | llistat telefons, string, màxim 3 telefons |
| `Destinataris/Destinatari /PersonaFisica/DocumentIdentificatiu` | nif o passaport (choice) |
| `Destinataris/Destinatari /PersonaFisica/DocumentIdentificatiu /NIF` | nif, string, format validat |
| `Destinataris/Destinatari /PersonaFisica/DocumentIdentificatiu /PASSAPORT` | passaport, string, format validat |
| `Destinataris/Destinatari /PersonaFisica/Nom` | nom destinatari, string |
| `Destinataris/Destinatari /PersonaFisica/PrimerCognom` | primer cognom destinatari, string |
| `Destinataris/Destinatari /PersonaFisica/SegonCognom` | segon cognom destinatari, string, opcional |
| `Destinataris/Destinatari /PersonaJuridica` | entitat persona juridica |
| `Destinataris/Destinatari /PersonaJuridica/BustiaCorreu` | llistat emails, string, màxim 3 busties |
| `Destinataris/Destinatari /PersonaJuridica/Telefon` | llistat telefons, string, màxim 3 telefons |
| `Destinataris/Destinatari /PersonaJuridica/DocumentIdentificatiu` | cif o vat (choice) |
| `Destinataris/Destinatari /PersonaJuridica/DocumentIdentificatiu /CIF` | cif, string, format validat |
| `Destinataris/Destinatari /PersonaJuridica/DocumentIdentificatiu /VAT` | vat, string, format validat |
| `Destinataris/Destinatari /PersonaJuridica/RaoSocial` | rao social, string |
| `Destinataris/Destinatari /PersonaJuridica/PersonaVinculada` | persona vinculada, opcional |
| `Destinataris/Destinatari /PersonaJuridica/PersonaVinculada /DocumentIdentificatiu` | nif o passaport (choice) |
| `Destinataris/Destinatari /PersonaJuridica/PersonaVinculada /DocumentIdentificatiu /NIF` | nif, string, format validat |
| `Destinataris/Destinatari /PersonaJuridica/PersonaVinculada /DocumentIdentificatiu /PASSAPORT` | passaport, string, format validat |
| `Destinataris/Destinatari /PersonaJuridica/PersonaVinculada /Nom` | nom destinatari, string |
| `Destinataris/Destinatari /PersonaJuridica/PersonaVinculada /PrimerCognom` | primer cognom destinatari, string |
| `Destinataris/Destinatari /PersonaJuridica/PersonaVinculada /SegonCognom` | segon cognom destinatari, string, opcional |
| `Idioma` | idioma de la notificació, enum, valors ca, oc, es, en, opcional |
| `DadesOfici` | dades ofici, complex opcional |
| `DadesOfici/CosNotificacio` | cos notificació, string, opcional |
| `DadesOfici/PeuRecurs` | peu recurs, string, opcional |
| `DadesAvisos` | dades avisos, opcional |
| `DadesAvisos/PrimerAvis` | primer avis, boolea |
| `DadesAvisos/RepeticioAvis` | repetició avis, boolea |
| `Documents` | llistat de documents de la notificació, màxim 100 documents |
| `Documents/Document` | detalls de cada document, complex |
| `Documents/Document/Nom` | nom del document, string |
| `Documents/Document/DigestDocument` | digest del document, string en base 64, la mida es comproba segons l'algoritme |
| `Documents/Document/DigestDocument /digestAlgorithm` | atribut (no element), algortime digest, valor: SHA-256, SHA-384, SHA-512 |
| `Documents/Document/Nom` | nom del document, string |
| `Documents/Document/DigestDocument` | digest del document, string en base 64, la mida es comproba segons l'algoritme |
| `Documents/Document/Tipus` | tipus de document, enum, valors possibles: Resolució, Annex |

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

[Aquí podeu veure la definició completa d'elements importats a la notificació Commons.xsd](https://github.com/ConsorciAOC/eNotumLite/blob/master/xsds/api/Commons.xsd)

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

[Aquí podeu veure la definició completa de l'esquema PeticioConsulta.xsd](https://github.com/ConsorciAOC/eNotumLite/blob/master/xsds/api/PeticioConsulta.xsd)

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

[Aquí podeu veure la definició completa de l'esquema PeticioCancelar.xsd](https://github.com/ConsorciAOC/eNotumLite/blob/master/xsds/api/PeticioCancelar.xsd)

### Petició Practicar Notificació
Aquesta petició serveix per acceptar o rebutjar notificacions, fins a un màxim de 100 notificacions.
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

[Aquí podeu veure la definició completa de l'esquema PeticioPracticar.xsd](https://github.com/ConsorciAOC/eNotumLite/blob/master/xsds/api/PeticioPracticar.xsd)

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
Nom destinatari {{destinatari.nom}}
```

Codi d'exemple per tal de recórrer llistats:
``` 
{{#notificacio.documents}}
   Nom document: {{nom}}
{{/notificacio.documents}}
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
