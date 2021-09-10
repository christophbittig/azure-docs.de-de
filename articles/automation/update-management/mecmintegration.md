---
title: Integrieren der Azure Automation-Updateverwaltung mit Microsoft Endpoint Configuration Manager
description: Dieser Artikel hilft Ihnen beim Konfigurieren von Microsoft Endpoint Configuration Manager mit der Updateverwaltung, um Softwareupdates auf Manager-Clients bereitzustellen.
services: automation
ms.subservice: update-management
ms.date: 07/14/2021
ms.topic: conceptual
ms.openlocfilehash: 6889d5058a7ca93e410afa0454e108d79bb70569
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114203901"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>Integrieren der Updateverwaltung mit Microsoft Endpoint Configuration Manager

Kunden, die in Microsoft Endpoint Configuration Manager investiert haben, um PCs, Server und Mobilgeräte zu verwalten, profitieren auch von der Leistungsstärke und dem Funktionsumfang dieser Lösung bei der Verwaltung von Softwareupdates im Rahmen des Softwareupdateverwaltungs-Zyklus.

Sie können Berichte für verwaltete Windows-Server erstellen und die Server aktualisieren, indem Sie Softwareupdatebereitstellungen in Microsoft Endpoint Configuration Manager erstellen und vorab bereitstellen. Außerdem können Sie mithilfe der [Updateverwaltung](overview.md) den detaillierten Status abgeschlossener Updatebereitstellungen abrufen. Auch wenn Sie Microsoft Endpoint Configuration Manager nur zum Aktualisieren von Complianceberichten, aber nicht zum Verwalten von Updatebereitstellungen für Ihre Windows-Server verwenden, können Sie für die Berichterstellung weiterhin Microsoft Endpoint Configuration Manager nutzen, während Sicherheitsupdates mit der Azure Automation-Updateverwaltung verwaltet werden.

>[!NOTE]
>Auch wenn die Updateverwaltung die Updatebewertung und das Patchen von Windows Server 2008 R2 unterstützt, unterstützt sie keine Clients, die von Microsoft Endpoint Configuration Manager verwaltet werden und auf denen dieses Betriebssystem ausgeführt wird.

## <a name="prerequisites"></a>Voraussetzungen

* Sie müssen Ihrem Automation-Konto die [Azure Automation-Updateverwaltung](overview.md) hinzugefügt haben.
* Windows-Server, die zurzeit von der Microsoft Endpoint Configuration Manager-Umgebung verwaltet werden, müssen auch Berichte an den Log Analytics-Arbeitsbereich übermitteln, in dem auch die Updateverwaltung aktiviert ist.
* Dieses Feature ist in der aktuellen Branchversion 1606 (und höher) von Microsoft Endpoint Configuration Manager aktiviert. Informationen zum Integrieren der zentralen Microsoft Endpoint Configuration Manager-Verwaltungswebsite oder einer eigenständigen primären Website mit Azure Monitor-Protokollen und zum Importieren von Sammlungen finden Sie unter [Herstellen einer Verbindung zwischen Configuration Manager und Azure Monitor-Protokollen](../../azure-monitor/logs/collect-sccm.md).  
* Windows-Agents müssen entweder für die Kommunikation mit einem WSUS-Server (Windows Server Update Services) konfiguriert sein oder über Zugriff auf Microsoft-Update verfügen, wenn sie keine Sicherheitsupdates von Microsoft Endpoint Configuration Manager erhalten.

Wie Sie die in Azure IaaS gehosteten Clients mit Ihrer vorhandenen Microsoft Endpoint Configuration Manager-Umgebung verwalten, richtet sich hauptsächlich nach der Verbindung zwischen Azure-Rechenzentren und Ihrer Infrastruktur. Diese Verbindung wirkt sich auf alle Designänderungen aus, die Sie an Ihrer Microsoft Endpoint Configuration Manager-Infrastruktur vornehmen müssen, und auf die Kosten, die aufgrund dieser Änderungen anfallen. Informationen zu den Planungsüberlegungen, die Sie berücksichtigen müssen, finden Sie unter [Configuration Manager in Azure – häufig gestellte Fragen](/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>Verwalten von Softwareupdates mit Microsoft Endpoint Configuration Manager

Führen Sie folgende Schritte aus, wenn Sie Updatebereitstellungen weiterhin über Microsoft Endpoint Configuration Manager verwalten möchten. Azure Automation stellt eine Verbindung mit Microsoft Endpoint Configuration Manager her, um Updates auf Clientcomputer anzuwenden, die mit Ihrem Log Analytics-Arbeitsbereich verbunden sind. Die Updateinhalte sind so im Cache der Clientcomputer verfügbar, als würde die Bereitstellung von Microsoft Endpoint Configuration Manager verwaltet.

1. Erstellen Sie mithilfe des unter [Bereitstellen von Softwareupdates](/configmgr/sum/deploy-use/deploy-software-updates) beschriebenen Vorgangs eine Softwareupdatebereitstellung von der Top-Level-Website in Ihrer Microsoft Endpoint Configuration Manager-Hierarchie. Die einzige Einstellung, die anders als eine Standardbereitstellung konfiguriert werden muss, ist die Option **Installationsstichtag** in Endpoint Configuration Manager. Sie muss auf ein zukünftiges Datum festgelegt werden, um sicherzustellen, dass die Updatebereitstellung nur von der Automation-Updateverwaltung initiiert wird. Diese Einstellung wird unter [Schritt 4, Bereitstellen der Softwareupdategruppe](/configmgr/sum/deploy-use/manually-deploy-software-updates#BKMK_4DeployUpdateGroup) beschrieben.

2. Konfigurieren Sie in Endpoint Configuration Manager die Option **Benutzerbenachrichtigungen**, um das Anzeigen von Benachrichtigungen auf den Zielcomputern zu verhindern. Es wird empfohlen, die Option **In Softwarecenter und allen Benachrichtigungen ausblenden** festzulegen, um zu verhindern, dass ein angemeldeter Benutzer über eine geplante Updatebereitstellung benachrichtigt wird und diese Updates manuell bereitstellt. Diese Einstellung wird unter [Schritt 4, Bereitstellen der Softwareupdategruppe](/configmgr/sum/deploy-use/manually-deploy-software-updates#BKMK_4DeployUpdateGroup) beschrieben.

3. Wählen Sie in Azure Automation die Option **Updateverwaltung** aus. Erstellen Sie anhand der unter [Erstellen einer Updatebereitstellung](deploy-updates.md#schedule-an-update-deployment) beschriebenen Schritte eine neue Bereitstellung, und wählen Sie in der Dropdownliste **Typ** den Eintrag **Importierte Gruppen** aus, um die entsprechende Microsoft Endpoint Configuration Manager-Sammlung auszuwählen. Berücksichtigen Sie dabei die folgenden wichtigen Punkte:

    a. Wenn in der ausgewählten Microsoft Endpoint Configuration Manager-Gerätesammlung ein Wartungsfenster definiert ist, verwenden die Elemente der Sammlung dieses Fenster und nicht die Einstellung **Dauer**, die in der geplanten Bereitstellung definiert ist.

    b. Elemente der Zielsammlung müssen über eine Verbindung mit dem Internet verfügen (entweder direkt, über einen Proxyserver oder über das Log Analytics-Gateway).

Nach Abschluss der Updatebereitstellung über Azure Automation installieren die Zielcomputer, die Mitglieder der ausgewählten Computergruppe sind, die Updates zum geplanten Zeitpunkt aus ihrem lokalen Clientcache. Sie können den [Status der Updatebereitstellung anzeigen](deploy-updates.md#check-deployment-status), um die Ergebnisse Ihrer Bereitstellung zu überwachen.

## <a name="manage-software-updates-from-azure-automation"></a>Verwalten von Softwareupdates über Azure Automation

Um Updates für virtuelle Windows Server-Computer zu verwalten, die Microsoft Endpoint Configuration Manager-Clients sind, müssen Sie die Clientrichtlinie so konfigurieren, dass die Funktion der Softwareupdateverwaltung für alle von der Updateverwaltung verwalteten Clients deaktiviert wird. Standardmäßig gelten Clienteinstellungen für alle Geräte in der Hierarchie. Weitere Informationen zu dieser Richtlinieneinstellung und ihrer Konfiguration finden Sie unter [Konfigurieren von Clienteinstellungen in Configuration Manager](/configmgr/core/clients/deploy/configure-client-settings).

Nachdem Sie diese Konfigurationsänderung vorgenommen haben, erstellen Sie anhand der unter [Erstellen einer Updatebereitstellung](deploy-updates.md#schedule-an-update-deployment) beschriebenen Schritte eine neue Bereitstellung, und wählen Sie in der Dropdownliste **Typ** den Eintrag **Importierte Gruppen** aus, um die entsprechende Microsoft Endpoint Configuration Manager-Sammlung auszuwählen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Einrichten eines Integrationszeitplans finden Sie unter [Planen einer Updatebereitstellung](deploy-updates.md#schedule-an-update-deployment).
