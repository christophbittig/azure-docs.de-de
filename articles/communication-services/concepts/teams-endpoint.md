---
title: Erstellen eines benutzerdefinierten Teams-Endpunkts
titleSuffix: An Azure Communication Services concept document
description: In diesem Artikel wird erläutert, wie Sie einen benutzerdefinierten Teams-Endpunkt erstellen.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 2038efe285f36466b0b562d4d87fbd172a44dd42
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469583"
---
# <a name="build-a-custom-teams-endpoint"></a>Erstellen eines benutzerdefinierten Teams-Endpunkts

> [!IMPORTANT]
> Wenn Sie einen benutzerdefinierten Teams-Endpunkt aktivieren oder deaktivieren möchten, füllen Sie [dieses Formular](https://forms.office.com/r/B8p5KqCH19) aus und übermitteln es.

Sie können mit Azure Communication Services benutzerdefinierte Teams-Endpunkte für die Kommunikation mit dem Microsoft Teams-Client oder anderen benutzerdefinierten Teams-Endpunkten erstellen. Über einen benutzerdefinierten Teams-Endpunkt können Sie die Sprach-, Video-, Chat- und Bildschirmfreigabefunktionen für Teams-Benutzer anpassen.

Sie können das Azure Communication Services Identity SDK verwenden, um Azure AD-Zugriffstoken (Azure Active Directory) von Teams-Benutzern gegen Zugriffstoken der Kommunikationsidentität auszutauschen. In den Diagrammen in den folgenden Abschnitten wird ein Anwendungsfall mit mehreren Mandanten gezeigt, bei dem das fiktive Unternehmen Fabrikam Kunde des fiktiven Unternehmens Contoso ist.

## <a name="calling"></a>Aufrufen 

Sprach-, Video- und Bildschirmfreigabefunktionen werden über Anruf-SDKs von Azure Communication Services bereitgestellt. Das folgende Diagramm zeigt eine Übersicht über den Prozess, den Sie beim Integrieren Ihrer Anruffunktionen mit benutzerdefinierten Teams-Endpunkte ausführen.

![Diagramm des Prozesses zum Aktivieren der Anruffunktion für einen benutzerdefinierten Teams-Endpunkt](./media/teams-identities/teams-identity-calling-overview.svg)

## <a name="chat"></a>Chat

Optional können Sie benutzerdefinierte Teams-Endpunkte auch dazu verwenden, Chatfunktionen mit Graph-APIs zu integrieren. Weitere Informationen zur Graph-API finden Sie in der Dokumentation zum [Chatressourcentyp](/graph/api/channel-post-messages). 

![Diagramm des Prozesses zum Aktivieren der Chatfunktion für einen benutzerdefinierten Teams-Endpunkt](./media/teams-identities/teams-identity-chat-overview.png)

## <a name="azure-communication-services-permissions"></a>Berechtigungen für Azure Communication Services

### <a name="delegated-permissions"></a>Delegierte Berechtigungen

|   Berechtigung    |  Anzeigezeichenfolge   |  BESCHREIBUNG | Administratoreinwilligung erforderlich | Microsoft-Konto unterstützt |
|:--- |:--- |:--- |:--- |:--- |
| _`https://auth.msft.communication.azure.com/VoIP`_ | Verwalten von Anrufen in Teams | Starten, Beitreten, Weiterleiten, Übertragen oder Verlassen von Teams-Anrufen und Aktualisieren der Aufrufeigenschaften | Nein | Nein |

### <a name="application-permissions"></a>Anwendungsberechtigungen

Keine.

### <a name="roles-for-granting-consent-on-behalf-of-a-company"></a>Rollen zum Erteilen der Einwilligung im Namen eines Unternehmens

- Globaler Administrator
- Anwendungsadministrator (nur in der privaten Vorschau)
- Cloudanwendungsadministrator (nur in der privaten Vorschau)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Ausgeben eines Teams-Zugriffstokens](../quickstarts/manage-teams-identity.md)

Weitere Informationen zur [Teams-Interoperabilität](./teams-interop.md)
