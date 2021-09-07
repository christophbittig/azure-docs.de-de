---
title: Bereitstellen von Affirmed Private Network Service in Azure
description: Hier erfahren Sie, wie Sie die Affirmed Private Network Service-Lösung in Azure bereitstellen.
author: KumudD
ms.service: private-multi-access-edge-compute-mec
ms.topic: how-to
ms.date: 06/16/2021
ms.author: hollycl
ms.openlocfilehash: d3c027a7d4006a947f520dee8406e40a30f1b304
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2021
ms.locfileid: "112458450"
---
# <a name="deploy-affirmed-private-network-service-on-azure"></a>Bereitstellen von Affirmed Private Network Service in Azure

Dieser Artikel bietet eine allgemeine Übersicht über den Prozess zur Bereitstellung einer APNS-Lösung (Affirmed Private Network Service) auf einem Azure Stack Edge-Gerät über den Microsoft Azure Marketplace.

Das folgende Diagramm zeigt die Systemarchitektur von Affirmed Private Network Service, einschließlich der für die Bereitstellung erforderlichen Ressourcen.

![Bereitstellung von Affirmed Private Network Service](media/deploy-affirmed-private-network-service/deploy-affirmed-private-network-service.png)

## <a name="collect-required-information"></a>Erfassen der erforderlichen Informationen

Zum Bereitstellen von APNS benötigen Sie die folgenden Ressourcen:

- Ein konfiguriertes Azure Network Function Manager-Geräteobjekt, das als digitaler Zwilling des Azure Stack Edge-Geräts dient 

- Eine vollständig bereitgestellte Azure Stack Edge-Instanz mit einer NetFoundry-VM 

- Die Abonnementgenehmigung für das Affirmed Management Systems-VM-Angebot und eine mit APNS verwaltete Anwendung 

- Ein Azure-Konto mit einem aktiven Abonnement und Zugriff auf Folgendes:  

    - Die integrierte Rolle **Besitzer** für Ihre Ressourcengruppe 

    - Die integrierte Rolle **Mitwirkender für verwaltete Anwendungen** für Ihr Abonnement 

    - Ein virtuelles Netzwerk und Subnetz für die Verknüpfung (öffnen Sie die Ports tcp/443 und tcp/8443) 

    - Fünf IP-Adressen im virtuellen Subnetz 

    - Ein gültiges SAS-Token von Affirmed Release Engineering  

    - Einen Administratorbenutzernamen und das zugehörige Kennwort für die Programmierung während der Bereitstellung 
    
## <a name="deploy-apns"></a>Bereitstellen von APNS

Um die von APNS verwaltete Anwendung automatisch mit allen erforderlichen Ressourcen und relevanten Informationen bereitzustellen, wählen Sie im Microsoft Azure Marketplace die verwaltete APNS-Anwendung aus. Wenn Sie APNS bereitstellen, werden alle erforderlichen Ressourcen automatisch in einer verwalteten Ressourcengruppe erstellt.

Führen Sie die folgenden Schritte aus, um APNS bereitzustellen:
1.  Öffnen Sie das Azure-Portal, und wählen Sie **Ressource erstellen** aus.
2.  Geben Sie *APNS* in die Suchleiste ein, und drücken Sie die EINGABETASTE.
3.  Wählen Sie **View Private Offers** (Private Angebote anzeigen) aus. 
    > [!NOTE]
    > Die von APNS verwaltete Anwendung wird erst angezeigt, nachdem Sie **View Private Offers** (Private Angebote anzeigen) ausgewählt haben.
4.  Wählen Sie im Dropdownmenü des **privaten Angebots** die Option **Erstellen** und dann die Option zum Bereitstellen aus.
5.  Führen Sie die Anwendungseinrichtung aus, legen Sie die Netzwerkeinstellungen fest, überprüfen Sie Ihre Angaben, und erstellen Sie die Ressource.
6.  Klicken Sie auf **Bereitstellen**.

## <a name="next-steps"></a>Nächste Schritte

- Detaillierte Anweisungen zum Bereitstellen von APNS und Konfigurieren von NetFoundry-Einstellungen auf einem Azure Stack Edge-Gerät finden Sie im [Bereitstellungsleitfaden für Affirmed Private Network Service](https://go.microsoft.com/fwlink/?linkid=2165732).
- Informationen zum programmgesteuerten Portal mit grafischer Benutzeroberfläche, das Operatoren zum Bereitstellen, Überwachen und Verwalten privater Mobile Core-Netzwerke verwenden, finden Sie im [Benutzerhandbuch für Affirmed Private Network Service Manager](https://go.microsoft.com/fwlink/?linkid=2165932).
- Weitere Informationen zu [Affirmed Private Network Service in Azure](affirmed-private-network-service-overview.md)
