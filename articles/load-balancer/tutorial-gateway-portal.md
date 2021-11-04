---
title: 'Tutorial: Erstellen einer Gateway Load Balancer-Instanz – Azure-Portal'
titleSuffix: Azure Load Balancer
description: In diesem Tutorial erfahren Sie, wie Sie eine Gateway Load Balancer-Instanz mit dem Azure-Portal erstellen.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 918f2da42a57752db2373e65665804d0a49ad31a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101006"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-the-azure-portal"></a>Tutorial: Erstellen einer Gateway Load Balancer-Instanz mit dem Azure-Portal

Azure Load Balancer umfasst Standard-, Basic- und Gateway-SKUs. Gateway Load Balancer wird zum transparenten Einfügen von virtuellen Netzwerkgeräten (Network Virtual Appliances, NVA) verwendet. Verwenden Sie Gateway Load Balancer für Szenarien, die hohe Leistung und hohe Skalierbarkeit von NVAs benötigen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Registrieren einer Previewfunktion.
> * Erstellen Sie ein virtuelles Netzwerk.
> * Erstellen einer Netzwerksicherheitsgruppe.
> * Erstellen einer Gateway Load Balancer-Instanz.
> * Verketten eines Load Balancer-Front-Ends mit der Gateway Load Balancer-Instanz.

> [!IMPORTANT]
> Die Azure Gateway Load Balancer befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine vorhandene öffentliche Azure Load Balancer-Instanz mit Standard-SKU. Weitere Informationen zum Erstellen eines Lastenausgleichs finden Sie im Artikel zum **[Erstellen eines öffentlichen Lastenausgleichs mit dem Azure-Portal](quickstart-load-balancer-standard-public-portal.md)** .
    - Für dieses Tutorial wird in den Beispielen **myLoadBalancer** als Name für den Lastenausgleich verwendet.

## <a name="register-preview-feature"></a>Registrieren einer Previewfunktion

Im Rahmen der öffentlichen Vorschau muss der Anbieter in Ihrem Azure-Abonnement registriert sein. Verwenden Sie die folgenden PowerShell- oder Azure CLI-Beispiele, um Ihr Abonnement zu aktivieren.

### <a name="powershell"></a>PowerShell

Verwenden Sie [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature), um das **AllowGatewayLoadBalancer**-Anbieterfeature zu registrieren:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowGatewayLoadBalancer

```

Verwenden Sie [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider), um den **Microsoft.Network**-Ressourcenanbieter zu registrieren:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network

```

### <a name="azure-cli"></a>Azure CLI

Verwenden Sie [az feature register](/cli/azure/feature#az_feature_register), um das **AllowGatewayLoadBalancer**-Anbieterfeature zu registrieren:

```azurecli-interactive
  az feature register \
    --name AllowGatewayLoadBalancer \
    --namespace Microsoft.Network
```

Verwenden Sie [az provider register](/cli/azure/provider#az_provider_register), um den **Microsoft.Network**-Ressourcenanbieter zu registrieren:

```azurecli-interactive
  az provider register \
    --namespace Microsoft.Network
```

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://preview.portal.azure.com](https://preview.portal.azure.com) beim Azure-Portal an.

## <a name="create-virtual-network"></a>Virtuelles Netzwerk erstellen

Für die Ressourcen, die sich im Back-End-Pool der Gateway Load Balancer-Instanz befinden, ist ein virtuelles Netzwerk erforderlich. 

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtuelles Netzwerk** ein. Wählen Sie in den Suchergebnissen **Virtuelle Netzwerke** aus.

2. Wählen Sie unter **Virtuelle Netzwerke** die Option **+ Erstellen** aus.

3. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **Neu erstellen**. </br> Geben Sie in **Name** den Wert **TutorGwLB-rg** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **USA, Osten** aus. |

4. Wählen Sie die Registerkarte **IP-Adressen** oder die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

5. Geben Sie auf der Registerkarte **IP-Adressen** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | IPv4-Adressraum | Geben Sie **10.1.0.0/16** ein. |

6. Wählen Sie unter **Subnetzname** das Wort **Standard** aus.

7. Geben Sie unter **Subnetz bearbeiten** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Subnetzname | Geben Sie **myBackendSubnet** ein. |
    | Subnetzadressbereich | Geben Sie **10.1.0.0/24** ein. |

8. Wählen Sie **Speichern** aus.

9. Wählen Sie die Registerkarte **Sicherheit** .

10. Wählen Sie unter **BastionHost** die Option **Aktivieren** aus. Geben Sie die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Bastion-Name | Geben Sie **myBastionHost** ein. |
    | AzureBastionSubnet-Adressraum | Geben Sie **10.1.1.0/27** ein. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. </br> Geben Sie **myBastionIP** als **Name** ein. </br> Klicken Sie auf **OK**. |


11. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

12. Klicken Sie auf **Erstellen**.

## <a name="create-nsg"></a>NSG erstellen

Verwenden Sie das folgende Beispiel, um eine Netzwerksicherheitsgruppe zu erstellen. Sie konfigurieren die NSG-Regeln, die für den Netzwerkdatenverkehr in dem zuvor erstellten virtuellen Netzwerk erforderlich sind.

1. Geben Sie im Suchfeld oben im Portal **Netzwerksicherheit**  ein. Wählen Sie in den Suchergebnissen **Netzwerksicherheitsgruppen** aus.

2. Wählen Sie **+ Erstellen** aus.

3. Geben Sie auf der Registerkarte **Grundlagen** von **Netzwerksicherheitsgruppe erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Ressourcengruppe | Wählen Sie **TutorGwLB-rg** aus. |
    | **Instanzendetails** |   |
    | Name | Geben Sie **myNSG** ein. |
    | Region | Wählen Sie **USA, Osten** aus. |

4. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

5. Klicken Sie auf **Erstellen**.

6. Geben Sie im Suchfeld oben im Portal **Netzwerksicherheit**  ein. Wählen Sie in den Suchergebnissen **Netzwerksicherheitsgruppen** aus.

7. Wählen Sie **myNSG** aus.

8. Wählen Sie in den **Einstellungen** für **myNSG** die Option **Eingangssicherheitsregeln** aus.

9. Wählen Sie **+ Hinzufügen**.

10. Geben Sie unter **Eingangssicherheitsregel hinzufügen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | `Source` | Übernehmen Sie den Standardwert **Beliebig**. |
    | Source port ranges | Übernehmen Sie den Standardwert **'*'** . |
    | Destination | Übernehmen Sie den Standardwert **Beliebig**. |
    | Dienst | Übernehmen Sie den Standardwert **Benutzerdefiniert**. |
    | Zielportbereiche | Geben Sie **'*'** ein. |
    | Protocol | Wählen Sie **Alle** aus. |
    | Aktion | Übernehmen Sie den Standardwert **Zulassen**. |
    | Priorität | Geben Sie **100** ein. | 
    | Name | Geben Sie **myNSGRule-AllowAll-All** ein. |

11. Wählen Sie **Hinzufügen**.

12. Wählen Sie in den **Einstellungen** die Option **Ausgangssicherheitsregeln** aus.

13. Wählen Sie **+ Hinzufügen**.

14. Geben Sie unter **Ausgangssicherheitsregel hinzufügen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | `Source` | Übernehmen Sie den Standardwert **Beliebig**. |
    | Source port ranges | Übernehmen Sie den Standardwert **'*'** . |
    | Destination | Übernehmen Sie den Standardwert **Beliebig**. |
    | Dienst | Übernehmen Sie den Standardwert **Benutzerdefiniert**. |
    | Zielportbereiche | Geben Sie **'*'** ein. |
    | Protocol | Wählen Sie **TCP** aus. |
    | Aktion | Übernehmen Sie den Standardwert **Zulassen**. |
    | Priorität | Geben Sie **100** ein. | 
    | Name | Geben **myNSGRule-AllowAll-TCP-Out** ein. |

15. Wählen Sie **Hinzufügen**.

Wählen Sie diese NSG aus, wenn Sie die NVAs für Ihre Bereitstellung erstellen.

## <a name="create-gateway-load-balancer"></a>Erstellen einer Gateway Load Balancer-Instanz

In diesem Abschnitt erstellen Sie die Konfiguration und stellen Sie die Gateway Load Balancer-Instanz bereit. 

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Wählen Sie auf der Seite **Load Balancer** die Option **Erstellen** aus.

3. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | **Projektdetails** |   |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Ressourcengruppe         | Wählen Sie **TutorGwLB-rg** aus. |
    | **Instanzendetails** |   |
    | Name                   | Geben Sie **myLoadBalancer-gw** ein.                                   |
    | Region         | Wählen Sie **(USA) USA, Osten** aus.                                        |
    | type          | Wählen Sie **Intern** aus.                                        |
    | SKU           | Wählen Sie **Gateway** aus. |

    :::image type="content" source="./media/tutorial-gateway-portal/create-load-balancer.png" alt-text="Screenshot: Registerkarte „Grundlagen“ zum Erstellen einer Load Balancer Standard-Instanz" border="true":::

4. Wählen Sie unten auf der Seite **Weiter: Front-End-IP-Konfiguration** aus.

5. Wählen Sie unter **Front-End-IP-Konfiguration** die Option **+ Front-End-IP-Adresse hinzufügen** aus.

6. Geben Sie **MyFrontEnd** in **Name** ein.

7. Wählen Sie unter **Subnetz** den Eintrag **myBackendSubnet** aus.

8. Wählen Sie **Dynamisch** für die **Zuweisung** aus.

9. Wählen Sie **Hinzufügen**.

10. Wählen Sie unten auf der Seite **Weiter: Back-End-Pools** aus.

11. Wählen Sie auf der Registerkarte **Back-End-Pools** die Option **+ Back-End-Pool hinzufügen** aus.

12. Geben Sie unter **Back-End-Pool hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myBackendPool** ein. |
    | Konfiguration des Back-End-Pools | Wählen Sie **Netzwerkschnittstelle** aus. |
    | IP-Version | Wählen Sie **IPv4** aus. |
    | **Konfiguration der Azure Load Balancer-Instanz** |   |
    | type | Wählen Sie **Intern und extern** aus. |
    | Interner Port | Übernehmen Sie den Standardwert **10800**. |
    | Interner Bezeichner | Übernehmen Sie den Standardwert **800**. |
    | Externer Port | Übernehmen Sie den Standardwert **10801**. |
    | Externer Bezeichner | Übernehmen Sie den Standardwert **801**. |

13. Wählen Sie **Hinzufügen**.

14. Wählen Sie unten auf der Seite die Schaltfläche **Weiter: Regeln für eingehenden Datenverkehr** aus.

15. Wählen Sie unter **Lastenausgleichsregel** auf der Registerkarte **Regeln für eingehenden Datenverkehr** die Option **+ Lastenausgleichsregel hinzufügen** aus.

16. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myLBRule** ein. |
    | IP-Version | Wählen Sie abhängig von Ihren Anforderungen **IPv4** oder **IPv6** aus. |
    | Front-End-IP-Adresse | Wählen Sie **MyFrontend** aus. |
    | Back-End-Pool | Wählen Sie **myBackendPool** aus. |
    | Integritätstest | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** den Namen **myHealthProbe** ein. </br> Wählen Sie für **Protokoll** die Option **HTTP** aus. </br> Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **OK** aus. |
    | Sitzungspersistenz | Wählen Sie **Keine**. |

    :::image type="content" source="./media/tutorial-gateway-portal/add-load-balancing-rule.png" alt-text="Screenshot: Erstellen der Lastenausgleichsregel." border="true":::

17. Wählen Sie **Hinzufügen**.

18. Klicken Sie unten auf der Seite auf die blaue Schaltfläche **Überprüfen + erstellen**.

19. Klicken Sie auf **Erstellen**.

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>Hinzufügen virtueller Netzwerkgeräte zum Back-End-Pool der Gateway Load Balancer-Instanz
Stellen Sie NVAs über den Azure Marketplace bereit. Fügen Sie die virtuellen NVA-Computer nach der Bereitstellung dem Back-End-Pool hinzu, indem Sie zur Registerkarte „Back-End-Pools“ Ihrer Gateway Load Balancer-Instanz navigieren.

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>Verketten eines Load Balancer-Front-Ends mit der Gateway Load Balancer-Instanz

In diesem Beispiel verketten Sie das Front-End eines Standardlastenausgleichs mit der Gateway Load Balancer-Instanz. 

Sie fügen das Front-End zur Front-End-IP eines vorhandenen Lastenausgleichs in Ihrem Abonnement hinzu.

1. Geben Sie im Azure-Portal im Suchfeld **Lastenausgleich** ein. Wählen Sie in den Suchergebnissen **Lastenausgleich** aus.

2. Wählen Sie unter **Lastenausgleichsmodule** die Option **myLoadBalancer** oder Ihren vorhandenen Lastenausgleich aus.

3. Wählen Sie in den **Einstellungen** die Option **Front-End-IP-Konfiguration** aus.

4. Wählen Sie die Front-End-IP-Adresse des Lastenausgleichs aus. In diesem Beispiel lautet der Name des Front-Ends **myFrontendIP**.

    :::image type="content" source="./media/tutorial-gateway-portal/frontend-ip.png" alt-text="Screenshot: Front-End-IP-Konfiguration." border="true":::

5. Wählen Sie **myFrontendIP (10.1.0.4)** im Pull-Down-Feld neben **Gateway Load Balancer** aus.

6. Wählen Sie **Speichern** aus.

    :::image type="content" source="./media/tutorial-gateway-portal/select-gateway-load-balancer.png" alt-text="Screenshot: Hinzufügen der Gateway Load Balancer-Instanz zur Front-End-IP." border="true":::


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Lastenausgleich und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie hierzu die Ressourcengruppe **TutorGwLB-rg** aus, die die Ressourcen enthält, und wählen Sie anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Erstellen von virtuellen Netzwerkgeräten in Azure. 

Wählen Sie beim Erstellen der NVAs die in diesem Tutorial erstellten Ressourcen aus:

* Virtuelles Netzwerk

* Subnet

* Netzwerksicherheitsgruppe

* Gateway Load Balancer

Fahren Sie mit dem nächsten Artikel fort, um sich über das Erstellen einer regionsübergreifenden Azure Load Balancer-Instanz zu informieren:
> [!div class="nextstepaction"]
> [Regionsübergreifender Lastenausgleich](tutorial-cross-region-powershell.md)
