---
title: Benutzerdefinierte rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) für Anwendungsentwickler – Microsoft Identity Platform
description: Hier erfahren Sie, was man unter „benutzerdefinierter RBAC“ versteht und warum es wichtig ist, sie in Ihren Anwendungen zu implementieren.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/15/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: john.garland, maggie.marxen, ian.bennett, marsma
ms.openlocfilehash: fc1154965b037fbc57dcafa30f08fd951721bf6f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486727"
---
# <a name="role-based-access-control-for-application-developers"></a>Rollenbasierte Zugriffssteuerung für Anwendungsentwickler

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) ermöglicht es bestimmten Benutzern oder Gruppen, bestimmte Berechtigungen im Hinblick darauf zu haben, auf welche Ressourcen sie zugreifen können, welche Aktionen sie mit diesen Ressourcen ausführen können und wer welche Ressourcen verwaltet. Die rollenbasierte Zugriffssteuerung bei einer Anwendung unterscheidet sich von der [rollenbasierten Zugriffssteuerung in Azure](../../role-based-access-control/overview.md) und der [rollenbasierten Zugriffssteuerung in Azure AD](../roles/custom-overview.md#understand-azure-ad-role-based-access-control). Benutzerdefinierte Azure-Rollen und integrierte Rollen gehören beide zu Azure RBAC, mit der Sie Azure-Ressourcen verwalten können. Azure AD RBAC ermöglicht Ihnen die Verwaltung von Azure AD-Ressourcen. In diesem Artikel wird die anwendungsspezifische rollenbasierte Zugriffssteuerung erläutert.


## <a name="what-are-roles"></a>Was sind Rollen?

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) ist eine beliebte Methode zum Erzwingen der Autorisierung in Anwendungen. Bei Verwendung von RBAC definiert ein Anwendungsentwickler Rollen, statt einzelne Benutzer oder Gruppen zu autorisieren. Ein Administrator kann dann verschiedenen Benutzern und Gruppen Rollen zuweisen, um zu steuern, wer auf welche Inhalte und Funktionen zugreifen kann.

RBAC unterstützt Sie als App-Entwickler beim Verwalten von Ressourcen und den Möglichkeiten, wie Benutzer diese Ressourcen einsetzen können. Mit RBAC kann ein App-Entwickler auch steuern, auf welche Bereiche einer App Benutzer zugreifen können. Während Administratoren mithilfe der Eigenschaft *Benutzerzuweisung erforderlich* steuern können, welche Benutzer Zugriff auf eine App haben, müssen Entwickler bestimmte Benutzer in der App und deren mögliche Aktionen darin berücksichtigen.

Als App-Entwickler müssen Sie zuerst im Azure AD Admin Center eine Rollendefinition im Registrierungsabschnitt für die App erstellen. Die Rollendefinition enthält einen Wert, der für Benutzer, die dieser Rolle zugewiesen wurden, zurückgegeben wird. Ein Entwickler kann dann mithilfe dieses Werts Anwendungslogik implementieren, um zu bestimmen, welche Aktionen diese Benutzer in einer Anwendung ausführen können oder nicht.

## <a name="options-for-adding-rbac-to-apps"></a>Optionen zum Hinzufügen von RBAC zu Apps

Es gibt mehrere Überlegungen, die beim Einbeziehen der rollenbasierten Zugriffssteuerungsautorisierung in eine Anwendung verwaltet werden müssen. Dazu gehören:
- Definieren der Rollen, die für die Autorisierungsanforderungen einer Anwendung erforderlich sind. 
- Anwenden, Speichern und Abrufen der entsprechenden Rollen für authentifizierte Benutzer. 
- Auswirkungen auf das gewünschte Anwendungsverhalten basierend auf den Rollen, die dem aktuellen Benutzer zugewiesen wurden. 

Sobald Sie die Rollen definiert haben, unterstützt Microsoft Identity Platform verschiedene Lösungen, die zum Anwenden, Speichern und Abrufen von Rolleninformationen für authentifizierte Benutzer verwendet werden können. Diese Lösungen umfassen App-Rollen, Azure AD-Gruppen und die Verwendung von benutzerdefinierten Datenspeichern für Benutzerrolleninformationen.

Entwickler können ihre eigene Implementierung flexibel dafür bereitstellen, wie Rollenzuweisungen als Anwendungsberechtigungen interpretiert werden sollen. Dies kann die Nutzung von Middleware oder anderen Funktionen umfassen, die von der Plattform ihrer Anwendungen oder zugehörigen Bibliotheken bereitgestellt werden. Apps erhalten normalerweise Benutzerrolleninformationen als Ansprüche und entscheiden über Benutzerberechtigungen anhand dieser Ansprüche.

### <a name="app-roles"></a>App-Rollen

Azure AD unterstützt das Deklarieren von App-Rollen für eine Anwendungsregistrierung. Wenn sich ein Benutzer bei einer Anwendung anmeldet, enthält Azure AD einen [Rollenanspruch](./access-tokens.md#payload-claims) für jede Rolle, die dem Benutzer für diese Anwendung gewährt wurde. Anwendungen, die Token mit diesen Ansprüchen empfangen, können dann anhand dieser Informationen und basierend auf den diesem Benutzer zugewiesenen Rollen bestimmen, welche Berechtigungen er ausüben darf.

### <a name="groups"></a>Gruppen

Entwickler können RBAC auch mithilfe von [Azure AD-Gruppen](../fundamentals/active-directory-manage-groups.md) in ihren Anwendungen implementieren. Dabei werden die Mitgliedschaften der Benutzer in spezifischen Gruppen als deren Rollenmitgliedschaften interpretiert. Bei Verwendung von Azure AD-Gruppen enthält Azure AD einen [Gruppenanspruch](./access-tokens.md#payload-claims) mit den Bezeichnern aller Gruppen, denen der Benutzer innerhalb des aktuellen Azure AD-Mandanten zugewiesen wurde. Anwendungen, die Token mit diesen Ansprüchen empfangen, können dann anhand dieser Informationen und basierend auf den diesem Benutzer zugewiesenen Rollen bestimmen, welche Berechtigungen er ausüben darf.

> [!IMPORTANT]
> Beim Arbeiten mit Gruppen müssen Entwickler das Konzept eines [Überschreitungsanspruchs](./access-tokens.md#payload-claims) beachten. Wenn ein Benutzer das Überschreitungslimit (150 bei SAML-Token, 200 bei JWT-Token, 6 bei Nutzung des impliziten Flows) überschreitet, gibt Azure AD standardmäßig keinen Gruppenanspruch im Token aus. Stattdessen wird ein „Überschreitungsanspruch“ in das Token einbezogen. Er gibt an, dass der Consumer des Tokens die Graph-API abfragen muss, um die Gruppenmitgliedschaften des Benutzers abzurufen. Weitere Informationen zum Arbeiten mit Überschreitungsansprüchen finden Sie unter [Ansprüche in Zugriffstoken](./access-tokens.md#claims-in-access-tokens). Es ist möglich, nur die einer Anwendung zugewiesenen Gruppen auszugeben, obwohl die [gruppenbasierte Zuweisung](../manage-apps/assign-user-or-group-access-portal.md) die Azure Active Directory Premium P1- oder P2-Edition erfordert.

### <a name="custom-data-store"></a>Benutzerdefinierter Datenspeicher

App-Rollen und -Gruppen speichern Informationen zu Benutzerzuweisungen im Verzeichnis „Azure AD“. Eine weitere Möglichkeit zum Verwalten von Benutzerrolleninformationen, die Entwicklern zur Verfügung stehen, ist deren Verwaltung außerhalb des Verzeichnisses in einem benutzerdefinierten Datenspeicher. Das kann z. B. eine SQL-Datenbank, ein Azure-Tabellenspeicher oder eine Azure Cosmos DB-Tabellen-API sein.

Die Verwendung von benutzerdefiniertem Speicher ermöglicht Entwicklern eine zusätzliche Anpassung und Kontrolle darüber, wie Benutzern Rollen zugewiesen und wie sie dargestellt werden. Diese zusätzliche Flexibilität führt jedoch auch zu mehr Verantwortung. Beispielsweise gibt es zurzeit keinen Mechanismus zum Einbeziehen dieser Informationen in Token, die von Azure AD zurückgegeben werden. Wenn Entwickler Rolleninformationen in einem benutzerdefinierten Datenspeicher verwalten, müssen die Apps die Rollen abrufen können. Dies geschieht normalerweise mithilfe von Erweiterungspunkten, die in der Middleware für die zum Entwickeln der Anwendung genutzten Plattform definiert wird. Darüber hinaus sind Entwickler für die ordnungsgemäße Sicherung des benutzerdefinierten Datenspeichers zuständig.

Mithilfe von [benutzerdefinierten Azure AD B2C-Richtlinien](../../active-directory-b2c/custom-policy-overview.md) können Sie mit benutzerdefinierten Datenspeichern interagieren und benutzerdefinierte Ansprüche in ein Token einbeziehen.

## <a name="choosing-an-approach"></a>Auswählen eines Ansatzes

Im Allgemeinen sind App-Rollen die empfohlene Lösung. App-Rollen stellen das einfachste Programmiermodell bereit und sind für RBAC-Implementierungen vorgesehen. Allerdings können bestimmte Anwendungsanforderungen darauf hinweisen, dass ein anderer Ansatz eine bessere Lösung wäre.

Entwickler können mithilfe von App-Rollen steuern, ob ein Benutzer sich bei einer App anmelden kann oder eine App ein Zugriffstoken für eine Web-API abrufen kann. App-Rollen werden von Entwicklern gegenüber Azure AD-Gruppen bevorzugt, wenn sie die Parameter der Autorisierung in ihrer App selbst beschreiben und steuern möchten. Beispielsweise wird eine App, die Gruppen für die Autorisierung verwendet, im nächsten Mandanten nicht mehr funktionieren, da sowohl Gruppen-ID als auch Name unterschiedlich sein könnte. Eine App, die App-Rollen verwendet, bleibt sicher. Tatsächlich ist das Zuweisen von Gruppen zu App-Rollen mit SaaS-Apps aus denselben Gründen beliebt.

Obwohl entweder App-Rollen oder Gruppen für die Autorisierung verwendet werden können, können wichtige Unterschiede zwischen ihnen beeinflussen, was die beste Lösung bei einem bestimmten Szenario ist.

|          |App-Rollen |Azure AD-Gruppen |Benutzerdefinierter Datenspeicher|
|----------|-----------|------------|-----------------|
|**Programmiermodell** |**Die einfachste Lösung**. Sie sind für eine Anwendung spezifisch und werden in der App-Registrierung definiert. Sie werden mit der Anwendung verschoben.|**Komplexer**. Gruppen-IDs variieren zwischen Mandanten, und Überschreitungsansprüche müssen möglicherweise berücksichtigt werden. Gruppen sind nicht spezifisch für eine App, sondern für einen Azure AD-Mandanten.|**Am komplexesten**. Entwickler müssen Mittel implementieren, mit denen Rolleninformationen sowohl gespeichert als auch abgerufen werden.|
|**Rollenwerte sind zwischen Azure AD-Mandanten statisch**|Ja  |Nein |Ist abhängig von der Implementierung.|
|**Rollenwerte können in mehreren Anwendungen verwendet werden**|Nein. Außer wenn die Rollenkonfiguration bei jeder App-Registrierung dupliziert wird.|Ja |Ja |
|**Informationen werden im Verzeichnis gespeichert**|Ja  |Ja |Nein |
|**Informationen werden per Token übermittelt**|Ja (Rollenanspruch)  |Ja (Im Fall einer Überschreitung müssen *Gruppenansprüche* möglicherweise zur Laufzeit abgerufen werden) |Nein. Wird zur Laufzeit über benutzerdefinierten Code abgerufen. |
|**Gültigkeitsdauer**|Ist in der App-Registrierung im Verzeichnis enthalten. Wird beim Entfernen der App-Registrierung entfernt.|Ist im Verzeichnis enthalten. Bleibt selbst dann intakt, wenn die App-Registrierung entfernt wird. |Ist im benutzerdefinierten Datenspeicher enthalten. Ist nicht an die App-Registrierung gebunden.|


## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen von App-Rollen zu Ihrer Anwendung und Empfangen der Rollen im Token](./howto-add-app-roles-in-azure-ad-apps.md).
- [Registrieren einer Anwendung bei der Microsoft Identity Platform](./quickstart-register-app.md).
- [Azure-Identitätsverwaltung und Sicherheit der Zugriffssteuerung – Bewährte Methoden](../../security/fundamentals/identity-management-best-practices.md).