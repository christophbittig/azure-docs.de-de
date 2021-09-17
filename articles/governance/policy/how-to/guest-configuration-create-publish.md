---
title: Veröffentlichen benutzerdefinierter Paketartefakte für Gastkonfigurationen
description: Erfahren Sie, wie Sie eine Gastkonfigurationspaketdatei in Azure Blob Storage veröffentlichen und ein SAS-Token für den sicheren Zugriff abrufen.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: aa20bc3d9c47258c6ebedd2419cf830ba47c1c1c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772929"
---
# <a name="how-to-publish-custom-guest-configuration-package-artifacts"></a>Veröffentlichen benutzerdefinierter Paketartefakte für Gastkonfigurationen

Bevor Sie beginnen, sollten Sie die Übersicht zur [Gastkonfiguration](../concepts/guest-configuration.md) lesen.

Benutzerdefinierte ZIP-Pakete für die Gastkonfiguration müssen an einem Speicherort abgelegt werden, auf den die verwalteten Computer über HTTPS zugreifen können. Als Speicherort kommen beispielsweise GitHub-Repositorys, ein Azure-Repository, ein Azure-Speicher oder ein Webserver in Ihrem privaten Rechenzentrum in Frage.

Konfigurationspakete, die `Audit` und `AuditandSet`unterstützen, werden auf die gleiche Weise veröffentlicht. Es ist nicht notwendig, während der Veröffentlichung je nach Paketmodus besondere Maßnahmen zu ergreifen.

## <a name="publish-a-configuration-package"></a>Veröffentlichen eines Konfigurationspakets

Der bevorzugte Speicherort zum Speichern eines Konfigurationspakets ist Azure Blob Storage.
Es werden keine besonderen Anforderungen an das Speicherkonto gestellt, aber es ist ratsam, die Datei in einer Region in der Nähe Ihrer Computer zu hosten. Falls Sie das Paket nicht öffentlich zugänglich machen möchten, können Sie ein [SAS-Token](../../../storage/common/storage-sas-overview.md) in die URL einfügen oder einen [Dienstendpunkt](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) für Computer in einem privaten Netzwerk implementieren.

Wenn Sie über kein Speicherkonto verfügen, erstellen Sie anhand des folgenden Beispiels ein Speicherkonto.

```powershell
# Creates a new resource group, storage account, and container
New-AzResourceGroup -name myResourceGroupName -Location WestUS
New-AzStorageAccount -ResourceGroupName myResourceGroupName -Name myStorageAccountName -SkuName 'Standard_LRS' -Location 'WestUs' | New-AzStorageContainer -Name guestconfiguration -Permission Blob
```

Der Befehl `Publish-GuestConfigurationPackage` lädt das Konfigurationspaket in Azure Blob Storage hoch und ruft ein SAS-Token ab, damit sicher darauf zugegriffen werden kann.

Parameter des Cmdlets `Publish-GuestConfigurationPackage`:

- **Pfad**: Speicherort des Pakets, das veröffentlicht werden soll
- **ResourceGroupName**: Name der Ressourcengruppe, in der sich das Speicherkonto befindet
- **StorageAccountName**: Der Name des Speicherkontos, in dem das Paket veröffentlicht werden soll
- **StorageContainerName:** (Standard: _guestconfiguration_) Name des Speichercontainers im Speicherkonto
- **Force:** Vorhandenes Paket im Speicherkonto mit demselben Namen überschreiben

Im nachfolgenden Beispiel wird das Paket im Speichercontainer „guestconfiguration“ veröffentlicht.

```powershell
Publish-GuestConfigurationPackage -Path ./MyConfig.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName | % ContentUri
```

## <a name="next-steps"></a>Nächste Schritte

- [Testen Sie das Paketartefakt](./guest-configuration-create-test.md) über Ihre Entwicklungsumgebung.
- Verwenden Sie das Modul `GuestConfiguration` zum [Erstellen einer Azure Policy-Definition](./guest-configuration-create-definition.md) für eine bedarfsorientierte Verwaltung Ihrer Umgebung.
- [Weisen Sie Ihre benutzerdefinierten Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Erfahren Sie, wie Sie [Details zur Konformität für die Gastkonfiguration](./determine-non-compliance.md#compliance-details-for-guest-configuration) für Richtlinienzuweisungen anzeigen können.
