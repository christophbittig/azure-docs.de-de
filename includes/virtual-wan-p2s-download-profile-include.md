---
ms.author: cherylmc
author: cherylmc
ms.date: 07/29/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: d13072a03dce5aa3c7fd84ccf458b4f4fad112f1
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778486"
---
1. Wählen Sie auf der Seite Ihres **virtuellen WAN** die Option **Benutzer-VPN-Konfigurationen** aus.
1. Wählen Sie auf der Seite **Benutzer-VPN-Konfigurationen** eine Konfiguration und anschließend **VPN-Profil für Virtual WAN-Benutzer herunterladen** aus.

   :::image type="content" source="./media/virtual-wan-p2s-download-profile/download.png" alt-text="Screenshot von „VPN-Profil für Virtual WAN-Benutzer herunterladen“.":::

   * Beim Herunterladen der Konfiguration auf WAN-Ebene wird ein integriertes Benutzer-VPN-Profil bereitgestellt, das auf Traffic Manager basiert. 
   
   * Informationen zu globalen oder hubbasierten Profilen finden Sie unter [Hubprofile](../articles/virtual-wan/global-hub-profile.md). Failoverszenarien werden durch ein globales Profil vereinfacht.

   * Wenn ein Hub aus irgendeinem Grund nicht verfügbar ist, stellt die integrierte Datenverkehrsverwaltung des Diensts die Konnektivität mit Azure-Ressourcen für Point-to-Site-Benutzer (über einen anderen Hub) sicher. Sie können jederzeit eine hubspezifische VPN-Konfiguration herunterladen, indem Sie zum entsprechenden Hub navigieren. Laden Sie unter **Benutzer-VPN (Point-to-Site)** das Profil **Benutzer-VPN** für den virtuellen Hub herunter.
1. Wählen Sie auf der Seite **VPN-Profil für Virtual WAN-Benutzer herunterladen** den gewünschten **Authentifizierungstyp** und anschließend **Profil generieren und herunterladen** aus. 

   Ein Profilpaket (ZIP-Datei) mit den Clientkonfigurationseinstellungen wird generiert und auf Ihren Computer heruntergeladen.

   :::image type="content" source="./media/virtual-wan-p2s-download-profile/generate.png" alt-text="Screenshot von „Profil generieren und herunterladen“":::.

