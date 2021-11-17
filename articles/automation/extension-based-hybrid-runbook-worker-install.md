---
title: Bereitstellen eines erweiterungsbasierten Hybrid Runbook Workers für Windows- oder Linux-Benutzer in Azure Automation (Vorschau)
description: In diesem Artikel erfahren Sie, wie Sie einen erweiterungsbasierten Hybrid Runbook Worker für Windows oder Linux bereitstellen, mit dem Sie Runbooks auf Windows-basierten Computern in Ihrem lokalen Rechenzentrum oder in einer Cloudumgebung ausführen können.
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: 48f83cd92f682b6fb66a1d7b5bebd49cd5053b62
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400773"
---
# <a name="deploy-an-extension-based-windows-or-linux-user-hybrid-runbook-worker-in-automation-preview"></a>Bereitstellen eines erweiterungsbasierten Hybrid Runbook Workers für Windows- oder Linux-Benutzer in Automation (Vorschau)

Das erweiterungsbasierte Onboarding ist nur für **benutzerbasierte** Hybrid Runbook Worker vorgesehen. Informationen zum Onboarding **systembasierter** Hybrid Runbook Worker finden Sie unter [Bereitstellen eines Agent-basierten Windows-Hybrid Runbook Workers in Automation](./automation-windows-hrw-install.md) bzw. unter [Bereitstellen eines Agent-basierten Linux-Hybrid Runbook Workers in Automation](./automation-linux-hrw-install.md). 

Sie können die Benutzerfunktion Hybrid Runbook Worker von Azure Automation verwenden, um Runbooks direkt auf einem Azure- oder Nicht-Azure-Computer auszuführen, einschließlich der Server, die bei [Server mit Azure Arc-Unterstützung](../azure-arc/servers/overview.md) registriert sind. Von dem Computer oder Server aus, der die Rolle hostet, können Sie Runbooks direkt auf dem Computer und mit Ressourcen in der Umgebung ausführen, um diese lokalen Ressourcen zu verwalten.

Azure Automation speichert und verwaltet Runbooks und übermittelt sie dann an dafür ausgewählte Computer. Dieser Artikel beschreibt, wie Sie einen benutzerbasierten Hybrid Runbook Worker auf einem Windows- oder Linux-Computer bereitstellen, den Worker entfernen und eine Hybrid Runbook Worker-Gruppe entfernen.

Lesen Sie nach dem erfolgreichen Bereitstellen eines Runbook Workers [Running runbooks on a Hybrid Runbook Worker (Ausführen von Runbooks auf einem Hybrid Runbook Worker)](automation-hrw-run-runbooks.md), um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Datencenter oder in einer anderen Cloudumgebung konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="machine-minimum-requirements"></a>Mindestanforderungen für Computer
- Zwei Kerne.
- 4 GB RAM.
- Die systemseitig zugewiesene verwaltete Identität muss auf der Azure-VM oder dem Arc-fähigen Server aktiviert sein.  Wenn die systemseitig zugewiesene verwaltete Identität nicht aktiviert ist, wird sie beim Hinzufügen aktiviert.
- Auf Nicht-Azure-Computern muss der Agent für Server mit Azure Arc-Unterstützung (Connected Machine-Agent) installiert sein. Anweisungen zum Installieren des `AzureConnectedMachineAgent` finden Sie unter [Verbinden von Hybridcomputern mit Azure über das Azure-Portal](../azure-arc/servers/onboard-portal.md).

### <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme
| Windows | Linux (x64)|
|---|---|
| &#9679; Windows Server 2019 (einschließlich Server Core) <br> &#9679; Windows Server 2016, Version 1709 and 1803 (ohne Server Core) <br> &#9679; Windows Server 2012, 2012 R2 <br><br> | &#9679; Debian GNU/Linux 7 und 8 <br> &#9679; Ubuntu 18.04 und 20.04 LTS, <br> &#9679; SUSE Linux Enterprise Server 15 und 15.1 (für SUSE wurden keine Versionen mit den Nummern 13 oder 14 veröffentlicht) <br> &#9679; Red Hat Enterprise Linux Server 7 und 8 |

### <a name="other-requirements"></a>Andere Anforderungen
| Windows | Linux (x64)|
|---|---|
| Windows PowerShell 5.1 (WMF 5.1 herunterladen). PowerShell Core wird nicht unterstützt.| Die Linux-Härtung darf nicht aktiviert sein.  |
| .NET Framework 4.6.2 oder höher ||

### <a name="package-requirements-for-linux"></a>Paketanforderungen für Linux

| Erforderliches Paket | BESCHREIBUNG |Mindestversion|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-Bibliothek| 2.5-12 |
|Openssl| OpenSSL-Bibliotheken | 1.0 (TLS 1.1 und TLS 1.2 werden unterstützt)|
|Curl | cURL-Webclient | 7.15.5|
|Python-ctypes | Fremdfunktionsbibliothek für Python| Python 2.x oder Python 3.x erforderlich |
|PAM | Module für austauschbare Authentifizierung|

| Optionale Pakete | BESCHREIBUNG | Mindestversion|
|--------------------- | --------------------- | -------------------|
| PowerShell Core | Um PowerShell-Runbooks auszuführen, muss PowerShell Core installiert sein. Anweisungen hierzu finden Sie unter [Installieren von PowerShell unter Linux](/powershell/scripting/install/installing-powershell-core-on-linux). | 6.0.0 |

### <a name="network-requirements"></a>Netzwerkanforderungen

### <a name="proxy-server-use"></a>Verwenden von Proxyservern

Wenn Sie einen Proxyserver für die Kommunikation zwischen Azure Automation und Computern verwenden, auf denen der erweiterungsbasierte Hybrid Runbook Worker ausgeführt wird, müssen Sie sicherstellen, dass auf die entsprechenden Ressourcen zugegriffen werden kann. Das Zeitlimit für Anforderungen vom Hybrid Runbook Worker und von den Automation-Diensten beträgt 30 Sekunden. Nach drei Versuchen tritt bei einer Anforderung ein Fehler auf.

### <a name="firewall-use"></a>Verwenden von Firewalls

Wenn Sie eine Firewall verwenden, um den Zugriff auf das Internet einzuschränken, müssen Sie die Firewall so konfigurieren, dass der Zugriff möglich ist. Nachfolgend sind der Port und die URLs aufgeführt, die für die Kommunikation von Hybrid Runbook Worker und [Automation State Configuration](./automation-dsc-overview.md) mit Azure Automation erforderlich sind.

| Eigenschaft | BESCHREIBUNG |
|---|---|
|Port | 443 für ausgehenden Internetzugriff|
|Globale URL |*.azure-automation.net|
|Globale URL von „US Gov Virginia“ |*.azure-automation.us|
|Agent-Dienst |`https://<workspaceId>.agentsvc.azure-automation.net`|

## <a name="create-hybrid-worker-group"></a>Erstellen einer Hybrid Worker-Gruppe 

Führen Sie die folgenden Schritte aus, um eine Hybrid Worker-Gruppe im Azure-Portal zu erstellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu Ihrem Automation-Konto. 

1. Wählen Sie unter **Prozessautomatisierung** die Option **Hybrid Worker-Gruppen** aus. 

1. Wählen Sie **+ Hybrid Worker-Gruppe erstellen** aus. 

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-groups-portal.png" alt-text="Auswählen der Option für Hybrid Worker-Gruppen im Portal":::

1. Geben Sie auf der Registerkarte  **Grundlagen** im Textfeld **Name** einen Namen für Ihre Hybrid Worker-Gruppe ein. 

1. Für die Option **Anmeldeinformationen für ausführendes Konto verwenden** gilt Folgendes: 
   - Wenn Sie **Nein** auswählen, wird die Hybriderweiterung über das lokale Systemkonto installiert.
   - Wenn Sie **Ja** auswählen, wählen Sie in der Dropdownliste das Anmeldeinformationsobjekt aus.

1. Wählen Sie **Weiter** aus, um zur Registerkarte **Hybrid Worker** zu wechseln. Sie können Azure-VMs oder Azure Arc-fähige Server auswählen, die dieser Hybrid Worker-Gruppe hinzugefügt werden sollen. Wenn Sie keine Computer auswählen, wird eine leere Hybrid Worker-Gruppe erstellt. Sie können auch später Computer hinzufügen. 

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/basics-tab-portal.png" alt-text="Eingeben von Namen und Anmeldeinformationen auf der Registerkarte „Grundlagen“":::

1. Wählen Sie **Computer hinzufügen** aus, um zur Seite **Computer als Hybrid Worker hinzufügen** zu wechseln. Es werden nur Computer angezeigt, die keiner anderen Hybrid Worker-Gruppe angehören. 

1. Aktivieren Sie das Kontrollkästchen neben den Computern, die Sie der Hybrid Worker-Gruppe hinzufügen möchten. Wenn Nicht-Azure-Computer nicht aufgeführt werden, stellen Sie sicher, dass der Azure Arc Connected Machine-Agent auf dem Computer installiert ist.  

1. Wählen Sie **Hinzufügen**. 

1. Klicken Sie auf **Weiter**, um zur Registerkarte **Überprüfen + erstellen** zu gelangen. 

1. Wählen Sie **Erstellen** aus. Die Hybrid Worker-Erweiterung wird auf dem Computer installiert, und der Hybrid Worker wird bei der Hybrid Worker-Gruppe registriert. Der Hybrid Worker wird der Gruppe erfolgt hinzugefügt, während die Installation der Erweiterung einige Minuten dauern kann. Wählen Sie **Aktualisieren** aus, um die neue Gruppe anzuzeigen. Wählen Sie den Gruppennamen aus, um die Details des Hybrid Workers anzuzeigen.

   > [!NOTE]
   > Ein ausgewählter Computer wird einer Hybrid Worker-Gruppe nicht hinzugefügt, wenn er bereits einer anderen Hybrid Worker-Gruppe angehört.

## <a name="add-a-machine-to-a-hybrid-worker-group"></a>Hinzufügen eines Computers zu einer Hybrid Worker-Gruppe

Sie können Computer auch einer vorhandenen Hybrid Worker-Gruppe hinzufügen.

1. Klicken Sie unter **Prozessautomatisierung** auf **Hybrid Worker-Gruppen**, und wählen Sie dann Ihre vorhandene Hybrid Worker-Gruppe aus, um zur Seite **Hybrid Worker-Gruppe** zu wechseln.

1. Wählen Sie unter **Hybrid Worker-Gruppe** die Option **Hybrid Worker** aus.

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-group-add-machine.png" alt-text="Schaltfläche „Hinzufügen“ zum Hinzufügen von Computern zu einer vorhandenen Gruppe":::

1. Wählen Sie **+ Hinzufügen** aus, um zur Seite **Computer als Hybrid Worker hinzufügen** zu wechseln. Es werden nur Computer angezeigt, die keiner anderen Hybrid Worker-Gruppe angehören. 

1. Aktivieren Sie das Kontrollkästchen neben den Computern, die Sie der Hybrid Worker-Gruppe hinzufügen möchten. Wenn Nicht-Azure-Computer nicht aufgeführt werden, stellen Sie sicher, dass der Azure Arc Connected Machine-Agent auf dem Computer installiert ist.

1. Klicken Sie auf **Hinzufügen**, um den Computer der Gruppe hinzuzufügen. Nach dem Hinzufügen wird der Computertyp als Azure-VM oder Arc-fähiger Server angezeigt. Im Feld **Plattform** wird der Worker als **Agent-basiert (V1)** oder **Erweiterungsbasiert (V2)** aufgeführt.

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-group-platform.png" alt-text="Plattformfeld mit Agent- oder erweiterungsbasiertem Worker":::

## <a name="delete-a-hybrid-runbook-worker"></a>Löschen eines Hybrid Runbook Workers

Sie können den Hybrid Runbook Worker über das Portal löschen.

1. Klicken Sie unter **Prozessautomatisierung** auf **Hybrid Worker-Gruppen**, und wählen Sie dann Ihre Hybrid Worker-Gruppe aus, um zur Seite **Hybrid Worker-Gruppe** zu wechseln.

1. Wählen Sie unter **Hybrid Worker-Gruppe** die Option **Hybrid Worker** aus.

1. Aktivieren Sie das Kontrollkästchen neben den Computern, die Sie aus der Hybrid Worker-Gruppe löschen möchten.

1. Klicken Sie auf **Löschen**.  Im Dialogfeld **Hybrid Worker löschen** wird eine Warnung angezeigt, dass der ausgewählte Hybrid Worker dauerhaft gelöscht wird. Klicken Sie auf **Löschen**. Bei diesem Vorgang wird die Erweiterung für den **erweiterungsbasierten Worker (V2)** gelöscht oder der Eintrag **Agent-basiert (V1)** aus dem Portal entfernt. Allerdings bleibt der veraltete Hybrid Worker auf der VM erhalten. Informationen zum manuellen Deinstallieren des Agents finden Sie unter [Deinstallieren des Agents](../azure-monitor/agents/agent-manage.md#uninstall-agent).

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/delete-machine-from-group.png" alt-text="Löschen einer VM aus einer vorhandenen Gruppe":::

   > [!NOTE]
   > Ein Hybrid Worker kann mit beiden Plattformen gleichzeitig vorhanden sein: **Agent-basiert (V1)** und **Erweiterungsbasiert (V2)** . Wenn Sie **Erweiterungsbasiert (V2)** auf einem Hybrid Worker installieren, auf dem bereits **Agent-basiert (V1)** ausgeführt wird, werden in der Gruppe zwei Einträge des Hybrid Runbook Workers angezeigt: einer mit der Plattform **Erweiterungsbasiert (V2)** und einer mit der Plattform **Agent-basiert (V1)** .

## <a name="delete-a-hybrid-runbook-worker-group"></a>Löschen einer Hybrid Runbook Worker-Gruppe

Sie können eine leere Hybrid Runbook Worker-Gruppe über das Portal löschen.

1. Klicken Sie unter **Prozessautomatisierung** auf **Hybrid Worker-Gruppen**, und wählen Sie dann Ihre Hybrid Worker-Gruppe aus, um zur Seite **Hybrid Worker-Gruppe** zu wechseln.

1. Klicken Sie auf **Löschen**. In einem Dialogfeld wird in einer Warnung darauf hingewiesen, dass alle Computer entfernt werden müssen, die als Hybrid Worker in der Hybrid Worker-Gruppe definiert sind. Wenn der Gruppe bereits ein Worker hinzugefügt wurde, müssen Sie den Worker zuerst aus der Gruppe löschen.

1. Wählen Sie **Ja** aus. Die Hybrid Worker-Gruppe wird gelöscht.

## <a name="use-azure-resource-manager-template"></a>Verwenden von Azure Resource Manager-Vorlagen

Sie können eine ARM-Vorlage (Azure Resource Manager) verwenden, um in Azure eine neue Windows-VM zu erstellen und mit einem vorhandenen Automation-Konto und einer vorhandenen Hybrid Worker-Gruppe zu verbinden. Informationen zu ARM-Vorlagen finden Sie unter [Was sind ARM-Vorlagen?](../azure-resource-manager/templates/overview.md)

### <a name="review-the-template"></a>Überprüfen der Vorlage

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationAccount": {
      "type": "string"
    },
    "automationAccountLocation": {
      "type": "string"
    },
    "workerGroupName": {
      "type": "string"
    },
    "virtualMachineName": {
      "type": "string",
      "defaultValue": "simple-vm",
      "metadata": {
        "description": "Name of the virtual machine."
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "minLength": 12,
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "vmLocation": {
      "type": "string",
      "defaultValue": "North Central US",
      "metadata": {
        "description": "Location for the VM."
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_DS1_v2",
      "metadata": {
        "description": "Size of the virtual machine."
      }
    },
    "osVersion": {
      "type": "string",
      "defaultValue": "2019-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "2016-Nano-Server",
        "2016-Datacenter-with-Containers",
        "2016-Datacenter",
        "2019-Datacenter",
        "2019-Datacenter-Core",
        "2019-Datacenter-Core-smalldisk",
        "2019-Datacenter-Core-with-Containers",
        "2019-Datacenter-Core-with-Containers-smalldisk",
        "2019-Datacenter-smalldisk",
        "2019-Datacenter-with-Containers",
        "2019-Datacenter-with-Containers-smalldisk"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version."
      }
    },
    "dnsNameForPublicIP": {
      "type": "string",
      "metadata": {
        "description": "DNS name for the public IP"
      }
    },
    "_CurrentDateTimeInTicks": {
      "type": "string",
      "defaultValue": "[utcNow('yyyy-MM-dd')]"
    }
  },
  "variables": {
    "nicName": "myVMNict",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
    "vmName": "[parameters('virtualMachineName')]",
    "virtualNetworkName": "MyVNETt",
    "publicIPAddressName": "myPublicIPt",
    "networkSecurityGroupName": "default-NSGt",
    "UniqueStringBasedOnTimeStamp": "[uniqueString(deployment().name, parameters('_CurrentDateTimeInTicks'))]"
  },
  "resources": [
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[parameters('vmLocation')]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
        }
      }
    },
    {
      "comments": "Default Network Security Group for template",
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2020-08-01",
      "name": "[variables('networkSecurityGroupName')]",
      "location": "[parameters('vmLocation')]",
      "properties": {
        "securityRules": [
          {
            "name": "default-allow-3389",
            "properties": {
              "priority": 1000,
              "access": "Allow",
              "direction": "Inbound",
              "destinationPortRange": "3389",
              "protocol": "Tcp",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationAddressPrefix": "*"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
      ],
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]",
              "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[variables('publicIPAddressName')]",
        "[variables('virtualNetworkName')]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[variables('nicName')]"
      ],
      "identity": {
             "type": "SystemAssigned"
      } ,
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "[parameters('osVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        }
      }
    },
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2021-06-22",
      "name": "[parameters('automationAccount')]",
      "location": "[parameters('automationAccountLocation')]",
      "properties": {
        "sku": {
          "name": "Basic"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('workerGroupName'),'/',guid('AzureAutomationJobName', variables('UniqueStringBasedOnTimeStamp')))]",
          "type": "hybridRunbookWorkerGroups/hybridRunbookWorkers",
          "apiVersion": "2021-06-22",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))]",
            "[resourceId('Microsoft.Compute/virtualMachines', variables('vmName'))]"
          ],
          "properties": {
            "vmResourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
          }
        }
      ]
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'),'/HybridWorkerExtension')]",
      "apiVersion": "2020-12-01",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))]",
        "[resourceId('Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Automation.HybridWorker",
        "type": "HybridWorkerForWindows",
        "typeHandlerVersion": "0.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "AutomationAccountURL": "[reference(resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))).AutomationHybridServiceUrl]"
        }
      }
    }
  ],
  "outputs": {
    "output1": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))).AutomationHybridServiceUrl]"
    }
  }
}
```

In der Vorlage sind die folgenden Azure-Ressourcen definiert:

- [hybridRunbookWorkerGroups/hybridRunbookWorkers](/azure/templates/microsoft.automation/automationaccounts/hybridrunbookworkergroups/hybridrunbookworkers)
- [Microsoft.Compute/virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions)

### <a name="review-parameters"></a>Überprüfen der Parameter

Überprüfen Sie die in dieser Vorlage verwendeten Parameter.

|Eigenschaft |BESCHREIBUNG |
|---|---|
|automationAccount| Der Name des vorhandenen Automation-Kontos. |
|automationAccountLocation | Die Region des vorhandenen Automation-Kontos. |
|workerGroupName | Der Name der vorhandenen Hybrid Worker-Gruppe. |
|virtualMachineName| Der Name der zu erstellenden VM. Standardwert: `simple-vm`.|
|adminUsername| Der Benutzername des VM-Administrators. |
|adminPassword| Das Kennwort des VM-Administrators. |
|vmLocation| Die Region für die neue VM. Standardwert: `North Central US`.|
|vmSize| Die Größe für die neue VM. Standardwert: `Standard_DS1_v2`. |
|osVersion| Das Betriebssystem für die neue Windows-VM. Standardwert: `2019-Datacenter`.|
|dnsNameForPublicIP| Der DNS-Name für die öffentliche IP-Adresse. |


## <a name="next-steps"></a>Nächste Schritte

* Um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Rechenzentrum oder in einer anderen Cloudumgebung konfigurieren, lesen Sie sich [Ausführen von Runbooks auf einem Hybrid Runbook Worker](automation-hrw-run-runbooks.md) durch.

* Informationen zum Behandeln von Problemen mit Ihren Hybrid Runbook Workern finden Sie unter [Problembehandlung von Hybrid Runbook Workern](troubleshoot/hybrid-runbook-worker.md#general).
