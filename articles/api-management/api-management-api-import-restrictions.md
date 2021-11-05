---
title: Einschränkungen und Details der Unterstützung von API-Formaten
titleSuffix: Azure API Management
description: Hier erhalten Sie Informationen zu bekannten Problemen und Einschränkungen bei der Unterstützung der Formate Open API, WSDL und WADL in Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/26/2021
ms.author: danlep
ms.openlocfilehash: 5ddb4d615dc3d1bd7c9830d11a27341baf57edb3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031810"
---
# <a name="api-import-restrictions-and-known-issues"></a>Einschränkungen und bekannte Probleme beim Import von APIs

Beim Importieren einer API stoßen Sie unter Umständen auf Einschränkungen oder Probleme, die behoben werden müssen, damit der Import erfolgreich ausgeführt werden kann. In diesem Artikel lernen Sie Folgendes:
* Verhalten von API Management während des OpenAPI-Imports 
* Importeinschränkungen (strukturiert nach dem jeweiligen Importformat der API) 
* Funktionsweise des OpenAPI-Exports

## <a name="api-management-during-openapi-import"></a>API Management während des OpenAPI-Imports

Während des OpenAPI-Imports werden von API Management folgende Aktionen durchgeführt:
* Spezifische Suche nach Abfragezeichenfolgenparametern, die als erforderlich gekennzeichnet sind
* Konvertierung der Abfragezeichenfolgenparameter in Vorlagenparameter 

Wenn Sie ein anderes Verhalten bevorzugen, haben Sie folgende Möglichkeiten: 
* Manuelles Ändern über den formularbasierten Editor 
* Entfernen des Attributs „required“ aus der OpenAPI-Definition, damit sie nicht in Vorlagenparameter konvertiert werden

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Einschränkungen für den OpenAPI/Swagger-Import

Wenn Sie beim Importieren Ihres OpenAPI-Dokuments Fehlermeldungen erhalten, stellen Sie sicher, dass Sie das Dokument zuvor wie folgt überprüft haben:
* Mithilfe der Designers im Azure-Portal (Entwurf > Front-End > OpenAPI-Spezifikations-Editor) oder 
* Mit einem Drittanbietertool, z. B. <a href="https://editor.swagger.io">Swagger-Editor</a>

### <a name="general"></a><a name="open-api-general"> </a>Allgemein

#### <a name="url-template-requirements"></a>Anforderungen für URL-Vorlagen

| Anforderung | BESCHREIBUNG |
| ----------- | ----------- |
| **Eindeutige Namen für erforderliche Pfad- und Abfrageparameter** | In OpenAPI: <ul><li>Ein Parametername muss nur an einem Ort (beispielsweise im Pfad, in der Abfrage oder im Header) eindeutig sein.</li></ul>In API Management:<ul><li>Vorgänge können sowohl nach Pfad- als auch nach Abfrageparametern unterschieden werden.</li><li>Da diese Unterscheidung von OpenAPI nicht unterstützt wird, müssen Parameternamen innerhalb der gesamten URL-Vorlage eindeutig sein.</li></ul>  |
| **Definierter URL-Parameter** | Muss Teil der URL-Vorlage sein. |
| **Verfügbare Quelldatei-URL** | Wird auf relative Server-URLs angewendet. |
| **`\$ref`-Zeiger** | Es können keine Verweise auf externe Dateien verwendet werden. |


#### <a name="openapi-specifications"></a>OpenAPI-Spezifikationen

**Unterstützte Versionen**

Von API Management wird nur Folgendes unterstützt:
* OpenAPI-Version 2
* OpenAPI-Version 3.0.x (bis Version 3.0.3)

Die OpenAPI-Version 3.1 wird in API Management derzeit nicht unterstützt.

**Größenbeschränkungen**

| Größenlimit | BESCHREIBUNG |
| ---------- | ----------- |
| **Bis zu 4 MB** | Gilt, wenn eine OpenAPI-Spezifikation inline in API Management importiert wird. |
| **Keine Größenbeschränkung** | Gilt, wenn ein OpenAPI-Dokument über eine URL eines Speicherorts bereitgestellt wird, auf den von Ihrem API Management-Dienst aus zugegriffen werden kann. |

#### <a name="supported-extensions"></a>Unterstützte Erweiterungen
Die einzigen unterstützten Erweiterungen sind:

| Durchwahl | BESCHREIBUNG |
| ----------- | ----------- |
| **`x-ms-paths`** | <ul><li>Ermöglicht das Definieren von Pfaden, die durch Abfrageparameter in der URL unterschieden werden.</li><li>Weitere Informationen finden Sie in der [AutoRest-Dokumentation](https://github.com/Azure/autorest/tree/main/docs/extensions#x-ms-paths).</li></ul> |
| **`x-servers`** | Eine Zurückportierung des [OpenAPI 3-Objekts `servers`](https://swagger.io/docs/specification/api-host-and-base-path/) für OpenAPI 2. |

#### <a name="unsupported-extensions"></a>Nicht unterstützte Erweiterungen
| Durchwahl | BESCHREIBUNG |
| ----------- | ----------- |
| **`Recursion`** | API Management unterstützt keine rekursiven Definitionen.<br />Dies sind beispielsweise Schemas, die auf sich selbst verweisen. |
| **Objekt `Server`** | Wird auf API-Vorgangsebene nicht unterstützt. |
| **`Produces`Schlüsselwort** | Beschreibt von einer API zurückgegebene MIME-Typen. <br />Wird nicht unterstützt. |

#### <a name="custom-extensions"></a>Benutzerdefinierte Erweiterungen
- Werden beim Importieren ignoriert
- Werden nicht gespeichert oder für den Export beibehalten

#### <a name="unsupported-definitions"></a>Nicht unterstützte Definitionen 
Inlineschemadefinitionen für API-Vorgänge werden nicht unterstützt. Schemadefinitionen:
- Werden im API-Bereich definiert
- Können in Anforderungs- oder Antwortbereichen von API-Vorgängen referenziert werden

#### <a name="ignored-definitions"></a>Ignorierte Definitionen
Sicherheitsdefinitionen werden ignoriert.

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI, Version 2

Die Unterstützung von OpenAPI Version 2 ist auf das JSON-Format beschränkt.

### <a name="openapi-version-30x"></a><a name="open-api-v3"> </a>OpenAPI-Version 3.0.x

Die neueste unterstützte Version der OpenAPI-Version 3.0 ist 3.0.3.

#### <a name="https-urls"></a>HTTPS-URLs
- Wenn viele `servers` angegeben sind, verwendet API Management die erste ermittelte HTTPS-URL. 
- Falls keine HTTPS-URLs vorhanden sind, bleibt die Server-URL leer.

#### <a name="supported"></a>Unterstützt
- `example`

#### <a name="unsupported"></a>Nicht unterstützt
Die folgenden Felder sind in der [OpenAPI-Version 3.0.x](https://swagger.io/specification/) enthalten, werden jedoch nicht unterstützt:

| Object | Feld |
| ----------- | ----------- |
| **OpenAPI** | `externalDocs` |
| **Komponenten** | <ul><li>`responses`</li><li>`parameters`</li><li>`examples`</li><li>`requestBodies`</li><li>`headers`</li><li>`securitySchemes`</li><li>`links`</li><li>`callbacks`</li></ul> |
| **PathItem** | <ul><li>`trace`</li><li>`servers`</li></ul> |
| **Vorgang** | <ul><li>`externalDocs`</li><li>`callbacks`</li><li>`security`</li><li>`servers`</li></ul> |
| **Parameter** | <ul><li>`allowEmptyValue`</li><li>`style`</li><li>`explode`</li><li>`allowReserved`</li></ul> |

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI-Import-, Update- und Exportmechanismen

### <a name="general"></a><a name="open-import-export-general"> </a>Allgemein

Für API-Definitionen, die aus einem API Management-Dienst exportiert werden, gilt Folgendes:
* Sie sind hauptsächlich für externe Anwendungen vorgesehen, die die im API Management-Dienst gehostete API aufrufen müssen. 
* Sie sind nicht für einen Import in denselben oder einen anderen API Management-Dienst gedacht. 

Informationen zur Konfigurationsverwaltung von API-Definitionen in verschiedenen Diensten/Umgebungen finden Sie in der Dokumentation zur Verwendung des API Management-Diensts mit Git. 

### <a name="add-new-api-via-openapi-import"></a>Hinzufügen einer neuen API über OpenAPI-Import

Für jeden Vorgang im OpenAPI-Dokument wird ein neuer Vorgang mit folgenden Merkmalen erstellt:
* Der Azure-Ressourcenname wird auf `operationId` festgelegt.
    * Der Wert von `operationId` wird normalisiert.
    *  Wenn `operationId` nicht angegeben ist (nicht vorhanden, `null` oder leer), wird der Wert des Azure-Ressourcennamens generiert, indem HTTP-Methode und Pfadvorlage kombiniert werden.
        * Beispiel: `get-foo`.

* Der Anzeigename wird auf `summary` festgelegt. 
    * Für den Wert `summary` gilt Folgendes:
        * Er wird unverändert importiert.
        * Seine Länge ist auf 300 Zeichen beschränkt.
    * Wenn `summary` nicht angegeben ist (nicht vorhanden, `null` oder leer), wird der Wert des Anzeigenamens auf `operationId` festgelegt. 

### <a name="update-an-existing-api-via-openapi-import"></a>Aktualisieren einer vorhandenen API über den OpenAPI-Import

Während des Imports geschieht für den bestehenden API-Vorgang Folgendes:
* Er wird geändert, um der im OpenAPI-Dokument beschriebenen API zu entsprechen. 
* Er wird einem Vorgang im OpenAPI-Dokument zugeordnet, indem sein `operationId`-Wert mit dem Azure-Ressourcennamen des vorhandenen Vorgangs verglichen wird. 
    * Wenn eine Übereinstimmung gefunden wird, werden die Eigenschaften vorhandener Vorgänge „direkt“ aktualisiert.
    * Wenn keine Übereinstimmung gefunden wird:
        * Es wird ein neuer Vorgang erstellt, indem HTTP-Methode und Pfadvorlage kombiniert werden, z. B. `get-foo`. 
        * Beim Import wird für jeden neuen Vorgang versucht, Richtlinien von einem vorhandenen Vorgang mit der gleichen HTTP-Methode und Pfadvorlage zu kopieren.

Alle vorhandenen nicht übereinstimmenden Vorgänge werden gelöscht.

Beachten Sie die folgenden Leitfäden, um den Import vorhersagbarer zu machen:

- Geben Sie die `operationId`-Eigenschaft für jeden Vorgang an.
- Ändern Sie `operationId` nach dem ersten Import nicht mehr.
- Ändern Sie `operationId` und HTTP-Methode oder Pfadvorlage niemals gleichzeitig.

### <a name="export-api-as-openapi"></a>Exportieren der API als OpenAPI

Für jeden Vorgang gilt:
* Der Name der Azure-Ressource wird als `operationId` exportiert.
* Der Anzeigename wird als `summary` exportiert.

**Normalisierungsregeln für `operationId`**
- In Kleinschreibung konvertieren.
- Jede Sequenz aus nicht alphanumerischen Zeichen wird durch einen einzelnen Bindestrich ersetzt.
    - Beispielsweise wird `GET-/foo/{bar}?buzz={quix}` in `get-foo-bar-buzz-quix-` transformiert.
- Bindestriche auf beiden Seiten werden gekürzt.
    - Beispielsweise wird `get-foo-bar-buzz-quix-` zu `get-foo-bar-buzz-quix`.
- Kürzen Sie auf 76 Zeichen, vier Zeichen weniger als die Obergrenze für einen Ressourcennamen.
- Verwenden Sie ggf. die restlichen vier Zeichen für ein Deduplizierungssuffix in Form von `-1, -2, ..., -999`.

## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

Sie erstellen SOAP-Passthrough- und SOAP-to-REST-APIs mithilfe von WSDL-Dateien.

### <a name="soap-bindings"></a>SOAP-Bindungen 
- Es werden nur SOAP-Bindungen der Codierungsstile „document“ und „literal“ unterstützt.
- Der Stil „rpc“ und die SOAP-Codierung werden nicht unterstützt.

### <a name="wsdlimport"></a>WSDL:Import
Wird nicht unterstützt. Führen Sie die Importe stattdessen in einem Dokument zusammen.

### <a name="messages-with-multiple-parts"></a>Mehrteilige Nachrichten 
Dieser Nachrichtentyp wird nicht unterstützt.

### <a name="wcf-wshttpbinding"></a>WCF wsHttpBinding 
- Für SOAP-Dienste, die mit Windows Communication Foundation erstellt wurden, sollte `basicHttpBinding` verwendet werden.
- `wsHttpBinding` wird nicht unterstützt.

### <a name="mtom"></a>MTOM 
- Dienste, die `MTOM` verwenden *können* funktionieren. 
- Eine offizielle Unterstützung wird derzeit nicht angeboten.

### <a name="recursion"></a>Rekursion
- Rekursiv definierte Typen werden von API Management nicht unterstützt.
- Ein Beispiel ist ein Array, das auf sich selbst verweist.

### <a name="multiple-namespaces"></a>Mehrere Namespaces
In einem Schema können zwar mehrere Namespaces verwendet werden, aber nur der Zielnamespace kann zum Definieren von Nachrichtenteilen verwendet werden. Diese Namespaces werden verwendet, um andere Ein- oder Ausgabeelemente zu definieren.

Andere Namespaces als der Zielnamespace werden beim Exportieren nicht beibehalten. Sie können zwar ein WSDL-Dokument importieren, das Nachrichtenteile mit anderen Namespaces definiert, beim Exportieren haben jedoch alle Nachrichtenteile den WSDL-Zielnamespace.

### <a name="arrays"></a>Arrays 
Bei der SOAP-to-REST-Transformation werden nur umschlossene Arrays unterstützt. Dies ist im Beispiel unten dargestellt:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

Derzeit sind keine Probleme beim Import im Format WADL bekannt.
