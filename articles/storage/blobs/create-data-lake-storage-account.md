---
title: Erstellen eines Speicherkontos für Azure Data Lake Storage Gen2
description: Erfahren Sie, wie Sie ein Speicherkonto für die Verwendung mit Azure Data Lake Storage Gen2 erstellen.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 04/27/2021
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: a2d9c23e5a6819c9e74cea10e78caab242d5cb35
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600321"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Erstellen eines Speicherkontos für die Verwendung mit Azure Data Lake Storage Gen2

Um die Funktionen von Data Lake Storage Gen2 zu verwenden, erstellen Sie ein Speicherkonto mit einem hierarchischen Namespace.

Eine Schrittanleitung finden Sie unter [Erstellen eines Speicherkontos](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

Stellen Sie beim Erstellen des Kontos sicher, dass Sie die in diesem Artikel beschriebenen Optionen auswählen.

## <a name="choose-a-storage-account-type"></a>Auswählen eines Speicherkontotyps

Die Funktionen von Data Lake Storage werden von den folgenden Typen von Speicherkonten unterstützt:

- Standard, Universell V2
- Premium, Blockblob

Informationen zum Auswählen zwischen diesen Optionen finden Sie unter [Übersicht über Azure Storage-Konten](../common/storage-account-overview.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

Auf der Seite **Speicherkonto erstellen** können Sie auf der Registerkarte **Grundlagen** zwischen diesen beiden Kontotypen wählen.

Wählen Sie **Standard** aus, um ein Standard-Konto vom Typ „Universell V2“ zu erstellen.

Wählen Sie **Premium** aus, um ein Premium-Konto vom Typ „Blockblob“ zu erstellen. Wählen Sie dann in der Dropdownliste **Premium-Kontotyp** die Option **Blockblobs** aus.

> [!div class="mx-imgBorder"]
> ![Option für Premium-Blockblob](./media/create-data-lake-storage-account/premium-block-blob-option.png)

## <a name="enable-the-hierarchical-namespace"></a>Aktivieren des hierarchischen Namespace

Sie aktivieren Data Lake Storage-Funktionen, indem Sie auf der Seite **Speicherkonto erstellen** auf der Registerkarte **Erweitert** die Einstellung **Hierarchischer Namespace aktivieren** auswählen. Diese Einstellung müssen Sie beim Erstellen des Kontos aktivieren. Später können Sie sie nicht mehr aktivieren.

In der folgenden Abbildung wird diese Einstellung auf der Seite **Speicherkonto erstellen** gezeigt.

> [!div class="mx-imgBorder"]
> ![Einstellung „Hierarchischer Namespace“](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Wenn Sie ein vorhandenes Speicherkonto mit Data Lake Storage verwenden möchten und die Einstellung „Hierarchischer Namespace“ deaktiviert ist, müssen Sie die Daten zu einem neuen Speicherkonto migrieren, in dem die Einstellung aktiviert ist.

> [!NOTE]
> **Datenschutz** und hierarchischer Namespace können nicht gleichzeitig aktiviert werden.

## <a name="next-steps"></a>Nächste Schritte

- [Speicherkontoübersicht](../common/storage-account-overview.md)
- [Verwenden von Azure Data Lake Storage Gen2 für Big Data-Anforderungen](data-lake-storage-data-scenarios.md)
- [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
