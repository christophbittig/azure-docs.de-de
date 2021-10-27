---
title: Systemanforderungen für Microsoft Azure Data Box-Datenträger | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie mehr über die Software- und Netzwerkanforderungen für Ihre Azure Data Box-Datenträger.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 10/07/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 171297be3e0e8e5215c5c551e984a45d0516507e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003154"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Systemanforderungen für Azure Data Box Disk

In diesem Artikel werden wichtige Systemanforderungen für Ihre Microsoft Azure Data Box-Datenträgerlösung und die Clients beschrieben, die mit dem Data Box-Datenträger verbunden sind. Sie sollten die Informationen sorgfältig lesen, bevor Sie Ihren Data Box-Datenträger bereitstellen. Auch später sollten Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

Die Systemanforderungen enthalten die unterstützten Plattformen für Clients, die eine Verbindung mit Datenträgern, unterstützten Speicherkonten und Speichertypen herstellen.

::: zone-end

::: zone target="chromeless"

## <a name="review-prerequisites"></a>Überprüfen der Voraussetzungen

1. Sie müssen Ihre Bestellung für Data Box Disk unter [Tutorial: Order your Azure Data Box Disk](data-box-disk-deploy-ordered.md) (Tutorial: Bestellen von Azure Data Box Disk) aufgegeben haben. Sie haben Ihre Datenträger und ein Verbindungskabel pro Datenträger erhalten.
2. Sie haben einen Clientcomputer, von dem Sie die Daten kopieren können. Ihr Client-Computer muss folgende Voraussetzungen erfüllen:

    - Er muss über ein unterstütztes Betriebssystem verfügen.
    - Andere erforderliche Software muss installiert sein.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Unterstützte Betriebssysteme für Clients

Im Folgenden finden Sie eine Liste der unterstützten Betriebssysteme für Datenträgerentsperrungen und Datenkopiervorgänge über die Clients, die mit dem Data Box-Datenträger verbunden sind.

| **Betriebssystem** | **Getestete Version** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64 Bit) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Andere erforderliche Software für Windows-Clients

Für den Windows-Client sollte darüber hinaus noch Folgendes installiert sein.

| **Software**| **Version** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5,1|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Andere erforderliche Software für Linux-Clients

Für den Linux-Client installiert das Data Box-Datenträgertoolset die folgende erforderliche Software:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>Unterstützte Verbindung

Der Clientcomputer, auf dem sich die Daten befinden, muss über einen USB 3.0-Anschluss (oder höher) verfügen. Die Datenträger werden mit dem bereitgestellten Kabel an diesen Client angeschlossen.

## <a name="supported-storage-accounts"></a>Unterstützte Speicherkonten

Im Folgenden finden Sie eine Liste der unterstützten Speichertypen für den Data Box-Datenträger.

| **Speicherkonto** | **Unterstützte Zugriffsebenen** |
| --- | --- |
| Klassisch Standard | |
| Universell V1 Standard  | Heiße Ebene, kalte Ebene |
| Universell V1 Premium   |  |
| Universell V2 Standard<sup>*</sup> | Heiße Ebene, kalte Ebene |
| Universell V2 Premium   |  |
| Blob-Speicherkonto | |

<sup>*</sup> *Azure Data Lake Storage Gen2 (ADLS Gen2) wird unterstützt.*

> [!IMPORTANT]
> Die Unterstützung für das NFS 3.0-Protokoll (Network File System, Netzwerkdateisystem) in Azure Blog Storage wird nicht mit Data Box Disk unterstützt.

## <a name="supported-storage-types-for-upload"></a>Unterstützte Speichertypen für den Upload

Im Folgenden finden Sie eine Liste der Speichertypen, die für den Upload in Azure mithilfe von Data Box Disk unterstützt werden.

| **Dateiformat** | **Hinweise** |
| --- | --- |
| Azure-Blockblob | |
| Azure-Seitenblob  | |
| Azure Files  | |
| Managed Disks | |

::: zone target="docs"

## <a name="next-step"></a>Nächster Schritt

* [Bereitstellen des Azure Data Box-Datenträgers](data-box-disk-deploy-ordered.md)

::: zone-end

