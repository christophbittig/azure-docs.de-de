---
title: Konfigurieren von Azure Functions mit einem virtuellen Netzwerk
description: Dieser Artikel zeigt Ihnen, wie Sie bestimmte virtuelle Netzwerkaufgaben für Azure Functions durchführen können.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: db0567456156f8ea74ba048e991000b57ae271b2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332381"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Konfigurieren von Azure Functions mit einem virtuellen Netzwerk

In diesem Artikel erfahren Sie, wie Sie Aufgaben im Zusammenhang mit der Konfiguration Ihrer Funktions-App zum Herstellen einer Verbindung und zum Ausführen in einem virtuellen Netzwerk ausführen. Ein ausführliches Tutorial zum Schützen Ihres Speicherkontos finden Sie unter [Tutorial: Integrieren von Azure Functions in ein virtuelles Azure-Netzwerk mithilfe privater Endpunkte](functions-create-vnet.md). Weitere Informationen zu Azure Functions und Netzwerken erhalten Sie unter [ Netzwerkoptionen von Azure Functions](functions-networking-options.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Einschränken Ihres Speicherkontos auf ein virtuelles Netzwerk 

Beim Erstellen einer Funktions-App müssen Sie ein allgemeines Azure Storage-Konto erstellen oder verknüpfen, das Blob-, Queue- und Table Storage unterstützt. Sie können dieses Speicherkonto durch eines ersetzen, das mit Dienstendpunkten oder privaten Endpunkten geschützt ist. Beim Konfigurieren Ihres Speicherkontos mit privaten Endpunkten wird der öffentliche Zugriff auf Ihre Funktions-App automatisch deaktiviert, und auf Ihre Funktions-App kann nur über das virtuelle Netzwerk zugegriffen werden. 

> [!NOTE]  
> Dieses Feature kann derzeit für alle von virtuellen Windows-Netzwerken unterstützten SKUs im Plan „Dedicated“ (App Service) und für Windows-Pläne vom Typ „Elastisch Premium“ verwendet werden. ASEv3 wird noch nicht unterstützt. Es wird auch bei privatem DNS für von virtuellen Linux-Netzwerken unterstützte SKUs unterstützt. Nutzung und benutzerdefiniertes DNS für Linux-Pläne werden nicht unterstützt. 

Einrichten einer Funktion mit einem auf ein privates Netzwerk beschränkten Speicherkonto:

1. Erstellen Sie eine Funktion mit einem Speicherkonto, für das keine Dienstendpunkte aktiviert sind.

1. Konfigurieren Sie die Funktion so, dass eine Verbindung zum virtuellen Netzwerk hergestellt wird.

1. Erstellen oder konfigurieren Sie ein anderes Speicherkonto.  Dabei handelt es sich um das Speicherkonto, in dem die Dienstendpunkte abgesichert und die Verbindung zur Funktion hergestellt werden sollen.

1. [Erstellen Sie eine Dateifreigabe](../storage/files/storage-how-to-create-file-share.md#create-a-file-share) im abgesicherten Speicherkonto.

1. Aktivieren Sie Dienstendpunkte oder einen privaten Endpunkt für das Speicherkonto.  
    * Wenn Sie private Endpunktverbindungen verwenden, benötigt das Speicherkonto einen privaten Endpunkt für die `file` und `blob` Unterressourcen.  Wenn bestimmte Funktionen wie Durable Functions verwendet werden, muss der Zugriff auf `queue` und `table` außerdem über eine Verbindung mit einem privaten Endpunkt möglich sein.
    * Wenn Sie Dienstendpunkte verwenden, aktivieren Sie das für ihre Funktions-Apps dedizierte Subnetz für Speicherkonten.

1. Kopieren Sie die Datei und den Blobinhalt aus dem Speicherkonto der Funktions-App in das geschützte Speicherkonto und die Dateifreigabe.

1. Kopieren Sie die Verbindungszeichenfolge für dieses Speicherkonto.

1. Aktualisieren Sie die **Anwendungseinstellungen** unter **Konfiguration** für die Funktions-App folgendermaßen:

    | Einstellungsname | Wert | Comment |
    |----|----|----|
    | `AzureWebJobsStorage`| Speicherverbindungszeichenfolge | Dies ist die Verbindungszeichenfolge für ein gesichertes Speicherkonto. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Speicherverbindungszeichenfolge | Dies ist die Verbindungszeichenfolge für ein gesichertes Speicherkonto. |
    | `WEBSITE_CONTENTSHARE` | Dateifreigabe | Der Name der Dateifreigabe, die im gesicherten Speicherkonto erstellt wurde und in der sich die Projektbereitstellungsdateien befinden. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Der Wert 1 ermöglicht die Skalierung Ihrer Funktions-App, wenn Sie Ihr Speicherkonto auf ein virtuelles Netzwerk beschränken. Sie sollten diese Einstellung aktivieren, wenn Sie Ihr Speicherkonto auf ein virtuelles Netzwerk einschränken. |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Erzwingt den gesamten ausgehenden Datenverkehr über das virtuelle Netzwerk. Erforderlich, wenn das Speicherkonto private Endpunktverbindungen verwendet. |

1. Wählen Sie **Speichern**, um die Anwendungseinstellungen zu speichern. Das Ändern von App-Einstellungen führt zu einem Neustart der App.  

Nachdem die Funktions-App neu gestartet wurde, ist sie nun mit einem gesicherten Speicherkonto verbunden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Netzwerkoptionen von Azure Functions](functions-networking-options.md)
