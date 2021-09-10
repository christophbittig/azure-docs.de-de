---
title: 'Tutorial: Konfigurieren des Netzwerks in einem Azure FXT Edge Filer-Cluster'
description: Hier erfahren Sie, wie Sie nach Erstellung des Azure FXT Edge Filer-Clusters die Netzwerkeinstellungen anpassen.
author: ekpgh
ms.author: v-erkel
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 8d349a0faa2cfc97f029e496b9bd92b1e5057018
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867501"
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

Wenn Clients auf den Cluster zugreifen, gleicht RRDNS ihre Anforderungen automatisch zwischen allen verfügbaren Schnittstellen aus.

Konfigurieren Sie Ihren DNS-Server so, dass er clientseitige Clusteradressen wie in der folgenden Abbildung dargestellt verarbeitet, um optimale Leistungen zu erhalten.

Auf der linken Seite wird ein virtueller Clusterserver und in der Mitte und auf der rechten Seite werden IP-Adressen angezeigt. Konfigurieren Sie die einzelnen Clientzugriffspunkte mit A-Einträgen und Zeigern, wie in der Abbildung gezeigt.

:::image type="complex" source="media/fxt-cluster-config/fxt-rrdns-diagram.png" alt-text="Diagramm der Cluster-Roundrobin-DNS-Konfiguration.":::
   <Das Diagramm zeigt Verbindungen zwischen drei Kategorien von Elementen: einem einzelnen vServer (links), drei IP-Adressen (mittlere Spalte) und drei Clientschnittstellen (rechte Spalte). Ein einzelner Kreis auf der linken Seite mit der Bezeichnung „vserver1“ ist durch Pfeile verbunden, die auf drei Kreise zeigen, die mit den IP-Adressen 10.0.0.10, 10.0.0.11 und 10.0.0.12 bezeichnet sind. Die Pfeile vom vserver-Kreis zu den drei IP-Adressenkreisen haben die Beschriftung „A“. Jeder der IP-Adressenkreise ist durch zwei Pfeile mit einem Kreis verbunden, der als Clientschnittstelle bezeichnet ist. Der Kreis mit der IP-Adresse 10.0.0.10 ist mit „vs1-client-IP-10“ verbunden, der Kreis mit der IP-Adresse 10.0.0.11 ist mit „vs1-client-IP-11“ verbunden, und der Kreis mit der IP-Adresse 10.0.0.12 ist mit „vs1-client-IP-12“ verbunden. Die Verbindungen zwischen den IP-Adressenkreisen und den Clientschnittstellenkreisen sind zwei Pfeile: ein Pfeil mit der Bezeichnung „PTR“, der vom IP-Adressenkreis auf den Kreis der Clientschnittstelle zeigt, und ein Pfeil mit der Bezeichnung „A“, der vom Kreis der Clientschnittstelle auf den IP-Adressenkreis zeigt.> :::image-end:::

Jede clientseitige IP-Adresse muss einen eindeutigen Namen für die interne Verwendung durch den Cluster aufweisen. (In diesem Diagramm werden die Client-IPs aus Gründen der Übersichtlichkeit mit „vs1-client-IP-*“ bezeichnet, aber in der Produktionsumgebung sollten Sie eine prägnantere Bezeichnung verwenden, z. B. „client*“.)

Clients binden den Cluster über den VServer-Namen als Serverargument ein.

Ändern Sie die Datei ``named.conf`` Ihres DNS-Servers, um die Reihenfolge für Abfragen an Ihren VServer festzulegen. Diese Option stellt sicher, dass alle verfügbaren Werte durchlaufen werden. Fügen Sie eine Anweisung wie die folgende hinzu:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Die folgenden ``nsupdate``-Befehle sind ein Beispiel für die ordnungsgemäße Konfiguration von DNS:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

### <a name="enable-dns-in-the-cluster"></a>Aktivieren von DNS im Cluster

Geben Sie auf der Einstellungsseite **Cluster** (Cluster) > **Administrative Network** (Verwaltungsnetzwerk) den DNS-Server an, den der Cluster verwendet. Zu den Einstellungen auf dieser Seite zählen die folgenden:

* DNS-Serveradresse
* DNS-Domänenname
* DNS-Suchdomänen

Ausführlichere Informationen finden Sie in der Anleitung zur Clusterkonfiguration unter [DNS Settings](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) (DNS-Einstellungen).

## <a name="next-steps"></a>Nächste Schritte

Dies ist der letzte Standardkonfigurationsschritt für den Azure FXT Edge Filer-Cluster.

* Informationen zu den LEDs und anderen Indikatoren des Systems finden Sie unter [Monitor Azure FXT Edge Filer hardware status](monitor.md) (Überwachen des Hardwarestatus von Azure FXT Edge Filer).
* Weitere Informationen zur Einbindung des FXT Edge Filer-Clusters durch Clients finden Sie unter [Tutorial: Einbinden des Clusters](mount-clients.md).
* Weitere Informationen zum Betreiben und Verwalten eines FXT Edge Filer-Cluster finden Sie in der [Anleitung zur Clusterkonfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html).
