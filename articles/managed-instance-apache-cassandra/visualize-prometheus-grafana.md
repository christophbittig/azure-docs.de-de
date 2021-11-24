---
title: Konfigurieren von Grafana für die Visualisierung von Metriken, die von Azure Managed Instance for Apache Cassandra ausgegeben werden
description: Hier erfahren Sie, wie Sie Grafana auf einem virtuellen Computer installieren und konfigurieren, um ausgegebene Metriken eines Clusters vom Typ „Azure Managed Instance for Apache Cassandra“ zu visualisieren.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: thvankra
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8fe10e1706667bba10133131f7a1d50a6e3fa110
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309808"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>Konfigurieren von Grafana für die Visualisierung von Metriken, die vom Managed Instance-Cluster ausgegeben werden

Wenn Sie einen Cluster vom Typ „Azure Managed Instance for Apache Cassandra“ bereitstellen, wird ein Server bereitgestellt, der als Host für [Prometheus](https://prometheus.io/) fungiert und von verschiedenen Clienttools genutzt werden kann. Prometheus ist eine Open-Source-Überwachungslösung. Die verwaltete Instanz gibt Metriken aus und speichert zehn Minuten oder 10 GB an Daten (je nachdem, welcher Schwellenwert zuerst erreicht wird). In diesem Artikel erfahren Sie, wie Sie Grafana für die Visualisierung von Metriken konfigurieren, die vom Managed Instance-Cluster ausgegeben werden. Zum Visualisieren von Metriken ist Folgendes erforderlich:

* Bereitstellen eines virtuellen Ubuntu-Computers in dem virtuellen Azure-Netzwerk, in dem sich die verwaltete Instanz befindet.
* Installieren des Open-Source-Tools [Grafana](https://grafana.com/grafana/), um Dashboards zu erstellen und von Prometheus ausgegebene Metriken zu visualisieren

## <a name="deploy-an-ubuntu-server"></a>Bereitstellen eines Ubuntu-Servers

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zu der Ressourcengruppe, in der sich Ihr Managed Instance-Cluster befindet. Wählen Sie **Hinzufügen** aus, und suchen Sie nach dem Image **Ubuntu Server 18.04 LTS**:

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Suchen nach dem Ubuntu-Serverimage im Azure-Portal" border="true":::

1. Wählen Sie das Image und anschließend **Erstellen** aus.

1. Geben Sie auf dem Blatt **Virtuellen Computer erstellen** Werte für die folgenden Felder ein. In anderen Feldern können Sie die Standardwerte übernehmen:

   * **Name des virtuellen Computers**: Geben Sie einen Namen für Ihren virtuellen Computer ein.
   * **Region**: Wählen Sie die Region aus, in der auch Ihr virtuelles Netzwerk bereitgestellt wurde.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Ausfüllen des Formulars, um einen virtuellen Computer mit Ubuntu-Serverimage zu erstellen" border="true":::

1. Wählen Sie auf der Registerkarte **Netzwerk** das virtuelle Netzwerk aus, in dem Ihre verwaltete Instanz bereitgestellt wurde:

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Konfigurieren der Netzwerkeinstellungen des Ubuntu-Servers" border="true":::

1. Wählen Sie abschließend **Überprüfen + Erstellen** aus, um Ihren Grafana-Server zu erstellen.

## <a name="install-grafana"></a>Installieren von Grafana

1. Öffnen Sie im Azure-Portal das virtuelle Netzwerk, in dem Sie die verwaltete Instanz und den Grafana-Server bereitgestellt haben. Es sollte eine VM-Skalierungsgruppe namens **cassandra-jump (Instanz 0)** angezeigt werden. Die Prometheus-Metriken werden in dieser VM-Skalierungsgruppe gehostet. Notieren Sie sich die IP-Adresse dieser Instanz:

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="Ermitteln der IP-Adresse der Prometheus-Instanz" border="true":::

1. Stellen Sie über die [Azure CLI](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) oder mithilfe Ihres bevorzugten Clienttools eine SSH-Verbindung mit Ihrem neu erstellten Ubuntu-Server her.

1. Anschließend müssen Sie Grafana installieren und für die Verbindungsherstellung mit der VM-Skalierungsgruppe konfigurieren, in der die Metriken gehostet werden. Öffnen Sie eine Eingabeaufforderung, und geben Sie den Befehl `nano` ein, um einen Nano-Text-Editor zu öffnen. Fügen Sie das folgende Skript in den Text-Editor ein, und ersetzen Sie `<prometheus IP address>` durch die IP-Adresse, die Sie sich im vorherigen Schritt notiert haben:

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. Drücken Sie `ctrl + X`, um die Datei zu speichern. Sie können die Datei `grafana.sh` nennen.

1. Geben Sie an der Eingabeaufforderung den Befehl `./grafana.sh` ein, um Grafana zu installieren.

1. Nach Abschluss der Installation ist Grafana am **Port 3000** in der IP-Adresse des Servers verfügbar, wie auf dem folgenden Screenshot zu sehen:

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="Ausführen von Grafana am Port 3000" border="true":::

1. Sie können aus Open-Source-Dashboards wählen, die für Apache Cassandra in Grafana erstellt wurden. Ein Beispiel wäre etwa die JSON-Datei [cluster-overview](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json). Laden Sie die JSON-Definition des Dashboards herunter, und importieren Sie sie in Grafana:

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Importieren der Grafana-JSON-Definition" border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Hochladen der Grafana-JSON-Definition" border="true":::

1. Anschließend können Sie Ihren Managed Instance-Cluster für Cassandra mithilfe des gewählten Dashboards überwachen:

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="Anzeigen der Managed Instance-Metriken für Cassandra auf dem Dashboard" border="true":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Dashboards konfigurieren, um Metriken in Prometheus mithilfe von Grafana zu visualisieren. Weitere Informationen zu Azure Managed Instance for Apache Cassandra finden Sie in den folgenden Artikeln:

* [Was ist Azure Managed Instance for Apache Cassandra? (Vorschau)](introduction.md)
* [Bereitstellen eines verwalteten Apache Spark-Clusters mit Azure Databricks](deploy-cluster-databricks.md)
