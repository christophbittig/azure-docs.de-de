---
title: 'Konfigurieren von VPN-Clients für P2S-OpenVPN-Protokollverbindungen: Azure AD-Authentifizierung: macOS'
description: Erfahren Sie, wie Sie einen macOS-VPN-Client konfigurieren, um über VPN Gateway Point-to-Site und Azure Active Directory-Authentifizierung eine Verbindung mit einem virtuellen Netzwerk herzustellen.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: cherylmc
ms.openlocfilehash: f2b23c469e7ababb9aab439e33cffd2de6e20eae
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514678"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections---macos"></a>Azure Active Directory-Authentifizierung: Konfigurieren eines VPN-Clients für P2S-OpenVPN-Protokollverbindungen: macOS

Dieser Artikel unterstützt Sie beim Konfigurieren eines VPN-Clients für einen Computer mit macOS 10.15 und höher für die Verbindung zu einem virtuellen Netzwerk mit einem Point-to-Site-VPN und Azure Active Directory-Authentifizierung. Damit Sie mit Azure AD eine Verbindung herstellen und eine Authentifizierung durchführen können, müssen Sie zuerst Ihren Azure AD-Mandanten konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren eines Azure AD-Mandanten](openvpn-azure-ad-tenant.md). Weitere Informationen zu Point-to-Site-Verbindungen finden Sie unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md).

> [!NOTE]
> * Die Azure AD-Authentifizierung wird nur für Verbindungen mit dem OpenVPN®-Protokoll unterstützt und erfordert den Azure-VPN-Client.
> * Azure VPN Client ist aufgrund lokaler Bestimmungen möglicherweise nicht in allen Regionen verfügbar.
>

Für jeden Computer, den Sie über eine Point-to-Site-VPN-Verbindung mit einem VNet verbinden möchten, müssen Sie folgende Schritte ausführen:
 
* Laden Sie Azure VPN Client auf den Computer herunter.
* Konfigurieren Sie ein Clientprofil, das die VPN-Einstellungen enthält. 

Wenn Sie mehrere Computer konfigurieren möchten, können Sie ein Clientprofil auf einem Computer erstellen, exportieren und dann auf andere Computer importieren.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie mit Azure AD eine Verbindung herstellen und eine Authentifizierung durchführen können, müssen Sie zuerst Ihren Azure AD-Mandanten konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren eines Azure AD-Mandanten](openvpn-azure-ad-tenant.md).

## <a name="to-download-the-azure-vpn-client"></a><a name="download"></a>So laden Sie Azure VPN Client herunter

1. Laden Sie [Azure VPN Client](https://apps.apple.com/us/app/azure-vpn-client/id1553936137) aus dem Apple Store herunter.
1. Installieren Sie den Client auf Ihrem Computer.

## <a name="to-import-a-connection-profile"></a><a name="import"></a>So importieren Sie ein Verbindungsprofil

1. Laden Sie die Profildateien herunter und extrahieren Sie sie. Informationen zu den erforderlichen Schritten finden Sie unter [Arbeiten mit VPN-Clientprofildateien](about-vpn-profile-download.md).
1. Wählen Sie auf der Azure VPN Client-Seite die Option zum **Importieren** aus.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-1.png" alt-text="Screenshot: Auswählen der Importoption von Azure VPN Client":::
1. Navigieren Sie zu der Profildatei, die Sie importieren möchten, wählen Sie sie aus, und klicken Sie dann auf **Öffnen**.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-2.png" alt-text="Screenshot: Auswählen von „Öffnen“ für die Importoption von Azure VPN Client":::
1. Zeigen Sie die Verbindungsprofilinformationen an, und klicken Sie dann auf **Speichern**.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-3.png" alt-text="Screenshot: Azure VPN Client speichert importierte Profileinstellungen":::
1. Wählen Sie im Bereich der VPN-Verbindungen das von Ihnen gespeicherte Verbindungsprofil aus. Klicken Sie dann auf **Verbinden**.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-4.png" alt-text="Screenshot: Auswählen von „Verbinden“ in Azure VPN Client":::
1. Sobald die Verbindung hergestellt wurde, ändert sich der Status in **Verbunden**. Um die Verbindung mit der Sitzung zu trennen, klicken Sie auf **Trennen**.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-5.png" alt-text="Screenshot: Azure VPN Client-Status „Verbunden“ und die Schaltfläche „Trennen“":::

## <a name="to-create-a-connection-manually"></a><a name="manual"></a>So erstellen Sie manuell eine Verbindung

1. Öffnen Sie Azure VPN Client. Wählen Sie zum Erstellen einer Verbindung die Option **Hinzufügen** aus.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-1.png" alt-text="Screenshot: Auswählen der Option „Hinzufügen“ von Azure VPN Client":::

1. Auf der Seite **Azure VPN Client** können Sie die Profileinstellungen konfigurieren.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-2.png" alt-text="Screenshot: Azure VPN Client-Profileinstellungen":::

   Konfigurieren Sie die folgenden Einstellungen:

   * **Name der Verbindung:** Der Name, mit dem Sie auf das Verbindungsprofil verweisen möchten.
   * **VPN-Server:** Dies ist der Name, den Sie verwenden möchten, um auf den Server zu verweisen. Der Name, den Sie hier auswählen, muss nicht der offizielle Name eines Servers sein.
   * **Servervalidierung**
     * **Zertifikatinformationen:** Die Zertifizierungsstelle des Zertifikats.
     * **Servergeheimnis:** Das Servergeheimnis.
   * **Clientauthentifizierung**
     * **Authentifizierungstyp:** Azure Active Directory.
     * **Mandant:** Der Name des Mandanten.
     * **Aussteller:** Der Name des Ausstellers.
1. Klicken Sie nach dem Ausfüllen der Felder auf **Speichern**.
1. Wählen Sie im Bereich der VPN-Verbindungen das von Ihnen konfigurierte Verbindungsprofil aus. Klicken Sie dann auf **Verbinden**.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-3.png" alt-text="Screenshot: Herstellen der Verbindung mit Azure VPN Client":::
1. Melden Sie sich mit Ihren Anmeldeinformationen an, um eine Verbindung herzustellen.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-4.png" alt-text="Screenshot: Azure VPN Client-Anmeldung zum Herstellen einer Verbindung":::
1. Nach dem Herstellen der Verbindung wird der Status **Verbunden** angezeigt. Wenn Sie die Verbindung trennen möchten, klicken Sie auf **Trennen**, um die Verbindung zu trennen.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-5.png" alt-text="Screenshot: Verbundene Azure VPN Client-Instanz und die Schaltfläche „Trennen“":::

## <a name="to-remove-a-connection-profile"></a><a name="remove"></a>So entfernen Sie ein Verbindungsprofil

Sie können das VPN-Verbindungsprofil von Ihrem Computer entfernen. 

1. Navigieren Sie zu Azure VPN Client.
1. Wählen Sie die zu entfernende VPN-Verbindung aus, klicken Sie auf die Dropdownliste, und wählen Sie **Entfernen** aus.

   :::image type="content" source="media/openvpn-azure-ad-client-mac/remove-1.png" alt-text="Screenshot: Entfernen einer Verbindung":::
1. Klicken Sie im Feld **VPN-Verbindung entfernen?** auf **Entfernen**.
   :::image type="content" source="media/openvpn-azure-ad-client-mac/remove-2.png" alt-text="Screenshot: Entfernungsvorgang":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Erstellen eines Azure Active Directory-Mandanten für P2S Open VPN-Verbindungen, die die Azure AD-Authentifizierung verwenden](openvpn-azure-ad-tenant.md).
