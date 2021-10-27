---
title: Microsoft Graph-API
description: Die Microsoft Graph-API ist eine RESTful-Web-API, die Ihnen den Zugriff auf Microsoft Cloud-Dienstressourcen ermöglicht.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/08/2021
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: a0159f1c633806c26bda39ffe0c8a295bec46982
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993700"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph-API

Die Microsoft Graph-API ist eine RESTful-Web-API, die Ihnen den Zugriff auf Microsoft Cloud-Dienstressourcen ermöglicht. Nachdem Sie Ihre App registriert und Authentifizierungstoken für einen Benutzer oder Dienst abgerufen haben, können Sie Anforderungen an die Microsoft Graph-API senden. Weitere Informationen finden Sie in der [Übersicht zu Microsoft Graph](/graph/overview).

Microsoft Graph stellt REST-APIs und Client-Bibliotheken für den Zugriff auf Daten in den folgenden Microsoft Cloud-Diensten bereit:

- Microsoft 365-Dienste: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner und SharePoint
- Enterprise Mobility + Security-Dienste: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager und Intune
- Windows 10-Dienste: Aktivitäten, Geräte, Benachrichtigungen
- Dynamics 365 Business Central

## <a name="versions"></a>Versionen

Die folgenden Versionen der Microsoft Graph API sind derzeit verfügbar:

- **Beta-Version**: Die Betaversion enthält APIs, die sich derzeit in der Vorschau befinden und über den Endpunkt `https://graph.microsoft.com/beta` zugänglich sind. Wenn Sie mit der Verwendung der Beta-APIs beginnen möchten, lesen Sie [Microsoft Graph beta endpoint reference](/graph/api/overview?view=graph-rest-beta&preserve-view=true) (Microsoft Graph – Beta-Endpunkt-Referenz).
- Version **v1.0 Version**: Die Version v1.0 enthält APIs, die allgemein verfügbar und für den Produktionseinsatz bereit sind. Die Version v1.0 ist über den Endpunkt `https://graph.microsoft.com/v1.0` zugänglich. Wenn Sie mit der Verwendung der v1.0-APIs beginnen möchten, lesen Sie [Microsoft Graph REST API v1.0 reference](/graph/api/overview?view=graph-rest-1.0&preserve-view=true) (Microsoft Graph – REST-API v1.0-Referenz).

Weitere Informationen zu Microsoft Graph API-Versionen finden Sie unter [Versions-, Support- und Änderungsrichtlinien für Microsoft Graph](/graph/versioning-and-support).


## <a name="get-started"></a>Erste Schritte

Um von einer Ressource, z. B. einem Benutzer oder einer E-Mail-Nachricht, zu lesen oder in sie zu schreiben, erstellen Sie eine Anfrage, die wie das folgende Muster aussieht:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Weitere Informationen zu den Elementen der erstellten Anforderung finden Sie unter [Verwenden der Microsoft Graph-API](/graph/use-the-api).

Schnellstartbeispiele stehen zur Verfügung, die Ihnen zeigen sollen, wie Sie auf die Leistung der Microsoft Graph-API zugreifen können. Die verfügbaren Beispiele greifen auf zwei Dienste mit einer Authentifizierung zu: Microsoft-Konto und Outlook. Jeder Schnellstart greift auf Informationen aus den Benutzerprofilen des Microsoft-Kontos zu und zeigt Ereignisse aus dem Kalender dieser Benutzer an.
Die Schnellstarts umfassen vier Schritte:

- Auswählen Ihrer Plattform
- Abrufen Ihrer App-ID (Client-ID)
- Erstellen des Beispiels
- Anmelden und Anzeigen von Ereignissen in Ihrem Kalender

Nach Abschluss des Schnellstarts verfügen Sie über eine ausführungsbereite App. Weitere Informationen finden Sie in [Microsoft Graph-Schnellstart – Häufig gestellte Fragen (FAQ)](/graph/quick-start-faq). Erste Schritte mit den Beispielen finden Sie unter [Microsoft Graph – Schnellstart](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Tools

**Der Microsoft Graph Explorer** ist ein webbasiertes Tool, mit dem Sie Anfragen an die Microsoft Graph API erstellen und testen können. Zugriff auf Microsoft Graph Explorer unter https://developer.microsoft.com/graph/graph-explorer.

**Postman** ist ein weiteres Tool, mit dem Sie Anfragen an die Microsoft Graph API stellen können. Sie können Postman unter https://www.getpostman.com herunterladen. Um mit Microsoft Graph in Postman zu interagieren, verwenden Sie die [Microsoft Graph Postman Sammlung](/graph/use-postman).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Microsoft Graph, einschließlich Informationen zur Verwendung und Tutorials, finden Sie unter:

- [Verwenden der Microsoft Graph-API](/graph/use-the-api)
- [ Microsoft Graph-Tutorials ](/graph/tutorials)
