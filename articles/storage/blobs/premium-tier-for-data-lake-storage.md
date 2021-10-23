---
title: Premium-Tarif für Azure Data Lake Storage
description: Informationen zur Leistungsstufe Premium für Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: normesta
ms.openlocfilehash: e9cab4b65da62bddd47cdab97c6f586b07f379d4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044445"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Premium-Tarif für Azure Data Lake Storage

Azure Data Lake Storage Gen2 unterstützt jetzt [Premium-Blockblobspeicherkonten](storage-blob-block-blob-premium.md). Premium-Blockblobspeicherkonten eignen sich ideal für Anwendungen und Workloads zur Analyse von Big Data, die eine geringe konsistente Latenz erfordern und eine große Anzahl von Transaktionen unterstützen. Beispiele für Workloads sind interaktive Workloads, IoT, Streaminganalysen, künstliche Intelligenz und maschinelles Lernen. 

>[!TIP]
> Wenn Sie sich über die Leistung und die Kostenvorteile der Nutzung eines Premium-Blockblobspeicherkontos informieren und erfahren möchten, wie andere Kunden von Data Lake Storage Gen2 diesen Kontotyp genutzt haben, lesen Sie [Premium-Blockblobspeicherkonten](storage-blob-block-blob-premium.md).

## <a name="getting-started-with-premium"></a>Erste Schritte mit Premium

Überprüfen Sie zuerst, ob Ihre bevorzugten Blob Storage-Features mit Premium-Blockblobspeicherkonten kompatibel sind, und erstellen Sie dann das Konto. 

>[!NOTE]
> Sie können ein vorhandenes Speicherkonto vom Typ „Standard, Universell V2“ nicht in ein Premium-Blockblobspeicherkonto konvertieren. Zum Migrieren zu einem Premium-Blockblobspeicherkonto müssen Sie erst ein neues Premium-Blockblobspeicherkonto erstellen und dann die Daten dorthin migrieren. 

### <a name="check-for-blob-storage-feature-compatibility"></a>Überprüfen der Kompatibilität von Blob Storage-Features

Einige Blob Storage-Features werden in Premium-Blockblobspeicherkonten noch nicht oder nur teilweise unterstützt. Bevor Sie Premium auswählen, lesen Sie den Artikel [Unterstützung von Blob Storage-Features in Azure Storage-Konten](storage-feature-support-in-storage-accounts.md), um zu ermitteln, ob die Features, die Sie nutzen möchten, in Ihrem Konto vollständig unterstützt werden. Die Funktionsunterstützung wird ständig erweitert, daher sollten Sie diesen Artikel regelmäßig auf Updates überprüfen.

### <a name="create-a-new-storage-account"></a>Neues Speicherkonto erstellen

Erstellen Sie ein neues Azure Storage-Konto. Eine ausführliche Anleitung dazu finden Sie unter [Erstellen eines Speicherkontos](../common/storage-account-create.md). 

Wählen Sie während der Erstellung des Kontos die Leistungsoption **Premium** und den Kontotyp **Blockblobs** aus. 

> [!div class="mx-imgBorder"]
> ![Erstellen eines Blockblobspeicherkontos](./media/storage-blob-block-blob-premium/create-block-blob-storage-account.png)

Wenn Sie die Funktionen von Azure Data Lake Storage Gen2 nutzen möchten, aktivieren Sie auf der Seite **Speicherkonto erstellen** auf der Registerkarte **Erweitert** die Einstellung **Hierarchischer Namespace**. 

In der folgenden Abbildung wird diese Einstellung auf der Seite **Speicherkonto erstellen** gezeigt.

> [!div class="mx-imgBorder"]
> ![Einstellung „Hierarchischer Namespace“](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="next-steps"></a>Nächste Schritte

Nutzen Sie den Tarif Premium für Azure Data Lake Storage mit Ihrem bevorzugten Analysedienst wie etwa Azure Databricks, Azure HDInsight und Azure Synapse Analytics. Lesen Sie [Tutorials zur Verwendung von Azure-Diensten mit Azure Data Lake Storage Gen2](data-lake-storage-integrate-with-services-tutorials.md).
