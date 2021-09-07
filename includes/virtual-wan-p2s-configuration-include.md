---
author: cherylmc
ms.author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 21bda32ddb1c87bb07b6679499e648e0b2f7a809
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734638"
---
1. Navigieren Sie zu dem virtuellen WAN, das Sie erstellt haben. 

1. Wählen Sie im Menü auf der linken Seite **Benutzer-VPN-Konfigurationen** aus.

1. Wählen Sie auf der Seite **Benutzer-VPN-Konfigurationen** oben auf der Seite die Option **+ Benutzer-VPN-Konfiguration erstellen** aus.

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Screenshot der Seite „Benutzer-VPN-Konfigurationen“":::

1. Geben Sie auf der Seite **Neue Benutzer-VPN-Konfiguration erstellen** auf der Registerkarte **Grundlagen** unter **Instanzdetails** den **Namen** ein, den Sie Ihrer VPN-Konfiguration zuweisen möchten. 

   :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Screenshot des IPsec-Umschalters, der auf „custom“ (benutzerdefiniert) festgelegt ist":::

1. Wählen Sie für **Tunneltyp** über das Dropdownmenü den Tunneltyp aus, den Sie verwenden möchten. Es stehen folgende Optionen für den Tunneltyp zur Verfügung: **IKEv2-VPN, OpenVPN** und **OpenVpn und IKEv2**. Für jeden Tunneltyp sind andere Einstellungen erforderlich.

   **Anforderungen und Parameter**:

     **IKEv2 VPN**

     * **Anforderungen:** Wenn Sie den Tunneltyp **IKEv2** auswählen, wird eine Meldung angezeigt, die Sie auffordert, eine Authentifizierungsmethode auszuwählen. Bei IKEv2 können Sie nur eine Authentifizierungsmethode angeben. Sie können Azure Certificate, Azure Active Directory oder eine RADIUS-basierte Authentifizierung auswählen.

     * **Benutzerdefinierte IPsec-Parameter:** Wenn Sie die Parameter für IKE Phase 1 und IKE Phase 2 anpassen möchten, legen Sie den IPsec-Umschalter auf **Custom** (Benutzerdefiniert) fest, und wählen Sie die Parameterwerte aus. Weitere Informationen zu anpassbaren Parametern finden Sie im Artikel zu den [Benutzerdefinierten IPsec-Richtlinien](../articles/virtual-wan/point-to-site-ipsec.md).

     **OpenVPN**

     * **Anforderungen:** Wenn Sie den **OpenVPN**-Tunneltyp auswählen, wird eine Meldung angezeigt, die Sie auffordert, einen Authentifizierungsmechanismus auszuwählen. Wenn OpenVPN als Tunneltyp ausgewählt ist, können Sie mehrere Authentifizierungsmethoden angeben. Sie können eine beliebige Teilmenge von Azure Certificate, Azure Active Directory oder eine RADIUS-basierte Authentifizierung auswählen. Bei der RADIUS-basierten Authentifizierung können Sie eine sekundäre IP-Adresse des RADIUS-Servers und ein Servergeheimnis bereitstellen.

1. Konfigurieren Sie die gewünschten **Authentifizierungsmethoden**. Jede Authentifizierungsmethode befindet sich auf einer separaten Registerkarte: **Azure-Zertifikat**, **RADIUS-Authentifizierung** und **Azure Active Directory**. Einige Authentifizierungsmethoden sind nur für bestimmte Tunneltypen verfügbar.

   Wählen Sie auf der Registerkarte für die zu konfigurierende Authentifizierungsmethode **Ja** aus, um die verfügbaren Konfigurationseinstellungen anzuzeigen.

   :::image type="content" source="media/virtual-wan-p2s-configuration/certificate-no.png" alt-text="Screenshot: Bildschirm mit hervorgehobener Option „Ja“":::

   * **Beispiel: Zertifikatauthentifizierung**

      :::image type="content" source="media/virtual-wan-p2s-configuration/certificate-authentication.png" alt-text="Screenshot: Ausgewählte Option „Ja“":::

   * **Beispiel: RADIUS-Authentifizierung**

      :::image type="content" source="media/virtual-wan-p2s-configuration/radius-authentication.png" alt-text="Screenshot: Seite für RADIUS-Authentifizierung":::

   * **Beispiel: Azure Active Directory-Authentifizierung**

      :::image type="content" source="media/virtual-wan-p2s-configuration/azure-active-directory.png" alt-text="Seite für Azure Active Directory-Authentifizierung":::

1. Wenn Sie die Konfiguration der Einstellungen abgeschlossen haben, klicken Sie unten auf der Seite auf **Überprüfen + erstellen**.

1. Klicken Sie auf **Erstellen**, um die Benutzer-VPN-Konfiguration zu erstellen.
