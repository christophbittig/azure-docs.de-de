---
title: Grundlagen der Autorisierung | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr zu den Grundlagen der Autorisierung in der Microsoft Identity Platform.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/23/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: johngarland, mamarxen, ianbe, marsma
ms.openlocfilehash: 4dcdb90daa9bb8cd1df79ec399eb9372fca02c61
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037188"
---
# <a name="authorization-basics"></a>Grundlagen der Autorisierung

Die **Autorisierung** (manchmal als *AuthZ* abgekürzt) wird verwendet, um Berechtigungen festzulegen, die zum Auswerten des Zugriffs auf Ressourcen oder Funktionen verwendet werden.  Im Gegensatz dazu wird die **Authentifizierung** (manchmal als *AuthN* abgekürzt) verwendet, um zu bestätigen, dass eine Entität, z. B. ein Benutzer oder ein Dienst, tatsächlich die vorgegebene Entität ist.

Bei der Autorisierung kann u. a. angegeben werden, auf welche Funktionen (oder Ressourcen) eine Entität zugreifen darf oder auf welche Daten diese Entität zugreifen und was sie mit diesen Daten tun kann. Dies wird häufig als *Zugriffssteuerung* bezeichnet.

> [!NOTE]
> Authentifizierung und Autorisierung sind Konzepte, die nicht nur auf Benutzer beschränkt sind. Dienste oder Daemonanwendungen sind häufig so aufgebaut, dass Anforderungen für Ressourcen nicht im Namen eines bestimmten Benutzers, sondern im eigenen Namen gestellt werden. Bei der Erörterung dieser Themen bezieht sich der Begriff „Entität“ auf einen Benutzer oder eine Anwendung.


## <a name="authorization-approaches"></a>Autorisierungsansätze

Es gibt mehrere gängige Ansätze für die Autorisierung. Die [rollenbasierte Zugriffssteuerung](./custom-rbac-for-developers.md) ist derzeit der gängigste Ansatz bei Microsoft Identity Platform.


### <a name="authentication-as-authorization"></a>Authentifizierung als Autorisierung 

Die einfachste Form der Autorisierung besteht wahrscheinlich darin, den Zugriff abhängig davon zu gewähren oder zu verweigern, ob die Entität, die eine Anforderung stellt, authentifiziert wurde. Wenn der Anforderer nachweisen kann, dass er die vorgegebene Entität ist, kann er auf die geschützten Ressourcen oder Funktionen zugreifen.

### <a name="access-control-lists"></a>Zugriffssteuerungslisten

Die Autorisierung über Zugriffssteuerungslisten (ACLs) umfasst die Verwaltung expliziter Listen bestimmter Entitäten, die Zugriff auf eine Ressource oder Funktion haben oder nicht. ACLs ermöglichen eine feinere Kontrolle über die Authentifizierung als Autorisierung. Deren Verwaltung wird aber mit zunehmender Anzahl der Entitäten schwierig.

### <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung 

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) ist wahrscheinlich der gängigste Ansatz zum Erzwingen der Autorisierung in Anwendungen. Bei der Verwendung von RBAC werden Rollen definiert, um die Arten von Aktivitäten zu beschreiben, die eine Entität ausführen kann. Ein Anwendungsentwickler gewährt Zugriff für Rollen und nicht für einzelne Entitäten. Ein Administrator kann dann verschiedenen Entitäten Rollen zuweisen, um zu steuern, welche Entitäten auf welche Ressourcen und Funktionen zugreifen können.

In erweiterten RBAC-Implementierungen können Rollen Berechtigungssätzen zugeordnet werden, wobei eine Berechtigung eine präzise Aktion oder Aktivität beschreibt, die ausgeführt werden kann. Rollen werden dann als Kombinationen von Berechtigungen konfiguriert. Der Gesamtberechtigungssatz der Entitäten für eine Anwendung wird als Schnittmenge der Berechtigungen, die den verschiedenen Rollen gewährt werden, denen die Entität zugewiesen ist, berechnet. Ein gutes Beispiel für diesen Ansatz ist die RBAC-Implementierung, die den Zugriff auf Ressourcen in Azure-Abonnements steuert.

> [!NOTE]
> Die [rollenbasierte Zugriffssteuerung für Anwendungen](./custom-rbac-for-developers.md) unterscheidet sich von [Azure RBAC](/azure/role-based-access-control/overview) und [Azure AD RBAC](../roles/custom-overview.md#understand-azure-ad-role-based-access-control). Benutzerdefinierte Azure-Rollen und integrierte Rollen gehören beide zu Azure RBAC, mit der Sie Azure-Ressourcen verwalten können. Azure AD RBAC ermöglicht Ihnen die Verwaltung von Azure AD-Ressourcen.

### <a name="attribute-based-access-control"></a>Attributbasierte Zugriffssteuerung 

Die attributbasierte Zugriffssteuerung (Attribute-Based Access Control, ABAC) ist ein präziserer Zugriffssteuerungsmechanismus. Bei diesem Ansatz werden Regeln auf Attribute der Entität, die Ressourcen, auf die zugegriffen wird, und die aktuelle Umgebung angewendet, um zu bestimmen, ob der Zugriff auf bestimmte Ressourcen oder Funktionen zulässig ist. Ein Beispiel dafür wäre, dass Benutzer, die Manager sind, an Werktagen von 9:00 bis 17:00 Uhr nur auf Dateien zugreifen dürfen, die mit dem Metadatentag „Manager nur während der Arbeitszeit“ gekennzeichnet sind. In diesem Fall wird der Zugriff durch Überprüfung des Benutzerattributs (Status als Manager), des Ressourcenattributs (Metadatentag für eine Datei) und eines Umgebungsattributs (aktuelle Uhrzeit) bestimmt.

Ein Vorteil von ABAC besteht darin, dass durch Auswertung von Regeln und Bedingungen eine präzisere und dynamische Zugriffssteuerung erreicht werden kann, ohne dass eine große Anzahl sehr spezifischer Rollen und RBAC-Zuweisungen erstellt werden muss.

Eine Methode zum Erreichen von ABAC mit Azure Active Directory ist die Verwendung [dynamischer Gruppen](../enterprise-users/groups-create-rule.md). Dynamische Gruppen ermöglichen Administratoren das dynamische Zuweisen von Benutzern zu Gruppen basierend auf bestimmten Benutzerattributen mit gewünschten Werten.  So kann beispielsweise eine Gruppe für Autoren erstellt werden, in der alle Benutzer mit der Aufgabenbezeichnung „Autor“ dynamisch der Gruppe „Autoren“ zugewiesen werden.  Dynamische Gruppen können in Kombination mit RBAC für die Autorisierung verwendet werden. Dabei werden Rollen Gruppen zugeordnet und Benutzer dynamisch Gruppen zugewiesen.

[Azure ABAC](../../role-based-access-control/conditions-overview.md) ist ein Beispiel für eine ABAC-Lösung, die derzeit verfügbar ist. Azure ABAC baut auf Azure RBAC auf und fügt Rollenzuweisungsbedingungen auf der Grundlage von Attributen im Kontext bestimmter Aktionen hinzu. 

## <a name="implementing-authorization"></a>Implementieren der Autorisierung

Die Autorisierungslogik wird häufig intern in Anwendungen oder Lösungen implementiert, für die eine Zugriffssteuerung erforderlich ist. In vielen Fällen stellen Anwendungsentwicklungsplattformen Middleware oder andere API-Lösungen zur Verfügung, die die Implementierung der Autorisierung vereinfachen. Beispiele hierfür sind die Verwendung von [AuthorizeAttribute](/aspnet/core/security/authorization/simple?view=aspnetcore-5.0&preserve-view=true) in ASP.NET oder [Route Guards](./scenario-spa-sign-in.md?tabs=angular2#sign-in-with-a-pop-up-window) in Angular.

Bei Autorisierungsansätzen, die auf Informationen zur authentifizierten Entität aufbauen, wertet eine Anwendung Informationen aus, die während der Authentifizierung ausgetauscht werden, z. B. Informationen, die in einem [Sicherheitstoken](./security-tokens.md) bereitgestellt wurden. Für Informationen, die nicht in einem Sicherheitstoken enthalten sind, kann eine Anwendung zusätzliche Aufrufe an externe Ressourcen durchführen.

Es ist nicht unbedingt erforderlich, dass Entwickler die Autorisierungslogik vollständig in ihre Anwendungen einbetten. Stattdessen können dedizierte Autorisierungsdienste verwendet werden, um die Implementierung und Verwaltung der Autorisierung zu zentralisieren.


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Implementierung einer benutzerdefinierten rollenbasierten Zugriffssteuerung in Anwendungen finden Sie unter [Rollenbasierte Zugriffssteuerung für Anwendungsentwickler](./custom-rbac-for-developers.md).
- Unter [Anwendungsmodell](./application-model.md) erhalten Sie Informationen zum Registrierungsvorgang für Ihre Anwendung, damit diese in die Microsoft Identity Platform integriert werden kann.
- Ein Beispiel für die Konfiguration einer einfachen authentifizierungsbasierten Autorisierung finden Sie unter [Konfigurieren Ihrer App Service- oder Azure Functions-App zur Verwendung der Azure AD-Anmeldung](/azure/app-service/configure-authentication-provider-aad).
