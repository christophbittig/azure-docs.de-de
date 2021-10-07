---
title: Erstellen einer Azure HDInsight-Instanz – Azure Data Lake Storage Gen2 – Portal
description: Hier erfahren Sie, wie Sie über das Portal Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern verwenden können.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/07/2021
ms.openlocfilehash: 2014f0cde340b0070b8c8e5e7fbbd7c2ab6667fa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124797570"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Erstellen eines Clusters mit Data Lake Storage Gen2 über das Azure-Portal

Das Azure-Portal ist ein webbasiertes Verwaltungstool für Dienste und Ressourcen, die in der Microsoft Azure-Cloud gehostet werden. In diesem Artikel wird beschrieben, wie Sie mit dem Portal Linux-basierte Azure HDInsight-Cluster erstellen. Weitere Informationen finden Sie unter [Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka usw](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](includes/hdinsight-delete-cluster-warning.md)]

Wenn Sie kein Azure-Abonnement haben, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

Konfigurieren Sie mithilfe der folgenden Schritte ein Data Lake Storage Gen2-Konto, um ein Speicherkonto zu konfigurieren, das einen hierarchischen Namespace aufweist.

## <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität

Erstellen Sie eine benutzerseitig zugewiesene verwaltete Identität, falls noch keine vorhanden ist.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie links oben **Ressource erstellen** aus.
1. Geben Sie im Suchfeld den Suchbegriff **Vom Benutzer zugewiesen** ein, und klicken Sie auf **Benutzerseitig zugewiesene verwaltete Identität**.
1. Klicken Sie auf **Erstellen**.
1. Geben Sie einen Namen für Ihre verwaltete Identität ein, und wählen Sie das richtige Abonnement, die Ressourcengruppe und den Speicherort aus.
1. Klicken Sie auf **Erstellen**.

Weitere Informationen zur Funktionsweise verwalteter Identitäten in Azure HDInsight finden Sie unter [Verwaltete Identitäten in Azure HDInsight](hdinsight-managed-identities.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png" alt-text="Erstellen einer benutzerseitig zugewiesenen verwalteten Identität":::

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Erstellen eines Speicherkontos für die Verwendung mit Data Lake Storage Gen2

Erstellen Sie ein Speicherkonto für die Verwendung mit Azure Data Lake Storage Gen2.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie links oben **Ressource erstellen** aus.
1. Geben Sie im Suchfeld den Suchbegriff **Speicher** ein, und klicken Sie auf **Speicherkonto**.
1. Klicken Sie auf **Erstellen**.
1. Auf dem Bildschirm **Speicherkonto erstellen**:
    1. Wählen Sie das richtige Abonnement und die Ressourcengruppe aus.
    1. Geben Sie einen Namen für Ihr Speicherkonto mit Data Lake Storage Gen2 ein.
    1. Klicken Sie auf die Registerkarte **Erweitert**.
    1. Klicken Sie auf **Aktiviert** neben **Hierarchischer Namespace** unter **Data Lake Storage Gen2**.
    1. Klicken Sie auf **Überprüfen + erstellen**.
    1. Klicken Sie auf **Erstellen**

Weitere Informationen zu anderen Optionen während der Erstellung des Speicherkontos finden Sie unter [Schnellstart: Erstellen eines Speicherkontos für Azure Data Lake Storage Gen2](../storage/blobs/create-data-lake-storage-account.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png" alt-text="Screenshot zur Speicherkontoerstellung im Azure-Portal":::

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>Festlegen von Berechtigungen für verwaltete Identitäten in Data Lake Storage Gen2

Weisen Sie die verwaltete Identität der Rolle **Besitzer von Speicherblobdaten** im Speicherkonto zu.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto.
1. Wählen Sie Ihr Speicherkonto und dann **Zugriffssteuerung (IAM)** aus, um die Zugriffssteuerungseinstellungen für das Konto anzuzeigen. Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um die Liste mit den Rollenzuweisungen anzuzeigen.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png" alt-text="Screenshot mit Anzeige der Zugriffssteuerungseinstellungen für Speicher":::

1. Klicken Sie auf die Schaltfläche **+ Rollenzuweisung hinzufügen**, um eine neue Rolle hinzuzufügen.
1. Wählen Sie im Fenster **Rollenzuweisung hinzufügen** die Rolle **Besitzer von Speicherblobdaten** aus. Wählen Sie dann das Abonnement aus, das über die verwaltete Identität und das Speicherkonto verfügt. Suchen Sie als Nächstes die vom Benutzer zugewiesene verwaltete Identität, die Sie zuvor erstellt haben. Wählen Sie abschließend die verwaltete Identität aus, sodass sie unter **Ausgewählte Mitglieder** aufgelistet wird.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png" alt-text="Screenshot: Zuweisen einer Azure-Rolle":::

1. Wählen Sie **Speichern** aus. Die vom Benutzer zugewiesene Identität, die Sie ausgewählt haben, wird jetzt unter der ausgewählten Rolle aufgelistet.
1. Nachdem das anfängliche Setup abgeschlossen ist, können Sie einen Cluster über das Portal erstellen. Der Cluster muss sich in der gleichen Azure-Region befinden wie das Speicherkonto. Wählen Sie auf der Registerkarte **Speicher** des Menüs zur Clustererstellung die folgenden Optionen aus:

    * Wählen Sie für **Primärer Speichertyp** **Azure Data Lake Storage Gen2** aus.
    * Suchen Sie unter **Primäres Speicherkonto** das neu erstellte Speicherkonto mit Data Lake Storage Gen2-Speicher, und wählen Sie es aus.

    * Wählen Sie unter **Identität** die neu erstellte, vom Benutzer zugewiesene verwaltete Identität aus.

        :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png" alt-text="Speichereinstellungen für das Verwenden von Data Lake Storage Gen2 mit Azure HDInsight":::

    > [!NOTE]
    > * Zum Hinzufügen eines sekundären Speicherkontos mit Data Lake Storage Gen2 müssen Sie auf der Speicherkontoebene lediglich die zuvor erstellte verwaltete Identität zur neuen Data Lake Storage Gen2-Instanz hinzufügen, die Sie hinzufügen möchten. Beachten Sie, dass das Hinzufügen eines sekundären Speicherkontos mit Data Lake Storage Gen2 über das Blatt „Zusätzliche Speicherkonten“ in HDInsight nicht unterstützt wird.
    > * Sie können RA-GRS oder RA-ZRS (georedundanter Speicher mit Lesezugriff oder geo- und zonenredundanter Speicher) für das von HDInsight verwendete Azure-Blobspeicherkonto aktivieren. Jedoch wird das Erstellen eines Clusters für den sekundären RA-GRS- oder RA-ZRS-Endpunkt nicht unterstützt.
    > * HDInsight unterstützt die Einrichtung von Data Lake Storage Gen2 als geozonenredundanten Speicher mit Lesezugriff (read-access geo-zone-redundant storage, RA-GZRS) oder geozonenredundanten Speicher (geo-zone-redundant storage, GZRS) nicht.

## <a name="delete-the-cluster"></a>Löschen des Clusters

Weitere Informationen finden Sie unter [Löschen eines HDInsight-Clusters mithilfe von Browser, PowerShell oder Azure-Befehlszeilenschnittstelle](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Problembehandlung

Falls beim Erstellen von HDInsight-Clustern Probleme auftreten, sehen Sie sich die [Voraussetzungen für die Zugriffssteuerung](./hdinsight-hadoop-customize-cluster-linux.md#access-control) an.

## <a name="next-steps"></a>Nächste Schritte

Sie haben die Erstellung eines HDInsight-Clusters erfolgreich abgeschlossen. Als Nächstes wird beschrieben, wie Sie mit Ihrem Cluster arbeiten.

### <a name="apache-spark-clusters"></a>Apache Spark-Cluster

* [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md)
* [Erstellen einer eigenständigen Anwendung mit Scala](spark/apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](spark/apache-spark-use-bi-tools.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-Cluster

* [Verwenden von Apache Hive mit HDInsight](hadoop/hdinsight-use-hive.md)
* [Verwenden von MapReduce mit HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase Cluster

* [Erste Schritte mit Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Entwickeln von Java-Anwendungen für Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
