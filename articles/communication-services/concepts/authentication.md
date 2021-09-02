---
title: Authentifizieren bei Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr über die Möglichkeiten, wie sich eine App oder ein Dienst bei Communication Services authentifizieren kann.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 7c2f060bbe317cbefc3b4748cbdced21053f80f7
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970305"
---
# <a name="authenticate-to-azure-communication-services"></a>Authentifizieren bei Azure Communication Services

Jede Clientinteraktion mit Azure Communication Services muss authentifiziert werden. Bei einer typischen Architektur werden (siehe [Client- und Serverarchitektur](./client-and-server-architecture.md)) werden *Zugriffsschlüssel* oder *verwaltete Identitäten* für die Authentifizierung verwendet.

Ein anderer Authentifizierungstyp verwendet *Benutzerzugriffstoken* für die Authentifizierung bei Diensten, die Benutzereingriffe erfordern. Beispielsweise verwendet der Chat- oder Anrufdienst *Benutzerzugriffstoken*, um das Hinzufügen von Benutzern zu einem Thread und Unterhaltungen untereinander zu ermöglichen.

## <a name="authentication-options"></a>Authentifizierungsoptionen

Die folgende Tabelle zeigt die Azure Communication Services SDKs und ihre Authentifizierungsoptionen an:

| SDK    | Authentifizierungsoption                               |
| ----------------- | ----------------------------------------------------|
| Identity          | Zugriffsschlüssel oder verwaltete Identität                      |
| SMS               | Zugriffsschlüssel oder verwaltete Identität                      |
| Telefonnummern     | Zugriffsschlüssel oder verwaltete Identität                      |
| Aufrufen           | Benutzerzugriffstoken                                   |
| Chat              | Benutzerzugriffstoken                                   |

Im Anschluss werden die einzelnen Autorisierungsoptionen kurz erläutert:

### <a name="access-key"></a>Zugriffsschlüssel

Die Zugriffsschlüssel-Authentifizierung eignet sich für Dienstanwendungen, die in einer vertrauenswürdigen Dienstumgebung ausgeführt werden. Den Zugriffsschlüssel finden Sie im Azure Communication Services-Portal. Die Dienstanwendung verwendet es als Anmeldeberechtigung, um die entsprechenden SDKs zu initialisieren. Sehen Sie sich ein Beispiel dafür an, wie es im [Identity SDK](../quickstarts/access-tokens.md)verwendet wird. 

Da der Zugriffsschlüssel Teil der Verbindungszeichenfolge Ihrer Ressource ist, entspricht die Authentifizierung mit einer Verbindungszeichenfolge der Authentifizierung mit einem Zugriffsschlüssel.

Wenn Sie ACS-APIs manuell mithilfe einer Zugriffstaste aufrufen möchten, müssen Sie die Anforderung signieren. Das Signieren der Anforderung wird ausführlich im [Tutorial](../tutorials/hmac-header-tutorial.md)erläutert.

### <a name="managed-identity"></a>Verwaltete Identität

Managed Identities bietet überlegene Sicherheit und Benutzerfreundlichkeit gegenüber anderen Autorisierungsoptionen. Durch die Verwendung von Azure AD vermeiden Sie beispielweise, dass Sie Ihren Kontozugangsschlüssel innerhalb Ihres Codes speichern müssen, wie es bei der Zugriffsschlüsselautorisierung der Fall ist. Während Sie weiterhin die Zugriffsschlüsselautorisierung mit Kommunikationsdienstanwendungen verwenden können, empfiehlt Microsoft, nach Möglichkeit zu Azure AD zu wechseln. 

Um eine verwaltete Identität einzurichten, [erstellen Sie eine registrierte Anwendung über die Azure-Befehlszeilenschnittstelle](../quickstarts/identity/service-principal-from-cli.md). Anschließend können der Endpunkt und die Anmeldeinformationen zur Authentifizierung der SDKs verwendet werden. Sehen Sie sich Beispiele für die Verwendung der [verwalteten Identität](../quickstarts/identity/service-principal.md) an.

### <a name="user-access-tokens"></a>Benutzer-Zugriffstoken

Benutzerzugriffstoken werden mit dem Identity SDK erstellt und sind mit den im Identity-SDK erstellten Benutzern verknüpft. Sehen Sie sich ein Beispiel für das [Erstellen von Benutzern und das Generieren von Token](../quickstarts/access-tokens.md) an. Anschließend werden Benutzerzugriffstoken verwendet, um Teilnehmer zu authentifizieren, die Unterhaltungen im Chat- oder Anruf-SDK hinzugefügt werden. Weitere Informationen finden Sie unter [Hinzufügen von Chatfunktionen zu Ihrer App](../quickstarts/chat/get-started.md). Die Authentifizierung per Benutzerzugriffstoken unterscheidet sich von der Authentifizierung mit Zugriffsschlüssel und verwalteten Identitäten darin, dass sie zum Authentifizieren eines Benutzers und nicht einer geschützten Azure-Ressource verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Communication Services-Ressourcen](../quickstarts/create-communication-resource.md)
> [Erstellen einer Azure Active Directory-Anwendung für verwaltete Identitäten über die Azure CLI](../quickstarts/identity/service-principal-from-cli.md)
> [Erstellen von Benutzerzugriffstoken](../quickstarts/access-tokens.md)

Weitere Informationen finden Sie in den folgenden Artikeln:
- [Erfahren Sie mehr über die Client- und Serverarchitektur](../concepts/client-and-server-architecture.md)
