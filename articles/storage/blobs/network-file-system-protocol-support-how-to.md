---
title: Einbinden von Azure Blob Storage mithilfe des NFS 3.0-Protokolls | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Container über eine Azure-VM (virtueller Computer) oder einen lokal ausgeführten Client in Blob Storage mithilfe des NFS 3.0-Protokolls einbinden.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6b22b99cdd883ed8dedb90f925a918fe9c25d9bb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444874"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol"></a>Einbinden von Azure Blob Storage mithilfe des NFS 3.0-Protokolls (Network File System)

Sie können einen Container in Blob-Speicher von einem Linux-basierten virtuellen Azure-Computer (VM) oder einem Linux-System aus, das lokal ausgeführt wird, mithilfe des NFS 3.0-Protokolls einbinden. Dieser Artikel bietet eine schrittweise Anleitung. Weitere Informationen zur Unterstützung des NFS 3.0-Protokolls in Blob Storage finden Sie unter [Unterstützung für Network File System 3.0 (NFS) in Azure Blob Storage](network-file-system-protocol-support.md).

## <a name="step-1-create-an-azure-virtual-network-vnet"></a>Schritt 1: Erstellen eines virtuellen Azure-Netzwerks (VNet)

Ihr Speicherkonto muss in einem VNet sein. Ein VNet ermöglicht Clients eine sichere Verbindung mit Ihrem Speicherkonto. Weitere Informationen zum VNet und wie Sie eins erstellen, finden Sie in der [Dokumentation zu virtuellen Netzwerken](../../virtual-network/index.yml).

> [!NOTE]
> Clients im selben VNet können Container in Ihr Konto einbinden. Sie können auch einen Container von einem Client aus einbinden, der in einem lokalen Netzwerk ausgeführt wird, doch Sie müssen zunächst Ihr lokales Netzwerk mit Ihrem VNet verbinden. Siehe [Unterstützte Netzwerkverbindungen](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-2-configure-network-security"></a>Schritt 2: Konfigurieren der Netzwerksicherheit

Die einzige Möglichkeit zum Sichern der Daten in Ihrem Konto besteht darin, ein VNet und andere Netzwerksicherheitseinstellungen zu verwenden. Alle anderen Tools, die zum Sichern von Daten verwendet werden, wie Kontoschlüsselautorisierung, Azure Active Directory (AD) und Zugriffssteuerungslisten (Access Control Lists, ACLs), werden in Konten, für die die Unterstützung des NFS 3.0-Protokolls aktiviert ist, noch nicht unterstützt.

Informationen zum Sichern der Daten in Ihrem Konto finden Sie in den folgenden Empfehlungen: [Netzwerksicherheitsempfehlungen für Blob Storage](security-recommendations.md#networking).

## <a name="step-3-create-and-configure-a-storage-account"></a>Schritt 3: Erstellen und Konfigurieren eines Speicherkontos

Um einen Container mithilfe von NFS 3.0 einzubinden, müssen Sie ein Speicherkonto erstellen. Sie können vorhandene Konten nicht aktivieren.

Das NFS 3.0-Protokoll wird für Standardspeicherkonten vom Typ „Universell V2“ und für Blockblobspeicherkonten vom Typ „Premium“ unterstützt. Weitere Informationen zu diesen Speicherkontotypen finden Sie unter [Speicherkontoübersicht](../common/storage-account-overview.md).

Wenn Sie das Konto konfigurieren, wählen Sie die folgenden Werte aus:

|Einstellung | Premium-Leistung | Standardleistung
|----|---|---|
|Ort|Alle verfügbaren Regionen |Alle verfügbaren Regionen
|Leistung|Premium| Standard
|Kontoart|BlockBlobStorage| Allgemein v2
|Replikation|Lokal redundanter Speicher (LRS), zonenredundanter Speicher (ZRS)| Lokal redundanter Speicher (LRS), zonenredundanter Speicher (ZRS)
|Konnektivitätsmethode|Öffentlicher Endpunkt (ausgewählte Netzwerke) oder privater Endpunkt |Öffentlicher Endpunkt (ausgewählte Netzwerke) oder privater Endpunkt
|Hierarchischer Namespace|Aktiviert|Aktiviert
|NFS V3|Aktiviert |Aktiviert

Sie können die Standardwerte für alle anderen Einstellungen übernehmen.

## <a name="step-4-create-a-container"></a>Schritt 4: Erstellen eines Containers

Erstellen Sie einen Container in Ihrem Speicherkonto, indem Sie eins dieser Tools oder SDKs verwenden:

|Tools|SDKs|
|---|---|
|[Azure portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure-Befehlszeilenschnittstelle](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

> [!NOTE]
> Standardmäßig ist die Root-Squashoption eines neuen Containers `no root squash`. Sie können diese Option jedoch in `root squash` oder `all squash` ändern. Weitere Informationen zu diesen Squashoptionen finden Sie in der Dokumentation Ihres Betriebssystems.

Die folgende Abbildung zeigt die Squashoptionen, wie sie im Azure-Portal angezeigt werden.

> [!div class="mx-imgBorder"]
> ![Squashoptionen im Azure-Portal](./media/network-file-system-protocol-how-to/squash-options-azure-portal.png)

## <a name="step-5-mount-the-container"></a>Schritt 5: Einbinden des Containers

Erstellen Sie ein Verzeichnis auf Ihrem Linux-System, und binden Sie dann einen Container in das Speicherkonto ein.

1. Erstellen Sie auf einem Linux-System ein Verzeichnis.

   ```
   mkdir -p /mnt/test
   ```

2. Binden Sie einen Container mit dem folgenden Befehl ein.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Ersetzen Sie den Platzhalter `<storage-account-name>`, der in diesem Befehl vorkommt, durch den Namen Ihres Speicherkontos.

   - Ersetzen Sie den Platzhalter `<container-name>` durch den Namen Ihres Containers.

---

## <a name="resolve-common-errors"></a>Beheben allgemeiner Fehler

|Fehler | Ursache/Lösung|
|---|---|
|`Access denied by server while mounting`|Stellen Sie sicher, dass der Client in einem unterstützten Subnetz ausgeführt wird. Siehe [Unterstützte Netzwerkadressen](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Sie müssen den Befehl zum Einbinden und die zugehörigen Parameter direkt im Terminal eingeben. Wenn Sie diesen Befehl oder einen Teil davon aus einer anderen Anwendung kopieren und in das Terminal einfügen, kann dies dazu führen, dass ausgeblendete Zeichen in den eingefügten Informationen diesen Fehler verursachen. Dieser Fehler kann auch auftreten, wenn das Konto nicht für NFS 3.0 aktiviert ist. |
|`Permision denied`| Der Standardmodus eines neu erstellten NFS v3-Containers ist 0750. Nicht-Root-Benutzer haben keinen Zugriff auf das Volume. Wenn ein Zugriff von Nicht-Root-Benutzern erforderlich ist, muss der Root-Benutzer den Modus in 0755 ändern. Beispiel für einen Befehl: `sudo chmod 0755 /mnt/<newcontainer>`|
|`EINVAL ("Invalid argument"`) |Dieser Fehler kann auftreten, wenn ein Client versucht, folgende Aktionen durchzuführen:<li>Schreiben in ein Blob, das von einem Blobendpunkt erstellt wurde.<li>Löschen eines Blobs, das über eine Momentaufnahme verfügt oder sich in einem Container befindet, der eine aktive WORM-Richtlinie (Write Once, Read Many) aufweist.|
|`EROFS ("Read-only file system"`) |Dieser Fehler kann auftreten, wenn ein Client versucht, folgende Aktionen durchzuführen:<li>Schreiben in ein Blob oder Löschen ein Blobs, das über eine aktive Lease verfügt.<li>Schreiben in ein Blob oder Löschen eines Blobs in einem Container, der eine aktive WORM-Richtlinie (Write Once, Read Many) aufweist. |
|`NFS3ERR_IO/EIO ("Input/output error"`) |Dieser Fehler kann auftreten, wenn ein Client versucht, Attribute für Blobs zu lesen, zu schreiben oder zu festlegen, die in der Archivzugriffsebene gespeichert sind. |
|`OperationNotSupportedOnSymLink` Fehler| Dieser Fehler kann bei einem Schreibvorgang über eine Blob- oder Azure Data Lake Storage Gen2-API zurückgegeben werden. Die Verwendung dieser APIs zum Schreiben oder Löschen symbolischer Verknüpfungen, die mit NFS 3.0 erstellt werden, ist nicht zulässig. Sie müssen den NFS v3-Endpunkt verwenden, um mit symbolischen Verknüpfungen zu arbeiten. |
|`mount: /mnt/test: bad option;`| Installieren Sie das nfs-Hilfsprogramm mit dem Befehl **sudo apt install nfs-common**.|

## <a name="see-also"></a>Weitere Informationen

- [Unterstützung für Network File System 3.0 (NFS) in Azure Blob Storage](network-file-system-protocol-support.md)
- [Bekannte Probleme bei der Unterstützung des Network File System 3.0-Protokolls (NFS) in Azure Blob Storage](network-file-system-protocol-known-issues.md)
