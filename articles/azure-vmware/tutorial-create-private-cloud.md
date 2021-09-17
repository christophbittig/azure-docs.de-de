---
title: 'Tutorial: Bereitstellen einer privaten Azure VMware Solution-Cloud'
description: Hier erfahren Sie, wie Sie eine private Azure VMware Solution-Cloud erstellen und bereitstellen.
ms.topic: tutorial
ms.date: 06/11/2021
ms.openlocfilehash: d91e9fe9261aa4a04f5e5dffd3505742d9886623
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730346"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>Tutorial: Bereitstellen einer privaten Azure VMware Solution-Cloud

Die private Azure VMware Solution-Cloud ermöglicht das Bereitstellen eines vSphere-Clusters in Azure. Für jede erstellte private Cloud gibt es standardmäßig ein vSAN-Cluster. Sie können Cluster hinzufügen, löschen und skalieren.  Pro Cluster sind mindestens drei Hosts erforderlich. Später können nacheinander weitere Host hinzugefügt werden. Pro Cluster sind bis zu 16 Hosts möglich. Pro privater Cloud sind maximal vier Cluster möglich.  Die erste Bereitstellung von Azure VMware Solution verfügt über drei Hosts. 

Testcluster stehen zu Bewertungszwecken zur Verfügung und sind auf drei Hosts begrenzt. Pro privater Cloud gibt es einen einzelnen Testcluster. Sie können einen Testcluster während des Evaluierungszeitraums unter Verwendung eines einzelnen Hosts skalieren.

Sie verwenden vSphere und NSX-T Manager, um die meisten weiteren Aspekte der Clusterkonfiguration oder des Clusterbetriebs zu verwalten. Der gesamte lokale Speicher jedes Hosts in einem Cluster befindet sich unter der Kontrolle von vSAN.

>[!TIP]
>Sie können den Cluster später jederzeit erweitern und zusätzliche Cluster hinzufügen, wenn Sie die anfängliche Bereitstellungsanzahl erhöhen möchten.

Da Sie Ihre private Cloud zunächst nicht mit Ihrer lokalen vCenter-Instanz verwalten können, müssen Sie zusätzliche Schritte für die Konfiguration durchführen.  In diesem Tutorial werden diese Schritte und die zugehörigen Voraussetzungen behandelt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer privaten Cloud von Azure VMware Solution
> * Vergewissern, dass die private Cloud bereitgestellt wurde

## <a name="prerequisites"></a>Voraussetzungen

- Geeignete Administratorrechte und Berechtigung zum Erstellen einer privaten Cloud. Sie müssen mindestens die Rolle „Mitwirkender“ im Abonnement haben.
- Verwenden Sie die Informationen, die Sie im Tutorial [Planen der Azure VMware Solution-Bereitstellung](plan-private-cloud-deployment.md) gesammelt haben.
- Vergewissern Sie sich, dass das Netzwerk ordnungsgemäß konfiguriert ist, wie unter [Checkliste für die Netzwerkplanung für Azure VMware Solution](tutorial-network-checklist.md) beschrieben.
- Hosts wurden bereitgestellt, und der Ressourcenanbieter [Microsoft.AVS wurde registriert](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider).

## <a name="create-a-private-cloud"></a>Erstellen einer privaten Cloud

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen einer privaten Cloud von Azure VMware Solution
> * Vergewissern, dass die private Cloud bereitgestellt wurde
> * Löschen einer privaten Azure VMware Solution-Cloud

Im nächsten Tutorial erfahren Sie, wie Sie eine Jumpbox erstellen. Die Jumpbox wird verwendet, um eine Verbindung mit Ihrer Umgebung herzustellen, damit Sie Ihre private Cloud lokal verwalten können.


> [!div class="nextstepaction"]
> [Tutorial: Hier erfahren Sie, wie Sie auf eine private Azure VMware Solution-Cloud zugreifen.](tutorial-access-private-cloud.md)
