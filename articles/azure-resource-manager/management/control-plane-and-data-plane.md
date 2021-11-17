---
title: Vorgänge auf Steuerungsebene und auf Datenebene
description: Beschreibt den Unterschied zwischen Vorgängen auf Steuerungsebene und auf Datenebene. Vorgänge auf Steuerungsebene werden von Azure Resource Manager verarbeitet. Vorgänge auf Datenebene werden von einem Dienst verarbeitet.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: c5e72693c751086f17958c39c96d12624e478b99
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893403"
---
# <a name="azure-control-plane-and-data-plane"></a>Azure-Steuerungsebene und -Datenebene

Azure-Vorgänge können in zwei Kategorien unterteilt werden: auf Steuerungsebene und auf Datenebene. In diesem Artikel werden die Unterschiede zwischen diesen beiden Arten von Vorgängen beschrieben.

Sie verwenden die Steuerungsebene, um Ressourcen in Ihrem Abonnement zu verwalten. Sie verwenden die Datenebene, um Funktionen zu verwenden, die von Ihrer Instanz eines Ressourcentyps verfügbar gemacht werden.

Beispiel:

* Sie erstellen einen virtuellen Computer über die Steuerungsebene. Nachdem der virtuelle Computer erstellt wurde, interagieren Sie mit diesem über Vorgänge auf Datenebene, z. B. über RDP (Remotedesktopprotokoll).

* Sie erstellen ein Speicherkonto über die Steuerungsebene. Sie verwenden die Datenebene, um Daten im Speicherkonto zu lesen und zu schreiben.

* Sie erstellen eine Cosmos-Datenbank über die Steuerungsebene. Zum Abfragen von Daten in der Cosmos-Datenbank verwenden Sie die Datenebene.

## <a name="control-plane"></a>Steuerungsebene

Alle Anforderungen für Vorgänge auf Steuerungsebene werden an die Azure Resource Manager-URL gesendet. Diese URL variiert je nach Azure-Umgebung.

* Für Azure Global ist die URL `https://management.azure.com`.
* Für Azure Government ist die URL `https://management.usgovcloudapi.net/`.
* Für Azure Deutschland ist die URL `https://management.microsoftazure.de/`.
* Für Microsoft Azure China 21Vianet ist die URL `https://management.chinacloudapi.cn`.

Informationen zu den Vorgängen, die die Azure Resource Manager-URL verwenden, finden Sie unter [Azure-REST-API](/rest/api/azure/). Beispielsweise ist der [create- oder update-Vorgang](/rest/api/mysql/singleserver/databases/create-or-update) für MySQL ein Vorgang der Steuerungsebene, weil die Anforderungs-URL wie folgt lautet:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure Resource Manager verarbeitet alle Anforderungen der Steuerungsebene. Die Azure-Features, die Sie zum Verwalten Ihrer Ressourcen implementiert haben, werden automatisch angewendet, beispielsweise:

* [Rollenbasierte Zugriffssteuerung von Azure (Azure-RBAC)](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [Verwaltungssperren](lock-resources.md)
* [Aktivitätsprotokolle](../../azure-monitor/essentials/activity-log.md)

Nachdem die Anforderung authentifiziert wurde, sendet Azure Resource Manager sie an den Ressourcenanbieter, der den Vorgang abschließt.

Die Steuerungsebene umfasst zwei Szenarien für die Verarbeitung von Anforderungen: „grünes Feld“ und „braunes Feld“. „Grünes Feld“ bezieht sich auf neue Ressourcen. „Braunes Feld“ bezieht sich auf vorhandene Ressourcen. Beim Bereitstellen von Ressourcen versteht Azure Resource Manager, wann neue Ressourcen erstellt werden und wann vorhandene Ressourcen aktualisiert werden müssen. Sie müssen sich keine Sorgen machen, dass identische Ressourcen erstellt werden.

## <a name="data-plane"></a>Datenebene

Anforderungen für Vorgänge auf Datenebene werden an einen Endpunkt gesendet, der für Ihre Instanz spezifisch ist. Beispielsweise ist der [Vorgang „Sprache erkennen“](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md) in Cognitive Services ein Vorgang auf Datenebene, da die Anforderungs-URL wie folgt lautet:

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

Vorgänge auf Datenebene sind nicht auf die REST-API beschränkt. Sie benötigen möglicherweise weitere Anmeldeinformationen, z. B. die Anmeldung bei einem virtuellen Computer oder einem Datenbankserver.

Funktionen, die die Verwaltung und Governance erzwingen, gelten möglicherweise nicht für Vorgänge auf Datenebene. Sie müssen die verschiedenen Möglichkeiten für die Interaktion von Benutzern mit Ihren Lösungen berücksichtigen. Beispielsweise verhindert eine Sperre, die Benutzer daran hindert, eine Datenbank zu löschen, nicht, dass Benutzer Daten über Abfragen löschen.

Sie können einige Richtlinien verwenden, um Vorgänge auf Datenebene zu steuern. Weitere Informationen finden Sie unter [Ressourcenanbietermodi (Vorschau) in Azure Policy](../../governance/policy/concepts/definition-structure.md#resource-provider-modes).

## <a name="next-steps"></a>Nächste Schritte

* Einen Überblick über Azure Resource Manager erhalten Sie unter [Was ist Azure Resource Manager?](overview.md)

* Weitere Informationen zu den Auswirkungen von Richtliniendefinitionen auf neue und vorhandene Ressourcen finden Sie unter [Auswerten der Auswirkung einer neuen Azure Policy-Definition](../../governance/policy/concepts/evaluate-impact.md).
