---
title: Integrieren von NPS in die RADIUS-Authentifizierung von VPN Gateway für MFA
titleSuffix: Azure VPN Gateway
description: Erfahren Sie mehr über das Integrieren der RADIUS-Authentifizierung für das Azure-VPN-Gateway in den NPS-Server für Multi-Factor Authentication.
services: vpn-gateway
author: ahmadnyasin
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 6fada4ca0ae24c2f3b859e02c55c7406cc44bb51
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734955"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrieren der RADIUS-Authentifizierung für das Azure-VPN-Gateway in den NPS-Server für Multi-Factor Authentication 

In diesem Artikel wird beschrieben, wie Sie den Netzwerkrichtlinienserver (Network Policy Server, NPS) in die RADIUS-Authentifizierung für das Azure-VPN-Gateway integrieren, um Multi-Factor Authentication (MFA) für Point-to-Site-VPN-Verbindungen bereitzustellen. 

## <a name="prerequisite"></a>Voraussetzung

Zum Aktivieren von MFA müssen die Benutzer in Azure Active Directory (Azure AD) enthalten sein. Der Azure AD-Dienst muss über die lokale Umgebung oder die Cloudumgebung synchronisiert werden. Darüber hinaus muss der Benutzer bereits die automatische Registrierung für MFA abgeschlossen haben.  Weitere Informationen finden Sie unter [Einrichten meines Kontos für die zweistufige Überprüfung](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)

## <a name="detailed-steps"></a>Ausführliche Schritte

### <a name="step-1-create-a-virtual-network-gateway"></a>Schritt 1: Erstellen eines Gateways des virtuellen Netzwerks

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Wählen Sie im virtuellen Netzwerk, in dem das Gateway des virtuellen Netzwerks gehostet wird, **Subnetze** und dann **Gatewaysubnetz** aus, um ein Subnetz zu erstellen. 

    ![Abbildung zum Hinzufügen eines Gatewaysubnetzes](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Erstellen Sie ein Gateway des virtuellen Netzwerks, indem Sie die folgenden Einstellungen angeben:

    - **Gatewaytyp**: Wählen Sie **VPN** aus.
    - **VPN-Typ**: Wählen Sie **Routenbasiert** aus.
    - **SKU**: Wählen Sie entsprechend Ihren Anforderungen einen SKU-Typ aus.
    - **Virtuelles Netzwerk**: Wählen Sie das virtuelle Netzwerk aus, in dem Sie das Gatewaysubnetz erstellt haben.

        ![Abbildung zu den Einstellungen für das Gateway des virtuellen Netzwerks](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-ad-mfa"></a>Schritt 2: Konfigurieren des NPS für Azure AD MFA

1. [Installieren Sie auf dem NPS-Server die NPS-Erweiterung für Azure AD MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Öffnen Sie die NPS-Konsole, klicken Sie mit der rechten Maustaste auf **RADIUS-Clients**, und wählen Sie dann **Neu** aus. Erstellen Sie den RADIUS-Client unter Angabe der folgenden Einstellungen:

    - **Anzeigename**: Geben Sie einen beliebigen Namen ein.
    - **Adresse (IP oder DNS)** : Geben Sie das Gatewaysubnetz ein, das Sie in Schritt 1 erstellt haben.
    - **Gemeinsamer geheimer Schlüssel**: Geben Sie einen beliebigen geheimen Schlüssel ein, und merken Sie sich den Schlüssel zur späteren Verwendung.

      ![Abbildung zu den RADIUS-Clienteinstellungen](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Legen Sie auf der Registerkarte **Erweitert** den Herstellernamen auf **RADIUS-Standard** fest, und stellen Sie sicher, dass das Kontrollkästchen **Zusätzliche Optionen** nicht aktiviert ist.

    ![Abbildung zu den erweiterten RADIUS-Clienteinstellungen](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Wechseln Sie zu **Richtlinien** > **Netzwerkrichtlinien**, doppelklicken Sie auf die Richtlinie **Verbindungen mit Microsoft-Routing- und Remotezugriffsserver**, wählen Sie **Zugriff gewähren** aus, und klicken Sie dann auf **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Schritt 3: Konfigurieren des Gateways des virtuellen Netzwerks

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Öffnen Sie das Gateway des virtuellen Netzwerks, das Sie erstellt haben. Stellen Sie sicher, dass der Gatewaytyp auf **VPN** festgelegt ist und als VPN-Typ **Routenbasiert** ausgewählt ist.
3. Klicken Sie auf **Punkt-zu-Standort-Konfiguration** > **Jetzt konfigurieren**, und geben Sie dann die folgenden Einstellungen an:

    - **Adresspool**: Geben Sie das Gatewaysubnetz ein, das Sie in Schritt 1 erstellt haben.
    - **Authentifizierungstyp**: Wählen Sie **RADIUS-Authentifizierung** aus.
    - **IP-Adresse des Servers**: Geben Sie die IP-Adresse des NPS-Servers ein.

      ![Abbildung zu den Punkt-zu-Standort-Einstellungen](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Nächste Schritte

- [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)
- [Integrieren Ihrer vorhandenen NPS-Infrastruktur in Azure AD Multi-Factor Authentication – Public Preview](../active-directory/authentication/howto-mfa-nps-extension.md)