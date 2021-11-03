---
title: 'Tutorial: Installieren eines physischen Azure FXT Edge Filer-Geräts'
description: Hier erfahren Sie, wie Sie die physische Gerätekomponente des Microsoft Azure FXT Edge Filer-Hybridspeichercaches auspacken, in einem Rack installieren und verkabeln.
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: e2ff6ab12ee4941f6a4f0c63836b06ec69875de6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086054"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Tutorial: Installieren von Azure FXT Edge Filer

In diesem Tutorial erfahren Sie, wie Sie einen Hardwareknoten für den Azure FXT Edge Filer-Hybridspeichercache installieren. Für einen Azure FXT Edge Filer-Cluster müssen mindestens drei Hardwareknoten installiert werden.

Die Installation umfasst das Auspacken und die Rackmontage des Geräts sowie das Anbringen des Kabelführungsarms (Cable Management Arm, CMA) und der Frontblende. Das Anschließen der Netzwerkkabel und das Herstellen der Stromversorgung werden in einem separaten Tutorial beschrieben.

Die Installation eines Azure FXT Edge Filer-Knotens dauert etwa eine Stunde.

Dieses Tutorial umfasst folgende Einrichtungsschritte:

> [!div class="checklist"]
>
> * Auspacken des Geräts
> * Montieren des Geräts in einem Rack
> * Anbringen der Frontblende (optional)

## <a name="installation-prerequisites"></a>Voraussetzungen für die Installation

Vergewissern Sie sich zunächst, dass das Rechenzentrum und das für die Montage vorgesehene Rack über Folgendes verfügen:

* Freier Einschub mit 1 HE in dem Rack, in dem Sie das Gerät montieren möchten
* Stromversorgungs- und Kühlsysteme, die die Anforderungen des Azure FXT Edge Filer-Geräts erfüllen. (Hilfreiche Informationen zur Planung und Dimensionierung der Installation finden Sie unter [Power and thermal specifications](specs.md#power-and-thermal-specifications) (Leistungs- und Temperaturangaben).)  

  > [!NOTE]
  > Verwenden Sie Stromverteilereinheiten mit zwei verschiedenen Verzweigungsleitungen, um die beiden redundanten Netzteile (Power Supply Units, PSUs) optimal zu nutzen. Ausführliche Informationen finden Sie unter [Anschließen der Netzkabel](network-power.md#connect-power-cables).  

## <a name="unpack-the-hardware-node"></a>Auspacken des Hardwareknotens

Jeder Azure FXT Edge Filer-Knoten wird in einem einzelnen Karton ausgeliefert. Gehen Sie zum Auspacken eines Geräts wie folgt vor:

1. Legen Sie den Karton auf einen flachen, ebenen Untergrund.

2. Überprüfen Sie den Karton und das Verpackungsmaterial auf Risse, Schnitte, Wasserschäden oder anderweitige offensichtliche Beschädigungen. Sollte der Karton oder die Verpackung stark beschädigt sein, öffnen Sie den Karton bzw. die Verpackung nicht. Wenden Sie sich an den Microsoft-Support, um zu ermitteln, ob das Gerät funktionstüchtig ist.

3. Packen Sie den Karton aus. Vergewissern Sie sich, dass Folgendes vorhanden ist:
   * Ein FXT-Gerät (einzelnes Gehäuse)
   * Zwei Netzkabel
   * Eine Frontblende und ein Schlüssel
   * Eine Schienenbaugruppe
   * Ein Kabelführungsarm (Cable Management Arm, CMA)
   * Installationsanleitung für den Kabelführungsarm (Booklet)
   * Anleitung für die Rackmontage (Booklet)
   * Informationen zu Sicherheit, Umwelt und rechtlichen Aspekten (Booklet)

Sollte etwas fehlen, wenden Sie sich an den Gerätehersteller.

Warten Sie, bis das Gerät Raumtemperatur erreicht hat, bevor Sie es installieren oder einschalten. Sollte sich an dem Gerät Kondensfeuchtigkeit gebildet haben, warten Sie mindestens 24 Stunden mit der Installation.

Im nächsten Schritt wird das Gerät in ein Rack eingebaut.

## <a name="rack-the-device"></a>Montieren des Geräts in einem Rack

Das Azure FXT Edge Filer-Gerät muss in einem standardmäßigen 19-Zoll-Rack installiert werden.

Der Azure FXT Edge Filer-Hybridcachespeicher besteht aus drei oder mehr Azure FXT Edge Filer-Geräten. Wiederholen Sie die Schritte für die Rackinstallation für jedes Gerät, das Teil Ihres Systems ist.

### <a name="rack-install-prerequisites"></a>Voraussetzungen für die Rackinstallation

* Machen Sie sich zunächst mit den Sicherheitsanweisungen vertraut. Diese finden Sie im beiliegenden Booklet mit den Informationen zu Sicherheit, Umwelt und rechtlichen Aspekten.

  > [!NOTE]
  > Heben Sie den Knoten immer zu zweit an. Das gilt auch, wenn Sie ihn in einem Rack installieren oder daraus entfernen.

* Ermitteln Sie die Art der Schieneninstallation für Ihr Rack.
  * Verwenden Sie bei Racks, die über einen Einrastmechanismus (Vierkant- oder Rundlöcher) verfügen, die Anleitung für die Schienenmontage ohne Werkzeug.
  * Bei Racks mit Gewindelöchern muss die Anleitung für die Schienenmontage mit Werkzeug verwendet werden.
  
    Für die Schienenmontage mit Werkzeug benötigen Sie acht Schrauben vom Typ 10-32, 12-24, M5 oder M6. Der Durchmesser des Schraubenkopfs muss weniger als 10 mm (0,4") betragen.

### <a name="identify-the-rail-kit-contents"></a>Überprüfen des Inhalts des Schienensatzes

Für die Installation der Schienenbaugruppe benötigen Sie folgende Komponenten:

1. Zwei Gleitschienen vom Typ „A7 Dell ReadyRails II“ (1)
1. Zwei Klettbänder (2)

![Nummerierte Abbildung des Schienensatzes](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Schienenmontage ohne Werkzeug (Racks mit Vierkant- oder Rundlöchern)

Gehen Sie bei Racks, die über einen Einrastmechanismus (Vierkant- oder Rundlöcher) verfügen, wie folgt vor, um die Schienen zu montieren:

1. Positionieren Sie das mit **FRONT** gekennzeichnete Endstück der linken und rechten Schiene so, dass es nach innen zeigt. Befestigen Sie die Endstücke mithilfe der Löcher auf der Vorderseite der vertikalen Rackflansche. (1)

2. Richten Sie die Endstücke in der gewünschten Montageposition an den unteren und oberen Löchern des Racks aus. (2)

3. Schieben Sie das hintere Ende der Schiene in Richtung des vertikalen Rackflanschs, bis es einrastet. Wiederholen Sie die gleiche Prozedur, um das vordere Endstück am vertikalen Rackflansch anzubringen. (3)

> [!TIP]
> Wenn Sie die Schienen entfernen möchten, betätigen Sie jeweils die Entriegelung in der Mitte des Endstücks (4), und entnehmen Sie die Schiene.

![Diagramm mit nummerierten Schritten zum Installieren und Entfernen der Schienen ohne Werkzeug](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Schienenmontage mit Werkzeug (Racks mit Gewindelöchern)

Gehen Sie bei Racks mit Gewindelöchern wie folgt vor, um die Schienen zu montieren:

1. Entfernen Sie die Stifte an der vorderen und hinteren Befestigungsklammer mithilfe eines Schlitzschraubendrehers. (1)
1. Ziehen und drehen Sie die Schienenarretierungen, um sie von den Befestigungsklammern zu entfernen. (2)
1. Befestigen Sie die linke und die rechte Schiene mit jeweils zwei Schrauben an den vorderen vertikalen Rackflanschen. (3)
1. Schieben Sie die linke und die rechte hintere Klammer von hinten gegen die hinteren vertikalen Rackflansche, und befestigen Sie sie ebenfalls mit jeweils zwei Schrauben. (4)

![Diagramm mit nummerierten Schritten zum Installieren und Entfernen der Schienen mit Werkzeug](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Installieren des Systems im Rack

Gehen Sie wie folgt vor, um das Azure FXT Edge Filer-Gerät im Rack zu montieren:

1. Ziehen Sie die inneren Gleitschienen aus dem Rack, bis sie einrasten. (1)
1. Setzen Sie den hinteren Schienenabstandshalter auf beiden Seiten des Geräts von oben in die hinteren J-förmigen Schlitze der Schienenbaugruppen ein. (2)
1. Drehen Sie das Gerät nach unten, bis alle Schienenabstandshalter in den J-förmigen Schlitzen platziert sind. (3)
1. Schieben Sie das Gerät nach innen, bis die Arretierhebel einrasten.
1. Drücken Sie auf den Schienenentriegelungsmechanismus an beiden Schienen (4), und schieben Sie das Gerät in das Rack.

![Diagramm mit nummerierten Schritten zum Installieren des Systems in einem Rack](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Entfernen des Systems aus dem Rack

Wenn Sie das Gerät aus dem Rack entfernen möchten, gehen Sie wie folgt vor:

1. Suchen Sie an der Seite der inneren Schienen nach den Arretierhebeln (1).
2. Entriegeln Sie den Mechanismus, indem Sie die Hebel jeweils nach oben in die geöffnete Position drehen (2).
3. Halten Sie beide Seiten des Systems gut fest, und ziehen Sie das System nach vorn, bis sich die Schienenabstandhalter an der Vorderseite der J-förmigen Schlitze befinden. Heben Sie das System nach oben aus dem Rack heraus, und legen Sie es auf einer ebenen Fläche ab (3).

![Abbildung mit nummerierten Schritten zum Entfernen eines Systems aus dem Rack](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Verwenden des Schnappmechanismus

1. Suchen Sie an der Vorderseite auf beiden Seiten des Systems nach dem Schnappmechanismus (1).
2. Der Mechanismus rastet automatisch ein, wenn das System in das Rack geschoben wird.

Zum Entriegeln müssen die beiden Riegel nach oben gezogen werden (2).

Für den Transport oder in instabilen Umgebungen kann das System bei Bedarf mithilfe der verfügbaren Befestigungsschrauben fixiert werden. Die Schrauben befinden sich jeweils unter dem Riegel und können mit einem passenden Kreuzschlitzschraubendreher (#2) angezogen werden (3).

![Nummerierte Abbildung zur Verwendung des Schnappmechanismus](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Installieren des Kabelführungsarms

Dem FXT Edge Filer-Gerät liegt ein optionaler Kabelführungsarm (Cable Management Arm, CMA) bei. Im Lieferumfang ist auch ein gedrucktes Booklet mit einer Installationsanleitung enthalten.

1. Packen Sie die Komponenten des Kabelführungsarms aus:
   * CMA-Einsatz (1)
   * CMA (2)
   * Nylon-Kabelbinder (3)
   * CMA-Befestigungsklammern (4)
   * Kabel für die Statusanzeige (5)

   > [!TIP]
   > Falls Sie das Rack transportieren möchten, während sich der Kabelführungsarm darin befindet, fassen Sie die beiden Kabelkanäle und den Einsatz mithilfe der Kabelbinder zusammen, und ziehen Sie sie gut fest. Diese Methode kann auch verwendet werden, um das System in instabilen Umgebungen zu schützen.

   ![Abbildung der CMA-Komponenten](media/fxt-install/cma-kit-400.png)

2. Installieren Sie den CMA-Einsatz.

   Der CMA-Einsatz sorgt für Stabilität und fungiert als Halterung für den Kabelführungsarm.

   1. Richten Sie die Seiten des Einsatzes an den Aufnahmeklammern auf der Innenseite der Schienen aus, und befestigen Sie den Einsatz.
   1. Schieben Sie den Einsatz hinein, bis er einrastet. (1)
   1. Wenn Sie den Einsatz entfernen möchten, betätigen Sie den Entriegelungsmechanismus, indem Sie die Entriegelungsknöpfe zur Mitte hin drücken, und ziehen Sie den Einsatz aus den Aufnahmeklammern heraus (2).

   ![Abbildung der Installation des CMA-Einsatzes](media/fxt-install/cma-tray-install-400.png)

3. Installieren Sie die CMA-Befestigungsklammern.

   > [!NOTE]
   >
   > * Der Kabelführungsarm kann an der linken oder an der rechten Montageschiene befestigt werden (je nachdem, wie Sie die Kabel des Systems verlegen möchten).
   > * Es empfiehlt sich, den Kabelführungsarm auf der gegenüberliegenden Seite der Netzteile (Seite A) zu montieren. Wenn Sie ihn auf der Seite B montieren, muss er gelöst werden, um das äußere Netzteil entfernen zu können.
   > * Vor dem Entfernen der Netzteile muss immer erst der Einsatz entfernt werden.

   ![Abbildung der Installation der CMA-Befestigungsklammern](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Wählen Sie die passende CMA-Befestigungsklammer für die Seite, auf der Sie den Kabelführungsarm montieren möchten (Seite B oder Seite A).
   1. Bringen Sie die entsprechend gekennzeichnete CMA-Befestigungsklammer für Seite A oder B am hinteren Ende der Gleitschiene an.
   1. Richten Sie die Löcher der Klammer an den Stiften der Gleitschiene aus. Drücken Sie die Klammer nach unten, bis sie einrastet.

4. Installieren Sie den Kabelführungsarm.

   1. Lassen Sie auf der Rückseite des Systems den Schnappmechanismus, der sich am vorderen Ende des Kabelführungsarms befindet, an der inneren Klammer der Schienenbaugruppe einrasten (1).
   1. Lassen Sie den anderen Schnappmechanismus am Ende der äußeren Klammer einrasten (2).
   1. Wenn Sie den Kabelführungsarm entfernen möchten, betätigen Sie den Entriegelungsmechanismus, der sich jeweils an der Oberseite der inneren und äußeren Vorrichtung mit dem Schnappmechanismus befindet (3).

   ![Abbildung der Installation des eigentlichen Kabelführungsarms](media/fxt-install/cma-install-400.png)

   Der Kabelführungsarm lässt sich zur Seite schwenken, um Arbeiten am System zu erleichtern. Heben Sie den Kabelführungsarm auf der Seite mit dem Gelenk an, um ihn aus dem Einsatz zu lösen (1). Schwenken Sie ihn anschließend zur Seite (2).

   ![Abbildung des Kabelführungsarms in der Wartungsposition](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Anbringen der Frontblende (optional)

In diesem Abschnitt erfahren Sie, wie Sie die Frontblende für die Azure FXT Edge Filer-Hardware anbringen und entfernen.

So bringen Sie die Frontblende an:

1. Entnehmen Sie den Blendenschlüssel aus dem Paket mit der Blende.
1. Richten Sie die Blende an der Vorderseite des Gehäuses aus, und führen Sie die Stifte auf der rechten Seite der Blende in die Aufnahmelöcher des rechten Flanschs für die Rackmontage des Knotens ein.
1. Positionieren Sie die linke Seite der Blende ordnungsgemäß am Gehäuse. Drücken Sie die Blende gegen das Gehäuse, bis sie auf der linken Seite einrastet.
1. Verriegeln Sie die Blende mithilfe des Schlüssels.

So entfernen Sie die Frontblende:

1. Entriegeln Sie die Blende mithilfe des Blendenschlüssels.
1. Drücken Sie links auf die Entriegelungstaste, und ziehen Sie die linke Seite der Blende vom Gehäuse weg.
1. Hängen Sie die rechte Seite aus, und entfernen Sie die Blende.

   ![Abbildung der Entriegelungstaste auf der linken Seite der Blende sowie der Vorgehensweise zum Entfernen der Blende durch Herausziehen der linken Seite](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie das Gerät ausgepackt und im Rack montiert haben, können Sie als Nächstes die Netzwerkkabel anschließen und die Stromversorgung für das Azure FXT Edge Filer-Gerät herstellen.

> [!div class="nextstepaction"]
> [Tutorial: Herstellen der Netzwerkverbindungen und der Stromversorgung für den Azure FXT Edge Filer-Knoten](network-power.md)
