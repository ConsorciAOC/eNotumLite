# eNotumLite

## Cicle de vida de les notificacions
![cicle de vida](https://github.com/ConsorciAOC/eNotumLite/blob/master/estats-enotum-lite.png)

## Estat de les notificacions
| Estat | Descripció | Estat previ |
| ----- | ---------- | ----------- |
| Entrada | Notificació introduïda al sistema mitjançant el servei de Crear Notificació. | - |
| Dipositada | Notificació dipositada automàticament mitjançant el motor d'estats. | Entrada |
| Acceptada | Notificació practicada amb motiu [ACCEPTAR] mitjançant el servei de Practicar Notificació o durant la consulta (si la notificació es de tipus [Comunicació]) | Dipositada |
| Rebutjada | Notificació practicada amb motiu [REBUTJAR] mitjançant el servei de Practicar Notificació. | Dipositada |
| Expirada | Notificació expirada automàticament, segons l'atribut [diesExpiracio]. | Dipositada |
| Error | Notificació marcada amb un error, mitjançant el motor d'estats. | - |
| Descartada | Notificació cancel·lada mitjançant el servei de Cancel·lar Notificació. | - |

## Modalitats de consum
* Aqui podeu veure els esquemes: https://github.com/ConsorciAOC/eNotumLite/tree/master/xsds
* Crear Notificació
* Consultar Notificació
* Practicar Notificació
* Cancelar Notificació

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
