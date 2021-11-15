---
title: Bereitstellen eines Azure Arc-Datencontrollers vom Azure Portal | Direkter Verbindungsmodus
description: Hier wird erläutert, wie der Datencontroller im direkten Verbindungsmodus vom Microsoft Azure-Portal bereitgestellt wird.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: overview
ms.openlocfilehash: 9c3c5cd60c7eec5832d8e609e960fd42d38855cc
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557811"
---
#  <a name="create-azure-arc-data-controller-from-azure-portal---direct-connectivity-mode"></a>Bereitstellen eines Azure Arc-Datencontrollers vom Microsoft Azure-Portal im direkten Verbindungsmodus

In diesem Artikel wird beschrieben, wie Sie den Azure Arc-Datencontroller während der aktuellen Vorschau dieses Features im direkten Verbindungsmodus bereitstellen. 

## <a name="complete-prerequisites"></a>Erfüllen der Voraussetzungen

Vergewissern Sie sich zunächst, dass Sie die Voraussetzungen unter [Bereitstellen eines Datencontrollers: direkter Verbindungsmodus (Voraussetzungen)](create-data-controller-direct-prerequisites.md) erfüllen.

## <a name="deploy-azure-arc-data-controller"></a>Bereitstellen eines Azure Arc-Datencontrollers

Der Azure Arc Datencontroller-Erstellungsablauf kann über das Azure-Portal auf eine der folgenden Arten gestartet werden:

- Suchen Sie in der Suchleiste im Azure-Portal nach „Azure Arc-Datencontroller“ und wählen Sie „+ Erstellen“ aus
- Auf der Übersichtsseite Ihres Azure Arc Kubernetes-Clusters,
  - Wählen Sie unter „Einstellungen“ die Option „Erweiterungen“ aus.
  - Wählen Sie auf der Übersichtsseite Erweiterungen die Option „Hinzufügen“ und dann „Azure Arc-Datencontroller“ aus
  - Wählen Sie im Azure Arc-Datencontroller Marketplace-Katalog die Option „Erstellen“ aus
  
Jede dieser Aktionen sollte Sie zur Seite „Azure Arc-Voraussetzungen für den Datencontroller“ des Erstellungsablaufs führen.

- Stellen Sie sicher, das die Option „Direkter Konnektivitätsmodus“ im Azure Arc Kubernetes-Cluster“ aktiviert ist. Wählen Sie „Weiter“ und dann „Datencontroller-Details“ aus
- Wählen Sie auf der Seite **Datencontroller-Details** folgendes aus:
  - Wählen Sie das Azure-Abonnement und die Ressourcengruppe aus, in die der Azure Arc-Datencontroller projiziert wird.
  - Geben Sie einen **Namen** für den Datencontroller ein
  - Wählen Sie einen bereits erstellten **Benutzerdefinierten Speicherort** oder wählen Sie „Neu erstellen“, um einen neuen benutzerdefinierten Speicherort zu erstellen. Wenn Sie einen neuen benutzerdefinierten Speicherort erstellen möchten, geben Sie einen Namen für den neuen benutzerdefinierten Speicherort ein. Wählen Sie dann in der Dropdownliste den Azure Arc-fähigen Kubernetes-Cluster aus, und geben Sie einen Namespace ein, der dem neuen benutzerdefinierten Speicherort zugeordnet werden soll. Wählen Sie schließlich im Fenster „Neuen benutzerdefinierten Speicherort erstellen“ die Option „Erstellen“ aus. Erfahren Sie mehr über [benutzerdefinierte Speicherorte](../kubernetes/conceptual-custom-locations.md)
  - **Kubernetes-Konfiguration**: Wählen Sie in der Dropdownliste eine Kubernetes-Konfigurationsvorlage aus, die ihrer Kubernetes-Verteilung am besten entspricht. Wenn Sie ihre eigenen Einstellungen verwenden oder über ein benutzerdefiniertes Profil verfügen, das Sie verwenden möchten, wählen Sie in der Dropdownliste die Option „Benutzerdefinierte Vorlage“ aus. Geben Sie auf dem Blatt, das auf der rechten Seite geöffnet wird, die Details für die Docker-Anmeldeinformationen, die Repository-Informationen, das Imagetag, die Image-Pullrichtlinie, den Infrastrukturtyp und die Speichereinstellungen für die Daten, die Protokolle und deren Größe, den Diensttyp und die Ports für den Controller und den Verwaltungsproxy ein. Wählen Sie „Übernehmen“ aus, wenn alle erforderlichen Informationen bereitgestellt sind. Sie können auch Ihre eigene Vorlagendatei hochladen, indem Sie oben auf dem Blatt „Eine Vorlage hochladen (JSON)“ auswählen. Wenn Sie benutzerdefinierte Einstellungen verwenden und eine Kopie dieser Einstellungen herunterladen möchten, verwenden Sie hierzu „Diese Vorlage herunterladen (JSON)“. Erfahren Sie mehr über die [benutzerdefinierten Konfigurationsprofile](create-custom-configuration-template.md).
  - Wählen Sie den passenden **Diensttyp** für Ihre Umgebung aus
  - **Anmeldeinformationen für das Dashboard für Metriken und Protokolle:** Geben Sie die Anmeldeinformationen für das Grafana- und das Kibana-Dashboard ein.
  - Klicken Sie auf die Schaltfläche „Weiter“ und dann „Zusätzliche Einstellungen“, um fortzufahren, nachdem alle erforderlichen Informationen bereitgestellt wurden.
- Auf der Seite mit den **Zusätzliche Einstellungen**:
  - **Metrikupload:** Wählen Sie diese Option aus, um Ihre Metriken automatisch in Azure Monitor hochzuladen, um Metriken aggregieren und analysieren, Warnungen auslösen, Benachrichtigungen senden oder automatisierte Aktionen auslösen zu können. Die erforderliche Rolle **Überwachung des Metriken-Verlegers** wird der verwalteten Identität der Erweiterung zugewiesen. 
  - **Protokollupload:** Wählen Sie diese Option aus, um Protokolle automatisch in einen vorhandenen Log Analytics-Arbeitsbereich hochzuladen. Geben Sie die Log Analytics-Arbeitsbereichs-ID und den gemeinsam genutzten Log Analytics-Zugriffsschlüssel ein. 
  - Wählen Sie „Weiter: Tags“ aus, um fortzufahren.
- Auf der Seite **Tags**, geben Sie die Namen und die Werte für Ihre Tags ein und wählen „Weiter“ und dann „Überprüfen + Erstellen“.
- Auf der Seite **Überprüfen + Erstellen** wird Ihnen die Zusammenfassung Ihrer Bereitstellung angezeigt. Stellen Sie sicher, dass alle Einstellungen korrekt sind und wählen Sie „Erstellen“ aus, um die Bereitstellung des Azure Arc-Datencontrollers zu starten.

## <a name="monitor-the-creation-from-azure-portal"></a>Das Überwachen der Erstellung über das Azure-Portal

Wenn Sie im vorherigen Schritt auf die Schaltfläche „Erstellen“ klicken, sollte die Übersichtsseite für die Azure-Bereitstellung gestartet werden, auf der der Fortschritt der Bereitstellung des Azure Arc-Datencontrollers angezeigt wird.

## <a name="monitor-the-creation-from-your-kubernetes-cluster"></a>Das Überwachen der Erstellung über Ihren Kubernetes-Cluster

Der Fortschritt der Bereitstellung des Azure Arc Datencontrollers kann wie folgt überwacht werden:

- Überprüfen Sie, ob die CRDs erstellt werden, indem Sie ```kubectl get crd ``` über Ihren Cluster ausführen  
- Überprüfen Sie, ob der Namespace erstellt wird, indem Sie ```kubectl get ns``` über Ihren Cluster ausführen
- Überprüfen Sie, ob der benutzerdefinierte Speicherort durch Ausführen ```az customlocation list --resource-group <resourcegroup> -o table``` von erstellt wird 
- Überprüfen Sie den Status der Pod-Bereitstellung, indem Sie ```kubectl get pods -ns <namespace>``` ausführen

## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer Azure Arc-fähigen SQL-verwalteten Instanz](create-sql-managed-instance.md)

[Erstellen einer Azure Arc-fähigen Servergruppe für PostgreSQL Hyperscale](create-postgresql-hyperscale-server-group.md)
