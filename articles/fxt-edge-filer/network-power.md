---
title: 'Tutorial: Verkabeln von Azure FXT Edge Filer'
description: Hier finden Sie Informationen zum Verkabeln der Netzwerkanschlüsse sowie zum Herstellen der Stromversorgung für Azure FXT Edge Filer-Hardware.
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: e38c450e8c8a37bd619947d93196b8aa6b57393e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044910"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Tutorial: Herstellen von Netzwerkverbindungen und Stromversorgung für den Azure FXT Edge Filer-Knoten

In diesem Tutorial erfahren Sie, wie Sie die Netzwerkverbindungen für einen Azure FXT Edge Filer-Hardwareknoten verkabeln.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Auswählen des passenden Netzwerkkabels für Ihre Umgebung
> * Herstellen der Verbindung zwischen einem Azure FXT Edge Filer-Knoten und Ihrem Datencenternetzwerk
> * Nutzen des Kabelführungsarms (Cable Management Arm, CMA) bei der Verkabelung
> * Herstellen der Stromversorgung für das im Rack installierte Gerät und Einschalten des Geräts

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, muss das Azure FXT Edge Filer-Gerät in einem Standardrack installiert werden. Der Kabelführungsarm muss für den Filer-Knoten installiert werden.

## <a name="identify-ports"></a>Identifizieren der Anschlüsse

Machen Sie sich mit den verschiedenen Anschlüssen auf der Rückseite Ihres Azure FXT Edge Filer-Geräts vertraut.

![Rückseite eines verkabelten Geräts](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Verkabeln des Geräts

* Verbinden Sie die RJ45-Anschlüsse wie unter [Netzwerkanschlüsse](#network-ports) beschrieben mit der Netzwerkquelle Ihres Rechenzentrums.  
* Verbinden Sie den [iDRAC-Anschluss](#idrac-port) mit einem separaten Netzwerk mit sicherem DHCP-Server.
* Verwenden Sie die USB-Anschlüsse und den VGA-Anschluss, um für die Ersteinrichtung eine Tastatur und einen Monitor an den Knoten anzuschließen. Sie müssen den Knoten starten und [ein anfängliches Kennwort festlegen](node-password.md), um die anderen Anschlüsse des Knotens zu aktivieren. Ausführliche Informationen finden Sie unter [Tutorial: Set hardware passwords](node-password.md) (Tutorial: Festlegen von Hardwarekennwörtern).

In diesem Artikel wird auch das [Anschließen der Stromkabel](#connect-power-cables) für den Knoten beschrieben.

Außerdem erfahren Sie, wie Sie bei Bedarf den [seriellen Anschluss](#serial-port-only-when-necessary) des Knotens verwenden, um eine spezielle Problembehandlung durchzuführen.

### <a name="network-ports"></a>Netzwerkports

Jeder Azure FXT Edge Filer-Knoten verfügt über folgende Netzwerkanschlüsse:

* Sechs Dual-Rate-Datenanschlüsse mit hoher Geschwindigkeit (25 GbE/10 GbE):

  * Vier Anschlüsse in Form von zwei Plug-In-Netzwerkadaptern mit jeweils zwei Anschlüssen
  * Zwei Anschlüsse des Mezzanine-Netzwerkadapters der Hauptplatine

* Zwei 1-GbE-Anschlüsse des Mezzanine-Netzwerkadapters der Hauptplatine

Die Datenanschlüsse mit hoher Geschwindigkeit (25 GbE/10 GbE) verfügen über standardmäßige, SPF28-konforme Gehäuse. Wenn Sie optische Kabel verwenden möchten, müssen Sie optische SFP28-Transceivermodule installieren (nicht im Lieferumfang enthalten).

Die 1-GbE-Anschlüsse verfügen über RJ-45-Standardbuchsen.

Eine vollständige Liste der unterstützten Kabel, Switches und Transceiver finden Sie in der [Interoperabilitätsmatrix für die Cavium FastlinQ 41000-Reihe](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

Welche Verbindungen für Ihr System verwendet werden müssen, hängt von Ihrer Rechenzentrumsumgebung ab.

* Wenn Sie eine Verbindung mit einem 25-GbE-Netzwerk herstellen, verkabeln Sie die Datenanschlüsse mit hoher Geschwindigkeit jeweils mithilfe eines der folgenden Kabeltypen:

  * Optische Kabel und SFP28-Transceiver mit einer Kapazität von 25 GbE oder 25 GbE/10 GbE (Dual Rate)
  * Direkt anschließbares Twinaxialkabel (SFP28-konform und 25-GbE-fähig)

* Wenn Sie eine Verbindung mit einem 10-GbE-Netzwerk herstellen, verkabeln Sie die Datenanschlüsse mit hoher Geschwindigkeit jeweils mithilfe eines der folgenden Kabeltypen:

  * Optische Kabel und SFP28-Transceiver mit einer Kapazität von 10 GbE oder 25 GbE/10 GbE (Dual Rate)
  * Direkt anschließbares Twinaxialkabel (SFP28-konform und 25-GbE-fähig)
  * Direkt anschließbares Twinaxialkabel (SFP28-konform und 10-GbE-fähig)

* Die 1-GbE-Netzwerkanschlüsse werden für Datenverkehr im Zusammenhang mit der Clusterverwaltung genutzt. Aktivieren Sie bei der Clustererstellung das Kontrollkästchen **Use 1Gb mgmt network** (1-Gbit-Verwaltungsnetzwerk verwenden), wenn Sie für die Clusterkonfiguration ein physisch getrenntes Netzwerk verwenden möchten, wie unter [Configure the management network](cluster-create.md#configure-the-management-network) (Konfigurieren des Verwaltungsnetzwerks) beschrieben. Verkabeln Sie die Anschlüsse über standardmäßige Cat5-Kabel (oder besser), wie in der Liste mit den unterstützten Kabeln beschrieben.

  Wenn Sie für den gesamten Datenverkehr die Datenanschlüsse mit hoher Geschwindigkeit verwenden möchten, müssen die 1-GbE-Anschlüsse nicht verkabelt werden. Standardmäßig werden die 1-GbE-Netzwerkanschlüsse nicht verwendet, wenn ein Datenanschluss mit einer höheren Geschwindigkeit verfügbar ist.  

### <a name="idrac-port"></a>iDRAC-Anschluss  

Bei dem mit iDRAC beschrifteten Anschluss handelt es sich um eine 1-Gbit-Verbindung für die Kommunikation mit einem RAS-Controller zur Hardwareverwaltung und Überwachung. FXT-Software nutzt mit diesem Controller die IPMI (Intelligent Platform Management Interface) zur Problembehandlung und Wiederherstellung. Sie können die integrierte [iDRAC-Schnittstelle](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) zur Hardwareüberwachung über diesen Anschluss verwenden. iDRAC- und IPMI-Zugriff sind standardmäßig aktiviert.

> [!Note]
> Der iDRAC-Anschluss kann unter Umgehung des Betriebssystems direkt mit der Hardware des Knotens interagieren.

Verwenden Sie die folgenden Sicherheitsstrategien, wenn Sie den iDRAC-Anschluss verwenden und konfigurieren:

* Verbinden Sie iDRAC-Anschlüsse nur mit einem Netzwerk, das physisch vom für den Clusterzugriff verwendeten Datennetzwerk getrennt ist.
* Legen Sie für jeden Knoten ein sicheres iDRAC-Administratorkennwort fest. Dieses Kennwort muss festgelegt werden, um die Hardware zu aktivieren. Eine entsprechende Anleitung finden Sie unter [Tutorial: Set hardware passwords](node-password.md) (Tutorial: Festlegen von Hardwarekennwörtern).
* In der Standardkonfiguration des iDRAC-Anschlusses werden DHCP und IPv4 für die IP-Adresszuweisung verwendet. Stellen Sie sicher, dass Ihre DHCP-Umgebung gut geschützt ist und Verbindungen zwischen DHCP-Clients und DHCP-Server eingeschränkt sind. (Die Clustersystemsteuerung enthält Einstellungen, mit denen die Adresskonfigurationsmethode der Knoten nach der Clustererstellung geändert werden kann.)
* Behalten Sie für den iDRAC-Anschluss den dedizierten Modus (Standardeinstellung) bei. Dadurch wird der iDRAC/IPMI-Netzwerkdatenverkehr auf den dedizierten RJ45-Anschluss beschränkt.

Für den iDRAC-Anschluss wird keine Netzwerkverbindung mit hoher Geschwindigkeit benötigt.
  
### <a name="serial-port-only-when-necessary"></a>Serieller Anschluss (nur bei Bedarf)

In einigen Fällen werden Sie vom Service/Support von Microsoft ggf. dazu aufgefordert, zur Problemdiagnose ein Terminal an den seriellen Port eines Knotens anzuschließen.

So schließen Sie die Konsole an:

1. Suchen Sie auf der Rückseite des FXT Edge Filer-Knotens nach dem seriellen Anschluss (COM1).
1. Verbinden Sie den seriellen Anschluss über ein Null-Modem-Kabel mit einem für ANSI-115200-8N1 konfigurierten Terminal.
1. Melden Sie sich bei der Konsole an, und führen Sie gemäß den Anweisungen des Supportpersonals weitere Schritte aus.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Verwenden des Kabelführungsarms (Cable Management Arm, CMA)

Im Lieferumfang jedes Azure FXT Edge Filer-Knotens ist ein optionaler Kabelführungsarm enthalten. Der Kabelführungsarm vereinfacht die Kabelführung sowie den Zugang zur Gehäuserückseite, ohne Kabel ausstecken zu müssen.

Gehen Sie wie folgt vor, um den Kabelführungsarm zu verwenden:

1. Fassen Sie die Kabel am Anfang und Ende des Kabelkanals mithilfe der Kabelbinder zusammen, damit benachbarte Systeme nicht behindert werden (1).
1. Bringen Sie den Kabelführungsarm in die Wartungsposition, und führen Sie das Kabelbündel durch den inneren und äußeren Kabelkanal (2).
1. Fixieren Sie die Kabel mithilfe der dafür vorgesehenen Klettbänder an beiden Enden der Kabelkanäle (3).
1. Bringen Sie den Kabelführungsarm wieder in die Normalposition (4).
1. Installieren Sie das Kabel für die Statusanzeige auf der Rückseite des Systems, und führen Sie es durch den Kabelführungsarm, um es zu befestigen. Befestigen Sie das andere Ende des Kabels am Rand des äußeren Kabelkanals des Kabelführungsarms (5).

   > [!CAUTION]
   > Achten Sie darauf, dass das Kabel für die Statusanzeige nicht durchhängt, nachdem Sie das Kabel durch den Kabelführungsarm geführt haben, um eine Beschädigung durch überstehende Kabel zu vermeiden.

![Abbildung: Kabelführungsarm mit installierten Kabeln](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
> Sollte der Kabelführungsarm nicht installiert sein, verwenden Sie die beiden Klettbänder des Schienensatzes, um die Kabel auf der Rückseite des Systems zu verlegen.
>
>  1. Suchen Sie an der Innenseite der beiden Rackflansche nach den äußeren CMA-Klammern.
>  2. Fassen Sie die Kabel lose zu seinem Bündel zusammen, und achten Sie dabei darauf, dass sie sich außer Reichweite der Systembefestigungen auf der linken und rechten Seite befinden.
>  3. Führen Sie die Klettbänder durch die Schlitze für die Werkzeugmontage (an der Außenseite der CMA-Klammern auf beiden Seiten des Systems), um die Kabelbündel zu befestigen.
>
>     ![Kabelführung ohne Kabelführungsarm](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Voraussetzungen für IP-Adressen

IP-Adressen für Hardwareknoten in einem Azure FXT Edge Filer-Hybridspeichercache werden von der Clustersoftware verwaltet.

Jeder Knoten benötigt mindestens eine IP-Adresse. Knotenadressen werden allerdings zugewiesen, wenn Knoten zum Cluster hinzugefügt oder daraus entfernt werden.

Die Gesamtanzahl der erforderlichen IP-Adressen hängt von der Knotenanzahl für den Cache ab.

Konfigurieren Sie den IP-Adressbereich mithilfe der Systemsteuerungssoftware, nachdem die Knoten installiert wurden. Weitere Informationen finden Sie unter [Gather information for the cluster](cluster-create.md#gather-information-for-the-cluster) (Sammeln von Informationen für den Cluster).  

## <a name="connect-power-cables"></a>Anschließen der Netzkabel

Jeder Azure FXT Edge Filer-Knoten verfügt über zwei Netzteile (Power Supply Units, PSUs).

> [!TIP]
> Schließen Sie zur Verwendung der beiden redundanten PSUs jeweils eines der Netzkabel an eine PDU (Power Distribution Unit) mit einer unabhängigen Verzweigungsleitung an.
>
> PDUs können zur besseren Absicherung auch über eine USV versorgt werden.

1. Verbinden Sie die beiliegenden Netzkabel mit den PSUs im Gehäuse. Vergewissern Sie sich, dass die Kabel und PSUs ordnungsgemäß angeschlossen sind.
1. Schließen Sie die Netzkabel an die PDUs des Racks an. Verwenden Sie nach Möglichkeit zwei separate Stromquellen für die beiden Kabel.

### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Einschalten eines Azure FXT Edge Filer-Knotens

Drücken Sie zum Einschalten des Knotens den Netzschalter auf der Vorderseite des Systems. Der Schalter befindet sich auf der rechten Seite des Bedienelements.

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Ausschalten eines Azure FXT Edge Filer-Knotens

Das System kann bei Tests sowie vor dem Hinzufügen zu einem Cluster über den Netzschalter heruntergefahren werden. Sobald ein Azure FXT Edge Filer-Knoten allerdings als Teil eines Clusters verwendet wird, sollte die Hardware über die Systemsteuerungssoftware des Clusters heruntergefahren werden. Ausführliche Informationen finden Sie unter [How to safely power off Azure FXT Edge Filer hardware](power-off.md) (Sicheres Ausschalten von Azure FXT Edge Filer-Hardware).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Hardware vollständig verkabelt haben, können Sie die einzelnen Knoten einschalten und ihre Stammkennwörter festlegen, um sie zu initialisieren.
> [!div class="nextstepaction"]
> [Festlegen anfänglicher Kennwörter](node-password.md)
