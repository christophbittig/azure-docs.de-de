---
title: Verschlüsselung in Azure Backup
description: Hier erfahren Sie mehr darüber, wie Verschlüsselungsfeatures in Azure Backup Sie dabei unterstützen, Ihre Sicherungsdaten zu schützen und die Sicherheitsanforderungen Ihres Unternehmens zu erfüllen.
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: 2e32bb880e42aad72a526e7515ba9156de75de9a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233054"
---
# <a name="encryption-in-azure-backup"></a>Verschlüsselung in Azure Backup

Alle Ihre gesicherten Daten werden automatisch verschlüsselt, wenn sie in der Cloud mit Azure Storage-Verschlüsselung gespeichert werden. Dies hilft Ihnen, Ihre Sicherheits- und Complianceverpflichtungen zu erfüllen. Ruhende Daten werden mit der AES-256-Verschlüsselung – einer der stärksten verfügbaren Blockchiffren – verschlüsselt und sind mit dem FIPS 140-2-Standard konform. Zusätzlich zur Verschlüsselung ruhender Daten werden alle Ihre Sicherungsdaten während der Übertragung über HTTPS übertragen. Sie bleiben immer im Azure-Backbone-Netzwerk.

## <a name="levels-of-encryption-in-azure-backup"></a>Verschlüsselungsebenen in Azure Backup

Azure Backup umfasst Verschlüsselung auf zwei Ebenen:

- **Verschlüsselung von Daten im Recovery Services-Tresor**
  - **Verwendung plattformseitig verwalteter Schlüssel:** Standardmäßig werden alle Ihre Daten mit von der Plattform verwalteten Schlüsseln verschlüsselt. Sie müssen Ihrerseits keine besonderen Maßnahmen ergreifen, um diese Verschlüsselung zu aktivieren. Dies gilt für alle Workloads, die in Ihrem Recovery Services-Tresor gesichert werden.
  - **Verwendung kundenseitig verwalteter Schlüssel:** Wenn Sie Ihre Azure-VMs sichern, können Sie Ihre Daten mithilfe von Verschlüsselungsschlüsseln verschlüsseln, die Sie besitzen und verwalten. Mit Azure Backup können Sie die in Azure Key Vault gespeicherten RSA-Schlüssel zum Verschlüsseln Ihrer Sicherungen verwenden. Der Verschlüsselungsschlüssel, der zum Verschlüsseln von Sicherungen verwendet wird, kann sich von dem für die Quelle verwendeten Verschlüsselungsschlüssel unterscheiden. Die Daten werden mithilfe eines AES-256 basierten Datenverschlüsselungsschlüssels (DEK) geschützt, der wiederum mit Ihren Schlüsseln geschützt wird. Dadurch erhalten Sie vollständige Kontrolle über die Daten und Schlüssel. Sie müssen dem Recovery Services-Tresor Zugriff auf den Verschlüsselungsschlüssel in Azure Key Vault gewähren, um eine Verschlüsselung zu ermöglichen. Bei Bedarf können Sie den Schlüssel deaktivieren oder den Zugriff widerrufen. Sie müssen jedoch die Verschlüsselung mit ihren Schlüsseln aktivieren, bevor Sie versuchen, Elemente im Tresor zu schützen. [Hier erhalten Sie weitere Informationen](encryption-at-rest-with-cmk.md).
  - **Verschlüsselung auf Infrastrukturebene:** Neben der Verschlüsselung Ihrer Daten im Recovery Services-Tresor unter Verwendung kundenseitig verwalteter Schlüssel können Sie auch eine zusätzliche, für die Speicherinfrastruktur konfigurierte Verschlüsselungsebene verwenden. Diese Infrastrukturverschlüsselung wird von der Plattform verwaltet. In Kombination mit der Verschlüsselung von ruhenden Daten unter Verwendung kundenseitig verwalteter Schlüssel ermöglicht sie eine zweischichtige Verschlüsselung Ihrer Sicherungsdaten. Die Infrastrukturverschlüsselung kann nur konfiguriert werden, wenn Sie sich zuvor dafür entscheiden, Ihre eigenen Schlüssel für die Verschlüsselung von ruhenden Daten zu verwenden. Bei der Infrastrukturverschlüsselung werden plattformseitig verwaltete Schlüssel für die Datenverschlüsselung verwendet.
- **Spezifische Verschlüsselung für die gesicherte Workload**  
  - **Sicherung von Azure-VMs:** Azure Backup unterstützt die Sicherung von VMs mit Datenträgern, die mit [plattformseitig verwalteten Schlüsseln](../virtual-machines/disk-encryption.md#platform-managed-keys) verschlüsselt werden sowie mit [kundenseitig verwalteten Schlüsseln](../virtual-machines/disk-encryption.md#customer-managed-keys), die Sie besitzen und verwalten. Darüber hinaus können Sie auch virtuelle Azure-Computer sichern, deren Datenträger für Betriebssystem oder Daten mithilfe von [Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade) verschlüsselt werden. ADE verwendet BitLocker für Windows-VMs bzw. DM-Crypt für Linux-VMs, um eine Verschlüsselung auf Gastsystemen durchzuführen.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](../storage/common/storage-service-encryption.md)
- Wenn Sie Fragen zum Thema Verschlüsselung haben, sehen Sie sich die [häufig gestellten Fragen zu Azure Backup](./backup-azure-backup-faq.yml) an.
