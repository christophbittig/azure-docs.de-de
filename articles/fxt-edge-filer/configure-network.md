---
title: 'Tutorial: Konfigurieren des Netzwerks in einem Azure FXT Edge Filer-Cluster'
description: Hier erfahren Sie, wie Sie nach Erstellung des Azure FXT Edge Filer-Clusters die Netzwerkeinstellungen anpassen.
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 10/07/2021
ms.openlocfilehash: a51f6654c8e82a46c1a31f2dd030cd2d5af754cd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347302"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Tutorial: Konfigurieren der Netzwerkeinstellungen des Clusters

Vor der Verwendung eines neu erstellten Azure FXT Edge Filer-Clusters müssen mehrere Netzwerkeinstellungen überprüft und für Ihren Workflow angepasst werden.

In diesem Tutorial erfahren Sie, welche Netzwerkeinstellungen für einen neuen Cluster ggf. angepasst werden müssen.

Sie erhalten Informationen zu folgenden Themen:

> [!div class="checklist"]
>
> * Welche Netzwerkeinstellungen nach der Clustererstellung ggf. aktualisiert werden müssen
> * Für welche Azure FXT Edge Filer-Anwendungsfälle ein AD- oder DNS-Server benötigt wird
> * Wie Sie Roundrobin-DNS (RRDNS) konfigurieren, um einen automatischen Lastenausgleich für an den FXT-Cluster gerichtete Clientanforderungen vorzunehmen

Der Zeitaufwand für diese Schritte hängt vom Umfang der erforderlichen Konfigurationsänderungen für Ihr System ab:

* Wenn Sie sich lediglich das Tutorial durchlesen und einige wenige Einstellungen überprüfen müssen, sind Sie voraussichtlich in zehn bis 15 Minuten fertig.
* Wenn Sie Roundrobin-DNS konfigurieren müssen, sollten Sie mindestens eine Stunde einplanen.

## <a name="adjust-network-settings"></a>Anpassen der Netzwerkeinstellungen

Die Einrichtung eines neuen Azure FXT Edge Filer-Clusters beinhaltet mehrere netzwerkbezogene Aufgaben. Ermitteln Sie anhand der folgenden Liste, welche Aufgaben für Ihr System erforderlich sind.

Weitere Informationen zu Netzwerkeinstellungen für den Cluster finden Sie in der Anleitung zur Clusterkonfiguration unter [Configuring Network Services for an Avere Cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) (Konfigurieren von Netzwerkdiensten für einen Avere-Cluster).

* Konfigurieren von Roundrobin-DNS für das clientseitige Netzwerk (optional)

  Konfigurieren Sie das DNS-System wie unter [Konfigurieren von DNS für den Lastenausgleich](#configure-dns-for-load-balancing) beschrieben, um einen Lastenausgleich für den Clusterdatenverkehr vorzunehmen.

* Überprüfen der NTP-Einstellungen

* Konfigurieren von Active Directory sowie von Benutzernamen-/Gruppennamendownloads (sofern erforderlich)

  Wenn Ihre Netzwerkhosts Active Directory oder einen anderen externen Verzeichnisdienst verwenden, müssen Sie die Verzeichnisdienstekonfiguration des Clusters ändern, um festzulegen, wie Benutzernamen und Gruppeninformationen durch den Cluster heruntergeladen werden sollen. Ausführliche Informationen finden Sie in der Anleitung zur Clusterkonfiguration unter **Cluster** > **Directory Services** (Verzeichnisdienste).

  Für SMB-Unterstützung wird ein AD-Server benötigt. Konfigurieren Sie AD, bevor Sie mit der SMB-Einrichtung beginnen.

* Definieren von VLANs (optional)
  
  Konfigurieren Sie alle ggf. benötigten VLANs, bevor Sie die VServer-Instanzen und den globalen Namespace Ihres Clusters definieren. Weitere Informationen finden Sie in der Anleitung zur Clusterkonfiguration im Abschnitt [Working with VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) (Verwenden von VLANs).

* Konfigurieren von Proxyservern (sofern erforderlich)

  Falls Ihr Cluster einen Proxyserver verwendet, um externe Adressen zu erreichen, gehen Sie wie folgt vor, um ihn einzurichten:

  1. Definieren Sie den Proxyserver auf der Seite **Proxykonfiguration**.
  1. Wenden Sie die Proxyserverkonfiguration auf der Seite **Cluster** > **General Setup** (Cluster > Allgemeine Einrichtung) oder auf der Seite **Core Filer Details** (Details der Kernspeichereinheit) an.
  
  Weitere Informationen finden Sie in der Anleitung zur Clusterkonfiguration unter [Using web proxies](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) (Verwenden von Webproxys).

* Hochladen von [Verschlüsselungszertifikaten](#encryption-certificates) für den Cluster (optional)

### <a name="encryption-certificates"></a>Verschlüsselungszertifikate

Der FXT Edge Filer-Cluster verwendet X.509-Zertifikate für folgende Funktionen:

* Verschlüsselung des Datenverkehrs für die Clusterverwaltung

* Authentifizierung bei KMPI-Drittanbieterservern im Namen eines Clients

* Überprüfung der Serverzertifikate von Cloudanbietern

Zertifikate können bei Bedarf über die Einstellungsseite **Cluster** > **Certificates** (Cluster > Zertifikate) in den Cluster hochgeladen werden. Ausführliche Informationen finden Sie in der Anleitung zur Clusterkonfiguration auf der Seite [Cluster > Certificates](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) (Cluster > Zertifikate).

Wenn Sie die Verwaltungskommunikation des Clusters verschlüsseln möchten, wählen Sie auf der Einstellungsseite **Cluster** > **General Setup** (Cluster > Allgemeine Einrichtung) aus, welches Zertifikat für die Verwaltung über TLS verwendet werden soll.

Stellen Sie sicher, dass Ihre administrativen Computer die [Verschlüsselungsstandards](supported-ciphers.md) des Clusters erfüllen.

> [!Note]
> Clouddienst-Zugriffsschlüssel werden mithilfe der Konfigurationsseite **Cloud Credentials** (Cloudanmeldeinformationen) gespeichert. Ein Beispiel finden Sie weiter oben im Abschnitt [Hinzufügen einer Kernspeichereinheit](add-storage.md#add-a-core-filer). Ausführliche Informationen finden Sie in der Anleitung zur Clusterkonfiguration im Abschnitt [Cloud Credentials](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) (Cloudanmeldeinformationen).

## <a name="configure-dns-for-load-balancing"></a>Konfigurieren von DNS für den Lastenausgleich

In diesem Abschnitt werden die Grundlagen zum Konfigurieren eines RRDNS-Systems (Roundrobin-DNS) erläutert, das die Clientlast auf alle clientseitigen IP-Adressen in Ihrem FXT Edge Filer-Cluster verteilt.

### <a name="decide-whether-or-not-to-use-dns"></a>Entscheiden, ob DNS verwendet werden soll

Die Verwendung eines Lastenausgleichs empfiehlt sich in jedem Fall. DNS muss aber nicht immer verwendet werden. So ist es beispielsweise bei bestimmten Arten von Clientworkflows unter Umständen sinnvoller, Cluster-IP-Adressen per Skript gleichmäßig auf Clients zu verteilen, wenn diese den Cluster einbinden. Unter [Tutorial: Einbinden des Clusters](mount-clients.md) werden einige Methoden beschrieben.

Beachten Sie diese Faktoren bei der Entscheidung, ob Sie einen DNS-Server verwenden:

* Wenn auf Ihr System ausschließlich über NFS-Clients zugegriffen wird, ist DNS nicht erforderlich. Sie können alle Netzwerkadressen unter Verwendung numerischer IP-Adressen angeben.

* Wenn Ihr System den SMB-Zugriff (CIFS) unterstützt, ist DNS erforderlich, da Sie eine DNS-Domäne für den Active Directory-Server angeben müssen.

* Wenn Sie die Kerberos-Authentifizierung verwenden möchten, ist DNS erforderlich.

### <a name="round-robin-dns-configuration-details"></a>Konfigurationsdetails für Roundrobin-DNS

Ein RRDNS-System (Roundrobin DNS) leitet Clientanforderungen automatisch an mehrere Adressen weiter.

Um dieses System einzurichten, müssen Sie die Konfigurationsdatei des DNS-Servers so anpassen, dass er bei Einbindungsanforderungen an die Hauptdomänenadresse der FXT Edge Filer-Instanz den Datenverkehr allen Bereitstellungspunkten des Clusters zuweist. Clients binden den Cluster unter Verwendung seines Domänennamens als Serverargument ein und werden automatisch zur nächsten IP-Adresse zur Einbindung geroutet.

Es gibt zwei Hauptschritte zum Konfigurieren von RRDNS:

1. Ändern Sie die Datei ``named.conf`` Ihres DNS-Servers so, dass eine zyklische Reihenfolge für Abfragen an Ihren FXT-Cluster festgelegt wird. Diese Option veranlasst den Server, alle verfügbaren IP-Adressen zu durchlaufen. Fügen Sie eine Anweisung wie die folgende hinzu:

   ```bash
   options {
       rrset-order {
           class IN A name "fxt.contoso.com" order cyclic;
       };
   };
   ```

1. Konfigurieren Sie wie im folgenden Beispiel für jede verfügbare IP-Adresse A- und Zeigereinträge (PTR).

   Diese ``nsupdate``-Befehle sind ein Beispiel für die korrekte Konfiguration von DNS für einen Azure FXT Edge Filer-Cluster mit dem Domänennamen fxt.contoso.com und drei Einbindungsadressen (10.0.0.10, 10.0.0.11 und 10.0.0.12):

   ```bash
   update add fxt.contoso.com. 86400 A 10.0.0.10
   update add fxt.contoso.com. 86400 A 10.0.0.11
   update add fxt.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   Mit diesen Befehlen wird ein A-Eintrag für jede der Einbindungsadressen des Clusters erstellt. Außerdem werden Zeigereinträge eingerichtet, um Reverse-DNS-Überprüfungen entsprechend zu unterstützen.

   Das folgende Diagramm zeigt die grundlegende Struktur dieser Konfiguration.

   :::image type="complex" source="media/round-robin-dns-diagram-fxt.png" alt-text="Diagramm der DNS-Konfiguration der Clienteinbindungspunkte.":::
   <Das Diagramm zeigt Verbindungen zwischen drei Kategorien von Elementen: einem einzelnen FXT Edge Filer-Clusterdomänennamen (links), drei IP-Adressen (mittlere Spalte) und drei Reverse-DNS-Clientschnittstellen zur internen Verwendung (rechte Spalte). Ein einzelnes Oval auf der linken Seite mit der Bezeichnung „fxt.contoso.com“ ist durch Pfeile verbunden, die auf drei Ovale zeigen, die mit den IP-Adressen 10.0.0.10, 10.0.0.11 und 10.0.0.12 bezeichnet sind. Die Pfeile vom Oval fxt.contoso.com zu den drei Ovalen mit IP-Adressen sind mit „A“ beschriftet. Jedes der Ovale mit IP-Adressen ist durch zwei Pfeile mit einem Oval verbunden, das als Clientschnittstelle beschriftet ist. Das Oval mit der IP-Adresse 10.0.0.10 ist mit client-IP-10.contoso.com verbunden. Das Oval mit der IP-Adresse 10.0.0.11 ist mit client-IP-11.contoso.com verbunden. Und das Oval mit der IP-Adresse 10.0.0.12 ist mit client-IP-11.contoso.com verbunden. Die Verbindungen zwischen den Ovalen mit IP-Adressen und den Clientschnittstellen sind zwei Pfeile: ein Pfeil mit der Beschriftung PTR, der vom Oval mit der IP-Adresse auf das Oval mit der Clientschnittstelle zeigt, und ein Pfeil mit der Beschriftung „A“, der vom Oval mit der Clientschnittstelle auf das Oval mit der IP-Adresse zeigt.> :::image-end:::

Nach der Konfiguration des RRDNS-Systems weisen Sie Ihre Clientsysteme an, die Adresse des FXT-Clusters in ihren Einbindungsbefehlen über dieses System aufzulösen.

### <a name="enable-dns-in-the-cluster"></a>Aktivieren von DNS im Cluster

Geben Sie auf der Einstellungsseite **Cluster** (Cluster) > **Administrative Network** (Verwaltungsnetzwerk) den DNS-Server an, den der Cluster verwendet. Zu den Einstellungen auf dieser Seite zählen die folgenden:

* DNS-Serveradresse
* DNS-Domänenname
* DNS-Suchdomänen

Ausführlichere Informationen finden Sie in der Anleitung zur Clusterkonfiguration unter [DNS Settings](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns) (DNS-Einstellungen).

## <a name="next-steps"></a>Nächste Schritte

Dies ist der letzte Standardkonfigurationsschritt für den Azure FXT Edge Filer-Cluster.

* Informationen zu den LEDs und anderen Indikatoren des Systems finden Sie unter [Monitor Azure FXT Edge Filer hardware status](monitor.md) (Überwachen des Hardwarestatus von Azure FXT Edge Filer).
* Weitere Informationen zur Einbindung des FXT Edge Filer-Clusters durch Clients finden Sie unter [Tutorial: Einbinden des Clusters](mount-clients.md).
* Weitere Informationen zum Betreiben und Verwalten eines FXT Edge Filer-Cluster finden Sie in der [Anleitung zur Clusterkonfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html).
