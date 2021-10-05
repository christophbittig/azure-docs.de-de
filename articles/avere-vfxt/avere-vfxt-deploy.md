---
title: Bereitstellen von Avere vFXT für Azure
description: Erfahren Sie, wie Sie den im Azure Marketplace verfügbaren Bereitstellungs-Assistenten verwenden, um einen Cluster mit Avere vFXT für Azure bereitzustellen.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 330fa1368c078f1e44dd436cd3bea6407c6f4cc6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124794650"
---
# <a name="deploy-the-vfxt-cluster"></a>Bereitstellen des vFXT-Clusters

Dieses Verfahren führt Sie durch die Verwendung des Bereitstellungsassistenten aus dem Azure Marketplace. Der Assistent stellt den Cluster automatisch mit einer Azure Resource Manager-Vorlage bereit. Nachdem Sie die Parameter im Formular eingegeben und auf **Erstellen** geklickt haben, führt Azure diese Schritte automatisch aus:

* Erstellen des Clustercontrollers, der eine einfache VM ist, die die Software enthält, die für die Bereitstellung und Verwaltung des Clusters erforderlich ist.
* Einrichten der Ressourcengruppe und der virtuellen Netzwerkinfrastruktur, einschließlich der Erstellung der neuen Elemente.
* Erstellen der Clusterknoten-VMs, und ihre Konfiguration als Avere-Cluster.
* Erstellen eines neuen Azure Blob-Containers, und seine Konfiguration als Cluster-Kernspeichereinheit, falls erforderlich.

Nachdem Sie die Anweisungen in diesem Dokument befolgt haben, verfügen Sie über ein virtuelles Netzwerk, ein Subnetz, einen Clustercontroller und einen vFXT-Cluster, wie im folgenden Diagramm dargestellt. Diese zeigt die optionalen Azure Blob-Kernspeichereinheit, die einen neuen Blob-Speichercontainer enthält, der in einem nicht dargestellten Speicherkonto enthalten ist, und einen Dienstendpunkt für Microsoft Storage innerhalb des Subnetzes.

![Screenshot: Abbildung mit drei konzentrischen Rechtecken mit Avere-Clusterkomponenten. Das äußere Rechteck trägt die Bezeichnung „Ressourcengruppe“ und enthält ein Sechseck mit der Bezeichnung „Blob-Speicher (optional)“. Das mittlere Rechteck trägt die Bezeichnung „Virtuelles Netzwerk: 10.0.0.0/16“ und enthält selbst keine spezifischen Komponenten. Das innerste Rechteck trägt die Bezeichnung „Subnetz:10.0.0.0/24“ und enthält eine VM mit der Bezeichnung „Clustercontroller“, einen Stapel aus drei VMs mit der Bezeichnung „vFXT-Knoten (vFXT-Cluster)“, und ein Sechseck mit der Bezeichnung „Dienstendpunkt“. Ein Pfeil verbindet den Dienstendpunkt, der sich innerhalb des Subnetzes befindet, mit dem Blob-Speicher, der sich in der Ressourcengruppe außerhalb des Subnetzes und außerhalb von „vnet“ befindet. Der Pfeil überschneidet dabei jeweils die rechte Seite der Rechtecke für das Subnetz und das virtuellen Netzwerk.](media/avere-vfxt-deployment.png)

Vergewissern Sie sich vor der Verwendung der Erstellungsvorlage, dass diese Voraussetzungen erfüllt sind:  

* [Neues Abonnement](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Berechtigungen des Abonnementbesitzers](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Kontingent für den vFXT-Cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Storage-Dienstendpunkt (bei Bedarf):](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) für Bereitstellungen benötigt, bei denen ein vorhandenes virtuelles Netzwerk verwendet und ein Blobspeicher erstellt wird.

Weitere Informationen zu den Schritten und der Planung der Clusterbereitstellung finden Sie unter [Planen Ihres Avere vFXT-Systems](avere-vfxt-deploy-plan.md) und [Übersicht über die Bereitstellung](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Erstellen des Avere vFXT for Azure

Rufen sie die Erstellungsvorlage im Azure-Portal auf, indem Sie nach Avere suchen und „Avere vFXT für Azure ARM-Vorlage“ auswählen.

![Browserfenster mit dem Azure-Portal mit Breadcrumbs „Neu > Marketplace > Alles“. Auf der Seite „Alles“ enthält das Suchfeld den Begriff „avere“ und das zweite Ergebnis, „Avere vFXT für Azure ARM-Vorlage“, ist rot hervorgehoben.](media/avere-vfxt-template-choose.png)

Nachdem Sie die Details auf der Seite „Avere vFXT for Azure-ARM-Vorlage“ gelesen haben, klicken Sie auf die zugehörige Schaltfläche **Erstellen**, um zu beginnen.

![Azure Marketplace mit der Anzeige der ersten Seite der Bereitstellungvorlage](media/avere-vfxt-deploy-first.png)

Die Vorlage ist in vier Schritte unterteilt – zwei Seiten zur Informationsbeschaffung sowie Validierungs- und Bestätigungsschritte.

* Auf Seite 1 sind die Einstellungen für die Clustercontroller-VM zusammengefasst.
* Seite 2 sammelt Parameter für die Erstellung des Clusters und zusätzlicher Ressourcen wie Subnetze und Speicher.
* Auf Seite 3 wird Ihre Auswahl zusammengefasst und die Konfiguration überprüft.
* Seite 4 erläutert die Softwarebedingungen und ermöglicht es Ihnen, den Erstellungsprozess des Clusters zu starten.

## <a name="page-one-parameters---cluster-controller-information"></a>Parameter auf Seite 1 – Clustercontrollerinformationen

Die erste Seite der Bereitstellungsvorlage konzentriert sich auf den Clustercontroller.

![Erste Seite der Bereitstellungsvorlage](media/avere-vfxt-deploy-1.png)

Geben Sie die folgenden Informationen ein:

* **Name des Clustercontrollers** – Legen Sie den Namen für die Clustercontroller-VM fest.

* **Controllerbenutzername** – Legen Sie den Root-Benutzernamen für die Clustercontroller-VM fest.

* **Authentifizierungstyp** – Wählen Sie entweder das Kennwort oder die Authentifizierung mit öffentlichem SSH-Schlüssel für die Verbindung mit dem Controller. Die Authentifizierungsmethode mit öffentlichem SSH-Schlüssel wird empfohlen. Lesen Sie dazu [Erstellen und Verwenden von SSH-Schlüsseln](../virtual-machines/linux/ssh-from-windows.md), wen Sie Hilfe benötigen.

* **Kennwort** oder **Öffentlicher SSH-Schlüssel** – Je nach Authentifizierungstyp, den Sie ausgewählt haben, müssen Sie einen öffentlichen RSA-Schlüssel oder ein Kennwort in den nächsten Feldern angeben. Diese Anmeldeinformationen werden in Kombination mit dem Benutzernamen verwenden, der zuvor bereitgestellt wurde.

* **Abonnement** – Wählen Sie das Avere vFXT-Abonnement aus.

* **Ressourcengruppe** – Wählen Sie eine vorhandene leere Ressourcengruppe für den Avere vFXT-Cluster aus, oder klicken Sie auf „Neu erstellen“, und geben Sie einen neuen Ressourcengruppennamen ein.

* **Speicherort** – Wählen Sie den Azure-Speicherort für Ihren Cluster und die Ressourcen.

Klicken Sie anschließend auf **OK**.

> [!NOTE]
> Wenn Sie möchten, dass der Clustercontroller eine öffentlich zugängliche IP-Adresse hat, erstellen Sie ein neues virtuelles Netzwerk für den Cluster, anstatt ein bestehendes Netzwerk auszuwählen. Diese Einstellung finden Sie auf der zweiten Seite.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Parameter auf Seite 2 – vFXT-Clusterinformationen

Auf der zweiten Seite der Bereitstellungsvorlage können Sie unter anderem Clustergröße, Knotentyp, Cachegröße und Speicherparameter festlegen.

![Zweite Seite der Bereitstellungsvorlage](media/avere-vfxt-deploy-2.png)

* **Anzahl der Avere vFXT-Clusterknoten** – Wählen Sie die Anzahl der Knoten im Cluster aus. Der Mindestwert sind drei Knoten, der Maximalwert beträgt 20.

* **Cluster-Verwaltungskennwort** – Erstellen Sie das Kennwort für die Clusterverwaltung. Dieses Kennwort wird mit dem Benutzernamen ```admin``` zur Anmeldung bei der Clustersteuerung verwendet, wo sie den Cluster überwachen und Clustereinstellungen konfigurieren können.

* **Avere vFXT-Clustername** – Geben Sie dem Cluster einen eindeutigen Namen.

* **Größe** – Dieser Bereich zeigt den VM-Typen an, der für die Clusterknoten verwendet wird. Obwohl es nur eine empfohlene Option gibt, öffnet der Link **Größe ändern** eine Tabelle mit Details zu diesem Instanztyp und einen Link zu einem Preisrechner.

* **Cachegröße pro Knoten** – Der Clustercache ist auf die Clusterknoten verteilt, sodass die gesamte Cachegröße auf Ihrem Avere vFXT-Cluster dieser Größe multipliziert mit der Anzahl der Knoten entspricht.

  Empfohlene Konfiguration: Verwenden Sie für Standard_E32s_v3-Knoten 4 TB pro Knoten.

* **Virtuelles Netzwerk** – Definieren Sie ein neues virtuelles Netzwerk als Host für den Cluster, oder wählen Sie ein vorhandenes Netzwerk aus, das den Voraussetzungen entspricht, die in [Planen des Avere vFXT-Systems](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure) beschrieben sind.

  > [!NOTE]
  > Wenn Sie ein neues virtuelles Netzwerk erstellen, wird dem Clustercontroller eine öffentliche IP-Adresse zugewiesen, sodass Sie auf das neue private Netzwerk zugreifen können. Wenn Sie ein bestehendes virtuelles Netzwerk auswählen, wird der Clustercontroller ohne eine öffentliche IP-Adresse konfiguriert.
  >
  > Eine öffentlich sichtbare IP-Adresse auf dem Clustercontroller ermöglicht einen einfacheren Zugriff auf den vFXT-Cluster, birgt aber ein leichtes Sicherheitsrisiko.
  >* Eine öffentliche IP-Adresse auf dem Clustercontroller ermöglicht es Ihnen, ihn als Jump-Host zu verwenden, um sich von außerhalb des privaten Subnetzes mit dem Avere vFXT-Cluster zu verbinden.
  >* Wenn Sie keine öffentliche IP-Adresse auf dem Controller haben, müssen Sie für den Zugriff auf den Cluster einen anderen Jump-Host, eine VPN-Verbindung oder ExpressRoute verwenden. Verwenden Sie z. B. ein vorhandenes virtuelles Netzwerk, in dem bereits eine VPN-Verbindung konfiguriert ist.
  >* Wenn Sie einen Controller mit einer öffentlichen IP-Adresse erstellen, sollten Sie den virtuellen Controllercomputer mit einer Netzwerksicherheitsgruppe schützen. Standardmäßig erstellt die Avere vFXT for Azure-Bereitstellung eine Netzwerksicherheitsgruppe, die den eingehenden Zugriff nur auf Port 22 für Controller mit öffentlichen IP-Adressen beschränkt. Sie können das System weiter schützen, indem Sie den Zugriff auf Ihren Bereich von IP-Quelladressen sperren, d.h. nur Verbindungen von Computern erlauben, die Sie für den Clusterzugriff verwenden möchten.

  Ein neues virtuelles Netzwerk wird auch mit einem Speicherdienstendpunkt für Azure Blob Storage und mit einer gesperrten Netzwerkzugriffssteuerung, die nur IPs des Clustersubnetzes zulässt, konfiguriert.

* **Subnetz** – Wählen Sie ein Subnetz aus, oder erstellen Sie ein neues.

* **Create and use blob storage** (Blob-Speicher erstellen und verwenden) – Wählen Sie **true** aus, um einen neuen Azure Blob-Container zu erstellen und ihn als Back-End-Speicher für den neuen Avere vFXT-Cluster zu konfigurieren. Diese Option erstellt auch ein neues Speicherkonto innerhalb der Ressourcengruppe des Clusters sowie einen Microsoft-Speicherdienstendpunkt innerhalb des Clustersubnetzes.
  
  Wenn Sie ein vorhandenes virtuelles Netzwerk zur Verfügung stellen, muss es einen Speicherdienstendpunkt haben, bevor Sie den Cluster erstellen. Weitere Informationen finden Sie unter [Planen des Avere vFXT-Systems](avere-vfxt-deploy-plan.md).

  Legen Sie für dieses Feld **false** fest, wenn Sie keinen neuen Container erstellen möchten. In diesem Fall müssen Sie den Speicher nach dem Erstellen des Clusters anfügen und konfigurieren. Anweisungen finden Sie unter [Konfigurieren von Speicher](avere-vfxt-add-storage.md).

* **(New) Storage account** ((Neues )Speicherkonto) – Wenn Sie einen neuen Azure Blob-Container erstellen, geben Sie einen Namen für das neue Speicherkonto ein.

## <a name="validation-and-purchase"></a>Überprüfung und Kauf

Auf Seite 3 wird die Konfiguration zusammengefasst und die Parameter werden überprüft. Wenn die Überprüfung erfolgreich war, überprüfen Sie die Zusammenfassung, und klicken Sie auf **OK**.

> [!TIP]
> Sie können die Erstellungseinstellungen dieses Clusters speichern, indem Sie neben der Schaltfläche **OK** auf den Link **Vorlage und Parameter herunterladen** klicken. Diese Informationen können hilfreich sein, wenn Sie später einen ähnlichen Cluster erstellen müssen, z. B. um in einem Notfallwiederherstellungs-Szenario einen Ersatzcluster zu erstellen. (Lesen Sie [Leitfäden zur Notfallwiederherstellung](disaster-recovery.md), um weitere Informationen zu erhalten.)

![Dritte Seite der Bereitstellungsvorlage – Überprüfung](media/avere-vfxt-deploy-3.png)

Auf Seite 4 werden die Nutzungsbedingungen und Links zu Datenschutz- und Preisinformationen angezeigt.

Geben Sie alle fehlenden Kontaktinformationen ein, und klicken Sie auf die Schaltfläche **Erstellen**, um den Bedingungen zuzustimmen und den Avere vFXT for Azure-Cluster zu erstellen.

![Vierte Seite der Bereitstellungsvorlage – Bedingungen, Schaltfläche „Erstellen“](media/avere-vfxt-deploy-4.png)

Die Bereitstellung des Clusters dauert 15 bis 20 Minuten.

## <a name="gather-template-output"></a>Sammeln der Vorlagenausgabe

Wenn die Avere vFXT-Vorlage die Erstellung des Clusters abgeschlossen hat, gibt sie wichtige Informationen über den neuen Cluster aus.

> [!TIP]
> Stellen Sie sicher, dass Sie die **Verwaltungs-IP-Adresse** aus der Vorlagenausgabe kopieren. Sie benötigen diese Adresse zum Verwalten des Clusters.

So finden Sie die Informationen:

1. Navigieren Sie zu der Ressourcengruppe für Ihren Clustercontroller.

1. Klicken Sie auf der linken Seite auf **Bereitstellungen** und dann auf **microsoft-avere.vfxt-template**.

   ![Portalseite „Ressourcengruppe“ mit auf der linken Seite ausgewählten Bereitstellungen und der Anzeige von „microsoft-avere.vfxt-template“ in einer Tabelle unter dem Bereitstellungsnamen](media/avere-vfxt-outputs-deployments.png)

1. Klicken Sie auf der linken Seite auf **Ausgaben**. Kopieren Sie die Werte in die einzelnen Felder.

   ![Ausgabeseite mit der Anzeige von Werten für SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs und MGMT_IP in Feldern rechts neben den Bezeichnungen](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem der Cluster ausgeführt wird und Sie seine Verwaltungs-IP-Adresse kennen, [verbinden Sie sich mit dem Clusterkonfigurationstool](avere-vfxt-cluster-gui.md).

Verwenden Sie die Konfigurationsbenutzeroberfläche, um den Cluster anzupassen, einschließlich der folgenden Setupaufgaben:

* [Aktivieren des Supports](avere-vfxt-enable-support.md)
* [Hinzufügen des Speichers](avere-vfxt-add-storage.md) (falls erforderlich)