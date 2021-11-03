---
title: Problembehandlung für NFS-Speicherziele in Azure HPC Cache
description: Tipps zur Vermeidung und Behebung von Konfigurationsfehlern und anderen Problemen, die beim Erstellen eines NFS-Speicherziels zu Fehlern führen können
author: femila
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2020
ms.author: femila
ms.openlocfilehash: 4c2cf6995395e44819a6a63f26609e72d81456e5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077362"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Behandeln von Problemen mit der NAS-Konfiguration und dem NFS-Speicherziel

Dieser Artikel enthält Lösungen für einige häufige Konfigurationsfehler und andere Probleme, die verhindern können, dass Azure HPC Cache ein NFS-Speichersystem als Speicherziel hinzufügt.

Dieser Artikel enthält Details zum Überprüfen von Ports und Aktivieren des Root-Zugriffs auf ein NAS-System. Er enthält außerdem ausführliche Informationen zu weniger häufigen Problemen, durch die beim Erstellen des NFS-Speicherziels Fehler auftreten können.

> [!TIP]
> Informieren Sie sich vor der Verwendung dieses Leitfadens über die [Voraussetzungen für NFS-Speicherziele](hpc-cache-prerequisites.md#nfs-storage-requirements).

Wenn die Lösung für Ihr Problem hier nicht enthalten ist, [öffnen Sie ein Supportticket](hpc-cache-support-ticket.md), damit Microsoft Service and Support mit Ihnen zusammen das Problem untersuchen und beheben kann.

## <a name="check-port-settings"></a>Überprüfen der Porteinstellungen

Azure HPC Cache benötigt Lese-/Schreibzugriff auf mehrere UDP/TCP-Ports auf dem Back-End-NAS-Speichersystem. Stellen Sie sicher, dass auf diese Ports auf dem NAS-System zugegriffen werden kann und dass der Datenverkehr für diese Ports durch alle Firewalls zwischen dem Speichersystem und dem Cachesubnetz zugelassen wird. Sie müssen möglicherweise mit Firewall- und Netzwerkadministratoren für Ihr Rechenzentrum zusammenarbeiten, um diese Konfiguration zu überprüfen.

Die Ports für Speichersysteme verschiedener Anbieter sind jeweils unterschiedlich. Überprüfen Sie daher die Anforderungen Ihres Systems beim Einrichten eines Speicherziels.

Im Allgemeinen benötigt der Cache Zugriff auf die folgenden Ports:

| Protocol | Port  | Dienst  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | mountd   |
| TCP/UDP  | 4047  | status   |

Verwenden Sie den folgenden ``rpcinfo``-Befehl, um Angaben zu den für Ihr System benötigten Ports zu erhalten. Mit dem Befehl unten werden die Ports aufgelistet und die relevanten Ergebnisse in einer Tabelle formatiert. (Ersetzen Sie den Platzhalter *<storage_IP>* durch die IP-Adresse Ihres Systems.)

Dieser Befehl kann über einen beliebigen Linux-Client mit installierter NFS-Infrastruktur ausgeführt werden. Wenn Sie einen Client innerhalb des Clustersubnetzes verwenden, können Sie damit auch die Konnektivität zwischen dem Subnetz und dem Speichersystem überprüfen.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Stellen Sie sicher, dass alle von der ``rpcinfo``-Abfrage zurückgegebenen Ports uneingeschränkten Datenverkehr aus dem Azure HPC Cache-Subnetz zulassen.

Überprüfen Sie diese Einstellungen im NAS und auch auf allen Firewalls zwischen dem Speichersystem und dem Cachesubnetz.

## <a name="check-root-access"></a>Überprüfen des Root-Zugriffs

Azure HPC Cache benötigt Zugriff auf die Exporte Ihres Speichersystems, um das Speicherziel zu erstellen. Insbesondere werden die Exporte als Benutzer-ID 0 bereitgestellt.

Bei unterschiedlichen Speichersystemen werden verschiedene Methoden verwendet, um diesen Zugriff zu ermöglichen:

* Bei Linux-Servern wird im Allgemeinen ``no_root_squash`` zum exportierten Pfad in ``/etc/exports`` hinzugefügt.
* Bei NetApp- und EMC-Systemen wird der Zugriff in der Regel mit Exportregeln gesteuert, die an bestimmte IP-Adressen oder Netzwerke gebunden sind.

Beachten Sie bei Verwendung von Exportregeln, dass der Cache mehrere verschiedene IP-Adressen aus dem Cachesubnetz verwenden kann. Genehmigen Sie den Zugriff aus dem gesamten Bereich möglicher Subnetz-IP-Adressen.

> [!NOTE]
> Der Cache benötigt zwar root-Zugriff auf das Back-End-Speichersystem, Sie können den Zugriff jedoch für Clients beschränken, die über den Cache eine Verbindung herstellen. Weitere Einzelheiten finden Sie unter [Steuern des Clientzugriffs](access-policies.md#root-squash).

Arbeiten Sie mit Ihrem NAS-Speicheranbieter zusammen, um die richtige Zugriffsebene für den Cache zu ermöglichen.

### <a name="allow-root-access-on-directory-paths"></a>Zulassen von Root-Zugriff auf Verzeichnispfade
<!-- linked in prereqs article -->

Bei NAS-Systemen, die hierarchische Verzeichnisse exportieren, benötigt Azure HPC Cache Root-Zugriff auf jede Exportebene.

Ein System könnte beispielsweise drei Exporte wie die folgenden anzeigen:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

Der Export ``/ifs/accounting/payroll`` ist ein untergeordnetes Element von ``/ifs/accounting``, und ``/ifs/accounting`` ist selbst ein untergeordnetes Element von ``/ifs``.

Wenn Sie den ``payroll``-Export als ein HPC Cache-Speicherziel hinzufügen, stellt der Cache tatsächlich ``/ifs/`` bereit und greift von dort aus auf das payroll-Verzeichnis zu. Daher benötigt Azure HPC Cache Root-Zugriff auf ``/ifs``, um auf den Export ``/ifs/accounting/payroll`` zuzugreifen.

Diese Anforderung steht im Bezug zu der Methode, mit der der Cache Dateien indiziert und Dateikonflikte vermeidet, wobei vom Speichersystem bereitstellte Dateihandles verwendet werden.

Ein NAS-System mit hierarchischen Exporten kann verschiedene Dateihandles für dieselbe Datei zur Verfügung stellen, wenn die Datei aus verschiedenen Exporten abgerufen wird. Beispielsweise kann ein Client ``/ifs/accounting`` bereitstellen und auf die Datei ``payroll/2011.txt`` zugreifen. Ein anderer Client stellt ``/ifs/accounting/payroll`` bereit und greift auf die Datei ``2011.txt`` zu. Je nachdem, wie das Speichersystem Dateihandles zuweist, können diese beiden Clients die gleiche Datei mit unterschiedlichen Dateihandles (eines für ``<mount2>/payroll/2011.txt`` und eines für ``<mount3>/2011.txt``) erhalten.

Das Back-End-Speichersystem speichert interne Aliase für Dateihandles, aber Azure HPC Cache kann nicht erkennen, welche Dateihandles im Index auf dasselbe Element verweisen. Daher ist es möglich, dass im Cache verschiedene Schreibvorgänge für dieselbe Datei zwischengespeichert sind und die Änderungen falsch angewendet werden, da nicht bekannt ist, dass es sich um dieselbe Datei handelt.

Um diesen möglichen Konflikt bei Dateien in mehreren Exporten zu vermeiden, stellt Azure HPC Cache automatisch den niedrigsten verfügbaren Export im Pfad bereit (``/ifs`` im Beispiel) und verwendet das von diesem Export bereitgestellte Dateihandle. Wenn mehrere Exporte denselben Basispfad verwenden, benötigt Azure HPC Cache Root-Zugriff auf diesen Pfad.

<!-- ## Enable export listing

The NAS must list its exports when the Azure HPC Cache queries it.

On most NFS storage systems, you can test this by sending the following query from a Linux client: ``showmount -e <storage IP address>``

Use a Linux client from the same virtual network as your cache, if possible.

If that command doesn't list the exports, the cache will have trouble connecting to your storage system. Work with your NAS vendor to enable export listing.  -->

## <a name="adjust-vpn-packet-size-restrictions"></a>Anpassen der Größenbeschränkungen für VPN-Pakete
<!-- link in prereqs article and configuration article -->

Wenn zwischen dem Cache und dem NAS-Gerät ein VPN vorhanden ist, kann es sein, dass 1500-Byte-Ethernet-Pakete in voller Größe vom VPN blockiert werden. Dieses Problem liegt möglicherweise vor, wenn große Austauschvorgänge zwischen NAS und der Azure HPC Cache-Instanz nicht beendet werden, kleinere Updates jedoch erwartungsgemäß funktionieren.

Es gibt keine einfache Möglichkeit, um festzustellen, ob Ihr System dieses Problem aufweist, es sei denn, Sie kennen die Details Ihrer VPN-Konfiguration. Nachfolgend sind einige Methoden aufgeführt, die Ihnen bei der Überprüfung dieses Problems helfen können.

* Verwenden Sie Paketsniffer auf beiden Seiten des VPN, um zu ermitteln, welche Pakete erfolgreich übertragen werden.
* Wenn Ihr VPN Ping-Befehle zulässt, können Sie das Senden eines Pakets in voller Größe testen.

  Führen Sie einen Ping-Befehl über das VPN an den NAS mit den folgenden Optionen aus. (Ersetzen Sie den Wert *<storage_IP>* durch die IP-Adresse Ihres Speichersystems.)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Diese Optionen werden im Befehl verwendet:

  * ``-M do``: Nicht fragmentieren
  * ``-c 1``: Nur ein Paket senden
  * ``-s 1472``: Größe der Nutzlast auf 1472 Bytes festlegen. Dies ist die maximale Größe der Nutzlast für ein 1500-Byte-Paket nach Berücksichtigung des Ethernet-Aufwands.

  Eine erfolgreiche Antwort sieht wie folgt aus:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Wenn das Ping-Signal mit 1472 Bytes fehlschlägt, liegt wahrscheinlich ein Problem mit der Paketgröße vor.

Um dieses Problem zu beheben, müssen Sie möglicherweise MSS-Clamping für das VPN konfigurieren, damit das Remotesystem die maximale Framegröße ordnungsgemäß erkennt. Weitere Informationen finden Sie in der [Dokumentation zu IPSec-/IKE-Parametern für VPN-Gateways](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec).

In einigen Fällen kann es hilfreich sein, die MTU-Einstellung für Azure HPC Cache in 1400 zu ändern. Wenn Sie jedoch die MTU für den Cache einschränken, müssen Sie auch die MTU-Einstellungen für Clients und Back-End-Speichersysteme einschränken, die mit dem Cache interagieren. Weitere Informationen finden Sie [Konfigurieren zusätzlicher Azure HPC Cache-Einstellungen](configuration.md#adjust-mtu-value).

## <a name="check-for-acl-security-style"></a>Prüfen auf ACL-Sicherheitsstil

Einige NAS-Systeme verwenden einen Hybridsicherheitsstil, bei dem Zugriffssteuerungslisten (ACLs) mit herkömmlicher POSIX- oder UNIX-Sicherheit kombiniert werden.

Wenn Ihr System den Sicherheitsstil als UNIX oder POSIX ohne das Akronym „ACL“ angibt, sind Sie von diesem Problem nicht betroffen.

Bei Systemen, die ACLs verwenden, muss Azure HPC Cache zusätzliche benutzerspezifische Werte nachverfolgen, um den Dateizugriff zu steuern. Dies erfolgt durch Aktivieren eines Zugriffscaches. Es gibt benutzerseitig keine Möglichkeit, den Zugriffscache zu aktivieren, aber Sie können ein Supportticket öffnen, um die Aktivierung für die betroffenen Speicherziele auf Ihrem Cachesystem anzufordern.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie ein Problem haben, das in diesem Artikel nicht behandelt wurde, [kontaktieren Sie den Support](hpc-cache-support-ticket.md), um Hilfe von Experten zu bekommen.
