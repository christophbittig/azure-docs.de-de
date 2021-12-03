---
title: Azure Confidential Computing-Lösungen auf virtuellen Computern
description: Erfahren Sie mehr über Azure Confidential Computing-Lösungen auf virtuellen Computern.
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1805b153d33915c2ec252c0e4a932eceb710b59f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331897"
---
# <a name="solutions-on-azure-for-intel-sgx"></a>Lösungen in Azure für Intel SGX

Dieser Artikel enthält Informationen zum Bereitstellen von VMS für Intel SGX.

### <a name="current-available-sizes-and-regions"></a>Aktuelle verfügbare Größen und Regionen

Um eine Liste der VM-Größen für Intel SGX in verfügbaren Regionen und Verfügbarkeitszonen zu erhalten, führen Sie den folgenden Befehl in [Azure CLI](/cli/azure/install-azure-cli-windows) aus:

```azurecli-interactive
az vm list-skus `
    --size Standard_DC `
    --all `
    --output table
```

### <a name="dedicated-host-requirements"></a>Anforderungen an dedizierte Hosts

Das Bereitstellen eines virtuellen Computers des Typs **Standard_DC8_v2**, **Standard_DC48s_v3** oder **Standard_DC48ds_v3** belegt den vollständigen Host und wird nicht für andere Mandanten oder Abonnements freigegeben. Diese VM-SKU-Familie bietet die Isolation, die Sie u. U. benötigen, um gesetzliche Vorgaben in Bezug auf Compliance und Sicherheit zu erfüllen, die in der Regel durch einen dedizierten Hostdienst erfüllt werden. Wenn Sie diese Größen auswählen, ordnet der physische Hostserver alle verfügbaren Hardwareressourcen einschließlich des EPC-Arbeitsspeichers nur Ihrem virtuellen Computer zu. Diese Bereitstellung ist nicht identisch mit dem Dienst [Azure Dedicated Host](../virtual-machines/dedicated-hosts.md), der von anderen Azure VM-Familien bereitgestellt wird.


## <a name="deployment-considerations"></a>Überlegungen zur Bereitstellung

Folgen Sie einem Tutorial zum Schnellstart, um eine VM der DCsv2-Serie in weniger als 10 Minuten bereitzustellen. 

- **Azure-Abonnement:** Um eine größere Anzahl von Confidential Computing-VM-Instanzen bereitzustellen, sollten Sie ein Abonnement mit nutzungsbasierter Bezahlung oder eine andere Kaufoption in Erwägung ziehen. Wenn Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden, verfügen Sie nicht über ein Kontingent für die entsprechende Menge von Azure-Computekernen.

- **Preise und regionale Verfügbarkeit**: Die Preise für VMs der Serien DCsv2, DCsv3 und DCdsv3 finden Sie auf der [Seite mit der Preisübersicht für virtuelle Computer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Informationen zur Verfügbarkeit in den Azure-Regionen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) .

- **Kontingent Speicherkerne:** Es kann sein, dass Sie das Kontingent für die Speicherkerne in Ihrem Azure-Abonnement anpassen müssen, indem Sie den Standardwert erhöhen. Möglicherweise ist bei Ihrem Abonnement auch die Anzahl von Kernen beschränkt, die in bestimmten VM-Größenkategorien, einschließlich der DCsv2-Serie, bereitgestellt werden können. In diesem Fall können Sie kostenlos [eine Anfrage an den Onlinekundensupport richten](../azure-portal/supportability/per-vm-quota-requests.md) und eine Erhöhung des Kontingents anfordern. Beachten Sie, dass Standardgrenzwerte unter Umständen je nach Abonnementkategorie variieren.

  > [!NOTE]
  > Wenn Sie einen umfangreichen Kapazitätsbedarf haben, wenden Sie sich an den Azure-Support. Azure-Kontingente sind Angebotsbeschränkungen, keine Kapazitätsgarantien. Unabhängig von Ihrem Kontingent werden nur die tatsächlich verwendeten Kerne in Rechnung gestellt.
  
- **Größenänderung**: Aufgrund ihrer spezialisierten Hardware können Sie die Größe dieser Instanzen nur innerhalb der selben Größenfamilie ändern. So können Sie beispielsweise die Größe einer VM der DCsv2-Serie nur in eine andere Größe der DCsv2-Serie ändern. 

- **Image**: Um Unterstützung für Intel Software Guard Extension (Intel SGX) auf Confidential Computing-Instanzen zu bieten, müssen alle Bereitstellungen auf Images der Generation 2 erfolgen. Azure Confidential Computing unterstützt Workloads, die unter Ubuntu 20.04 Gen 2, Ubuntu 18.04 Gen 2 and Windows Server 2019 Gen 2. ausgeführt werden. Weitere Informationen zu unterstützten und nicht unterstützten Szenarios finden Sie unter [Unterstützung für VMs der Generation 2 in Azure](../virtual-machines/generation-2.md). 

- **Speicher**: DCsv2-Serie unterstützt SSD Standard und SSD Premium, mit Ausnahme von DC8_v2. DCsv3 und DCdsv3-Serie unterstützen SSD Standard, SSD Premium und Disk Ultra.

- **Datenträgerverschlüsselung**: Confidential Computing-Instanzen unterstützen zurzeit keine Datenträgerverschlüsselung. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Überlegungen zu Hochverfügbarkeit und Notfallwiederherstellung

Wenn Sie VMs in Azure nutzen, müssen Sie eine Lösung für Hochverfügbarkeit und Notfallwiederherstellung implementieren, um Ausfallzeiten zu vermeiden. 

Azure Confidential Computing unterstützt derzeit keine Zonenredundanz über Verfügbarkeitszonen. Um für Confidential Computing maximale Verfügbarkeit und Redundanz zu erreichen, verwenden Sie [Verfügbarkeitsgruppen](../virtual-machines/availability-set-overview.md). Aufgrund von Hardwarebeschränkungen können Verfügbarkeitsgruppen für Confidential Computing-Instanzen nur maximal 10 Updatedomänen haben. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Bereitstellung mit ARM-Vorlage (Azure Resource Manager)

Azure Resource Manager ist der Bereitstellungs- und Verwaltungsdienst für Azure. Er bietet eine Verwaltungsebene, die das Erstellen, Aktualisieren und Löschen von Ressourcen in Ihrem Azure-Abonnement ermöglicht. Mithilfe von Verwaltungsfeatures wie Zugriffssteuerung, Sperren und Tags können Sie Ihre Ressourcen nach der Bereitstellung schützen und organisieren.

Weitere Informationen zu ARM-Vorlagen finden Sie in der [Übersicht über die Vorlagenbereitstellung](../azure-resource-manager/templates/overview.md).

Verwenden Sie zum Bereitstellen mithilfe von ARM-Vorlagen die [VM-Ressource](../virtual-machines/windows/template-description.md). Stellen Sie sicher, dass Sie die richtigen Eigenschaften für **vmSize** und **imageReference** angeben.

### <a name="vm-size"></a>Größe des virtuellen Computers

Geben Sie in der Resource Manager-Vorlage in der Ressource „Virtual Machine“ die folgenden Größen an. Diese Zeichenfolge wird in **properties** als **vmSize** eingegeben.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2",
        "Standard_DC1s_v3",
        "Standard_DC2s_v3",
        "Standard_DC4s_v3",
        "Standard_DC8s_v3",
        "Standard_DC16s_v3",
        "Standard_DC24s_v3",
        "Standard_DC32s_v3",
        "Standard_DC48s_v3",
        "Standard_DC1ds_v3",
        "Standard_DC2ds_v3",
        "Standard_DC4ds_v3",
        "Standard_DC8ds_v3",
        "Standard_DC16ds_v3",
        "Standard_DC24ds_v3",
        "Standard_DC32ds_v3",
        "Standard_DC48ds_v3",
      ],
```

### <a name="gen2-os-image"></a>Gen2-Betriebssystemimage

Unter **properties** müssen Sie zudem unter **storageProfile** auf ein Image verweisen. Verwenden Sie *nur eines* der folgenden Images für **imageReference**.

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "20_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "20_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Nächste Schritte 

In diesem Artikel haben Sie mehr über die Qualifikationen und Konfiguration erfahren, die beim Erstellen eines virtuellen Intel SGX-Computers erforderlich sind.

> [!div class="nextstepaction"]
> [Erstellen eines virtuellen SGX-Computers mithilfe von Azure Marketplace](quick-create-marketplace.md)
