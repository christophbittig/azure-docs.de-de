---
title: Einbinden einer Azure HPC Cache-Instanz
description: Herstellen einer Verbindung von Clients mit einem Azure HPC Cache-Dienst
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/27/2021
ms.author: femila
ms.openlocfilehash: 45c84008705ef47bd64301e262e878d0308123af
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032960"
---
# <a name="mount-the-azure-hpc-cache"></a>Einbinden einer Azure HPC Cache-Instanz

Nachdem der Cache erstellt wurde, können NFS-Clients mit einem einfachen `mount`-Befehl darauf zugreifen. Der Befehl verbindet einen bestimmten Speicherzielpfad auf der Azure HPC Cache-Instanz mit einem lokalen Verzeichnis auf dem Clientcomputer.

Der mount-Befehl besteht aus den folgenden Elementen:

* Eine der Einbindungsadressen des Cache (aufgelistet auf der Übersichtsseite des Cache).
* Ein Pfad des virtuellen Namespace, den Sie für das Speicherziel festlegen (aufgeführt auf der Seite „Cache-Namespace“).
* Dem lokalen Pfad, der auf dem Client verwendet werden soll.
* Befehlsparametern zur Erfolgsoptimierung dieser Art von NFS-Einbindung.

Auf der Seite **Einbindungsanweisungen** des Caches werden die Informationen und empfohlenen Optionen gesammelt und der Prototyp eines mount-Befehls erstellt, den Sie kopieren können. Weitere Informationen finden Sie im Abschnitt [Verwenden des Hilfsprogramms „Einbindungsanweisungen“](#use-the-mount-instructions-utility).

## <a name="prepare-clients"></a>Vorbereiten der Clients

Befolgen Sie die Richtlinien in diesem Abschnitt, um sicherzustellen, dass Ihre Clients die Azure HPC Cache-Instanz auch einbinden können.

### <a name="provide-network-access"></a>Bereitstellen von Netzwerkzugriff

Die Clientcomputer müssen über Netzwerkzugriff auf das virtuelle Netzwerk des Caches und das private Subnetz verfügen.

Erstellen Sie beispielsweise Client-VMs innerhalb des gleichen virtuellen Netzwerks, oder verwenden Sie einen Endpunkt, ein Gateway oder eine andere Lösung im virtuellen Netzwerk für den Zugriff von außen. (Denken Sie daran, dass nur der Cache selbst im Subnetz des Caches gehostet werden sollte.)

### <a name="install-utilities"></a>Installieren der Hilfsprogramme

Installieren Sie zur Unterstützung des NFS-mount-Befehls das entsprechende Linux-Hilfsprogramm:

* Für Red Hat Enterprise Linux oder SuSE: `sudo yum install -y nfs-utils`
* Für Ubuntu oder Debian: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Erstellen eines lokalen Pfads

Erstellen Sie auf jedem Client einen Pfad zum lokalen Verzeichnis, über den eine Verbindung mit dem Cache hergestellt wird. Erstellen Sie einen Pfad für jeden Namespacepfad, den Sie einbinden möchten.

Beispiel: `sudo mkdir -p /mnt/hpc-cache-1/target3`

Die Seite [Einbindungsanweisungen](#use-the-mount-instructions-utility) im Azure-Portal enthält einen Prototypbefehl, den Sie kopieren können.

Wenn Sie den Clientcomputer mit dem Cache verbinden, ordnen Sie diesen Pfad einem virtuellen Namespacepfad zu, der einen Speicherzielexport darstellt. Erstellen Sie Verzeichnisse für jeden der virtuellen Namespacepfade, die der Client verwenden wird.

## <a name="use-the-mount-instructions-utility"></a>Verwenden des Hilfsprogramms „Einbindungsanweisungen“

Sie können die Seite **Einbindungsanweisungen** im Azure-Portal verwenden, um einen kopierbaren Einbindungsbefehl zu erstellen. Öffnen Sie die Seite im Abschnitt **Konfigurieren** der Ansicht „Cache“ im Portal.

Bevor Sie den Befehl auf einem Client verwenden, stellen Sie sicher, dass der Client die Voraussetzungen erfüllt und über die Software verfügt, die erforderlich ist, um den NFS-Befehl `mount` wie oben unter [Vorbereiten der Clients](#prepare-clients) beschrieben, zu verwenden.

![Screenshot einer Azure HPC Cache-Instanz im Azure-Portal mit der Seite „Konfigurieren > Einbindungsanweisungen“](media/mount-instructions.png)

Erstellen Sie anhand dieser Vorgehensweise den Einbindungsbefehl.

1. Passen Sie das Feld **Clientpfad** an. Dieses Feld bietet einen Beispielbefehl, den Sie verwenden können, um einen lokalen Pfad auf dem Client zu erstellen. Der Client greift lokal in diesem Verzeichnis auf den Inhalt von Azure HPC Cache zu.

   Klicken Sie auf das Feld, und bearbeiten Sie den Befehl, um den gewünschten Verzeichnisnamen einzuschließen. Der Name wird am Ende der Zeichenfolge nach `sudo mkdir -p` angezeigt.

   ![Screenshot des Felds „Clientpfad“ mit am Ende positioniertem Cursor.](media/mount-edit-client.png)

   Nachdem Sie das Feld bearbeitet haben, wird der Einbindungsbefehl am unteren Rand der Seite mit dem neuen Clientpfad aktualisiert.

1. Wählen Sie die **Einbindungsadresse des Caches** in der Liste aus. In diesem Menü werden alle [Clienteinbindungspunkte](#find-mount-command-components) des Caches aufgeführt.

   Gleichen Sie die Clientlast über alle verfügbaren Einbindungsadressen hinweg aus, um eine bessere Cacheleistung zu erzielen.

   ![Screenshot des Felds „Einbindungsadresse des Caches“ mit Auswahlfeld, in dem drei IP-Adressen zur Auswahl angezeigt werden.](media/mount-select-ip.png)

1. Wählen Sie den **Pfad des virtuellen Namespace** aus, der für den Client verwendet werden soll. Diese Pfade sind mit Exporten auf dem Back-End-Speichersystem verknüpft.

   ![Der Screenshot zeigt das Feld „Pfad des virtuellen Namespace“ mit geöffnetem Selektor.](media/mount-select-target.png)

   Sie können die Pfade des virtuellen Namespace auf der Portalseite **Namespace** anzeigen. Informationen zur Vorgehensweise finden Sie unter [Einrichten des aggregierten Namespace](add-namespace-paths.md).

   Weitere Informationen zur aggregierten Namespacefunktion von Azure HPC Cache finden Sie unter [Planen des aggregierten Namespace](hpc-cache-namespace.md).

1. In das Feld **Einbindungsbefehl** wird automatisch ein angepasster Einbindungsbefehl eingefügt, für den die Einbindungsadresse, der virtuelle Namespacepfad und der Clientpfad verwendet werden, die Sie in den vorherigen Feldern festgelegt haben.

   Klicken Sie auf das Kopiersymbol auf der rechten Seite des Felds, um den Befehl automatisch in die Zwischenablage zu kopieren.

   ![Der Screenshot zum Prototyp des Felds „Einbindungsbefehl“ zeigt einen daraufhin eingeblendeten Text für die Schaltfläche „In die Zwischenablage kopieren“ an.](media/mount-command-copy.png)

   Darunter werden alternative Mount-Befehle angezeigt, die den gleichen Client-Pfad und Namespace-Pfad haben, aber unterschiedliche Cache-Mount-Adressen verwenden. Um eine optimale Leistung zu erzielen, müssen Sie die Clients gleichmäßig auf alle verfügbaren Adressen des HPC-Cache verteilen.

1. Verwenden Sie den kopierten mount-Befehl auf dem Clientcomputer, um eine Verbindung zwischen dem Clientcomputer und dem Azure HPC Cache herzustellen. Sie können den mount-Befehl direkt über die Befehlszeile auf dem Client ausgeben oder ihn in ein Setupskript oder eine -vorlage auf dem Client einfügen.

## <a name="understand-mount-command-syntax"></a>Grundlegendes zur Syntax des mount-Befehls

Der mount-Befehl weist das folgende Format auf:

> sudo mount {*options*} *cache_mount_address*:/*namespace_path* *local_path*

Beispiel:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Nachdem dieser Befehl erfolgreich ausgeführt wurde, wird der Inhalt des Speicherexports im ``hpccache``-Verzeichnis auf dem Client angezeigt.

### <a name="mount-command-options"></a>Einbindungsbefehlsoptionen

Um eine problemlose Clienteinbindung sicherzustellen, übergeben Sie diese Einstellungen und Argumente in Ihrem Einbindungsbefehl:

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| Empfohlene Einstellungen für den mount-Befehl | BESCHREIBUNG |
--- | ---
``hard`` | Softwareseitige Einbindungen für Azure HPC Cache sind Anwendungsfehlern und möglichen Datenverlusten zugeordnet.
``proto=tcp`` | Diese Option unterstützt eine angemessene Behandlung von NFS-Netzwerkfehlern.
``mountproto=tcp`` | Diese Option unterstützt die angemessene Behandlung von Netzwerkfehlern für Einbindungsvorgänge.
``retry=<value>`` | Legen Sie ``retry=30`` fest, um vorübergehende Einbindungsfehler zu vermeiden. (Bei Einbindungen im Vordergrund wird ein anderer Wert empfohlen.)

### <a name="find-mount-command-components"></a>Suchen nach Komponenten für den mount-Befehl

Wenn Sie einen Einbindungsbefehl ohne die Seite **Einbindungsanweisungen** erstellen möchten, finden Sie die dafür erforderlichen Komponenten auf den folgenden Seiten: die Einbindungsadressen auf der Seite **Übersicht** des Caches und die Pfade zum virtuellen Namespace auf der Seite **Namespace**.

![Screenshot der Übersichtsseite einer Azure HPC Cache-Instanz mit einem Hervorhebungskasten um die Einbindungsadressliste rechts unten](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Die Cache-Einbindungsadressen entsprechen Netzwerkschnittstellen innerhalb des Cache-Subnetzes. Diese NICs werden in einer Ressourcengruppe mit Namen angezeigt, die auf `-cluster-nic-` und einer Zahl enden. Sie dürfen diese Schnittstellen nicht ändern oder löschen, sonst ist der Cache nicht mehr verfügbar.

Die Pfade des virtuellen Namespace werden auf der Einstellungsseite **Namespace** des Caches angezeigt.

![Der Screenshot der Portalseite „Einstellungen > Namespace“ mit einem Hervorhebungsrahmen, der die erste Spalte der Tabelle umgibt: „Namespacepfad“](media/view-namespace-paths.png)

## <a name="use-all-available-mount-addresses"></a>Alle verfügbaren Mount-Adressen verwenden

Sie müssen den Client-Datenverkehr auf alle für den Cache aufgeführten IP-Adressen verteilen. Wenn Sie alle Ihre Clients an nur eine Adresse mounten, wird die Leistung des Cache beeinträchtigt.

Sie können verschiedene Mount-Adressen für verschiedene Clients manuell oder durch Erstellung eines Skripts auswählen. Sie können auch einen DNS-Server verwenden, der für Round-Robin-DNS (RRDNS) konfiguriert ist, um Client-Mounts automatisch auf alle verfügbaren Adressen zu verteilen. Lesen Sie [Lastenausgleich für HPC Cache-Datenverkehr](client-load-balancing.md), um mehr zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr darüber, wie Sie den gesamten Durchsatz Ihres Caches nutzen können, indem Sie die [Client-Last ausgleichen](client-load-balancing.md).
* Informationen zum Verschieben von Daten in die Speicherziele des Caches finden Sie unter [Auffüllen des neuen Azure-Blobspeichers](hpc-cache-ingest.md).
