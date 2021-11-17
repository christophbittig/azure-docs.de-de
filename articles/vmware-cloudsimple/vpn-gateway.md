---
title: 'Azure VMware Solution by CloudSimple: Einrichten eines VPN-Gateways'
description: Beschreibt, wie Sie ein Point-to-Site-VPN-Gateway und ein Site-to-Site-VPN-Gateway einrichten und Verbindungen zwischen Ihrem lokalen Netzwerk und Ihrer privaten CloudSimple-Cloud herstellen.
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 047a764d78ae011c50f20f1ee5a12cac5080b497
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283295"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>Einrichten von VPN-Gateways für ein CloudSimple-Netzwerk

VPN-Gateways ermöglichen es Ihnen, eine Verbindung mit einem CloudSimple-Netzwerk aus Ihrem lokalen Netzwerk und über einen Clientcomputer remote herzustellen. Eine VPN-Verbindung zwischen Ihrem lokalen Netzwerk und Ihrem CloudSimple-Netzwerk ermöglicht den Zugriff auf vCenter und Workloads in Ihrer privaten Cloud. CloudSimple unterstützt Site-to-Site-VPN- und Point-to-Site-VPN-Gateways.

## <a name="vpn-gateway-types"></a>VPN-Gatewaytypen

* Eine **Site-to-Site-VPN**-Verbindung ermöglicht Ihnen das Einrichten der Workloads Ihrer privaten Cloud, um auf lokale Dienste zuzugreifen. Sie können auch ein lokales Active Directory als Identitätsquelle für die Authentifizierung bei Ihrem vCenter in der privaten Cloud verwenden.  Derzeit wird nur der **richtlinienbasierte VPN**-Typ unterstützt.
* Eine **Point-to-Site-VPN**-Verbindung ist die einfachste Möglichkeit, von Ihrem Computer eine Verbindung mit der privaten Cloud herzustellen. Verwenden Sie eine Point-to-Site-VPN-Verbindung, wenn Sie eine Remoteverbindung mit der privaten Cloud herstellen möchten. Informationen zum Installieren eines Clients für eine Point-to-Site-VPN-Verbindung finden Sie unter [Konfigurieren einer VPN-Verbindung mit Ihrer privaten Cloud](set-up-vpn.md).

In einer Region können Sie ein Point-to-Site-VPN-Gateway und ein Site-to-Site-VPN-Gateway erstellen.

## <a name="automatic-addition-of-vlansubnets"></a>Automatisches Hinzufügen von VLANs/Subnetzen

CloudSimple-VPN-Gateways bieten Richtlinien zum Hinzufügen von VLANs/Subnetzen zu VPN-Gateways.  Mit Richtlinien können Sie verschiedene Regeln für Verwaltungs-VLANs/-Subnetze und benutzerdefinierte VLANs/Subnetze angeben.  Regeln für Verwaltungs-VLANs/-Subnetze gelten für alle neuen privaten Clouds, die Sie erstellen.  Regeln für benutzerdefinierte VLANs/Subnetze ermöglichen das automatische Hinzufügen neuer VLANs/Subnetze zu vorhandenen oder neuen privaten Clouds für ein Site-to-Site-VPN-Gateway. Sie definieren die Richtlinie für jede Verbindung.

Die Richtlinien zum Hinzufügen von VLANs/Subnetzen zu VPN-Gateways gelten sowohl für Site-to-Site-VPN- als auch für Point-to-Site-VPN-Gateways.

## <a name="automatic-addition-of-users"></a>Automatisches Hinzufügen von Benutzern

Mit einem Point-to-Site-VPN-Gateway können Sie eine Richtlinie zum automatischen Hinzufügen von neuen Benutzern definieren. Standardmäßig verfügen alle Besitzer und Mitwirkenden des Abonnements über Zugriff auf das CloudSimple-Portal.  Benutzer werden nur erstellt, wenn das CloudSimple-Portal zum ersten Mal gestartet wird.  Wenn Sie **Automatically add rules** (Regeln automatisch hinzufügen) auswählen, kann jeder neue Benutzer über eine Point-to-Site-VPN-Verbindung auf das CloudSimple-Netzwerk zugreifen.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Einrichten eines Site-to-Site-VPN-Gateways

1. [Greifen Sie auf das CloudSimple Portal zu](access-cloudsimple-portal.md), und wählen Sie **Network** (Netzwerk) aus.
2. Wählen Sie **VPN Gateway** aus.
3. Klicken Sie auf **New VPN Gateway**.

    ![Erstellen eines VPN-Gateways](media/create-vpn-gateway.png)

4. Geben Sie für **Gateway configuration** die folgenden Einstellungen an, und klicken Sie auf **Weiter**.

    * Wählen Sie **Site-to-Site VPN** als Gatewaytyp aus.
    * Geben Sie einen Namen ein, um das Gateway zu bezeichnen.
    * Wählen Sie den Azure-Speicherort aus, in dem der CloudSimple-Dienst bereitgestellt wird.
    * Aktivieren Sie optional Hochverfügbarkeit.

    ![Erstellen eines Site-to-Site-VPN-Gateways](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Das Aktivieren der von Hochverfügbarkeit erfordert, dass Ihr lokales VPN-Gerät das Herstellen einer Verbindung mit zwei IP-Adressen unterstützt. Diese Option kann nicht deaktiviert werden, nachdem das VPN-Gateway bereitgestellt wurde.

5. Erstellen Sie die erste Verbindung aus Ihrem lokalen Netzwerk, und klicken Sie auf **Weiter**.

    * Geben Sie einen Namen ein, um die Verbindung zu identifizieren.
    * Geben Sie als IP-Peeradresse die öffentliche IP-Adresse Ihres lokalen VPN-Gateways ein.
    * Geben Sie den Peerbezeichner Ihres lokalen VPN-Gateways ein.  Der Peerbezeichner ist normalerweise die öffentliche IP-Adresse Ihres lokalen VPN-Gateways.  Wenn Sie einen bestimmten Bezeichner für Ihr Gateway konfiguriert haben, geben Sie diesen Bezeichner ein.
    * Kopieren Sie den gemeinsam verwendeten Schlüssel, der für die Verbindung von Ihrem lokalen VPN-Gateway verwendet werden soll.  Um den standardmäßigen gemeinsam verwendeten Schlüssel zu ändern und einen neuen Schlüssel anzugeben, klicken Sie auf das Bearbeitungssymbol.
    * Geben Sie für **On-Premises Prefixes** (Lokale Präfixe) die lokalen CIDR-Präfixe ein, die auf das cloudsimple-Netzwerk zugreifen.  Beim Erstellen der Verbindung können Sie mehrere CIDR-Präfixe hinzufügen.

    ![Erstellen einer Site-to-Site-VPN-Gatewayverbindung](media/create-vpn-gateway-s2s-connection.png)

6. Aktivieren Sie die VLANs/Subnetze für Ihr privates Cloudnetzwerk, auf das über das lokale Netzwerk zugegriffen werden soll, und klicken Sie dann auf **Next** (Weiter).

    * Aktivieren Sie **Add management VLANs/Subnets of Private Clouds** (Verwaltungs-VLANs/-Subnetze privater Clouds hinzufügen), um Verwaltungs-VLANs/-Subnetze hinzuzufügen.  Das Verwaltungssubnetz ist für vMotion- und vSAN-Subnetze erforderlich.
    * Um vMotion-Subnetze hinzuzufügen, aktivieren Sie **Add vMotion network of Private Clouds** (vMotion-Netzwerk privater Clouds hinzufügen).
    * Um vSAN-Subnetze hinzuzufügen, aktivieren Sie **Add vSAN network of Private Clouds** (vSAN-Netzwerk privater Clouds hinzufügen).
    * Wählen Sie bestimmte VLANs aus, oder heben Sie die Auswahl auf.

    ![Erstellen der Verbindung](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Überprüfen Sie die Einstellungen, und klicken Sie auf **Submit** (Senden).

    ![Überprüfen und Erstellen eines Site-to-Site-VPN Gateways](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Erstellen eines Point-to-Site-VPN-Gateways

1. [Greifen Sie auf das CloudSimple Portal zu](access-cloudsimple-portal.md), und wählen Sie **Network** (Netzwerk) aus.
2. Wählen Sie **VPN Gateway** aus.
3. Klicken Sie auf **New VPN Gateway**.

    ![Erstellen eines VPN-Gateways](media/create-vpn-gateway.png)

4. Geben Sie für **Gateway configuration** die folgenden Einstellungen an, und klicken Sie auf **Weiter**.

    * Wählen Sie **Point-to-Site VPN** als Gatewaytyp aus.
    * Geben Sie einen Namen ein, um das Gateway zu bezeichnen.
    * Wählen Sie den Azure-Speicherort aus, in dem der CloudSimple-Dienst bereitgestellt wird.
    * Geben Sie das Clientsubnetz für das Point-to-Site-Gateway an.  DHCP-Adressen werden aus dem Clientsubnetz angegeben, wenn Sie eine Verbindung herstellen.

5. Geben Sie für **Connection/User** die folgenden Einstellungen an, und klicken Sie auf **Weiter**.

    * Um allen aktuellen und zukünftigen Benutzern automatisch Zugriff auf die private Cloud über dieses Point-to-Site-Gateway zu gestatten, wählen Sie **Automatically add all users** (Alle Benutzer automatisch hinzufügen) aus. Wenn Sie diese Option auswählen, werden automatisch alle Benutzer in der Benutzerliste ausgewählt. Sie können die automatische Option außer Kraft setzen, indem Sie einzelne Benutzer in der Liste deaktivieren.
    * Um einzelne Benutzer auszuwählen, klicken Sie auf die Kontrollkästchen in der Benutzerliste.

6. Im Abschnitt „VLANs/Subnets“ können Sie Verwaltungs- und Benutzer-VLANs/Subnetze für das Gateway und für Verbindungen angeben.

    * Mit den Optionen **Automatically add** (Automatisch hinzufügen) wird die globale Richtlinie für das Gateway festgelegt. Die Einstellungen gelten für das aktuelle Gateway. Die Einstellungen können im **Auswahl** bereich überschrieben werden.
    * Wählen Sie **Add management VLANs/Subnets of Private Clouds** aus. 
    * Um alle benutzerdefinierten VLANs/Subnetze hinzuzufügen, klicken Sie auf **Add user-defined VLANs/Subnets**.
    * Die **Auswahl**-Einstellungen überschreiben die globalen Einstellungen unter **Automatically add**.

7. Klicken Sie auf **Weiter**, um die Einstellungen zu überprüfen. Klicken Sie auf die Bearbeiten-Symbole, um jegliche Änderungen vorzunehmen.
8. Klicken Sie auf **Create**, um das VPN-Gateway zu erstellen.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Clientsubnetz und Protokolle für Point-to-Site-VPN-Gateways

Das Point-to-Site-VPN-Gateway ermöglicht TCP- und UDP-Verbindungen.  Wählen Sie das Protokoll aus, das verwendet werden soll, wenn Sie von Ihrem Computer aus eine Verbindung herstellen, indem Sie die TCP- oder UDP-Konfiguration auswählen.

Das konfigurierte Clientsubnetz wird für TCP- und UDP-Clients verwendet.  Das CIDR-Präfix ist in zwei Subnetze unterteilt: ein Subnetz für TCP und ein Subnetz für UDP-Clients. Wählen Sie die Präfixmaske basierend auf der Anzahl der VPN-Benutzer aus, die gleichzeitig eine Verbindung herstellen.  

In der folgenden Tabelle wird die Anzahl gleichzeitiger Clientverbindungen für die Präfixmask aufgeführt.

| Präfixmaske | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Anzahl gleichzeitiger Verbindungen | 124 | 60 | 28 | 12 | 4 |
| Anzahl gleichzeitiger UDP-Verbindungen | 124 | 60 | 28 | 12 | 4 |

Informationen zum Herstellen einer Verbindung mit einem Point-to-Site-VPN finden Sie unter [Herstellen einer Verbindung mit CloudSimple mit einem Point-to-Site-VPN](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn).
