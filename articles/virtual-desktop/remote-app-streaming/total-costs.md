---
title: Grundlegendes zu den Gesamtkosten für die Bereitstellung von Azure Virtual Desktop – Azure
description: Erhalten Sie Informationen zum Schätzen der Gesamtkosten Ihrer Azure Virtual Desktop-Bereitstellung.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 11/12/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3daabe070d0745c95bc16582262897da9f4c5aae
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399502"
---
# <a name="understanding-total-azure-virtual-desktop-deployment-costs"></a>Grundlegendes zu den Gesamtkosten für die Bereitstellung von Azure Virtual Desktop

Azure Virtual Desktop-Kosten stammen aus zwei Quellen: dem zugrunde liegenden Azure-Ressourcenverbrauch und der Lizenzierung. Azure Virtual Desktop-Kosten werden der Organisation in Rechnung gestellt, die die Azure Virtual Desktop-Bereitstellung besitzt, nicht den Endbenutzern, die auf die Bereitstellungsressourcen zugreifen. Einige Lizenzierungsgebühren müssen im Voraus bezahlt werden. Andere Lizenzen und die zugrunde liegenden Gebühren für den Ressourcenverbrauch basieren auf Verbrauchseinheiten, die Ihre Nutzung nachverfolgen.

In diesem Artikel erläutern wir die Verbrauchs- und Lizenzierungskosten und wie Sie die Kosten für den Dienst vor der Bereitstellung von Azure Virtual Desktop mithilfe des Azure-Preisrechners abschätzen können. Dieser Artikel enthält auch Anweisungen zum Verwenden von Azure Cost Management zum Anzeigen der Kosten nach der Bereitstellung von Azure Virtual Desktop.

>[!NOTE]
>Der Kunde, der für die Azure Virtual Desktop-Bereitstellung bezahlt, ist für die Verwaltung der Ressourcen und die Kosten während der gesamten Lebensdauer ihrer Bereitstellung verantwortlich. Wenn der Besitzer die mit seiner Azure Virtual Desktop-Bereitstellung verbundenen Ressourcen nicht mehr benötigt, sollte er sicherstellen, dass diese Ressourcen ordnungsgemäß entfernt werden. Weitere Informationen finden Sie unter [Verwalten von Azure-Ressourcen über das Azure-Portal](../../azure-resource-manager/management/manage-resources-portal.md).

## <a name="consumption-costs"></a>Verbrauchskosten

Verbrauchskosten sind die Summe aller Gebühren für die Azure-Ressourcennutzung für Benutzer, die auf einen Azure Virtual Desktop-Hostpool zugreifen. Diese Kosten stammen von den virtuellen Computern (VMs) des Sitzungshosts innerhalb der Hostpools, einschließlich der Ressourcen, die von anderen Produkten in Azure gemeinsam genutzt werden, und von Identitätsverwaltungssystemen, die zusätzliche Infrastruktur benötigen, damit der Dienst verfügbar bleibt, z. B. ein Domänencontroller für Active Directory Domain Services (AD DS).

### <a name="session-host-vm-costs"></a>Kosten für virtuelle Sitzungshostcomputer

In Azure Virtual Desktop verwenden virtuelle Sitzungshostcomputer die folgenden drei Azure-Dienste:

- Virtuelle Computer (Compute)
- Speicher für verwaltete Datenträger (einschließlich Betriebssystemspeicher pro VM und beliebiger Datenträger für persönliche Desktops)
- Bandbreite (Netzwerk)

Diese Gebühren können auf der Ebene der Azure-Ressourcengruppe angezeigt werden, auf der die hostpoolspezifischen Ressourcen, einschließlich der virtuellen Sitzungshostcomputer, zugewiesen werden. Wenn ein oder mehrere Hostpools auch so konfiguriert sind, dass sie den kostenpflichtigen Log Analytics-Dienst verwenden, um VM-Daten an das optionale Azure Virtual Desktop Insights-Feature zu senden, führt die Rechnung auch die Gebühren für Log Analytics für die entsprechenden Azure-Ressourcengruppen auf. Weitere Informationen finden Sie unter [Überwachen der Kostenvoranschläge für Windows Virtual Desktop](../azure-monitor-costs.md).

Von den drei primären Kosten für die Nutzung des VM-Sitzungshosts, die zu Beginn dieses Abschnitts aufgeführt sind, kostet „Compute“ in der Regel am meisten. Um die Computekosten zu senken und den Ressourcenbedarf mit der Verfügbarkeit zu optimieren, entscheiden sich viele Kunden für eine [automatische Skalierung der Sitzungshosts](../set-up-scaling-script.md).

### <a name="domain-controller-costs-for-active-directories"></a>Domänencontrollerkosten für Active Directory-Instanzen

Domänencontroller-VMs verwenden mindestens die folgenden vier Azure-Dienste:

- Virtuelle Computer (Compute)
- Speicher für verwaltete Datenträger (einschließlich Betriebssystemspeicher pro VM und beliebiger Datenträger für persönliche Desktops)
- Bandbreite (Netzwerk)
- Virtuelle Netzwerke

Wenn Ihre Azure Virtual Desktop-Bereitstellung auf einen Domänencontroller zurückgreift, um ihre Active Directory-Instanz auszuführen, sollten Sie diesen in die Gesamtkosten für die Azure Virtual Desktop-Bereitstellung einbeziehen. Domänencontroller, die in Azure gehostet werden, teilen sich auch die drei Azure-Dienste für virtuelle Sitzungshostcomputer, die in den [Kosten für virtuelle Sitzungshostcomputer](#session-host-vm-costs) beschrieben sind, da ein virtueller Azure-Standardcomputer auch die Identitäten der Active Directory-Instanz zur Verfügung stellen muss.

Domänencontroller unterscheiden sich jedoch in einigen wesentlichen Punkten von virtuellen Sitzungshostcomputern:

- Domänencontroller verursachen eine zusätzliche virtuelle Netzwerkgebühr, da sie mit anderen Diensten außerhalb der Bereitstellung kommunizieren müssen.
- Die Skalierung der Verfügbarkeit von Domänencontrollern kann zu Problemen führen, da Ihre Bereitstellungen darauf angewiesen sind, dass Ihre Domänencontroller immer verfügbar sind.

### <a name="shared-service-costs"></a>Kosten für gemeinsam genutzte Dienste

Je nachdem, welche Features Ihre Azure Virtual Desktop-Bereitstellung verwendet, müssen Sie möglicherweise auch für Azure-Speicher für eine beliebige Kombination der folgenden optionalen Features bezahlen:

- [MSIX-Feature zum Anfügen von Apps](../what-is-app-attach.md)
- [Benutzerdefinierte Betriebssystemimages](../set-up-customize-master-image.md)
- [FSLogix-Profile](../fslogix-containers-azure-files.md)

Diese Features verwenden Azure-Speicheroptionen wie [Azure Files](../../storage/files/storage-files-introduction.md) und [Azure NetApp Files](../../azure-netapp-files/azure-netapp-files-introduction.md), sodass sie ihren Speicher mit anderen Azure-Diensten als Azure Virtual Desktop teilen können. Wir empfehlen, ein separates Speicherkonto für den Speicher zu erstellen, den Sie für Ihre Azure Virtual Desktop-Features kaufen, um sicherzustellen, dass Sie den Unterschied zwischen dessen Kosten und den Kosten für andere von Ihnen verwendete Azure-Dienste erkennen können.

### <a name="user-access-costs"></a>Kosten für den Benutzerzugriff

Sie zahlen jeden Monat die Kosten für den Benutzerzugriff für jeden Benutzer, der eine Verbindung mit Apps oder Desktops in Ihrer Azure Virtual Desktop-Bereitstellung herstellt. Weitere Informationen über die Preisgestaltung von Azure Virtual Desktop pro Benutzer finden Sie unter [Grundlegendes zu Lizenzen und zu den Preisen für benutzerspezifischen Zugriff](licensing.md).

## <a name="predicting-costs-before-deploying-azure-virtual-desktop"></a>Vorhersagen der Kosten vor der Bereitstellung von Azure Virtual Desktop

Nachdem Sie jetzt die Grundlagen verstanden haben, lassen Sie uns mit der Schätzung beginnen. Dazu müssen wir sowohl die Kosten für den Verbrauch als auch für den Zugriff der Benutzer schätzen.

### <a name="predicting-consumption-costs"></a>Vorhersagen der Verbrauchskosten

Sie können den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/) verwenden, um die Kosten für die Nutzung von Azure Virtual Desktop abzuschätzen, bevor Sie eine Bereitstellung erstellen. So können Sie die Verbrauchskosten vorhersagen:

1. Wählen Sie im Preisrechner die Registerkarte **Compute** (Berechnen) aus, um die Computeoptionen des Azure-Preisrechners anzuzeigen.

2. Wählen Sie **Azure Virtual Desktop** aus. Das Azure Virtual Desktop-Rechnermodul sollte angezeigt werden.

3. Geben Sie die Werte für Ihre Bereitstellung in die Felder ein, um Ihre monatliche Azure-Rechnung auf der Grundlage Ihrer erwarteten Compute-, Speicher- und Netzwerknutzung zu schätzen.

>[!NOTE]
>Derzeit kann das Azure-Preisrechnermodul von Azure Virtual Desktop nur die Verbrauchskosten für virtuelle Sitzungshostcomputer und den gesamten zusätzlichen Speicherplatz aller optionalen Azure Virtual Desktop-Features schätzen, die Speicherplatz benötigen, den Sie bereitstellen möchten. Sie können jedoch Schätzungen für andere Azure Virtual Desktop-Features in separaten Modulen auf derselben Seite des Azure-Preisrechners hinzufügen, um eine vollständigere oder modulare Kostenschätzung zu erhalten.
>
>Sie können zusätzliche Azure-Preisrechnermodule hinzufügen, um die Kostenauswirkungen anderer Komponenten Ihrer Bereitstellung zu schätzen. Dies umfasst folgende Komponenten, ist aber nicht auf diese beschränkt:
>
>- Domänencontroller
>- Andere speicherabhängige Features wie benutzerdefinierte Betriebssystemimages, das MSIX-Feature zum Anfügen von Apps und Azure Monitor

### <a name="predicting-user-access-costs"></a>Vorhersagen der Kosten für den Benutzerzugriff

Die Kosten für den Benutzerzugriff hängen von der Anzahl der Benutzer ab, die jeden Monat eine Verbindung mit Ihrer Bereitstellung herstellen. Wie Sie die zu erwartenden Gesamtkosten für den Benutzerzugriff abschätzen können, erfahren Sie unter [Schätzen der App-Streamingkosten auf Benutzerebene für Azure Virtual Desktop](streaming-costs.md).

## <a name="viewing-costs-after-deploying-azure-virtual-desktop"></a>Anzeigen der Kosten nach der Bereitstellung von Azure Virtual Desktop

Sobald Sie Azure Virtual Desktop bereitgestellt haben, können Sie [Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md) verwenden, um Ihre Rechnungen einzusehen. In diesem Abschnitt erfahren Sie, wie Sie die Preise für Ihre aktuellen Dienste ermitteln können.

### <a name="viewing-bills-for-consumption-costs"></a>Anzeigen von Rechnungen für Verbrauchskosten

Mit den richtigen [Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md)-Berechtigungen können Benutzer in Ihrem Unternehmen, z. B. Abrechnungsadministratoren, [Kostenanalysetools](../../cost-management-billing/costs/cost-analysis-common-uses.md) verwenden und Azure-Abrechnungen über [Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md) finden, um die monatlichen Kosten für den Verbrauch von Azure Virtual Desktop im Rahmen Ihrer Azure-Abonnements nachzuverfolgen.

### <a name="viewing-bills-for-user-access-costs"></a>Anzeigen von Rechnungen für Benutzerzugriffskosten

Die Kosten für den Benutzerzugriff treten in jedem Abrechnungszeitraum auf der Azure-Rechnung für jedes angemeldete Abonnement auf, zusammen mit den Verbrauchskosten und anderen Azure-Gebühren.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie eine genauere Vorstellung davon haben möchten, wie viel bestimmte Teile Ihrer Bereitstellung kosten werden, werfen Sie einen Blick auf diese Artikel:

- [Grundlegendes zu Lizenzen und zu den Preisen für benutzerspezifischen Zugriff](licensing.md)
- [Schätzen der App-Streamingkosten auf Benutzerebene für Azure Virtual Desktop](streaming-costs.md)
- [Markieren Sie die Azure Virtual Desktop-Ressourcen zur Verwaltung von Kosten](../tag-virtual-desktop-resources.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)