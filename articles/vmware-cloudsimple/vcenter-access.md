---
title: Azure VMware Solution by CloudSimple – Zugreifen auf den vSphere-Client
description: Hier wird beschrieben, wie Sie auf das vCenter-Portal Ihrer privaten Cloud zugreifen.
author: suzizuber
ms.author: v-szuber
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3c0e60c6dcba217231b03bfae482664746d7ccb3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339103"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Zugreifen auf das vCenter-Portal Ihrer privaten Cloud

Sie können das vCenter-Portal Ihrer privaten Cloud über das Azure-Portal oder das CloudSimple-Portal starten.  Das vCenter-Portal ermöglicht Ihnen die Verwaltung der VMware-Infrastruktur für Ihre private Cloud.

## <a name="before-you-begin"></a>Voraussetzungen

Für den Zugriff auf das vCenter-Portal muss eine Netzwerkverbindung hergestellt und eine DNS-Namensauflösung aktiviert werden.  Sie können eine der folgenden Optionen verwenden, um eine Netzwerkverbindung mit Ihrer privaten Cloud herzustellen.

* [Herstellen einer Verbindung aus der lokalen Umgebung mit CloudSimple mithilfe von ExpressRoute](on-premises-connection.md)
* [Konfigurieren einer VPN-Verbindung mit ihrer privaten CloudSimple-Cloud](set-up-vpn.md)

Informationen zum Einrichten der DNS-Namensauflösung für die VMware-Infrastrukturkomponenten der privaten Cloud finden Sie unter [Konfigurieren von DNS für die Namensauflösung für den vCenter-Zugriff in der privaten Cloud über lokale Arbeitsstationen](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="access-vcenter-from-azure-portal"></a>Zugreifen auf vCenter über das Azure-Portal

Sie können das vCenter-Portal Ihrer privaten Cloud über das Azure-Portal starten.

1. Wählen Sie **Alle Dienste** aus.

2. Suchen Sie nach **CloudSimple Services**.

3. Wählen Sie den CloudSimple-Dienst aus, über den Sie eine Verbindung mit Ihrer privaten Cloud herstellen möchten.

4. Klicken Sie auf der Seite **Overview** (Übersicht) auf **View VMware Private Clouds** (Private VMware-Clouds anzeigen).

    ![Übersicht über den CloudSimple-Dienst](media/cloudsimple-service-overview.png)

5. Wählen Sie die gewünschte private Cloud aus der Liste aus, und klicken Sie dann auf **Launch vSphere Client** (vSphere-Client starten).

    ![VSphere-Client starten](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Zugreifen auf vCenter über das CloudSimple-Portal

Sie können das vCenter-Portal Ihrer privaten Cloud über das CloudSimple-Portal starten.

1. Rufen Sie das [CloudSimple-Portal](access-cloudsimple-portal.md) auf.

2. Wählen Sie unter **Resources** (Ressourcen) die private Cloud, auf die Sie zugreifen möchten, und klicken Sie dann auf **Launch vSphere Client** (vSphere-Client starten).

    ![vSphere-Client starten – Ressourcen](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Sie können das vCenter-Portal auch über den Zusammenfassungsbildschirm Ihrer privaten Cloud starten.

    ![vSphere-Client starten – Zusammenfassung](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen und Verwalten von VLANs/Subnetzen für private Clouds](create-vlan-subnet.md)
* [Berechtigungsmodell der privaten CloudSimple-Cloud von VMware-vCenter](learn-private-cloud-permissions.md)