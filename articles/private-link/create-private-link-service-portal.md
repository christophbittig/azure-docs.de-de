---
title: 'Schnellstart: Erstellen eines Private Link-Diensts über das Azure-Portal'
titlesuffix: Azure Private Link
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie über das Azure-Portal einen Private Link-Dienst erstellen.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 08/18/2021
ms.author: allensu
ms.openlocfilehash: de7d106ae02e1150d9765e60fbfdf0dc0cb9bc74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810322"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Schnellstart: Erstellen eines Private Link-Diensts über das Azure-Portal

Führen Sie die ersten Schritte zum Erstellen eines Private Link-Diensts für Ihren Dienst aus.  Gewähren Sie für Private Link den Zugriff auf Ihren Dienst oder Ihre Ressource hinter Azure Load Balancer Standard.  Benutzer Ihres Diensts verfügen über privaten Zugriff aus ihrem virtuellen Netzwerk.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-an-internal-load-balancer"></a>Erstellen eines internen Load Balancers

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und eine interne Azure Load Balancer-Instanz.

### <a name="virtual-network"></a>Virtuelles Netzwerk

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und das Subnetz zum Hosten des Lastenausgleichs, der auf Ihren Private Link-Dienst zugreift.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk > Virtuelles Netzwerk** aus, oder suchen Sie über das Suchfeld nach **Virtuelles Netzwerk**.

3. Klicken Sie auf **Erstellen**. 

4. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **Neu erstellen**. Geben Sie **CreatePrivLinkService-rg** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **(USA) USA, Osten 2** aus. |

5. Wählen Sie die Registerkarte **IP-Adressen** oder die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

6. Geben Sie auf der Registerkarte **IP-Adressen** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | IPv4-Adressraum | Geben Sie **10.1.0.0/16** ein. |

7. Wählen Sie unter **Subnetzname** das Wort **Standard** aus.

8. Geben Sie unter **Subnetz bearbeiten** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Subnetzname | Geben Sie **myBackendSubnet** ein. |
    | Subnetzadressbereich | Geben Sie **10.1.0.0/24** ein. |

9. Wählen Sie **Speichern** aus.

10. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

11. Klicken Sie auf **Erstellen**.

### <a name="create-nat-gateway"></a>Erstellen eines NAT Gateways

In diesem Abschnitt wird ein NAT-Gateway erstellt und dem Subnetz in dem virtuellen Netzwerk zugewiesen, das Sie zuvor erstellt haben.

1. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk> NAT-Gateway** aus, oder suchen Sie über das Suchfeld nach **NAT-Gateway**.

2. Klicken Sie auf **Erstellen**. 

3. Geben Sie auf der Registerkarte **Grundlagen** unter **Gateway für die Netzwerkadressübersetzung (NAT) erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Wählen Sie Ihr Azure-Abonnement.                                  |
    | Ressourcengruppe   | Wählen Sie **CreatePrivLinkService-rg** aus. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myNATGateway** ein.                                    |
    | Region           | Wählen Sie **(USA) USA, Osten 2** aus.  |
    | Verfügbarkeitszone | Wählen Sie **Keine**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **10** ein. |

4. Wählen Sie die Registerkarte **Ausgehende IP-Adresse** oder unten auf der Seite die Schaltfläche **Weiter: Ausgehende IP-Adresse** aus.

5. Geben Sie auf der Registerkarte **Ausgehende IP-Adresse** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | **Einstellung** | **Wert** |
    | ----------- | --------- |
    | Öffentliche IP-Adressen | Wählen Sie **Neue öffentliche IP-Adresse erstellen** aus. </br> Geben Sie unter **Name** den Namen **myNATgatewayIP** ein. </br> Klicken Sie auf **OK**. |

6. Wählen Sie die Registerkarte **Subnetz** oder unten auf der Seite die Schaltfläche **Weiter: Subnetz** aus.

7. Wählen Sie auf der Registerkarte **Subnetz** in der Dropdownliste **Virtuelles Netzwerk** die Option **myVNet** aus.

8. Aktivieren Sie das Kontrollkästchen neben **myBackendSubnet**.

9. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

10. Klicken Sie auf **Erstellen**.

### <a name="create-load-balancer"></a>Erstellen eines Load Balancers

In diesem Abschnitt wird ein Lastenausgleich für virtuelle Computer erstellt.

Während der Erstellung des Lastenausgleichs konfigurieren Sie Folgendes:

* Front-End-IP-Adresse
* Back-End-Pool
* Lastenausgleichsregeln für eingehenden Datenverkehr

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Wählen Sie auf der Seite **Load Balancer** die Option **Erstellen** aus.

3. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | **Projektdetails** |   |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie **CreatePrivLinkService-rg** aus. |
    | **Instanzendetails** |   |
    | Name                   | Geben Sie **myLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **(USA) USA, Osten 2** aus.                                        |
    | type          | Wählen Sie **Intern** aus.                                        |
    | SKU           | Übernehmen Sie den Standardwert **Standard**. |

4. Wählen Sie unten auf der Seite **Weiter: Front-End-IP-Konfiguration** aus.

5. Wählen Sie unter **Front-End-IP-Konfiguration** die Option **+ Front-End-IP-Adresse hinzufügen** aus.

6. Geben Sie unter **Name** den Wert **LoadBalancerFrontend** ein.

7. Wählen Sie unter **Subnetz** den Eintrag **myBackendSubnet** aus.

8. Wählen Sie **Dynamisch** für die **Zuweisung** aus.

9. Wählen Sie unter **Verfügbarkeitszone** die Option **Zonenredundant** aus.

    > [!NOTE]
    > In Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) haben Sie die Möglichkeit, „Keine Zone“ (Standardoption), eine bestimmte Zone oder die Option „Zonenredundant“ auszuwählen. Die Auswahl hängt von Ihren spezifischen Domänenfehleranforderungen ab. In Regionen ohne Verfügbarkeitszonen wird dieses Feld nicht angezeigt. </br> Weitere Informationen zu Verfügbarkeitszonen finden Sie in der [Übersicht über Verfügbarkeitszonen](../availability-zones/az-overview.md).

10. Wählen Sie **Hinzufügen**.

11. Wählen Sie unten auf der Seite **Weiter: Back-End-Pools** aus.

12. Wählen Sie auf der Registerkarte **Back-End-Pools** die Option **+ Back-End-Pool hinzufügen** aus.

13. Geben Sie unter **Back-End-Pool hinzufügen** in das Feld **Name** den Namen **myBackendPool** ein.

14. Wählen Sie unter **Backend Pool Configuration** (Konfiguration des Back-End-Pools) die Option **NIC** oder **IP-Adresse** aus.

15. Wählen Sie unter **IP-Version** die Option **IPv4** oder **IPv6** aus.

16. Wählen Sie **Hinzufügen**.

17. Wählen Sie unten auf der Seite die Schaltfläche **Weiter: Regeln für eingehenden Datenverkehr** aus.

18. Wählen Sie unter **Lastenausgleichsregel** auf der Registerkarte **Regeln für eingehenden Datenverkehr** die Option **+ Lastenausgleichsregel hinzufügen** aus.

19. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHTTPRule** ein. |
    | IP-Version | Wählen Sie abhängig von Ihren Anforderungen **IPv4** oder **IPv6** aus. |
    | Front-End-IP-Adresse | Wählen Sie **LoadBalancerFrontEnd** aus. |
    | Protocol | Wählen Sie **TCP** aus. |
    | Port | Geben Sie **80** ein. |
    | Back-End-Port | Geben Sie **80** ein. |
    | Back-End-Pool | Wählen Sie **myBackendPool** aus. |
    | Integritätstest | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** den Namen **myHealthProbe** ein. </br> Wählen Sie für **Protokoll** die Option **HTTP** aus. </br> Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **OK** aus. |
    | Sitzungspersistenz | Wählen Sie **Keine**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **15** ein, oder wählen Sie diesen Wert aus. |
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**. |
    | Unverankerte IP | Wählen Sie **Deaktiviert** aus. |

20. Wählen Sie **Hinzufügen**.

21. Klicken Sie unten auf der Seite auf die blaue Schaltfläche **Überprüfen + erstellen**.

22. Klicken Sie auf **Erstellen**.

## <a name="create-a-private-link-service"></a>Erstellen eines Private Link-Diensts

In diesem Abschnitt erstellen Sie einen Private Link-Dienst hinter einem Standardlastenausgleich.

1. Wählen Sie im Azure-Portal oben links auf der Seite die Option **Ressource erstellen** aus.

2. Suchen Sie im Feld **Marketplace durchsuchen** nach **Private Link**.

3. Klicken Sie auf **Erstellen**.

4. Wählen Sie in der **Übersicht** unter **Private Link Center** die blaue Schaltfläche **Create private link service** (Private Link-Dienst erstellen) aus.

5. Geben Sie auf der Registerkarte **Grundlagen** unter **Create private link service** (Private Link-Dienst erstellen) die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |  |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Ressourcengruppe | Wählen Sie **CreatePrivLinkService-rg** aus. |
    | **Instanzendetails** |  |
    | Name | Geben Sie **myPrivateLinkService** ein. |
    | Region | Wählen Sie **(USA) USA, Osten 2** aus. |

6. Wählen Sie die Registerkarte **Ausgangseinstellungen** oder unten auf der Seite die Option **Weiter: Ausgangseinstellungen** aus.

7. Geben Sie auf der Registerkarte **Ausgangseinstellungen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Load Balancer | Wählen Sie **myLoadBalancer** aus. |
    | Front-End-IP-Adresse des Lastenausgleichs | Wählen Sie **LoadBalancerFrontEnd (10.1.0.4)** aus. |
    | NAT-Quellsubnetz | Wählen Sie **mySubnet (10.1.0.0/24)** aus. |
    | TCP-Proxy V2 aktivieren | Übernehmen Sie den Standardwert **Nein**. </br> Wählen Sie **Ja** aus, falls Ihre Anwendung einen TCP-Proxy V2-Header erwartet. |
    | **Einstellungen für private IP-Adressen** |  |
    | Übernehmen Sie die Standardeinstellungen. |  |

8. Wählen Sie die Registerkarte **Zugriffssicherheit** oder unten auf der Seite die Option **Weiter: Zugriffssicherheit** aus.

9. Übernehmen Sie auf der Registerkarte **Zugriffssicherheit** die Standardeinstellung **Nur rollenbasierte Zugriffssteuerung**.

10. Wählen Sie die Registerkarte **Tags** aus, oder **Weiter: Tags** unten auf der Seite.

11. Wählen Sie die Registerkarte **Überprüfen und erstellen** oder unten auf der Seite die Option **Weiter: Überprüfen + erstellen** aus.

12. Wählen Sie auf der Registerkarte **Überprüfen und erstellen** die Option **Erstellen** aus.

Ihr Private Link-Dienst wird erstellt und kann Datenverkehr empfangen. Konfigurieren Sie Ihre Anwendung hinter Ihrer Instanz von Load Balancer Standard, falls Sie den Datenverkehrsfluss anzeigen möchten.

## <a name="create-private-endpoint"></a>Erstellen eines privaten Endpunkts

In diesem Abschnitt ordnen Sie den Private Link-Dienst einem privaten Endpunkt zu. Ein virtuelles Netzwerk enthält den privaten Endpunkt für den Private Link-Dienst. In diesem virtuellen Netzwerk sind die Ressourcen enthalten, die auf Ihren Private Link-Dienst zugreifen.

### <a name="create-private-endpoint-virtual-network"></a>Erstellen eines virtuellen Netzwerks des privaten Endpunkts

1. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk > Virtuelles Netzwerk** aus, oder suchen Sie über das Suchfeld nach **Virtuelles Netzwerk**.

2. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **CreatePrivLinkService-rg** aus. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNetPE** ein.                                    |
    | Region           | Wählen Sie **(USA) USA, Osten 2** aus. |

3. Wählen Sie die Registerkarte **IP-Adressen** oder die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

4. Geben Sie auf der Registerkarte **IP-Adressen** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | IPv4-Adressraum | Geben Sie **11.1.0.0/16** ein. |

5. Wählen Sie unter **Subnetzname** das Wort **Standard** aus.

6. Geben Sie unter **Subnetz bearbeiten** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Subnetzname | Geben Sie **mySubnetPE** ein. |
    | Subnetzadressbereich | Geben Sie **11.1.0.0/24** ein. |

7. Wählen Sie **Speichern** aus.

8. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

9. Klicken Sie auf **Erstellen**.

### <a name="create-private-endpoint"></a>Erstellen eines privaten Endpunkts

1. Wählen Sie auf der oberen linken Seite des Bildschirms im Portal die Option **Ressource erstellen** > **Netzwerk** > **Private Link** aus, oder geben Sie im Suchfeld **Private Link** ein.

2. Wählen Sie **Erstellen** aus.

3. Wählen Sie in **Private Link-Center** im linken Menü **Private Endpunkte** aus.

4. Wählen Sie unter **Private Endpunkte** die Option **+ Hinzufügen** aus.

5. Geben Sie auf der Registerkarte **Grundlagen** unter **Privaten Endpunkt erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **CreatePrivLinkService-rg** aus. Diese Ressourcengruppe wurde im vorherigen Abschnitt erstellt.|
    | **Instanzendetails** |  |
    | Name  | Geben Sie **myPrivateEndpoint** ein. |
    | Region | Wählen Sie **(USA) USA, Osten 2** aus. |

6. Wählen Sie die Registerkarte **Ressource** oder unten auf der Seite **Weiter: Ressource** aus.
    
7. Geben Sie in **Ressource** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Verbindungsmethode | Wählen Sie **Hiermit wird eine Verbindung mit einer Azure-Ressource im eigenen Verzeichnis hergestellt** aus. |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Ressourcentyp | Wählen Sie **Microsoft.Network/privateLinkServices** aus. |
    | Resource | Wählen Sie **myPrivateLinkService** aus. |

8. Wählen Sie die Registerkarte **Konfiguration** oder die Schaltfläche **Weiter: Konfiguration** am unteren Bildschirmrand aus.

9. Geben Sie diese Informationen in **Konfiguration** ein oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Netzwerk** |  |
    | Virtual Network | Wählen Sie **myVNetPE** aus. |
    | Subnet | Wählen Sie **mySubnetPE** aus. |

10. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder am unteren Bildschirmrand die Schaltfläche **Überprüfen + erstellen** aus.

11. Wählen Sie **Erstellen** aus.

### <a name="ip-address-of-private-endpoint"></a>IP-Adresse des privaten Endpunkts

In diesem Abschnitt finden Sie die IP-Adresse des privaten Endpunkts, die zum Lastenausgleich und Private Link-Dienst gehört.

1. Wählen Sie in der linken Spalte des Azure-Portals die Option **Ressourcengruppen** aus.

2. Wählen Sie die Ressourcengruppe **CreatePrivLinkService-rg** aus.

3. Wählen Sie in der Ressourcengruppe **CreatePrivLinkService-rg** die Option **myPrivateEndpoint** aus.

4. Wählen Sie auf der Seite **Übersicht** von **myPrivateEndpoint** den Namen der Netzwerkschnittstelle aus, die dem privaten Endpunkt zugeordnet ist.  Der Name der Netzwerkschnittstelle beginnt mit **myPrivateEndpoint.nic**.

5. Auf der Seite **Übersicht** der NIC des privaten Endpunkts wird unter **Private IP-Adresse** die IP-Adresse des Endpunkts angezeigt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Private Link-Dienst nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die im Rahmen dieser Schnellstartanleitung verwendeten Ressourcen zu bereinigen.

1. Geben Sie im Portal oben im Suchfeld den Suchbegriff **CreatePrivLinkService-rg** ein, und wählen Sie in den Suchergebnissen den Eintrag **CreatePrivLinkService-rg** aus.

2. Wählen Sie die Option **Ressourcengruppe löschen**.

3. Geben Sie unter **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN** den Namen **CreatePrivLinkService-rg** ein.

4. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Folgendes durchgeführt:

* Erstellen eines virtuellen Netzwerks und einer internen Azure Load Balancer-Instanz
* Erstellen eines Private Link-Diensts
* Erstellen eines virtuellen Netzwerks und eines privaten Endpunkts für den Private Link-Dienst

Weitere Informationen zu privaten Azure-Endpunkten finden Sie unter:
> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines privaten Endpunkts mit dem Azure-Portal](create-private-endpoint-portal.md)
