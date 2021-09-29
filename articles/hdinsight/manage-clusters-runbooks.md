---
title: 'Tutorial: Erstellen von Clustern mithilfe von Azure Automation-Runbooks – Azure HDInsight'
description: Hier erfahren Sie, wie Sie Azure Automation-Runbooks verwenden, um Azure HDInsight-Cluster mit Skripts zu erstellen und zu löschen, die in der Cloud ausgeführt werden.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: d8091bd5e1bb95ff29d0be948a464f587a49f62f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819740"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Tutorial: Erstellen von Azure HDInsight-Clustern mit Azure Automation

Azure Automation ermöglicht die Erstellung von Skripts, die in der Cloud ausgeführt werden, sowie die bedarfs- und zeitplanbasierte Verwaltung von Azure-Ressourcen. In diesem Artikel erfahren Sie, wie Sie PowerShell-Runbooks verwenden, um Azure HDInsight-Cluster zu erstellen und zu löschen.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Installieren von Modulen, die für die Interaktion mit HDInsight erforderlich sind
> * Erstellen und Speichern von Anmeldeinformationen, die für die Clustererstellung benötigt werden
> * Erstellen eines neuen Azure Automation-Runbooks für die Erstellung eines HDInsight-Clusters

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein [Azure Automation-Konto](../automation/quickstarts/create-account-portal.md).
* Ein [Azure Storage-Konto](../storage/common/storage-account-create.md) als Clusterspeicher.

## <a name="install-hdinsight-modules"></a>Installieren der HDInsight-Module

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie Ihre Azure Automation-Konten aus.
1. Wählen Sie unter **Freigegebene Ressourcen** die Option **Modulkatalog** aus.
1. Geben Sie **AzureRM.Profile** in das Feld ein, und drücken Sie die EINGABETASTE, um den Suchvorgang zu starten. Wählen Sie das verfügbare Suchergebnis aus.
1. Wählen Sie im Bildschirm **AzureRM.profile** die Option **Importieren** aus. Aktivieren Sie das Kontrollkästchen für die Aktualisierung der Azure-Module, und wählen Sie anschließend **OK** aus.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermprofile-module.png" alt-text="Importieren des Moduls „AzureRM.profile“" border="false":::

1. Wählen Sie unter **Freigegebene Ressourcen** die Option **Modulkatalog** aus, um zum Modulkatalog zurückzukehren.
1. Geben Sie **HDInsight** ein. Wählen Sie **AzureRM.HDInsight** aus.

    :::image type="content" source="./media/manage-clusters-runbooks/browse-modules-hdinsight.png" alt-text="Durchsuchen der HDInsight-Module" border="true":::

1. Wählen Sie im Bereich **AzureRM.HDInsight** die Option **Importieren** und anschließend **OK** aus.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermhdinsight-module.png" alt-text="Importieren des Moduls „AzureRM.HDInsight“" border="true":::

## <a name="create-credentials"></a>Erstellen von Anmeldeinformationen

1. Wählen Sie unter **Freigegebene Ressourcen** die Option **Anmeldeinformationen** aus.
1. Wählen Sie **Anmeldeinformationen hinzufügen** aus.
1. Geben Sie im Bereich **Neue Anmeldeinformationen** die erforderlichen Informationen ein. Diese Anmeldeinformationen dienen zum Speichern des Clusterkennworts für die Anmeldung bei Ambari.

    | Eigenschaft | Wert |
    | --- | --- |
    | Name | `cluster-password` |
    | Benutzername | `admin` |
    | Kennwort | `SECURE_PASSWORD` |
    | Kennwort bestätigen | `SECURE_PASSWORD` |

1. Wählen Sie **Erstellen** aus.
1. Wiederholen Sie den gleichen Vorgang, um neue Anmeldeinformationen vom Typ `ssh-password` mit dem Benutzernamen `sshuser` und einem Kennwort Ihrer Wahl zu erstellen. Wählen Sie **Erstellen** aus. Diese Anmeldeinformationen dienen zum Speichern des SSH-Kennworts für Ihren Cluster.

    :::image type="content" source="./media/manage-clusters-runbooks/create-credentials.png" alt-text="Erstellen von Anmeldeinformationen" border="true":::

## <a name="create-a-runbook-to-create-a-cluster"></a>Erstellen eines Runbooks zum Erstellen eines Clusters

1. Wählen Sie **Runbooks** unter Option **Prozessautomatisierung** aus.
1. Wählen Sie **Runbook erstellen** aus.
1. Geben Sie im Bereich **Runbook erstellen** einen Namen für das Runbook ein (beispielsweise `hdinsight-cluster-create`). Wählen Sie in der Dropdownliste **Runbooktyp** die Option **PowerShell** aus.
1. Wählen Sie **Erstellen** aus.

    :::image type="content" source="./media/manage-clusters-runbooks/create-runbook.png" alt-text="Erstellen eines Runbooks" border="true":::

1. Geben Sie im Bildschirm **PowerShell-Runbook bearbeiten** den folgenden Code ein, und wählen Sie **Veröffentlichen** aus:

    :::image type="content" source="./media/manage-clusters-runbooks/publish-runbook.png" alt-text="Veröffentlichen des Runbooks" border="true":::

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Erstellen eines Runbooks zum Löschen eines Clusters

1. Wählen Sie **Runbooks** unter Option **Prozessautomatisierung** aus.
1. Wählen Sie **Runbook erstellen** aus.
1. Geben Sie im Bereich **Runbook erstellen** einen Namen für das Runbook ein (beispielsweise `hdinsight-cluster-delete`). Wählen Sie in der Dropdownliste **Runbooktyp** die Option **PowerShell** aus.
1. Wählen Sie **Erstellen** aus.
1. Geben Sie im Bildschirm **PowerShell-Runbook bearbeiten** den folgenden Code ein, und wählen Sie **Veröffentlichen** aus:

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Ausführen von Runbooks

### <a name="create-a-cluster"></a>Erstellen eines Clusters

1. Zeigen Sie die Runbookliste für Ihr Automation-Konto an, indem Sie unter **Prozessautomatisierung** die Option **Runbooks** auswählen.
1. Wählen Sie `hdinsight-cluster-create` oder den Namen aus, den Sie beim Erstellen Ihres Runbooks für die Clustererstellung verwendet haben.
1. Wählen Sie **Starten** aus, um das Runbook sofort auszuführen. Sie können auch eine periodische Runbookausführung planen. Weitere Informationen finden Sie unter [Planen eines Runbooks in Azure Automation](../automation/shared-resources/schedules.md).
1. Geben Sie die erforderlichen Parameter für das Skript ein, und wählen Sie **OK** aus. Dadurch wird ein neuer HDInsight-Cluster mit dem Namen erstellt, den Sie im Parameter **CLUSTERNAME** angegeben haben.

    :::image type="content" source="./media/manage-clusters-runbooks/execute-create-runbook.png" alt-text="Ausführen des Runbooks für die Clustererstellung" border="true":::

### <a name="delete-a-cluster"></a>Löschen eines Clusters

Löschen Sie den Cluster, indem Sie das von Ihnen erstellte Runbook `hdinsight-cluster-delete` auswählen. Wählen Sie **Starten** aus, geben Sie den Parameter **CLUSTERNAME** ein, und wählen Sie **OK** aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das erstellte Azure Automation-Konto nicht mehr benötigen, löschen Sie es, um unnötige Gebühren zu vermeiden. Wählen Sie hierzu im Azure-Portal die Ressourcengruppe aus, in der Sie das Azure Automation-Konto erstellt haben, wählen Sie das Automation-Konto aus, und wählen Sie anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten von Apache Hadoop-Clustern in HDInsight mit Azure PowerShell](hdinsight-administer-use-powershell.md)