---
title: Verwenden von Azure PowerShell zum Erstellen einer Service Bus-Warteschlange
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie mit Azure PowerShell einen Service Bus-Namespace und darin eine Warteschlange erstellen.
author: spelluru
ms.author: spelluru
ms.date: 09/28/2021
ms.topic: quickstart
ms.devlang: dotnet
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: f98bf871abf50e3164f0ea9ca281887e29abfba9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211751"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Verwenden von Azure PowerShell zum Erstellen eines Service Bus-Namespace und einer Warteschlange
In dieser Schnellstartanleitung wird veranschaulicht, wie Sie mit Azure PowerShell einen Service Bus-Namespace und eine Warteschlange erstellen. Darüber hinaus wird beschrieben, wie Sie Anmeldeinformationen für die Autorisierung abrufen, die von einer Clientanwendung zum Senden bzw. Empfangen von Nachrichten für eine Warteschlange genutzt werden können. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung benötigen Sie ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto][] erstellen, bevor Sie beginnen. 

In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell. Diesen Dienst können Sie nach der Anmeldung beim Azure-Portal starten. Ausführliche Informationen zu Azure Cloud Shell finden Sie unter [Übersicht über Azure Cloud Shell](../cloud-shell/overview.md). Sie können auch Azure PowerShell auf Ihrem Computer [installieren](/powershell/azure/install-Az-ps) und verwenden. 


## <a name="provision-resources"></a>Bereitstellen von Ressourcen
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Starten Sie Azure Cloud Shell über das in der folgenden Abbildung gezeigte Symbol: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell starten":::
3. Wechseln Sie im unteren Cloud Shell-Fenster von **Bash** zu **PowerShell**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Wechseln zum PowerShell-Modus":::    
4. Führen Sie den folgenden Befehl aus, um eine Azure-Ressourcengruppe zu erstellen. Aktualisieren Sie ggf. den Namen der Ressourcengruppe und des Standorts. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Führen Sie den folgenden Befehl aus, um einen Service Bus-Messagingnamespace zu erstellen. In diesem Beispiel ist `ContosoRG` die Ressourcengruppe, die Sie im vorherigen Schritt erstellt haben. `ContosoSBusNS` ist der Name des Service Bus-Namespace, der in dieser Ressourcengruppe erstellt wurde. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Führen Sie Folgendes aus, um eine Warteschlange in dem Namespace zu erstellen, den Sie im vorherigen Schritt erstellt haben. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Rufen Sie die primäre Verbindungszeichenfolge für den Namespace ab. Sie verwenden diese Verbindungszeichenfolge, um eine Verbindung mit der Warteschlange herzustellen und Nachrichten zu senden und zu empfangen. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
    ```

    Notieren Sie sich die Verbindungszeichenfolge und den Namen der Warteschlange. Sie verwenden diese Angaben zum Senden und Empfangen von Nachrichten. 


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie einen Service Bus-Namespace und darin dann eine Warteschlange erstellt. Informationen zum Senden bzw. Empfangen von Nachrichten für die Warteschlange finden Sie in einer der folgenden Schnellstartanleitungen im Abschnitt **Senden und Empfangen von Nachrichten**. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)

[kostenloses Konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
