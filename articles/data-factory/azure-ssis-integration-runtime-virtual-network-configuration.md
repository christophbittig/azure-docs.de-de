---
title: Konfigurieren eines virtuellen Netzwerks für die Injektion von Azure-SSIS Integration Runtime
description: Hier erfahren Sie, wie Sie ein virtuelles Netzwerk für die Injektion von Azure-SSIS Integration Runtime konfigurieren.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 11/03/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e3683011794e89acbac6bbbb985c7cb85ebd9ebd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475982"
---
# <a name="configure-a-virtual-network-for-injection-of-azure-ssis-integration-runtime"></a>Konfigurieren eines virtuellen Netzwerks für die Injektion von Azure-SSIS Integration Runtime

Wenn Sie SQL Server Integration Services (SSIS) in Azure Data Factory (ADF) verwenden, gibt es zwei Methoden, um Azure-SSIS Integration Runtime (IR) mit einem virtuellen Netzwerk zu verbinden: „Standard“ und „Express“. Bei der Methode „Express“ wird Azure-SSIS IR schneller gestartet, und es sind keine Anforderungen für eingehenden und weniger Anforderungen für ausgehenden Datenverkehr vorhanden. Im Vergleich zur Methode „Standard“ gibt es jedoch einige Einschränkungen.

## <a name="compare-the-standard-and-express-virtual-network-injection-methods"></a><a name="compare"></a>Vergleich der Injektionsmethoden „Standard“ und „Express“ für virtuelle Netzwerke

In dieser Tabelle werden die Unterschiede zwischen den Injektionsmethoden „Standard“ und „Express“ für virtuelle Netzwerke hervorgehoben:

| Vergleich | Inkektionsmethode „Standard“ für virtuelle Netzwerke | Injektionsmethode „Express“ für virtuelle Netzwerke |
|------------|------------------------------------|-----------------------------------|
| **Startdauer von Azure-SSIS IR** | ca. 30 Minuten | ca. 5 Minuten | 
| **Einstellungen für das Azure-Abonnement und Ressourcengruppen** | *Microsoft.Batch* muss im Abonnement für das virtuelle Netzwerk als Ressourcenanbieter registriert sein.<br/><br/>Das Erstellen einer öffentlichen IP-Adresse, eines Lastenausgleichs und einer Netzwerksicherheitsgruppe (NSG) muss in der Ressourcengruppe des virtuellen Netzwerks zulässig sein. | *Microsoft.Batch* muss im Abonnement für das virtuelle Netzwerk als Ressourcenanbieter registriert sein. | 
| **Subnetz des virtuellen Netzwerks** | Das Subnetz muss nicht für andere Azure-Dienste dediziert sein. | Das Subnetz muss nicht für andere Azure-Dienste dediziert sein.<br/><br/>Das Subnetz muss an *Microsoft.Batch/batchAccounts* delegiert werden. | 
| **Berechtigung für virtuelle Netzwerke** | Benutzer*innen, die Azure-SSIS IR erstellen, müssen über die Berechtigung _Microsoft.Network/virtualNetworks/\*/join_ verfügen. | Benutzer*innen, die Azure-SSIS IR erstellen, müssen über die Berechtigung *Microsoft.Network/virtualNetworks/subnets/join/action* verfügen. | 
| **Statische öffentliche IP-Adressen** | (Optional) Verwenden Sie Ihre eigenen statischen öffentlichen IP-Adressen (BYOIP) für Azure-SSIS IR. | (Optional) Konfigurieren Sie die Netzwerkadressenübersetzung (Network Address Translation, NAT) für das virtuelle Netzwerk, um eine statische öffentliche IP-Adresse für Azure-SSIS IR einzurichten. | 
| **Benutzerdefinierte DNS-Server** | Es wird empfohlen, nicht aufgelöste DNS-Anforderungen an rekursive Azure-Resolver weiterzuleiten. | Es wird empfohlen, nicht aufgelöste DNS-Anforderungen an rekursive Azure-Resolver weiterzuleiten.<br/><br/>Für Azure-SSIS IR ist ein benutzerdefiniertes Standardsetup erforderlich. | 
| **Eingehender Datenverkehr** | Die *Ports 29876 und 29877* müssen mit dem Diensttag *BatchNodeManagement* als Quelle für TCP-Datenverkehr geöffnet sein. | Nicht erforderlich. | 
| **Ausgehender Datenverkehr** | Port *443* muss mit dem Diensttag *AzureCloud* als Ziel für TCP-Datenverkehr geöffnet sein. | Port *443* muss mit dem Diensttag *DataFactoryManagement* als Ziel für TCP-Datenverkehr geöffnet sein. | 
| **Ressourcensperre** | Dies ist in der Ressourcengruppe nicht zulässig. | Dies ist im virtuellen Netzwerk nicht zulässig. | 
| **Azure-SSIS-IRs pro virtuellem Netzwerk** | Unbegrenzt. | Nur eine Runtime. | 

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/standard-express-virtual-network-injection.png" alt-text="Screenshot der Injektionsmethoden „Standard“ und „Express“ für virtuelle Netzwerke" lightbox="media/join-azure-ssis-integration-runtime-virtual-network/standard-express-virtual-network-injection.png":::

Ihr virtuelles Netzwerk muss basierend auf Ihrer Injektionsmethode anders konfiguriert werden. Wenn Sie die Methode „Express“ verwenden, finden Sie weitere Informationen im Artikel [Injektionsmethode „Express“ für virtuelle Netzwerke](azure-ssis-integration-runtime-express-virtual-network-injection.md). Falls dies nicht der Fall ist, finden Sie Details im Artikel [Injektionsmethode „Standard“ für virtuelle Netzwerke](azure-ssis-integration-runtime-standard-virtual-network-injection.md).
  
## <a name="register-azure-batch-as-a-resource-provider"></a><a name="registerbatch"></a>Registrieren von Azure Batch als Ressourcenanbieter

Mit dem Azure-Portal können Sie Azure Batch, die zugrunde liegende Infrastruktur für SSIS in ADF, als Ressourcenanbieter im Azure-Abonnement registrieren, das über das virtuelle Netzwerk verfügt, dem Azure-SSIS IR beitreten kann. Wenn Sie Azure Batch bereits verwendet oder Azure-SSIS IR über die ADF-Benutzeroberfläche in diesem Abonnement erstellt haben, ist der Dienst bereits registriert. Andernfalls können Sie hierfür die folgenden Schritte ausführen:

1. Nachdem Sie Ihr virtuelles Netzwerk im Azure-Portal ausgewählt haben, wählen Sie das hervorgehobene Abonnement auf der Seite **Übersicht** aus.  

1. Klicken Sie im Menü auf der linken Seite auf **Ressourcenanbieter**.

1. Wählen Sie *Microsoft.Batch* aus, und registrieren Sie ggf. diesen Ressourcenanbieter.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="Bestätigung des Status &quot;Registriert&quot;":::

Wenn *Microsoft.Batch* nicht in der Liste angezeigt wird, können Sie in diesem Abonnement [ein leeres Azure Batch-Konto erstellen](../batch/batch-account-create-portal.md), um es später wieder zu löschen. 

## <a name="delegate-a-subnet-to-azure-batch"></a><a name="delegatesubnet"></a>Delegieren eines Subnetzes an Azure Batch

Wenn Sie die Injektionsmethode „Express“ für virtuelle Netzwerke verwenden, müssen Sie das Subnetz, in das Azure-SSIS IR eingefügt wird, an Azure Batch delegieren. Dies ist die zugrunde liegende Infrastruktur für SSIS in ADF. Mithilfe des Azure-Portal können Sie dazu die folgenden Schritte ausführen:

1. Nachdem Sie Ihr virtuelles Netzwerk im Azure-Portal ausgewählt haben, klicken Sie im Menü auf der linken Seite auf **Subnetze**.

1. Wählen Sie den Namen Ihres Subnetzes aus, um den entsprechenden Bereich zu öffnen, und stellen Sie sicher, dass IP-Adressen für mindestens das Zweifache der Anzahl Ihrer Azure-SSIS IR-Knoten verfügbar sind.

1. Wählen Sie im Dropdownmenü **Subnetz an einen Dienst delegieren** die Option *Microsoft.Batch/batchAccounts* aus.

1. Klicken Sie auf die Schaltfläche **Speichern**.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/delegate-subnet-to-batch.png" alt-text="Delegieren eines Subnetzes an Azure Batch":::

## <a name="grant-virtual-network-permissions"></a><a name="grantperms"></a>Erteilen von Berechtigungen für virtuelle Netzwerke

Mithilfe des Azure-Portals können Sie den Benutzer*innen, die Azure-SSIS IR erstellen, die erforderlichen Berechtigungen für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) erteilen, um dem virtuellen Netzwerk bzw. Subnetz beizutreten. Führen Sie dazu die folgenden Schritte aus:

1. Nachdem Sie Ihr virtuelles Netzwerk im Azure-Portal ausgewählt haben, können Sie im Menü auf der linken Seite auf **Zugriffssteuerung (IAM)** klicken, wenn Sie die Injektionsmethode „Standard“ für virtuelle Netzwerke verwenden. Wenn Sie die Injektionsmethode „Express“ für virtuelle Netzwerke verwenden, können Sie im Menü auf der linken Seite auf **Subnetze** klicken, Ihre Subnetzzeile auswählen und dann im Menü oben auf **Benutzer verwalten** klicken, um die Seite **Zugriffssteuerung** zu öffnen.

1. Klicken Sie auf die Schaltfläche **Rollenzuweisung hinzufügen**, um die Seite **Rollenzuweisung hinzufügen** zu öffnen.

1. Wählen Sie *Netzwerkmitwirkender* oder Ihre benutzerdefinierte Rolle in der Liste **Rolle** aus, und klicken Sie dann auf die Schaltfläche **Weiter**.

1. Wählen Sie im Abschnitt **Zugriff zuweisen zu** die Option **User, group, or service principal** (Benutzer, Gruppe oder Dienstprinzipal) aus.

1. Klicken Sie auf den Link **Mitglieder auswählen**, um die Benutzer*innen zu suchen und auszuwählen, die Azure-SSIS IR erstellen.

1. Klicken Sie auf die Schaltflächen **Auswählen**, **Weiter** und dann auf **Überprüfen und zuweisen**.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/grant-virtual-network-permissions.png" alt-text="Erteilen von Berechtigungen für virtuelle Netzwerke":::

## <a name="next-steps"></a>Nächste Schritte

- [Injektionsmethode „Express“ für virtuelle Netzwerke](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Injektionsmethode „Standard“ für virtuelle Netzwerke](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Verknüpfen einer Azure-SSIS IR mit einem virtuellen Netzwerk über die ADF-Benutzeroberfläche](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Verknüpfen von Azure-SSIS IR mit einem virtuellen Netzwerk über Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](tutorial-deploy-ssis-packages-azure.md). Dieses Tutorial enthält ausführliche Anweisungen zum Erstellen einer Azure-SSIS IR. Es verwendet den Azure SQL-Datenbank-Server, um SSISDB zu hosten. 
- [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dieser Artikel baut auf dem Tutorial auf. Er enthält Anweisungen zum Verwenden eines Azure SQL-Datenbank-Servers, der mit einem Endpunkt eines virtuellen Netzwerks/einer IP-Firewallregel/einem privaten Endpunkt oder einer Azure SQL Managed Instance-Instanz konfiguriert ist, die mit einem virtuellen Netzwerk zum Hosten von SSISDB verknüpft wird. Außerdem wird veranschaulicht, wie Sie Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen und verstehen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS Integration Runtime aufskalieren, indem Sie weitere Knoten hinzufügen.
