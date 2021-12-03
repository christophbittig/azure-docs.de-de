---
title: Verwalten des Zugriffs auf Microsoft Sentinel-Daten nach Ressourcen| Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie den Zugriff auf Microsoft Sentinel-Daten anhand der Ressourcen, auf die ein Benutzer zugreifen kann, verwalten können. Durch die Verwaltung des Zugriffs nach Ressource können Sie den Zugriff nur auf bestimmte Daten ohne die gesamte Microsoft Sentinel-Erfahrung ermöglichen. Diese Methode wird auch als rollenbasierte Zugriffssteuerung (RBAC) im Ressourcenkontext bezeichnet.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 38fd7f30dc412872ccdb6817ae1f502b78a77641
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522496"
---
# <a name="manage-access-to-microsoft-sentinel-data-by-resource"></a>Verwalten des Zugriffs auf Microsoft Sentinel-Daten nach Ressource

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In der Regel haben Benutzer, die Zugriff auf einen Microsoft Sentinel-Arbeitsbereich haben, auch Zugriff auf alle Arbeitsbereichsdaten, einschließlich Sicherheitsinhalten. Administratoren können [Azure-Rollen](roles.md) verwenden, um den Zugriff auf bestimmte Features in Microsoft Sentinel zu konfigurieren. Dies hängt jeweils von den Zugriffsanforderungen des Teams ab.

Möglicherweise verfügen Sie jedoch über einige Benutzer, die nur auf bestimmte Daten in Ihrem Microsoft Sentinel-Arbeitsbereich zugreifen müssen, aber keinen Zugriff auf die gesamte Microsoft Sentinel-Umgebung haben sollten. Beispiel: Sie möchten einem nicht für Sicherheitsvorgänge zuständigen Team Zugriff auf die Windows-Ereignisdaten für die eigenen Server gewähren.

Für Fälle dieser Art empfehlen wir Ihnen, Ihre rollenbasierte Zugriffssteuerung (RBAC) basierend auf den Ressourcen zu konfigurieren, die für Ihre Benutzer zulässig sind – anstelle des Zugriffs auf den Microsoft-Sentinel-Arbeitsbereich oder bestimmte Microsoft-Sentinel-Features. Diese Methode wird auch als die Einrichtung der **rollenbasierten Zugriffssteuerung (RBAC) im Ressourcenkontext** bezeichnet.

Wenn Benutzer zugriff auf Microsoft-Sentinel-Daten über die Ressourcen haben, auf die sie anstelle des Microsoft Sentinel-Arbeitsbereichs zugreifen können, können sie Protokolle und Arbeitsmappen mithilfe der folgenden Methoden anzeigen:

- **Über die Ressource selbst**, z. B. einen virtuellen Azure-Computer. Verwenden Sie diese Methode, um die Protokolle und Arbeitsmappen nur für eine bestimmte Ressource anzuzeigen.

- **Über Azure Monitor**. Verwenden Sie diese Methode, wenn Sie Abfragen erstellen möchten, mit denen mehrere Ressourcen bzw. Ressourcengruppen abgedeckt werden. Legen Sie Ihren Bereich beim Navigieren zu Protokollen und Arbeitsmappen in Azure Monitor auf eine oder mehrere bestimmte Ressourcengruppen oder Ressourcen fest.

Aktivieren Sie in Azure Monitor die rollenbasierte Zugriffssteuerung (RBAC) für den Ressourcenkontext. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf Protokolldaten und Arbeitsbereiche in Azure Monitor](../azure-monitor/logs/manage-access.md).

> [!NOTE]
> Falls es sich bei Ihren Daten nicht um eine Azure-Ressource, z. B. Syslog-, CEF- oder AAD-Daten, oder von einem benutzerdefinierten Sammler erfasste Daten handelt, müssen Sie manuell die Ressourcen-ID konfigurieren, die zum Ermitteln der Daten und Aktivieren des Zugriffs verwendet wird. Weitere Informationen finden Sie unter [Explizites Konfigurieren der rollenbasierten Zugriffssteuerung im Ressourcenkontext](#explicitly-configure-resource-context-rbac).
>
> Darüber hinaus werden [Funktionen](../azure-monitor/logs/functions.md) und gespeicherte Suchvorgänge in ressourcenorientierten Kontexten nicht unterstützt. Daher werden Microsoft Sentinel-Features wie Analyse und [Normalisierung](normalization.md) für Ressourcen- und Kontextbasierte RBAC in Microsoft Sentinel nicht unterstützt.
> 

## <a name="scenarios-for-resource-context-rbac"></a>Szenarien für die rollenbasierte Zugriffssteuerung (RBAC) im Ressourcenkontext

In der folgenden Tabelle sind die Szenarien angegeben, in denen RBAC im Ressourcenkontext am hilfreichsten ist. Beachten Sie die Unterschiede bei den Zugriffsanforderungen zwischen SOC-Teams und anderen Teams.

| Anforderungstyp |SOC-Team  |Anderes Team  |
|---------|---------|---------|
|**Berechtigungen**     | Gesamter Arbeitsbereich        |   Nur bestimmte Ressourcen      |
|**Datenzugriff**     |  Alle Daten des Arbeitsbereichs       | Nur Daten für Ressourcen, für die das Team eine Zugriffsberechtigung besitzt        |
|**Erfahrung**     |  Die vollständige Microsoft Sentinel-Benutzeroberfläche, die möglicherweise durch die dem Benutzer zugewiesenen [funktionalen](roles.md) Berechtigungen eingeschränkt ist       |  Nur Protokollabfragen und Arbeitsmappen       |
|     |         |         |

Falls für Ihr Team ähnliche Zugriffsanforderungen wie für andere Teams (keine SOC-Teams) gemäß der obigen Tabelle bestehen, ist RBAC im Ressourcenkontext unter Umständen gut für Ihre Organisation geeignet.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>Alternative Methoden zum Implementieren von RBAC im Ressourcenkontext

Je nach den Berechtigungen, die für Ihre Organisation benötigt werden, ist die Nutzung von RBAC im Ressourcenkontext ggf. keine vollwertige Lösung.

Die folgende Liste enthält Beschreibungen von Szenarien, in denen andere Datenzugriffslösungen unter Umständen besser für Ihre Anforderungen geeignet sind:

|Szenario  |Lösung  |
|---------|---------|
|**Eine Niederlassung verfügt über ein SOC-Team, das eine vollständige Microsoft Sentinel-Erfahrung erfordert.**     |  Verwenden Sie in diesem Fall eine Architektur mit mehreren Arbeitsbereichen, damit Sie Ihre Datenberechtigungen trennen können. <br><br>Weitere Informationen finden Sie unter <br>- [Erweitern von Microsoft Sentinel über Arbeitsbereiche und Mandanten hinweg](extend-sentinel-across-workspaces-tenants.md)<br>    - [Arbeiten mit Vorfällen in vielen Arbeitsbereichen gleichzeitig](multiple-workspace-view.md)          |
|**Sie möchten Zugriff auf eine bestimmte Art von Ereignis gewähren**.     |  Gewähren Sie für einen Windows-Administrator den Zugriff auf Windows-Sicherheitsereignisse auf allen Systemen. <br><br>Verwenden Sie in solchen Fällen [RBAC auf Tabellenebene](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043), um die Berechtigungen für die einzelnen Tabellen festzulegen.       |
| **Der Zugriff auf eine Ebene mit höherer Granularität soll eingeschränkt werden (entweder nicht basierend auf der Ressource oder nur auf einen Teil der Felder eines Ereignisses).**   |   Es kann beispielsweise sein, dass Sie den Zugriff auf Office 365-Protokolle auf ein Tochterunternehmen eines Benutzers beschränken möchten. <br><br>Gewähren Sie in diesem Fall Zugriff auf die Daten, indem Sie die integrierte Integration mit [Power BI-Dashboards und -Berichten](../azure-monitor/logs/log-powerbi.md) verwenden.      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>Explizites Konfigurieren der rollenbasierten Zugriffssteuerung im Ressourcenkontext

Führen Sie die folgenden Schritte aus, wenn Sie die rollenbasierte Zugriffssteuerung (RBAC) im Ressourcenkontext konfigurieren möchten, es sich bei Ihren Daten aber nicht um eine Azure-Ressource handelt.

Zu den Daten in Ihrem Microsoft Sentinel-Arbeitsbereich, bei denen es sich nicht um Azure-Ressourcen handelt, zählen beispielsweise Syslog-, CEF- oder AAD-Daten oder von einem benutzerdefinierten Collector gesammelte Daten.

**Gehen Sie wie folgt vor, um die rollenbasierte Zugriffssteuerung im Ressourcenkontext explizit zu konfigurieren**:

1. Vergewissern Sie sich, dass Sie die [rollenbasierte Zugriffssteuerung (RBAC) im Ressourcenkontext](../azure-monitor/logs/manage-access.md) in Azure Monitor aktiviert haben. 

1. [Erstellen Sie eine Ressourcengruppe](../azure-resource-manager/management/manage-resource-groups-portal.md) für jedes Benutzerteam, das ohne die gesamte Microsoft Sentinel-Umgebung auf Ihre Ressourcen zugreifen muss.

    Weisen Sie jedem Teammitglied [Berechtigungen für das Lesen von Protokollen](../azure-monitor/logs/manage-access.md#resource-permissions) zu.

1. Weisen Sie den von Ihnen erstellten Ressourcenteamgruppen Ressourcen zu, und versehen Sie die Ereignisse mit den relevanten Ressourcen-IDs.

    Wenn Azure-Ressourcen Daten an Microsoft Sentinel senden, werden die Protokolleinträge automatisch mit der Ressourcen-ID der Datenquelle versehen.

    > [!TIP]
    > Wir empfehlen Ihnen, die Ressourcen, für die Sie Zugriff gewähren, in einer speziell für diesen Zweck erstellten Ressourcengruppe zu gruppieren.
    >
    > Falls dies nicht möglich ist, sollten Sie sicherstellen, dass Ihr Team über Protokollleseberechtigungen direkt für die Ressourcen verfügt, auf die Zugriff bestehen soll.
    >

    Weitere Informationen zu Ressourcen-IDs finden Sie unter:

    - [Ressourcen-IDs mit Protokollweiterleitung](#resource-ids-with-log-forwarding)
    - [Ressourcen-IDs mit Logstash-Sammlung](#resource-ids-with-logstash-collection)
    - [Ressourcen-IDs mit der Log Analytics-API-Sammlung](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>Ressourcen-IDs mit Protokollweiterleitung

Wenn Ereignisse per [Common Event Format (CEF)](connect-common-event-format.md) oder [Syslog](connect-syslog.md) erfasst werden, wird die Protokollweiterleitung genutzt, um Ereignisse für mehrere Quellsysteme zu erfassen.

Wenn beispielsweise eine CEF- oder Syslog-Weiterleitungs-VM die Quellen abhört, die Syslog-Ereignisse senden, und diese an Microsoft Sentinel weiterleitet, wird die Ressourcen-ID der Log-Weiterleitungs-VM allen Ereignissen zugewiesen, die sie weiterleitet.

Falls mehrere Teams vorhanden sind, sollten Sie sicherstellen, dass Sie über separate VMs für die Protokollweiterleitung verfügen, von denen die Ereignisse für die einzelnen Teams verarbeitet werden.

Durch die Trennung Ihrer VMs wird beispielsweise dafür gesorgt, dass Syslog-Ereignisse, die zu Team „A“ gehören, auch mit der Sammler-VM „A“ erfasst werden.

> [!TIP]
> - Stellen Sie bei Verwendung einer lokalen VM oder anderen Cloud-VM, z. B. AWS, als Mittel für die Protokollweiterleitung sicher, dass diese über eine Ressourcen-ID verfügt, indem Sie [Azure Arc](../azure-arc/servers/overview.md) implementieren.
> - Erwägen Sie, zur Skalierung Ihrer VM-Umgebung für die Protokollweiterleitung eine [VM-Skalierungsgruppe](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) zu erstellen, über die Ihre CEF- und Sylog-Protokolle erfasst werden.


### <a name="resource-ids-with-logstash-collection"></a>Ressourcen-IDs mit Logstash-Sammlung

Wenn Sie Ihre Daten mit dem Microsoft Sentinel [Logstash-Ausgabe-Plugin](connect-logstash.md) sammeln, verwenden Sie das Feld **azure_resource_id**, um Ihren benutzerdefinierten Kollektor so zu konfigurieren, dass die Ressourcen-ID in Ihre Ausgabe aufgenommen wird.

Wenn Sie die rollenbasierte Zugriffssteuerung (RBAC) im Ressourcenkontext nutzen und die per API erfassten Ereignisse für bestimmte Benutzer verfügbar sein sollen, verwenden Sie die Ressourcen-ID der Ressourcengruppe, die Sie [für Ihre Benutzer erstellt](#explicitly-configure-resource-context-rbac) haben.

Der folgende Code enthält ein Beispiel für eine Logstash-Konfigurationsdatei:

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> Hierbei kann es ratsam sein, mehrere `output`-Abschnitte hinzuzufügen, um die auf die einzelnen Ereignisse angewendeten Tags unterscheiden zu können.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>Ressourcen-IDs mit der Log Analytics-API-Sammlung

Bei Verwendung der [Log Analytics-Datensammler-API](../azure-monitor/logs/data-collector-api.md) können Sie für die Zuweisung zu Ereignissen eine Ressourcen-ID nutzen, indem Sie den HTTP-Anforderungsheader [*x-ms-AzureResourceId*](../azure-monitor/logs/data-collector-api.md#request-headers) verwenden.

Wenn Sie die rollenbasierte Zugriffssteuerung (RBAC) im Ressourcenkontext nutzen und die per API erfassten Ereignisse für bestimmte Benutzer verfügbar sein sollen, verwenden Sie die Ressourcen-ID der Ressourcengruppe, die Sie [für Ihre Benutzer erstellt](#explicitly-configure-resource-context-rbac) haben.



## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Berechtigungen in Microsoft Sentinel](roles.md).
