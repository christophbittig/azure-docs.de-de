---
ms.author: cherylmc
author: cherylmc
ms.date: 08/18/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 53c6980ea6e5d868a842b1e14031f27ff51f655a
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122638255"
---
1. Navigieren Sie zu **Virtueller Hub > VPN (Site-to-Site)** .

1. Unter Umständen müssen Sie auf **Hub association: Connected to this hub** (Hubzuordnung: Mit diesem Hub verbunden) klicken, um die Filter zu löschen und Ihre Standorte anzuzeigen.

1. Aktivieren Sie das Kontrollkästchen für den Standort, den Sie verbinden möchten, und klicken Sie dann auf **VPN-Standorte verbinden**.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect-site.png" alt-text="Screenshot: Verbinden von Standorten":::

1. Konfigurieren Sie auf der Seite **Standorte verbinden** die erforderlichen Einstellungen.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Screenshot: Bereich „Standorte verbinden“ für einen virtuellen Hub, bereit für die Eingabe eines vorinstallierten Schlüssels und zugehöriger Einstellungen":::

   * **Vorinstallierter Schlüssel (PSK)** : Geben Sie den von Ihrem VPN-Gerät verwendeten vorinstallierten Schlüssel an. Wenn Sie keinen Schlüssel eingeben, wird von Azure automatisch ein Schlüssel generiert. Anschließend verwenden Sie diesen Schlüssel für die Konfiguration Ihres VPN-Geräts.
   * **Protokoll und IPsec**: Sie können entweder die Standardeinstellungen für „Protokoll“ (IKEv2) und „IPsec“ (Standard) übernehmen oder benutzerdefinierte Einstellungen konfigurieren. Weitere Informationen finden Sie unter [Virtual WAN-Standardrichtlinien für IPSec-Konnektivität](../articles/virtual-wan/virtual-wan-ipsec.md).
   * **Propagate Default Route** (Standardroute weitergeben): Ändern Sie diese Einstellung nur in **Aktivieren**, wenn Sie wissen, dass Sie die Standardroute weitergeben möchten. Lassen Sie andernfalls **Deaktivieren** eingestellt. Sie können diese Einstellung später jederzeit ändern. 
   
     Mit der Option **Aktivieren** kann der virtuelle Hub eine bekannte Standardroute für diese Verbindung weitergeben. Dieses Flag ermöglicht die Weitergabe der Standardroute an eine Verbindung, wenn diese dem Virtual WAN-Hub bereits bekannt ist, weil darin eine Firewall bereitgestellt wurde, oder wenn für eine andere verbundene Site die Tunnelerzwingung aktiviert ist. Der Ursprung der Standardroute liegt nicht im Virtual WAN-Hub. 
   * **Richtlinienbasierte Datenverkehrsauswahl verwenden**: Übernehmen Sie für diese Einstellung den Wert **Deaktivieren**, es sei denn, Sie konfigurieren eine Verbindung mit einem Gerät, das diese Einstellung verwendet.

1. Klicken Sie unten auf der Seite auf **Verbinden**.

1. Wenn Sie auf **Verbinden** klicken, wird **Aktualisierung...** unter „Konnektivitätsstatus“ angezeigt. Nach Abschluss der Aktualisierung werden für den Standort die Verbindung und der Konnektivitätsstatus angezeigt.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connectivity-status.png" alt-text="Screenshot: Site-to-Site-Verbindung und Konnektivitätsstatus" lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/connectivity-status.png":::

   **Bereitstellungsstatus der Verbindung**: Dies ist der Status der Azure-Ressource für die Verbindung zwischen dem VPN-Standort und dem VPN-Gateway des Azure-Hubs. Nachdem dieser Vorgang auf der Steuerungsebene erfolgreich abgeschlossen wurde, wird mit dem Herstellen der Konnektivität für das Azure-VPN-Gateway und das lokale VPN-Gerät fortgefahren.

   **Konnektivitätsstatus**: Dies ist der tatsächliche Status der Konnektivität (Datenpfad) zwischen dem VPN-Gateway von Azure im Hub und dem VPN-Standort. Nach Abschluss der Aktualisierung kann einer der folgenden Zustände angezeigt werden:

    * **Unbekannt:** Dieser Status tritt normalerweise auf, wenn die Back-End-Systeme aktiv sind und der Übergang in einen anderen Status erfolgt.
    * **Verbindung wird hergestellt…** : Das Azure-VPN-Gateway versucht, den tatsächlichen lokalen VPN-Standort zu erreichen.
    * **Verbunden**: Die Konnektivität zwischen dem VPN-Gateway und dem lokalen VPN-Standort wurde hergestellt.
   * **Nicht verbunden**: Die Konnektivität wurde nicht hergestellt.
    * **Getrennt**: Dieser Status tritt auf, wenn die Verbindung aus irgendeinem Grund (lokal oder in Azure) getrennt wurde.
1. Wenn Sie Änderungen am Standort vornehmen möchten, wählen Sie den Standort aus, und klicken Sie dann auf das Kontextmenü **...** .

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/edit.png" alt-text="Screenshot: Bearbeiten, Löschen und Herunterladen" lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/expand/edit.png":::

   Auf dieser Seite können Sie die folgenden Aufgaben ausführen: 

   * Bearbeiten oder Löschen der VPN-Verbindung
   * Löschen der VPN-Verbindung mit diesem Hub
   * Herunterladen einer branchspezifischen Konfiguration, um Details zum Azure-Standort zu erhalten. Wenn Sie die Konfigurationsdatei herunterladen möchten, die für alle verbundenen Standorte in Ihrem Hub gilt, wählen Sie stattdessen im Menü oben auf der Seite die Option **VPN-Konfiguration herunterladen** aus.
