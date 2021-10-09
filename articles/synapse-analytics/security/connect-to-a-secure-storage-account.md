---
title: Herstellen einer Verbindung mit einem sicheren Speicherkonto über Ihren Azure Synapse-Arbeitsbereich
description: In diesem Artikel erfahren Sie, wie Sie eine Verbindung mit einem sicheren Speicherkonto über Ihren Azure Synapse-Arbeitsbereich herstellen.
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: seshin
ms.reviewer: jrasnick
ms.openlocfilehash: 103aaa913511c8d61e8e2a28ede81973fca98d1a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774638"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Herstellen einer Verbindung mit einem sicheren Azure-Speicherkonto über Ihren Synapse-Arbeitsbereich

In diesem Artikel erfahren Sie, wie Sie eine Verbindung mit einem sicheren Azure-Speicherkonto über Ihren Azure Synapse-Arbeitsbereich herstellen. Sie können ein Azure-Speicherkonto mit Ihrem Synapse-Arbeitsbereich verknüpfen, wenn Sie den Arbeitsbereich erstellen. Nachdem Sie den Arbeitsbereich erstellt haben, können Sie weitere Speicherkonten verknüpfen.


## <a name="secured-azure-storage-accounts"></a>Geschützte Azure-Speicherkonten
Azure-Speicher bietet ein mehrschichtiges Sicherheitsmodell, mit dem Sie den Zugriff auf Ihre Speicherkonten schützen und steuern können. Sie können IP-Firewallregeln konfigurieren, um Datenverkehr von ausgewählten öffentlichen IP-Adressbereichen Zugriff auf Ihr Speicherkonto zu gewähren. Sie können auch Netzwerkregeln konfigurieren, um Datenverkehr von ausgewählten virtuellen Netzwerken Zugriff auf Ihr Speicherkonto zu gewähren. Sie können IP-Firewallregeln, die den Zugriff von ausgewählten IP-Adressbereichen zulassen, und Netzwerkregeln, die Zugriff von ausgewählten virtuellen Netzwerken auf dasselbe Speicherkonto gewähren, kombinieren. Diese Regeln gelten für den öffentlichen Endpunkt eines Speicherkontos. Sie benötigen keine Zugriffsregeln, um den Datenverkehr von verwalteten privaten Endpunkten, die in Ihrem Arbeitsbereich erstellt wurden, zu einem Speicherkonto zuzulassen. Speicherfirewallregeln können auf vorhandene Speicherkonten angewendet werden, oder auf neue Speicherkonten, während Sie diese erstellen. Weitere Informationen zum Schützen Ihres Speicherkontos finden Sie [hier](../../storage/common/storage-network-security.md).

## <a name="synapse-workspaces-and-virtual-networks"></a>Synapse-Arbeitsbereiche und virtuelle Netzwerke
Wenn Sie einen Synapse-Arbeitsbereich erstellen, können Sie auswählen, dass diesem ein verwaltetes virtuelles Netzwerk zugeordnet wird. Wenn Sie das verwaltete virtuelle Netzwerk nicht für Ihren Arbeitsbereich aktivieren, wenn Sie es erstellen, befindet sich der Arbeitsbereich zusammen mit anderen Synapse-Arbeitsbereichen, denen kein verwaltetes virtuelles Netzwerk zugeordnet ist, in einem freigegebenen virtuellen Netzwerk. Wenn Sie beim Erstellen des Arbeitsbereichs das verwaltete virtuelle Netzwerk aktiviert haben, wird Ihr Arbeitsbereich einem dedizierten, von Azure Synapse verwalteten virtuellen Netzwerk zugeordnet. Diese virtuellen Netzwerke werden nicht in Ihrem Kundenabonnement erstellt. Daher können Sie mithilfe der oben beschriebenen Netzwerkregeln Datenverkehr von diesen virtuellen Netzwerken keinen Zugriff auf Ihr geschütztes Speicherkonto gewähren.  

## <a name="access-a-secured-storage-account"></a>Zugriff auf ein geschütztes Speicherkonto
Synapse wird in Netzwerken betrieben, die nicht in Ihre Netzwerkregeln eingeschlossen werden können. Die folgenden Schritte müssen ausgeführt werden, um den Zugriff von Ihrem Arbeitsbereich auf Ihr geschütztes Speicherkonto zu ermöglichen.

* Erstellen Sie einen Azure Synapse-Arbeitsbereich mit einem damit verbundenen verwalteten virtuellen Netzwerk und erstellen Sie verwaltete private Endpunkte zum sicheren Speicherkonto. 

    Wenn Sie Azure Portal verwenden, um Ihren Arbeitsbereich zu erstellen, können Sie das verwaltete virtuelle Netzwerk unter der Registerkarte **Networking** aktivieren, wie unten gezeigt. Wenn Sie ein verwaltetes virtuelles Netzwerk aktivieren oder Synapse feststellt, dass es sich bei dem primären Speicherkonto um ein sicheres Speicherkonto handelt, haben Sie die Möglichkeit, eine verwaltete private Endpunkt-Verbindungsanfrage für das sichere Speicherkonto zu erstellen (siehe unten). Der Inhaber des Speicherkontos muss die Verbindungsanfrage genehmigen, um die private Verbindung herzustellen. Alternativ wird Synapse diese Verbindungsanfrage genehmigen, wenn der Benutzer, der einen Apache Spark-Pool im Arbeitsbereich erstellt, über ausreichende Berechtigungen verfügt, um die Verbindungsanfrage zu genehmigen.
![Verwaltetes VNet und verwalteter privater Endpunkt aktivieren](./media/connect-to-a-secure-storage-account/enable-managed-virtual-network-managed-private-endpoint.png) 
    


* Gewähren Sie Ihrem Azure Synapse-Arbeitsbereich als vertrauenswürdigem Azure-Dienst Zugriff auf Ihr geschütztes Speicherkonto. Als vertrauenswürdiger Dienst stellt Synapse dann unter Verwendung einer strengen Authentifizierung eine sichere Verbindung mit Ihrem Speicherkonto her.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>Erstellen Sie einen Synapse-Arbeitsbereich mit einem verwalteten virtuellen Netzwerk, und erstellen Sie verwaltete private Endpunkte für Ihr Speicherkonto
Sie können die [folgenden Schritte](./synapse-workspace-managed-vnet.md) ausführen, um einen Synapse-Arbeitsbereich zu erstellen, dem ein verwaltetes virtuelles Netzwerk zugeordnet ist. Nachdem der Arbeitsbereich mit einem zugeordneten verwalteten virtuellen Netzwerk erstellt wurde, können Sie mit den [hier](./how-to-create-managed-private-endpoints.md) aufgeführten Schritte einen verwalteten privaten Endpunkt für Ihr geschütztes Speicherkonto erstellen. 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Gewähren Sie Ihrem Azure Synapse-Arbeitsbereich als vertrauenswürdigem Azure-Dienst Zugriff auf Ihr geschütztes Speicherkonto
Analytische Funktionen wie der dedizierte SQL-Pool und der serverlose SQL-Pool verwenden eine mehrinstanzenfähige Infrastruktur, die im verwalteten virtuellen Netzwerk nicht bereitgestellt wird. Damit Datenverkehr von diesen Funktionen auf das geschützte Speicherkonto zugreifen kann, müssen Sie mit den folgenden Schritten den Zugriff auf Ihr Speicherkonto basierend auf der vom System zugewiesenen verwalteten Identität des Arbeitsbereichs konfigurieren.

Navigieren Sie im Azure-Portal zu Ihrem geschützten Speicherkonto. Wählen Sie im Navigationsbereich auf der linken Seite **Netzwerk** aus. Wählen Sie im Abschnitt **Ressourceninstanzen** die Option *Microsoft.Synapse/workspaces* als **Ressourcentyp** aus, und geben Sie für **Instanzname** den Namen des Arbeitsbereichs ein. Wählen Sie **Speichern** aus.

![Speicherkonto-Netzwerkkonfiguration.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

Sie sollten jetzt in der Lage sein, über den Arbeitsbereich auf das geschützte Speicherkonto zuzugreifen.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Azure Synapse Analytics: verwaltetes virtuelles Netzwerk (Vorschauversion)](./synapse-workspace-managed-vnet.md).

Erfahren Sie mehr über [Verwaltete private Endpunkte in Synapse (Vorschau)](./synapse-workspace-managed-private-endpoints.md).