---
title: Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk mithilfe einer Benutzeroberfläche
description: Erfahren Sie, wie Sie die Azure Data Factory Studio-Benutzeroberfläche verwenden, um eine Azure-SSIS Integration Runtime mit einem virtuellen Azure-Netzwerk zu verbinden.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d02a5d1025e470d8751d87816cbcc6395be41593
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403155"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network-with-azure-data-factory-studio-ui"></a>Verknüpfen Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk mit einer Azure Data Factory Studio-Benutzeroberfläche

In diesem Abschnitt erfahren Sie, wie Sie mithilfe von Microsoft Azure-Portal und der Data Factory Studio-Benutzeroberfläche eine vorhandene Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen (klassisch oder Azure Resource Manager). 

Bevor Sie die Azure-SSIS IR mit dem virtuellen Netzwerk verknüpfen, müssen Sie das virtuelle Netzwerk ordnungsgemäß konfigurieren. Führen Sie die Schritte in dem Abschnitt aus, der dem Typ Ihres virtuellen Netzwerks entspricht (klassisch oder Azure Resource Manager). Befolgen Sie dann die Schritte im dritten Abschnitt, um die Azure-SSIS IR mit dem virtuellen Netzwerk zu verknüpfen. 

## <a name="configure-an-azure-resource-manager-virtual-network"></a>Konfigurieren eines virtuellen Azure Resource Manager-Netzwerks

Konfigurieren Sie ein virtuelles Azure Resource Manager-Netzwerk mithilfe des Portals, bevor Sie eine Azure-SSIS IR mit dem Netzwerk verknüpfen.

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit wird die Data Factory-Benutzeroberfläche nur in diesen Webbrowsern unterstützt. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wählen Sie **Weitere Dienste**. Filtern Sie nach **Virtuelle Netzwerke**, und wählen Sie die Option aus. 

1. Filtern Sie nach Ihrem virtuellen Netzwerk, und wählen Sie es in der Liste aus. 

1. Wählen Sie auf der Seite **Virtuelles Netzwerk** die Option **Eigenschaften** aus. 

1. Wählen Sie für die **RESSOURCEN-ID** die Schaltfläche „Kopieren“ aus, um die Ressourcen-ID für das virtuelle Netzwerk in die Zwischenablage zu kopieren. Speichern Sie die ID aus der Zwischenablage in OneNote oder in einer Datei. 

1. Wählen Sie im linken Menü **Subnetze** aus. Stellen Sie sicher, dass die Anzahl der verfügbaren Adressen größer ist als die Anzahl der Knoten in Ihrer Azure-SSIS IR. 

1. Vergewissern Sie sich, dass dieser Azure Batch-Anbieter in dem Azure-Abonnement, das über das virtuelle Netzwerk verfügt, registriert ist. Alternativ können Sie auch den Azure Batch-Anbieter registrieren. Wenn in Ihrem Abonnement bereits ein Azure Batch-Konto enthalten ist, ist Ihr Abonnement für Azure Batch registriert. (Wenn Sie die Azure-SSIS Integration Runtime im Data Factory-Portal erstellen, wird der Azure Batch-Anbieter automatisch für Sie erstellt.) 

   1. Wählen Sie im Azure-Portal im linken Menü **Abonnements** aus. 

   1. Wählen Sie Ihr Abonnement aus. 

   1. Wählen Sie auf der linken Seite **Ressourcenanbieter** aus, und bestätigen Sie, dass **Microsoft.Batch** ein registrierter Anbieter ist. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="Bestätigung des Status &quot;Registriert&quot;":::

   Wenn **Microsoft.Batch** nicht in der Liste angezeigt wird, erstellen Sie zum Registrieren [ein leeres Azure Batch-Konto](../batch/batch-account-create-portal.md) in Ihrem Abonnement. Sie können es später wieder löschen. 

## <a name="configure-a-classic-virtual-network"></a>Konfigurieren eines klassischen virtuellen Netzwerks

Konfigurieren Sie ein klassisches virtuelles Netzwerk mithilfe des Portals, bevor Sie eine Azure-SSIS IR mit dem Netzwerk verknüpfen. 

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit wird die Data Factory-Benutzeroberfläche nur in diesen Webbrowsern unterstützt. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wählen Sie **Weitere Dienste**. Filtern Sie nach **Virtuelle Netzwerke (klassisch)** , und wählen Sie die Option aus. 

1. Filtern Sie nach Ihrem virtuellen Netzwerk, und wählen Sie es in der Liste aus. 

1. Wählen Sie auf der Seite **Virtuelles Netzwerk (klassisch)** die Option **Eigenschaften** aus. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png" alt-text="Ressourcen-ID des klassischen virtuellen Netzwerks":::

1. Wählen Sie für **RESSOURCEN-ID** die Schaltfläche „Kopieren“ aus, um die Ressourcen-ID für das klassische Netzwerk in die Zwischenablage zu kopieren. Speichern Sie die ID aus der Zwischenablage in OneNote oder in einer Datei. 

1. Wählen Sie im linken Menü **Subnetze** aus. Stellen Sie sicher, dass die Anzahl der verfügbaren Adressen größer ist als die Anzahl der Knoten in Ihrer Azure-SSIS IR. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png" alt-text="Anzahl der verfügbaren Adressen im virtuellen Netzwerk":::

1. Verknüpfen Sie **MicrosoftAzureBatch** mit der Rolle **Mitwirkender für klassische virtuelle Computer** für das virtuelle Netzwerk. 

   1. Wählen Sie im linken Menü **Zugriffssteuerung (IAM)** aus, und wählen Sie die Registerkarte **Rollenzuweisungen** aus. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png" alt-text="&quot;Zugriffssteuerung&quot;- und &quot;Hinzufügen&quot;-Schaltflächen":::

   1. Wählen Sie **Rollenzuweisung hinzufügen** aus.

   1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** für **Rolle** die Option **Mitwirkender für klassische virtuelle Computer** aus. Fügen Sie in das Feld **Auswählen** die Zeichenfolge **ddbf3205-c6bd-46ae-8127-60eb93363864** ein, und wählen Sie dann in der Liste der Suchergebnisse **Microsoft Azure Batch** aus. 

       Die Suchergebnisse:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png" alt-text=" auf der Seite":::&quot;Rollenzuweisung hinzufügen&quot;

   1. Wählen Sie **Speichern** aus, um die Einstellungen zu speichern und die Seite zu schließen. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png" alt-text="Speichern der Zugriffseinstellungen":::

   1. Vergewissern Sie sich, dass **Microsoft Azure Batch** in der Liste der Mitwirkenden angezeigt wird. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png" alt-text="Bestätigen des Azure Batch-Zugriffs":::

1. Vergewissern Sie sich, dass dieser Azure Batch-Anbieter in dem Azure-Abonnement, das über das virtuelle Netzwerk verfügt, registriert ist. Alternativ können Sie auch den Azure Batch-Anbieter registrieren. Wenn in Ihrem Abonnement bereits ein Azure Batch-Konto enthalten ist, ist Ihr Abonnement für Azure Batch registriert. (Wenn Sie die Azure-SSIS Integration Runtime im Data Factory-Portal erstellen, wird der Azure Batch-Anbieter automatisch für Sie erstellt.) 

   1. Wählen Sie im Azure-Portal im linken Menü **Abonnements** aus. 

   1. Wählen Sie Ihr Abonnement aus. 

   1. Wählen Sie auf der linken Seite **Ressourcenanbieter** aus, und bestätigen Sie, dass **Microsoft.Batch** ein registrierter Anbieter ist. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="Bestätigung des Status &quot;Registriert&quot;":::

   Wenn **Microsoft.Batch** nicht in der Liste angezeigt wird, erstellen Sie zum Registrieren [ein leeres Azure Batch-Konto](../batch/batch-account-create-portal.md) in Ihrem Abonnement. Sie können es später wieder löschen. 

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Verknüpfen der Azure SSIS-IR mit einem virtuellen Netzwerk

Nachdem Sie Ihr virtuelles Azure Resource Manager-Netzwerk oder klassisches virtuelles Netzwerk konfiguriert haben, können Sie die Azure-SSIS IR mit dem virtuellen Netzwerk verknüpfen:

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit wird die Data Factory-Benutzeroberfläche nur in diesen Webbrowsern unterstützt. 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Menü **Data Factorys** aus. Wenn **Data Factorys** nicht im Menü angezeigt wird, wählen Sie **Weitere Dienste** und dann im Abschnitt **INTELLIGENCE + ANALYSE** die Option **Data Factorys** aus. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png" alt-text="Liste von Data Factorys":::

1. Wählen Sie Ihre Data Factory mit der Azure-SSIS IR in der Liste aus. Die Startseite für Ihre Data Factory wird angezeigt. Wählen Sie die Kachel **Erstellen und überwachen** aus. Die Data Factory-Benutzeroberfläche wird auf einer separaten Registerkarte angezeigt. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png" alt-text="Data Factory-Startseite":::

1. Wechseln Sie in der Data Factory-Benutzeroberfläche zur Registerkarte **Bearbeiten**, klicken Sie auf **Verbindungen**, und wechseln Sie zur Registerkarte **Integration Runtimes**. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png" alt-text="Registerkarte &quot;Integration Runtimes&quot;":::

1. Wenn Ihre Azure-SSIS IR ausgeführt wird, wählen Sie in der Liste **Integration Runtimes** in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Beenden** aus. Sie können Ihre Azure-SSIS IR erst bearbeiten, wenn sie beendet wurde. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png" alt-text="Beenden der IR":::

1. Wählen Sie in der Liste **Integration Runtimes** in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Bearbeiten** aus. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png" alt-text="Bearbeiten der Integration Runtime":::

1. Navigieren Sie im Bereich für die Integration Runtime-Einrichtung durch die Abschnitte **Allgemeine Einstellungen** und **SQL-Einstellungen**, indem Sie die Schaltfläche **Weiter** auswählen. 

1. Gehen Sie im Abschnitt **Erweiterte Einstellungen** folgendermaßen vor: 

   1. Aktivieren Sie das Kontrollkästchen **VNET für die Einbindung Ihrer Azure-SSIS Integration Runtime-Instanz auswählen, Erstellung bestimmter Netzwerkressourcen für ADF ermöglichen und optional eigene statische öffentliche IP-Adressen verwenden**. 

   1. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Ihr virtuelles Netzwerk enthält.

   1. Für **Standort** ist derselbe Standort wie für Ihre Integration Runtime ausgewählt.

   1. Wählen Sie für **Typ** den Typ des virtuellen Netzwerks aus: „Klassisch“ oder „Azure Resource Manager“. Sie sollten ein virtuelles Azure Resource Manager-Netzwerk auswählen, da das klassische virtuelle Netzwerk bald veraltet sein wird.

   1. Wählen Sie unter **VNET-Name** den Namen des virtuellen Netzwerks aus. Es sollte mit dem Netzwerk übereinstimmen, das für SQL-Datenbank mit VNET-Dienstendpunkten oder die verwaltete SQL-Instanz mit einem privaten Endpunkt zum Hosten der SSISDB verwendet wird. Oder er sollte mit dem Namen des Netzwerks übereinstimmen, das mit Ihrem lokalen Netzwerk verbunden ist. Andernfalls kann es sich um ein beliebiges virtuelles Netzwerk handeln, damit Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwendet werden.

   1. Wählen Sie unter **Subnetzname** den Namen des Subnetzes für Ihr virtuelles Netzwerk aus. Es sollte mit dem Subnetz übereinstimmen, das für SQL-Datenbank mit VNET-Dienstendpunkten zum Hosten der SSISDB verwendet wird. Oder es sollte ein anderes Subnetz sein als das, das für Ihre SQL Managed Instance mit einem privaten Endpunkt zum Hosten der SSISDB verwendet wird. Andernfalls kann es sich um ein beliebiges Subnetz handeln, damit Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwendet werden.

   1. Aktivieren Sie das Kontrollkästchen **Statische öffentliche IP-Adressen für die Azure-SSIS Integration Runtime einbringen**, um festzulegen, ob Sie Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwenden möchten, damit Sie sie in der Firewall für Ihre Datenquellen zulassen können.

      Führen Sie die unten angegebenen Schritte aus, falls Sie das Kontrollkästchen aktivieren.

      1. Wählen Sie für **Erste statische öffentliche IP-Adresse** die erste statische öffentliche IP-Adresse aus, die [den Anforderungen für Ihre Azure-SSIS IR entspricht](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP). Klicken Sie auf den Link **Neue erstellen**, wenn Sie über keine statischen öffentlichen IP-Adressen verfügen, um diese im Azure-Portal zu erstellen. Klicken Sie dann hier auf die Schaltfläche „Aktualisieren“, um sie auszuwählen.
      
      1. Wählen Sie für **Zweite statische öffentliche IP-Adresse** die zweite statische öffentliche IP-Adresse aus, die [den Anforderungen für Ihre Azure-SSIS IR entspricht](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP). Klicken Sie auf den Link **Neue erstellen**, wenn Sie über keine statischen öffentlichen IP-Adressen verfügen, um diese im Azure-Portal zu erstellen. Klicken Sie dann hier auf die Schaltfläche „Aktualisieren“, um sie auszuwählen.

   1. Wählen Sie **VNET-Überprüfung** aus. Wenn die Überprüfung erfolgreich war, wählen Sie **Weiter** aus. 

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="Erweiterte Einstellungen mit einem virtuellen Netzwerk":::

1. Überprüfen Sie im Abschnitt **Zusammenfassung** alle Einstellungen für Ihre Azure-SSIS IR. Wählen Sie dann **Aktualisieren** aus.

1. Starten Sie die Azure-SSIS IR, indem Sie in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Starten** auswählen. Es dauert etwa 20 bis 30 Minuten, um die Azure-SSIS IR zu starten, die mit einem virtuellen Netzwerk verknüpft wird. 

## <a name="next-steps"></a>Nächste Schritte
- [Übersicht: Verknüpfen Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](join-azure-ssis-integration-runtime-virtual-network.md)
- [Virtuelle Netzwerk-Details der Azure-SSIS Integration Runtime](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Verknüpfen Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk mithilfe von Microsoft Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](./tutorial-deploy-ssis-packages-azure.md). Dieses Tutorial enthält ausführliche Anweisungen zum Erstellen einer Azure-SSIS IR. Dabei wird Azure SQL-Datenbank zum Hosten des SSIS-Katalogs verwendet. 
- [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dieser Artikel baut auf dem Tutorial auf. Sie erfahren, wie Sie Azure SQL-Datenbank mit VNET-Dienstendpunkten oder mit einer SQL Managed Instance in einem virtuellen Netzwerk verwenden, um den SSIS-Katalog zu hosten. Außerdem wird veranschaulicht, wie Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen. Darüber hinaus enthält er Statusbeschreibungen für die zurückgegebenen Informationen. 
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Außerdem wird gezeigt, wie Sie Ihre Azure SSIS-IR aufskalieren, indem Sie weitere Knoten hinzufügen.
