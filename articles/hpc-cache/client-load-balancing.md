---
title: Lastenausgleich für Clientverbindungen mit Azure HPC Cache
description: Erfahren Sie, wie Sie einen DNS-Server für einen Roundrobin-Lastenausgleich für Azure HPC Cache konfigurieren.
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/20/2021
ms.author: femila
ms.openlocfilehash: d1346a6060fe1093d7dec37f6fbefb43bee800ab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019968"
---
# <a name="load-balance-hpc-cache-client-traffic"></a>Lastenausgleich für HPC Cache-Clientdatenverkehr

In diesem Artikel werden einige grundlegende Methoden zum gleichmäßigen Verteilen des Clientdatenverkehrs an alle Einbindungspunkte in Ihrer Azure HPC Cache-Instanz vorgestellt.

Jede HPC Cache-Instanz hat mindestens drei IP-Adressen. Bei Caches mit höheren Durchsatzwerten sind bis zu 12 möglich. Es ist wichtig, alle IP-Adressen zu nutzen, um von sämtlichen Vorteilen von Azure HPC Cache zu profitieren.

Es gibt verschiedene Lastenausgleichsoptionen für Ihre Clienteinbindungen:

* Manuelles Wählen einer anderen IP-Adresse zur Einbindung für jeden Client
* Einschließen der IP-Adressrotation in Ihre Skripts zur Clienteinbindung
* Konfigurieren eines DNS-Systems zum automatischen Routen von Clientanforderungen zwischen allen verfügbaren Adressen (Roundrobin-DNS)

Das richtige Lastenausgleichssystem hängt von der Komplexität Ihres Workflows, der Anzahl der IP-Adressen in Ihrem Cache und einer großen Anzahl weiterer Faktoren ab. Wenden Sie sich an Ihren Azure-Berater, wenn Sie Hilfe bei der Entscheidung über den für Sie richtigen Ansatz benötigen.

## <a name="assign-ip-addresses-manually"></a>Manuelles Zuweisen von IP-Adressen

Die IP-Adressen zum Einbinden Ihres Caches werden auf den Seiten **Übersicht** und **Einbindungsanweisungen** im Azure-Portal sowie in der Erfolgsmeldung angezeigt, die beim Erstellen eines Caches mit Azure CLI oder PowerShell ausgegeben wird.

Auf der Seite **Einbindungsanweisungen** können Sie für jeden Client einen angepassten Einbindungsbefehl generieren. Wählen Sie beim Erstellen mehrerer Befehle alle Werte für die **Cacheeinbindungsadresse** aus.

Weitere Informationen finden Sie unter [Einbinden der Azure HPC Cache-Instanz](hpc-cache-mount.md).

## <a name="use-scripted-load-balancing"></a>Verwenden eines skriptgesteuerten Lastenausgleichs

Es gibt mehrere Möglichkeiten, Clienteinbindungen unter den verfügbaren IP-Adressen programmgesteuert zu rotieren. Hier sind zwei Beispiele angegeben.

### <a name="mount-command-script-cksum-example"></a>Beispiel für Einbindungsbefehlsskript „cksum“

Dieser Beispieleinbindungsbefehl verwendet die Hashfunktion ``cksum`` und den Hostnamen des Clients, um die Clientverbindungen automatisch auf alle verfügbaren IP-Adressen in Ihrer HPC Cache-Instanz zu verteilen. Wenn alle Clientcomputer eindeutige Hostnamen haben, können Sie diesen Befehl auf jedem Client ausführen, um sicherzustellen, dass alle verfügbaren Einbindungspunkte verwendet werden.

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.0.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/${NAMESPACE} /mnt
```

Um dieses Beispiel in Ihrem Workflow zu nutzen, passen Sie diese Terme an:

* Geben Sie im Ausdruck ```X=``` eine durch Leerzeichen getrennte Liste aller Einbindungsadressen des Caches in sortierter Reihenfolge an.

  Der Ausdruck ``(X=(10.0.0.{7..9})`` legt die Variable X als diese Reihe von Einbindungsadressen fest: {10.0.0.7, 10.0.0.8, 10.0.0.9}. Verwenden Sie die Basis-IP-Adresse des Caches und die Adressen so, wie sie auf der Seite „Übersicht“ Ihres Caches gezeigt werden. Wenn die Adressen nicht aufeinander folgen, listen Sie alle in numerischer Reihenfolge auf.

* Geben Sie im Term ```%3``` die tatsächliche Anzahl der IP-Adressen Ihres Caches an (normalerweise 3, 6, 9 oder 12).

  Wählen Sie z. B. ``%9``, wenn Ihr Cache neun IP-Adressen für die Einbindung von Clients verfügbar macht.

* Verwenden Sie für den Ausdruck ``${NAMESPACE}`` den Namespacepfad des Speicherziels, auf das der Client zugreift.

  Sie können eine von Ihnen definierte Variable verwenden (*NAMESPACE* im Beispiel), oder stattdessen den Literalwert übergeben.
  
  Im Befehlsbeispiel am Ende dieses Abschnitts wird ein Literalwert für den Namespacepfad ``/blob-target-1`` verwendet.

* Wenn Sie auf Ihren Clientcomputern einen benutzerdefinierten lokalen Pfad nutzen möchten, ändern Sie den Wert ``/mnt`` in den gewünschten Pfad.

Hier sehen Sie ein Beispiel eines aufgefüllten Clienteinbindungsbefehls:

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.7.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/blob-target-1 /hpc-cache/blob1 
```

### <a name="round-robin-function-example"></a>Beispiel für Roundrobin-Funktion

Dieses Codebeispiel verwendet Client-IP-Adressen als zufälliges Element, um Clients an alle verfügbaren IP-Adressen des HPC Caches zu verteilen.

```bash
function mount_round_robin() {

  # to ensure the clients are spread out somewhat evenly the default
  # mount point is based on this client's IP octet4 % number of HPC cache mount IPs.

  declare -a MOUNT_IPS="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
  HASH=$(hostname | cksum | cut -f 1 -d ' ')
  DEFAULT_MOUNT_INDEX=$((${HASH} % ${#MOUNT_IPS[@]}))
  ROUND_ROBIN_IP=${MOUNT_IPS[${DEFAULT_MOUNT_INDEX}]}

  DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

  # no need to write again if it is already there
  if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
      echo "${ROUND_ROBIN_IP}:${NFS_PATH} ${DEFAULT_MOUNT_POINT} nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
      mkdir -p "${DEFAULT_MOUNT_POINT}"
      chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
  fi
  if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
      retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
  fi
}
```

## <a name="use-dns-load-balancing"></a>Verwenden des DNS-Lastenausgleichs

In diesem Abschnitt werden die Grundlagen des Konfigurierens eines DNS-Systems erläutert, um Clientverbindungen auf alle Einbindungspunkte Ihrer Azure HPC Cache-Instanz zu verteilen. Diese Methode berücksichtigt nicht die Menge an Datenverkehr, die jeder Client generiert. Sie stellt jedoch sicher, dass die Clients gleichmäßig auf alle Schnittstellen des Cache verteilt werden, anstatt nur eine oder zwei zu nutzen.

Dieses Dokument enthält keine Anweisungen zur Einrichtung und Verwaltung eines DNS-Servers für Ihre Clients in der Azure-Umgebung.

DNS ist nicht erforderlich, um Clients mithilfe des Protokolls NFS und der IP-Adressen einzubinden. DNS *ist* jedoch erforderlich, wenn Sie Domänennamen anstelle von IP-Adressen verwenden möchten, um hardwaregestützte NAS-Systeme zu erreichen, oder wenn Ihr Workflow bestimmte erweiterte Protokolleinstellungen umfasst.

Auf das DNS-System zur Verteilung von Adressen an Clients muss die HPC Cache-Instanz nicht zugreifen. Im Einzelfall möchten Sie vielleicht ein benutzerdefiniertes DNS-System für den Cache selbst verwenden, aber das Konfigurieren dieses Systems ist viel komplizierter als das Einrichten eines solchen Roundrobinsystems für Clients. Wenden Sie sich an den Azure-Support, wenn Sie vorhaben, den [DNS-Server Ihrer *HPC Cache-Instanz* in ein benutzerdefiniertes System zu ändern](configuration.md#set-a-custom-dns-configuration).

### <a name="configure-round-robin-distribution-for-cache-mount-points"></a>Konfigurieren der Roundrobinverteilung für Cacheeinbindungspunkte

Ein RRDNS-System (Roundrobin DNS) leitet Clientanforderungen automatisch an mehrere Adressen weiter.

Um dieses System einzurichten, müssen Sie die Konfigurationsdatei des DNS-Servers so anpassen, dass er bei eingehenden Anforderungen an die Hauptdomänenadresse der HPC Cache-Instanz den Datenverkehr allen Einbindungspunkten des HPC Cache-Systems zuweist. Clients binden die HPC Cache-Instanz unter Verwendung ihres Domänennamens als Serverargument ein und werden automatisch zur nächsten IP-Adresse zur Einbindung geroutet.

Es gibt zwei Hauptschritte zum Konfigurieren von RRDNS:

1. Ändern Sie die Datei ``named.conf`` Ihres DNS-Servers so, dass eine zyklische Reihenfolge für Abfragen an Ihre HPC Cache-Instanz festgelegt wird. Diese Option veranlasst den Server, alle verfügbaren IP-Adressen zu durchlaufen. Fügen Sie eine Anweisung wie die folgende hinzu:

   ```bash
   options {
       rrset-order {
           class IN A name "hpccache.contoso.com" order cyclic;
       };
   };
   ```

1. Konfigurieren Sie wie im folgenden Beispiel für jede verfügbare IP-Adresse A- und Zeigereinträge (PTR).

   Diese ``nsupdate``-Befehle sind ein Beispiel für die korrekte Konfiguration von DNS für eine HPC Cache-Instanz mit dem Domänennamen hpccache.contoso.com und drei Einbindungsadressen (10.0.0.10, 10.0.0.11 und 10.0.0.12):

   ```bash
   update add hpccache.contoso.com. 86400 A 10.0.0.10
   update add hpccache.contoso.com. 86400 A 10.0.0.11
   update add hpccache.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   Mit diesen Befehlen wird ein A-Eintrag für jede der HPC Cache-Adressen erstellt. Außerdem werden Zeigereinträge eingerichtet, um Reverse-DNS-Überprüfungen entsprechend zu unterstützen.

   Das folgende Diagramm zeigt die grundlegende Struktur dieser Konfiguration.

   :::image type="complex" source="media/round-robin-dns-diagram-hpc.png" alt-text="Diagramm der DNS-Konfiguration der Clienteinbindungspunkte.":::
   <Das Diagramm zeigt Verbindungen zwischen drei Kategorien von Elementen: einem einzelnen HPC Cache-Domänennamen (links), drei IP-Adressen (mittlere Spalte) und drei Reverse-DNS-Clientschnittstellen zur internen Verwendung (rechte Spalte). Ein einzelnes Oval auf der linken Seite mit der Bezeichnung „hpccache.contoso.com“ ist durch Pfeile verbunden, die auf drei Ovale zeigen, die mit den IP-Adressen 10.0.0.10, 10.0.0.11 und 10.0.0.12 bezeichnet sind. Die Pfeile vom Oval hpccache.contoso.com zu den drei Ovalen mit IP-Adressen sind mit „A“ beschriftet. Jedes der Ovale mit IP-Adressen ist durch zwei Pfeile mit einem Oval verbunden, das als Clientschnittstelle beschriftet ist. Das Oval mit der IP-Adresse 10.0.0.10 ist mit client-IP-10.contoso.com verbunden. Das Oval mit der IP-Adresse 10.0.0.11 ist mit client-IP-11.contoso.com verbunden. Und das Oval mit der IP-Adresse 10.0.0.12 ist mit client-IP-11.contoso.com verbunden. Die Verbindungen zwischen den Ovalen mit IP-Adressen und den Clientschnittstellen sind zwei Pfeile: ein Pfeil mit der Beschriftung PTR, der vom Oval mit der IP-Adresse auf das Oval mit der Clientschnittstelle zeigt, und ein Pfeil mit der Beschriftung „A“, der vom Oval mit der Clientschnittstelle auf das Oval mit der IP-Adresse zeigt.> :::image-end:::

Nach der Konfiguration des RRDNS-Systems weisen Sie Ihre Clientsysteme an, die HPC Cache-Adresse in ihren Einbindungsbefehlen über dieses System aufzulösen.

## <a name="next-steps"></a>Nächste Schritte

* [Wenden Sie sich an den Support](hpc-cache-support-ticket.md), wenn Sie Hilfe beim Lastenausgleich unter Clients benötigen.
* Informationen zum Verschieben von Daten in die Speicherziele des Caches finden Sie unter [Auffüllen des neuen Azure-Blobspeichers](hpc-cache-ingest.md).
