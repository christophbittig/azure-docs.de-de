---
title: Übersicht über die Azure Kubernetes Service-Diagnose (AKS)
description: Informationen zur Selbstdiagnose von Clustern in Azure Kubernetes Service.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yunjchoi
ms.openlocfilehash: 838eb4b7abfc5a2fba3fd16d0259bfad2cc13e08
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455965"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Übersicht über die Azure Kubernetes Service-Diagnose (Vorschau)

Das Beheben von Problemen mit dem Azure Kubernetes Service-Cluster (AKS-Cluster) spielt eine wichtige Rolle bei der Wartung Ihres Clusters, insbesondere wenn im Cluster unternehmenskritische Workloads ausgeführt werden. Die AKS-Diagnose ist eine intelligente Funktion zur Selbstdiagnose mit folgenden Eigenschaften:
* Hilft beim Identifizieren und Beheben von Problemen im Cluster 
* Ist cloudnativ
* Erfordert keine zusätzliche Konfiguration oder Abrechnungskosten

Dieses Feature befindet sich jetzt in der Phase der öffentlichen Vorschau. 

## <a name="open-aks-diagnostics"></a>Öffnen der AKS-Diagnose

So greifen Sie auf die AKS-Diagnose zu

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Kubernetes-Cluster.
1. Klicken Sie im linken Navigationsbereich auf **Diagnose und Problembehandlung**, um die AKS-Diagnose zu öffnen.
1. Wählen Sie eine Kategorie aus, die das Problem Ihres Clusters am besten beschreibt, z. B. _Probleme mit Clusterknoten_. Dazu gehen Sie folgendermaßen vor:
    * Verwenden Sie die Schlüsselwörter auf der Kachel der Startseite.
    * Geben Sie ein Schlüsselwort, das Ihr Problem am besten beschreibt, in die Suchleiste ein.

![Startseite](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Anzeigen eines Diagnoseberichts

Nachdem Sie auf eine Kategorie geklickt haben, wird ein spezifischer Diagnosebericht für den Cluster angezeigt. Diagnoseberichte geben alle Probleme in Ihrem Cluster mit Statussymbolen auf intelligente Weise an. Sie können Detailinformationen zu den einzelnen Themen aufrufen, indem Sie auf **Weitere Informationen** klicken. Daraufhin werden ausführliche Beschreibungen zu folgenden Punkten angezeigt:
* Probleme
* Empfohlene Aktionen
* Links zu hilfreichen Dokumenten
* Zugehörige Metriken
* Protokollieren von Daten 

Diagnoseberichte werden basierend auf dem aktuellen Status des Clusters generiert, nachdem verschiedene Überprüfungen ausgeführt wurden. Sie können nützlich sein, um das Problem mit dem Cluster genau zu ermitteln und die nächsten Schritte zur Problembehandlung zu verstehen.

![Diagnosebericht](./media/concepts-diagnostics/diagnostic-report.png)

![Erweiterter Diagnosebericht](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Clustererkentnisse

Die folgenden Diagnoseprüfungen sind unter **Cluster Insights** (Clustererkenntnisse) verfügbar.

### <a name="cluster-node-issues"></a>Probleme mit Clusterknoten

Unter „Cluster Node Issues“ (Probleme mit Clusterknoten) werden knotenbezogene Probleme überprüft, die zu einem unerwarteten Verhalten des Clusters führen.

- Bereitschaftsprobleme bei Knoten
- Knotenfehler
- Nicht genügend Ressourcen
- IP-Konfiguration von Knoten fehlt
- CNI-Fehler bei Knoten
- Knoten nicht gefunden
- Knoten ausgeschaltet
- Authentifizierungsfehler bei Knoten
- Kube-Proxy für Knoten veraltet

### <a name="create-read-update--delete-crud-operations"></a>CRUD-Vorgänge (Erstellen, Lesen, Aktualisieren und Löschen)

Hier wird überprüft, ob Vorgänge zum Erstellen, Lesen, Aktualisieren und Löschen vorliegen, die zu Problemen im Cluster führen.

- Fehler beim Löschen eines verwendeten Subnetzes
- Fehler beim Löschen der Netzwerksicherheitsgruppe
- Fehler beim Löschen der verwendeten Routingtabelle
- Fehler beim Bereitstellen der referenzierten Ressource
- Fehler beim Löschen der öffentlichen IP-Adresse
- Bereitstellungsfehler wegen Bereitstellungskontingent
- Vorgangsfehler wegen Organisationsrichtlinie
- Fehlende Abonnementregistrierung
- Fehler beim Bereitstellen der VM-Erweiterung
- Subnetzkapazität
- Fehler aufgrund von Kontingentüberschreitung

### <a name="identity-and-security-management"></a>Identitäts- und Sicherheitsverwaltung

Die Identitäts- und Sicherheitsverwaltung erkennt Authentifizierungs- und Autorisierungsfehler, die eine Kommunikation mit dem Cluster verhindern.

- Fehler bei der Knotenautorisierung
- 401-Fehler
- 403-Fehler

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie [AKS Periscope](https://aka.ms/aksperiscope), um Protokolle zu erfassen, anhand derer Sie Clusterprobleme beheben können.

* Lesen Sie den Abschnitt [Selektierungsverfahren](/azure/architecture/operator-guides/aks/aks-triage-practices) der AKS-Anleitung für die Betriebsvorgänge am 2. Tag.

* Posten Sie Ihre Fragen oder Ihr Feedback auf [UserVoice](https://feedback.azure.com/d365community/forum/aabe212a-f724-ec11-b6e6-000d3a4f0da0), indem Sie dem Titel „[Diag]“ hinzufügen.
