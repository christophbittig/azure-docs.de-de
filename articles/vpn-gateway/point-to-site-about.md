---
title: Informationen zu Azure-Point-to-Site-VPN-Verbindungen
titleSuffix: Azure VPN Gateway
description: Erfahren Sie mehr über Point-to-Site-VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 07/28/2021
ms.author: cherylmc
ms.openlocfilehash: ddcb49b1ce2bd882590b1d85cb634846ef3b4d40
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355056"
---
# <a name="about-point-to-site-vpn"></a>Informationen zu Point-to-Site-VPN

Mit einer P2S-VPN-Gatewayverbindung (Point-to-Site) können Sie von einem einzelnen Clientcomputer aus eine sichere Verbindung mit Ihrem virtuellen Netzwerk herstellen. Eine P2S-Verbindung wird hergestellt, indem Sie die Verbindung vom Clientcomputer aus starten. Diese Lösung ist nützlich für Telearbeiter, die an einem Remotestandort (beispielsweise zu Hause oder in einer Konferenz) eine Verbindung mit Azure-VNETs herstellen möchten. Wenn nur einige wenige Clients eine Verbindung mit einem VNET herstellen müssen, ist ein P2S-VPN (und nicht ein S2S-VPN) ebenfalls eine nützliche Lösung. Dieser Artikel gilt für das [Resource Manager-Bereitstellungsmodell](../azure-resource-manager/management/deployment-models.md).

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Welches Protokoll verwendet P2S?

P2S-VPN kann eins der folgenden Protokolle verwenden:

* **OpenVPN®-Protokoll**, ein auf SSL/TLS basierendes VPN-Protokoll. Eine TLS-VPN-Lösung kann Firewalls durchdringen, da die meisten Firewalls den von TLS verwendeten TCP-Port 443 für ausgehenden Datenverkehr öffnen. OpenVPN kann zum Herstellen einer Verbindung von Android-, iOS- (Version 11.0 und höher), Windows-, Linux- und Mac-Geräten (macOS-Version 10.13 und höher) verwendet werden.

* Secure Socket Tunneling-Protokoll (SSTP), ein proprietäres TLS-basiertes VPN-Protokoll. Eine TLS-VPN-Lösung kann Firewalls durchdringen, da die meisten Firewalls den von TLS verwendeten TCP-Port 443 für ausgehenden Datenverkehr öffnen. SSTP wird nur auf Windows-Geräten unterstützt. Azure unterstützt alle Versionen von Windows, die über SSTP verfügen und TLS 1.2 unterstützen (Windows 8.1 und höher).

* IKEv2-VPN, eine standardbasierte IPsec-VPN-Lösung. IKEv2-VPN kann zum Herstellen einer Verbindung von Mac-Geräten (macOS-Version 10.11 und höher) verwendet werden.


>[!NOTE]
>IKEv2 und OpenVPN für P2S sind ausschließlich für das [Resource Manager-Bereitstellungsmodell](../azure-resource-manager/management/deployment-models.md) verfügbar. Für das klassische Bereitstellungsmodell sind sie nicht verfügbar.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>Wie werden P2S-VPN-Clients authentifiziert?

Bevor Azure eine P2S-VPN-Verbindung akzeptiert, muss zunächst der Benutzer authentifiziert werden. Azure bietet zwei Mechanismen zum Authentifizieren eines Benutzers, der eine Verbindung herstellt.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Authentifizieren mit der nativen Azure-Zertifikatauthentifizierung

Bei Verwendung der nativen Azure-Zertifikatauthentifizierung wird der Benutzer, der eine Verbindung herstellt, mit einem auf dem Gerät vorhandenen Clientzertifikat authentifiziert. Clientzertifikate werden über ein vertrauenswürdiges Stammzertifikat generiert und dann auf jedem Clientcomputer installiert. Sie können ein Stammzertifikat verwenden, das mit einer Unternehmenslösung generiert wurde, oder ein selbstsigniertes Zertifikat generieren.

Die Überprüfung des Clientzertifikats wird vom VPN-Gateway durchgeführt und erfolgt während der Herstellung der P2S-VPN-Verbindung. Das Stammzertifikat ist für die Überprüfung erforderlich und muss in Azure hochgeladen werden.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Authentifizieren mit der nativen Azure Active Directory-Authentifizierung

Die Azure AD-Authentifizierung ermöglicht Benutzern das Herstellen einer Verbindung mit Azure mit ihren Azure Active Directory-Anmeldeinformationen. Die native Azure AD-Authentifizierung wird nur für das OpenVPN-Protokoll und Windows 10 unterstützt und erfordert die Verwendung von [Azure VPN Client](https://go.microsoft.com/fwlink/?linkid=2117554).

Mit der nativen Azure AD-Authentifizierung können Sie den bedingten Zugriff von Azure AD sowie die Multi-Factor Authentication-Funktionen (MFA) für VPN nutzen.

Auf einer hohen Ebene müssen Sie die folgenden Schritte ausführen, um die Azure AD-Authentifizierung zu konfigurieren:

1. [Konfigurieren eines Azure AD-Mandanten](openvpn-azure-ad-tenant.md)

2. [Aktivieren der Azure AD-Authentifizierung im Gateway](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Herunterladen und Konfigurieren von Azure VPN Client](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Authentifizieren mit Active Directory-Domänenserver (AD)

Die AD-Domänenauthentifizierung ermöglicht Benutzern das Herstellen einer Verbindung mit Azure mit ihren Anmeldeinformationen für die Organisationsdomäne. Dafür ist ein RADIUS-Server erforderlich, der in den AD-Server integriert wird. Organisationen können auch ihre vorhandene RADIUS-Bereitstellung nutzen.

Der RADIUS-Server kann lokal oder im Azure-VNET bereitgestellt werden. Während der Authentifizierung fungiert das Azure-VPN-Gateway als Vermittler und leitet Authentifizierungsnachrichten zwischen dem RADIUS-Server und dem Gerät weiter, das eine Verbindung herstellt. Die Erreichbarkeit des Gateways durch den RADIUS-Server ist daher wichtig. Wenn sich der RADIUS-Server in der lokalen Umgebung befindet, ist aus Gründen der Erreichbarkeit eine VPN-Site-to-Site-Verbindung zwischen Azure und dem lokalen Standort erforderlich.

Der RADIUS-Server kann auch in AD-Zertifikatdienste integriert werden. Dadurch können Sie den RADIUS-Server und Ihre Unternehmenszertifikatbereitstellung für die P2S-Zertifikatauthentifizierung als Alternative zur Azure-Zertifikatauthentifizierung verwenden. Der Vorteil besteht darin, dass Sie keine Stammzertifikate und gesperrten Zertifikate in Azure hochladen müssen.

Ein RADIUS-Server kann auch in andere externe Identitätssysteme integriert werden. Dadurch ergeben sich zahlreiche Authentifizierungsoptionen für P2S-VPNs, einschließlich Optionen für die mehrstufige Authentifizierung.

![Diagramm: Point-to-Site-VPN mit einer lokalen Website](./media/point-to-site-about/p2s.png)

## <a name="what-are-the-client-configuration-requirements"></a>Welche Anforderungen an die Clientkonfiguration müssen erfüllt sein?

>[!NOTE]
>Bei Windows-Clients müssen Sie über Administratorrechte auf dem Clientgerät verfügen, um die VPN-Verbindung von dem Clientgerät zu Azure zu initiieren.
>

Benutzer verwenden die nativen VPN-Clients auf Windows- und Mac-Geräten für P2S. Azure bietet eine ZIP-Datei mit der VPN-Clientkonfiguration, die Einstellungen enthält, die diese nativen Clients zum Herstellen einer Verbindung mit Azure benötigen.

* Bei Windows-Geräten besteht die VPN-Clientkonfiguration aus einem Installer-Paket, das Benutzer auf ihren Geräten installieren.
* Bei Mac-Geräten besteht sie aus der Datei „mobileconfig“, die Benutzer auf ihren Geräten installieren.

Die ZIP-Datei enthält zudem die Werte einiger wichtiger Einstellungen in Azure, mit denen Sie ein eigenes Profil für diese Geräte erstellen können. Zu den Werten zählen die VPN-Gatewayadresse, konfigurierte Tunneltypen, Routen und das Stammzertifikat für die Gatewayüberprüfung.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Welche Gateway-SKUs unterstützen P2S-VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Gateway-SKU-Empfehlungen finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Die Basic-SKU unterstützt keine IKEv2- oder RADIUS-Authentifizierung.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Welche IKE/IPsec-Richtlinien werden in VPN-Gateways für P2S konfiguriert?


**IKEv2**

| **Verschlüsselung** | **Integrität** | **PRF** | **DH-Gruppe** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **Verschlüsselung** | **Integrität** | **PFS-Gruppe** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Welche TLS-Richtlinien werden in VPN-Gateways für P2S konfiguriert?
**TLS**

|**Richtlinien** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Wie konfiguriere ich eine P2S-Verbindung?

Eine P2S-Konfiguration erfordert einige bestimmte Schritte. Die folgenden Artikel enthalten die Schritte, anhand derer Sie eine P2S-Konfiguration durchführen können, und Links zur Konfiguration der VPN-Clientgeräte:

* [Konfigurieren einer Point-to-Site-Verbindung unter Verwendung der RADIUS-Authentifizierung](point-to-site-how-to-radius-ps.md)

* [Konfigurieren einer Point-to-Site-Verbindung unter Verwendung der nativen Azure-Zertifikatauthentifizierung](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurieren von OpenVPN](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>Entfernen der Konfiguration einer P2S-Verbindung

Sie können die Konfiguration einer Verbindung mithilfe von PowerShell oder der CLI entfernen. Beispiele finden Sie in den [häufig gestellten Fragen](vpn-gateway-vpn-faq.md#removeconfig).

## <a name="how-does-p2s-routing-work"></a>Wie funktioniert P2S-Routing?

Weitere Informationen finden Sie in folgenden Artikeln:

* [Informationen zu Point-to-Site-VPN-Routing](vpn-gateway-about-point-to-site-routing.md)

* [Ankündigen benutzerdefinierter Routen](vpn-gateway-p2s-advertise-custom-routes.md)

## <a name="faqs"></a>Häufig gestellte Fragen

Es gibt mehrere FAQ-Abschnitte für P2S, die sich auf die Authentifizierung beziehen.

* [Häufig gestellte Fragen: Zertifikatauthentifizierung](vpn-gateway-vpn-faq.md#P2S)

* [Häufig gestellte Fragen: RADIUS-Authentifizierung](vpn-gateway-vpn-faq.md#P2SRADIUS)
 
## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren einer Point-to-Site-Verbindung unter Verwendung der RADIUS-Authentifizierung](point-to-site-how-to-radius-ps.md)

* [Konfigurieren einer Point-to-Site-Verbindung unter Verwendung der Azure-Zertifikatauthentifizierung](vpn-gateway-howto-point-to-site-rm-ps.md)

**„OpenVPN“ ist eine Marke von OpenVPN Inc.**
