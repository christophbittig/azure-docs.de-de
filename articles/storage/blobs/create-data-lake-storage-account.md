---
title: Erstellen eines Speicherkontos für Azure Data Lake Storage Gen2
description: Erfahren Sie, wie Sie ein Speicherkonto für die Verwendung mit Azure Data Lake Storage Gen2 erstellen.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 10/14/2021
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 35761e10cecf1cb209f004f99f773c09d91dc0e5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046865"
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

Sie aktivieren Data Lake Storage-Funktionen, indem Sie auf der Seite **Speicherkonto erstellen** auf der Registerkarte **Erweitert** die Einstellung **Hierarchischer Namespace aktivieren** auswählen. 

In der folgenden Abbildung wird diese Einstellung auf der Seite **Speicherkonto erstellen** gezeigt.

> [!div class="mx-imgBorder"]
> ![Einstellung „Hierarchischer Namespace“](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Informationen zum Aktivieren von Data Lake Storage-Funktionen für ein vorhandenes Konto finden Sie unter [Ein Upgrade von Azure Blob Storage mit Azure Data Lake Storage Gen2-Funktionen](upgrade-to-data-lake-storage-gen2-how-to.md).

> [!NOTE]
> **Datenschutz** und hierarchischer Namespace können nicht gleichzeitig aktiviert werden.

## <a name="next-steps"></a>Nächste Schritte

- [Speicherkontoübersicht](../common/storage-account-overview.md)
- [Upgrade von Azure Blob Storage mit Azure Data Lake Storage Gen2-Funktionen](upgrade-to-data-lake-storage-gen2-how-to.md)
- [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
