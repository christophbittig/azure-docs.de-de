---
title: Avere vFXT DNS – Azure
description: Konfigurieren eines DNS-Servers für den Roundrobin-Lastenausgleich mit Avere vFXT für Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: rohogue
ms.openlocfilehash: 26f85b4d3a2cc49e55d228fbd73ae7cb3b66bf5d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337868"
---
# <a name="avere-cluster-dns-configuration"></a>DNS-Konfiguration des Avere-Clusters

In diesem Abschnitt werden die Grundlagen der Konfiguration eines DNS-Systems für den Lastenausgleich Ihres Avere vFXT-Clusters erläutert.

Dieses Dokument *enthält keine* Anweisungen zur Einrichtung und Verwaltung eines DNS-Servers in der Azure-Umgebung.

Anstatt Roundrobin-DNS für den Lastenausgleich in einem vFXT-Cluster in Azure zu verwenden, sollten Sie IP-Adressen mithilfe manueller Methoden gleichmäßig zwischen den Clients zuzuweisen, wenn sie eingebunden sind. Mehrere Methoden sind unter [Einbinden des Avere-Clusters](avere-vfxt-mount-clients.md) beschrieben.

Beachten Sie diese Faktoren bei der Entscheidung, ob Sie einen DNS-Server verwenden:

* Wenn nur über NFS-Clients auf Ihr System zugegriffen wird, ist die Verwendung von DNS nicht erforderlich. Alle Netzwerkadressen können über numerische IP-Adressen angegeben werden.

* Wenn Ihr System den SMB-Zugriff (CIFS) unterstützt, ist DNS erforderlich, da Sie eine DNS-Domäne für den Active Directory-Server angeben müssen.

* Wenn Sie die Kerberos-Authentifizierung verwenden möchten, ist DNS erforderlich.

## <a name="load-balancing"></a>Lastenausgleich

Um die Gesamtlast zu verteilen, konfigurieren Sie Ihre DNS-Domäne so, dass sie die Roundrobin-Lastverteilung für clientseitige IP-Adressen verwendet.

## <a name="configuration-details"></a>Konfigurationsdetails

Wenn Clients auf den Cluster zugreifen, gleicht RRDNS (Roundrobin-DNS) ihre Anforderungen automatisch zwischen allen verfügbaren Schnittstellen aus.

Um dieses System einzurichten, müssen Sie die Konfigurationsdatei des DNS-Servers so anpassen, dass er bei Einbindungsanforderungen an die Hauptdomänenadresse des vFXT-Clusters den Datenverkehr allen Bereitstellungspunkten des vFXT-Clusters zuweist. Clients binden den vFXT-Cluster unter Verwendung seines Domänennamens als Serverargument ein und werden automatisch zur nächsten IP-Adresse zur Einbindung geroutet.

Es gibt zwei Hauptschritte zum Konfigurieren von RRDNS:

1. Ändern Sie die Datei ``named.conf`` Ihres DNS-Servers so, dass eine zyklische Reihenfolge für Abfragen an Ihren vFXT-Cluster festgelegt wird. Diese Option veranlasst den Server, alle verfügbaren IP-Adressen zu durchlaufen. Fügen Sie eine Anweisung wie die folgende hinzu:

   ```bash
   options {
       rrset-order {
           class IN A name "vfxt.contoso.com" order cyclic;
       };
   };
   ```

1. Konfigurieren Sie wie im folgenden Beispiel für jede verfügbare IP-Adresse A- und Zeigereinträge (PTR).

   Diese ``nsupdate``-Befehle sind ein Beispiel für die korrekte Konfiguration von DNS für einen vFXT-Cluster mit dem Domänennamen vfxt.contoso.com und drei Einbindungsadressen (10.0.0.10, 10.0.0.11 und 10.0.0.12):

   ```bash
   update add vfxt.contoso.com. 86400 A 10.0.0.10
   update add vfxt.contoso.com. 86400 A 10.0.0.11
   update add vfxt.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   Mit diesen Befehlen wird ein A-Eintrag für jede der Einbindungsadressen des Clusters erstellt. Außerdem werden Zeigereinträge eingerichtet, um Reverse-DNS-Überprüfungen entsprechend zu unterstützen.

   Das folgende Diagramm zeigt die grundlegende Struktur dieser Konfiguration.

   :::image type="complex" source="media/round-robin-dns-diagram-vfxt.png" alt-text="Diagramm der DNS-Konfiguration der Clienteinbindungspunkte.":::
   <Das Diagramm zeigt Verbindungen zwischen drei Kategorien von Elementen: einem einzelnen vFXT-Clusterdomänennamen (links), drei IP-Adressen (mittlere Spalte) und drei Reverse-DNS-Clientschnittstellen zur internen Verwendung (rechte Spalte). Ein einzelnes Oval auf der linken Seite mit der Bezeichnung „vfxt.contoso.com“ ist durch Pfeile verbunden, die auf drei Ovale zeigen, die mit den IP-Adressen 10.0.0.10, 10.0.0.11 und 10.0.0.12 bezeichnet sind. Die Pfeile vom Oval „vfxt.contoso.com“ zu den drei Ovalen mit IP-Adressen sind mit „A“ beschriftet. Jedes der Ovale mit IP-Adressen ist durch zwei Pfeile mit einem Oval verbunden, das als Clientschnittstelle beschriftet ist. Das Oval mit der IP-Adresse 10.0.0.10 ist mit client-IP-10.contoso.com verbunden. Das Oval mit der IP-Adresse 10.0.0.11 ist mit client-IP-11.contoso.com verbunden. Und das Oval mit der IP-Adresse 10.0.0.12 ist mit client-IP-11.contoso.com verbunden. Die Verbindungen zwischen den Ovalen mit IP-Adressen und den Clientschnittstellen sind zwei Pfeile: ein Pfeil mit der Beschriftung PTR, der vom Oval mit der IP-Adresse auf das Oval mit der Clientschnittstelle zeigt, und ein Pfeil mit der Beschriftung „A“, der vom Oval mit der Clientschnittstelle auf das Oval mit der IP-Adresse zeigt.> :::image-end:::

Nach der Konfiguration des RRDNS-Systems weisen Sie Ihre Clientsysteme an, die Adresse des Clusters in ihren Einbindungsbefehlen über dieses System aufzulösen.

## <a name="cluster-dns-settings"></a>Cluster-DNS-Einstellungen

Geben Sie auf der Einstellungsseite **Cluster** > **Verwaltungsnetzwerk** den DNS-Server an, den der vFXT-Cluster verwendet. Zu den Einstellungen auf dieser Seite zählen die folgenden:

* DNS-Serveradresse
* DNS-Domänenname
* DNS-Suchdomänen

Weitere Informationen zur Verwendung dieser Seite finden Sie in der Anleitung zur Konfiguration des Avere-Clusters unter [DNS-Einstellungen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns).
