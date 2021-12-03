---
title: Log Analytics-VM-Erweiterung für Windows
description: Stellen Sie den Log Analytics-Agent mithilfe einer VM-Erweiterung auf einem virtuellen Windows-Computer bereit.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 11/02/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 41a46f2ccb925a51aec92bad94d1c1e2164745f4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312037"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Log Analytics-VM-Erweiterung für Windows

Azure Monitor-Protokolle bieten Überwachungsfunktionen für cloudbasierte und lokale Ressourcen. Die Log Analytics-Agent-VM-Erweiterung für Windows wird von Microsoft veröffentlicht und unterstützt. Die Erweiterung installiert den Log Analytics-Agent auf virtuellen Azure-Computern und registriert virtuelle Computer in einem vorhandenen Log Analytics-Arbeitsbereich. Dieses Dokument enthält ausführliche Informationen zu den unterstützten Plattformen, Konfigurationen und Bereitstellungsoptionen für die Log Analytics-VM-Erweiterung für Windows.

> [!NOTE]
> Mit Azure-Arc-fähigen Servern können Sie Log-Analytics-Agent-Erweiterungen für virtuelle Computer auf nicht in Azure gehostete Windows- und Linux-Computern bereitstellen, entfernen und aktualisieren, um die Verwaltung Ihrer Hybridcomputer über ihren gesamten Lebenszyklus zu vereinfachen. Weitere Informationen finden Sie unter [Verwaltung von Erweiterungen für virtuelle Maschinen mit Azure-Arc-fähigen Servern](../../azure-arc/servers/manage-vm-extensions.md).

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Ausführliche Informationen zu den unterstützten Windows-Betriebssystemen finden Sie im Artikel [Übersicht über Azure Monitor-Agents](../../azure-monitor/agents/agents-overview.md#supported-operating-systems).

### <a name="agent-and-vm-extension-version"></a>Version des Agents und der VM-Erweiterung

Die folgende Tabelle enthält eine Zuordnung der Version der Log Analytics-VM-Erweiterung für Windows und des Log Analytics-Agent-Pakets für jedes Release. 

| Log Analytics-Agent-Paketversion für Windows | Version der Log Analytics-VM-Erweiterung für Windows | Veröffentlichungsdatum | Versionsinformationen |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18053| 1.0.18053.0 | Oktober 2020   | <ul><li>Neue Agent-Problembehandlung</li><li>Aktualisiert die Weise, in der der Agent Zertifikatänderungen bei Azure-Diensten behandelt</li></ul> |
| 10.20.18040 | 1.0.18040.2 | August 2020   | <ul><li>Löst ein Problem in Azure Arc</li></ul> |
| 10.20.18038 | 1.0.18038 | April 2020   | <ul><li>Ermöglicht Konnektivität über Private Link mithilfe von Azure Monitor Private Link-Bereichen.</li><li>Fügt eine Drosselung bei der Erfassung hinzu, um einen versehentlichen plötzlichen Anstieg der Erfassung in einem Arbeitsbereich zu vermeiden.</li><li>Fügt Unterstützung für zusätzliche Azure Government-Clouds und -Regionen hinzu.</li><li>Behebt einen Fehler, der zum Absturz von HealthService.exe führte.</li></ul> |
| 10.20.18029 | 1.0.18029 | März 2020   | <ul><li>Fügt Unterstützung für SHA-2-Codesignaturen hinzu.</li><li>Verbessert Installation und Verwaltung von VM-Erweiterungen.</li><li>Behebt einen Fehler mit Azure-Arc-fähiger Serverintegration</li><li>Fügt ein integriertes Tool zur Problembehandlung für den Kundensupport hinzu.</li><li>Fügt Unterstützung für zusätzliche Azure Government Regionen hinzu.</li> |
| 10.20.18018 | 1.0.18018 | Oktober 2019 | <ul><li> Kleinere Fehlerbehebungen und Stabilisierungsverbesserungen </li></ul> |
| 10.20.18011 | 1.0.18011 | Juli 2019 | <ul><li> Kleinere Fehlerbehebungen und Stabilisierungsverbesserungen </li><li> Erhöhung des Werts für „MaxExpressionDepth“ auf 10.000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Juni 2019 | <ul><li> Kleinere Fehlerbehebungen und Stabilisierungsverbesserungen </li><li> Hinzugefügte Möglichkeit, Standardanmeldeinformationen zu deaktivieren, wenn eine Proxyverbindung hergestellt wird (Unterstützung für WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | März 2019 | <ul><li>Kleinere Stabilisierungskorrekturen </li></ul> |
| 10.19.10006 | – | Dezember 2018 | <ul><li> Kleinere Stabilisierungskorrekturen </li></ul> | 
| 8.0.11136 | – | September 2018 |  <ul><li> Unterstützung für die Erkennung einer Änderung der Ressourcen-ID bei der VM-Verschiebung hinzugefügt </li><li> Unterstützung für Melden der Ressourcen-ID bei Verwendung einer Installation ohne Erweiterung hinzugefügt </li></ul>| 
| 8.0.11103 | – |  April 2018 | |
| 8.0.11081 | 1.0.11081 | November 2017 | | 
| 8.0.11072 | 1.0.11072 | September 2017 | |
| 8.0.11049 | 1.0.11049 | Februar 2017 | |

### <a name="microsoft-defender-for-cloud"></a>Microsoft Defender für Cloud

Microsoft Defender für Cloud stellt den Log Analytics-Agent automatisch bereit und verbindet ihn mit dem standardmäßigen Log Analytics-Arbeitsbereich für das Azure-Abonnement. Wenn Sie Microsoft Defender für Cloud verwenden, führen Sie die in diesem Dokument beschriebenen Schritte nicht aus. Andernfalls überschreiben Sie den konfigurierten Arbeitsbereich und unterbrechen die Verbindung mit Microsoft Defender für Cloud.

### <a name="internet-connectivity"></a>Internetkonnektivität

Um die Log Analytics-Agent-Erweiterung für Windows verwenden zu können, muss der virtuelle Zielcomputer mit dem Internet verbunden sein. 

## <a name="extension-schema"></a>Erweiterungsschema

Der folgende JSON-Code zeigt das Schema für die Log Analytics-Agent-Erweiterung. Für die Erweiterung werden die Arbeitsbereichs-ID und der Arbeitsbereichsschlüssel aus dem Log Analytics-Zielarbeitsbereich benötigt. Diese sind in den Einstellungen des Arbeitsbereichs im Azure-Portal zu finden. Da der Arbeitsbereichsschlüssel als vertrauliche Information behandelt werden muss, sollte er in einer geschützten Einstellungskonfiguration gespeichert werden. Die geschützten Einstellungsdaten der Azure-VM-Erweiterung werden verschlüsselt und nur auf dem virtuellen Zielcomputer entschlüsselt. Bitte beachten Sie, dass bei **workspaceId** und **workspaceKey** die Groß-/Kleinschreibung beachtet wird.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

### <a name="property-values"></a>Eigenschaftswerte

| Name | Wert/Beispiel |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (z.B.)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (z.B.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* In der Log Analytics-API heißt die „workspaceId“ „consumerId“.

> [!NOTE]
> Informationen zu weiteren Eigenschaften finden Sie unter [Verbinden von Windows-Computern mit Azure Monitor](../../azure-monitor/agents/agent-windows.md).

## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Das im vorherigen Abschnitt erläuterte JSON-Schema kann in einer Azure Resource Manager-Vorlage zum Ausführen der Log Analytics-Agent-Erweiterung im Rahmen einer Azure Resource Manager-Bereitstellung verwendet werden. Eine Beispielvorlage mit der Log Analytics-Agent-VM-Erweiterung finden Sie im [Azure-Schnellstartkatalog](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/oms-extension-windows-vm). 

>[!NOTE]
>Die Vorlage unterstützt nicht die Angabe mehrerer Arbeitsbereichs-IDs und Arbeitsbereichsschlüssel, wenn Sie den Agent zum Berichten an mehrere Arbeitsbereiche konfigurieren möchten. Informationen zum Konfigurieren des Agents zum Berichten an mehrere Arbeitsbereiche finden Sie unter [Hinzufügen oder Entfernen von Arbeitsbereichen](../../azure-monitor/agents/agent-manage.md#adding-or-removing-a-workspace).  

Der JSON-Code für eine Erweiterung des virtuellen Computers kann innerhalb der VM-Ressource geschachtelt oder im Stamm bzw. auf der obersten Ebene einer Resource Manager-JSON-Vorlage platziert werden. Die Platzierung des JSON-Codes wirkt sich auf den Wert von Name und Typ der Ressource aus. Weitere Informationen finden Sie unter [Set name and type for child resources](../../azure-resource-manager/templates/child-resource-name-type.md) (Festlegen von Name und Typ für untergeordnete Ressourcen). 

Im folgenden Beispiel wird davon ausgegangen, dass die Log Analytics-Erweiterung in der VM-Ressource geschachtelt ist. Beim Schachteln der Ressource für die Erweiterung wird der JSON-Code im `"resources": []`-Objekt des virtuellen Computers platziert.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Beim Platzieren des JSON-Codes für die Erweiterung im Stamm der Vorlage enthält der Name der Ressource einen Verweis auf die übergeordnete VM, und der Typ spiegelt die geschachtelte Konfiguration wider. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell-Bereitstellung

Mit dem Befehl `Set-AzVMExtension` können Sie die Log Analytics-Agent-VM-Erweiterung auf einem vorhandenen virtuellen Computer bereitstellen. Vor dem Ausführen des Befehls müssen die öffentliche und die private Konfiguration in einer PowerShell-Hashtabelle gespeichert werden. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe des Azure PowerShell-Moduls abgerufen werden. Führen Sie über das Azure PowerShell-Modul den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Die Ausgabe der Erweiterungsausführung wird in Dateien im folgenden Verzeichnis protokolliert:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).
