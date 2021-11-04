---
title: 'Tutorial: Erstellen des Azure FXT Edge Filer-Cacheclusters'
description: Hier erfahren Sie, wie Sie mit Azure FXT Edge Filer einen Cachecluster für Hybridspeicher erstellen und sich zum Konfigurieren der Clustereinstellungen bei der Systemsteuerung des Clusters anmelden.
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: bc9ba05d6a441d7a223342f5c0dc964dc8eb1844
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086206"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Tutorial: Erstellen des Azure FXT Edge Filer-Clusters

Nach der Installation und Initialisierung der Azure FXT Edge Filer-Hardwareknoten für Ihren Cache erstellen Sie mithilfe der FXT-Clustersoftware den Cachecluster.

Dieses Tutorial führt Sie durch die Schritte zum Konfigurieren Ihrer Hardwareknoten als ein Cluster.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erforderliche Informationen vor dem Erstellen des Clusters
> * Unterschiede zwischen dem Verwaltungsnetzwerk des Clusters, dem Clusternetzwerk und dem Clientnetzwerk
> * Herstellen einer Verbindung mit einem Clusterknoten
> * Erstellen eines ersten Clusters mit einem Azure FXT Edge Filer-Knoten
> * Anmelden bei der Systemsteuerung für den Cluster zum Konfigurieren der Clustereinstellungen

Dieses Verfahren dauert zwischen 15 und 45 Minuten. Die Dauer hängt auch davon ab, wie lange Sie für das Ermitteln der IP-Adressen und Netzwerkressourcen benötigen.

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen vor Beginn dieses Tutorials erfüllt sein:

* Die Hardwaresysteme für Azure FXT Edge Filer müssen in Ihrem Rechenzentrum installiert sein.

  Sie benötigen nur einen Knoten, um den Cluster zu erstellen, Sie müssen aber [mindestens zwei weitere Knoten hinzufügen](add-nodes.md), bevor Sie den Cluster konfigurieren und für den Einsatz vorbereiten können.

* Anschließen der erforderlichen Stromversorgungs- und Netzwerkkabel an das System  
* Mindestens ein Azure FXT Edge Filer-Knoten muss eingeschaltet sein, und [das root-Kennwort muss festgelegt worden sein](node-password.md).

## <a name="gather-information-for-the-cluster"></a>Sammeln von Informationen für den Cluster

Sie benötigen die folgenden Informationen zum Erstellen des Azure FXT Edge Filer-Clusters:

* Clustername

* Administratorkennwort für den Cluster

* IP-Adressen:

  * Eine einzelne IP-Adresse für die Clusterverwaltung sowie Netzmaske und Router für das Verwaltungsnetzwerk
  * Die erste und letzte IP-Adresse eines zusammenhängenden Bereichs von IP-Adressen für die Clusterkommunikation (Knoten-zu-Knoten). Weitere Informationen finden Sie weiter unten unter [IP-Adressverteilung](#ip-address-distribution).
  * (Die IP-Adressen für die Clients werden nach der Erstellung des Clusters festgelegt.)

* Informationen zur Netzwerkinfrastruktur:

  * IP-Adresse eines DNS-Servers für den Cluster
  * Name der DNS-Domäne für den Cluster
  * Name oder IP-Adresse für die Cluster-NTP-Server (entweder ein Server oder drei bzw. mehr)
  * Entscheidung zur Aktivierung von Link Aggregation nach IEEE 802.1AX-2008 an den Clusterschnittstellen
  * Bei Aktivierung von Link Aggregation: Entscheidung zur Verwendung einer dynamischen Aggregation nach IEEE 802.3ad (LACP)

Sie können diese Elemente der Netzwerkinfrastruktur auch nach der Clustererstellung konfigurieren, es empfiehlt sich jedoch, dies während der Erstellung durchzuführen.

### <a name="ip-address-distribution"></a>IP-Adressverteilung

Der Azure FXT Edge Filer-Cachecluster für Hybridspeicher verwendet IP-Adressen in drei Kategorien:

* Verwaltungs-IP-Adresse: Eine einzelne IP-Adresse für die Clusterverwaltung

  Diese Adresse fungiert als Einstiegspunkt für den Zugriff auf die Hilfsprogramme zur Clusterkonfiguration (die webbasierten-Systemsteuerung oder die XML-RPC-API). Diese Adresse wird automatisch dem primären Knoten im Cluster zugewiesen und automatisch verschoben, wenn der primäre Knoten geändert wird.

  Es können auch andere IP-Adressen für den Zugriff auf die Systemsteuerung verwendet werden, die Verwaltungs-IP-Adresse ist jedoch speziell darauf ausgelegt, auch bei einem Failover einzelner Knoten den Zugriff zu ermöglichen.

* Clusternetzwerk: Ein IP-Adressbereich für die Clusterkommunikation

  Das Clusternetzwerk wird für die Kommunikation zwischen Clusterknoten sowie zum Abrufen von Dateien aus dem Back-End-Speicher (Core-Filer) verwendet.

  **Bewährte Methode:** Ordnen Sie eine IP-Adresse pro physischem Port zu, der für die Clusterkommunikation auf jedem Azure FXT Edge Filer-Knoten verwendet wird. Der Cluster weist den einzelnen Knoten automatisch Adressen aus dem angegebenen Bereich zu.

* Clientnetzwerk: Der IP-Adressbereich, den Clients zum Anfordern und Schreiben von Dateien verwenden

  Die Clientnetzwerkadressen werden von Clients für den Zugriff auf die Core-Filer-Daten über den Cluster verwendet. So kann beispielsweise ein NFS-Client eine dieser Adressen einbinden.

  **Bewährte Methode:** Ordnen Sie eine IP-Adresse pro physischem Port zu, der für die Clusterkommunikation auf jedem Knoten der FXT-Serie verwendet wird.

  Der Cluster verteilt die clientseitigen IP-Adressen so gleichmäßig wie möglich auf die enthaltenen Knoten.

  Der Einfachheit halber konfigurieren viele Administratoren einen einzigen DNS-Namen mit Roundrobin-DNS-Konfiguration (RRDNS), um die Verteilung der Clientanforderungen auf den Adressbereich zu vereinfachen. Bei diesem Setup können außerdem alle Clients denselben Einbindungsbefehl verwenden, um auf den Cluster zuzugreifen. Weitere Informationen finden Sie unter [Konfigurieren von DNS](configure-network.md#configure-dns-for-load-balancing).

Die Verwaltungs-IP-Adresse und ein Bereich von Adressen im Clusternetzwerk müssen angegeben werden, um einen neuen Cluster zu erstellen. Die IP-Adressen für die Clients werden nach der Erstellung des Clusters angegeben.

## <a name="connect-to-the-first-node"></a>Verbinden mit dem ersten Knoten

Sie können eine Verbindung mit jedem der installierten FXT-Knoten herstellen und die Software des Betriebssystems zum Einrichten des Clusters verwenden.

Wenn nicht bereits geschehen, schalten Sie mindestens einen der FXT-Knoten für Ihren Cluster ein, und stellen Sie sicher, dass er über eine Netzwerkverbindung und eine IP-Adresse verfügt. Sie müssen ein neues root-Kennwort festlegen, um den Knoten zu aktivieren. Befolgen Sie dazu die Schritte unter [Festlegen von Hardwarekennwörtern](node-password.md), wenn Sie dies nicht bereits erledigt haben.

Zum Prüfen der Netzwerkverbindung vergewissern Sie sich, ob die Netzwerkverbindungs-LEDs des Knotens leuchten (und ggf. auch die am Netzwerkswitch, mit dem er verbunden ist). Die Indikator-LEDs werden in [Überwachen des Hardwarestatus von Azure FXT Edge Filer](monitor.md) beschrieben.

Wenn der Knoten startet, fordert er eine IP-Adresse an. Sofern er mit einem DHCP-Server verbunden ist, akzeptiert er die per DHCP bereitgestellte IP-Adresse. (Diese IP-Adresse ist temporär. Sie ändert sich bei der Erstellung des Clusters.)

Wenn er nicht mit einem DHCP-Server verbunden ist oder keine Antwort erhält, verwendet der Knoten die Bonjour-Software, um eine selbst zugewiesene IP-Adresse in der Form 169.254.\*.\* festzulegen. Sie sollten allerdings eine temporäre statische IP-Adresse auf einer der Netzwerkkarten des Knotens festlegen, bevor Sie ihn zum Erstellen eines Clusters verwenden. Anweisungen finden Sie in diesem veralteten Dokument (aktuelle Informationen erhalten Sie vom Microsoft Service and Support): [Anhang A: Festlegen einer statischen IP-Adresse auf einem FXT-Knoten](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Ermitteln der IP-Adresse

Stellen Sie eine Verbindung mit dem Azure FXT Edge Filer-Knoten her, um seine IP-Adresse zu ermitteln. Sie können für die Verbindung ein serielles Kabel, eine direkte Verbindung über die USB- und VGA-Anschlüsse oder einen KVM-Switch verwenden. (Weitere Informationen zu Portverbindungen finden Sie unter [Festlegen anfänglicher Kennwörter](node-password.md).)

Melden Sie sich nach dem Herstellen einer Verbindung mit dem Benutzernamen `root` und dem Kennwort an, das Sie beim ersten Starten des Knotens festgelegt haben.

Sie müssen nach der Anmeldung die IP-Adresse des Knotens ermitteln.

Verwenden Sie den Befehl `ifconfig`, um die Adressen anzuzeigen, die dem System zugewiesen sind.

Der Befehl `ifconfig | grep -B5 inet` sucht z. B. nach Ports mit Internetadressen und gibt fünf Zeilen Kontextinformationen zur Port-ID zurück.

Notieren Sie alle IP-Adressen, die im Bericht von ifconfig angezeigt werden. Adressen mit Portnamen wie „e0a“ oder „e0b“ sind geeignete Optionen. Verwenden Sie keine IP-Adressen mit Namen, die „e7*“ enthalten, da diese Namen nur für iDRAC-/IPMI-Dienstports verwendet werden.

## <a name="load-the-cluster-configuration-wizard"></a>Laden des Clusterkonfigurations-Assistenten

Verwenden Sie das browserbasierte Konfigurationstool zum Erstellen des Clusters.

Geben Sie die IP-Adresse für den Knoten in einem Webbrowser ein. Falls der Browser einen Hinweis zu einer nicht vertrauenswürdigen Website anzeigt, navigieren Sie trotzdem zu der Website. (Einzelne Azure FXT Edge Filer-Knoten verfügen nicht über Sicherheitszertifikate, die von einer Zertifizierungsstelle ausgestellt wurden.)

![Anmeldeseite der Systemsteuerung im Browserfenster](media/fxt-cluster-create/unconfigured-node-gui.png)

Lassen Sie die Felder **Benutzername** und **Kennwort** leer. Klicken Sie auf **Anmelden**, um die Seite für die Clustererstellung zu laden.

![Bildschirm für die Ersteinrichtung eines nicht konfigurierten Knotens in der grafischen Benutzeroberfläche der browserbasierten Systemsteuerung. Es werden Optionen zum Aktualisieren der Software, zum manuellen Konfigurieren eines Clusters und zum Konfigurieren eines Clusters mit einer Setupdatei angezeigt.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Erstellen Sie den Cluster.

Das Clusterkonfigurationstool führt Sie durch eine Reihe von Bildschirmen für das Erstellen des Azure FXT Edge Filer-Clusters. Stellen Sie vor Beginn sicher, dass Sie die [erforderlichen Informationen](#gather-information-for-the-cluster) zur Hand haben.

### <a name="creation-options"></a>Optionen für die Erstellung

Der erste Bildschirm enthält drei Optionen. Verwenden Sie die Option für die manuelle Konfiguration, sofern Sie nicht von den Supportmitarbeitern spezielle Anweisungen erhalten haben.

Klicken Sie auf **I will configure the cluster manually** (Ich konfiguriere den Cluster manuell), um den Bildschirm mit den Optionen für das Konfigurieren eines neuen Clusters zu laden.

Die anderen Optionen werden nur selten verwendet:

* „I want to update the system image first“ (Ich möchte zunächst das Systemimage aktualisieren) fordert Sie zum Installieren neuer Betriebssystemsoftware auf, bevor Sie den Cluster erstellen können. (Die derzeit installierte Softwareversion wird am oberen Rand des Bildschirms angezeigt.) Sie müssen eine Paketdatei für die Software angeben – entweder als eine URL mit Benutzernamen und Kennwort oder durch Hochladen einer Datei von Ihrem Computer.

* Die Option mit einer Clustersetupdatei wird manchmal vom Microsoft-Kundendienst und Support verwendet.

## <a name="cluster-options"></a>Clusteroptionen

Auf dem nächsten Bildschirm werden Sie aufgefordert, Optionen für den neuen Cluster zu konfigurieren.

Die Seite ist in zwei Hauptabschnitte unterteilt: **Basic Configuration** (Basiskonfiguration) und **Networking Configuration** (Netzwerkkonfiguration). Der Abschnitt für die Netzwerkkonfiguration enthält weitere Unterabschnitte: einen für das **Verwaltungsnetzwerk** und einen für das **Clusternetzwerk**.

### <a name="basic-configuration"></a>Basiskonfiguration

Geben Sie im oberen Abschnitt grundlegende Informationen zum neuen Cluster ein.

![Details vom Abschnitt „Basic Configuration“ (Basiskonfiguration) auf der Browserseite mit der grafischen Benutzeroberfläche. Er enthält drei Felder (für Clustername, Administratorkennwort, Kennwortbestätigung).](media/fxt-cluster-create/basic-configuration.png)

* **Cluster name** (Clustername): Geben Sie einen eindeutigen Namen für den Cluster ein.

  Der Clustername muss diese Kriterien erfüllen:
  
  * Länge: 1 bis 16 Zeichen
  * Kann Buchstaben, Zahlen, Bindestriche (-) und Unterstriche (_) enthalten
  * Darf keine anderen Satzzeichen oder Sonderzeichen enthalten
  
  Sie können diesen Namen später auf der Konfigurationsseite **Cluster** > **General Setup** (Cluster > Allgemeine Konfiguration) ändern. (Weitere Informationen zu Clustereinstellungen finden Sie im [Cluster Configuration Guide](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) (Anleitung zur Clusterkonfiguration – nicht Teil dieser Dokumentation).)

  > [!NOTE]
  > Der Clustername wird zum Identifizieren von Systeminformationen verwendet, die zu Überwachungs- oder Problembehandlungszwecken zum Support hochgeladen werden. Es ist daher hilfreich, den Namen Ihres Unternehmens einzufügen.

* **Admin password** (Administratorkennwort): Legen Sie das Kennwort für den Standardadministrator (`admin`) fest.
  
  Sie sollten einzelne Benutzerkonten für jede Person einrichten, die den Cluster verwaltet – Sie können den Benutzer `admin` jedoch nicht entfernen. Melden Sie sich als `admin` an, wenn Sie zusätzliche Benutzer erstellen möchten.

  Sie können das Kennwort für `admin` in der Systemsteuerung des Clusters auf der Einstellungsseite **Administration** > **Users** (Verwaltung > Benutzer) ändern. Details finden Sie in der Dokumentation **Users** (Benutzer) im [Cluster Configuration Guide](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html) (Anleitung zur Clusterkonfiguration).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Netzwerkkonfiguration

Im Abschnitt **Networking** (Netzwerk) werden Sie aufgefordert, die Netzwerkinfrastruktur anzugeben, die der Cluster verwenden wird.

Es müssen zwei separate Netzwerke konfiguriert werden:

* Das *Verwaltungsnetzwerk* bietet Administratorzugriff auf den Cluster für die Konfiguration und Überwachung. Die hier angegebene IP-Adresse wird für Verbindungen mit der Systemsteuerung oder für den SSH-Zugriff verwendet.

  Bei den meisten Clustern wird nur eine einzelne Verwaltungs-IP-Adresse verwendet. Wenn Sie jedoch Schnittstellen hinzufügen möchten, können Sie dies nach dem Erstellen des Clusters durchführen.

* Das *Clusternetzwerk* wird für die Kommunikation zwischen Clusterknoten sowie zwischen Clusterknoten und dem Back-End-Speicher (Core-Filer) verwendet.

Das clientseitige Netzwerk wird später konfiguriert, nachdem der Cluster erstellt wurde.

Dieser Abschnitt enthält auch die Konfiguration für DNS- und NTP-Server, die von beiden Netzwerken verwendet werden.

### <a name="configure-the-management-network"></a>Konfigurieren des Verwaltungsnetzwerks

Die Einstellungen im Abschnitt **Management** (Verwaltung) gelten für das Netzwerk, das Administratorzugriff auf den Cluster ermöglicht.

![Details des Abschnitts „Management“ (Verwaltung) mit Feldern für 5 Optionen und einem Kontrollkästchen für ein 1-GBit-Verwaltungsnetzwerk](media/fxt-cluster-create/management-network-filled.png)

* **Management IP** (Verwaltungs-IP-Adresse): Geben Sie die IP-Adresse an, die Sie für den Zugriff auf die Systemsteuerung des Clusters verwenden möchten. Diese Adresse wird vom primären Knoten des Clusters in Anspruch genommen. Sie wird aber automatisch auf einen fehlerfreien Knoten verschoben, wenn der ursprüngliche primäre Knoten nicht verfügbar ist.

  Bei den meisten Clustern wird nur eine Verwaltungs-IP-Adresse verwendet. Wenn Sie mehrere verwenden möchten, können Sie diese nach der Erstellung des Clusters auf der Einstellungsseite **Cluster** > **Administrative Network** (Cluster > Verwaltungsnetzwerk) hinzufügen. Weitere Informationen finden Sie im [Cluster Configuration Guide](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html) (Anleitung zur Clusterkonfiguration).

* **Netmask** (Netzmaske): Geben Sie die Netzmaske für das Verwaltungsnetzwerk an.

* **Router:** Geben Sie die Adresse des Standardgateways für das Verwaltungsnetzwerk an.

* **VLAN tag (optional)** (VLAN-Tag (optional)): Wenn Ihr Cluster VLAN-Tags verwendet, geben Sie das Tag für das Verwaltungsnetzwerk an.

  Zusätzliche VLAN-Einstellungen werden auf der Einstellungsseite **Cluster** > **VLAN** konfiguriert. Weitere Informationen finden Sie in den Abschnitten zum [Arbeiten mit VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) und [Cluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) in der Anleitung zur Clusterkonfiguration.

* **MTU:** Passen Sie bei Bedarf die maximale Übertragungseinheit (MTU) für das Verwaltungsnetzwerk Ihres Clusters an.

* **Use 1Gb mgmt network** (1-GBit-Verwaltungsnetzwerk verwenden): Aktivieren Sie dieses Kontrollkästchen, wenn Sie die beiden 1-GbE-Netzwerkports auf Ihren FXT-Knoten ausschließlich dem Verwaltungsnetzwerk zuweisen möchten. (Sie benötigen für den übrigen Datenverkehr 25-GbE-/10-GbE-Ports.) Wenn Sie dieses Kontrollkästchen nicht aktivieren, verwendet das Verwaltungsnetzwerk den Port mit der höchsten verfügbaren Geschwindigkeit.

### <a name="configure-the-cluster-network"></a>Konfigurieren des Clusternetzwerks

Die Einstellungen zum Clusternetzwerk gelten für den Datenverkehr zwischen den Clusterknoten sowie zwischen Clusterknoten und Core-Filern.

![Details im Abschnitt „Cluster“ mit Feldern für die Eingabe von sechs Werten](media/fxt-cluster-create/cluster-network-filled.png)

* **First IP** (Erste IP-Adresse) und **Last IP** (Letzte IP-Adresse): Geben Sie die IP-Adressen ein, mit denen der Bereich für die interne Clusterkommunikation definiert wird. Die hier verwendeten IP-Adressen müssen zusammenhängend sein und dürfen nicht per DHCP zugewiesen werden.

  Sie können nach dem Erstellen des Clusters weitere IP-Adressen hinzufügen. Verwenden Sie dazu die Einstellungsseite **Cluster** > **Cluster Networks** (Cluster > Clusternetzwerke – siehe [Cluster Configuration Guide](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks) (Anleitung zur Clusterkonfiguration)).

  Der Wert in **Number of IPs in Range** (Anzahl von IP-Adressen im Bereich) wird berechnet und automatisch angezeigt.

* **Non-mgmt netmask (optional)** (Nicht-Verwaltungs-Netzmaske (optional)): Geben Sie die Netzmaske für das Clusternetzwerk an.

  Das System schlägt automatisch den Netzmaskenwert vor, den Sie für das Verwaltungsnetzwerk eingegeben haben. Sie können ihn bei Bedarf ändern.

* **Cluster router (optional)** (Clusterrouter (optional)): Geben Sie die Adresse des Standardgateways für das Clusternetzwerk ein.

  Das System schlägt automatisch die gleiche Gatewayadresse vor, die Sie für das Verwaltungsnetzwerk angegeben haben.

* **Cluster VLAN tag (optional)** (Cluster-VLAN-Tag (optional)): Wenn Ihr Cluster VLAN-Tags verwendet, geben Sie das Tag für das Clusternetzwerk an.

* **Non-mgmt MTU (optional)** (Nicht-Verwaltungs-MTU (optional)): Passen Sie bei Bedarf die maximale Übertragungseinheit (MTU) für das Clusternetzwerk an.

### <a name="configure-cluster-dns-and-ntp"></a>Konfigurieren von DNS und NTP für den Cluster

Unterhalb des Abschnitts **Cluster** befinden sich Felder zum Angeben von DNS- und NTP-Servern und zum Aktivieren von Link Aggregation. Diese Einstellungen gelten für alle Netzwerke, die der Cluster verwendet.

![Details im Abschnitt zur DNS-/NTP-Konfiguration mit drei Feldern für DNS-Server, Feldern für DNS-Domäne und DNS-Suche, drei Feldern für NTP-Server und einem Dropdownmenü mit Optionen für Link Aggregation](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS server(s)** (DNS-Server): Geben Sie die IP-Adresse eines oder mehrerer DNS-Server (Domain Name System) ein.

  DNS wird für alle Cluster empfohlen und ist erforderlich, wenn Sie SMB, AD oder Kerberos verwenden möchten.
  
  Konfigurieren Sie für eine optimale Leistung den DNS-Server des Clusters für den Roundrobin-Lastenausgleich, der in [Konfigurieren von DNS für den Azure FXT Edge Filer-Cluster](configure-network.md#configure-dns-for-load-balancing) beschrieben wird.

* **DNS domain** (DNS-Domäne): Geben Sie den Namen der Netzwerkdomäne ein, die vom Cluster verwendet wird.

* **DNS search** (DNS-Suche): Geben Sie optional zusätzliche Domänennamen ein, die das System zur Auflösung von DNS-Abfragen durchsuchen soll. Sie können bis zu sechs Domänennamen getrennt durch Leerzeichen hinzufügen.

* **NTP server(s)** (NTP-Server): Geben Sie in die bereitgestellten Felder einen oder drei NTP-Server (Network Time Protocol) ein. Sie können Hostnamen oder IP-Adressen verwenden.

* **Link aggregation:** Mit Link Aggregation können Sie anpassen, wie die Ethernetports auf den FXT-Knoten des Clusters verschiedene Arten von Datenverkehr verarbeiten. Weitere Informationen finden Sie unter [Link Aggregation](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) in der Anleitung zur Clusterkonfiguration.

### <a name="click-the-create-button"></a>Klicken auf die Schaltfläche „Create cluster“ (Cluster erstellen)

Klicken Sie nach dem Angeben aller erforderlichen Einstellungen im Formular auf die Schaltfläche **Create Cluster** (Cluster erstellen).

![Unterer Teil des ausgefüllten Formulars mit Cursor über der Schaltfläche „Create cluster“ (Cluster erstellen) rechts unten](media/fxt-cluster-create/create-cluster.png)

Das System zeigt während des Erstellens des Clusters eine Meldung an.

![Statusmeldung zur Clustererstellung im Browser: „The FXT node is now creating the cluster. Dies dauert einige Minuten. When the cluster is created, visit this link to complete the configuration.“ (Der FXT-Knoten erstellt jetzt den Cluster. Dies dauert einige Minuten. Besuchen Sie nach der Clustererstellung diesen Link, um die Konfiguration abzuschließen.) mit Link für „visit this link“ (diesen Link besuchen)](media/fxt-cluster-create/creating-message.png)

Sie können nach einigen Augenblicken auf den Link in der Meldung klicken, um zur Systemsteuerung für den Cluster zu navigieren. (Dieser Link führt zu der IP-Adresse, die Sie unter **Management IP** (Verwaltungs-IP-Adresse) angegeben haben.) Es dauert zwischen 15 Sekunden und einer Minute, bis der Link aktiviert wird, nachdem Sie auf die Schaltfläche „Create cluster“ (Cluster erstellen) geklickt haben. Wenn die Webbenutzeroberfläche nicht geladen wird, warten Sie noch einige Sekunden länger, und klicken Sie dann erneut auf den Link.

Die Erstellung des Clusters dauert eine Minute oder länger, Sie können sich aber bereits während des Prozesses bei der Systemsteuerung anmelden. Es ist normal, dass auf der Dashboardseite der Systemsteuerung Warnungen angezeigt werden, bis der Vorgang der Clustererstellung abgeschlossen ist.

## <a name="open-the-settings-pages"></a>Öffnen der Einstellungsseiten

Nach der Erstellung des Clusters müssen Sie die Konfiguration an Ihr Netzwerk und Ihren Workflow anpassen.

Verwenden Sie die Webbenutzeroberfläche der Systemsteuerung, um den neuen Cluster einzurichten. Folgen Sie dem Link auf dem Statusbildschirm während der Clustererstellung, oder navigieren Sie zur Verwaltungs-IP-Adresse, die Sie für den Cluster festgelegt haben.

Melden Sie sich auf der Webbenutzeroberfläche mit dem Benutzernamen `admin` und dem Kennwort an, das Sie bei der Clustererstellung festgelegt haben.

![Webbrowser mit Anmeldefeldern für die Systemsteuerung](media/fxt-cluster-create/admin-login.png)

Die Systemsteuerung wird mit der Seite **Dashboard** geöffnet. Nach Abschluss der Clustererstellung sollten keine Warnmeldungen mehr angezeigt werden.

Klicken Sie auf die Registerkarte **Settings** (Einstellungen), um den Cluster zu konfigurieren.

Auf der Registerkarte **Settings** (Einstellungen) wird auf der linken Seitenleiste ein Menü der Konfigurationsseiten angezeigt. Die Seiten sind nach Kategorie geordnet. Klicken Sie auf das Steuerelement + oder - über dem Namen der Kategorie, um einzelne Seiten zu erweitern oder auszublenden.

![Registerkarte „Settings“ (Einstellungen) der Systemsteuerung (im Browser) mit der Seite „Cluster > General Setup“ (Cluster > Allgemeine Konfiguration)](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Schritte zur Clustereinrichtung

An diesem Punkt des Prozesses ist Ihr Cluster zwar vorhanden, aber er verfügt nur über einen Knoten und noch über keine clientseitigen IP-Adressen und keinen Back-End-Speicher. Es sind weitere Installationsschritte erforderlich, um aus einem neu erstellten Cluster ein Cachesystem zu machen, das Ihren Workflow verarbeiten kann.

### <a name="required-configuration"></a>Erforderliche Konfiguration

Diese Schritte sind für die meisten (oder alle) Cluster erforderlich.

* Hinzufügen von Knoten zum Cluster

  Drei Knoten sind Standard, aber viele Produktionscluster enthalten noch mehr – bis maximal 24 Knoten.

  Lesen Sie das Tutorial zum [Hinzufügen von Clusterknoten](add-nodes.md), um zu erfahren, wie Ihrem Cluster andere Azure FXT Edge Filer-Einheiten hinzugefügt werden und wie Hochverfügbarkeit aktiviert wird.

* Angeben des Back-End-Speichers

  Fügen Sie Definitionen für *Core-Filer* für jedes Back-End-Speichersystem hinzu, das der Cluster verwenden wird. Weitere Informationen finden Sie unter [Hinzufügen von Back-End-Speicher und Konfigurieren des virtuellen Namespace](add-storage.md#about-back-end-storage).

* Einrichten von Clientzugriff und virtuellem Namespace

  Erstellen Sie mindestens einen virtuellen Server (VServer), und weisen Sie ihm einen IP-Adressbereich zu, der für die Clientcomputer verwendet werden kann. Außerdem müssen Sie den Clusternamespace konfigurieren (auch als globaler Namespace oder GNS bezeichnet), eine Funktion für ein virtuelles Dateisystem, mit der Sie Exporte vom Back-End-Speicher virtuellen Pfaden zuordnen können. Der Clusternamespace stellt Clients eine konsistente und zugängliche Dateisystemstruktur zur Verfügung – selbst wenn Sie die Back-End-Speichermedien wechseln. Darüber hinaus kann der Namespace auch eine benutzerfreundliche Hierarchie für den virtuellen Speicher für Azure-Blobcontainer oder einen anderen unterstützten Cloudobjektspeicher bereitstellen.

  Einzelheiten finden Sie unter [Konfigurieren des Namespace](add-storage.md#configure-the-namespace). Dieser Schritt umfasst Folgendes:
  * Erstellen von VServern
  * Einrichten von Verbindungen zwischen der Clientnetzwerkansicht und dem Back-End-Speicher
  * Definieren der Client-IP-Adressen, die von den einzelnen VServern verarbeitet werden

  > [!Note]
  > Vor dem Einrichten des Clusters-GNS wird eine umfassende Planung empfohlen. Lesen Sie die Abschnitte zum [Verwenden eines globalen Namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) und [Erstellen und Verwenden von VServern](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) in der Anleitung zur Clusterkonfiguration, um weitere Informationen zu erhalten.

* [Anpassen der Netzwerkeinstellungen](configure-network.md)

  Es gibt mehrere netzwerkbezogene Einstellungen, die bei einem neuen Cluster überprüft oder angepasst werden sollten. Unter [Anpassen von Netzwerkeinstellungen](configure-network.md) finden Sie Details zu diesen Elementen:

  * Überprüfen der DNS- und NTP-Konfiguration
  * Konfigurieren der Verzeichnisdienste (bei Bedarf)
  * Einrichten von VLANs
  * Konfigurieren von Proxyservern
  * Hinzufügen von IP-Adressen zum Clusternetzwerk
  * Speichern von Verschlüsselungszertifikaten

* [Einrichten der Überwachung durch den Support](#enable-support)

  Sie müssen die Datenschutzrichtlinie für das Konfigurationstool akzeptieren, und Sie sollten gleichzeitig die Einstellungen für das Hochladen zum Support konfigurieren.

  Der Cluster kann automatisch Daten über Ihren Cluster, einschließlich Statistiken und Debugdateien, für das Troubleshooting hochladen. Diese Uploads ermöglichen es dem Microsoft-Kundendienst und Support, den bestmöglichen Kundendienst zu bieten. Sie können anpassen, was überwacht wird, und Sie können optional den proaktiven Support und den Remotedienst für das Troubleshooting aktivieren.  

### <a name="optional-configuration"></a>Optionale Konfiguration

Diese Schritte sind nicht für alle Cluster erforderlich. Sie sind für bestimmte Typen von Workflows oder für bestimmte Arten der Clusterverwaltung erforderlich.

* Anpassen von Knoteneinstellungen

  Sie können Knotennamen festlegen und IPMI-Ports für Knoten konfigurieren – entweder für den gesamten Cluster oder einzeln. Wenn Sie diese Einstellungen vor dem Hinzufügen von Knoten zum Cluster konfigurieren, übernehmen neue Knoten diese Einstellungen automatisch beim Beitritt. Die Optionen werden im veralteten Dokument zur Clustererstellung [Anpassen von Knoteneinstellungen](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html) beschrieben.

  > [!TIP]
  > Ein Teil der Dokumentation für dieses Produkt ist noch nicht auf der Dokumentationswebsite von Microsoft Azure verfügbar. Über die Links zum [Cluster Configuration Guide](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) (Anleitung zur Clusterkonfiguration) und zur veralteten Version des [Cluster Creation Guide](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) (Anleitung zur Clustererstellung) gelangen Sie zu einer separat gehosteten GitHub-Website.

* Konfigurieren von SMB

  Wenn Sie SMB-Zugriff auf Ihren Cluster sowie NFS zulassen möchten, müssen Sie SMB konfigurieren und aktivieren. SMB (manchmal als CIFS bezeichnet) wird normalerweise verwendet, um Microsoft Windows-Clients zu unterstützen.

  Das Planen und Konfigurieren von SMB erfordert mehr als das Klicken auf einige Schaltflächen in der Systemsteuerung. Je nach den Anforderungen Ihres Systems kann SMB beeinflussen, wie Sie die Core-Filer definieren, wie viele VServer Sie erstellen und wie Sie Ihre Verbindungen, den Namespace, Zugriffsberechtigungen und andere Einstellungen konfigurieren.

  Weitere Informationen finden Sie in der Anleitung zur Clusterkonfiguration im Abschnitt zum [Konfigurieren des SMB-Zugriffs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html).

* Installieren zusätzlicher Lizenzen

  Wenn Sie andere Cloudspeicher als Azure Blob Storage verwenden möchten, müssen Sie eine zusätzliche Featurelizenz installieren. Kontaktieren Sie Ihren Microsoft-Mitarbeiter, um weitere Informationen zum Erwerb einer FlashCloud<sup>TM</sup>-Lizenz zu erhalten. Weitere Informationen finden Sie unter [Hinzufügen von Back-End-Speicher und Konfigurieren des virtuellen Namespace](add-storage.md#about-back-end-storage).

### <a name="enable-support"></a>Aktivieren des Supports

Der Azure FXT Edge Filer-Cluster kann automatisch Supportdaten über Ihren Cluster hochladen. Diese Uploads ermöglichen es den Mitarbeitern, den bestmöglichen Kundendienst zu bieten.

Gehen Sie folgendermaßen vor, um Supportuploads einzurichten.

1. Navigieren Sie zur Einstellungsseite **Cluster** > **Support**. Akzeptieren Sie die Datenschutzrichtlinie.

   ![Screenshot der Systemsteuerung und des Popupfensters mit der Bestätigungsschaltfläche zum Akzeptieren der Datenschutzrichtlinie](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Klicken Sie auf das Dreieck links neben **Kundeninformationen**, um den Abschnitt zu erweitern.
1. Klicken Sie auf die Schaltfläche **Revalidate upload information** (Uploadinformationen erneut überprüfen).
1. Legen Sie den Supportnamen des Clusters unter **Eindeutiger Clustername** fest – stellen Sie sicher, dass er Ihren Cluster eindeutig identifiziert, um die Supportmitarbeiter zu unterstützen.
1. Aktivieren Sie die Kontrollkästchen für **Statistiküberwachung**, **Upload allgemeiner Informationen** und **Upload von Absturzinformationen**.
1. Klicken Sie auf **Submit**(Senden).  

   ![Screenshot der Supporteinstellungsseite mit ausgefüllten Kundeninformationen](media/fxt-cluster-create/fxt-support-info.png)

1. Klicken Sie auf das Dreieck links neben **Secure Proactive Support (SPS)** , um den Abschnitt zu erweitern.
1. Aktivieren Sie das Kontrollkästchen für **SPS-Link aktivieren**.
1. Klicken Sie auf **Submit**(Senden).

   ![Screenshot der Supporteinstellungsseite mit ausgefülltem SPS-Abschnitt](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie den grundlegenden Cluster erstellt und die Datenschutzrichtlinie akzeptiert haben, fügen Sie die übrigen Clusterknoten hinzu.

> [!div class="nextstepaction"]
> [Hinzufügen von Clusterknoten](add-nodes.md)
