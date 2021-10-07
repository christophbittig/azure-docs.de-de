---
title: Erhöhen der App-Sicherheit nach dem Prinzip der geringsten Rechte
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie gemäß dem Prinzip der geringsten Rechte die Sicherheit Ihrer Anwendung und deren Daten erhöhen und mit welchen Features von Microsoft Identity Platform Sie geringstmögliche Zugriffsberechtigungen implementieren.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: yuhko, saumadan, marsma
ms.openlocfilehash: a7e504bfd13d89ba417067faf88cf17cf12e1d0b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786482"
---
# <a name="enhance-security-with-the-principle-of-least-privilege"></a>Erhöhen der Sicherheit nach dem Prinzip der geringsten Rechte

Zur Erhöhung der Informationssicherheit sollte Benutzern und Anwendungen gemäß dem Prinzip der geringsten Rechte nur Zugriff auf Daten und Vorgänge gewährt werden, die sie für die Ausführung ihrer Aufgaben benötigen.

Befolgen Sie die hier beschriebenen Schritte, um die Angriffsfläche Ihrer Anwendung und die Auswirkungen einer möglichen Sicherheitsverletzung (den *Auswirkungsbereich*) für Ihre in Microsoft Identity Platform integrierte Anwendung zur reduzieren.

## <a name="recommendations-at-a-glance"></a>Empfehlungen auf einen Blick

- Vermeiden Sie **überprivilegierte** Anwendungen, indem Sie *nicht verwendete* und *reduzierbare* Berechtigungen aufheben.
- Verwenden Sie das **Einwilligungsframework** von Identity Platform, um bei Zugriffsanforderungen auf geschützte Daten die Einwilligung eines Mitarbeiters zu erzwingen.
- Wenden Sie das Prinzip der geringsten Rechte beim **Erstellen** von Anwendungen in allen Entwicklungsphasen an.
- **Überprüfen** Sie Ihre bereitgestellten Anwendungen regelmäßig, um überprivilegierte Apps zu identifizieren.

## <a name="whats-an-overprivileged-application"></a>Was ist eine *überprivilegierte* Anwendung?

Eine Anwendung wird als „überprivilegiert“ bezeichnet, wenn sie über **nicht verwendete** oder **reduzierbare** Berechtigungen verfügt. Durch nicht verwendete und reduzierbare Berechtigungen kann es zu nicht autorisiertem oder unbeabsichtigtem Zugriff auf Daten oder Vorgänge kommen, der für die Ausführung der Aufgaben einer App oder ihrer Benutzer nicht erforderlich ist.

:::row:::
   :::column span="":::
      ### <a name="unused-permissions"></a>Nicht verwendete Berechtigungen

        Bei einer nicht verwendeten Berechtigung handelt es sich um eine Berechtigung, die einer Anwendung erteilt wurde, deren dadurch verfügbar gemachte API bzw. Vorgang bei regulärer Verwendung jedoch nicht von der App aufgerufen wird.

        - **Beispiel**: Eine Anwendung zeigt eine Liste von Dateien an, die im OneDrive-Speicher des angemeldeten Benutzers gespeichert sind. Dies geschieht durch Aufrufen der Microsoft Graph-API und mithilfe der Berechtigung [Files.Read](/graph/permissions-reference). Der App wurde jedoch auch die Berechtigung [Calendars.Read](/graph/permissions-reference#calendars-permissions) erteilt, obwohl sie keine Kalenderfeatures bereitstellt und die Kalender-API nicht aufruft.

        - **Sicherheitsrisiko**: Nicht verwendete Berechtigungen stellen ein Sicherheitsrisiko im Bereich der *horizontalen Rechteausweitung* dar. Eine Entität, die ein Sicherheitsrisiko in Ihrer Anwendung ausnutzt, könnte über eine nicht verwendete Berechtigung Zugriff auf eine API bzw. einen Vorgang erhalten, der bei beabsichtigter Verwendung von der Anwendung weder unterstützt noch zugelassen wird.

        - **Abhilfe**: Entfernen Sie alle Berechtigungen, die nicht in API-Aufrufen Ihrer Anwendung verwendet werden.
   :::column-end:::
   :::column span="":::
      ### <a name="reducible-permissions"></a>Reduzierbare Berechtigungen

        Eine reduzierbare Berechtigung weist eine Entsprechung mit niedrigeren Berechtigungen auf, die der Anwendung und deren Benutzern dennoch den Zugriff bereitstellt, der für die Ausführung der jeweiligen Aufgaben erforderlich ist.

        - **Beispiel**: Eine Anwendung zeigt die Profilinformationen des angemeldeten Benutzers durch Aufrufen der Microsoft Graph-API an. Die Profilbearbeitung wird jedoch nicht unterstützt. Der App wurde jedoch die Berechtigung [User.ReadWrite.All](/graph/permissions-reference#user-permissions) erteilt. Hier wird die Berechtigung *User.ReadWrite.All* als reduzierbar angesehen, da die Berechtigung *User.Read.All* ausreicht, um schreibgeschützten Zugriff auf die Profildaten des Benutzers zu gewähren.

        - **Sicherheitsrisiko**: Reduzierbare Berechtigungen stellen ein Sicherheitsrisiko im Bereich der *vertikalen Rechteausweitung* dar. Eine Entität, die ein Sicherheitsrisiko in Ihrer Anwendung ausnutzt, könnte über eine reduzierbare Berechtigung nicht autorisierten Zugriff auf Daten erhalten bzw. Vorgänge ausführen, die normalerweise nicht Teil der Rolle dieser Entität sind.

        - **Abhilfe**: Ersetzen Sie alle reduzierbaren Berechtigungen in Ihrer Anwendung durch die jeweilige Entsprechung mit den geringstmöglichen Berechtigungen, die für die Ausführung der gewünschten Funktionen ausreichen.
   :::column-end:::
:::row-end:::

Vermeiden Sie Sicherheitsrisiken durch nicht verwendete und reduzierbare Berechtigungen, indem Sie immer nur die *geringstmöglichen* Berechtigungen erteilen. In anderen Worten: Eine Anwendung bzw. ein Benutzer sollten immer nur diejenigen Zugriffsrechte erhalten, die für die Ausführung der erforderlichen Aufgaben auch benötigt werden.

## <a name="use-consent-to-control-access-to-data"></a>Steuern des Datenzugriffs mithilfe von Einwilligung

Die meisten Anwendungen, die Sie erstellen, benötigen Zugriff auf geschützte Daten, zu dem der Besitzer der Daten seine [Einwilligung](application-consent-experience.md#consent-and-permissions) geben muss. Eine Einwilligung kann auf unterschiedliche Weise erteilt werden. Dies kann etwa durch einen Mandantenadministrator erfolgen, der *allen* Benutzern in einem Azure AD-Mandanten eine Zugriffsberechtigung erteilen kann, oder durch die Benutzer der Anwendung selbst.

Wenn eine Anwendung auf dem Gerät Ihres Benutzers Zugriff auf geschützte Daten anfordert, sollte sie nach der Einwilligung des Benutzers fragen, bevor sie Zugang zu den geschützten Daten gewährt. Der Endbenutzer muss die Einwilligung für die angeforderte Berechtigung erteilen (oder verweigern), bevor die Anwendung fortfahren kann.

:::image type="content" source="./media/least-privilege-best-practice/api-permissions.png" alt-text="Screenshot des Azure-Portals mit dem Bereich „API-Berechtigungen“ einer App-Registrierung":::

## <a name="least-privilege-during-app-development"></a>Prinzip der geringsten Rechte bei der App-Entwicklung

Als Entwickler einer Anwendung liegt es in *Ihrer* Verantwortung, die Sicherheit der App und der Benutzerdaten zu gewährleisten.

Befolgen Sie die folgenden Richtlinien während der Anwendungsentwicklung, um keine überprivilegierte App zu erstellen:

- Machen Sie sich vollumfänglich mit den Berechtigungen vertraut, die für die API-Aufrufe Ihrer Anwendung erforderlich sind.
- Identifizieren Sie mithilfe des [Graph-Explorers](https://developer.microsoft.com/graph/graph-explorer) die jeweils geringstmögliche Berechtigung für einen erforderlichen API-Aufruf Ihrer App.
- Ermitteln Sie die entsprechenden [Berechtigungen](/graph/permissions-reference) aufsteigend nach dem Berechtigungsumfang.
- Entfernen Sie alle doppelten Berechtigungen, wenn Ihre App API-Aufrufe mit überlappenden Berechtigungen ausführt.
- Nutzen Sie für Ihre Anwendung nur die geringsten Berechtigungen, indem Sie in der Berechtigungsliste jeweils die geringstmögliche Berechtigung auswählen.

## <a name="least-privilege-for-deployed-apps"></a>Prinzip der geringsten Rechte für bereitgestellte Apps

Häufig schrecken Organisationen davor zurück, bereits ausgeführte Anwendungen zu ändern, um Auswirkungen auf den laufenden Geschäftsbetrieb zu vermeiden. Ihre Organisation sollte dennoch abwägen, ob das Risiko eines Sicherheitsincidents, der durch überprivilegierte Berechtigungen Ihrer App ermöglicht oder verschlimmert wird, eine geplante Aktualisierung der Anwendung nicht aufwiegt.

Stellen Sie mithilfe der folgenden Standardmethoden sicher, dass bereitgestellte Apps in Ihrer Organisation nicht überprivilegiert sind oder es im Laufe der Zeit werden:

- Werten Sie die von Ihren Anwendungen ausgeführten API-Aufrufe aus.
- Ermitteln Sie mithilfe des [Graph-Explorers](https://developer.microsoft.com/graph/graph-explorer) und der [Microsoft Graph](/graph/overview)-Dokumentation die erforderlichen und geringstmöglichen Berechtigungen.
- Überwachen Sie die Berechtigungen, die Benutzern oder Anwendungen erteilt werden.
- Aktualisieren Sie Ihre Anwendungen, damit diese die geringstmöglichen Berechtigungen aufweisen.
- Überprüfen Sie die Berechtigungen regelmäßig dahingehend, ob alle autorisierten Berechtigungen weiterhin relevant sind.

## <a name="next-steps"></a>Nächste Schritte

**Zugriff auf geschützte Ressourcen und Einwilligung**

Weitere Informationen zum Konfigurieren des Zugriffs auf geschützte Ressourcen und der Benutzeroberfläche zum Erteilen der Zugriffseinwilligung finden Sie in den folgenden Artikeln:

- [Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](../develop/v2-permissions-and-consent.md)
- [Grundlegendes zur Zustimmung für Azure AD-Anwendungen](../develop/application-consent-experience.md)

**Zero Trust**: Nutzen Sie die hier beschriebenen Maßnahmen gemäß dem Prinzip der geringsten Rechte im Rahmen der proaktiven [Zero Trust-Sicherheitsstrategie](/security/zero-trust/) in Ihrer Organisation.
