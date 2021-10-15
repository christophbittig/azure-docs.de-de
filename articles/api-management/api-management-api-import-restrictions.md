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
ms.date: 09/24/2021
ms.author: danlep
ms.openlocfilehash: 3f16961ec4774708fa55a2a49e408a6b980cdb31
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057604"
---
# <a name="api-import-restrictions-and-known-issues"></a>Einschränkungen und bekannte Probleme beim Import von APIs

## <a name="about-this-list"></a>Informationen zu dieser Liste

Beim Importieren einer API stoßen Sie unter Umständen auf Einschränkungen oder Probleme, die behoben werden müssen, damit der Import erfolgreich ausgeführt werden kann. In diesem Artikel lernen Sie Folgendes:
* Alle Einschränkungen sind nach dem jeweiligen Importformat der API angeordnet. 
* Funktionsweise des OpenAPI-Exports

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Einschränkungen für den OpenAPI/Swagger-Import

Wenn Sie beim Importieren Ihres OpenAPI-Dokuments Fehlermeldungen erhalten, stellen Sie sicher, dass Sie das Dokument zuvor wie folgt überprüft haben:
* Mithilfe der Designers im Azure-Portal (Entwurf > Front-End > OpenAPI-Spezifikations-Editor) oder 
* Mit einem Drittanbietertool, z. B. <a href="https://editor.swagger.io">Swagger-Editor</a>

### <a name="general"></a><a name="open-api-general"> </a>Allgemein

- Erforderliche Parameter für Pfad und Abfrage müssen eindeutige Namen besitzen.
    - In OpenAPI muss ein Parametername nur an einem Ort (beispielsweise im Pfad, in der Abfrage oder im Header) eindeutig sein. 
    - In API Management können Vorgänge sowohl nach Pfad- als auch nach Abfrageparametern unterschieden werden. 
        - Da OpenAPI dies nicht unterstützt, müssen Parameternamen innerhalb der gesamten URL-Vorlage eindeutig sein.
- Beim Inlineimport in API Management kann eine OpenAPI-Spezifikation bis zu 4 MB groß sein. 
    - Die Größenbeschränkung gilt nicht, wenn ein OpenAPI-Dokument über eine URL zu einem Speicherort bereitgestellt wird, auf den von Ihrem API Management zugegriffen werden kann.
- Mit `\$ref`-Zeigern kann nicht auf externe Dateien verwiesen werden.
- Die einzigen unterstützten Erweiterungen sind:
    - `x-ms-paths` 
    - `x-servers` 
- Benutzerdefinierte Erweiterungen:
    - Werden beim Import ignoriert
    - Werden nicht gespeichert oder für den Export beibehalten
- Sicherheitsdefinitionen werden ignoriert.
- `Recursion`: 
    - API Management unterstützt keine rekursiven Definitionen.
    - Dies sind beispielsweise Schemas, die auf sich selbst verweisen.
- Das `server`-Objekt wird nicht auf der Ebene des API-Vorgangs unterstützt.
- Das Schlüsselwort `Produces`, mit dem von einer API zurückgegebene MIME-Typen beschrieben werden, wird nicht unterstützt. 
- Wenn eine Quelldatei-URL vorhanden ist, wird sie auf relative Server-URLs angewandt.
- Inlineschemadefinitionen für API-Vorgänge werden nicht unterstützt. Schemadefinitionen:
    - Werden im API-Bereich definiert
    - Können in Anforderungs- oder Antwortbereichen von API-Vorgängen referenziert werden
- Ein definierter URL-Parameter muss Teil der URL-Vorlage sein.

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI, Version 2

Die Unterstützung von OpenAPI Version 2 ist auf das JSON-Format beschränkt.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI, Version 3

-   Wenn viele `servers` angegeben sind, verwendet API Management die erste ermittelte HTTPS-URL. 
- Falls keine HTTPS-URLs vorhanden sind, bleibt die Server-URL leer.
- `Examples` wird nicht unterstützt, aber `example`.
- Die folgenden Felder sind in OpenAPI Version 3.x enthalten, werden jedoch nicht unterstützt:
    - `explode`
    - `style`
    - `allowReserved`

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI-Import-, Update- und Exportmechanismen

### <a name="general"></a><a name="open-import-export-general"> </a>Allgemein

Für API-Definitionen, die aus einem API Management-Dienst exportiert werden, gilt Folgendes:
* Sie sind hauptsächlich für externe Anwendungen vorgesehen, die die im API Management-Dienst gehostete API aufrufen müssen. 
* Sie sind nicht für einen Import in denselben oder einen anderen API Management-Dienst gedacht. 

Informationen zur Konfigurationsverwaltung von API-Definitionen in verschiedenen Diensten/Umgebungen finden Sie in der Dokumentation zur Verwendung des API Management-Diensts mit Git. 

### <a name="add-new-api-via-openapi-import"></a>Hinzufügen einer neuen API über OpenAPI-Import

Für jeden im OpenAPI-Dokument gefundenen Vorgang wird ein neuer Vorgang mit dem Namen der Azure-Ressource und dem auf `operationId` und `summary` festgelegten Anzeigenamen erstellt. 
* Der Wert von `operationId` wird normalisiert.
    *  Wenn `operationId` nicht angegeben ist (nicht vorhanden, `null` oder leer), wird der Wert des Azure-Ressourcennamens generiert, indem HTTP-Methode und Pfadvorlage kombiniert werden, z. B. `get-foo`.
* Der `summary`-Wert wird unverändert importiert, und die Länge ist auf 300 Zeichen beschränkt.
    * Wenn `summary` nicht angegeben ist (nicht vorhanden, `null` oder leer), wird der Wert von `display name` auf `operationId` festgelegt. 

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

Folgendes gilt für jeden Vorgang:
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

- **SOAP-Bindungen**  
    - Es werden nur SOAP-Bindungen der Codierungsstile „document“ und „literal“ unterstützt.
    - Der Stil „rpc“ und die SOAP-Codierung werden nicht unterstützt.
- **WSDL:Import**
    - Wird nicht unterstützt. Führen Sie die Importe stattdessen in einem Dokument zusammen.
- **Mehrteilige Nachrichten** 
    - Dieser Nachrichtentyp wird nicht unterstützt.
- **WCF wsHttpBinding** 
    - Für SOAP-Dienste, die mit Windows Communication Foundation erstellt wurden, sollte `basicHttpBinding` verwendet werden.
    - `wsHttpBinding` wird nicht unterstützt.
- **MTOM** 
    - Dienste, die `MTOM` verwenden *können* funktionieren. 
    - Eine offizielle Unterstützung wird derzeit nicht angeboten.
- **Rekursion** 
    - Rekursiv definierte Typen werden von API Management nicht unterstützt.
    - Ein Beispiel ist ein Array, das auf sich selbst verweist.
- **Mehrere Namespaces** 
    - In einem Schema können zwar mehrere Namespaces verwendet werden, aber nur der Zielnamespace kann zum Definieren von Nachrichtenteilen verwendet werden. 
    - Andere Namespaces als der Zielnamespace werden nicht beibehalten. 
        - Diese Namespaces werden verwendet, um andere Ein- oder Ausgabeelemente zu definieren.
        - Ein solches WSDL-Dokument kann zwar importiert werden, beim Exportieren haben jedoch alle Nachrichtenteile den WSDL-Zielnamespace.
- **Arrays** 
    - Bei der SOAP-to-REST-Transformation werden nur umschlossene Arrays unterstützt. Dies ist im Beispiel unten dargestellt:

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
