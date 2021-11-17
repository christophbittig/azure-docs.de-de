---
title: 'Azure VMware Solution by CloudSimple: Konfigurieren von Hochverfügbarkeit zwischen einem lokalen Standort und einem CloudSimple VPN-Gateway'
description: Beschreibt, wie eine Hochverfügbarkeitsverbindung zwischen einer lokalen Umgebung und einem CloudSimple VPN-Gateway konfiguriert wird, das für Hochverfügbarkeit ausgelegt ist.
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d9996b150bd07c50082db7b3bedbac04b90559c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305748"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Konfigurieren einer Hochverfügbarkeitsverbindung zwischen einem lokalen Standort und einem CloudSimple VPN-Gateway

Netzwerkadministratoren können eine hochverfügbare IPsec-Site-to-Site-VPN-Verbindung aus ihrer lokalen Umgebung mit einem CloudSimple VPN-Gateway konfigurieren.

In diesem Leitfaden werden Schritte zur Konfiguration einer lokalen Firewall für eine IPsec-Site-to-Site-VPN-Hochverfügbarkeitsverbindung beschrieben. Die detaillierten Schritte sind spezifisch für den Typ der lokalen Firewall. In diesem Leitfaden werden beispielhaft Schritte für zwei Firewalltypen beschrieben: Cisco ASA und Palo Alto Networks.

## <a name="before-you-begin"></a>Voraussetzungen

Führen Sie die folgenden Aufgaben aus, bevor Sie die lokale Firewall konfigurieren.

1. Vergewissern Sie sich, dass Ihre Organisation die erforderlichen Knoten [bereitgestellt](create-nodes.md) und mindestens eine private CloudSimple-Cloud erstellt hat.
2. [Konfigurieren Sie ein Site-to-Site-VPN-Gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) zwischen Ihrem lokalen Netzwerk und Ihrer privaten CloudSimple-Cloud.

Weitere Informationen zu unterstützten Vorschlägen für Phase 1 und Phase 2 finden Sie unter [Übersicht über VPN-Gateways](cloudsimple-vpn-gateways.md).

## <a name="configure-on-premises-cisco-asa-firewall"></a>Konfigurieren einer lokalen Cisco ASA-Firewall

Die Anleitungen in diesem Abschnitt gelten für Cisco ASA, Version 8.4 oder höher. Im Konfigurationsbeispiel wird Cisco Adaptive Security Appliance Software Version 9.10 im IKEv1-Modus bereitgestellt und konfiguriert.

Damit das Site-to-Site-VPN funktioniert, müssen Sie UDP 500/4500 und ESP (IP-Protokoll 50) aus der primären und sekundären öffentlichen CloudSimple-IP-Adresse (Peer-IP) für die externe Schnittstelle des lokalen Cisco ASA VPN-Gateways zulassen.

### <a name="1-configure-phase-1-ikev1"></a>1. Konfigurieren von Phase 1 (IKEv1)

Um Phase 1 (IKEv1) für die externe Schnittstelle zu aktivieren, geben Sie den folgenden CLI-Befehl in die Cisco ASA-Firewall ein.

`crypto ikev1 enable outside`

### <a name="2-create-an-ikev1-policy"></a>2. Erstellen einer IKEv1-Richtlinie

Erstellen Sie eine IKEv1-Richtlinie, die die Algorithmen und Methoden definiert, die für Hashing, Authentifizierung, Diffie-Hellman-Gruppe, Lebensdauer und Verschlüsselung verwendet werden sollen.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Erstellen einer Tunnelgruppe

Erstellen Sie eine Tunnelgruppe unter den IPSec-Attributen. Konfigurieren Sie die IP-Peeradresse und den vorinstallierten Schlüssel für den Tunnel, den Sie beim [Konfigurieren des Site-to-Site-VPN-Gateways](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) festlegen.

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Konfigurieren von Phase 2 (IPsec)

Erstellen Sie zum Konfigurieren von Phase 2 (IPSec) eine Zugriffssteuerungsliste (Access Control List, ACL), die den zu verschlüsselnden Datenverkehr im Tunnel definiert. Im folgenden Beispiel stammt der relevante Datenverkehr aus dem Tunnel, dessen Quelle das lokale Subnetz (10.16.1.0/24) und dessen Ziel das Remotesubnetz der privaten Cloud (192.168.0.0/24) ist. Die ACL kann mehrere Einträge enthalten, wenn zwischen den Standorten mehrere Subnetze vorhanden sind.

In den Cisco ASA (Version 8.4 oder höher) können Objekte oder Objektgruppen erstellt werden, die als Container für die Netzwerke, Subnetze, IP-Hostadressen oder mehrere Objekte fungieren. Erstellen Sie ein-Objekt für das lokale Subnetz und ein-Objekt für die Remotesubnetze, und verwenden Sie diese für die kryptografische ACL und die NAT-Anweisungen.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Definieren eines lokalen Subnetzes als Objekt

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Definieren des CloudSimple-Remotesubnetzes als Objekt

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Konfigurieren einer Zugriffsliste für den relevanten Datenverkehr

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Konfigurieren der Transformationsgruppe

Konfigurieren Sie die Transformationsgruppe (TS), die das Schlüsselwort ```ikev1``` einschließen muss. Die in der Transformationsgruppe angegebenen Verschlüsselungs- und Hashattribute müssen mit den Parametern übereinstimmen, die in der [Standardkonfiguration für CloudSimple VPN-Gateways](cloudsimple-vpn-gateways.md) aufgeführt sind.

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Konfigurieren der kryptografischen Zuordnung

Konfigurieren Sie die kryptografische Zuordnung, die die folgenden Komponenten enthält:

* IP-Peeradresse
* Definierte ACL, die den relevanten Datenverkehr enthält
* Transformationsgruppe

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. Anwenden der kryptografischen Zuordnung

Wenden Sie die kryptografische Zuordnung auf die externe Schnittstelle an:

`crypto map mymap interface outside`

### <a name="8-confirm-applicable-nat-rules"></a>8. Bestätigen der anwendbaren NAT-Regeln

Die folgende NAT-Regel wird verwendet. Stellen Sie sicher, dass der VPN-Datenverkehr keiner anderen NAT-Regel unterliegt.

`nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside`

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Beispielausgabe für ein eingerichtetes IPSec-Site-to-Site-VPN von Cisco ASA

Ausgabe der Phase 1:

![Ausgabe der Phase 1 für die Cisco ASA-Firewall](media/ha-vpn-connection-cisco-phase1.png)

Ausgabe der Phase 2:

![Ausgabe der Phase 2 für die Cisco ASA-Firewall](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Konfigurieren einer lokalen Palo Alto Networks-Firewall

Die Anleitungen in diesem Abschnitt gelten für Palo Alto Networks, Version 7.1 oder höher. In diesem Konfigurationsbeispiel wird Palo Alto Networks VM-Series Software Version 8.1.0 im IKEv1-Modus bereitgestellt und konfiguriert.

Damit das Site-to-Site-VPN funktioniert, müssen Sie UDP 500/4500 und ESP (IP-Protokoll 50) aus der primären und sekundären öffentlichen CloudSimple-IP-Adresse (Peer-IP) für die externe Schnittstelle des lokalen Palo Alto Networks-Gateways zulassen.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Erstellen der primären und sekundären Tunnelschnittstellen

Melden Sie sich bei der Palo Alto-Firewall an, wählen Sie **Network (Netzwerk)**  > **Interfaces (Schnittstellen)**  > **Tunnel** > **Add (Hinzufügen)** aus, konfigurieren Sie die folgenden Felder, und klicken Sie dann auf **OK**.

* Interface Name (Schnittstellenname). Das erste Feld wird automatisch mit dem Schlüsselwort „tunnel“ aufgefüllt. Geben Sie im angrenzenden Feld eine beliebige Zahl zwischen 1 und 9.999 ein. Diese Schnittstelle wird als primäre Tunnelschnittstelle verwendet, um Site-to-Site-Datenverkehr zwischen dem lokalen Rechenzentrum und der privaten Cloud zu übertragen.
* Comment (Kommentar). Geben Sie Kommentare ein, um den Zweck des Tunnels leicht identifizieren zu können.
* NetFlow Profile (NetFlow-Profil). Behalten Sie den Standardwert bei.
* Config.Schnittstelle zuweisen zu: Virtueller Router: Wählen Sie **Standard** aus. 
        Sicherheitszone: Wählen Sie die Zone für vertrauenswürdigen LAN-Datenverkehr aus. In diesem Beispiel lautet der Name der Zone für den LAN-Datenverkehr „Trust“.
* IPv4. Klicken Sie auf **Add** (Hinzufügen), und fügen Sie eine beliebige nicht überlappende /32-IP-Adresse in Ihrer Umgebung hinzu, die der primären Tunnelschnittstelle zugewiesen und zum Überwachen der Tunnel verwendet wird (dies wird später erläutert).

Da diese Konfiguration für ein Hochverfügbarkeits-VPN gilt, sind zwei Tunnelschnittstellen erforderlich: eine primäre und eine sekundäre. Wiederholen Sie die vorherigen Schritte, um die sekundäre Tunnelschnittstelle zu erstellen. Wählen Sie eine andere Tunnel-ID und eine andere nicht verwendete /32-IP-Adresse aus.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Einrichten von statischen Routen für Subnetze der privaten Cloud, die über das Site-to-Site-VPN erreicht werden sollen

Routen sind erforderlich, damit die lokalen Subnetze die Subnetze der privaten CloudSimple-Cloud erreichen können.

Wählen Sie **Network (Netzwerk)**  > **Virtual Routers (Virtuelle Router)**  > *default (Standard)*  > **Static Routes (Statische Routen)**  > **Add (Hinzufügen)** aus, konfigurieren Sie die folgenden Felder, und klicken Sie dann auf **OK**.

* Name. Geben Sie einen beliebigen Namen ein, um den Zweck der Route leicht identifizieren zu können.
* Destination. Geben Sie die Subnetze der privaten CloudSimple-Cloud an, die über S2S-Tunnelschnittstellen vom lokalen Standort aus erreicht werden sollen.
* Schnittstelle Wählen Sie in der Dropdownliste die primäre Tunnelschnittstelle aus, die Sie in Schritt-1 (Abschnitt 2) erstellt haben. In diesem Beispiel handelt es sich um „tunnel.20“.
* Next Hop (Nächster Hop). Wählen Sie **Keine** aus.
* Admin Distance (Administratorabstand). Behalten Sie den Standardwert bei.
* Metric (Metrik). Geben Sie einen beliebigen Wert zwischen 1 und 65.535 ein. Der Schlüssel besteht darin, eine niedrigere Metrik für die Route einzugeben, die der primären Tunnelschnittstelle entspricht, verglichen mit der Metrik der Route, die der sekundären Tunnelschnittstelle entspricht, wodurch die erstgenannte Route bevorzugt wird. Wenn „tunnel.20“ einen Metrikwert von 20 gegenüber einem Metrikwert von 30 für „tunnel.30“ aufweist, wird „tunnel.20“ bevorzugt.
* Route Table (Routingtabelle). Behalten Sie den Standardwert bei.
* BFD Profile (BFD-Profil). Behalten Sie den Standardwert bei.
* Path monitoring (Pfadüberwachung). Lassen Sie die Option deaktiviert.

Wiederholen Sie die vorherigen Schritte, um eine weitere Route für Subnetze der privaten Cloud zu erstellen, die als sekundäre/Sicherungsroute über die sekundäre Tunnelschnittstelle verwendet werden kann. Wählen Sie dieses Mal eine andere Tunnel-ID und eine höhere Metrik als für die primäre Route aus.

### <a name="3-define-the-cryptographic-profile"></a>3. Definieren des kryptografischen Profils

Definieren Sie ein kryptografisches Profil, das die Protokolle und Algorithmen für die Identifizierung, Authentifizierung und Verschlüsselung angibt, die zum Einrichten von VPN-Tunneln in IKEv1 Phase 1 verwendet werden sollen.

Wählen Sie **Network (Netzwerk)**  > **Expand Network Profiles (Netzwerkprofile erweitern)**  > **IKE Crypto** > **Add (Hinzufügen)** aus, konfigurieren Sie die folgenden Felder, und klicken Sie dann auf **OK**.

* Name. Geben Sie einen beliebigen Namen des kryptografisch IKE-Profils ein.
* DH Group (DH-Gruppe). Klicken Sie auf **Add** (Hinzufügen), und wählen Sie die entsprechende DH-Gruppe aus.
* Verschlüsselung: Klicken Sie auf **Add** (Hinzufügen), und wählen Sie die entsprechende Verschlüsselungsmethode aus.
* Authentifizierung Klicken Sie auf **Add** (Hinzufügen), und wählen Sie die entsprechende Authentifizierungsmethode aus.
* Key lifetime (Lebensdauer des Schlüssels). Behalten Sie den Standardwert bei.
* IKEv2 Authentication Multiple (IKEv2-Authentifizierung mehrfach). Behalten Sie den Standardwert bei.

### <a name="4-define-ike-gateways"></a>4. Definieren von IKE-Gateways

Definieren Sie IKE-Gateways, um die Kommunikation zwischen den Peers an jedem Ende des VPN-Tunnels einzurichten.

Wählen Sie **Network (Netzwerk)**  > **Expand Network Profiles (Netzwerkprofile erweitern)**  > **IKE Gateways** > **Add (Hinzufügen)** aus, konfigurieren Sie die folgenden Felder, und klicken Sie dann auf **OK**.

Registerkarte „General“ (Allgemein):

* Name. Geben Sie den Namen für das IKE-Gateway ein, für das Peering mit dem primären CloudSimple-VPN-Peer ausgeführt werden soll.
* Version. Wählen Sie **IKEv1 only mode** (Nur IKEv1-Modus) aus.
* Address Type (Adresstyp). Wählen Sie **IPv4** aus.
* Schnittstelle Wählen Sie die öffentliche oder externe Schnittstelle aus.
* Local IP Address (Lokale IP-Adresse). Behalten Sie den Standardwert bei.
* Peer IP Address Type (Typ der IP-Peeradresse). Wählen Sie **IP** aus.
* Peer Address (Peeradresse). Geben Sie die primäre CloudSimple-VPN-IP-Peeradresse ein.
* Authentifizierung Wählen Sie **Pre-Shared Key** (Vorinstallierter Schlüssel) aus.
* Pre-shared Key/Confirm Pre-shared Key (Vorinstallierter Schlüssel/Vorinstallierten Schlüssel bestätigen). Geben Sie den vorinstallierten Schlüssel ein, der dem Schlüssel des CloudSimple-VPN-Gateways entspricht.
* Local Identification (Lokale Identifikation). Geben Sie die öffentliche IP-Adresse der lokalen Palo Alto-Firewall ein.
* Peer Identification (Peeridentifikation). Geben Sie die primäre CloudSimple-VPN-IP-Peeradresse ein.

Registerkarte „Advanced Options“ (Erweiterte Optionen):

* Enable Passive Mode (Passiven Modus aktivieren). Lassen Sie die Option deaktiviert.
* Enable NAT Traversal (NAT Traversal aktivieren). Lassen Sie die Option deaktiviert, wenn sich die lokale Palo Alto-Firewall nicht hinter einem NAT-Gerät befindet. Andernfalls aktivieren Sie das Kontrollkästchen.

IKEv1:

* Exchange Mode (Austauschmodus). Wählen Sie **main** aus.
* IKE Crypto Profile (Kryptografische IKE-Profil). Wählen Sie das zuvor erstellte kryptografische IKE-Profil aus. Lassen Sie das Kontrollkästchen „Enable Fragmentation“ (Fragmentierung aktivieren) deaktiviert.
* Dead Peer Detection (Erkennung eines nicht aktiven Peers). Lassen Sie das Kontrollkästchen deaktiviert.

Wiederholen Sie die vorherigen Schritte, um das sekundäre IKE-Gateway zu erstellen.

### <a name="5-define-ipsec-crypto-profiles"></a>5. Definieren der kryptografischen IPSEC-Profile

Wählen Sie **Network (Netzwerk)**  > **Expand Network Profiles (Netzwerkprofile erweitern)**  > **IPSEC Crypto** > **Add (Hinzufügen)** aus, konfigurieren Sie die folgenden Felder, und klicken Sie dann auf **OK**.

* Name. Geben Sie einen Namen für das kryptografische IPSec-Profil ein.
* IPsec Protocol (IPSec-Protokoll). Wählen Sie **ESP** aus.
* Verschlüsselung: Klicken Sie auf **Add** (Hinzufügen), und wählen Sie die entsprechende Verschlüsselungsmethode aus.
* Authentifizierung Klicken Sie auf **Add** (Hinzufügen), und wählen Sie die entsprechende Authentifizierungsmethode aus.
* DH Group (DH-Gruppe). Wählen Sie **no-pfs** aus.
* Lifetime (Lebensdauer). Legen Sie diesen Wert auf 30 Minuten fest.
* Enable (Aktivieren). Lassen Sie das Kontrollkästchen deaktiviert.

Wiederholen Sie die vorherigen Schritte, um ein weiteres kryptografisches IPSec-Profil zu erstellen, das als sekundärer CloudSimple-VPN-Peer verwendet wird. Das gleiche kryptografische IPSec-Profil kann auch für die primären und sekundären IPSec-Tunnel verwendet werden (siehe folgendes Verfahren).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Definieren von Überwachungsprofilen für die Tunnelüberwachung

Wählen Sie **Network (Netzwerk)**  > **Expand Network Profiles (Netzwerkprofile erweitern)**  > **Monitor (Überwachen)**  > **Add (Hinzufügen)** aus, konfigurieren Sie die folgenden Felder, und klicken Sie dann auf **OK**.

* Name. Geben Sie einen beliebigen Namen für das Überwachungsprofil ein, das für die Tunnelüberwachung für proaktiven Reaktionen auf den Fehler verwendet werden soll.
* Action (Aktion). Wählen Sie **Fail Over** aus.
* Interval (Intervall). Geben Sie den Wert **3** ein.
* Threshold (Schwellenwert). Geben Sie den Wert **7** ein.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Einrichten der primären und sekundären IPSec-Tunnel.

Wählen Sie **Network (Netzwerk)**  > **IPsec Tunnels (IPSEC-Tunnel)**  > **Add (Hinzufügen)** aus, konfigurieren Sie die folgenden Felder, und klicken Sie dann auf **OK**.

Registerkarte „General“ (Allgemein):

* Name. Geben Sie einen beliebigen Namen für den primären IPSEC-Tunnel ein, für den Peering mit dem primären CloudSimple-VPN-Peer ausgeführt werden soll.
* Tunnel Interface (Tunnelschnittstelle). Wählen Sie die primäre Tunnelschnittstelle aus.
* Type (Typ). Behalten Sie den Standardwert bei.
* Address Type (Adresstyp). Wählen Sie **IPv4** aus.
* IKE Gateway. Wählen Sie das primäre IKE-Gateway aus.
* IPsec Crypto Profile (Kryptografisches IPSEC-Profil). Wählen Sie das primäre IPSec-Profil aus. Wählen Sie **Show Advanced Options** (Erweiterte Optionen anzeigen) aus.
* Enable Replay Protection (Wiedergabeschutz aktivieren). Behalten Sie den Standardwert bei.
* Copy TOS Header (TOS-Header kopieren). Lassen Sie das Kontrollkästchen deaktiviert.
* Tunnel Monitor (Tunnelüberwachung). Aktivieren Sie das Kontrollkästchen.
* Destination IP (Ziel-IP). Geben Sie eine beliebige IP-Adresse ein, die zum Subnetz der privaten CloudSimple-Cloud gehört, die über die Site-to-Site-Verbindung zulässig ist. Stellen Sie sicher, dass die Tunnelschnittstellen (z.B. „tunnel.20 - 10.64.5.2/32“ and „tunnel.30 - 10.64.6.2/32“) für Palo Alto die IP-Adresse der privaten CloudSimple-Cloud über das Site-to-Site-VPN erreichen dürfen. Die folgende Konfiguration zeigt dies für Proxy-IDs.
* Profil. Wählen Sie das Überwachungsprofil aus.

Registerkarte „Proxy-IDs“: Klicken Sie auf **IPv4** > **Hinzufügen**, und konfigurieren Sie folgende Option:

* Proxy ID. Geben Sie einen beliebigen Namen für den relevanten Datenverkehr ein. Es können mehrere Proxy-IDs in einen IPSec-Tunnel übertragen werden.
* Lokal. Geben Sie die lokalen Subnetze an, die über das Site-to-Site-VPN mit Subnetzen der privaten Cloud kommunizieren dürfen.
* Remote. Geben Sie die Remote-subnetze der privaten Cloud an, die mit den lokalen Subnetzen kommunizieren dürfen.
* Protocol (Protokoll). Wählen Sie **any** (Beliebig) aus.

Wiederholen Sie die vorherigen Schritte, um einen weiteren IPsec-Tunnel zu erstellen, der als sekundärer CloudSimple-VPN-Peer verwendet wird.

## <a name="references"></a>References

Konfigurieren von NAT für Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Konfigurationshandbuch für die Cisco ASA 5500-Serie</a>

Unterstützte IKEv1- und IKEv2-Attribute für Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa72/configuration/guide/conf_gd/ike.html" target="_blank">CLI-Konfigurationshandbuch für die Cisco ASA-Serie</a>

Konfigurieren eines IPSec-Site-to-Site-VPN für Cisco ASA mit Version 8.4 oder höher:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Konfigurieren von IKEv1-IPSec-Site-to-Site-Tunneln mit ASDM oder der CLI für ASA</a>

Konfigurieren von der Cisco Adaptive Security Appliance v (ASAv) in Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Schnellstarthandbuch für Cisco Adaptive Security Virtual Appliance (ASAv)</a>

Konfigurieren des Site-to-Site-VPN mit Proxy-IDs für Palo Alto:

[Einrichten eines Site-to-Site-VPN](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Einrichten der Tunnelüberwachung:

[Einrichten der Tunnel Überwachung](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE-Gateway- oder IPSec-Tunnelvorgänge:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Aktivieren/Deaktivieren, Aktualisieren oder Neustarten eines IKE-Gateways oder IPSec-Tunnels</a>
