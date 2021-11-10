---
title: Verwenden privater Endpunkte zum Integrieren von Azure Functions in ein virtuelles Netzwerk
description: In diesem Tutorial wird erläutert, wie Sie eine Funktion mit einem virtuellen Azure-Netzwerk verbinden und mithilfe privater Endpunkte sperren.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: 8eaf46cdab44fcf4ebdb69564cd05f07f8f88f1b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850031"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>Tutorial: Integrieren von Azure Functions in ein virtuelles Azure-Netzwerk mithilfe privater Endpunkte

In diesem Tutorial wird erläutert, wie Sie mithilfe von Azure Functions eine Verbindung mit Ressourcen in einem virtuellen Azure-Netzwerk mithilfe privater Endpunkte herstellen. Sie erstellen eine Funktion mithilfe eines Speicherkontos, das hinter einem virtuellen Netzwerk gesperrt ist. Das virtuelle Netzwerk verwendet einen Service Bus-Warteschlangentrigger.

In diesem Tutorial gehen Sie wie folgt vor:

> [!div class="checklist"]
> * Erstellen einer Funktions-App im Premium-Plan.
> * Erstellen von Azure-Ressourcen (z. B. Service Bus-Instanz, Speicherkonto, virtuelles Netzwerk)
> * Sperren des Speicherkontos hinter einem privaten Endpunkt.
> * Sperren der Service Bus-Instanz hinter einem privaten Endpunkt
> * Bereitstellen einer Funktions-App, die sowohl Service Bus- als auch HTTP-Trigger verwendet
> * Sperren der Funktions-App hinter einem privaten Endpunkt.
> * Testen, ob die Funktions-App hinter dem virtuellen Netzwerk geschützt ist.
> * Bereinigen der Ressourcen

## <a name="create-a-function-app-in-a-premium-plan"></a>Erstellen einer Funktions-App in einem Premium-Tarif

Sie erstellen eine .NET-Funktions-App im Premium-Plan, da in diesem Tutorial C# verwendet wird. Andere Programmiersprachen werden in Windows auch unterstützt. Dieser Premium-Plan ermöglicht eine serverlose Skalierung und unterstützt gleichzeitig die Integration in ein virtuelles Netzwerk.

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.

1. Wählen Sie auf der Seite **Neu** die Option **Compute** > **Funktions-App** aus.

1. Verwenden Sie auf der Seite **Grundlagen** die folgende Tabelle, um die Einstellungen der Funktions-App zu konfigurieren.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Ihr Abonnement | Abonnement, unter dem diese neue Funktions-App erstellt wird. |
    | **[Ressourcengruppe](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Name der neuen Ressourcengruppe, in der Sie Ihre Funktions-App erstellen. |
    | **Name der Funktions-App** | Global eindeutiger Name | Der Name, der Ihre neue Funktionen-App bezeichnet Gültige Zeichen sind `a-z` (Groß-/Kleinschreibung nicht beachtet), `0-9` und `-`.  |
    |**Veröffentlichen**| Code | Wählen Sie aus, ob Sie Codedateien oder einen Docker-Container veröffentlichen. |
    | **Runtimestapel** | .NET | In diesem Tutorial wird .NET verwendet. |
    |**Region**| Bevorzugte Region | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe oder in der Nähe von anderen Diensten aus, auf die Ihre Funktionen zugreifen. |

1. Klicken Sie auf **Weiter: Hosting** aus. Geben Sie auf der Seite **Hosting** die folgenden Einstellungen ein.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
    | ------------ | ---------------- | ----------- |
    | **[Speicherkonto](../storage/common/storage-account-create.md)** |  Global eindeutiger Name |  Erstellen Sie ein Speicherkonto, das von Ihrer Funktions-App verwendet wird. Speicherkontonamen müssen eine Länge von 3 bis 24 Zeichen haben. Sie dürfen nur Ziffern und Kleinbuchstaben enthalten. Sie können auch ein vorhandenes Konto verwenden, das die [Anforderungen an das Speicherkonto](./storage-considerations.md#storage-account-requirements) erfüllen muss. |
    |**Betriebssystem**| Windows | In diesem Tutorial wird Windows verwendet. |
    | **[Plan](./functions-scale.md)** | Premium | Der Hostingplan, der definiert, wie Ihre Ressourcen der Funktionen-App zugewiesen werden Wenn Sie **Premium** wählen, wird standardmäßig ein neuer App Service-Plan erstellt. Der Standardwert für **SKU und Größe** ist **EP1**, wobei *EP* für _Elastisch Premium_ steht. Weitere Informationen finden Sie in der Liste der [Premium-SKUs](./functions-premium-plan.md#available-instance-skus).<br/><br/>Wenn Sie JavaScript-Funktionen im Rahmen eines Premium-Plans ausführen, wählen Sie eine Instanz mit weniger vCPUs aus. Weitere Informationen finden Sie unter [Auswählen von Premium-Plänen mit Einzelkern](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Klicken Sie auf **Weiter: Überwachung** aus. Geben Sie auf der Seite **Überwachung** die folgenden Einstellungen ein.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Standard | Erstellen Sie eine Application Insights-Ressource mit dem gleichen App-Namen in der nächstgelegenen unterstützten Region. Erweitern Sie diese Einstellung, wenn Sie den **Namen der neuen Ressource** ändern oder Ihre Daten an einem anderen **Standort** innerhalb einer [Azure-Geografie](https://azure.microsoft.com/global-infrastructure/geographies/) speichern müssen. |

1. Wählen Sie **Bewerten + erstellen** aus, um die App-Konfigurationsauswahl zu überprüfen.

1. Überprüfen Sie Ihre Einstellungen auf der Seite **Überprüfen und erstellen**. Klicken Sie dann auf **Erstellen**, um die Funktions-App bereitzustellen.

1. Wählen Sie oben rechts im Portal das **Benachrichtigungssymbol** aus, und achten Sie auf die Meldung **Bereitstellung erfolgreich**.

1. Wählen Sie **Zu Ressource wechseln**, um Ihre neue Funktionen-App anzuzeigen. Sie können auch die Option **An Dashboard anheften** auswählen. Wenn Sie die Funktions-App anheften, können Sie einfacher über das Dashboard auf sie zugreifen.

Glückwunsch! Sie haben Ihre neue Premium-Funktions-App erfolgreich erstellt.

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Als Nächstes erstellen Sie ein Speicherkonto, eine Service Bus-Instanz und ein virtuelles Netzwerk. 
### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Ihre virtuellen Netzwerke benötigen ein Speicherkonto, das von dem Konto getrennt ist, das Sie mit ihrer Funktions-APP erstellt haben.

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.

1. Suchen Sie auf der Seite **Neu** nach *Speicherkonto*. Klicken Sie anschließend auf **Erstellen**.

1. Verwenden Sie auf der Seite **Grundlagen** die folgende Tabelle, um die Einstellungen für das Speicherkonto zu konfigurieren. Für alle anderen Einstellungen können Sie die Standardwerte beibehalten.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. | 
    | **[Ressourcengruppe](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Die Ressourcengruppe aus, die Sie mit Ihrer Funktions-App erstellt haben. |
    | **Name** | mysecurestorage| Der Name des Speicherkontos, auf das der private Endpunkt angewendet wird. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Die Region, in der Sie Ihre Funktions-App erstellt haben. |

1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie **Erstellen** aus, wenn die Validierung erfolgreich war.

### <a name="create-a-service-bus"></a>Erstellen einer Service Bus-Instanz

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.

1. Suchen Sie auf der Seite **Neu** nach *Service Bus*. Klicken Sie anschließend auf **Erstellen**.

1. Verwenden Sie auf der Registerkarte **Grundlagen** die folgende Tabelle, um die Einstellungen für Service Bus zu konfigurieren. Für alle anderen Einstellungen können Sie die Standardwerte beibehalten.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. |
    | **[Ressourcengruppe](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Die Ressourcengruppe aus, die Sie mit Ihrer Funktions-App erstellt haben. |
    | **Namespacename** | myServiceBus| Der Name der Service Bus-Instanz, auf die der private Endpunkt angewandt wird |
    | **[Location](https://azure.microsoft.com/regions/)** | myFunctionRegion | Die Region, in der Sie Ihre Funktions-App erstellt haben. |
    | **Preisstufe** | Premium | Wählen Sie diesen Tarif aus, um private Endpunkte mit Azure Service Bus zu verwenden können. |

1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie **Erstellen** aus, wenn die Validierung erfolgreich war.

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Azure-Ressourcen werden in diesem Tutorial entweder in ein virtuelles Netzwerk integriert oder in einem virtuellen Netzwerk platziert. Sie verwenden private Endpunkte, um den Netzwerkdatenverkehr auf das virtuelle Netzwerk zu beschränken.

In diesem Tutorial werden zwei Subnetze erstellt:
- **default**: Subnetz für private Endpunkte. Private IP-Adressen werden aus diesem Subnetz vergeben.
- **functions**: Subnetz für die Integration von Azure Functions in das virtuelle Netzwerk. Dieses Subnetz wird an die Funktions-App delegiert.

Erstellen Sie das virtuelle Netzwerk, in das die Funktions-App integriert wird:

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.

1. Suchen Sie auf der Seite **Neu** nach *Virtuelles Netzwerk*. Klicken Sie anschließend auf **Erstellen**.

1. Verwenden Sie auf der Registerkarte **Grundlagen** die folgende Tabelle, um die Einstellungen für das virtuelle Netzwerk zu konfigurieren.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. | 
    | **[Ressourcengruppe](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Die Ressourcengruppe aus, die Sie mit Ihrer Funktions-App erstellt haben. |
    | **Name** | myVirtualNet| Der Name des virtuellen Netzwerks, mit dem Ihre Funktions-App eine Verbindung herstellt |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Die Region, in der Sie Ihre Funktions-App erstellt haben. |

1. Wählen Sie auf der Registerkarte **IP-Adressen** die Option **Subnetz hinzufügen** aus. Verwenden Sie die folgende Tabelle zum Konfigurieren der Subnetz-Einstellungen.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Screenshot der Konfigurationsansicht für das Erstellen eines virtuellen Netzwerks.":::

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Subnetzname** | functions | Der Name des Subnetzes, mit dem Ihre Funktions-App eine Verbindung herstellt | 
    | **Subnetzadressbereich** | 10.0.1.0/24 | Subnetzadressbereich. Beachten Sie, dass der IPv4-Adressraum in der obigen Abbildung 10.0.0.0/16 lautet. Beim Adressraum 10.1.0.0/16 würde der empfohlene Subnetzadressbereich 10.1.1.0/24 lauten. |

1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie **Erstellen** aus, wenn die Validierung erfolgreich war.

## <a name="lock-down-your-storage-account"></a>Sperren Sie Ihr Speicherkonto

Private Endpunkte in Azure werden verwendet, um über eine private IP-Adresse eine Verbindung mit bestimmten Azure-Ressourcen herzustellen. Diese Verbindung stellt sicher, dass der Netzwerkdatenverkehr auf das ausgewählte virtuelle Netzwerk beschränkt wird und der Zugriff nur für bestimmte Ressourcen möglich ist. 

Erstellen Sie mithilfe Ihres Speicherkontos die privaten Endpunkte für Azure File Storage, Azure Blob Storage und Azure Table Storage:

1. Wählen Sie im neuen Speicherkonto im Menü auf der linken Seite die Option **Netzwerk** aus.

1. Wählen Sie auf der Registerkarte **Verbindungen mit privatem Endpunkt** **Privater Endpunkt** aus.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Screenshot: Erstellen von privaten Endpunkten für das Speicherkonto.":::

1. Verwenden Sie auf der Registerkarte **Grundlagen** die in der folgenden Tabelle aufgeführten Einstellungen für den privaten Endpunkt.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. | 
    | **[Ressourcengruppe](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Wählen Sie die Ressourcengruppe aus, die Sie mit Ihrer Funktions-App erstellt haben. |
    | **Name** | file-endpoint | Der Name des privaten Endpunkts für Dateien aus Ihrem Speicherkonto. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Wählen Sie die Region aus, in der Sie Ihr Speicherkonto erstellt haben. |

1. Verwenden Sie auf der Registerkarte **Ressource** die in der folgenden Tabelle aufgeführten Einstellungen für den privaten Endpunkt.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. | 
    | **Ressourcentyp**  | Microsoft.Storage/storageAccounts | Der Ressourcentyp für Speicherkonten. |
    | **Ressource** | mysecurestorage | Das Speicherkonto, das Sie erstellt haben. |
    | **Zielunterressource** | file | Der private Endpunkt, der für Dateien aus dem Speicherkonto verwendet wird. |

1. Wählen Sie auf der Registerkarte **Konfiguration** für die Einstellung **Subnetz** die Option **Standard** aus.

1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie **Erstellen** aus, wenn die Validierung erfolgreich war. Ressourcen im virtuellen Netzwerk können nun mit Speicherdateien kommunizieren.

1. Erstellen Sie einen weiteren privaten Endpunkt für Blobs. Verwenden Sie auf der Registerkarte **Ressourcen** die in der folgenden Tabelle aufgeführten Einstellungen. Verwenden Sie für alle anderen Einstellungen die gleichen Werte, die Sie zum Erstellen des privaten Endpunkts für Dateien verwendet haben.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. | 
    | **Ressourcentyp**  | Microsoft.Storage/storageAccounts | Der Ressourcentyp für Speicherkonten. |
    | **Name** | blob-endpoint | Der Name des privaten Endpunkts für Blobs aus Ihrem Speicherkonto |
    | **Ressource** | mysecurestorage | Das Speicherkonto, das Sie erstellt haben. |
    | **Zielunterressource** | Blob | Der private Endpunkt, der für Blobs aus dem Speicherkonto verwendet wird. |
1. Erstellen Sie einen weiteren privaten Endpunkt für Tabellen. Verwenden Sie auf der Registerkarte **Ressourcen** die in der folgenden Tabelle aufgeführten Einstellungen. Verwenden Sie für alle anderen Einstellungen die gleichen Werte, die Sie zum Erstellen des privaten Endpunkts für Dateien verwendet haben.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. | 
    | **Ressourcentyp**  | Microsoft.Storage/storageAccounts | Der Ressourcentyp für Speicherkonten. |
    | **Name** | Tabellenendpunkt | Der Name des privaten Endpunkts für Blobs aus Ihrem Speicherkonto |
    | **Ressource** | mysecurestorage | Das Speicherkonto, das Sie erstellt haben. |
    | **Zielunterressource** | table | Der private Endpunkt, der für Tabellen im Speicherkonto verwendet wird. |
1. Kehren Sie nach dem Erstellen der privaten Endpunkte zum Abschnitt **Firewalls und virtuelle Netzwerke** Ihres Speicherkontos zurück.  
1. Vergewissern Sie sich, dass **Ausgewählte Netzwerke** ausgewählt ist.  Es ist nicht erforderlich, ein vorhandenes virtuelles Netzwerk hinzuzufügen.

Ressourcen im virtuellen Netzwerk können jetzt über den privaten Endpunkt mit dem Speicherkonto kommunizieren.
## <a name="lock-down-your-service-bus"></a>Sperren Ihrer Service Bus-Instanz

Erstellen Sie den privaten Endpunkt, um die Service Bus-Instanz zu sperren:

1. Wählen Sie in der neuen Service Bus-Instanz im Menü auf der linken Seite **Netzwerk** aus.

1. Wählen Sie auf der Registerkarte **Verbindungen mit privatem Endpunkt** **Privater Endpunkt** aus.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Screenshot der Navigation zu privaten Endpunkten für die Service Bus-Instanz":::

1. Verwenden Sie auf der Registerkarte **Grundlagen** die in der folgenden Tabelle aufgeführten Einstellungen für den privaten Endpunkt.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. | 
    | **[Ressourcengruppe](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Die Ressourcengruppe aus, die Sie mit Ihrer Funktions-App erstellt haben. |
    | **Name** | sb-endpoint | Der Name des privaten Endpunkts für Dateien aus Ihrem Speicherkonto. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Die Region aus, in der Sie Ihr Speicherkonto erstellt haben. |

1. Verwenden Sie auf der Registerkarte **Ressource** die in der folgenden Tabelle aufgeführten Einstellungen für den privaten Endpunkt.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. | 
    | **Ressourcentyp**  | Microsoft.ServiceBus/namespaces | Der Ressourcentyp für die Service Bus-Instanz |
    | **Ressource** | myServiceBus | Die Service Bus-Instanz, die Sie zuvor in diesem Tutorial erstellt haben. |
    | **Untergeordnete Zielressource** | Namespace | Der private Endpunkt, der für den Namespace der Service Bus-Instanz verwendet wird. |

1. Wählen Sie auf der Registerkarte **Konfiguration** für die Einstellung **Subnetz** die Option **Standard** aus.

1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie **Erstellen** aus, wenn die Validierung erfolgreich war. 
1. Kehren Sie nach dem Erstellen des privaten Endpunkts zum Abschnitt **Firewalls und virtuelle Netzwerke** Ihres Service Bus-Namespace zurück.
1. Vergewissern Sie sich, dass **Ausgewählte Netzwerke** ausgewählt ist.
1. Wählen Sie **+ Vorhandenes virtuelles Netzwerk hinzufügen** aus, um das zuvor erstellte virtuelle Netzwerk hinzuzufügen.
1. Verwenden Sie auf der Registerkarte **Netzwerke hinzufügen** die Netzwerkeinstellungen aus der folgenden Tabelle:

    | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG|
    |---------|-----------------|------------|
    | **Abonnement** | Ihr Abonnement | Das Abonnement, in dem Ihre Ressourcen erstellt werden. |
    | **Virtuelle Netzwerke** | myVirtualNet | Der Name des virtuellen Netzwerks, mit dem Ihre Funktions-App eine Verbindung herstellt |
    | **Subnetze** | functions | Der Name des Subnetzes, mit dem Ihre Funktions-App eine Verbindung herstellt |

1. Wählen Sie **Client-IP-Adresse hinzufügen** aus, um Ihrer aktuellen Client-IP-Adresse Zugriff auf den Namespace zu gewähren.
    > [!NOTE]
    > Das Zulassen Ihrer Client-IP-Adresse ist erforderlich, damit im Azure-Portal [später in diesem Tutorial Nachrichten in der Warteschlange veröffentlicht werden können](#test-your-locked-down-function-app).
1. Wählen Sie **Aktivieren** aus, um den Dienstendpunkt zu aktivieren.
1. Wählen Sie **Hinzufügen** aus, um das ausgewählte virtuelle Netzwerk und das Subnetz den Firewallregeln für die Service Bus-Instanz hinzuzufügen.
1. Wählen Sie **Speichern** aus, um die aktualisierten Firewallregeln zu speichern.

Ressourcen im virtuellen Netzwerk können jetzt über den privaten Endpunkt mit der Service Bus-Instanz kommunizieren.

## <a name="create-a-file-share"></a>Erstellen einer Dateifreigabe

1. Wählen Sie im Speicherkonto, das Sie erstellt haben, im Menü auf der linken Seite die Option **Dateifreigaben** aus.

1. Wählen Sie **+ Dateifreigaben** aus. Benennen Sie die Dateifreigabe für die Zwecke dieses Tutorials mit *Dateien*.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Screenshot: Erstellen einer Dateifreigabe im Speicherkonto":::

1. Klicken Sie auf **Erstellen**.

## <a name="get-the-storage-account-connection-string"></a>Abrufen der Verbindungszeichenfolge des Speicherkontos

1. Wählen Sie im Speicherkonto, das Sie erstellt haben, im Menü auf der linken Seite die Option **Zugriffsschlüssel** aus.

1. Wählen Sie **Schlüssel anzeigen** aus. Kopieren Sie die Verbindungszeichenfolge von **key1**, und speichern Sie sie. Sie benötigen diese Verbindungszeichenfolge, wenn Sie die App-Einstellungen konfigurieren.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Screenshot: Abrufen der Verbindungszeichenfolge für ein Speicherkonto":::

## <a name="create-a-queue"></a>Erstellen einer Warteschlange

Erstellen Sie die Warteschlange, aus der Ihr Azure Functions-Service Bus-Trigger Ereignisse abruft:

1. Wählen Sie in Ihrer Service Bus-Instanz im Menü auf der linken Seite **Warteschlangen** aus.

1. Wählen Sie **Warteschlange** aus. Geben Sie für dieses Tutorial *queue* als Namen der neuen Warteschlange an.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Screenshot der Erstellung einer Service Bus-Warteschlange":::

1. Wählen Sie **Erstellen** aus.

## <a name="get-a-service-bus-connection-string"></a>Abrufen einer Service Bus-Verbindungszeichenfolge

1. Wählen Sie in Ihrer Service Bus-Instanz im Menü auf der linken Seite die Option **Richtlinien für gemeinsamen Zugriff** aus.

1. Wählen Sie **RootManageSharedAccessKey**. Kopieren Sie die **primäre Verbindungszeichenfolge**, und speichern Sie sie. Sie benötigen diese Verbindungszeichenfolge, wenn Sie die App-Einstellungen konfigurieren.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Screenshot des Abrufens der Service Bus-Verbindungszeichenfolge":::

## <a name="integrate-the-function-app"></a>Integrieren der Funktions-App

Wenn Sie Ihre Funktions-App mit virtuellen Netzwerken verwenden möchten, müssen Sie sie mit einem Subnetz verknüpfen. Sie verwenden ein bestimmtes Subnetz für die Integration des virtuellen Netzwerks mit Azure Functions. Sie verwenden das Standardsubnetz für andere private Endpunkte, die Sie in diesem Tutorial erstellen.

1. Wählen Sie in ihrer Funktions-App im Menü auf der linken Seite die Option **Netzwerk** aus.

1. Wählen Sie unter **VNET-Integration** die Option **Zum Konfigurieren hier klicken** aus.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Screenshot: Navigation zu „VNET-Integration“.":::

1. Wählen Sie **VNET hinzufügen** aus.

1. Unter **Virtuelles Netzwerk** wählen Sie das zuvor erstellte virtuelle Netzwerk aus.

1. Wählen Sie das zuvor erstellte **Funktions**-Subnetz aus. Klicken Sie auf **OK**.  Ihre Funktions-App ist jetzt in Ihr virtuelles Netzwerk integriert.

    Wenn sich das virtuelle Netzwerk und die Funktions-App in unterschiedlichen Abonnements befinden, müssen Sie zunächst **Mitwirkender**-Zugriff für den Dienstprinzipal **Microsoft Azure App Service** im virtuellen Netzwerk gewähren.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Screenshot: Verbinden einer Funktions-App mit einem Subnetz":::

## <a name="configure-your-function-app-settings"></a>Aktualisieren der Einstellungen Ihrer Funktions-App

1. Wählen Sie in ihrer Funktions-App im Menü auf der linken Seite die Option **Konfiguration** aus.

1. Wenn Sie Ihre Funktions-App mit virtuellen Netzwerken verwenden möchten, aktualisieren Sie die in der folgenden Tabelle gezeigten App-Einstellungen. Wählen Sie **+ Neue Anwendungseinstellung** oder das Symbol **Bearbeiten** in der Spalte ganz rechts in der Tabelle mit den App-Einstellungen aus. Wählen Sie abschließend **Speichern** aus.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | Die Verbindungszeichenfolge des Speicherkontos, das Sie erstellt haben. Diese Verbindungszeichenfolge stammt aus dem Abschnitt [Beschaffen der Verbindungszeichenfolge für das Speicherkonto](#get-the-storage-account-connection-string). Mit dieser Einstellung kann Ihre Funktions-App das sichere Speicherkonto für normale Vorgänge zur Laufzeit verwenden. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | Die Verbindungszeichenfolge des Speicherkontos, das Sie erstellt haben. Mit dieser Einstellung kann Ihre Funktions-App das sichere Speicherkonto für Azure Files verwenden, was bei der Bereitstellung verwendet wird. |
    | **WEBSITE_CONTENTSHARE** | files | Der Name der Dateifreigabe, die Sie im Speicherkonto erstellt haben. Verwenden Sie diese Einstellung in Verbindung mit WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Erstellen Sie diese App-Einstellung für die Verbindungszeichenfolge Ihrer Service Bus-Instanz. Diese Verbindungszeichenfolge stammt aus dem Abschnitt [Abrufen einer Service Bus-Verbindungszeichenfolge](#get-a-service-bus-connection-string).|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Erstellen Sie diese App-Einstellung. Der Wert 1 ermöglicht das Skalieren Ihrer Funktions-App, wenn Ihr Speicherkonto auf ein virtuelles Netzwerk beschränkt ist. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Erstellen Sie diese App-Einstellung. Sobald Ihre App in ein virtuelles Netzwerk integriert ist, verwendet es den gleichen DNS-Server wie das virtuelle Netzwerk. Ihre Funktions-App benötigt diese Einstellung, damit sie zusammen mit privaten Zonen in Azure DNS funktionieren kann. Dies ist erforderlich, wenn Sie private Endpunkte verwenden. |

1. Wählen Sie in der Ansicht **Konfiguration** die Registerkarte **Einstellungen der Funktionsruntime** aus.

1. Legen Sie die **Überwachung der Runtimeskalierung** auf **Ein** fest. Klicken Sie dann auf **Speichern**. Die runtimegesteuerte Skalierung ermöglicht es Ihnen, HTTP-fremde Triggerfunktionen mit Diensten zu verbinden, die in Ihrem virtuellen Netzwerk ausgeführt werden.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Screenshot: Aktivieren der runtimegesteuerten Skalierung für Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Bereitstellen eines Service Bus- und HTTP-Triggers

1. Wechseln Sie in GitHub zum folgenden Beispiel-Repository. Es enthält eine Funktions-App und zwei Funktionen, einen HTTP-Trigger und einen Service Bus-Warteschlangentrigger.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. Wählen Sie oben auf der Seite **Verzweigen** aus, um in Ihrem eigenen GitHub-Konto oder Ihrer GitHub-Organisation eine Verzweigung dieses Repositorys zu erstellen.

1. Wählen Sie in ihrer Funktions-App im Menü auf der linken Seite die Option **Bereitstellungs-Center** aus. Wählen Sie dann **Einstellungen** aus.

1. Verwenden Sie auf der Registerkarte **Einstellungen** die in der folgenden Tabelle gezeigten Bereitstellungseinstellungen.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Quelle** | GitHub | Sie sollten ein GitHub-Repository für den Beispielcode aus Schritt 2 erstellt haben. | 
    | **Organisation**  | myOrganization | Die Organisation, in der Ihr Repository eingecheckt ist. Das ist in der Regel Ihr Konto. |
    | **Repository** | functions-vnet-tutorial | Das Repository wurde von https://github.com/Azure-Samples/functions-vnet-tutorial geforkt. |
    | **Branch** | Standard | Screenshot der Hauptverzweigung des von Ihnen erstellten Repositorys. |
    | **Runtimestapel** | .NET | Der Beispielcode ist in C# geschrieben. |
    | **Version** | .NET Core 3.1 | Die Runtimeversion |

1. Wählen Sie **Speichern** aus. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Screenshot: Bereitstellen des Azure Functions-Codes über das Portal":::

1. Die Erstbereitstellung kann mehrere Minuten in Anspruch nehmen. Wenn Ihre App erfolgreich bereitgestellt wurde, wird auf der Registerkarte **Protokolle** die Statusmeldung **Erfolgreich (aktiv)** angezeigt. Aktualisieren Sie die Seite bei Bedarf.

Glückwunsch! Sie haben Ihre Beispiel-Funktions-App erfolgreich bereitgestellt.

## <a name="lock-down-your-function-app"></a>Sperren Sie Ihre Funktions-App

Erstellen Sie nun den privaten Endpunkt zum Sperren Ihrer Funktions-App. Dieser private Endpunkt verbindet Ihre Funktions-App mithilfe einer privaten IP-Adresse über eine private und sichere Verbindung mit Ihrem virtuellen Netzwerk. 

Weitere Informationen finden Sie in der [Dokumentation zum privaten Endpunkt](../private-link/private-endpoint-overview.md).

1. Wählen Sie in ihrer Funktions-App im Menü auf der linken Seite die Option **Netzwerk** aus.

1. Wählen Sie unter **Verbindungen mit privaten Endpunkten** die Option **Verbindungen mit privaten Endpunkten konfigurieren** aus.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Screenshot: Navigation zum privaten Endpunkt einer Funktions-App.":::

1. Wählen Sie **Hinzufügen** aus.

1. Verwenden Sie im Bereich, der geöffnet wird, die folgenden Einstellungen für den privaten Endpunkt:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Screenshot: Erstellen eines privaten Endpunkts für eine Funktions-App. Der Name lautet functionapp-endpoint. Das Abonnement ist „Private Test Sub CACHHAI“. Das virtuelle Netzwerk heißt MyVirtualNet-tutorial. Das Subnetz entspricht dem Standard.":::

1. Wählen Sie **OK** aus, um den privaten Endpunkt hinzuzufügen. 
 
Glückwunsch! Sie haben Ihre Funktions-App, Ihre Service Bus-Instanz und Ihr Speicherkonto erfolgreich durch Hinzufügen von privaten Endpunkten geschützt.

### <a name="test-your-locked-down-function-app"></a>Testen der gesperrten Funktions-App

1. Wählen Sie in ihrer Funktions-App im Menü auf der linken Seite die Option **Functions** aus.

1. Wählen Sie **ServiceBusQueueTrigger** aus.

1. Klicken Sie im Menü auf der linken Seite auf **Überwachen**. 
 
Sie werden feststellen, dass Sie Ihre App nicht überwachen können. Ihr Browser hat keinen Zugriff auf das virtuelle Netzwerk hat und daher nicht direkt auf Ressourcen im virtuellen Netzwerk zugreifen. 
 
Im folgenden finden Sie eine alternative Möglichkeit zum Überwachen ihrer Funktion mit Application Insights:

1. Wählen Sie in ihrer Funktions-App im Menü auf der linken Seite **Application Insights** aus. Wählen Sie dann **Application Insights-Daten anzeigen** aus.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Screenshot: Anzeigen von Application Insights für eine Funktions-App.":::

1. Wählen Sie im Menü auf der linken Seite die Option **Livemetriken** aus.

1. Öffnen Sie eine neue Registerkarte. Wählen Sie in Ihrer Service Bus-Instanz im Menü auf der linken Seite **Warteschlangen** aus.

1. Wählen Sie Ihre Warteschlange aus.

1. Wählen Sie im Menü auf der linken Seite **Service Bus Explorer** aus. Wählen Sie unter **Senden** als **Inhaltstyp** die Option **Text/Plain** aus. Geben Sie dann eine Nachricht ein. 

1. Wählen Sie **Senden** aus, um die Nachricht zu senden.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Screenshot des Sendens von Service Bus-Nachrichten über das Portal":::

1. Auf der Registerkarte **Livemetriken** sollte angezeigt werden, dass der Service Bus-Warteschlangentrigger ausgelöst wurde. Ist dies nicht der Fall, senden Sie die Nachricht über **Service Bus Explorer** erneut.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Screenshot: Anzeigen von Nachrichten mithilfe von Livemetriken für Funktions-Apps.":::

Glückwunsch! Sie haben die Einrichtung Ihrer Funktions-App mit privaten Endpunkten erfolgreich getestet.

## <a name="understand-private-dns-zones"></a>Verstehen von privaten DNS-Zonen
Sie haben einen privaten Endpunkt zum Herstellen einer Verbindung mit Azure-Ressourcen verwendet. Sie stellen die Verbindung mit einer privaten IP-Adresse statt mit dem öffentlichen Endpunkt her. Vorhandene Azure-Dienste sind so konfiguriert, dass sie die bestehende DNS-Konfiguration zum Herstellen der Verbindung mit dem öffentlichen Endpunkt verwenden. Sie müssen die DNS-Konfiguration außer Kraft setzen, um eine Verbindung mit dem privaten Endpunkt herzustellen.

Für jede mit einem privaten Endpunkt konfigurierte Azure-Ressource wird eine private DNS-Zone erstellt. Für jede private IP-Adresse, die dem privaten Endpunkt zugeordnet ist, wird ein DNS-Eintrag erstellt.

Die folgenden DNS-Zonen wurden in diesem Tutorial erstellt:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Premium-Funktions-App, ein Speicherkonto und eine Service Bus-Instanz erstellt. Sie haben alle diese Ressourcen hinter privaten Endpunkten geschützt. 

Verwenden Sie die folgenden Links, um mehr über die Netzwerkoptionen von Azure Functions und über private Endpunkte zu erfahren:

- [Netzwerkoptionen in Azure Functions](./functions-networking-options.md)
- [Premium-Tarif für Azure Functions](./functions-premium-plan.md)
- [Private Endpunkte für Service Bus](../service-bus-messaging/private-link-service.md)
- [Private Endpunkte für Azure Storage](../storage/common/storage-private-endpoints.md)
