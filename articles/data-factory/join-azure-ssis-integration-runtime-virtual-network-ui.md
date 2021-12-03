---
title: Verknüpfen von Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk über das Azure-Portal
description: Hier erfahren Sie, wie Sie Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk über das Azure-Portal verknüpfen.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15b2a1bbac1d89c74a2fdea2f5ce3af51261b29e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087892"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network-via-azure-portal"></a>Verknüpfen von Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk über das Azure-Portal

In diesem Artikel erfahren Sie, wie Sie Ihre vorhandene Integration Runtime (IR) für Azure SQL Server Integration Services (SSIS) in Azure Data Factory (ADF) über das Azure-Portal/die ADF-Benutzeroberfläche mit einem virtuellen Netzwerk verknüpfen. 

Bevor Sie Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen, müssen Sie zuerst das virtuelle Netzwerk ordnungsgemäß konfigurieren. Weitere Informationen dazu finden Sie im Artikel [Konfiguration des virtuellen Netzwerks für Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md). Führen Sie als Nächstes die Schritte im Abschnitt unten aus, die für den Typ Ihres virtuellen Netzwerks (Azure Resource Manager/klassisch) gelten. Führen Sie abschließend die Schritte im letzten Abschnitt aus, um Azure-SSIS IR mit dem virtuellen Netzwerk zu verknüpfen. 

## <a name="configure-an-azure-resource-manager-virtual-network"></a>Konfigurieren eines virtuellen Azure Resource Manager-Netzwerks

Verwenden Sie das Azure-Portal, um ein virtuelles Azure Resource Manager-Netzwerk zu konfigurieren, bevor Sie versuchen, Azure-SSIS IR mit ihm zu verbinden.

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit unterstützen nur diese Webbrowser die ADF-Benutzeroberfläche. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wählen Sie **Weitere Dienste**. Filtern Sie nach **Virtuelle Netzwerke**, und wählen Sie die Option aus. 

1. Filtern Sie nach Ihrem virtuellen Netzwerk, und wählen Sie es in der Liste aus. 

1. Wählen Sie im Menü auf der linken Seite **Subnetze** aus.

   - Stellen Sie sicher, dass ein geeignetes Subnetz vorhanden ist, dem Azure-SSIS IR beitreten kann. Weitere Informationen dazu finden Sie im Abschnitt [Auswählen eines Subnetzes](azure-ssis-integration-runtime-standard-virtual-network-injection.md#subnet).

   - Wenn Sie die Express-Injektionsmethode des virtuellen Netzwerks verwenden, stellen Sie sicher, dass das ausgewählte Subnetz an Azure Batch delegiert ist. Weitere Informationen dazu finden Sie im Abschnitt [Delegieren eines Subnetzes an Azure Batch](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet).

1. Stellen Sie sicher, dass *Microsoft.Batch* ein registrierter Ressourcenanbieter im Azure-Abonnement ist, der über das virtuelle Netzwerk verfügt, dem Azure-SSIS IR beitreten kann. Ausführliche Anweisungen finden Sie im Abschnitt [Registrieren von Azure Batch als Ressourcenanbieter](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

## <a name="configure-a-classic-virtual-network"></a>Konfigurieren eines klassischen virtuellen Netzwerks

Verwenden Sie das Azure-Portal, um ein klassisches virtuelles Netzwerk zu konfigurieren, bevor Sie versuchen, Azure-SSIS IR mit ihm zu verknüpfen. 

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit unterstützen nur diese Webbrowser die ADF-Benutzeroberfläche. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wählen Sie **Weitere Dienste**. Filtern Sie nach **Virtuelle Netzwerke (klassisch)** , und wählen Sie die Option aus. 

1. Filtern Sie nach Ihrem virtuellen Netzwerk, und wählen Sie es in der Liste aus. 

1. Wählen Sie auf der Seite **Virtuelles Netzwerk (klassisch)** die Option **Eigenschaften** aus. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png" alt-text="Ressourcen-ID des klassischen virtuellen Netzwerks":::

1. Wählen Sie für die Schaltfläche „Kopieren“ für **RESSOURCEN-ID** aus, um die Ressourcen-ID für das virtuelle Netzwerk in die Zwischenablage zu kopieren. Speichern Sie die ID aus der Zwischenablage in OneNote oder in einer Datei. 

1. Wählen Sie im Menü auf der linken Seite **Subnetze** aus. Stellen Sie sicher, dass die Anzahl der verfügbaren IP-Adressen in Ihrem ausgewählten Subnetz größer als die doppelte Anzahl Ihrer Azure-SSIS IR-Knoten ist. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png" alt-text="Anzahl der verfügbaren Adressen im virtuellen Netzwerk":::

1. Verknüpfen Sie **MicrosoftAzureBatch** mit der Rolle **Mitwirkender für klassische virtuelle Computer** für das virtuelle Netzwerk. 

   1. Wählen Sie im Menü auf der linken Seite die Option **Zugriffssteuerung (IAM)** aus, und wählen Sie die Registerkarte **Rollenzuweisungen** aus. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png" alt-text="&quot;Zugriffssteuerung&quot;- und &quot;Hinzufügen&quot;-Schaltflächen":::

   1. Wählen Sie **Rollenzuweisung hinzufügen** aus.

   1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** für **Rolle** die Option **Mitwirkender für klassische virtuelle Computer** aus. Fügen Sie **ddbf3205-c6bd-46ae-8127-60eb93363864** in das Feld **Auswählen** ein, und wählen Sie dann **MicrosoftAzureBatch** aus der Liste der Suchergebnisse aus. 

      Die Suchergebnisse:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png" alt-text=" auf der Seite":::&quot;Rollenzuweisung hinzufügen&quot;

   1. Wählen Sie **Speichern** aus, um die Einstellungen zu speichern und die Seite zu schließen. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png" alt-text="Speichern der Zugriffseinstellungen":::

   1. Vergewissern Sie sich, dass **MicrosoftAzureBatch** in der Liste der Mitwirkenden angezeigt wird. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png" alt-text="Bestätigen des Azure Batch-Zugriffs":::

1. Stellen Sie sicher, dass *Microsoft.Batch* ein registrierter Ressourcenanbieter im Azure-Abonnement ist, der über das virtuelle Netzwerk verfügt, dem Azure-SSIS IR beitreten kann. Ausführliche Anweisungen finden Sie im Abschnitt [Registrieren von Azure Batch als Ressourcenanbieter](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

## <a name="join-azure-ssis-ir-to-the-virtual-network"></a>Verknüpfen von Azure-SSIS IR mit einem virtuellen Netzwerk

Nachdem Sie ein klassisches virtuelles Netzwerk oder ein virtuelles Azure Resource Manager-Netzwerk konfiguriert haben, können Sie Azure-SSIS IR mit dem virtuellen Netzwerk verknüpfen.

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit unterstützen nur diese Webbrowser die ADF-Benutzeroberfläche. 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Menü auf der linken Seite **Data Factorys** aus. Wenn **Data Factorys** nicht im Menü angezeigt wird, wählen Sie **Weitere Dienste** und dann im Abschnitt **INTELLIGENCE + ANALYSE** die Option **Data Factorys** aus. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png" alt-text="Liste von Data Factorys":::

1. Wählen Sie ADF mit Azure-SSIS IR in der Liste aus. Ihre ADF-Startseite wird angezeigt. Wählen Sie die Kachel **Erstellen und überwachen** aus. Die ADF-Benutzeroberfläche wird auf einer separaten Registerkarte angezeigt. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png" alt-text="Data Factory-Startseite":::

1. Wechseln Sie auf der ADF-Benutzeroberfläche zur Registerkarte **Bearbeiten**, wählen Sie **Verbindungen** aus, und wechseln Sie zur Registerkarte **Integration Runtimes**. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png" alt-text="Registerkarte &quot;Integration Runtimes&quot;":::

1. Wenn Ihre Azure-SSIS IR ausgeführt wird, wählen Sie in der Liste **Integration Runtimes** in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Beenden** aus. Sie können Ihre Azure-SSIS IR erst bearbeiten, wenn sie beendet wurde. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png" alt-text="Beenden der IR":::

1. Wählen Sie in der Liste **Integration Runtimes** in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Bearbeiten** aus. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png" alt-text="Bearbeiten der Integration Runtime":::

1. Navigieren Sie im Bereich **Integration Runtime-Setup** durch die Seiten **Allgemeine Einstellungen** und **Bereitstellungseinstellungen**, indem Sie die Schaltfläche **Weiter** auswählen.

1. Führen Sie auf der Seite **Erweiterte Einstellungen** die folgenden Schritte aus:

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="Erweiterte Einstellungen mit einem virtuellen Netzwerk":::

   1. Aktivieren Sie das Kontrollkästchen **VNET für die Einbindung Ihrer Azure-SSIS Integration Runtime-Instanz auswählen, Erstellung bestimmter Netzwerkressourcen für ADF ermöglichen und optional eigene statische öffentliche IP-Adressen verwenden**. 

   1. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Ihr virtuelles Netzwerk enthält.

   1. Für **Standort** ist derselbe Standort wie für Ihre Integration Runtime ausgewählt.

   1. Wählen Sie für **Typ** den Typ Ihres virtuellen Netzwerks aus: **Virtuelles Azure Resource Manager-Netzwerk**/klassisches virtuelles Netzwerk. Es wird empfohlen, **Virtuelles Azure Resource Manager-Netzwerk** auszuwählen, da das klassische virtuelle Netzwerk bald veraltet sein wird.

   1. Wählen Sie unter **VNET-Name** den Namen des virtuellen Netzwerks aus. Es sollte derselbe sein, der zum Konfigurieren eines Dienstendpunkts/privaten Endpunkts eines virtuellen Netzwerks für Ihren Azure SQL-Datenbank-Server verwendet wird, der SSISDB hostet. Alternativ sollte es derselbe sein, der mit Ihrer Azure SQL Managed Instance-Instanz, die SSISDB hostet, verknüpft wurde. Oder er sollte mit dem Namen des Netzwerks übereinstimmen, das mit Ihrem lokalen Netzwerk verbunden ist. Andernfalls kann es sich um ein beliebiges virtuelles Netzwerk handeln, damit Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwendet werden.

   1. Wählen Sie unter **Subnetzname** den Namen des Subnetzes für Ihr virtuelles Netzwerk aus. Es sollte derselbe sein, der zum Konfigurieren eines Dienstendpunkts für Ihren Azure SQL-Datenbank-Server, der SSISDB hostet, verwendet wird. Alternativ sollte es sich um ein anderes Subnetz handeln als das, mit dem Ihre Azure SQL Managed Instance-Instanz verknüpft ist, die SSISDB hostet. Andernfalls kann es sich um ein beliebiges Subnetz handeln, damit Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwendet werden.

   1. Wählen Sie für **VNet-Injektionsmethode** die Injektionsmethode Ihres virtuellen Netzwerks aus: **Express**/**Standard**. 
   
      Informationen zum Vergleichen dieser Methoden finden Sie im Artikel [Vergleichen der Standard- und Expressinjektionsmethoden virtueller Netzwerke](azure-ssis-integration-runtime-virtual-network-configuration.md#compare) 
   
      Wenn Sie **Express** auswählen, lesen Sie den Artikel [Express-Injektionsmethode virtueller Netzwerke](azure-ssis-integration-runtime-express-virtual-network-injection.md). 
      
      Wenn Sie **Standard** auswählen, lesen Sie den Artikel [Standard-Injektionsmethode virtueller Netzwerke](azure-ssis-integration-runtime-standard-virtual-network-injection.md).  Mit dieser Methode können Sie auch:

      1. Das Kontrollkästchen **Bring static public IP addresses for your Azure-SSIS Integration Runtime** (Statische öffentliche IP-Adressen für Azure-SSIS Integration Runtime verwenden) aktivieren, um auszuwählen, ob Sie Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwenden möchten, damit Sie sie in der Firewall Ihrer Datenspeicher zulassen können.

         Führen Sie die unten angegebenen Schritte aus, falls Sie das Kontrollkästchen aktivieren.

         1. Wählen Sie für **Erste statische öffentliche IP-Adresse** die erste statische öffentliche IP-Adresse aus, die [den Anforderungen für Ihre Azure-SSIS IR entspricht](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip). Wenn Sie über keine IP-Adresse verfügen, klicken Sie auf den Link **Neu erstellen**, um statische öffentliche IP-Adressen im Azure-Portal zu erstellen, und klicken Sie dann auf die Schaltfläche „Aktualisieren“, damit Sie diese auswählen können.
      
         1. Wählen Sie für **Zweite statische öffentliche IP-Adresse** die zweite statische öffentliche IP-Adresse aus, die [den Anforderungen für Ihre Azure-SSIS IR entspricht](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip). Wenn Sie über keine IP-Adresse verfügen, klicken Sie auf den Link **Neu erstellen**, um statische öffentliche IP-Adressen im Azure-Portal zu erstellen, und klicken Sie dann auf die Schaltfläche „Aktualisieren“, damit Sie diese auswählen können.

   1. Wählen Sie **VNET-Überprüfung** aus. Wenn die Überprüfung erfolgreich war, wählen Sie **Weiter** aus. 

1. Überprüfen Sie auf der Seite **Zusammenfassung** alle Einstellungen für Azure-SSIS IR, und wählen Sie dann die Schaltfläche **Aktualisieren** aus.

1. Starten Sie Azure-SSIS IR, indem Sie in der Spalte **Aktionen** für Azure-SSIS IR die Schaltfläche **Starten** auswählen. Es dauert ungefähr 20-30 Minuten, bis die Azure-SSIS IR-Instanz gestartet wird, die ein virtuelles Netzwerk mit einer Express-Injektionsmethode verbindet. 

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren eines virtuellen Netzwerks zur Injektion von Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Express-Injektionsmethode virtueller Netzwerke](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Standard-Injektionsmethode virtueller Netzwerke](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Verknüpfen von Azure-SSIS IR mit einem virtuellen Netzwerk über Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](tutorial-deploy-ssis-packages-azure.md). Dieses Tutorial enthält ausführliche Anweisungen zum Erstellen einer Azure-SSIS IR. Es verwendet den Azure SQL-Datenbank-Server, um SSISDB zu hosten. 
- [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dieser Artikel baut auf dem Tutorial auf. Er enthält Anweisungen zum Verwenden eines Azure SQL-Datenbank-Servers, der mit einem Endpunkt eines virtuellen Netzwerks/einer IP-Firewallregel/einem privaten Endpunkt oder einer Azure SQL Managed Instance-Instanz konfiguriert ist, die mit einem virtuellen Netzwerk zum Hosten von SSISDB verknüpft wird. Außerdem wird veranschaulicht, wie Sie Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen und verstehen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS Integration Runtime aufskalieren, indem Sie weitere Knoten hinzufügen.
