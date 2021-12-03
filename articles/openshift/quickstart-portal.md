---
title: Bereitstellen eines ARO-Clusters mithilfe des Azure-Portals
description: Bereitstellen eines ARO-Clusters mithilfe des Azure-Portals
author: rahulmehta
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: quickstart
ms.date: 10/21/2021
ms.openlocfilehash: 15fe2cc5c9735c28de867510cd5497a892554db8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271158"
---
# <a name="quickstart-deploy-an-azure-red-hat-openshift-aro-cluster-using-the-azure-portal"></a>Schnellstart: Bereitstellen eines ARO-Clusters (Azure Red Hat OpenShift) mithilfe des Azure-Portals

Azure Red Hat OpenShift (ARO) ist ein verwalteter OpenShift-Dienst, mit dem Sie Cluster schnell bereitstellen und verwalten können. In dieser Schnellstartanleitung stellen Sie einen ARO-Cluster mit dem Azure-Portal bereit.

## <a name="prerequisites"></a>Voraussetzungen
Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-an-aro-cluster"></a>Erstellen eines ARO-Clusters
1.  Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.
2.  Wählen Sie **Container** > **Azure Red Hat OpenShift** aus.
3.  Konfigurieren Sie auf der Seite **Grundlagen** die folgenden Optionen:
    * **Projektdetails**:
        *   Wählen Sie ein **Azure-Abonnement** aus.
        *   Wählen Sie eine **Azure-Ressourcengruppe** wie z. B. *myResourceGroup* aus, oder erstellen Sie eine solche.
    * **Clusterdetails**:
        * Wählen Sie eine **Region** für den ARO-Cluster aus.
        *   Geben Sie unter **OpenShift cluster name** (Name des OpenShift-Clusters) einen Namen ein, etwa *myAROCluster*.
        *   Geben Sie unter **Domänenname** einen Namen ein.
        *   Wählen Sie Werte für **Master VM Size** (Größe der Master-VM) und **Worker VM Size** (Größe der Worker-VM) aus.

![Registerkarte **Grundlagen** im Azure-Portal](./media/portal-quickstart/basics-tab.jpg)

4.  Konfigurieren Sie auf der Seite **Authentifizierung** die folgenden Optionen:
    1) Wählen Sie in der Auswahl für den Dienstprinzipaltyp die Option **Vorhandenes verwenden** aus. (Wurde die Option bereits vorab ausgewählt, ist keine Aktion erforderlich, und Sie können mit den nächsten Schritten fortfahren.)
    2) Suchen Sie nach dem Ressourcenanbieter für Azure Red Hat OpenShift, und wählen Sie ihn aus. 

>[!NOTE]
>Ignorieren Sie die Schaltfläche „Neu erstellen“ und andere optionale Felder.

![Registerkarte **Authentifizierung** im Azure-Portal](./media/portal-quickstart/authentication.jpg)

5.  Stellen Sie auf der Registerkarte **Netzwerk** sicher, dass Folgendes konfiguriert ist:
    * Virtuelles Netzwerk
    * Mastersubnetz
    * Workersubnetz
    * API-Serversichtbarkeit
    * Eingangssichtbarkeit

![Registerkarte **Netzwerk** im Azure-Portal](./media/portal-quickstart/networking.jpg)

6.  Fügen Sie im Abschnitt **Tags** Tags hinzu, um Ihre Ressourcen zu organisieren.

![Registerkarte **Tags** im Azure-Portal](./media/portal-quickstart/tags.jpg)
 
7.  Klicken Sie auf **Überprüfen + erstellen** und danach auf **Erstellen**, wenn die Überprüfung abgeschlossen ist.

![Registerkarte **Überprüfen und erstellen** im Azure-Portal](./media/portal-quickstart/review.jpg)
 
8.  Die Erstellung eines ARO-Clusters dauert etwa 35 bis 45 Minuten. Navigieren Sie nach Abschluss der Bereitstellung wie folgt zu Ihrer Ressource:
    *   Klicken Sie auf **Zu Ressource wechseln**. Oder:
    *   Navigieren Sie zur ARO-Clusterressourcengruppe, und wählen Sie die ARO-Ressource aus.
        *   Siehe folgendes Beispielclusterdashboard: Navigieren Sie zu *myResourceGroup*, und wählen Sie die Ressource *myAROCluster* aus.
