---
title: Installieren und Konfigurieren der Microsoft Azure-Diagnoseerweiterung (WAD)
description: Erfahren Sie mehr über das Installieren und Konfigurieren der Windows-Diagnoseerweiterung. Außerdem wird beschrieben, wie die Daten in einem Azure Storage-Konto gespeichert werden.
services: azure-monitor
author: bwren
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6fb9698b4f2f20fb4fa527bbd68cc2b4c352d5f5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663495"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Installieren und Konfigurieren der Microsoft Azure-Diagnoseerweiterung (WAD)
Die [Azure-Diagnoseerweiterung](diagnostics-extension-overview.md) ist ein Agent in Azure Monitor, der Überwachungsdaten vom Gastbetriebssystem und Workloads von virtuellen Azure-Computern und anderen Computeressourcen sammelt. Dieser Artikel enthält ausführliche Informationen zum Installieren und Konfigurieren der Windows-Diagnoseerweiterung sowie eine Beschreibung der Speicherung von Daten in einem Azure Storage-Konto.

Die Diagnoseerweiterung wird als [VM-Erweiterung](../../virtual-machines/extensions/overview.md) in Azure implementiert, sodass die gleichen Installationsoptionen mit Resource Manager-Vorlagen, PowerShell und der Befehlszeilenschnittstelle unterstützt werden. Weitere Informationen finden Sie unter [Erweiterungen und Features für virtuelle Computer für Windows](../../virtual-machines/extensions/features-windows.md).

## <a name="overview"></a>Übersicht
Wenn Sie die Windows Azure-Diagnoseerweiterung konfigurieren, müssen Sie ein Speicherkonto angeben, an das alle angegebenen Daten gesendet werden sollen. Sie können optional eine oder mehrere *Datensenken* hinzufügen, um die Daten an verschiedene Speicherorte zu senden.

- Azure Monitor-Senke: Hiermit werden Gastleistungsdaten an Azure Monitor-Metriken gesendet.
- Event Hub-Senke: Hiermit werden Gastleistungs- und Protokolldaten an Azure Event Hubs gesendet, um außerhalb von Azure weitergeleitet zu werden. Diese Senke kann im Azure-Portal nicht konfiguriert werden.


## <a name="install-with-azure-portal"></a>Installieren über das Azure-Portal
Sie können die Diagnoseerweiterung auf einem einzelnen virtuellen Computer im Azure-Portal installieren und konfigurieren. Bei dieser Methode können Sie eine Oberfläche verwenden, anstatt direkt mit der Konfiguration zu arbeiten. Wenn Sie die Diagnoseerweiterung aktivieren, wird automatisch eine Standardkonfiguration mit den gängigsten Leistungsindikatoren und Ereignissen verwendet. Sie können diese Standardkonfiguration entsprechend Ihren spezifischen Anforderungen ändern.

> [!NOTE]
> Im Folgenden werden die gängigsten Einstellungen für die Diagnoseerweiterung beschrieben. Ausführliche Informationen zu allen Konfigurationsoptionen finden Sie unter [Schema der Diagnoseerweiterung für Windows](diagnostics-extension-schema-windows.md).

1. Öffnen Sie das Menü für einen virtuellen Computer im Azure-Portal.

2. Klicken Sie im Abschnitt **Überwachung** des VM-Menüs auf **Diagnoseeinstellungen**.

3. Klicken Sie auf **Überwachung auf Gastebene aktivieren**, wenn die Diagnoseerweiterung noch nicht aktiviert wurde.

   ![Aktivieren der Überwachung](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. Für den virtuellen Computer wird ein neues Azure Storage-Konto erstellt, dessen Name auf dem Namen der Ressourcengruppe für die VM basiert. Außerdem wird ein Standardsatz von Gastleistungsindikatoren und Protokollen ausgewählt.

   ![Diagnoseeinstellungen](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. Wählen Sie auf der Registerkarte **Leistungsindikatoren** die Gastmetriken aus, die Sie von diesem virtuellen Computer sammeln möchten. Verwenden Sie die Einstellung **Benutzerdefiniert**, um eine erweiterte Auswahl zu treffen.

   ![Leistungsindikatoren](media/diagnostics-extension-windows-install/performance-counters.png)

6. Wählen Sie auf der Registerkarte **Protokolle** die Protokolle aus, die vom virtuellen Computer gesammelt werden sollen. Protokolle können an Storage oder Event Hubs gesendet werden, aber nicht an Azure Monitor. Verwenden Sie den [Log Analytics-Agent](../agents/log-analytics-agent.md), um Gastprotokolle für Azure Monitor zu sammeln.

   ![Screenshot der Registerkarte „Protokolle“ mit verschiedenen ausgewählten Protokollen für einen virtuellen Computer](media/diagnostics-extension-windows-install/logs.png)

7. Geben Sie auf der Registerkarte **Absturzabbilder** die Prozesse zum Sammeln von Speicherabbildern nach einem Absturz an. Die Daten werden in das Speicherkonto für die Diagnoseeinstellung geschrieben, und Sie können optional einen Blobcontainer angeben.

   ![Absturzabbilder](media/diagnostics-extension-windows-install/crash-dumps.png)

8. Geben Sie auf der Registerkarte **Senken** an, ob die Daten an andere Speicherorte als Azure Storage gesendet werden sollen. Wenn Sie **Azure Monitor** auswählen, werden Gastleistungsdaten an Azure Monitor-Metriken gesendet. Die Event Hub-Senke kann nicht mit dem Azure-Portal konfiguriert werden.

   ![Screenshot der Registerkarte „Senken“ mit aktivierter Option „Diagnosedaten an Azure Monitor senden“](media/diagnostics-extension-windows-install/sinks.png)
   
   Wenn Sie keine systemseitig zugewiesene Identität aktiviert haben, die für den virtuellen Computer konfiguriert ist, wird möglicherweise die folgende Warnung angezeigt, sobald Sie eine Konfiguration mit der Azure Monitor-Senke speichern. Klicken Sie auf das Banner, um die systemseitig zugewiesene Identität zu aktivieren.
   
   ![Verwaltete Entität](media/diagnostics-extension-windows-install/managed-entity.png)

9. Im **Agent** können Sie das Speicherkonto ändern, das Datenträgerkontingent festlegen und angeben, ob Diagnoseinfrastrukturprotokolle gesammelt werden sollen.  

   ![Screenshot der Registerkarte „Agent“ mit der Option zum Festlegen des Speicherkontos](media/diagnostics-extension-windows-install/agent.png)

10. Klicken Sie zum Speichern der Konfiguration auf **Speichern**. 

> [!NOTE]
> Obwohl die Konfiguration für die Diagnoseerweiterung in JSON oder XML formatiert werden kann, wird jede im Azure-Portal ausgeführte Konfiguration immer als JSON gespeichert. Wenn Sie bei einer anderen Konfigurationsmethode XML verwenden und dann die Konfiguration über das Azure-Portal ändern, werden die Einstellungen in JSON geändert. Außerdem gibt es keine Option zum Einrichten des Aufbewahrungszeitraums für diese Protokolle.

## <a name="resource-manager-template"></a>Resource Manager-Vorlage
Informationen zum Bereitstellen der Diagnoseerweiterung mit Azure Resource Manager-Vorlagen finden Sie unter [Verwenden von Überwachung und Diagnose bei einer Windows-VM und Azure Resource Manager-Vorlagen](../../virtual-machines/extensions/diagnostics-template.md). 

## <a name="azure-cli-deployment"></a>Bereitstellung mithilfe der Azure-Befehlszeilenschnittstelle
Sie können die Azure-Diagnoseerweiterung mithilfe der Azure-Befehlszeilenschnittstelle über [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) auf einem vorhandenen virtuellen Computer bereitstellen, wie im folgenden Beispiel erläutert. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Die geschützten Einstellungen werden im [PrivateConfig-Element](diagnostics-extension-schema-windows.md#privateconfig-element) des Konfigurationsschemas definiert. Im Folgenden finden Sie ein minimales Beispiel für eine geschützte Einstellungsdatei, die das Speicherkonto definiert. Ausführliche Informationen zu den privaten Einstellungen finden Sie unter [Beispielkonfiguration](diagnostics-extension-schema-windows.md#privateconfig-element).

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

Die öffentlichen Einstellungen werden im [Public-Element](diagnostics-extension-schema-windows.md#publicconfig-element) des Konfigurationsschemas definiert. Im Folgenden finden Sie ein minimales Beispiel für eine öffentliche Einstellungsdatei, die die Sammlung von Diagnoseinfrastruktur-Protokollen, eines einzelnen Leistungsindikators und eines einzelnen Ereignisprotokolls ermöglicht. Ausführliche Informationen zu den öffentlichen Einstellungen finden Sie unter [Beispielkonfiguration](diagnostics-extension-schema-windows.md#publicconfig-element).

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell-Bereitstellung
Sie können die Azure-Diagnoseerweiterung mithilfe von PowerShell über [Set-AzVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) auf einem vorhandenen virtuellen Computer bereitstellen, wie im folgenden Beispiel erläutert. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Die privaten Einstellungen werden im [PrivateConfig-Element](diagnostics-extension-schema-windows.md#privateconfig-element) des Konfigurationsschemas definiert, während die öffentlichen Einstellungen im [Public-Element](diagnostics-extension-schema-windows.md#publicconfig-element) definiert sind. Sie können die Details des Speicherkontos auch als Parameter des Cmdlets Set-AzVMDiagnosticsExtension angeben, anstatt sie in die privaten Einstellungen einzubeziehen.

Im Folgenden finden Sie ein minimales Beispiel für eine Konfigurationsdatei, die die Sammlung von Diagnoseinfrastruktur-Protokollen, eines einzelnen Leistungsindikators und eines einzelnen Ereignisprotokolls ermöglicht. Ausführliche Informationen zu den privaten und öffentlichen Einstellungen finden Sie unter [Beispielkonfiguration](diagnostics-extension-schema-windows.md#publicconfig-element). 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
                "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
                },
                "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "PerformanceCounterConfiguration": [
                        {
                            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                            "sampleRate": "PT3M",
                            "unit": "percent"
                        }
                    ]
                },
                "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                        "DataSource": [
                        {
                            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                        }
                    ]
                }
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Informationen finden Sie außerdem unter [Aktivieren der Azure-Diagnose auf einer virtuellen Azure-Maschine unter Windows mithilfe von PowerShell](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Datenspeicher
In der nachstehenden Tabelle sind die unterschiedlichen Arten von Daten aufgelistet, die von der Diagnoseerweiterung gesammelt werden, sowie ob sie als Tabelle oder Blob gespeichert werden. Die in Tabellen gespeicherten Daten können auch in Blob Storage gespeichert werden, abhängig von der [StorageType-Einstellung](diagnostics-extension-schema-windows.md#publicconfig-element) in Ihrer öffentlichen Konfiguration.


| Daten | Speichertyp | BESCHREIBUNG |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabelle | Diagnosemonitor und Konfigurationsänderungen |
| WADDirectoriesTable | Tabelle | Vom Diagnosemonitor überwachte Verzeichnisse.  Dies umfasst IIS-Protokolle, Protokolle zu IIS-Anforderungsfehlern und benutzerdefinierte Verzeichnisse.  Der Speicherort der Blob-Protokolldatei ist im Feld „Container“ festgelegt, ihr Name im Feld „RelativePath“.  Das Feld „AbsolutePath“ gibt den Speicherort und Namen der Datei wie auf dem virtuellen Azure-Computer vorhanden an. |
| WadLogsTable | Tabelle | Mit dem Ablaufverfolgungslistener in Code erstellte Protokolle. |
| WADPerformanceCountersTable | Tabelle | Leistungsindikatoren. |
| WADWindowsEventLogsTable | Tabelle | Windows-Ereignisprotokolle. |
| wad-iis-failedreqlogfiles | Blob | Enthält Informationen aus den Protokollen zu IIS-Anforderungsfehlern. |
| wad-iis-logfiles | Blob | Enthält Informationen zu IIS-Protokollen. |
| "custom" | Blob | Ein benutzerdefinierter Container basierend auf Konfigurationsverzeichnissen, die vom Diagnosemonitor überwacht werden.  Der Name dieses Blob-Containers wird in WADDirectoriesTable festgelegt. |

## <a name="tools-to-view-diagnostic-data"></a>Tools zum Anzeigen von Diagnosedaten
Mehrere Tools stehen zum Anzeigen der Daten nach der Übertragung an den Speicher zur Verfügung. Beispiel:

* Server-Explorer in Visual Studio – wenn Sie die Azure-Tools für Microsoft Visual Studio installiert haben, können Sie den Azure-Speicherknoten im Server-Explorer zum Anzeigen schreibgeschützter Blob- und Tabellendaten aus Ihren Azure-Speicherkonten verwenden. Sie können Daten aus dem lokalen Speicheremulatorkonto und auch aus den für Azure erstellten Speicherkonten anzeigen. Weitere Informationen finden Sie unter [Durchsuchen und Verwalten von Speicherressourcen mit dem Server-Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* Beim [Microsoft Azure Storage-Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) handelt es sich um eine eigenständige App, über die Sie ganz einfach mit Azure Storage-Daten arbeiten können – unter Windows, OSX und Linux.
* [Azure Management Studio](https://cerebrata.com/blog/introducing-azure-management-studio-and-azure-explorer) enthält Azure Diagnostics Manager, mit dem Sie die Diagnosedaten anzeigen, herunterladen und verwalten können, die von den auf Azure ausgeführten Anwendungen gesammelt werden.

## <a name="next-steps"></a>Nächste Schritte
- Ausführliche Informationen zum Weiterleiten von Überwachungsdaten an Azure Event Hubs finden Sie unter [Senden von Daten von der Microsoft Azure-Diagnoseerweiterung an Event Hubs](diagnostics-extension-stream-event-hubs.md).
