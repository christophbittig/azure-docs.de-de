---
title: Bereitstellen von OpenShift Container Platform 4.x in Azure
description: Bereitstellen von OpenShift Container Platform 4.x in Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f1e37e401bd44c4e6077e9a69dbc6ad72f8eaa0d
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693958"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Bereitstellen von OpenShift Container Platform 4.x in Azure

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Die Bereitstellung von OpenShift Container Platform (OCP) 4.2 wird jetzt in Azure über das IPI-Modell (Installer-Provisioned Infrastructure, vom Installer bereitgestellte Infrastruktur) unterstützt.  Die Angebotsseite zum Testen von OpenShift 4 ist [try.openshift.com](https://try.openshift.com/). Zum Installieren von OCP 4.2 in Azure besuchen Sie die Seite [Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned).  Für den Zugriff auf diese Website sind Red Hat-Anmeldeinformationen erforderlich.


## <a name="notes"></a>Notizen 

 - Zum Installieren und Ausführen von OCP 4.x in Azure ist ein Azure Active Directory (AAD)-Dienstprinzipal erforderlich.
     - Dem Dienstprinzipal muss die API-Berechtigung **Application.ReadWrite.OwnedBy** für Azure Active Directory Graph erteilt werden.
     - Ein AAD-Mandantenadministrator muss eine Administratoreinwilligung für diese API-Berechtigung geben, damit sie wirksam wird.
     - Dem Dienstprinzipal müssen die Rollen **Mitwirkender** und **Benutzerzugriffsadministrator** für das Abonnement zugewiesen werden.
 - Das Installationsmodell für OCP 4.x unterscheidet sich von 3.x, und es sind keine Azure Resource Manager-Vorlagen für die Bereitstellung von OCP 4.x in Azure verfügbar.
 - Wenn während des Installationsprozesses Probleme auftreten, wenden Sie sich an das jeweilige Unternehmen (Microsoft oder Red Hat).

| Problembeschreibung | Ansprechpartner |
|-------------------|---------------|
| Azure-spezifische Probleme (AAD, Dienstprinzipal, Azure-Abonnement usw.)                              | Microsoft |
| OpenShift-spezifische Probleme (Installationsfehler, Red Hat-Abonnement usw.) |  Red Hat  |




## <a name="next-steps"></a>Nächste Schritte

- [Getting Started with OpenShift Container Platform](https://docs.openshift.com) (Erste Schritte mit OpenShift Container Platform)
