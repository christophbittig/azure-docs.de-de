---
title: Einschränkungen und Details der Unterstützung von API-Formaten
titleSuffix: Azure API Management
description: Hier erhalten Sie Informationen zu bekannten Problemen und Einschränkungen bei der Unterstützung der Formate Open API, WSDL und WADL in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: c5f1509a5d037e5866ed86266693e955bdca2d6f
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186423"
---
# <a name="api-import-restrictions-and-known-issues"></a>Einschränkungen und bekannte Probleme beim Import von APIs

## <a name="about-this-list"></a>Informationen zu dieser Liste

Beim Importieren einer API stoßen Sie unter Umständen auf Einschränkungen oder Probleme, die behoben werden müssen, damit der Import erfolgreich ausgeführt werden kann. Diese Einschränkungen werden nachfolgend unter dem jeweiligen Importformat der API beschrieben. Außerdem wird beschrieben, wie der OpenAPI-Export funktioniert.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Einschränkungen für den OpenAPI/Swagger-Import

Wenn Sie beim Importieren Ihres OpenAPI-Dokuments Fehlermeldungen erhalten, stellen Sie sicher, dass Sie das Dokument zuvor überprüft haben. Dazu können Sie entweder im Azure-Portal den Designer (Entwurf – Front-End – OpenAPI-Spezifikations-Editor) oder ein Tool eines Drittanbieters (z. B. <a href="https://editor.swagger.io">Swagger Editor</a>) verwenden.

### <a name="general"></a><a name="open-api-general"> </a>Allgemein

-   Erforderliche Parameter für Pfad und Abfrage müssen eindeutige Namen besitzen. (In OpenAPI muss ein Parametername nur an einem Ort (beispielsweise im Pfad, in der Abfrage oder im Header) eindeutig sein. In API Management können Vorgänge jedoch sowohl nach Pfad- als auch nach Abfrageparametern unterschieden werden (dies wird von OpenAPI nicht unterstützt). Daher müssen Parameternamen innerhalb der gesamten URL-Vorlage eindeutig sein.)
- Beim Inlineimport in API Management kann eine OpenAPI-Spezifikation bis zu 4 MB groß sein. Die Größenbeschränkung gilt nicht, wenn ein OpenAPI-Dokument über eine URL zu einem Speicherort bereitgestellt wird, auf den von Ihrem API Management zugegriffen werden kann.
-   Mit `\$ref`-Zeigern kann nicht auf externe Dateien verwiesen werden.
-   `x-ms-paths` und `x-servers` sind die einzigen unterstützten Erweiterungen.
-   Benutzerdefinierte Erweiterungen werden beim Import ignoriert und nicht für den Export gespeichert oder aufbewahrt.
-   `Recursion`: API Management unterstützt keine Definitionen, die rekursiv definiert sind (z. B. Schemas, die auf sich selbst verweisen).
-   Die Quelldatei-URL (sofern vorhanden) wird auf relative Server-URLs angewendet.
-   Sicherheitsdefinitionen werden ignoriert.
-   Inlineschemadefinitionen für API-Vorgänge werden nicht unterstützt. Schemadefinitionen werden im API-Bereich definiert, und auf sie kann in Anforderungs- oder Antwortbereichen von API-Vorgängen verwiesen werden.
-   Ein definierter URL-Parameter muss Teil der URL-Vorlage sein.
-   Das `server`-Objekt wird nicht auf der Ebene des API-Vorgangs unterstützt.
-   `Produces`-Schlüsselwort, mit dem von einer API zurückgegebene MIME-Typen beschrieben werden, wird nicht unterstützt. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI, Version 2

-   Nur das JSON-Format wird unterstützt.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI, Version 3

-   Wenn viele `servers` angegeben sind, versucht API Management, die erste HTTPS-URL auszuwählen. Wenn keine HTTPS-URLs vorhanden sind, wird die erste HTTP-URL ausgewählt. Wenn keine HTTP-URLs vorhanden sind, bleibt die Server-URL leer.
-   `Examples` wird nicht unterstützt, aber `example`.

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI-Import-, Update- und Exportmechanismen

### <a name="general"></a><a name="open-import-export-general"> </a>Allgemein

-   Die aus dem API Management-Dienst exportierten API-Definitionen sind hauptsächlich für Anwendungen gedacht, die extern zum API Management-Dienst sind, die die im API Management-Dienst gehostete API aufrufen müssen. Exportierte API-Definitionen sind nicht für einen erneuten Import in denselben oder einen anderen API Management-Dienst gedacht. Informationen zur Konfigurationsverwaltung von API-Definitionen in verschiedenen Diensten/Umgebungen finden Sie in der Dokumentation zur Verwendung des API Management-Diensts mit Git. 

### <a name="add-new-api-via-openapi-import"></a>Hinzufügen einer neuen API über OpenAPI-Import

Für jeden im OpenAPI-Dokument gefundenen Vorgang wird ein neuer Vorgang mit dem Namen der Azure-Ressource und dem auf `operationId` und `summary` festgelegten Anzeigenamen erstellt. Der `operationId`-Wert wird nach den unten beschriebenen Regeln normalisiert. Der `summary`-Wert wird unverändert importiert, und die Länge ist auf 300 Zeichen beschränkt.

Wenn `operationId` nicht angegeben ist (d. h. nicht vorhanden, `null` oder leer), wird der Wert des Azure-Ressourcennamens generiert, indem HTTP-Methode und Pfadvorlage kombiniert werden, z. B. `get-foo`.

Wenn `summary` nicht angegeben ist (d. h. nicht vorhanden, `null` oder leer), wird der `display name`-Wert auf `operationId` festgelegt. Wenn `operationId` nicht angegeben ist, wird der Wert des Anzeigenamens generiert, indem HTTP-Methode und Pfadvorlage kombiniert werden, z. B. `Get - /foo`.

### <a name="update-an-existing-api-via-openapi-import"></a>Aktualisieren einer vorhandenen API über den OpenAPI-Import

Beim Importieren wird eine vorhandene API so geändert, dass sie der im OpenAPI-Dokument beschriebenen API entspricht. Jeder Vorgang im OpenAPI-Dokument wird mit dem vorhandenen Vorgang abgeglichen, indem der `operationId`-Wert mit dem Azure-Ressourcennamen des vorhandenen Vorgangs verglichen wird.

Wenn eine Übereinstimmung gefunden wird, werden die Eigenschaften vorhandener Vorgänge „direkt“ aktualisiert.

Wenn keine Übereinstimmung gefunden wird, wird ein neuer Vorgang mit den im obigen Abschnitt beschriebenen Regeln erstellt. Beim Import wird für jeden neuen Vorgang versucht, Richtlinien von einem vorhandenen Vorgang mit der gleichen HTTP-Methode und Pfadvorlage zu kopieren.

Alle vorhandenen nicht übereinstimmenden Vorgänge werden gelöscht.

Beachten Sie folgende Richtlinien, um den Import vorhersagbarer zu machen:

- Stellen Sie sicher, dass Sie die `operationId`-Eigenschaft für jeden Vorgang angeben.
- Ändern Sie `operationId` nach dem ersten Import nicht mehr.
- Ändern Sie `operationId` und HTTP-Methode oder Pfadvorlage niemals gleichzeitig.

### <a name="export-api-as-openapi"></a>Exportieren der API als OpenAPI

Der Azure-Ressourcenname wird für jeden Vorgang als `operationId` und der Anzeigename als `summary` exportiert.
Normalisierungsregeln für operationId

- In Kleinschreibung konvertieren.
- Ersetzen Sie jede Sequenz von nicht alphanumerischen Zeichen durch einen einzelnen Bindestrich, z. B. wird `GET-/foo/{bar}?buzz={quix}` in `get-foo-bar-buzz-quix-` transformiert.
- Entfernen Sie Bindestriche auf beiden Seiten, z. B. wird `get-foo-bar-buzz-quix-` zu `get-foo-bar-buzz-quix`.
- Kürzen Sie auf 76 Zeichen, vier Zeichen weniger als die Obergrenze für einen Ressourcennamen.
- Verwenden Sie ggf. die restlichen vier Zeichen für ein Deduplizierungsuffix in Form von `-1, -2, ..., -999`.


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL-Dateien werden verwendet, um SOAP-Passthrough- und SOAP-to-REST-APIs zu erstellen.

-   **SOAP-Bindungen**: Es werden nur SOAP-Bindungen unterstützt, die im Format von „document“- oder „literal“-Codierung vorliegen. Das „rpc“-Format und SOAP-Codierung werden nicht unterstützt.
-   **WSDL:Import**: Dieses Attribut wird nicht unterstützt. Kunden sollten die zu importierenden Elemente in einem Dokument zusammenführen.
-   **Mehrteilige Nachrichten**: Diese Art von Nachrichten wird nicht unterstützt.
-   **WCF wsHttpBinding**: Für SOAP-Dienste, die mit Windows Communication Foundation erstellt wurden, muss basicHttpBinding verwendet werden – wsHttpBinding wird nicht unterstützt.
-   **MTOM**: Dienste, die MTOM verwenden, funktionieren <em>möglicherweise</em>. Eine offizielle Unterstützung wird derzeit nicht angeboten.
-   **Rekursion**: Typen, die rekursiv definiert sind (beispielsweise auf ein eigenes Array verweisen), werden von APIM nicht unterstützt.
-   **Mehrere Namespaces**: In einem Schema können zwar mehrere Namespaces verwendet werden, aber nur der Zielnamespace kann zum Definieren von Nachrichtenteilen verwendet werden. Namespaces, die zum Definieren anderer Ein- oder Ausgabeelememente verwendet werden und bei denen es sich nicht um den Zielnamespace handelt, werden nicht beibehalten. Ein solches WSDL-Dokument kann zwar importiert werden, beim Exportieren haben jedoch alle Nachrichtenteile den WSDL-Zielnamespace.
-   **Arrays**: Bei der SOAP-to-REST-Transformation werden nur umschlossene Arrays unterstützt. Dies ist im Beispiel unten dargestellt:

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
