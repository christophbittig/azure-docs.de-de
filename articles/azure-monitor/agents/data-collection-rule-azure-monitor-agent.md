---
title: Konfigurieren der Datensammlung für den Azure Monitor-Agent (Vorschau)
description: In diesem Artikel wird beschrieben, wie Sie eine Datensammlungsregel erstellen, um mit dem Azure Monitor-Agent Daten von VMs zu sammeln.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/16/2021
ms.openlocfilehash: 81c82152bf87944c7aed191c12e067567ec2b086
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662245"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent"></a>Konfigurieren der Datensammlung für den Azure Monitor-Agent

Mit Datensammlungsregeln werden die in Azure Monitor eingehenden Daten definiert, und es wird angegeben, wohin sie gesendet werden sollen. In diesem Artikel wird beschrieben, wie Sie eine Datensammlungsregel erstellen, um mit dem Azure Monitor-Agent Daten von virtuellen Computern zu sammeln.

Eine umfassende Beschreibung der Datensammlungsregeln finden Sie unter [Datensammlungsregeln in Azure Monitor](data-collection-rule-overview.md).

> [!NOTE]
> In diesem Artikel wird beschrieben, wie Sie Daten für virtuelle Computer nur mit dem Azure Monitor-Agent konfigurieren.

## <a name="data-collection-rule-associations"></a>Zuordnungen zu Datensammlungsregeln

Zum Anwenden einer Datensammlungsregel auf einen virtuellen Computer erstellen Sie eine Zuordnung für den Computer. Ein virtueller Computer kann über eine Zuordnung zu mehreren Datensammlungsregeln verfügen, und einer Datensammlungsregel können mehrere virtuelle Computer zugeordnet sein. Auf diese Weise können Sie eine Gruppe mit Datensammlungsregeln definieren, die jeweils für eine bestimmte Anforderung gelten, und sie dann nur auf die virtuellen Computer anwenden, für die sie gelten. 

Angenommen, in einer Umgebung mit einer Gruppe von virtuellen Computern wird auf einigen eine Branchenanwendung und auf den anderen SQL Server ausgeführt. Unter Umständen verfügen Sie über eine Standard-Datensammlungsregel, die für alle virtuellen Computer gilt, und über separate Datensammlungsregeln, mit denen Daten speziell für die Branchenanwendung und für SQL Server gesammelt werden. Die Zuordnungen der virtuellen Computer zu den Datensammlungsregeln sieht dann in etwa wie im folgenden Diagramm aus.

![Diagramm mit virtuellen Computern, auf denen eine Branchenanwendung und SQL Server gehostet werden. Dazu gehören die Datensammlungsregeln „central-i t-default“ und „lob-app“ für die Branchenanwendung sowie „central-i t-default“ und „s q l“ für SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Erstellen einer Regel und einer Zuordnung im Azure-Portal

Sie können das Azure-Portal verwenden, um eine Datensammlungsregel zu erstellen und dieser Regel unter Ihrem Abonnement virtuelle Computer zuzuordnen. Der Azure Monitor-Agent wird automatisch installiert, und für alle virtuellen Computer wird eine verwaltete Identität erstellt (falls noch nicht geschehen).

> [!IMPORTANT]
> Beim Erstellen einer Datensammlungsregel über das Portal wird auf den Zielressourcen zusätzlich zu möglicherweise bereits vorhandenen benutzerseitig zugewiesenen Identitäten eine systemseitig zugewiesene verwaltete Identität aktiviert. Wenn die benutzerseitig zugewiesene Identität nicht extra angegeben wird, wird auf dem Computer für vorhandene Anwendungen standardmäßig die systemseitig zugewiesene Identität verwendet. [Weitere Informationen](../../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)

                    

> [!NOTE]
> Wenn Sie Daten an Log Analytics senden möchten, müssen Sie die Datensammlungsregel in **derselben Region** erstellen, in der sich auch Ihr Log Analytics-Arbeitsbereich befindet. Die Regel kann Computern in anderen unterstützten Regionen zugeordnet werden.

Wählen Sie im Portal im Menü **Azure Monitor** unter **Einstellungen** die Option **Datensammlungsregeln** aus. Klicken Sie auf **Erstellen**, um eine neue Datensammlungsregel und Zuweisung zu erstellen.

[![Datensammlungsregeln](media/data-collection-rule-azure-monitor-agent/data-collection-rules-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules-updated.png#lightbox)

Klicken Sie auf **Hinzufügen**, um eine neue Regel und eine Gruppe von Zuordnungen zu erstellen. Geben Sie einen **Regelnamen** und dann ein **Abonnement**, eine **Ressourcengruppe** und eine **Region** an. Hiermit wird angegeben, wo die Datensammlungsregel erstellt wird. Die virtuellen Computer und ihre Zuordnungen können unter einem beliebigen Abonnement bzw. einer Ressourcengruppe auf dem Mandanten angeordnet sein.
Wählen Sie außerdem den geeigneten **Plattformtyp** aus, der den Ressourcentyp angibt, auf den diese Regel angewendet werden kann. Bei benutzerdefinierten Regeln sind sowohl Windows- als auch Linux-Typen zulässig. Dies ermöglicht vorab zusammengestellte Erstellungsoberflächen mit Optionen, die auf den ausgewählten Plattformtyp zugeschnitten sind.

[![Grundlagen von Datensammlungsregeln](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics-updated.png#lightbox)

Fügen Sie auf der Registerkarte **Ressourcen** die Ressourcen (virtuelle Computer, VM-Skalierunggruppen, Arc für Server) hinzu, auf die die Datensammlungsregel angewendet werden soll. Der Azure Monitor-Agent wird auf Ressourcen installiert, auf denen er noch nicht installiert ist. Außerdem wird die verwaltete Azure-Identität aktiviert.

[![Datensammlungsregeln für virtuelle Computer](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines-updated.png#lightbox)

Klicken Sie auf der Registerkarte **Sammeln und übermitteln** auf **Datenquelle hinzufügen**, um eine Datenquelle und einen Zielsatz hinzuzufügen. Wählen Sie einen **Datenquellentyp** aus. Die entsprechenden Details für die Auswahl werden angezeigt. Für Leistungsindikatoren können Sie eine Auswahl aus einer Gruppe mit Objekten mit der zugehörigen Stichprobenhäufigkeit treffen. Für Ereignisse können Sie bestimmte Protokolle bzw. entsprechende Funktionen und den Schweregrad auswählen. 

[![Einfache Datenquelle](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic-updated.png#lightbox)


Um andere Protokolle und Leistungsindikatoren aus den [derzeit unterstützten Datenquellen](azure-monitor-agent-overview.md#data-sources-and-destinations) anzugeben oder um Ereignisse mithilfe von XPath-Abfragen zu filtern, wählen Sie **Benutzerdefiniert** aus. Anschließend können Sie einen [XPath](https://www.w3schools.com/xml/xpath_syntax.asp) für bestimmte Werte angeben, die gesammelt werden sollen. Beispiele hierzu finden Sie unter [Beispiel für eine Datensammlungsregel](data-collection-rule-overview.md#sample-data-collection-rule).

[![Datenquelle: Benutzerdefiniert](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom-updated.png#lightbox)

Fügen Sie auf der Registerkarte **Ziel** mindestens ein Ziel für die Datenquelle hinzu. Für Windows-Ereignis- und Syslog-Datenquellen ist nur das Senden an Azure Monitor-Protokolle möglich. Bei Leistungsindikatoren ist sowohl das Senden an Azure Monitor-Metriken als auch an Azure Monitor-Protokolle möglich.

[![Destination](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Klicken Sie auf **Datenquelle hinzufügen** und dann auf **Bewerten + erstellen**, um die Details der Datensammlungsregel und die Zuordnung zur Gruppe mit den VMs zu überprüfen. Klicken Sie auf **Erstellen**, um die Erstellung durchzuführen.

> [!NOTE]
> Nachdem die Datensammlungsregel und die Zuordnungen erstellt wurden, kann es bis zu fünf Minuten dauern, bis Daten an die Ziele gesendet werden.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Begrenzen der Datensammlung mit benutzerdefinierten XPath-Abfragen
Da Ihnen alle in einem Log Analytics-Arbeitsbereich gesammelten Daten in Rechnung gestellt werden, sollten Sie nur die Daten sammeln, die Sie benötigen. Mit der Standardkonfiguration im Azure-Portal haben Sie nur eingeschränkte Möglichkeiten zur Filterung der Ereignisse, die gesammelt werden sollen. Bei Anwendungs- und Systemprotokollen sind dies alle Protokolle mit einem bestimmten Schweregrad. Bei Sicherheitsprotokollen sind es alle Protokolle von Überwachungserfolgen oder Überwachungsfehlern.

Zur Angabe von zusätzlichen Filtern müssen Sie mithilfe der benutzerdefinierten Konfiguration eine XPath-Abfrage angeben, mit der die Ereignisse herausgefiltert werden. XPath-Einträge haben folgende Form: `LogName!XPathQuery`. Angenommen, Sie möchten aus dem Anwendungsereignisprotokoll nur Ereignisse mit der Ereignis-ID 1035 abrufen. In diesem Fall würde die XPathQuery-Abfrage für diese Ereignisse `*[System[EventID=1035]]` lauten. Da Sie die Ereignisse aus dem Anwendungsereignisprotokoll abrufen möchten, würde die XPath-Abfrage `Application!*[System[EventID=1035]]` lauten.

Eine Liste der Einschränkungen in XPath, die vom Windows-Ereignisprotokoll unterstützt werden, finden Sie unter [Einschränkungen für XPath 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations).

> [!TIP]
> Verwenden Sie zum Testen der Gültigkeit einer XPathQuery-Abfrage das PowerShell-Cmdlet `Get-WinEvent` mit dem Parameter `FilterXPath`. Das folgende Skript ist ein Beispiel hierfür:
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - **Im obigen Cmdlet ist der Wert für den Parameter „-LogName“ der erste Teil der XPath-Abfrage bis zum „!“, während nur der Rest der XPath-Abfrage in den $XPath-Parameter übernommen wird.**
> - Werden Ereignisse zurückgegeben, ist die Abfrage gültig.
> - Wenn Sie die Meldung *Es wurden keine Ereignisse gefunden, die mit den angegebenen Auswahlkriterien übereinstimmen* erhalten, ist die Abfrage möglicherweise gültig, aber es sind keine übereinstimmenden Ereignisse auf dem lokalen Computer vorhanden.
> - Wenn Sie die Meldung *Die angegebene Abfrage ist ungültig* erhalten, ist die Abfragesyntax ungültig. 

Die folgende Tabelle enthält Beispiele für das Filtern von Ereignissen mithilfe einer benutzerdefinierten XPath-Abfrage:

| Beschreibung |  XPath |
|:---|:---|
| Es werden nur Systemereignisse mit der Ereignis-ID 4648 gesammelt. |  `System!*[System[EventID=4648]]`
| Es werden nur Systemereignisse mit der Ereignis-ID 4648 und dem Prozessnamen „consent.exe“ gesammelt. | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| Es werden alle kritischen, Fehler-, Warnungs- und Informationsereignisse aus dem Systemereignisprotokoll mit Ausnahme der Ereignis-ID 6 (Treiber geladen) gesammelt. |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Es werden alle erfolgreichen und fehlerhaften Sicherheitsereignisse für die Ereignis-ID 4624 (Erfolgreiche Anmeldung) gesammelt. |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>Erstellen einer Regel und einer Zuordnung mithilfe der REST-API

Führen Sie die folgenden Schritte aus, um mithilfe der REST-API eine Datensammlungsregel und Zuordnungen zu erstellen.

> [!NOTE]
> Wenn Sie Daten an Log Analytics senden möchten, müssen Sie die Datensammlungsregel in **derselben Region** erstellen, in der sich auch Ihr Log Analytics-Arbeitsbereich befindet. Die Regel kann Computern in anderen unterstützten Regionen zugeordnet werden.

1. Erstellen Sie die DCR-Datei manuell im JSON-Format, das im [Beispiel für eine Datensammlungsregel](data-collection-rule-overview.md#sample-data-collection-rule) gezeigt wird.

2. Erstellen Sie die Regel mithilfe der [REST-API](/rest/api/monitor/datacollectionrules/create#examples).

3. Erstellen Sie mithilfe der [REST-API](/rest/api/monitor/datacollectionruleassociations/create#examples) für jede VM eine Zuordnung zur Datensammlungsregel.


## <a name="create-rule-and-association-using-resource-manager-template"></a>Erstellen einer Regel und einer Zuordnung mithilfe einer Resource Manager-Vorlage

> [!NOTE]
> Wenn Sie Daten an Log Analytics senden möchten, müssen Sie die Datensammlungsregel in **derselben Region** erstellen, in der sich auch Ihr Log Analytics-Arbeitsbereich befindet. Die Regel kann Computern in anderen unterstützten Regionen zugeordnet werden.

Mithilfe einer Resource Manager-Vorlage können Sie eine Regel und eine Zuordnung für einen virtuellen Azure-Computer oder einen Server mit Azure Arc-Unterstützung erstellen. Beispielvorlagen finden Sie unter [Beispiele für Resource Manager-Vorlagen für Datensammlungsregeln in Azure Monitor](./resource-manager-data-collection-rules.md).


## <a name="manage-rules-and-association-using-powershell"></a>Verwalten von Regeln und Zuordnungen mithilfe von PowerShell

> [!NOTE]
> Wenn Sie Daten an Log Analytics senden möchten, müssen Sie die Datensammlungsregel in **derselben Region** erstellen, in der sich auch Ihr Log Analytics-Arbeitsbereich befindet. Die Regel kann Computern in anderen unterstützten Regionen zugeordnet werden.

**Datensammlungsregeln in Azure Monitor (Vorschau)**

| Aktion | Befehl |
|:---|:---|
| Abrufen von Regeln | [Get-AzDataCollectionRule](/powershell/module/az.monitor/get-azdatacollectionrule?view=azps-5.4.0&preserve-view=true) |
| Erstellen einer Regel | [New-AzDataCollectionRule](/powershell/module/az.monitor/new-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| Aktualisieren einer Regel | [Set-AzDataCollectionRule](/powershell/module/az.monitor/set-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| Löschen einer Regel | [Remove-AzDataCollectionRule](/powershell/module/az.monitor/remove-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| Aktualisieren von „Tags“ für eine Regel | [Update-AzDataCollectionRule](/powershell/module/az.monitor/update-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |

**Zuordnungen zu Datensammlungsregeln**

| Aktion | Befehl |
|:---|:---|
| Abrufen von Zuordnungen | [Get-AzDataCollectionRuleAssociation](/powershell/module/az.monitor/get-azdatacollectionruleassociation?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| Erstellen einer Zuordnung | [New-AzDataCollectionRuleAssociation](/powershell/module/az.monitor/new-azdatacollectionruleassociation?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| Löschen einer Zuordnung | [Remove-AzDataCollectionRuleAssociation](/powershell/module/az.monitor/remove-azdatacollectionruleassociation?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |



## <a name="manage-rules-and-association-using-azure-cli"></a>Verwalten von Regeln und Zuordnungen mithilfe der Azure CLI

> [!NOTE]
> Wenn Sie Daten an Log Analytics senden möchten, müssen Sie die Datensammlungsregel in **derselben Region** erstellen, in der sich auch Ihr Log Analytics-Arbeitsbereich befindet. Die Regel kann Computern in anderen unterstützten Regionen zugeordnet werden.

Dies ist als Teil der Erweiterung **monitor-control-service** der Azure CLI aktiviert. Eine Auflistung aller Befehle finden Sie [hier](/cli/azure/monitor/data-collection/rule?view=azure-cli-latest&preserve-view=true).


## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über den [Azure Monitor-Agent](azure-monitor-agent-overview.md).
- Informieren Sie sich über die [Datensammlungsregeln](data-collection-rule-overview.md).
