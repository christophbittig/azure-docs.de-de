---
title: Verwalten des sicheren Zugriffs auf Ressourcen in Spoke-VNETs für P2S-Clients
titleSuffix: Azure Virtual WAN
description: Dieser Artikel hilft Ihnen, mit Azure Virtual WAN und Azure Firewall-Regeln den sicheren Zugriff auf virtuelle Netzwerke für Benutzer-VPN-Clients (Point-to-Site) zu verwalten.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 07/29/2021
ms.author: cherylmc
ms.openlocfilehash: e46f9c5e3d59c4f30d0a9a0a9dddcf98cd636e40
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339440"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>Verwalten des sicheren Zugriffs auf Ressourcen in Spoke-VNETs für Benutzer-VPN-Clients

In diesem Artikel erfahren Sie, wie Sie Virtual WAN- und Azure Firewall-Regeln und -Filter verwenden, um den sicheren Zugriff auf Ihre Ressourcen in Azure über Point-to-Site-IKEv2- oder Open VPN-Verbindungen verwalten. Diese Konfiguration ist hilfreich, wenn Sie den Zugriff auf Azure-Ressourcen für Remotebenutzer einschränken oder Ihre Ressourcen in Azure sichern möchten.

Die Schritte in diesem Artikel unterstützen Sie bei der Erstellung der Architektur im folgenden Diagramm, um Benutzer-VPN-Clients den Zugriff auf eine bestimmte Ressource (VM1) in einem mit dem virtuellen Hub verbundenen Spoke-VNET, jedoch nicht auf andere Ressourcen (VM2) zu ermöglichen. Verwenden Sie dieses Architekturbeispiel als grundlegende Richtlinie.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="Diagramm: Geschützter virtueller Hub" :::

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* Sie verfügen über die Werte, die für die gewünschte Authentifizierungskonfiguration verfügbar sind. Beispiel: ein RADIUS-Server, Azure Active Directory-Authentifizierung oder [Generieren und Exportieren von Zertifikaten](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

## <a name="create-a-virtual-wan"></a>Erstellen eines virtuellen WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>Definieren der P2S-Konfigurationsparameter

Die P2S-Konfiguration (Point-to-Site) definiert die Parameter für das Herstellen der Verbindung mit Remoteclients. In diesem Abschnitt erfahren Sie, wie Sie P2S-Konfigurationsparameter definieren und dann die Konfiguration erstellen, die für das VPN-Clientprofil verwendet wird. Welche Anweisungen Sie befolgen, hängt von der Authentifizierungsmethode ab, die Sie verwenden möchten.

### <a name="authentication-methods"></a>Authentifizierungsmethoden

Wenn Sie die Authentifizierungsmethode auswählen, stehen Ihnen drei Optionen zur Auswahl. Für jede Methode gelten bestimmte Anforderungen. Wählen Sie eine der folgenden Methoden aus, und führen Sie dann die Schritte aus.

* **Azure Active Directory-Authentifizierung:** Rufen Sie Folgendes ab:

   * Die **Anwendungs-ID** der Azure-VPN-Unternehmensanwendung, die in Ihrem Azure AD-Mandanten registriert ist.
   * Den **Aussteller**. Beispiel: `https://sts.windows.net/your-Directory-ID`.
   * Den **Azure AD-Mandanten**. Beispiel: `https://login.microsoftonline.com/your-Directory-ID`.

* **RADIUS-basierte Authentifizierung:** Abrufen der IP-Adresse des RADIUS-Servers, des RADIUS-Servergeheimnisses und der Zertifikatinformationen.

* **Azure-Zertifikate:** Für diese Konfiguration sind Zertifikate erforderlich. Sie müssen Zertifikate entweder generieren oder abrufen. Ein Clientzertifikat wird für jeden Client benötigt. Außerdem müssen die Stammzertifikatinformationen (öffentlicher Schlüssel) hochgeladen werden. Weitere Informationen zu den erforderlichen Zertifikaten finden Sie unter [Generieren und Exportieren von Zertifikaten für Punkt-zu-Standort-Verbindungen mithilfe von PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>Erstellen des Hubs und Gateways

In diesem Abschnitt erstellen Sie den virtuellen Hub mit einem Point-to-Site-Gateway. Zum Konfigurieren können Sie die folgenden Beispielwerte verwenden:

* **Privater IP-Adressraum des Hubs:** 10.0.0.0/24
* **Clientadresspool:** 10.5.0.0/16
* **Benutzerdefinierte DNS-Server:** Sie können bis zu fünf DNS-Server auflisten.

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generieren der VPN-Clientkonfigurationsdateien

In diesem Abschnitt generieren und laden Sie die Konfigurationsprofildateien herunter. Diese Dateien werden verwendet, um den nativen VPN-Client auf dem Clientcomputer zu konfigurieren. Informationen zum Inhalt der Clientprofildateien finden Sie unter [Erstellen und Installieren von VPN-Clientkonfigurationsdateien für P2S-Konfigurationen mit nativer Azure-Zertifikatauthentifizierung](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md).

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>VPN-Clients konfigurieren

Verwenden Sie das heruntergeladene Profil, um die Clients für den Remotezugriff zu konfigurieren. Das Verfahren unterscheidet sich für jedes Betriebssystem. Befolgen Sie die Anweisungen, die für Ihr System gelten.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>Verbinden des Spoke-VNET

In diesem Abschnitt erstellen Sie eine Verbindung zwischen Ihrem Hub und dem Spoke-VNet.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>Erstellen von virtuellen Computern

In diesem Abschnitt erstellen Sie in Ihrem VNET zwei virtuelle Computer, VM1 und VM2. Im Netzwerkdiagramm werden 10.18.0.4 und 10.18.0.5 verwendet. Wenn Sie Ihre virtuellen Computer konfigurieren, stellen Sie sicher, dass Sie (auf der Registerkarte „Netzwerk“) das von Ihnen erstellte virtuelle Netzwerk auswählen. Schritte zum Erstellen einer VM finden Sie unter [Schnellstart: Erstellen einer VM](../virtual-machines/windows/quick-create-portal.md).

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>Schützen des virtuellen Hubs

Ein virtueller Standard-Hub verfügt nicht über integrierte Sicherheitsrichtlinien zum Schutz der Ressourcen in den virtuellen Spoke-Netzwerken. Ein geschützter virtueller Hub verwendet Azure Firewall oder einen Drittanbieter, um den eingehenden und ausgehenden Datenverkehr zum Schutz Ihrer Ressourcen in Azure zu verwalten.

Konvertieren Sie den Hub mithilfe des folgenden Artikels in einen geschützten Hub: [Konfigurieren von Azure Firewall in einem Virtual WAN-Hub](howto-firewall.md).

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> Erstellen von Regeln zum Verwalten und Filtern von Datenverkehr

Erstellen Sie Regeln, die das Verhalten von Azure Firewall vorschreiben. Wenn Sie den Hub schützen, stellen Sie sicher, dass alle Pakete, die in den virtuellen Hub eingehen, vor dem Zugriff auf Ihre Azure-Ressourcen der Firewallverarbeitung unterliegen.

Nach Ausführung dieser Schritte haben Sie eine Architektur erstellt, mit der VPN-Benutzer mit der privaten IP-Adresse 10.18.0.4, jedoch **NICHT** mit der privaten IP-Adresse 10.18.0.5 auf den virtuellen Computer zugreifen können.

1. Navigieren Sie im Azure-Portal zum **Firewall Manager**.
1. Wählen Sie unter „Sicherheit“ die Option **Azure Firewall-Richtlinien** aus.
1. Wählen Sie die Option **Azure-Firewallrichtlinie erstellen** aus.
1. Geben Sie unter **Richtliniendetails** einen Namen ein, und wählen Sie die Region aus, in der der virtuelle Hub bereitgestellt wird.
1. Klicken Sie auf **Weiter: DNS-Einstellungen (Vorschau)** .
1. Klicken Sie auf **Weiter: Regeln**.
1. Wählen Sie auf der Registerkarte **Regeln** die Option **Regelsammlung hinzufügen** aus.
1. Geben Sie einen Namen für die Sammlung an. Legen Sie **Netzwerk** als Typ fest. Fügen Sie den Prioritätswert **100** hinzu.
1. Geben Sie den Namen der Regel, den Quelltyp, die Quelle, das Protokoll, die Zielports und den Zieltyp an, wie im folgenden Beispiel gezeigt. Wählen Sie anschließend **Hinzufügen** aus. Diese Regel ermöglicht allen IP-Adressen aus dem VPN-Clientpool den Zugriff auf den virtuellen Computer mit der privaten IP-Adresse 10.18.04, aber nicht auf andere Ressourcen, die mit dem virtuellen Hub verbunden sind. Erstellen Sie alle gewünschten Regeln, die den gewünschten Architektur- und Berechtigungsregeln entsprechen.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="Firewallregeln" :::

1. Klicken Sie auf **Weiter: Threat Intelligence** aus.
1. Klicken Sie auf **Weiter: Hubs**.
1. Wählen Sie auf der Registerkarte **Hubs** die Option **Virtuelle Hubs zuordnen** aus.
1. Wählen Sie den zuvor erstellten virtuellen Hub und dann **Hinzufügen** aus.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

Es dauert mindestens 5 Minuten, bis dieser Vorgang abgeschlossen ist.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Weiterleiten von Datenverkehr über Azure Firewall

In diesem Abschnitt müssen Sie sicherstellen, dass der Datenverkehr über Azure Firewall weitergeleitet wird.

1. Wählen Sie im Portal in **Firewall Manager** die Option **Geschützte virtuelle Hubs** aus.
1. Wählen Sie den virtuellen Hub aus, den Sie erstellt haben.
1. Wählen Sie unter **Einstellungen** die Option **Sicherheitskonfiguration** aus.
1. Wählen Sie unter **Private traffic** (Privater Datenverkehr) die Option **Send via Azure Firewall** (Über Azure Firewall senden) aus.
1. Überprüfen Sie, ob die VNET-Verbindung und der private Datenverkehr der Branch-Verbindung durch Azure Firewall geschützt sind.
1. Wählen Sie **Speichern** aus.

## <a name="validate"></a><a name="validate"></a>Überprüfen

Überprüfen Sie die Einrichtung des geschützten Hubs.

1. Stellen Sie von Ihrem Clientgerät aus über VPN eine Verbindung mit dem **geschützten virtuellen Hub** her.
1. Senden Sie von Ihrem Client aus ein Ping-Signal an die IP-Adresse **10.18.0.4**. Es sollte eine Antwort angezeigt werden.
1. Senden Sie von Ihrem Client aus ein Ping-Signal an die IP-Adresse **10.18.0.5**. Es sollte keine Antwort angezeigt werden.

### <a name="considerations"></a>Überlegungen

* Stellen Sie sicher, dass die **Tabelle effektiver Routen** auf dem geschützten virtuellen Hub über den nächsten Hop für privaten Datenverkehr durch die Firewall verfügt. Um auf die Tabelle effektiver Routen zuzugreifen, navigieren Sie zu Ihrer **virtuellen Hubressource**. Wählen Sie unter **Konnektivität** die Option **Routing** und dann **Effektive Routen** aus. Wählen Sie dort die **Standard**-Routingtabelle aus.
* Vergewissern Sie sich, dass Sie im Abschnitt [Erstellen von Regeln](#create-rules) Regeln erstellt haben. Wenn diese Schritte ausgelassen werden, werden die von Ihnen erstellten Regeln dem Hub nicht tatsächlich zugeordnet, und Routingtabelle und Paketfluss verwenden Azure-Firewall nicht.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie unter [Virtual WAN – Häufig gestellte Fragen](virtual-wan-faq.md).
* Weitere Informationen zu Azure Firewall finden Sie unter [Azure Firewall – Häufig gestellte Fragen](../firewall/firewall-faq.yml).
