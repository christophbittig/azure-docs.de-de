---
title: 'Tutorial: Erstellen eines regionsübergreifenden Lastenausgleichs mithilfe des Azure-Portals'
titleSuffix: Azure Load Balancer
description: In diesem Tutorial stellen Sie zunächst eine regionsübergreifende Azure Load Balancer-Instanz mit dem Azure-Portal bereit.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/02/2021
ms.openlocfilehash: f0004845033493dc7546bb3af467918ea77ebcad
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201616"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Tutorial: Erstellen einer regionsübergreifenden Azure Load Balancer-Instanz mithilfe des Azure-Portals

Ein regionsübergreifender Lastenausgleich stellt sicher, dass ein Dienst global in mehreren Azure-Regionen verfügbar ist. Wenn eine Region ausfällt, wird der Datenverkehr an den nächstgelegenen fehlerfreien Lastenausgleich weitergeleitet.  

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines regionsübergreifenden Lastenausgleichs
> * Erstellen eines Back-End-Pools mit zwei regionalen Lastenausgleichsmodulen
> * Erstellen Sie eine Load Balancer-Regel.
> * Testen des Lastenausgleichs

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

> [!IMPORTANT]
> Die regionsübergreifende Azure Load Balancer-Instanz befindet sich derzeit in der öffentlichen Vorschauphase (Public Preview).
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement.
- Zwei Azure Load Balancer-Instanzen der SKU **Standard** mit Back-End-Pool in zwei verschiedenen Azure-Regionen
    - Informationen zum Erstellen einer regionalen Load Balancer Standard-Instanz virtueller Computer für Back-End-Pools finden Sie unter [Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für den Lastenausgleich virtueller Computer über das Azure-Portal](quickstart-load-balancer-standard-public-portal.md).
        - Fügen Sie den Namen der Lastenausgleichsmodule, virtuellen Computer und anderen Ressourcen in jeder Region mit **-R1** und **-R2** an. 

## <a name="sign-in-to-azure-portal"></a>Anmelden beim Azure-Portal

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-cross-region-load-balancer"></a>Erstellen eines regionsübergreifenden Lastenausgleichs

In diesem Abschnitt erstellen Sie Folgendes: 

* Regionsübergreifender Lastenausgleich
* Front-End mit einer globalen öffentlichen IP-Adresse
* Back-End-Pool mit zwei regionalen Lastenausgleichsmodulen

> [!IMPORTANT]
> Vergewissern Sie sich vor dem Ausführen dieser Schritte, dass zwei regionale Lastenausgleichsmodule mit Back-End-Pools in Ihrem Abonnement bereitgestellt wurden.  Weitere Informationen finden Sie unter **[Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für den Lastenausgleich virtueller Computer über das Azure-Portal](quickstart-load-balancer-standard-public-portal.md)** .

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Load Balancer** aus.

2. Wählen Sie auf der Seite **Load Balancer** die Option **Erstellen** aus.

3. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | **Projektdetails** |    |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie die Option **Neu erstellen** aus, und geben Sie im Textfeld **CreateCRLBTutorial-rg** ein. |
    | **Instanzendetails** |   |
    | Name                   | Geben Sie **myLoadBalancer-CR** ein.                                   |
    | Region         | Wählen Sie **USA, Westen** aus.                                        |
    | type          | Wählen Sie **Öffentlich** aus.                                        |
    | SKU           | Übernehmen Sie den Standardwert **Standard**. |
    | Tarif           | Wählen Sie **Global** aus. |

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Erstellen eines regionsübergreifenden Lastenausgleichs" border="true":::
  
4. Wählen Sie unten auf der Seite **Weiter: Front-End-IP-Konfiguration** aus.

5. Wählen Sie unter **Front-End-IP-Konfiguration** die Option **+ Front-End-IP-Adresse hinzufügen** aus.

6. Geben Sie unter **Front-End-IP-Adresse hinzufügen** ins Feld **Name** den Namen **LoadBalancerFrontend** ein.

7. Wählen Sie unter **IP-Version** die Option **IPv4** oder **IPv6** aus.

8. Wählen Sie unter **Öffentliche IP-Adresse** die Option **Neu erstellen** aus. Geben Sie unter **Name** den Namen **myPublicIP** ein.  Klicken Sie auf **OK**.

9. Wählen Sie **Hinzufügen**.

10. Wählen Sie unten auf der Seite **Weiter: Back-End-Pools** aus.

11. Wählen Sie unter **Back-End-Pools** die Option **+ Back-End-Pool hinzufügen** aus.

12. Geben Sie unter **Back-End-Pool hinzufügen** in das Feld **Name** den Namen **myBackendPool-cr** ein.

13. Wählen Sie unter **Lastenausgleichsmodule** die Option **myLoadBalancer-r1** oder im Dropdownfeld **Lastenausgleich** den ersten regionalen Lastenausgleich aus. Überprüfen Sie, ob die Werte für **Front-End-IP-Konfiguration** und **IP-Adresse** den Werten für **myLoadBalancer-r1** entsprechen.

14. Wählen Sie unter **myLoadBalancer-r2** oder im Dropdownfeld **Lastenausgleich** den zweiten regionalen Lastenausgleich aus. Überprüfen Sie, ob die Werte für **Front-End-IP-Konfiguration** und **IP-Adresse** den Werten für **myLoadBalancer-r2** entsprechen.

15. Wählen Sie **Hinzufügen**.

16. Wählen Sie unten auf der Seite **Weiter: Eingangsregeln** aus.

17. Wählen Sie unter **Eingangsregeln** die Option **+ Lastenausgleichsregel hinzufügen** aus.

18. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHTTPRule-cr** ein. |
    | IP-Version | Wählen Sie unter **IP-Version** die Option **IPv4** oder **IPv6** aus. |
    | Front-End-IP-Adresse | Wählen Sie **LoadBalancerFrontEnd** aus. |
    | Protocol | Wählen Sie **TCP** aus. |
    | Port | Geben Sie **80** ein. |
    | Back-End-Pool | Wählen Sie **myBackendPool-cr** aus. |
    | Sitzungspersistenz | Wählen Sie **Keine**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **15** ein, oder bewegen Sie den Schieberegler auf „15“. |
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**. |
    | Unverankerte IP | Übernehmen Sie den Standardwert **Deaktiviert**. |

19. Wählen Sie **Hinzufügen**.

20. Wählen Sie am unteren Rand der Seite die Option **Bewerten + erstellen** aus.

21. Wählen Sie auf der Registerkarte **Überprüfen und erstellen** die Option **Erstellen** aus.

    > [!NOTE]
    > Regionsübergreifender Lastenausgleich kann nur in den folgenden Startregionen bereitgestellt werden: **USA, Osten 2; USA, Westen, Europa, Westen; Asien, Südosten; USA, Mitte; Europa, Norden; Asien, Osten**. Weitere Informationen finden Sie unter **https://aka.ms/homeregionforglb**.

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

In diesem Abschnitt testen Sie den regionsübergreifenden Lastenausgleich. Sie stellen in einem Webbrowser eine Verbindung mit der öffentlichen IP-Adresse her.  Sie halten die virtuellen Computer in den Back-End-Pools eines der regionalen Lastenausgleiche an und beobachten das Failover.

1. Ermitteln Sie auf dem Bildschirm **Übersicht** die öffentliche IP-Adresse für den Load Balancer. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend **myPublicIP-CR** aus.

2. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Die Standardseite des IIS-Webservers wird im Browser angezeigt.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Testen des Load Balancers" border="true":::

3. Beenden Sie die virtuellen Computer im Back-End-Pool eines der regionalen Lastenausgleichsmodule.

4. Aktualisieren Sie den Webbrowser, und beobachten Sie das Failover der Verbindung zum anderen regionalen Lastenausgleich.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Testen des Lastenausgleichs nach einem Failover" border="true":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Lastenausgleich und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. 

Wählen Sie hierzu die Ressourcengruppe **CreateCRLBTutorial-rg** aus, die die Ressourcen enthält, und wählen Sie anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie:

* einen regionsübergreifenden Lastenausgleich erstellt
* dem Back-End-Pool des regionsübergreifenden Lastenausgleichs regionale Lastenausgleichsmodule hinzugefügt
* eine Lastenausgleichsregel erstellt
* den Lastenausgleich getestet

Weitere Informationen zum regionsübergreifenden Lastenausgleich finden Sie unter folgendem Link:
> [!div class="nextstepaction"]
> [Regionsübergreifender Lastenausgleich (Vorschau)](cross-region-overview.md)
