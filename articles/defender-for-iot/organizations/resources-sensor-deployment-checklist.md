---
title: Prüfliste vor der Bereitstellung für Azure Defender für IoT
description: Dieser Artikel enthält Informationen und eine Prüfliste, die Sie bei der Vorbereitung Ihres Standorts auf die Bereitstellung verwenden sollten.
ms.date: 07/18/2021
ms.topic: checklist
ms.openlocfilehash: ac60e574a3d61bfa0c3106375d0606b7de6d9494
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/26/2021
ms.locfileid: "114676936"
---
# <a name="pre-deployment-checklist-overview"></a>Prüfliste für die Vorbereitung der Bereitstellung

Dieser Artikel enthält Informationen und eine Prüfliste, die Sie bei der Vorbereitung Ihres Standorts auf die Bereitstellung verwenden sollten, um ein erfolgreiches Onboarding sicherzustellen.

- Die physische Defender für IoT-Sensor muss mit den verwalteten Switches verbunden werden, die den Austausch von Industriedaten in den Schichten 1 und 2 sehen können (in manchen Fällen auch Schicht 3).
- Der Sensor lauscht an einem Switchspiegelport (SPAN-Port) oder TAP.
- Der Verwaltungsport ist über SSL mit dem Unternehmensnetzwerk verbunden.

## <a name="checklist"></a>Checkliste

In einem Diagramm mit einem Überblick über ein Industrienetzwerk können die Standorttechniker den richtigen Standort für Azure Defender für IoT-Geräte bestimmen.

### <a name="1-global-network-diagram"></a>1. Globales Netzwerkdiagramm

Das globale Netzwerkdiagramm zeigt die industrielle OT-Umgebung.

:::image type="content" source="media/resources-sensor-deployment-checklist/purdue-model.png" alt-text="Hier fügt sich der Azure Defender für IoT-Sensor in die Einrichtung ein.":::

:::image type="content" source="media/resources-sensor-deployment-checklist/backbone-switch.png" alt-text="Hier fügt sich der Azure Defender für IoT-Sensor in das Purdue-Modell ein.":::

> [!Note] 
> Die Defender für IoT-Appliance sollte mit einem Switch auf niedrigerer Ebene verbunden sein, der den Datenverkehr zwischen den Ports auf dem Switch sehen kann. 

### <a name="2-committed-devices"></a>2. Committete Geräte

Geben Sie die ungefähre Anzahl von Netzwerkgeräten an, die überwacht werden. Sie benötigen diese Information, wenn Sie für Ihr Abonnement das Onboarding im Portal für Azure Defender für IoT durchführen. Während des Onboardingprozesses werden Sie aufgefordert, die Geräteanzahl in Inkrementen von 1.000 einzugeben.

### <a name="3-optional-subnet-list"></a>3. (Optional) Subnetzliste 

Geben Sie eine Subnetzliste der Produktionsnetzwerke an.

| **#** | **Subnetzname** | **Beschreibung** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="4-vlans"></a>4. VLANs

Bereitstellen einer VLAN-Liste der Produktionsnetzwerke.

| **#** | **VLAN-Name** | **Beschreibung** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="5-switch-models-and-mirroring-support"></a>5. Switchmodelle und Unterstützung der Spiegelung

Um zu überprüfen, ob die Switches über die Fähigkeit zur Spiegelung von Ports verfügen, geben Sie die Switchmodellnummern an, mit denen sich die Defender für IoT-Plattform verbinden soll.

| **#** | **Switch** | **Modell** | **Unterstützung für Spiegelung des Datenverkehrs (SPAN, RSPAN oder keine)** |
|--|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="6-third-party-switch-management"></a>6. Switchverwaltung von Drittanbietern

Werden die Switches von einem Drittanbieter verwaltet? J oder N 

Falls ja, von wem? __________________________________ 

Wie lautet die Richtlinie des Anbieters? __________________________________ 

### <a name="7-serial-connection"></a>7. Serielle Verbindung

Gibt es Geräte, die über eine serielle Verbindung im Netzwerk kommunizieren? Ja oder Nein 

Falls ja, geben Sie an, mit welchen seriellen Kommunikationsprotokollen: ________________ 

Falls ja, markieren Sie im Netzwerkdiagramm, welche Geräte mit seriellen Protokollen kommunizieren und wo sie sich befinden.

*Hinzufügen Ihres Netzwerkdiagramms mit markierten seriellen Verbindungen.*

### <a name="8-vendors-and-protocols-industrial-equipment"></a>8. Anbieter und Protokolle (Industriegeräte)

Bereitstellen einer Liste der Hersteller und Protokolle der Industrieanlagen. (Optional)

| **#** | **Hersteller** | **Kommunikationsprotokoll** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

Zum Beispiel:

- Siemens

- Rockwell-Automatisieurng – Ethernet oder IP

- Emerson – DeltaV, Ovation

### <a name="9-qos"></a>9. QoS

Für QoS ist die Standardeinstellung des Sensors 1,5 MBit/s. Geben Sie an, ob Sie diesen Wert ändern möchten: ________________ 

   Geschäftseinheit (BU): ________________

### <a name="10-sensor"></a>10. Sensor  

Die Sensor-Appliance ist über einen Netzwerkadapter mit einem Switch-SPAN-Port verbunden. Sie ist zur Verwaltung über einen weiteren dedizierten Netzwerkadapter mit dem Unternehmensnetzwerk des Kunden verbunden.

Geben Sie Zugriffsdetails für das Sensor-NIC an, das mit dem Unternehmensnetzwerk verbunden werden soll: 

| Element | Appliance 1 | Appliance 2 | Appliance 3 |
|--|--|--|--|
| IP-Adresse der Appliance |  |  |  |
| Subnet |  |  |  |
| Standardgateway |  |  |  |
| DNS |  |  |  |
| Hostname |  |  |  |

### <a name="11-idraciloserver-management"></a>11. iDRAC/iLO/Serververwaltung

| Element | Appliance 1 | Appliance 2 | Appliance 3 |
|--|--|--|--|
| IP-Adresse der Appliance |  |  |  |
| Subnet |  |  |  |
| Standardgateway |  |  |  |
| DNS |  |  |  |

### <a name="12-on-premises-management-console"></a>12. Lokale Verwaltungskonsole  

| Element | Aktiv | Passiv (bei der Verwendung von HA) |
|--|--|--|
| IP-Adresse |  |  |
| Subnet |  |  |
| Standardgateway |  |  |
| DNS |  |  |

### <a name="13-snmp"></a>13. SNMP  

| Element | Details |
|--|--|
| IP |  |
| IP-Adresse |  |
| Username |  |
| Kennwort |  |
| Authentifizierungsart | MD5 oder SHA |
| Verschlüsselung | DES oder AES |
| Geheimer Schlüssel |  |
| SNMP v2-Community String |

### <a name="14-ssl-certificate"></a>14. SSL-Zertifikat

Planen Sie die Verwendung eines SSL-Zertifikats? Ja oder Nein

Wenn ja, welchen Dienst verwenden Sie, um es zu generieren? Welche Attribute nehmen Sie in das Zertifikat auf (z. B. Domäne oder IP-Adresse)?

### <a name="15-smtp-authentication"></a>15. SMTP-Authentifizierung

Planen Sie die Verwendung von SMTP zum Weiterleiten von Benachrichtigungen an einen E-Mail-Server? Ja oder Nein

Falls ja, welche Authentifizierungsmethode möchten Sie verwenden?  

### <a name="16-active-directory-or-local-users"></a>16. Active Directory oder lokale Benutzer

Wenden Sie sich an einen Active Directory-Administrator, um eine Active Directory-Standortbenutzergruppe zu erstellen, oder erstellen Sie lokale Benutzer. Stellen Sie sicher, dass Ihre Benutzer auf den Bereitstellungstag vorbereitet werden.

### <a name="17-iot-device-types-in-the-network"></a>17. IoT-Gerätetypen im Netzwerk

| Gerätetyp | Anzahl der Geräte im Netzwerk | Durchschnittliche Bandbreite |
|--|--|--|
| Ex. Kamera |  |
| Beispiel Röntgengerät |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |

## <a name="next-steps"></a>Nächste Schritte

[Informationen zur Netzwerkeinrichtung von Azure Defender für IoT](how-to-set-up-your-network.md)

[Informationen zur Installation von Defender für IoT](how-to-install-software.md)
