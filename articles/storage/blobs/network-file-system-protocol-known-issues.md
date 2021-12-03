---
title: Bekannte Probleme mit NFS 3.0 in Azure Blob Storage
description: Erfahren Sie mehr über Einschränkungen und bekannte Probleme bei der Unterstützung des Protokolls NFS 3.0 (Network File System) in Azure Blob Storage.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 13808d38a4ddb7e00af64b02df390eee1e1f9469
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421967"
---
# <a name="known-issues-with-network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Bekannte Probleme bei der Unterstützung des Network File System 3.0-Protokolls (NFS) in Azure Blob Storage

In diesem Artikel werden Einschränkungen und bekannte Probleme bei der Unterstützung des Protokolls NFS 3.0 (Network File System) in Azure Blob Storage beschrieben.

> [!IMPORTANT]
> Da Sie das Feature Ihres Kontos für hierarchische Namespaces für die Verwendung von NFS 3.0 aktivieren müssen, gelten alle bekannten Probleme, die im Artikel [Bekannte Probleme mit Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md) beschrieben werden, auch für Ihr Konto.

## <a name="nfs-30-support"></a>Unterstützung von NFS 3.0

- Die Unterstützung von NFS 3.0 kann für vorhandene Speicherkonten nicht aktiviert werden.

- Die Unterstützung von NFS 3.0 kann in einem Speicherkonto nicht mehr deaktiviert werden, nachdem Sie sie aktiviert haben.

- GRS-, GZRS- und RA-GRS-Redundanzoptionen werden nicht unterstützt, wenn Sie ein NFS 3.0-Speicherkonto erstellen.

## <a name="nfs-30-features"></a>Funktionen von NFS 3.0

Die folgenden Features von NFS 3.0 werden noch nicht unterstützt.

- NFS 3.0 über UDP. Nur NFS 3.0 über TCP wird unterstützt.

- Sperren von Dateien mithilfe des Netzwerksperrungs-Managers (NLM). Einbindungsbefehle müssen den `-o nolock`-Parameter enthalten.

- Einbinden von Unterverzeichnissen Sie können nur das Stammverzeichnis (Container) einbinden.

- Auflisten von Einbindungen (z. B. mit dem Befehl `showmount -a`)

- Auflisten von Exporten (z. B. mit dem Befehl `showmount -e`)

- Fester Link

- Schreibgeschütztes Exportieren eines Containers

## <a name="nfs-30-clients"></a>NFS 3.0-Clients

Windows-Client für NFS wird noch nicht unterstützt

## <a name="blob-storage-features"></a>Blob Storage-Features

Wenn Sie die NFS 3.0-Protokollunterstützung aktivieren, werden einige Blob Storage-Features vollständig unterstützt, andere möglicherweise nicht auf Vorschauebene oder noch gar nicht.

Informationen dazu, wie die einzelnen Blob-Storage-Features in Konten unterstützt werden, für die NFS 3.0-Unterstützung aktiviert ist, finden Sie unter [Unterstützung von Blob-Storage-Features in Azure Storage-Konten](storage-feature-support-in-storage-accounts.md).

## <a name="see-also"></a>Weitere Informationen

- [Unterstützung für Network File System 3.0 (NFS) in Azure Blob Storage](network-file-system-protocol-support.md)
- [Einbinden von Azure Blob Storage mithilfe des NFS 3.0-Protokolls (Network File System)](network-file-system-protocol-support-how-to.md)
