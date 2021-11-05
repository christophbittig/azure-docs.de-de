---
title: Tutorial zum Konfigurieren einer Azure-SSIS Integration Runtime für die Verknüpfung mit einem virtuellen Netzwerk
description: Tutorial zum Konfigurieren einer Azure-SSIS Integration Runtime für die Verknüpfung mit einem virtuellen Netzwerk.
author: swinarko
ms.author: sawinark
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
ms.openlocfilehash: 8ee28e9c310002c0a0b2e29a77b35c71045c9fc4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040857"
---
# <a name="configure-azure-ssis-integration-runtime-to-join-a-virtual-network"></a>Konfigurieren einer Azure-SSIS Integration Runtime für die Verknüpfung mit einem virtuellen Netzwerk

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieses Tutorial enthält minimale Schritte über das Azure-Portal / die Azure-Data-Factory-Benutzeroberfläche (ADF), um Ihre Azure-SQL-Server-Integration-Services (SSIS) Integration Runtime (IR) für die Verknüpfung mit einem virtuellen Netzwerk mit einer Express-Einspeis-Methode zu konfigurieren.  Dazu müssen folgende Schritte ausgeführt werden:

1. Konfigurieren Sie ein virtuelles Netzwerk für die Express-Einspeis-Methode über das Azure-Portal.
1. Verbinden Ihres Azure-SSIS IR mit dem virtuellen Netzwerk mit einer Express-Einspeis-Methode über die ADF-Benutzeroberfläche

## <a name="prerequisites"></a>Voraussetzungen

- **Azure-SSIS IR:** Erstellen Sie zunächst über die [ADF-Benutzeroberfläche eine Azure-SSIS IR,](tutorial-deploy-ssis-packages-azure.md) falls noch nicht vorhanden.

- **Virtuelles Netzwerk:** Wenn Sie noch keines besitzen, erstellen Sie zuerst [ein virtuelles Netzwerk über das Azure-Portal.](../virtual-network/quick-create-portal.md) Stellen Sie Folgendes sicher:
  - In Ihrem virtuellen Netzwerk gibt es keine Ressourcensperre.
  - Dem Nutzer, der die Azure-SSIS IR erstellt, werden die erforderlichen Berechtigungen für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) zum Beitreten zu Ihrem virtuellen Netzwerk/Subnetz gewährt. Weitere Informationen finden Sie im Abschnitt [Berechtigungen für virtuelle Netzwerke auswählen](azure-ssis-integration-runtime-express-virtual-network-injection.md#perms).

Die folgenden Konfigurationen virtueller Netzwerke werden in diesem Tutorial nicht behandelt:

- Wenn Sie eine statische öffentliche IP-Adresse für Azure-SSIS IR verwenden.
- Sie verwenden einen eigenen DNS-Server (Domain Name System).
- Sie verwenden eine Netzwerksicherheitsgruppe (NSG).
- Wenn Sie benutzerdefinierte Routen (USER-Defined Routes, UDRs) verwenden.

Wenn Sie diese optionalen Schritte ausführen möchten, lesen Sie den Abschnitt [Express-Einspeisungsmethode für virtuelle Netzwerke.](azure-ssis-integration-runtime-express-virtual-network-injection.md)

## <a name="configure-a-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Konfigurieren Sie ein virtuelles Netzwerk über das Azure-Portal, bevor Sie versuchen, eine Azure-SSIS IR mit dem Netzwerk zu verknüpfen.

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit unterstützen nur diese Webbrowser die ADF-Benutzeroberfläche.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Weitere Dienste**. Filtern Sie nach **Virtuelle Netzwerke**, und wählen Sie die Option aus.

1. Filtern Sie nach Ihrem virtuellen Netzwerk, und wählen Sie es in der Liste aus.

1. Wählen Sie im Menü links **Subnetze** aus.

   - Stellen Sie sicher, dass ein geeignetes Subnetz vorhanden ist, mit dem Ihre Azure-SSIS IR verbunden werden kann. Weitere Informationen finden Sie im Abschnitt [Auswählen eines Subnetzes.](azure-ssis-integration-runtime-express-virtual-network-injection.md#subnet)

   - Stellen Sie sicher, dass das ausgewählte Subnetz an Azure Batch delegiert worden ist. Weitere Informationen finden Sie im Abschnitt [Delegieren eines Subnetzes an Azure Batch.](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet)

1. Stellen Sie sicher, dass *Microsoft.Batch* ein registrierter Ressourcenanbieter im Azure-Abonnement ist, der über das virtuelle Netzwerk verfügt, dem Ihre Azure-SSIS IR beitreten soll. Ausführliche Anweisungen finden Sie im Abschnitt [Azure Batch als Ressourcenanbieter registrieren](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

## <a name="join-azure-ssis-ir-to-the-virtual-network"></a>Azure-SSIS IR einem virtuellen Netzwerk hinzufügen

Sie müssen das virtuelle Netzwerk konfigurieren, bevor Sie die Azure-SSIS IR damit verknüpfen können:

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit unterstützen nur diese Webbrowser die ADF-Benutzeroberfläche.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Menü **Data Factorys** aus. Wenn **Data Factorys** nicht im Menü angezeigt wird, wählen Sie **Weitere Dienste** und dann im Abschnitt **INTELLIGENCE + ANALYSE** die Option **Data Factorys** aus.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png" alt-text="Liste von Data Factorys":::

1. Wählen Sie Ihre ADF mit der Azure-SSIS IR in der Liste aus. Die Startseite für Ihre ADF wird angezeigt. Wählen Sie die Kachel **Erstellen und überwachen** aus. Die ADF-Benutzeroberfläche wird auf einer separaten Registerkarte angezeigt.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png" alt-text="Data Factory-Startseite":::

1. Wechseln Sie in der Data-Factory-Benutzeroberfläche zur Registerkarte **Bearbeiten**, klicken Sie auf **Verbindungen** und wechseln Sie zur Registerkarte **Integration Runtimes**.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png" alt-text="Registerkarte &quot;Integration Runtimes&quot;":::

1. Wenn Ihre Azure-SSIS IR ausgeführt wird, wählen Sie in der Liste **Integration Runtimes** in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Beenden** aus. Sie können Ihre Azure-SSIS IR erst bearbeiten, wenn sie beendet wurde.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png" alt-text="Beenden der IR":::

1. Wählen Sie in der Liste **Integration Runtimes** in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Bearbeiten** aus.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png" alt-text="Bearbeiten der Integration Runtime":::

1. Navigieren Sie im Bereich **Integration Runtime Setup** durch die Seiten **Allgemeine Einstellungen** und **Bereitstellungseinstellungen**, indem Sie die Schaltfläche **Weiter** auswählen.

1. Führen Sie auf der Seite **Erweiterte Einstellungen** die unten angegebenen Schritte aus.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet-express.png" alt-text="Erweiterte Einstellungen für die Exress-Einspeisung virtueller Netzwerke":::

   1. Aktivieren Sie das Kontrollkästchen **VNET für die Einbindung Ihrer Azure-SSIS Integration Runtime-Instanz auswählen, Erstellung bestimmter Netzwerkressourcen für ADF ermöglichen und optional eigene statische öffentliche IP-Adressen verwenden**.

   1. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Ihr virtuelles Netzwerk enthält.

   1. Für **Standort** ist derselbe Standort wie für Ihre Integration Runtime ausgewählt.

   1. Wählen Sie für **Typ** den Typ Ihres virtuellen Netzwerks aus: **Azure Resource Manager-VNET** oder „Klassisches virtuelles Netzwerk“. Es wird empfohlen, die Option **Azure Resource Manager-VNET** auszuwählen, da das klassische virtuelle Netzwerk bald veraltet sein wird.

   1. Wählen Sie unter **VNET-Name** den Namen des virtuellen Netzwerks aus. Dies sollte derselbe Name sein, der zum Konfigurieren eines VNET-Dienstendpunkts bzw. privaten Endpunkts für Ihren Azure SQL-Datenbank-Server verwendet wird, der SSISDB hostet. Alternativ kann dies der Name sein, der von Ihrer Azure SQL verwalteten Instanz (Managed Instance) verknüpft wurde, die SSISDB hostet. Oder er sollte mit dem Namen des Netzwerks übereinstimmen, das mit Ihrem lokalen Netzwerk verbunden ist.

   1. Wählen Sie unter **Subnetzname** den Namen des Subnetzes für Ihr virtuelles Netzwerk aus. Dies sollte derselbe Name sein, der zum Konfigurieren eines VNET-Dienstendpunkts für Ihren Azure-SQL-Datenbank-Server verwendet wird, der SSISDB hostet. Alternativ sollte es sich um ein anderes als das von Ihrer Azure SQL verwalteten Instanz (Managed Instance) verknüpftes Subnetz handeln, das SSISDB hostet.

   1. Für die **VNET Einspeisungsmethode** wählen Sie **Express** für die Express-Einspeisung virtueller Netzwerke aus.

   1. Wählen Sie **VNET-Überprüfung** aus. Wenn die Überprüfung erfolgreich war, wählen Sie **Weiter** aus.

1. Überprüfen Sie auf der Seite **Zusammenfassung** alle Einstellungen für die Azure-SSIS IR und klicken Sie dann auf die Schaltfläche **Aktualisieren**.

1. Starten Sie die Azure-SSIS IR, indem Sie in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Starten** auswählen. Es dauert ungefähr 5 Minuten, bis die Azure-SSIS IR gestartet wird, die ein virtuelles Netzwerk mit einer Express-Einspeisungsmethode verbindet.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren eines virtuellen Netzwerks zum Einspeisen der Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Express-Einspeisungsmethode virtueller Netzwerke](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Azure-SSIS IR zu einem virtuellen Netzwerk über eine ADF-Benutzeroberfläche hinzufügen](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure-SSIS IR zu einem virtuellen Netzwerk über Azure PowerShell hinzufügen](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](tutorial-deploy-ssis-packages-azure.md). Dieses Tutorial enthält ausführliche Anweisungen zum Erstellen einer Azure-SSIS IR. Es wird der Azure-SQL-Datenbank-Server zum Hosten von SSISDB verwendet. 
- [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dieser Artikel baut auf dem Tutorial auf. Er enthält Anweisungen zur Verwendung des Azure-SQL-Datenbank-Servers, der mit einem VNS-Dienstendpunkt/einer IP-Firewallregel/einem privaten Endpunkt konfiguriert ist, oder einer Azure SQL verwalteten Instanz, die mit einem virtuellen Netzwerk zum Hosten von SSISDB verbunden ist. Außerdem wird veranschaulicht, wie Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen und verstehen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS Integration Runtime aufskalieren, indem Sie weitere Knoten hinzufügen.
