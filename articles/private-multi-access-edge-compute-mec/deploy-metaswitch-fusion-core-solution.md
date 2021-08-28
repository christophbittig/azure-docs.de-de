---
title: Bereitstellen von Fusion Core auf einem Azure Stack Edge-Gerät
description: Erfahren Sie, wie Sie Cloudlösungen aus Microsoft Azure und Metaswitch-Netzwerken bereitstellen, die Ihnen helfen können, Ihr Netzwerk in Zukunft zu sichern, Kosten zu senken und neue Geschäftsmodelle und Einnahmeströme zu erzeugen.
author: djrmetaswitch
ms.service: private-multi-access-edge-compute-mec
ms.topic: how-to
ms.date: 06/16/2021
ms.author: drichards
ms.openlocfilehash: 93e7511cbe8202f15dd055467c253044fa617e74
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286921"
---
# <a name="deploy-fusion-core-on-an-azure-stack-edge-device"></a>Bereitstellen von Fusion Core auf einem Azure Stack Edge-Gerät

Dieser Artikel bietet eine allgemeine Übersicht über den Prozess der Bereitstellung von Fusion Core auf einem Azure Stack Edge-Gerät.

## <a name="collect-required-azure-resources"></a>Sammeln erforderlicher Azure-Ressourcen

Sie benötigen Folgendes:

- Einen vollständig bereitgestellten Azure Stack Edge Pro mit GPU. Die Anschlüsse des Geräts müssen wie folgt verbunden sein.

  - Port 5 : LAN
  - Port 6 : WLAN
  - Ein Port mit Ihrem Verwaltungsnetzwerk verbunden. Sie können einen beliebigen Port zwischen 1 und 4 auswählen. Sie müssen ihren ausgewählten Port für Compute aktiviert haben, wie im Schritt „Aktivieren des Computenetzwerks“ unter [Tutorial: Konfigurieren des Netzwerks für Azure Stack Edge Pro mit GPU](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md) beschrieben.
- Ein Azure-Konto mit einem aktiven Abonnement und Zugriff auf Folgendes.

  - Den Azure-Netzwerkfunktionsmanager-Dienst. Dieser verfügt über den Ressourcenanbieternamespace „Microsoft.HybridNetwork“. Weitere Informationen finden Sie unter [Was ist der Azure-Netzwerkfunktionsmanager?](../network-function-manager/overview.md).
  - Die verwaltete Anwendung „Fusion Core - 5G Packet Core“. Sie müssen den Zugriff anfordern, indem Sie [https://azuremarketplace.microsoft.com/marketplace/apps/metaswitch.fusioncore_0-1-0?tab=Overview](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/metaswitch.fusioncore_0-1-0?tab=Overview) besuchen und die Schaltfläche **Kontaktformular** (CONTACT ME) verwenden. Unsere Vertriebsmitarbeiter stellen Ihnen Informationen darüber zur Verfügung, wie Sie Fusion Core bewerten oder kaufen können. Nachdem Sie eine dieser Optionen ausgewählt haben, gewähren wir Ihnen Zugriff auf die verwaltete Anwendung.
  - Die integrierte Rolle **Besitzer** im Abonnementbereich. Wenn dies in Ihrer Organisation nicht möglich ist, wenden Sie sich an Ihren Metaswitch-Supportmitarbeiter.
- Ein konfiguriertes **Azure-Netzwerkfunktionsmanager-Gerät** eobjekt, das das Azure Stack Edge-Gerät darstellt.

## <a name="deploy-fusion-core"></a>Bereitstellen von Fusion Core

Das folgende Diagramm zeigt den Prozess der Bereitstellung von Fusion Core, einschließlich der Objekte, die Sie erstellen, und der Methoden für die fortlaufende Verwaltung nach der Installation.

:::image type="content" source="./media/deploy-metaswitch-solution/fusion-core-on-azure-stack-edge-deployment-process.png" alt-text="Fusion Core-Bereitstellungsprozess":::  

Nachdem Sie Fusion Core für ASE im Azure Marketplace ausgewählt haben, stellen Sie eine benannte Instanz der verwalteten Fusion Core-Anwendung in der Ressourcengruppe Ihrer Wahl in Ihrem Azure-Abonnement bereit.

Die verwaltete Fusion Core-Anwendung erstellt eine verwaltete Ressourcengruppe mit Packet Core-Netzwerkfunktion und Relayressourcen, die auf Ihrem Azure Stack Edge-Gerät (ASE) installiert werden müssen.

- Die Packet Core-Netzwerkfunktionsressource definiert die Fusion Core-Basis-VM-Version und deren Konfigurationsparameter. Sie wird im Azure-Portal standardmäßig ausgeblendet.
- Die Relayressource enthält eine Reihe von Hybridverbindungen, die die erforderliche Konnektivität für die Installation von und den Remotezugriff auf Überwachungsschnittstellen bereitstellen.

Fusion Core wird dann wie folgt automatisch auf Ihrem ASE-Gerät bereitgestellt.

1. Das ASE-Gerät lädt die Fusion Core Basis-VM herunter und startet sie.
1. Die Metaswitch-Dienstverwaltungsanwendung installiert Fusion Core auf dem Kubernetes-Cluster in der Fusion Core Basis-VM und stellt es bereit.

Zusätzlich zur Koordination der Installation von Fusion Core ermöglicht die Metaswitch-Dienstverwaltungsanwendung Metaswitch-Supportmitarbeitern die Überwachung des Systemzustands und von Zugriffsdiagnosen.


## <a name="next-steps"></a>Nächste Schritte
- Eine schrittweise Anleitung zum Bereitstellen von Fusion Core auf einem Azure Stack Edge-Gerät finden Sie, wenn Sie das [Lösungshandbuch für Private 5G Edge Fusion Core herunterladen](https://go.microsoft.com/fwlink/?linkid=2165096).
- Weitere Informationen zu [Private 5G Edge Fusion Core](metaswitch-fusion-core-overview.md)