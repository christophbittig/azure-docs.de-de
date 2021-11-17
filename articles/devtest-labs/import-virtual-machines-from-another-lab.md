---
title: Importieren von virtuellen Computern aus einem anderen Lab
description: Erfahren Sie, wie Sie VMs in Azure DevTest Labs aus einem Lab in ein anderes importieren.
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 50f17183caa7da86ec6704af35129ed8bd707d5a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059845"
---
# <a name="import-virtual-machines-from-one-lab-to-another"></a>Importieren von VMs aus einem Lab in ein anderes

In diesem Artikel wird erläutert und beschrieben, wie Sie virtuelle Computer (VMs) aus einem DevTest Labs-Lab in ein anderes Lab importieren.

## <a name="scenarios"></a>Szenarien
Die folgenden Beispiele beschreiben Szenarien, in denen Sie ggf. VMs aus einem Lab in ein anderes Lab importieren müssen:

- Eine Person wechselt aus einer Gruppe in eine andere und möchte ihren Entwicklerdesktop im Lab des neuen Teams übernehmen.
- Die Gruppe hat ein [Kontingent auf Abonnementebene](../azure-resource-manager/management/azure-subscription-service-limits.md) erreicht und möchte die Teams in mehrere Azure-Abonnements unterteilen.
- Das Unternehmen wechselt zu Azure ExpressRoute (oder einer anderen neuen Netzwerktopologie), und das Team möchte die VMs verschieben, um diese neue Infrastruktur zu verwenden.

## <a name="requirements-and-constraints"></a>Anforderungen und Einschränkungen

Beim Importvorgang werden die VMs aus dem Quell-Lab in das Ziel-Lab importiert. Optional können Sie die VM bei diesem Vorgang umbenennen. Der Importvorgang umfasst alle Abhängigkeiten wie Datenträger, Zeitpläne und Netzwerkeinstellungen.

Der Vorgang ist ein Kopiervorgang, kein Verschiebungsvorgang, und er kann sehr lange dauern. Die Importzeit hängt teilweise von den folgenden Faktoren ab:

- Anzahl und Größe der an den Quellcomputer angefügten Datenträger
- Entfernung zwischen Quell- und Zielregionen

Wenn der Import abgeschlossen ist, fährt der Prozess die Quell-VM herunter und startet die neue VM, die im Ziel-Lab ausgeführt wird.

Es gibt mehrere Anforderungen und Einschränkungen für den Import von VMs aus einem Lab:

- Sie können VMs über Abonnements und Regionen hinweg importieren, beide Abonnements müssen jedoch dem gleichen Azure Active Directory-Mandanten zugeordnet sein.
- VMs dürfen sich im Quell-Lab nicht in einem abrufbaren Zustand befinden.
- Sie müssen der Besitzer der VM im Quell-Lab und der Besitzer des Ziel-Labs sein.
- Zurzeit wird dieses Feature ausschließlich mit PowerShell und der REST-API unterstützt.

## <a name="use-powershell-to-import-one-or-all-lab-vms"></a>Verwenden von PowerShell zum Importieren einer oder aller Lab-VMs

Laden Sie [ImportVirtualMachines.ps1](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines) herunter, und führen Sie das Skript aus. Mithilfe des Skripts können Sie eine einzelne VM oder alle VMs aus dem Quell-Lab in das Ziel-Lab importieren.

Um dieses PowerShell-Skript auszuführen, identifizieren Sie die Quell- und Zielabonnements und -Labs sowie die Quell-VM. Geben Sie optional einen neuen Namen für die Ziel-VM an.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>"`
                            -SourceDevTestLabName "<Name of the source lab>"`
                            -SourceVirtualMachineName "<Name of the VM to import from the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contains the destination lab>"`
                            -DestinationDevTestLabName "<Name of the destination lab>"`
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```
Wenn Sie keine Quell-VM angeben, importiert das Skript automatisch alle VMs im Quell-Lab.

## <a name="use-http-rest-to-import-a-vm"></a>Verwenden von HTTP REST zum Importieren einer VM

Der REST-Aufruf ist einfach. Sie geben die Informationen zum Identifizieren der Quell- und Zielressourcen an. Der Vorgang wird für die Ressource des Ziel-Labs ausgeführt.

```http
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Festlegen von Richtlinien für ein Lab](devtest-lab-set-lab-policy.md)
- [Häufig gestellte Fragen zu DevTest Labs](devtest-lab-faq.yml)
