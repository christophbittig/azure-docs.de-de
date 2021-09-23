---
title: Konfigurieren einer P2S-Benutzer-VPN-Verbindung mit Azure Active Directory-Authentifizierung
titleSuffix: Azure Virtual WAN
description: Hier erfahren Sie, wie Sie die Azure Active Directory-Authentifizierung für ein Virtual WAN-Benutzer-VPN (Point-to-Site) konfigurieren.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/20/2021
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 38006136ddd5a5331699b2768c35ebd794e563a8
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778507"
---
# <a name="configure-a-point-to-site-user-vpn-connection---azure-active-directory-authentication"></a>Konfigurieren einer Point-to-Site-Benutzer-VPN-Verbindung: Azure Active Directory-Authentifizierung

In diesem Artikel erfahren Sie, wie Sie die Azure AD-Authentifizierung für ein Benutzer-VPN in Virtual WAN konfigurieren, um eine OpenVPN-Verbindung mit Ihren Ressourcen in Azure herzustellen. Azure Active Directory-Authentifizierung ist nur für Gateways mit OpenVPN-Protokoll verfügbar. Weitere Informationen zu Virtual WAN finden Sie auf der Seite mit der [Übersicht über Virtual WAN](virtual-wan-about.md).

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen eines virtuellen WAN
* Erstellen einer Benutzer-VPN-Konfiguration
* Herunterladen eines Benutzer-VPN-Profils für eine Virtual WAN-Instanz
* Erstellen eines virtuellen Hubs
* Bearbeiten eines Hubs zum Hinzufügen eines P2S-Gateways
* Verbinden eines VNet mit einem virtuellen Hub
* Herunterladen und Anwenden der VPN-Clientkonfiguration für Benutzer
* Anzeigen Ihrer Virtual WAN-Instanz

:::image type="content" source="./media/virtual-wan-about/virtualwanp2s.png" alt-text="Virtual WAN-Diagramm.":::

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass die folgenden Voraussetzungen erfüllt sind bzw. Folgendes vorhanden ist:

* Sie verfügen über ein virtuelles Netzwerk, mit dem Sie eine Verbindung herstellen möchten. Stellen Sie sicher, dass sich kein Subnetz Ihres lokalen Netzwerks mit den virtuellen Netzwerken für die Verbindungsherstellung überschneidet. Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie in der [Schnellstartanleitung](../virtual-network/quick-create-portal.md).

* Ihr virtuelles Netzwerk verfügt nicht über Gateways für virtuelle Netzwerke. Falls Ihr virtuelles Netzwerk über ein Gateway verfügt (entweder VPN oder ExpressRoute), müssen Sie alle Gateways entfernen. Für diese Konfiguration ist es erforderlich, dass virtuelle Netzwerke stattdessen mit dem Gateway des Virtual WAN-Hubs verbunden werden.

* Beschaffen Sie sich einen IP-Adressbereich für Ihre Hubregion. Der Hub ist ein virtuelles Netzwerk, das von Virtual WAN erstellt und verwendet wird. Der von Ihnen für den Hub angegebene Adressbereich darf sich nicht mit einem Ihrer vorhandenen virtuellen Netzwerke überlappen, mit denen Sie eine Verbindung herstellen. Außerdem ist keine Überlappung mit Ihren Adressbereichen möglich, mit denen Sie lokal eine Verbindung herstellen. Falls Sie nicht mit den IP-Adressbereichen in Ihrer lokalen Netzwerkkonfiguration vertraut sind, sollten Sie sich an eine Person wenden, die Ihnen diese Informationen zur Verfügung stellen kann.

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Erstellen eines Virtual WAN

Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich mit Ihrem Azure-Konto an.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-user-vpn-configuration"></a><a name="user-config"></a>Erstellen einer Benutzer-VPN-Konfiguration

Eine Benutzer-VPN-Konfiguration definiert die Parameter für das Herstellen der Verbindung mit Remoteclients. Es ist wichtig, die Benutzer-VPN-Konfiguration zu erstellen, bevor Sie Ihren virtuellen Hub mit P2S-Einstellungen konfigurieren, da Sie die Benutzer-VPN-Konfiguration angeben müssen, die Sie verwenden möchten.

1. Navigieren Sie zur Seite **Virtual WAN > Benutzer-VPN-Konfigurationen**, und klicken Sie auf **+ Benutzer-VPN-Konfiguration erstellen**.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/user-vpn.png" alt-text="Screenshot: Benutzer-VPN-Konfiguration erstellen.":::
1. Geben Sie auf der Seite **Grundlagen** die Parameter an.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/basics.png" alt-text="Screenshot der Seite „Grundlagen“.":::

   * **Konfigurationsname:** Geben Sie einen für Ihre Benutzer-VPN-Konfiguration gewünschten Namen ein.
    * **Tunneltyp**: Wählen Sie im Dropdownmenü „OpenVPN“ aus.
1. Klicken Sie auf **Azure Active Directory**, um die Seite zu öffnen.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/values.png" alt-text="Screenshot der Seite „Azure Active Directory“.":::

    Schalten Sie **Azure Active Directory** auf **Ja** um, und stellen Sie die folgenden Werte basierend auf Ihren Mandantendetails bereit. Sie können die erforderlichen Werte auf der Seite „Azure Active Directory“ für Enterprise-Anwendungen im Portal anzeigen.
   * **Authentifizierungsmethode:** Klicken Sie auf „Azure Active Directory“.
   * **Zielgruppe:** Geben Sie die Anwendungs-ID der in Ihrem Azure AD-Mandanten registrierten [Azure-VPN](openvpn-azure-ad-tenant.md)-Unternehmensanwendung ein. 
   * **Aussteller** - `https://sts.windows.net/<your Directory ID>/`
   * **AAD-Mandant** - `https://login.microsoftonline.com/<your Directory ID>`
1. Klicken Sie auf **Erstellen**, um die Benutzer-VPN-Konfiguration zu erstellen. Sie wählen diese Konfiguration später in der Übung aus.

## <a name="create-an-empty-hub"></a><a name="site"></a>Erstellen eines leeren Hubs

Für diese Übung erstellen wir einen leeren virtuellen Hub. Im nächsten Abschnitt fügen Sie einem bereits vorhandenen Hub ein Gateway hinzu. Es ist jedoch auch möglich, diese Schritte zu kombinieren und den Hub mit den P2S-Gatewayeinstellungen gleichzeitig zu erstellen.

[!INCLUDE [Create an empty hub](../../includes/virtual-wan-empty-hub-include.md)]

## <a name="add-a-p2s-gateway-to-a-hub"></a><a name="hub"></a>Hinzufügen eines P2S-Gateways zu einem Hub

In diesem Abschnitt erfahren Sie, wie Sie einem bereits vorhandenen virtuellen Hub ein Gateway hinzufügen. Dieser Schritt kann bis zu 30 Minuten dauern, bis die Aktualisierung des Hubs abgeschlossen ist.

1. Navigieren Sie unter dem virtuellen WAN zur Seite **Hubs**.
1. Wählen Sie den Hub aus, dem Sie die VPN-Serverkonfiguration zuordnen möchten, und klicken Sie auf die Auslassungspunkte ( **...** ), um das Menü anzuzeigen. Klicken Sie dann auf **Virtuellen Hub bearbeiten**.

   :::image type="content" source="media/virtual-wan-point-to-site-azure-ad/select-hub.png" alt-text="Der Screenshot zeigt die im Menü ausgewählte Option zum Bearbeiten eines virtuellen Hubs." lightbox="media/virtual-wan-point-to-site-azure-ad/select-hub.png":::

1. Aktivieren Sie auf der Seite **Virtuellen Hub bearbeiten** die Kontrollkästchen **VPN -Gateway für VPN-Standorte einschließen** und **Point-to-Site-Gateway einschließen,** um die Einstellungen anzuzeigen. Konfigurieren Sie dann die Werte.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/edit-virtual-hub.png" alt-text="Screenshot: Seite „Virtuellen Hub bearbeiten“.":::

   * **Gatewayskalierungseinheiten**: Wählen Sie die Gatewayskalierungseinheiten aus. Skalierungseinheiten stellen die aggregierte Kapazität des Benutzer-VPN-Gateways dar. Wenn Sie 40 oder mehr Gatewayskalierungseinheiten auswählen, müssen Sie Ihren Clientadresspool entsprechend planen. Informationen dazu, wie sich diese Einstellung auf den Clientadresspool auswirkt, finden Sie unter [Informationen zu Clientadresspools](about-client-address-pools.md). Informationen zu Gatewayskalierungseinheiten finden Sie in den [häufig gestellten Fragen (FAQ)](virtual-wan-faq.md#for-user-vpn-point-to-site--how-many-clients-are-supported).
   * **Benutzer-VPN--Konfiguration**: Wählen Sie die Konfiguration aus, die Sie zuvor erstellt haben.
   * **Clientadresspool**: Geben Sie den Clientadresspool an, aus dem den VPN-Clients IP-Adressen zugewiesen werden. Diese Einstellung entspricht den Gatewayskalierungseinheiten, die Sie 
1. Klicken Sie auf **Confirm** (Bestätigen). Das Aktualisieren des Hubs kann bis zu 30 Minuten dauern.

## <a name="connect-vnet-to-hub"></a><a name="connect-vnet"></a>Verbinden eines VNET mit einem Hub

In diesem Abschnitt erstellen Sie eine Verbindung zwischen Ihrem virtuellen Hub und Ihrem VNet.

[!INCLUDE [Connect virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-user-vpn-profile"></a><a name="device"></a>Herunterladen des VPN-Profils für Benutzer

Alle erforderlichen Konfigurationseinstellungen für die VPN-Clients sind in einer ZIP-Datei für die VPN-Clientkonfiguration enthalten. Mithilfe der Einstellungen in der ZIP-Datei können Sie die VPN-Clients einfach konfigurieren. Die von Ihnen generierten VPN-Clientkonfigurationsdateien gelten speziell für die Benutzer-VPN-Gatewaykonfiguration für Ihr Gateway. In diesem Abschnitt generieren Sie die zum Konfigurieren Ihrer VPN-Clients verwendeten Dateien und laden sie herunter.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-user-vpn-clients"></a>Konfigurieren von Benutzer-VPN-Clients

Auf jedem Computer, der eine Verbindung herstellt, muss ein Client installiert sein. Sie konfigurieren jeden Client mithilfe der VPN-Benutzer-Clientprofildateien, die Sie in den vorherigen Schritten heruntergeladen haben. Verwenden Sie den Artikel, der sich auf das Betriebssystem bezieht, das Sie verbinden möchten.

### <a name="to-configure-macos-vpn-clients-preview"></a>So konfigurieren Sie macOS-VPN-Clients (Vorschau)

Anweisungen zum **macOS**-Client finden Sie unter [Konfigurieren eines VPN-Clients – macOS (Vorschau)](openvpn-azure-ad-client-mac.md).

### <a name="to-configure-windows-vpn-clients"></a>So konfigurieren Sie Windows-VPN-Clients

[!INCLUDE [Download Azure VPN client](../../includes/vpn-gateway-download-vpn-client.md)]

#### <a name="to-import-a-vpn-client-profile-windows"></a><a name="import"></a>So importieren Sie ein VPN-Clientprofil (Windows)

1. Wählen Sie auf der Seite **Importieren** aus.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/import/import-1.png" alt-text="Screenshot der Importseite.":::

1. Navigieren Sie zur XML-Datei für das Profil und wählen Sie sie aus. Wählen Sie bei ausgewählter Datei **Öffnen** aus.

    ![Der Screenshot zeigt das Dialogfeld „Öffnen“, in dem Sie eine Datei auswählen können.](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

1. Geben Sie den Namen des Profils an und wählen Sie **Speichern** aus.

    ![Der Screenshot zeigt den hinzugefügten Verbindungsnamen und die ausgewählte Schaltfläche „Speichern“ an.](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

1. Wählen Sie **Verbinden** aus, um die Verbindung zum VPN herzustellen.

    ![Der Screenshot zeigt die Schaltfläche „Verbinden“ für die für die gerade erstellte Verbindung an.](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

1. Nachdem die Verbindung hergestellt wurde, wird das Symbol grün und **Verbunden** angezeigt.

    ![Der Screenshot zeigt die Verbindung mit dem Status „Verbunden“ mit der Option zum Trennen der Verbindung.](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile---windows"></a><a name="delete"></a>So löschen Sie ein Clientprofil: Windows

1. Wählen Sie neben dem Clientprofil, das Sie löschen möchten, die Auslassungspunkte (...) aus. Wählen Sie dann **Entfernen** aus.

    ![Screenshot der im Menü ausgewählten Option „Entfernen“.](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

1. Wählen Sie **Entfernen** aus, um den Löschvorgang durchzuführen.

    ![Der Screenshot zeigt ein Bestätigungsdialogfeld mit der Option zum Entfernen oder Abbrechen.](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues---windows"></a><a name="diagnose"></a>Diagnose von Verbindungsproblemen: Windows

1. Zum Diagnostizieren von Verbindungsproblemen können Sie das **Diagnose**-Tool verwenden. Wählen Sie neben der VPN-Verbindung, die Sie diagnostizieren möchten, die Auslassungspunkte (...) aus, um das Menü einzublenden. Wählen Sie dann **Diagnose** aus.

    ![Screenshot der im Menü ausgewählten Option „Diagnose“.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

1. Wählen Sie auf der Seite **Verbindungseigenschaften** die Option **Diagnose ausführen** aus.

    ![Der Screenshot zeigt die Schaltfläche „Diagnose ausführen“ für eine Verbindung an.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

1. Melden Sie sich mit Ihren Anmeldeinformationen an.

    ![Der Screenshot zeigt das Dialogfeld „Anmelden“ für diese Aktion an.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

1. Zeigen Sie die Diagnoseergebnisse an.

    ![Screenshot mit den Ergebnissen der Diagnose.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Anzeigen Ihrer Virtual WAN-Instanz

1. Navigieren Sie zum virtuellen WAN.
2. Auf der Seite „Übersicht“ steht jeder Punkt auf der Karte für einen Hub.
3. Im Abschnitt mit den Hubs und Verbindungen können Sie den Hubstatus, die Site, die Region, den VPN-Verbindungsstatus und die Anzahl von ein- und ausgehenden Byte anzeigen.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Bereinigen von Ressourcen

Wenn Sie die von Ihnen erstellten Ressourcen nicht mehr benötigen, können Sie sie löschen. Einige Virtual WAN-Ressourcen müssen aufgrund von Abhängigkeiten in einer bestimmten Reihenfolge gelöscht werden. Das Löschen kann bis zu 30 Minuten dauern.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Virtual WAN finden Sie auf der Seite mit der [Übersicht über Virtual WAN](virtual-wan-about.md).
