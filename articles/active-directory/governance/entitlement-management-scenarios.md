---
title: Typische Szenarien für die Berechtigungsverwaltung – Azure AD
description: Erfahren Sie, welche allgemeinen Schritte Sie in typischen Szenarien für die Azure Active Directory-Berechtigungsverwaltung ausführen müssen.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3091afbab67f65a1850bb4ffbd73487d0e0327c9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742549"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Typische Szenarien für die Azure AD-Berechtigungsverwaltung

Es gibt mehrere Möglichkeiten, wie Sie die Berechtigungsverwaltung für Ihre Organisation konfigurieren können. Für den Einstieg ist es jedoch hilfreich, die typischen Szenarien für Administratoren, Katalogbesitzer, Zugriffspaketmanager, genehmigende Personen und Anforderer zu verstehen.

## <a name="delegate"></a>Delegieren

### <a name="administrator-delegate-management-of-resources"></a>Administrator: Delegieren der Verwaltung von Ressourcen

1. [Video ansehen: Delegierung von IT an Abteilungsleiter](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegieren von Benutzern an die Rolle „Katalogersteller“](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Katalogersteller: Delegieren der Verwaltung von Ressourcen

- [Erstellen eines neuen Katalogs](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Katalogbesitzer: Delegieren der Verwaltung von Ressourcen

1. [Hinzufügen von Mitbesitzern zum Katalog](entitlement-management-catalog-create.md#add-more-catalog-owners)
1. [Hinzufügen von Ressourcen zum Katalog](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Katalogbesitzer: Delegieren der Verwaltung von Zugriffspaketen

1. [Video ansehen: Delegierung von Katalogbesitzern an Zugriffspaketmanager](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegieren von Benutzern an die Rolle „Zugriffspaketmanager“](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Steuern des Zugriffs für Benutzer in Ihrer Organisation

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Zugriffspaketmanager: Gewähren von Ressourcenzugriffsanforderungen für Mitarbeiter in Ihrer Organisation

1. [Erstellen eines neuen Zugriffspakets](entitlement-management-access-package-create.md#start-new-access-package)
1. [Hinzufügen von Gruppen, Teams, Anwendungen oder SharePoint-Websites zum Zugriffspaket](entitlement-management-access-package-create.md#resource-roles)
1. [Hinzufügen einer Anforderungsrichtlinie, damit Benutzer in Ihrem Verzeichnis Zugriff anfordern können](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Angeben von Ablaufeinstellungen](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Anforderer: Anfordern des Zugriffs auf Ressourcen

1. [Anmelden beim Portal „Mein Zugriff“](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Suchen des Zugriffspakets
1. [Zugriff anfordern](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Genehmigende Person: Genehmigen von Ressourcenanforderungen

1. [Öffnen der Anforderung im Portal „Mein Zugriff“](entitlement-management-request-approve.md#open-request)
1. [Genehmigen oder Ablehnen von Zugriffsanforderungen](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Anforderer: Anzeigen der Ressourcen, auf die Sie bereits Zugriff haben

1. [Anmelden beim Portal „Mein Zugriff“](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Anzeigen aktiver Zugriffspakete

## <a name="govern-access-for-users-outside-your-organization"></a>Steuern des Zugriffs für Benutzer außerhalb Ihrer Organisation

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Administrator: Zusammenarbeit mit einer externen Partnerorganisation

1. [Informationen zur Funktionsweise des Zugriffs für externe Benutzer](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Überprüfen der Einstellungen für externe Benutzer](entitlement-management-external-users.md#settings-for-external-users)
1. [Hinzufügen einer Verbindung zur externen Organisation](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Zugriffspaketmanager: Zusammenarbeit mit einer externen Partnerorganisation

1. [Erstellen eines neuen Zugriffspakets](entitlement-management-access-package-create.md#start-new-access-package)
1. [Hinzufügen von Gruppen, Teams, Anwendungen oder SharePoint-Websites zum Zugriffspaket](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Hinzufügen einer Anforderungsrichtlinie, damit Benutzer, die sich nicht in Ihrem Verzeichnis befinden, Zugriff anfordern können](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Angeben von Ablaufeinstellungen](entitlement-management-access-package-create.md#lifecycle)
1. [Kopieren des Links zum Anfordern des Zugriffspakets](entitlement-management-access-package-settings.md)
1. Senden Sie den Link an Ihren externen Partner (Partnerkontakt), damit dieser ihn für seine Benutzer freigibt

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Anforderer: Anfordern von Zugriff auf Ressourcen als externer Benutzer

1. Suchen Sie den Link für das Zugriffspaket, den Sie von Ihrem Kontakt erhalten haben
1. [Anmelden beim Portal „Mein Zugriff“](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Zugriff anfordern](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Genehmigende Person: Genehmigen von Ressourcenanforderungen

1. [Öffnen der Anforderung im Portal „Mein Zugriff“](entitlement-management-request-approve.md#open-request)
1. [Genehmigen oder Ablehnen von Zugriffsanforderungen](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Anforderer: Anzeigen der Ressourcen, auf die Sie bereits Zugriff haben

1. [Anmelden beim Portal „Mein Zugriff“](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Anzeigen aktiver Zugriffspakete

## <a name="day-to-day-management"></a>Tägliche Verwaltungsaufgaben

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Zugriffspaketmanager: Aktualisieren der Ressourcen für ein Projekt

1. [Video ansehen: Tägliche Verwaltungsaufgaben: Die Dinge haben sich geändert](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Öffnen des Zugriffspakets
1. [Hinzufügen oder Entfernen von Gruppen, Teams, Anwendungen oder SharePoint-Websites](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Zugriffspaketmanager: Aktualisieren der Dauer eines Projekts

1. [Video ansehen: Tägliche Verwaltungsaufgaben: Die Dinge haben sich geändert](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Öffnen des Zugriffspakets
1. [Öffnen der Lebenszykluseinstellungen](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Aktualisieren der Ablaufeinstellungen](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Zugriffspaketmanager: Aktualisieren der Genehmigungsweise für den Zugriff bei einem Projekt

1. [Video ansehen: Tägliche Verwaltungsaufgaben: Die Dinge haben sich geändert](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Öffnen einer vorhandenen Richtlinie mit Anforderungseinstellungen](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Aktualisieren der Genehmigungseinstellungen](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Zugriffspaketmanager: Aktualisieren der Beteiligten an einem Projekt

1. [Video ansehen: Tägliche Verwaltungsaufgaben: Die Dinge haben sich geändert](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Entfernen von Benutzern, die keinen Zugriff mehr benötigen](entitlement-management-access-package-assignments.md)
1. [Öffnen einer vorhandenen Richtlinie mit Anforderungseinstellungen](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Hinzufügen von Benutzern, die Zugriff benötigen](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Zugriffspaketmanager: Direktes Zuweisen bestimmter Benutzer zu einem Zugriffspaket

1. [Wenn Benutzer unterschiedliche Lebenszykluseinstellungen benötigen, fügen Sie dem Zugriffspaket eine neue Richtlinie hinzu](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Direktes Zuweisen bestimmter Benutzer zu dem Zugriffspaket](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Zuweisungen und Berichte

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Administrator: Anzeigen der Zugriffszuweisungen (Benutzer) für ein Zugriffspaket

1. Öffnen eines Zugriffspakets
1. [Anzeigen von Zuweisungen](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Archivieren von Berichten und Protokollen](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Administrator: Anzeigen der den Benutzern zugewiesenen Ressourcen

1. [Anzeigen von Zugriffspaketen für einen Benutzer](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Anzeigen der Ressourcenzuweisungen für einen Benutzer](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Programmgesteuerte Verwaltung

Mithilfe von Microsoft Graph können Sie auch Zugriffspakete, Kataloge, Richtlinien, Anforderungen und Zuweisungen verwalten.  Ein Benutzer in einer passenden Rolle mit einer Anwendung, die über die delegierte Berechtigung `EntitlementManagement.Read.All` oder `EntitlementManagement.ReadWrite.All` verfügt, kann die [Berechtigungsverwaltungs-API](/graph/tutorial-access-package-api) aufrufen.  Eine Anwendung mit diesen Anwendungsberechtigungen kann ebenfalls viele dieser API-Funktionen verwenden (mit Ausnahme der Verwaltung von Ressourcen in Katalogen und Zugriffspaketen).

## <a name="next-steps"></a>Nächste Schritte

- [Delegierung und Rollen](entitlement-management-delegate.md)
- [Anforderungsprozess und E-Mail-Benachrichtigungen](entitlement-management-process.md)