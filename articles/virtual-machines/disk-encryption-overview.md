---
title: Übersicht über Verschlüsselungsoptionen für verwaltete Datenträger
description: Übersicht über Verschlüsselungsoptionen für verwaltete Datenträger
author: msmbaldwin
ms.date: 06/05/2021
ms.topic: conceptual
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 989a7fcc058284c1c706cbfc30c97a77200de111
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315310"
---
# <a name="overview-of-managed-disk-encryption-options"></a>Übersicht über Verschlüsselungsoptionen für verwaltete Datenträger

Für Ihre verwalteten Datenträger stehen verschiedene Arten von Verschlüsselung zur Verfügung, z. B. Azure Disk Encryption (ADE), serverseitige Verschlüsselung (Server-Side Encryption, SSE) und Verschlüsselung auf dem Host.

- **Azure Disk Encryption** unterstützt Sie beim Schutz Ihrer Daten gemäß den Sicherheits- und Complianceanforderungen Ihrer Organisation. Von ADE wird Volumeverschlüsselung für das Betriebssystem und die Datenträger von virtuellen Azure-Computern (VMs) bereitgestellt. Hierfür wird das Feature [DM-Crypt](https://wikipedia.org/wiki/Dm-crypt) von Linux oder das [BitLocker](https://wikipedia.org/wiki/BitLocker)-Feature von Windows verwendet. ADE ist in Azure Key Vault integriert, um Ihnen die Steuerung und Verwaltung der Datenträgerverschlüsselungsschlüssel und -geheimnisse zu erleichtern.  Ausführliche Informationen finden Sie unter [Azure Disk Encryption für Linux-VMs](./linux/disk-encryption-overview.md) und [Azure Disk Encryption für virtuelle Windows-Computer](./windows/disk-encryption-overview.md).

- Bei der **serverseitigen Verschlüsselung** (auch als Verschlüsselung ruhender Daten oder Azure Storage-Verschlüsselung bezeichnet) werden Daten, die auf verwalteten Azure-Datenträgern (Betriebssystemdatenträger und Datenträger mit Daten) gespeichert sind, beim Speichern in der Cloud automatisch verschlüsselt.  Ausführliche Informationen finden Sie unter [Serverseitige Verschlüsselung von Azure Disk Storage](./disk-encryption.md).

- Mit der **Verschlüsselung auf dem Host** wird sichergestellt, dass die auf dem VM-Host gespeicherten Daten im Ruhezustand verschlüsselt und verschlüsselt an den Speicherdienst übermittelt werden. Datenträger mit aktivierter Verschlüsselung auf dem Host werden nicht mit SSE verschlüsselt. Stattdessen stellt der Server, der Ihren virtuellen Computer hostet, die Verschlüsselung für Ihre Daten bereit, und die verschlüsselten Daten werden an Azure Storage übermittelt. Ausführliche Informationen finden Sie unter [Verschlüsselung auf dem Host: End-to-End-Verschlüsselung für Ihre VM-Daten](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="comparison"></a>Vergleich

Nachfolgend werden SSE, ADE und Verschlüsselung auf dem Host verglichen.

| | Verschlüsselung ruhender Daten (Betriebssystemdatenträger und Datenträger mit Daten) | Verschlüsselung temporärer Datenträger | Verschlüsselung von Caches | Verschlüsselte Datenflüsse zwischen Computeressource und Speicher | Kundenseitige Kontrolle von Schlüsseln | Status der Datenträgerverschlüsselungverschlüsselung in Microsoft Defender für Cloud |
|--|--|--|--|--|--|--|
| **Verschlüsselung ruhender Daten mit plattformseitig verwaltetem Schlüssel (SSE+PMK)** | &#x2705; | &#10060; | &#10060; | &#10060; | &#10060; | Fehlerhaft, nicht zutreffend, wenn ausgenommen |
| **Verschlüsselung ruhender Daten mit kundenseitig verwaltetem Schlüssel (SSE+CMK)** | &#x2705; | &#10060; | &#10060; | &#10060; | &#x2705; | Fehlerhaft, nicht zutreffend, wenn ausgenommen |
| **Azure-Datenträgerverschlüsselung** | &#x2705; | &#x2705; | &#x2705; | &#x2705; | &#x2705; | Healthy |
| **Verschlüsselung auf dem Host**  | &#x2705; | &#x2705; | &#x2705; | &#x2705; | &#x2705; | Fehlerhaft, nicht zutreffend, wenn ausgenommen |

> [!Important]
> Bei der Verschlüsselung auf dem Host erkennt Microsoft Defender für Cloud den Verschlüsselungsstatus nicht.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Disk Encryption für Linux-VMs](./linux/disk-encryption-overview.md)
- [Azure Disk Encryption für Windows-VMs](./windows/disk-encryption-overview.md)
- [Serverseitige Verschlüsselung von Azure Disk Storage](./disk-encryption.md)
- [Verschlüsselung auf dem Host](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Übersicht über die Azure-Verschlüsselung](../security/fundamentals/encryption-overview.md)
