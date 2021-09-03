---
title: Aktualisieren des Azure Storage-Kontozugriffsschlüssels in Azure HDInsight
description: Erfahren Sie, wie Sie den Azure Storage-Kontozugriffsschlüssel in Azure HDInsight-Cluster aktualisieren.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/29/2021
ms.openlocfilehash: 9adfb5e438aec8625cd7c4b164f5999181d9c31f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354858"
---
# <a name="update-azure-storage-account-access-keys-in-hdinsight-cluster"></a>Aktualisieren von Azure Storage-Kontozugriffsschlüsseln in HDInsight-Cluster

In diesem Artikel erfahren Sie, wie Sie Azure Storage-Kontozugriffsschlüssel für die primären oder sekundären Speicherkonten in Azure HDInsight rotieren.

>[!CAUTION]
> Durch die direkte Rotation des Zugriffsschlüssels auf der Speicherseite kann nicht mehr auf den HDInsight-Cluster zugegriffen werden.

## <a name="prerequisites"></a>Voraussetzungen

* Wir werden einen Ansatz verwenden, um die primären und sekundären Zugriffsschlüssel des Speicherkontos gestaffelt und abwechselnd zu rotieren, um sicherzustellen, dass der HDInsight-Cluster während des gesamten Prozesses zugänglich ist.

    Im Folgenden finden Sie ein Beispiel für die Verwendung primärer und sekundärer Speicherzugriffsschlüssel und für die Einrichtung von Rotationsrichtlinien für diese:
    1. Verwenden Sie „access key1“ für das Speicherkonto, wenn Sie einen HDInsight-Cluster erstellen.
    1. Richten Sie alle N Tage eine Rotationsrichtlinie für „access key2“ ein. Aktualisieren Sie im Rahmen dieser Rotation HDInsight so, dass „access key1“ verwendet wird, und rotieren Sie dann „access key2“ im Speicherkonto.
    1. Richten Sie alle N/2 Tage eine Rotationsrichtlinie für „access key1“ ein. Aktualisieren Sie im Rahmen dieser Rotation HDInsight so, dass „access key2“ verwendet wird, und rotieren Sie dann „access key1“ im Speicherkonto.
    1. Beim oben genannten Ansatz wird „access key1“ alle N/2, 3N/2 usw. Tage rotiert, und „access key2“ wird alle N, 2N, 3N usw. Tage rotiert.

* Informationen zum Einrichten der regelmäßigen Rotation von Speicherkontoschlüsseln finden Sie unter [Automatisieren der Rotation eines Geheimnisses](../key-vault/secrets/tutorial-rotation-dual.md).

## <a name="update-storage-account-access-keys"></a>Aktualisieren von Zugriffsschlüsseln für ein Speicherkonto

Verwenden Sie eine [Skriptaktion](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster), um die Schlüssel mit den folgenden Aspekten zu aktualisieren:

|Eigenschaft | Wert |
|---|---|
|Bash-Skript-URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/update-storage-account-v01.sh`|
|Knotentyp(en)|Head|
|Parameter|`ACCOUNTNAME` `ACCOUNTKEY` `-p` (optional)|

* `ACCOUNTNAME` ist der Name des Speicherkontos im HDInsight-Cluster.
* `ACCOUNTKEY` ist der Zugriffsschlüssel für `ACCOUNTNAME`.
* `-p` ist optional. Bei Angabe wird der Schlüssel nicht verschlüsselt und im Klartext in der Datei „core-site.xml“ gespeichert.

## <a name="known-issues"></a>Bekannte Probleme

Das vorherige Skript aktualisiert den Zugriffsschlüssel direkt nur clusterseitig und erneuert keine Kopie aufseiten des HDInsight-Ressourcenanbieters. Daher schlägt die Skriptaktion, die im Speicherkonto gehostet wird, nach der Rotation des Zugriffsschlüssels fehl.

Problemumgehung: Verwenden Sie [SAS-URIs](hdinsight-storage-sharedaccesssignature-permissions.md) für Skriptaktionen, oder machen Sie die Skripts öffentlich zugänglich.

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen weiterer Speicherkonten](hdinsight-hadoop-add-storage.md)