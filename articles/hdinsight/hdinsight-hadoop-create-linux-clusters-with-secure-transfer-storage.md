---
title: Apache Hadoop und Speicher mit sicherer Übertragung – Azure HDInsight
description: Hier erfahren Sie, wie Sie HDInsight-Cluster mit Azure-Speicherkonten mit sicherer Übertragung erstellen.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: deb10f2b3e4e2b5e7d911992a601f66e1e557268
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211649"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop-Cluster mit Speicherkonten mit sicherer Übertragung in Azure HDInsight

Das Feature [Sichere Übertragung erforderlich](../storage/common/storage-require-secure-transfer.md) erhöht die Sicherheit Ihres Azure Storage-Kontos, indem alle Anforderungen für Ihr Konto über eine sichere Verbindung übertragen werden müssen. Dieses Feature und das WASB-Schema werden erst ab HDInsight-Clusterversion 3.6 unterstützt.

> [!IMPORTANT]
> Das Aktivieren der sicheren Speicherübertragung nach dem Erstellen eines Clusters kann zu Fehlern bei der Verwendung Ihres Speicherkontos führen und wird daher nicht empfohlen. Es ist besser, einen neuen Cluster mit einem Speicherkonto zu erstellen, bei dem die sichere Übertragung bereits aktiviert ist.

## <a name="storage-accounts"></a>Speicherkonten

### <a name="azure-portal"></a>Azure-Portal

Standardmäßig ist die Eigenschaft für erforderliche sichere Übertragung aktiviert, wenn Sie ein Speicherkonto im Azure-Portal erstellen.

Informationen zum Aktualisieren eines vorhandenen Speicherkontos über das Azure-Portal finden Sie unter [Erfordern sicherer Übertragung über das Azure-Portal](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

Stellen Sie für das PowerShell-Cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) sicher, dass der Parameter `-EnableHttpsTrafficOnly` auf `1` festgelegt ist.

Informationen zum Aktualisieren eines vorhandenen Speicherkontos mit PowerShell finden Sie unter [Erfordern sicherer Übertragung mit PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>Azure CLI

Stellen Sie für den Azure CLI-Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create) sicher, dass der Parameter `--https-only` auf `true` festgelegt ist.

Informationen zum Aktualisieren eines vorhandenen Speicherkontos über die Azure-Befehlszeilenschnittstelle finden Sie unter [Erfordern sicherer Übertragung über die Azure-Befehlszeilenschnittstelle](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

### <a name="secure-transfer-errors"></a>Fehler bei der sicheren Übertragung


Wenn Sie nach dem Erstellen des HDInsight-Clusters versehentlich die Option „Require secure transfer“ (Sichere Übertragung erforderlich) aktiviert haben, werden möglicherweise Fehlermeldungen wie die folgende angezeigt:

`com.microsoft.azure.storage.StorageException: The account being accessed does not support http.`

Nur bei HBase-Clustern können Sie die folgenden Schritte ausführen, um die Clusterfunktionalität wiederherzustellen:
1. Beenden Sie HBase über Ambari.
2. Beenden Sie das HDFS über Ambari.
3. Navigieren Sie in Ambari zu HDFS --> Configs --> Advanced --> fs.defaultFS (HDFS --> Konfigurationen --> Erweitert --> fs.defaultFS).
4. Ändern Sie „wasb“ in „wasbs“, und speichern Sie diese Änderung.
5. Wenn Sie das Feature für beschleunigte Schreibvorgänge verwenden, muss bei den HBase-Konfigurationen bei „hbase.rootDir“ ebenfalls „wasb“ in „wasbs“ geändert werden.
6. Starten Sie alle erforderlichen Dienste neu.

## <a name="add-additional-storage-accounts"></a>Hinzufügen weiterer Speicherkonten

Weitere Speicherkonten mit sicherer Übertragung können auf unterschiedliche Weise hinzugefügt werden:

* Ändern Sie die Azure Resource Manager-Vorlage im letzten Abschnitt.
* Erstellen Sie über das [Azure-Portal](https://portal.azure.com) einen Cluster, und geben Sie ein verknüpftes Speicherkonto an.
* Verwenden Sie die Skriptaktion, um einem vorhandenen HDInsight-Cluster weitere Speicherkonten mit sicherer Übertragung hinzuzufügen. Weitere Informationen finden Sie unter [Hinzufügen zusätzlicher Speicherkonten zu HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Nächste Schritte

* Verwendung von Azure Storage (WASB) anstelle von [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) als Standarddatenspeicher
* Informationen zur Verwendung von Azure Storage durch HDInsight finden Sie unter [Verwenden von Azure Storage mit HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Informationen zum Hochladen von Daten in HDInsight finden Sie im Artikel zum [Hochladen von Daten für Hadoop-Aufträge in HDInsight](hdinsight-upload-data.md).
