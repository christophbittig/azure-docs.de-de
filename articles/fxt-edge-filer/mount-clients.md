---
title: Einbinden von Clients in den Microsoft Azure FXT Edge Filer-Cluster
description: Hier erfahren Sie, wie NFS-Clientcomputer den Azure FXT Edge Filer-Hybridspeichercache einbinden können.
author: femila
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: femila
ms.openlocfilehash: 831d023251d4de4d56d35eeb8f81f8d321d143dc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070639"
---
# <a name="tutorial-mount-the-cluster"></a>Tutorial: Einbinden des Clusters

In diesem Tutorial erfahren Sie, wie Sie NFS-Clients in den Azure FXT Edge Filer-Cluster einbinden. Clients binden die virtuellen Namespacepfade ein, die Sie beim Hinzufügen von Back-End-Speicher zugewiesen haben.

In diesem Tutorial wird Folgendes vermittelt:

> [!div class="checklist"]
>
> * Strategien für den Lastenausgleich von Clients im gesamten Spektrum clientseitiger IP-Adressen
> * Vorgehensweise zum Erstellen eines Einbindungspfads auf der Grundlage einer clientseitigen IP-Adresse und einer Namespaceverbindung
> * Argumente für den mount-Befehl

Planen Sie für dieses Tutorial ca. 45 Minuten ein.

## <a name="steps-to-mount-the-cluster"></a>Schritte zum Einbinden des Clusters

Führen Sie die folgenden Schritte aus, um Clientcomputer mit Ihrem Azure FXT Edge Filer-Cluster zu verbinden.

1. Entscheiden Sie, wie Sie den Clientdatenverkehr auf Ihre Clusterknoten aufteilen. Weitere Informationen finden Sie nachfolgend unter [Verteilen der Clientauslastung](#balance-client-load).
1. Identifizieren Sie die Cluster-IP-Adresse und den Verbindungspfad für die Einbindung.
1. Bestimmen Sie den clientseitigen Pfad für die Einbindung.
1. Führen Sie den Befehl [mount](#use-recommended-mount-command-options) mit entsprechenden Argumenten aus.

## <a name="balance-client-load"></a>Verteilen der Clientauslastung

Um die Clientanforderungen auf alle Knoten im Cluster zu verteilen, sollten Sie Clients auf die gesamte Bandbreite der clientseitigen IP-Adressen einbinden. Diese Aufgabe kann auf verschiedene Weise automatisiert werden.

Informationen zum Lastenausgleich mit Roundrobin-DNS für den Cluster finden Sie unter [Konfigurieren von DNS für den Lastenausgleich](configure-network.md#configure-dns-for-load-balancing). Wenn Sie diese Methode verwenden möchten, müssen Sie einen DNS-Server verwalten, was in diesen Artikeln allerdings nicht behandelt wird.

Eine einfachere Methode für kleine Installationen ist die Verwendung eines Skripts, das IP-Adressen im gesamten Bereich zuweist, wenn der Client eingebunden wird.

Für große oder komplizierte Systeme sind ggf. andere Lastenausgleichsmethoden erforderlich. Wenden Sie sich an Ihren Ansprechpartner bei Microsoft, oder stellen Sie eine [Supportanfrage](support-ticket.md), um Unterstützung zu erhalten. (Azure Load Balancer wird aktuell *nicht* für Azure FXT Edge Filer unterstützt.)

## <a name="create-the-mount-command"></a>Erstellen des mount-Befehls

Von Ihrem Client aus ordnet der Befehl ``mount`` den virtuellen Server (vserver) im Azure FXT Edge Filer-Cluster einem Pfad des lokalen Dateisystems zu.

Das Format ist ``mount <FXT cluster path> <local path> {options}``.

Der mount-Befehl umfasst drei Elemente:

* Clusterpfad: Eine Kombination aus IP-Adresse und Namespaceverbindungspfad, wie weiter unten beschrieben.
* Lokaler Pfad – Der Pfad auf dem Client
* Optionen für den mount-Befehl: Diese sind unter [Verwenden empfohlener Optionen für den mount-Befehl](#use-recommended-mount-command-options) aufgeführt.

### <a name="create-the-cluster-path"></a>Erstellen des Clusterpfads

Der Clusterpfad ist eine Kombination aus der *IP-Adresse* des virtuellen Servers und dem Pfad zu einer *Namespaceverbindung*. Die Namespaceverbindung ist ein virtueller Pfad, der beim [Hinzufügen des Speichersystems](add-storage.md#create-a-junction) definiert wurde.

Wenn Sie also beispielsweise ``/fxt/files`` als Namespacepfad verwendet haben, binden Ihre Clients „*IP-Adresse*:/fxt/files“ an ihrem lokalen Bereitstellungspunkt ein.

![Dialogfeld „Neue Verbindung hinzufügen“ mit „/avere/files“ im Feld für den Namespacepfad](media/fxt-mount/fxt-junction-example.png)

Die IP-Adresse ist eine der clientseitigen IP-Adressen, die für den vserver definiert sind. Der Bereich der clientseitigen IP-Adressen ist in der Systemsteuerung für den Cluster an zwei Orten angegeben:

* Tabelle **VServers** (Registerkarte „Dashboard“) –

  ![Registerkarte „Dashboard“ der Systemsteuerung mit ausgewählter Registerkarte „VServer“ (in der Datentabelle unterhalb der Grafik) und eingekreistem IP-Adressabschnitt](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* Einstellungsseite **Clientseitiges Netzwerk** –

  ![Einstellungen > VServer > Konfigurationsseite für clientseitiges Netzwerk mit einem Kreis um den Abschnitt „Adressbereich“ der Tabelle für einen bestimmten VServer](media/fxt-mount/fxt-ip-addresses-settings.png)

Vereinen Sie die IP-Adresse und den Namespacepfad, um den Clusterpfad für den mount-Befehl zu erhalten.

mount-Beispielbefehl für den Client: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Erstellen des lokalen Pfads

Den lokalen Pfad für den mount-Befehl können Sie selbst bestimmen. Sie können eine beliebige Pfadstruktur als Teil Ihres virtuellen Namespace festlegen. Entwerfen Sie einen für Ihren Clientworkflow geeigneten Namespace und lokalen Pfad.

Weitere Informationen zum clientseitigen Namespace finden Sie in der [Namespaceübersicht](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) der Anleitung zur Clusterkonfiguration.

Fügen Sie zusätzlich zu den Pfaden die weiter unten beschriebenen [Optionen für den mount-Befehl](#use-recommended-mount-command-options) hinzu, wenn Sie die einzelnen Clients einbinden.

### <a name="use-recommended-mount-command-options"></a>Verwenden empfohlener Optionen für den mount-Befehl

Um eine problemlose Clienteinbindung sicherzustellen, übergeben Sie diese Einstellungen und Argumente in Ihrem mount-Befehl:

`mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}`

| Erforderliche Einstellungen | BESCHREIBUNG |
--- | ---
``hard`` | Bei zeitweiligen Einbindungen in den Azure FXT Edge Filer-Cluster kommt es zu Anwendungsfehlern und möglichen Datenverlusten.
``proto=netid`` | Diese Option unterstützt eine angemessene Behandlung von NFS-Netzwerkfehlern.
``mountproto=netid`` | Diese Option unterstützt die angemessene Behandlung von Netzwerkfehlern für Einbindungsvorgänge.
``retry=n`` | Legen Sie ``retry=30`` fest, um vorübergehende Einbindungsfehler zu vermeiden. (Bei Einbindungen im Vordergrund wird ein anderer Wert empfohlen.)

| Bevorzugte Einstellungen | BESCHREIBUNG |
| --- | --- |
| `nointr` | Verwenden Sie diese Option, wenn Ihre Clients ältere Betriebssystemkernel (vor April 2008) verwenden, die diese Option unterstützen. Standardmäßig wird die Option „intr“ verwendet. |

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Clients eingebunden haben, können Sie Ihren Workflow testen und erste Schritte mit Ihrem Cluster ausführen.

Wenn Sie Daten in eine neue Cloud-Kernspeichereinheit verschieben müssen, verwenden Sie die parallele Datenerfassung, um die Cachestruktur zu nutzen. Einige Strategien werden in [Verschieben von Daten in den vFXT-Cluster – Parallele Datenerfassung](../avere-vfxt/avere-vfxt-data-ingest.md) beschrieben. (Avere vFXT for Azure ist ein cloudbasiertes Produkt mit einer sehr ähnlichen Cachetechnologie wie Azure FXT Edge Filer.)

Informationen zum Behandeln von Hardwareproblemen finden Sie bei Bedarf unter [Überwachen des Hardwarestatus von Azure FXT Edge Filer](monitor.md).
