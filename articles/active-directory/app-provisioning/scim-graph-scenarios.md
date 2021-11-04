---
title: Verwenden von SCIM, Microsoft Graph und Azure Active Directory zum Bereitstellen von Benutzern und Anreichern von Apps mit Daten
description: Durch die gemeinsame Nutzung von SCIM und Microsoft Graph in Azure Active Directory können Sie Benutzer bereitstellen und Ihre Anwendung mit den benötigten Daten anreichern.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: f97ff11a1a7e3829ef16aa4e903f88e2ad66949a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432755"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Durch die gemeinsame Nutzung von SCIM und Microsoft Graph können Sie Benutzer bereitstellen und Ihre Anwendung mit den benötigten Daten anreichern

**Zielgruppe:** Dieser Artikel richtet sich an Entwickler von Anwendungen, die mit Azure Active Directory (Azure AD) integriert werden sollen. Wenn Sie Anwendungen verwenden möchten, die bereits mit Azure AD integriert sind, z. B. Zoom, ServiceNow und Dropbox, können Sie diesen Artikel überspringen und mit den anwendungsspezifischen [Tutorials](../saas-apps/tutorial-list.md) oder dem Artikel zur [Funktionsweise der Bereitstellung](./how-provisioning-works.md) fortfahren.

**Gängige Szenarios**

Azure AD stellt einen einsatzbereiten Dienst für die Bereitstellung und eine erweiterbare Plattform zum Erstellen von Anwendungen bereit. Die Entscheidungsstruktur erläutert die Schritte zur Verwendung von [SCIM](https://aka.ms/scimoverview) und [Microsoft Graph](/graph/overview) zum Automatisieren der Bereitstellung. 

> [!div class="checklist"]
> * Automatisches Erstellen von Benutzern in meiner Anwendung
> * Automatisches Entfernen von Benutzern aus meiner Anwendung, wenn sie keinen Zugriff mehr haben sollen
> * Integrieren meiner Anwendung mit mehreren Identitätsanbietern für die Bereitstellung
> * Bereichern von Anwendungen mit Daten aus Microsoft-Diensten wie Teams, Outlook und Office
> * Automatisches Erstellen, Aktualisieren und Löschen von Benutzern und Gruppen in Azure AD und Active Directory

![SCIM und Microsoft Graph – Entscheidungsstruktur](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Szenario 1: Automatisches Erstellen von Benutzern in meiner Anwendung
Heutzutage stellen IT-Administratoren Benutzer bereit, indem sie Benutzerkonten manuell erstellen oder regelmäßig CSV-Dateien in die Anwendung hochladen. Dieser Vorgang ist für die Kunden zeitaufwändig und verlangsamt die Einführung meiner Anwendung. Sie benötigen lediglich grundlegende Benutzerinformationen wie Namen, E-Mail-Adressen und Benutzerprinzipalnamen, um einen Benutzer zu erstellen. 

**Empfehlung**: 
* Wenn Ihre Kunden verschiedene Identitätsanbieter verwenden und Sie keine Synchronisierungsengine für die Integration mit den einzelnen Identitätsanbietern verwalten möchten, unterstützen Sie einen SCIM-kompatiblen [/Users](https://aka.ms/scimreferencecode)-Endpunkt. Ihre Kunden können diesen Endpunkt problemlos zur Integration mit dem Azure AD-Bereitstellungsdienst verwenden und automatisch Benutzerkonten erstellen, wenn sie Zugriff benötigen. Sobald Sie den Endpunkt einmal erstellt haben, ist er mit allen Identitätsanbietern kompatibel. Sehen Sie sich die folgende Beispielanforderung an, um zu erfahren, wie ein Benutzer mit SCIM erstellt wird.
* Wenn Sie Benutzerdaten, die sich im Benutzerobjekt in Azure AD befinden, und andere Daten von Microsoft benötigen, können Sie einen SCIM-Endpunkt für die Benutzerbereitstellung erstellen und in Microsoft Graph aufrufen, um die restlichen Daten abzurufen. 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Szenario 2: Automatisches Entfernen von Benutzern aus meiner App
Die Kunden, die meine Anwendung verwenden, sind sicherheitsorientiert und haben Governanceanforderungen, nach denen Konten entfernt werden müssen, wenn die Mitarbeiter sie nicht mehr benötigen. Wie kann ich die Aufhebung der Bereitstellung in meiner Anwendung automatisieren?

**Empfehlung:** Unterstützung des SCIM-kompatiblen Endpunkts „/Users“. Der Azure AD-Bereitstellungsdienst sendet Anforderungen zum Deaktivieren und Löschen, wenn der Benutzer keinen Zugriff mehr haben soll. Wir empfehlen, sowohl das Deaktivieren als auch das Löschen von Benutzern zu unterstützen. Sehen Sie sich die folgenden Beispiele an, um zu erfahren, wie eine Anforderung zum Deaktivieren und Löschen aussieht. 

Benutzer deaktivieren
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Benutzer löschen
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Szenario 3: Automatisieren der Verwaltung von Gruppenmitgliedschaften in meiner App
Für den Zugriff auf verschiedene Ressourcen verwendet meine Anwendung Gruppen, und die Kunden möchten ihre in Azure AD genutzten Gruppen wiederverwenden. Wie kann ich Gruppen aus Azure AD importieren und diese bei Mitgliedschaftsänderungen auf dem aktuellen Stand halten?  

**Empfehlung:** Unterstützung des SCIM-kompatiblen Endpunkts [/Groups](https://aka.ms/scimreferencecode). Der Azure AD-Bereitstellungsdienst übernimmt das Erstellen von Gruppen und das Verwalten von Mitgliedschaftsaktualisierungen in Ihrer Anwendung. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Szenario 4: Bereichern von Apps mit Daten aus Microsoft-Diensten wie Teams, Outlook und OneDrive
Meine Anwendung ist in Microsoft Teams integriert und basiert auf Nachrichtendaten. Außerdem werden Dateien für Benutzer in OneDrive gespeichert. Wie kann ich meine Anwendung mit den Daten aus diesen Diensten und aus Microsoft insgesamt anreichern?

**Empfehlung:** [Microsoft Graph](/graph/) ist Ihr Einstiegspunkt für den Zugriff auf Microsoft-Daten. Jede Workload macht APIs mit den benötigten Daten verfügbar. Microsoft Graph kann zusammen mit der [SCIM-Bereitstellung](./use-scim-to-provision-users-and-groups.md) für die oben genannten Szenarien verwendet werden. Mit SCIM können Sie grundlegende Benutzerattribute in Ihrer Anwendung bereitstellen, während Sie über einen Aufruf von Microsoft Graph alle anderen benötigten Daten abrufen. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Szenario 5: Nachverfolgen von Änderungen in Microsoft-Diensten wie Teams, Outlook und Azure AD
Ich muss Änderungen in Teams und an Outlook-Nachrichten verfolgen und in Echtzeit darauf reagieren können. Wie kann ich erreichen, dass diese Änderungen per Pushvorgang an meine Anwendung gesendet werden?

**Empfehlung:** Microsoft Graph stellt [Änderungsbenachrichtigungen](/graph/webhooks) und eine [Änderungsnachverfolgung](/graph/delta-query-overview) für verschiedene Ressourcen bereit. Beachten Sie die folgenden Einschränkungen von Änderungsbenachrichtigungen:
- Wenn ein Ereignisempfänger ein Ereignis bestätigt, aber aus irgendeinem Grund nicht darauf reagiert, kann das Ereignis verloren gehen.
- Die Reihenfolge, in der Änderungen empfangen werden, ist nicht garantiert chronologisch.
- Änderungsbenachrichtigungen enthalten nicht immer die [Ressourcendaten](/graph/webhooks-with-resource-data). Aus den oben genannten Gründen verwenden Entwickler bei Synchronisierungsszenarien häufig eine Kombination aus Änderungsbenachrichtigungen und Änderungsnachverfolgung. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Szenario 6: Bereitstellen von Benutzern und Gruppen in Azure AD
Meine Anwendung erstellt Informationen zu einem Benutzer, die Kunden in Azure AD benötigen. Dabei kann es sich um eine HR-Anwendung handeln, die Personaleinstellungen verwaltet, eine Kommunikations-App, die Telefonnummern für Benutzer erstellt, oder eine andere App, die Daten generiert, die in Azure AD nützlich sind. Wie fülle ich den Benutzerdatensatz in Azure AD mit diesen Daten auf? 

**Empfehlung:** Microsoft Graph macht die Endpunkte „/Users“ und „/Groups“ verfügbar, die Sie in Today integrieren können, um Benutzer in Azure AD bereitzustellen. Beachten Sie, dass Azure Active Directory das Zurückschreiben dieser Benutzer in Active Directory nicht unterstützt. 

> [!NOTE]
> Microsoft verfügt über einen Bereitstellungsdienst, der Daten per Pullvorgang aus HR-Anwendungen wie Workday und SuccessFactors abruft. Diese Integrationen werden von Microsoft erstellt und verwaltet. Wenn eine neue HR-Anwendung in unseren Dienst aufgenommen werden soll, können Sie dies auf [UserVoice](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789) anfordern. 

## <a name="related-articles"></a>Verwandte Artikel

- [Lesen Sie die Dokumentation zur Synchronisierung für Microsoft Graph](/graph/api/resources/synchronization-overview)
- [Integrieren einer benutzerdefinierten SCIM-App in Azure AD](use-scim-to-provision-users-and-groups.md)
