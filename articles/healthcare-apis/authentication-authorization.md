---
title: Authentifizierung und Autorisierung von Azure Healthcare-APIs
description: Dieser Artikel bietet eine Übersicht über die Authentifizierung und Autorisierung der ApIs für das Gesundheitswesen.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: 53f31790d5b92234bfd35e723e377e682cd13a4d
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252520"
---
# <a name="authentication--authorization-for-the-healthcare-apis-preview"></a>Authentifizierung & Autorisierung für die Gesundheits-APIs (Vorschau)

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Dieser Artikel bietet eine Übersicht über den Authentifizierungs- und Autorisierungsprozess für den Zugriff auf die ApIs im Gesundheitswesen.

## <a name="authentication"></a>Authentifizierung

Die Gesundheits-APIs sind eine Sammlung geschützter verwalteter Dienste mit [Azure Active Directory (Azure AD),](../active-directory/index.yml)einem globalen Identitätsanbieter, der [OAuth 2.0](https://oauth.net/2/)unterstützt.

Damit die Dienste der Gesundheits-APIs auf Azure-Ressourcen wie Speicherkonten und Event Hubs zugreifen können, müssen Sie **die vom System verwaltete Identität aktivieren** und der verwalteten Identität die **richtigen Berechtigungen erteilen.** Weitere Informationen finden Sie unter [Verwaltete Azure-Identitäten.](../active-directory/managed-identities-azure-resources/overview.md)

Die Gesundheits-APIs unterstützen keine anderen Identitätsanbieter. Kunden können jedoch ihren eigenen Identitätsanbieter verwenden, um Anwendungen zu schützen und ihnen die Interaktion mit den Healthcare-APIs zu ermöglichen, indem sie Clientanwendungen und Benutzerdatenzugriffssteuerungen verwalten.

Die Clientanwendungen werden im Azure AD registriert und können für den Zugriff auf die Healthcare-APIs verwendet werden. Benutzerdatenzugriffssteuerungen werden in den Anwendungen oder Diensten durchgeführt, die Geschäftslogik implementieren.

### <a name="application-roles"></a>Anwendungsrollen

Authentifizierten Benutzern und Clientanwendungen der ApIs für das Gesundheitswesen müssen die richtigen Anwendungsrollen erteilt werden.

Der FHIR-Dienst der Healthcare-APIs bietet die folgenden Rollen:

* **FHIR-Datenleser:** Kann FHIR-Daten lesen (und durchsuchen).
* **FHIR Data Writer:** Kann FHIR-Daten lesen, schreiben und soft delete.
* **FHIR-Datenexporter:** Kann Daten ($export Operator) lesen und exportieren.
* **FHIR-Datenmitwirkender:** Kann alle Vorgänge auf Datenebene ausführen.
* **FHIR-Datenkonverter:** Kann den Konverter zum Durchführen der Datenkonvertierung verwenden.

Der DICOM-Dienst der ApIs für das Gesundheitswesen bietet die folgenden Rollen:

* **DICOM-Datenbesitzer:** Kann DICOM-Daten lesen, schreiben und löschen.
* **DICOM-Daten lesen:** Kann DICOM-Daten lesen.

Für die IoT Connector sind keine Anwendungsrollen erforderlich, es wird jedoch der "Azure Event Hubs-Datenempfänger" verwendet, um im Event Hub des Kundenabonnements gespeicherte Daten abzurufen.

## <a name="authorization"></a>Authorization

Nachdem die authentifizierten Benutzer und Clientanwendungen mit den richtigen Anwendungsrollen gewährt wurden, können sie auf die Dienste der Gesundheits-APIs zugreifen, indem sie ein **gültiges Zugriffstoken** abrufen, das von Azure AD ausgestellt wurde, und bestimmte Vorgänge ausführen, die von den Anwendungsrollen definiert werden.
 
* Für den FHIR-Dienst ist das Zugriffstoken spezifisch für den Dienst oder die Ressource.
* Für den DICOM-Dienst wird das Zugriffstoken der Ressource und `dicom.healthcareapis.azure.com` nicht einem bestimmten Dienst gewährt.
* Für die IoT Connector ist das Zugriffstoken nicht erforderlich, da es nicht für die Benutzer oder Clientanwendungen verfügbar gemacht wird.

### <a name="steps-for-authorization"></a>Schritte für die Autorisierung

Es gibt zwei gängige Möglichkeiten zum Abrufen eines Zugriffstokens, die in der Azure AD-Dokumentation ausführlich beschrieben werden: [Autorisierungscodeflow](../active-directory/develop/v2-oauth2-auth-code-flow.md) und [Clientanmeldeinformationsflow.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)

Zum Abrufen eines Zugriffstokens für die Gesundheits-APIs sind dies die Schritte mithilfe des **Autorisierungscodeflows:**

1. **Der Client sendet eine Anforderung an den Azure AD Autorisierungsendpunkt.** Azure AD leitet den Client zu einer Anmeldeseite um, auf der sich der Benutzer mit entsprechenden Anmeldeinformationen authentifiziert (z. B. Benutzername und Kennwort oder zweistufige Authentifizierung). **Nach erfolgreicher Authentifizierung wird ein Autorisierungscode an den Client zurückgegeben.** Azure AD lässt nur zu, dass dieser Autorisierungscode an eine registrierte Antwort-URL zurückgegeben wird, die in der Clientanwendungsregistrierung konfiguriert ist.

2. **Die Clientanwendung tauscht den Autorisierungscode gegen ein Zugriffstoken am Azure AD Tokenendpunkt aus.** Beim Anfordern eines Tokens muss die Clientanwendung möglicherweise einen geheimen Clientschlüssel bereitstellen (den Sie während der Anwendungsregistrierung hinzufügen können).
 
3. **Der Client stellt eine Anforderung an die Gesundheits-APIs,** z. B. eine `GET` Anforderung, alle Patienten im FHIR-Dienst zu durchsuchen. Beim Erstellen der Anforderung **wird das Zugriffstoken in einen `HTTP` Anforderungsheader eingeschlossen,** z. **`Authorization: Bearer xxx`** B. .

4. **Der Dienst für Gesundheits-APIs überprüft, ob das Token entsprechende Ansprüche (Eigenschaften im Token) enthält.** Wenn sie gültig ist, schließt sie die Anforderung ab und gibt Daten an den Client zurück.

Im Flow für **Clientanmeldeinformationen** werden Berechtigungen direkt an die Anwendung selbst erteilt. Wenn die Anwendung ein Token für eine Ressource vorlegt, erzwingt die Ressource, dass die Anwendung selbst über die Autorisierung zum Ausführen einer Aktion verfügt, da kein Benutzer an der Authentifizierung beteiligt ist. Daher unterscheidet er sich wie folgt vom **Autorisierungscodeflow:**

- Der Benutzer oder der Client muss sich nicht interaktiv anmelden.
- Der Autorisierungscode ist nicht erforderlich.
- Das Zugriffstoken wird direkt über Anwendungsberechtigungen abgerufen.

### <a name="access-token"></a>Zugriffstoken

Das Zugriffstoken ist eine signierte [Base64-codierte](https://en.wikipedia.org/wiki/Base64) Sammlung von Eigenschaften (Ansprüchen), die Informationen über die Identität, Rollen und Berechtigungen des Clients vermitteln, die dem Benutzer oder Client gewährt werden.

Die ApIs für das Gesundheitswesen erwarten in der Regel eine [JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token). Es besteht aus drei Teilen: 
* Header
* Nutzlast (die Ansprüche)
* Signatur, wie in der folgenden Abbildung dargestellt. Weitere Informationen finden Sie unter [Azure-Zugriffstoken.](../active-directory/develop/active-directory-configurable-token-lifetimes.md)

[![JASON-Webtokensignatur. ](media/azure-access-token.png) ](media/azure-access-token.png#lightbox)

Sie können Onlinetools wie [https://jwt.ms](https://jwt.ms/) verwenden, um den Tokeninhalt anzuzeigen. Beispielsweise können Sie die Anspruchsdetails anzeigen.

|**Anspruchstyp**          |**Wert**               |**Hinweise**                               |
|------------------------|------------------------|----------------------------------------|
|aud                     |https://xxx.fhir.azurehealthcareapis.com|Identifiziert den vorgesehenen Empfänger des Tokens. In `id_tokens` ist die Zielgruppe die Anwendungs-ID Ihrer App, die Ihrer App im Azure-Portal zugewiesen ist. Ihre App sollte diesen Wert überprüfen und das Token ablehnen, wenn der Wert nicht übereinstimmt.|
|iss                     |https://sts.windows.net/{tenantid}/|Identifiziert den Sicherheitstokendienst (STS), der das Token und den Azure AD-Mandanten, in dem der Benutzer authentifiziert wurde, erstellt und zurückgibt. Wenn das Token vom v2.0-Endpunkt ausgegeben wurde, endet der URI mit `/v2.0`. Die GUID, die angibt, dass der Benutzer ein Consumer-Benutzer eines Microsoft-Kontos ist, lautet `9188040d-6c67-4c5b-b112-36a304b66dad`. Ihre App sollte den GUID-Teil des Anspruchs verwenden, um den Satz von Mandanten einzuschränken, die sich bei der App anmelden können, falls zutreffend.|
|iat                     |(Zeitstempel)            |„Issued At“ gibt an, wann die Authentifizierung für dieses Token erfolgt ist.|
|nbf                     |(Zeitstempel)            |Der Anspruch „nbf“ (nicht vor) gibt die Zeit an, vor der das JWT NICHT für die Bearbeitung akzeptiert werden darf.|
|exp                     |(Zeitstempel)            |Der Anspruch „exp“ (Ablaufzeit) gibt die Ablaufzeit an, ab oder nach der das JWT NICHT für die Bearbeitung akzeptiert werden darf. Es ist wichtig zu beachten, dass eine Ressource das Token auch vor diesem Zeitpunkt ablehnen kann, wenn z. B. eine Änderung der Authentifizierung erforderlich ist oder eine Tokensperrung erkannt wurde.|
|aio                     |E2ZgYxxx                |Ein interner Anspruch, der von Azure AD verwendet wird, um Daten für die Wiederverwendung von Token aufzuzeichnen. Sollte ignoriert werden.|
|appid                   |e97e1b8c-xxx            |Dies ist die Anwendungs-ID des Clients, der das Token verwendet. Die Anwendung kann als sie selbst oder im Auftrag eines Benutzers agieren. Die Anwendungs-ID stellt in der Regel ein Anwendungsobjekt dar, kann aber auch ein Dienstprinzipalobjekt in Azure AD darstellen.|
|appidacr                |1                       |Gibt an, wie der Client authentifiziert wurde. Bei einem öffentlichen Client ist der Wert 0. Wenn die Client-ID und der geheime Clientschlüssel verwendet werden, ist der Wert 1. Wenn ein Clientzertifikat für die Authentifizierung verwendet wurde, lautet der Wert 2.|
|idp                     |https://sts.windows.net/{tenantid}/|Der Identitätsanbieter, der den Antragsteller des Tokens authentifiziert hat. Dieser Wert ist mit dem Wert des Ausstelleranspruchs identisch, es sei denn, das Benutzerkonto befindet sich nicht im selben Mandanten wie der Aussteller, z. B. Gäste. Wenn der Anspruch nicht vorhanden ist, bedeutet dies, dass stattdessen der Wert von iss verwendet werden kann. Für persönliche Konten, die in einem Organisationskontext verwendet werden (z. B. ein persönliches Konto, das zu einem Azure AD Mandanten eingeladen wurde), kann der idp-Anspruch "live.com" oder ein STS-URI sein, der den Microsoft-Konto Mandanten 9188040d-6c67-4c5b-b112-36a304b66dad enthält.|
|oid                     |Beispiel: tenantid         |Dies ist der unveränderliche Bezeichner für ein Objekt im Microsoft-Identitätssystem, in diesem Fall ein Benutzerkonto. Diese ID identifiziert den Benutzer anwendungsübergreifend eindeutig. Zwei verschiedene Anwendungen, die sich beim gleichen Benutzer anmelden, erhalten den gleichen Wert im oid-Anspruch. Der Microsoft Graph gibt diese ID als ID-Eigenschaft für ein bestimmtes Benutzerkonto zurück. Da die Oid es mehreren Apps ermöglicht, Benutzer zu korrelieren, ist der Profilbereich erforderlich, um diesen Anspruch zu erhalten. Hinweis: Wenn ein einzelner Benutzer in mehreren Mandanten vorhanden ist, enthält der Benutzer in jedem Mandanten eine andere Objekt-ID. Sie werden als unterschiedliche Konten betrachtet, obwohl sich der Benutzer mit denselben Anmeldeinformationen bei jedem Konto anmeldet.|
|Rh                       |0.ARoxxx              |Ein interner Anspruch, der von Azure zum erneuten Überprüfen von Token verwendet wird. Sie sollte ignoriert werden.|
|sub                      |Beispiel: tenantid        |Der Prinzipal, für den das Token Informationen zusichert, z. B. der Benutzer einer App. Dieser Wert ist unveränderlich und kann nicht erneut zugewiesen oder wiederverwendet werden. Der Antragsteller ist ein paarweiser Bezeichner: Er gilt nur für eine bestimmte Anwendungs-ID. Wenn sich ein Benutzer bei zwei verschiedenen Apps mit zwei verschiedenen Client-IDs anmeldet, erhalten diese Apps zwei unterschiedliche Werte für den Antragstelleranspruch. Dies kann abhängig von den Architektur- und Datenschutzanforderungen möglicherweise wünschenswert sein oder nicht.|
|tid                      |Beispiel: tenantid        |Eine GUID, die den Azure AD-Mandanten darstellt, aus dem der Benutzer stammt. Bei Geschäfts- und Schulkonten ist die GUID die unveränderliche Mandanten-ID der Organisation, zu der der Benutzer gehört. Bei persönlichen Konten lautet der Wert 9188040d-6c67-4c5b-b112-36a304b66dad. Der Profilbereich ist erforderlich, um diesen Anspruch zu erhalten.
|uti                      |bY5glsxxx             |Ein interner Anspruch, der von Azure zum erneuten Überprüfen von Token verwendet wird. Sie sollte ignoriert werden.|
|ver                      |1                     |Gibt die Version des Tokens an.|
 
**Das Zugriffstoken ist standardmäßig eine Stunde lang gültig. Sie können ein neues Token abrufen oder es mithilfe des Aktualisierungstokens erneuern, bevor es abläuft.**

Zum Abrufen eines Zugriffstokens können Sie Tools wie Postman, die REST-Clienterweiterung in Visual Studio Code, PowerShell, CLI, curl und die [Azure AD-Authentifizierungsbibliotheken](../active-directory/develop/reference-v2-libraries.md)verwenden.

## <a name="encryption"></a>Verschlüsselung

Wenn Sie einen neuen Dienst von Azure Healthcare-APIs erstellen, werden Ihre Daten standardmäßig mit von **Microsoft verwalteten Schlüsseln** verschlüsselt. 

* Der FHIR-Dienst ermöglicht die Verschlüsselung ruher Daten, wenn Daten im Datenspeicher gespeichert werden.
* Der DICOM-Dienst ermöglicht die Verschlüsselung ruher Daten, wenn imagegebende Daten, einschließlich eingebetteter Metadaten, im Datenspeicher beibehalten werden. Wenn Metadaten extrahiert und im FHIR-Dienst gespeichert werden, werden sie automatisch verschlüsselt.
* IoT Connector speichert nach der Datenzuordnung und Normalisierung Gerätenachrichten im FHIR-Dienst, der automatisch verschlüsselt wird. In Fällen, in denen Gerätenachrichten an Azure Event Hubs gesendet werden, die Azure Storage zum Speichern der Daten verwenden, werden die Daten automatisch mit Azure Storage Service Encryption (Azure SSE) verschlüsselt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie die Authentifizierung und Autorisierung der ApIs für das Gesundheitswesen kennengelernt. Informationen zum Bereitstellen einer Instanz des Gesundheitswesen-APIs-Diensts finden Sie unter

>[!div class="nextstepaction"]
>[Bereitstellen eines Arbeitsbereichs für Gesundheits-APIs (Vorschauversion) über das Azue-Portal](healthcare-apis-quickstart.md)
