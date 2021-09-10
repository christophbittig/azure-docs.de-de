---
title: 'Schnellstart: Verbinden von Azure Data Explorer mit einem Azure Synapse Analytics-Arbeitsbereich'
description: Sie erfahren, wie Sie einen Azure Data Explorer-Cluster mit einem Azure Synapse Analytics-Arbeitsbereich verbinden, indem Sie Apache Spark für Azure Synapse Analytics verwenden.
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: 9f45bc134c656701ff02beca464ac00fdf222f88
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830201"
---
# <a name="connect-to-azure-data-explorer-using-apache-spark-for-azure-synapse-analytics"></a>Verbinden mit Azure Data Explorer mithilfe von Apache Spark für Azure Synapse Analytics

In diesem Artikel wird beschrieben, wie Sie aus Synapse Studio mit Apache Spark für Azure Synapse Analytics auf eine Azure Data Explorer-Datenbank zugreifen.

## <a name="prerequisites"></a>Voraussetzungen

* [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](/azure/data-explorer/create-cluster-database-portal)
* Vorhandensein eines Azure Synapse Analytics-Arbeitsbereichs, oder erstellen Sie einen neuen Arbeitsbereich anhand der Schritte in [Schnellstart: Erstellen eines Azure Synapse-Arbeitsbereichs](./quickstart-create-workspace.md).
* Vorhandensein eines Apache Spark-Pools, oder erstellen Sie einen neuen Pool anhand der Schritte in [Schnellstart: Erstellen eines neuen Apache Spark-Pools (Vorschauversion)](./quickstart-create-apache-spark-pool-portal.md).
* [Erstellen Sie eine Azure Active Directory (Azure AD)-App durch Bereitstellen einer Azure AD-Anwendung](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app).
* Gewähren Sie Ihrer Azure AD-App Zugriff auf Ihre Datenbank, indem Sie die Schritte in [Verwalten der Berechtigungen für Datenbanken in Azure Data Explorer](/azure/data-explorer/manage-database-permissions) verwenden.

## <a name="go-to-synapse-studio"></a>Wechseln zu Synapse Studio

Wählen Sie in einem Azure Synapse-Arbeitsbereich die Option **Synapse Studio starten** aus. Wählen Sie auf der Synapse Studio-Homepage die Option **Daten** aus, um zum **Datenobjekt-Explorer** zu gelangen.

## <a name="connect-an-azure-data-explorer-database-to-an-azure-synapse-workspace"></a>Herstellen einer Verbindung zwischen einer Azure Data Explorer-Datenbank und einem Azure Synapse-Arbeitsbereich

Für die Verbindungsherstellung einer Azure Data Explorer-Datenbank mit einem Arbeitsbereich wird ein verknüpfter Dienst verwendet. Mit einem verknüpften Azure Data Explorer-Dienst können Sie über Apache Spark für Azure Synapse Daten durchsuchen und erkunden sowie lesen und schreiben. Sie können auch Integrationsaufträge in einer Pipeline ausführen.

Führen Sie im Datenobjekt-Explorer die folgenden Schritte aus, um eine direkte Verbindung mit einem Azure Data Explorer-Cluster herzustellen:

1. Wählen Sie neben **Daten** das Symbol **+** aus.
1. Wählen Sie **Verbinden** aus, um eine Verbindung mit externen Daten herzustellen.
1. Select **Azure Data Explorer (Kusto)** aus.
1. Wählen Sie **Weiter**.
1. Verwenden Sie einen aussagekräftigen Anzeigenamen für den verknüpften Dienst. Der Name wird im Datenobjekt-Explorer angezeigt und von Azure Synapse-Laufzeiten verwendet, um eine Verbindung mit der Datenbank herzustellen.
1. Wählen Sie den Azure Data Explorer-Cluster aus Ihrem Abonnement aus, oder geben Sie den URI ein.
1. Geben Sie die **Dienstprinzipal-ID** und den **Dienstprinzipalschlüssel** ein. Stellen Sie sicher, dass dieser Dienstprinzipal über Anzeigezugriff auf die Datenbank für Lesevorgänge und über Erfassungszugriff für die Erfassung von Daten verfügt.
1. Geben Sie den Azure Data Explorer-Datenbanknamen ein.
1. Wählen Sie **Verbindung testen** aus, um sicherzustellen, dass Sie über die richtigen Berechtigungen verfügen.
1. Wählen Sie **Erstellen** aus.

    ![Screenshot, der einen neuen verknüpften Dienst zeigt.](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > (Optional) **Verbindung testen** überprüft nicht den Schreibzugriff. Stellen Sie sicher, dass die Dienstprinzipal-ID über Schreibzugriff auf die Azure Data Explorer-Datenbank verfügt.

1. Azure Data Explorer-Cluster und -Datenbanken werden auf der Registerkarte **Verknüpft** unter dem Abschnitt **Azure Data Explorer** angezeigt.

    ![Screenshot, der das Durchsuchen nach Clustern zeigt.](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

1. Bevor Sie über ein Notebook mit dem verknüpften Dienst interagieren können, muss er im Arbeitsbereich veröffentlicht werden. Klicken Sie in der Symbolleiste auf **Veröffentlichen**, prüfen Sie die ausstehenden Änderungen, und klicken Sie auf **OK**.

    > [!NOTE]
    > Im aktuellen Release werden die Datenbankobjekte basierend auf Ihren Azure AD-Kontoberechtigungen in den Azure Data Explorer-Datenbanken aufgefüllt. Beim Ausführen der Apache Spark-Notebooks oder -Integrationsaufträge werden die Anmeldeinformationen im Linkdienst (z. B. Dienstprinzipal) verwendet.

## <a name="quickly-interact-with-code-generated-actions"></a>Schnelles Interagieren mit durch Code generierte Aktionen

Wenn Sie mit der rechten Maustaste auf eine Datenbank oder Tabelle klicken, wird eine Liste mit Spark-Beispielnotebooks angezeigt. Wählen Sie eine Option aus, um Daten in Azure Data Explorer zu lesen, zu schreiben oder zu streamen.

[![Screenshot, der neue Beispielnotebooks zeigt.](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

Hier sehen Sie ein Beispiel für das Lesen von Daten. Fügen Sie das Notebook an Ihren Spark-Pool an, und führen Sie die Zelle aus.

[![Screenshot, der ein neues Lesenotebook zeigt.](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE]
   > Bei der erstmaligen Ausführung kann es mehr als drei Minuten dauern, bis die Spark-Sitzung initiiert wurde. Nachfolgende Ausführungen sind dann deutlich schneller.

## <a name="limitations"></a>Einschränkungen

Der Azure Data Explorer-Connector wird für verwaltete virtuelle Azure Synapse-Netzwerke derzeit nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

* [Beispielcode mit erweiterten Optionen](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Azure Data Explorer (Kusto): Spark-Connector](https://github.com/Azure/azure-kusto-spark)
