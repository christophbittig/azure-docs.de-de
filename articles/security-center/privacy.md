---
title: Verwalten von Benutzerdaten in Microsoft Defender für Cloud | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Benutzerdaten in Microsoft Defender für Cloud verwalten. Bei der Verwaltung der Benutzerdaten haben Sie auch die Möglichkeit, auf Daten zuzugreifen, Daten zu löschen oder zu exportieren.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 24a40de1a5b085110571929c4e35295a5ff131dc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096074"
---
# <a name="manage-user-data-in-microsoft-defender-for-cloud"></a>Verwalten von Benutzerdaten in Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dieser Artikel enthält Informationen zur Verwaltung der Benutzerdaten in Microsoft Defender für Cloud. Bei der Verwaltung der Benutzerdaten haben Sie auch die Möglichkeit, auf Daten zuzugreifen, Daten zu löschen oder zu exportieren.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Ein Defender für Cloud-Benutzer, dem die Rolle „Leser“, „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen wird, kann mit dem Tool auf die Kundendaten zugreifen. Weitere Informationen zu den Rollen „Kontoadministrator“, „Leser“, „Besitzer“ und „Mitwirkender“ finden Sie unter [Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/built-in-roles.md). Siehe [Azure-Abonnementadministratoren](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Suchen und Ermitteln von personenbezogenen Daten
Ein Defender für Cloud-Benutzer kann seine personenbezogenen Daten über das Azure-Portal anzeigen. In Defender für Cloud werden nur Details für einen Sicherheitskontakt gespeichert, wie z. B. E-Mail-Adressen und Telefonnummern. Weitere Informationen finden Sie unter [Bereitstellen von Details für einen Sicherheitskontakt in Microsoft Defender für Cloud](configure-email-notifications.md).

Im Azure-Portal kann ein Benutzer mithilfe des Features „Just-In-Time“ von Defender für Cloud für den Zugriff auf VMs zulässige IP-Konfigurationen anzeigen. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf virtuelle Computer mithilfe des Just-In-Time-Features](just-in-time-access-usage.md).

Im Azure-Portal kann ein Benutzer von Defender für Cloud bereitgestellte Sicherheitswarnungen einschließlich IP-Adressen und Details zu Angreifern anzeigen. Weitere Informationen finden Sie unter [Verwalten von und Reagieren auf Sicherheitswarnungen in Microsoft Defender für Cloud](managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Klassifizieren personenbezogener Daten
Im Feature „Sicherheitskontakt“ von Defender für Cloud gefundene personenbezogene Daten müssen nicht klassifiziert werden. Als Daten gespeichert sind eine E-Mail-Adresse (oder mehrere E-Mail-Adressen) und eine Telefonnummer. [Kontaktdaten](configure-email-notifications.md) werden von Defender für Cloud überprüft.

Die mit dem Feature [Just-In-Time](just-in-time-access-usage.md) von Defender für Cloud gespeicherten IP-Adressen und Portnummern müssen nicht klassifiziert werden.

Nur ein Benutzer, dem die Rolle „Administrator“ zugewiesen ist, kann personenbezogene Daten durch das [Anzeigen von Warnungen](managing-and-responding-alerts.md) in Defender für Cloud klassifizieren.

## <a name="securing-and-controlling-access-to-personal-data"></a>Sichern und Steuern des Zugriffs auf personenbezogene Daten
Ein Defender für Cloud-Benutzer, dem die Rolle „Leser“, „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann auf [Sicherheitskontaktdaten](configure-email-notifications.md) zugreifen.

Ein Defender für Cloud-Benutzer, dem die Rolle „Leser“, „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann auf die [Just-In-Time-Richtlinien](just-in-time-access-usage.md) zugreifen.

Ein Defender für Cloud-Benutzer, dem die Rolle „Leser“, „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann seine [Warnungen](managing-and-responding-alerts.md) anzeigen.

## <a name="updating-personal-data"></a>Aktualisieren von personenbezogenen Daten
Ein Defender für Cloud-Benutzer, dem die Rolle „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann [Sicherheitskontaktdaten](configure-email-notifications.md) über das Azure-Portal aktualisieren.

Ein Defender für Cloud-Benutzer, dem die Rolle „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann seine [Just-In-Time-Richtlinien](just-in-time-access-usage.md) aktualisieren.

Ein Kontoadministrator kann keine Warnungsvorfälle bearbeiten. [Warnungsvorfälle](managing-and-responding-alerts.md) gelten als Sicherheitsdaten und sind schreibgeschützt.

## <a name="deleting-personal-data"></a>Löschen von personenbezogenen Daten
Ein Defender für Cloud-Benutzer, dem die Rolle „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann [Sicherheitskontaktdaten](configure-email-notifications.md) über das Azure-Portal löschen.

Ein Defender für Cloud-Benutzer, dem die Rolle „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann [Just-In-Time-Richtlinien](just-in-time-access-usage.md) über das Azure-Portal löschen.

Ein Defender für Cloud-Benutzer kann keine Warnungsvorfälle löschen. Aus Sicherheitsgründen gelten [Warnungsvorfälle](managing-and-responding-alerts.md) als schreibgeschützte Daten.

## <a name="exporting-personal-data"></a>Exportieren von personenbezogenen Daten
Ein Defender für Cloud-Benutzer, dem die Rolle „Leser“, „Besitzer“, „Mitwirkender“ oder „Kontoadministrator“ zugewiesen ist, kann [Sicherheitskontaktdaten](configure-email-notifications.md) wie folgt exportieren:

- Kopieren aus dem Azure-Portal
- Ausführen des Azure-REST-API-Aufrufs GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Ein Defender für Cloud-Benutzer, dem die Rolle „Kontoadministrator“ zugewiesen ist, kann die [Just-In-Time-Richtlinien](just-in-time-access-usage.md) mit den IP-Adressen wie folgt exportieren:

- Kopieren aus dem Azure-Portal
- Ausführen des Azure-REST-API-Aufrufs GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Ein Kontoadministrator kann die Warnungsdetails wie folgt exportieren:

- Kopieren aus dem Azure-Portal
- Ausführen des Azure-REST-API-Aufrufs GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Weitere Informationen finden Sie unter [Abrufen von Sicherheitswarnungen (GET-Auflistung)](/previous-versions/azure/reference/mt704050(v=azure.100)).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Einschränken der Verwendung der personenbezogenen Daten für die Profilerstellung oder das Marketing ohne Zustimmung
Ein Defender für Cloud-Benutzer kann sich für die Deaktivierung entscheiden, indem er seine [Sicherheitskontaktdaten](configure-email-notifications.md) löscht.

[Just-in-Time-Daten](just-in-time-access-usage.md) gelten als nicht identifizierbare Daten und werden für einen Zeitraum von 30 Tagen beibehalten.

[Warnungsdaten](managing-and-responding-alerts.md) gelten als Sicherheitsdaten und werden für einen Zeitraum von zwei Jahren beibehalten.

## <a name="auditing-and-reporting"></a>Überwachung und Berichterstellung
Überwachungsprotokolle von Sicherheitskontakt-, Just-In-Time- und Warnungsaktualisierungen werden in [Azure-Aktivitätsprotokollen](../azure-monitor/essentials/platform-logs-overview.md) beibehalten.
