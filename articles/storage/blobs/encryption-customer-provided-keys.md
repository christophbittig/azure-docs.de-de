---
title: Angeben eines Verschlüsselungsschlüssels bei Richten einer Anforderung an Blob Storage
titleSuffix: Azure Storage
description: Clients, die Anforderungen an Azure Blob Storage richten, haben die Möglichkeit, einen Verschlüsselungsschlüssel anforderungsbezogen anzugeben. Die Einbeziehung des Verschlüsselungsschlüssels in die Anforderung ermöglicht eine präzise Steuerung der Verschlüsselungseinstellungen für Blob-Speichervorgänge.
services: storage
author: tamram
ms.service: storage
ms.date: 12/14/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: b0f11be9261785ca332d47a7406133e3106547fd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471197"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage"></a>Angeben eines Verschlüsselungsschlüssels bei Richten einer Anforderung an Blob Storage

Clients, die Anforderungen an Azure Blob Storage richten, haben die Möglichkeit, einen AES-256-Verschlüsselungsschlüssel anforderungsbezogen anzugeben. Die Einbeziehung des Verschlüsselungsschlüssels in die Anforderung ermöglicht eine präzise Steuerung der Verschlüsselungseinstellungen für Blob-Speichervorgänge. Vom Kunden bereitgestellte Schlüssel können in Azure Key Vault oder einem anderen Schlüsselspeicher gespeichert werden.

## <a name="encrypting-read-and-write-operations"></a>Verschlüsseln von Lese-und Schreibvorgängen

Wenn eine Clientanwendung einen Verschlüsselungsschlüssel auf die Anforderung hin bereitstellt, führt Azure Storage die Verschlüsselung und Entschlüsselung beim Lesen und Schreiben von Blobdaten transparent aus. In Azure Storage wird neben den Inhalten des Blobs ein SHA-256-Hash des Verschlüsselungsschlüssels geschrieben. Der Hash wird verwendet, um zu überprüfen, ob alle nachfolgenden Vorgänge für das Blob denselben Verschlüsselungsschlüssel verwenden.

Azure Storage speichert oder verwaltet nicht den Verschlüsselungsschlüssel, den der Client mit der Anforderung sendet. Nach Abschluss des Verschlüsselungs- oder Entschlüsselungsprozesses wird der Schlüssel sicher verworfen.

Wenn ein Client ein Blob erstellt oder aktualisiert und dazu für die Anforderung einen vom Kunden verwalteten Schlüssel verwendet, müssen nachfolgende Lese- und Schreibanforderungen für dieses Blob den Schlüssel ebenfalls bereitstellen. Wenn der Schlüssel bei einer Anforderung für ein Blob, das mit einem vom Kunden bereitgestellten Schlüssel bereits verschlüsselt wurde, nicht bereitgestellt wird, schlägt die Anforderung mit dem Fehlercode 409 (Konflikt) fehl.

Wenn die Clientanwendung bei der Anforderung einen Verschlüsselungsschlüssel sendet und das Speicherkonto mit einem von Microsoft oder vom Kunden verwalteten Schlüssel ebenfalls verschlüsselt wurde, verwendet Azure Storage den bei der Anforderung bereitgestellten Schlüssel zur Verschlüsselung und Entschlüsselung.

Zum Senden des Verschlüsselungsschlüssels als Teil der Anforderung muss ein Client über HTTPS eine sichere Verbindung zu Azure Storage herstellen.

Jede Blobmomentaufnahme kann einen eigenen Verschlüsselungsschlüssel haben.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Anforderungsheader zur Angabe von vom Kunden bereitgestellten Schlüsseln

Für REST-Aufrufe können Clients mithilfe der folgenden Header Informationen zum Verschlüsselungsschlüssel bei einer Anforderung an den Blobspeicher sicher übergeben:

|Anforderungsheader | BESCHREIBUNG |
|---------------|-------------|
|`x-ms-encryption-key` |Erforderlich bei Schreib- und Leseanforderungen. Ein Base64-codierter AES-256-Verschlüsselungsschlüsselwert. |
|`x-ms-encryption-key-sha256`| Erforderlich bei Schreib- und Leseanforderungen. Der Base64-codierte SHA256 des Verschlüsselungsschlüssels. |
|`x-ms-encryption-algorithm` | Erforderlich bei Schreibanforderungen, optional bei Leseanforderungen. Gibt den Algorithmus an, der beim Verschlüsseln von Daten mit dem angegebenen Schlüssel verwendet werden soll.  Der Wert dieses Headers muss auf `AES256` festgelegt sein. |

Die Angabe von Verschlüsselungsschlüsseln bei der Anforderung ist optional. Wenn Sie aber einen der oben aufgeführten Header für einen Schreibvorgang angeben, müssen Sie alle Schlüssel angeben.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Blob-Speichervorgänge, die vom Kunden bereitgestellte Schlüssel unterstützen

Die folgenden Blob-Speichervorgänge unterstützen das Senden von vom Kunden bereitgestellten Verschlüsselungsschlüsseln bei einer Anforderung:

- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Block List](/rest/api/storageservices/put-block-list)
- [Put Block](/rest/api/storageservices/put-block)
- [Put Block from URL](/rest/api/storageservices/put-block-from-url)
- [Put Page](/rest/api/storageservices/put-page)
- [Put Page from URL](/rest/api/storageservices/put-page-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Set Blob Properties](/rest/api/storageservices/set-blob-properties)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Rotieren der vom Kunden bereitgestellten Schlüssel

Um einen Verschlüsselungsschlüssel, der zur Verschlüsselung eines Blobs verwendet wurde, zu rotieren, laden Sie das Blob herunter und dann mit dem neuen Verschlüsselungsschlüssel erneut hoch.

> [!IMPORTANT]
> Das Azure-Portal kann nicht zum Lesen aus oder Schreiben in einen Container oder ein Blob verwendet werden, der bzw. das mit einem Schlüssel verschlüsselt wird, der bei der Anforderung bereitgestellt wurde.
>
> Schützen Sie unbedingt den Verschlüsselungsschlüssel, den Sie bei einer Anforderung in einem Blobspeicher bereitstellen, in einem sicheren Schlüsselspeicher wie Azure Key Vault. Wenn Sie versuchen, einen Schreibvorgang für einen Container oder ein Blob ohne den Verschlüsselungsschlüssel auszuführen, schlägt der Vorgang fehl, und Sie verlieren Ihren Zugriff auf das Objekt.

## <a name="feature-support"></a>Featureunterstützung

In der folgenden Tabelle wird gezeigt, wie dieses Feature in Ihrem Konto unterstützt wird und welche Auswirkungen die Aktivierung bestimmter Funktionen auf den Support hat. 

| Speicherkontotyp                | Blob Storage (Standardunterstützung)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>    
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Standard, Universell V2 | ![Ja](../media/icons/yes-icon.png) |![Nein](../media/icons/no-icon.png)              | ![Nein](../media/icons/no-icon.png) | 
| Premium-Blockblobs          | ![Ja](../media/icons/yes-icon.png) |![Nein](../media/icons/no-icon.png)              | ![Nein](../media/icons/no-icon.png) |

<sup>1</sup>    Für Data Lake Storage Gen2 und das NFS 3.0-Protokoll (Network File System) ist ein Speicherkonto mit aktiviertem hierarchischem Namespace erforderlich.

## <a name="next-steps"></a>Nächste Schritte

- [Angeben eines vom Kunden bereitgestellten Schlüssels für eine Anforderung an Blob Storage mit .NET](storage-blob-customer-provided-key.md)
- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](../common/storage-service-encryption.md)
