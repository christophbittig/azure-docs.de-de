---
title: Tag Azure Virtual Desktop-Ressourcen - Azure
description: Was Tagging ist und wie Sie es nutzen können, um Azure Servicekosten in Azure Virtual Desktop zu verwalten.
author: heidilohr
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1c77e7b2ec767cd843cbf34b7f3a44d074d20ab5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404333"
---
# <a name="tag-azure-virtual-desktop-resources-to-manage-costs"></a>Tag Azure Virtual Desktop-Ressourcen, um Kosten zu verwalten

Tagging ist ein Tool, das für alle Azure-Dienste verfügbar ist und Ihnen hilft, Ressourcen innerhalb ihres Azure-Abonnements zu organisieren. Durch die Organisation der Ressourcen wird es einfacher, die Kosten für mehrere Dienste zu verfolgen. Mithilfe von Tags können Sie auch nachvollziehen, wie viel jede Gruppierung von Azure-Ressourcen pro Abrechnungszyklus kostet. Wenn Sie mehr über Tagging im Allgemeinen erfahren möchten, lesen Sie [Verwenden Sie Tags, um Ihre Azure-Ressourcen und die Verwaltungshierarchie zu organisieren](../azure-resource-manager/management/tag-resources.md). Sie können sich auch ein [schnelles Video](https://www.youtube.com/watch?v=dUft4FZ40O8) über andere Möglichkeiten zur Verwendung von Azure-Tags ansehen.

## <a name="how-tagging-works"></a>Wie das Tagging funktioniert

Sie können Azure-Dienste, die Sie verwalten, im Azure-Portal oder über PowerShell markieren. Die Tags werden als Schlüssel-Wert-Paare von Text angezeigt. Wenn Sie getaggte Azure-Ressourcen verwenden, wird das zugehörige Tag-Schlüssel-Wert-Paar an die Ressourcennutzung angehängt.

Sobald Ihr Bereitstellung getaggte Nutzungsinformationen an [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) meldet, können Sie Ihre Tagging-Struktur verwenden, um Kostendaten zu filtern. Wie Sie in Azure Cost Management nach Tags filtern können, erfahren Sie im [Schnellstart: Erkunden und Analysieren von Kosten mit der Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md).

### <a name="add-edit-or-delete-tags"></a>Tags hinzufügen, bearbeiten oder löschen

Wenn Sie ein neues Tag auf eine Ressource anwenden, wird es in Azure Cost Management erst sichtbar, wenn die zugehörige Azure-Ressource Aktivität meldet. Wenn Sie ein bestehendes Tag auf Ihre Ressourcen anwenden, wird diese Änderung auch nicht in Azure Cost Management sichtbar sein, bis die Azure-Ressourcen Aktivität melden.

Wenn Sie einen Tag-Namen bearbeiten, werden die zugehörigen Ressourcen nun Kosten mit dem neuen Schlüssel-Wert-Paar assoziieren. Sie können die Daten weiterhin mit dem alten Tag filtern, aber alle neuen Daten, die nach der Änderung entstehen, werden mit dem neuen Tag gemeldet.

Wenn Sie ein Tag löschen, meldet Azure Virtual Desktop die mit dem gelöschten Tag verbundenen Daten nicht mehr an Azure Cost Management. Sie können weiterhin mit gelöschten Tags nach Daten filtern, die vor dem Löschen des Tags gemeldet wurden.

>[!IMPORTANT]
>Mit Tags versehene Azure-Ressourcen, die nicht mehr aktiv sind, seit Sie ihnen neue oder aktualisierte Tags zugewiesen haben, melden keine mit den geänderten Tags verbundenen Aktivitäten an Azure Cost Management. Sie können erst dann nach bestimmten Tags filtern, wenn die damit verbundene Aktivität vom Dienst an Azure Cost Management gemeldet wird.

### <a name="view-all-existing-tags"></a>Alle vorhandenen Tags anzeigen

Sie können alle vorhandenen Tags für Ihre Azure-Dienste anzeigen, indem Sie das Azure-Portal aufrufen und dann die [Registerkarte **Tags** öffnen](https://ms.portal.azure.com/#blade/HubsExtension/TagsBlade). Die Registerkarte Tags zeigt Ihnen alle Tags in Objekten, auf die Sie Zugriff haben. Sie können Tags auch nach ihren Schlüsseln oder Werten sortieren, wenn Sie schnell eine große Anzahl von Tags gleichzeitig aktualisieren müssen.

### <a name="what-tags-can-and-cant-do"></a>Was Tags tun und nicht tun können

Tags melden nur Nutzungs- und Kostendaten für Azure-Ressourcen, denen sie direkt zugeordnet sind. Wenn Sie eine Ressource markiert haben, ohne die anderen Ressourcen darin zu markieren, meldet Azure Virtual Desktop nur Aktivitäten, die sich auf die markierte Ressource der obersten Ebene beziehen. Sie müssen auch jede Ressource unter dieser Top-Level-Ressource markieren, wenn Sie möchten, dass Ihre Rechnungsdaten korrekt sind.

Um mehr über die Funktionsweise von Tags in Azure Cost Management zu erfahren, siehe [Wie Tags in Kosten- und Nutzungsdaten verwendet werden](../cost-management-billing/costs/understand-cost-mgt-data.md#how-tags-are-used-in-cost-and-usage-data).

Eine Liste der bekannten Azure-Tag-Einschränkungen finden Sie unter [Verwendung von Tags zur Organisation Ihrer Azure-Ressourcen und Verwaltungshierarchie](../azure-resource-manager/management/tag-resources.md#limitations).

## <a name="using-tags-in-azure-virtual-desktop"></a>Verwendung von Tags in Azure Virtual Desktop

Nachdem Sie nun die Grundlagen von Azure-Tags verstanden haben, lassen Sie uns nun besprechen, wie Sie diese in Azure Virtual Desktop verwenden können.

Sie können Azure-Tags verwenden, um die Kosten für die Erstellung, Verwaltung und Bereitstellung von virtualisierten Erlebnissen für Ihre Kunden und Benutzer zu organisieren. Das Tagging kann Ihnen auch dabei helfen, Ressourcen zu verfolgen, die Sie direkt über Azure Virtual Desktop und andere Azure-Dienste kaufen, die mit Azure Virtual Desktop-Bereitstellungen verbunden sind.

## <a name="suggested-tags-for-azure-virtual-desktop"></a>Empfohlene Tags für Azure Virtual Desktop

Da Azure Virtual Desktop mit anderen Azure-Diensten zusammenarbeiten kann, um seine Bereitstellungen zu unterstützen, gibt es kein universelles System zur Kennzeichnung von Bereitstellungsressourcen. Das Wichtigste ist, dass Sie eine Strategie entwickeln, die für Sie und Ihr Unternehmen funktioniert. Wir haben jedoch einige Vorschläge, die Ihnen helfen könnten, insbesondere wenn Sie Azure zum ersten Mal verwenden. 

Die folgenden Vorschläge gelten für alle Azure Virtual Desktop-Bereitstellungen:

- Machen Sie sich mit den von Ihnen erworbenen Azure-Diensten vertraut, damit Sie verstehen, was Sie kennzeichnen möchten. Während Sie lernen, wie Sie das Azure-Portal verwenden, sollten Sie eine Liste von Dienstgruppen und Objekten führen, auf die Sie Tags anwenden können. Zu den Ressourcen, die Sie im Auge behalten sollten, gehören Ressourcengruppen, virtuelle Maschinen, Datenträger und Netzwerkschnittstellenkarten (NICs). Eine umfassendere Liste der kostenverursachenden Servicekomponenten, die Sie kennzeichnen können, finden Sie unter [Gesamtkosten der Bereitstellung von Azure Virtual Desktop verstehen](./remote-app-streaming/total-costs.md).

- Erstellen Sie eine Aggregation der Kostenberichte, um Ihre Tags zu organisieren. Sie können entweder [einem allgemeinen Tagging-Muster](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging.md) folgen oder ein neues Muster erstellen, das den Bedürfnissen Ihrer Organisation entspricht.

- Achten Sie darauf, dass Ihre Markierungen überall einheitlich sind, wo Sie sie verwenden. Selbst der kleinste Tippfehler kann sich auf die Datenberichterstattung auswirken. Vergewissern Sie sich also, dass Sie genau das Schlüssel-Wert-Paar hinzufügen, das Sie später nachschlagen möchten.

- Führen Sie Buch über alle Tags, die Sie aktualisieren oder bearbeiten. Mit diesem Datensatz können Sie die historischen Daten der einzelnen Tags nach Bedarf kombinieren. Wenn Sie ein Tag bearbeiten oder aktualisieren, sollten Sie diese Änderungen auch auf alle Ressourcen anwenden, die das geänderte Tag enthalten.

## <a name="suggested-tags-for-azure-virtual-desktop-host-pools"></a>Empfohlene Tags für Azure Virtual Desktop-Hostpools

Jede virtuelle Maschine in einem Azure Virtual Desktop-Hostpool bildet ein kostenerzeugendes Konstrukt. Da Hostpools die Grundlage für eine Azure Virtual Desktop-Bereitstellung bilden, sind ihre VMs in der Regel die Hauptkostenquelle für Azure Virtual Desktop-Bereitstellungen. Wenn Sie ein Tagging-System einrichten möchten, empfehlen wir Ihnen, mit dem Tagging aller Host-Pools in Ihrer Bereitstellung zu beginnen, um die VM-Rechenkosten zu verfolgen. Die Kennzeichnung Ihrer Host-Pools kann Ihnen helfen, diese VM-Kosten mithilfe der Filterung in Azure Cost Management zu identifizieren.

Wie bei den [allgemeinen Vorschlägen](#suggested-tags-for-azure-virtual-desktop) gibt es kein universelles System zur Kennzeichnung von Hostpools. Wir haben jedoch ein paar Vorschläge, die Ihnen bei der Organisation Ihrer Hostpool-Tags helfen können:

- Die Kennzeichnung von Hostpools während der Erstellung ist optional, aber die Kennzeichnung während des Erstellungsprozesses erleichtert Ihnen später die Anzeige aller gekennzeichneten Nutzungen in Azure Cost Management. Ihre Hostpool-Tags folgen allen kostenverursachenden Komponenten der Sitzungshosts innerhalb Ihres Hostpools. Erfahren Sie mehr über sitzungshostspezifische Kosten unter [Gesamtkosten für die Bereitstellung von Azure Virtual Desktop verstehen](./remote-app-streaming/total-costs.md).

- Wenn Sie das Azure-Portal verwenden, um einen neuen Hostpool zu erstellen, bietet Ihnen der Erstellungsworkflow die Möglichkeit, vorhandene Tags hinzuzufügen. Diese Tags werden an alle Ressourcen weitergegeben, die Sie während des Erstellungsprozesses des Hostpools erstellen. Tags werden auch auf alle Sitzungshosts angewendet, die Sie einem vorhandenen Hostpool im Azure-Portal hinzufügen. Allerdings müssen Sie die Tags jedes Mal manuell eingeben, wenn Sie einen neuen Sitzungsleiter hinzufügen.

- Es ist unwahrscheinlich, dass Sie jemals einen vollständigen Kostenbericht für jeden unterstützenden Azure-Dienst erhalten werden, der mit Ihren Host-Pools arbeitet, da die Konfigurationsoptionen sowohl unbegrenzt als auch für jeden Kunden einzigartig sind. Es liegt an Ihnen zu entscheiden, wie genau Sie die Kosten für alle Azure-Dienste, die mit Ihrer Azure Virtual Desktop-Bereitstellung verbunden sind, verfolgen möchten. Je gründlicher Sie diese Kosten durch Kennzeichnung verfolgen, desto genauer wird Ihr monatlicher Kostenbericht für Azure Virtual Desktop.

- Wenn Sie Ihr Tagging-System um Ihre Host-Pools herum aufbauen, achten Sie darauf, dass Sie Schlüssel-Wert-Paare verwenden, die sich später sinnvoll zu anderen Azure-Diensten hinzufügen lassen.

## <a name="suggested-tags-for-other-azure-virtual-desktop-resources"></a>Empfohlene Tags für andere Ressourcen von Azure Virtual Desktop

Die meisten Kunden von Azure Virtual Desktop setzen andere Azure-Dienste ein, um ihre Bereitstellungen zu unterstützen. Wenn Sie die Kosten für diese zusätzlichen Leistungen in Ihren Kostenbericht aufnehmen wollen, sollten Sie die folgenden Vorschläge berücksichtigen:

- Wenn Sie bereits einen Azure-Dienst oder Ressourcen erworben haben, die Sie in Ihre Azure Virtual Desktop-Bereitstellungen integrieren möchten, haben Sie zwei Möglichkeiten:
   
   - Trennen Sie Ihre erworbenen Azure-Dienste zwischen verschiedenen Azure-Abonnements.
   - Kombinieren Sie alle erworbenen Azure-Dienste im selben Abonnement mit Ihren Azure Virtual Desktop-Tags.

   Durch die Trennung Ihrer Dienstleistungen erhalten Sie eine klarere Vorstellung von den Kosten für jede einzelne Dienstleistung, die jedoch am Ende teurer sein kann. Möglicherweise müssen Sie für diese Dienste zusätzlichen Speicherplatz erwerben, um sicherzustellen, dass Ihr Azure Virtual Desktop über einen eigenen Speicherplatz verfügt. 

   Die Kombination Ihrer eingekauften Dienste ist kostengünstiger, kann aber Ihre Kostenaufstellung aufblähen, da die Nutzungsdaten für gemeinsam genutzte Ressourcen nicht so genau sind. Um den Mangel an Genauigkeit auszugleichen, können Sie Ihren Ressourcen mehrere Tags hinzufügen, um die gemeinsamen Kosten durch Filter zu sehen, die verschiedene Faktoren berücksichtigen.

- Wenn Sie mit der Erstellung Ihres Tagging-Systems mit einem anderen Azure-Dienst begonnen haben, stellen Sie sicher, dass die von Ihnen erstellten Schlüssel-Wert-Paare später auf Ihre Azure Virtual Desktop-Bereitstellung oder andere Dienste angewendet werden können.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mehr über die allgemeinen Kosten von Azure Virtual Desktop erfahren möchten, lesen Sie bitte [Die Gesamtkosten für die Bereitstellung von Azure Virtual Desktop](./remote-app-streaming/total-costs.md).

Wenn Sie mehr über Azure-Tags erfahren möchten, lesen Sie die folgenden Ressourcen:

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen und Verwaltungshierarchie](../azure-resource-manager/management/tag-resources.md)

- [Ein Video, das den Wert der Verwendung von Azure-Tags erklärt](https://www.youtube.com/watch?v=dUft4FZ40O8)

- [Verwendung von Tags in Kosten- und Nutzungsdaten](../cost-management-billing/costs/understand-cost-mgt-data.md#how-tags-are-used-in-cost-and-usage-data)

- [Entwickeln Ihrer Benennungs- und Kennzeichnungsstrategie für Azure-Ressourcen](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

- [Definieren Ihrer Tagstrategie](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging)

- [Leitfaden zur Entscheidungsfindung für Ressourcenbenennung und -markierung](/azure/cloud-adoption-framework/decision-guides/resource-tagging)

Wenn Sie mehr über Azure Cost Management erfahren möchten, lesen Sie die folgenden Artikel:

- [Was ist „Azure Cost Management + Abrechnung“?](../cost-management-billing/cost-management-billing-overview.md)

- [Schnellstart: Ermitteln und Analysieren von Kosten mit der Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md)
