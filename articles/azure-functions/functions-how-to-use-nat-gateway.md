---
title: Steuern der ausgehenden IP-Adresse von Azure Functions mithilfe eines Azure Virtual Network NAT-Gateways
description: In diesem ausführlichen Tutorial erfahren Sie Schritt für Schritt, wie Sie die NAT für eine mit einem virtuellen Azure-Netzwerk verbundene Funktion konfigurieren.
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 0223ebe7401745fc9dfff9b3935b5f2252cfa39d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265406"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>Tutorial: Steuern der ausgehenden IP-Adresse von Azure Functions mithilfe eines Azure Virtual Network NAT-Gateways

Virtual Network NAT (Network Address Translation, Netzwerkadressenübersetzung) vereinfacht die Einrichtung von ausschließlich ausgehender Internetkonnektivität für virtuelle Netzwerke. Bei der Konfiguration in einem Subnetz werden für die gesamte Konnektivität in ausgehender Richtung die von Ihnen angegebenen statischen öffentlichen IP-Adressen verwendet. Eine NAT kann bei Azure Functions oder Web-Apps hilfreich sein, die auf einen Drittanbieterdienst angewiesen sind, von dem als Sicherheitsmaßnahme eine Positivliste mit IP-Adressen verwendet wird. Weitere Informationen finden Sie unter [Was ist Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md).

In diesem Tutorial erfahren Sie, wie Sie virtuelle Virtual Network NAT verwenden, um ausgehenden Datenverkehr einer über HTTP ausgelösten Funktion weiterzuleiten. Diese Funktion ermöglicht die Überprüfung der eigenen ausgehenden IP-Adresse. Dieses Tutorial umfasst Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks
> * Erstellen einer Funktions-App im Premium-Tarif
> * Erstellen einer öffentlichen IP-Adresse
> * Erstellen eines NAT-Gateways
> * Konfigurieren der Funktions-App zum Weiterleiten von ausgehendem Datenverkehr über das NAT-Gateway

## <a name="topology"></a>Topologie

Das folgende Diagramm zeigt die Architektur der Lösung, die Sie erstellen:

![Benutzeroberfläche für die NAT-Gatewayintegration](./media/functions-how-to-use-nat-gateway/topology.png)

Funktionen, die im Premium-Plan ausgeführt werden, bieten dieselben Hostingfunktionen wie Web-Apps in Azure App Service, wozu auch ein Feature zur VNET-Integration gehört. Weitere Informationen zur VNET-Integration, einschließlich Problembehandlung und erweiterter Konfiguration, finden Sie unter [Integrieren Ihrer App in ein Azure Virtual Network](../app-service/overview-vnet-integration.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial ist es wichtig, dass Sie die Funktionsweise und Verwendung von IP-Adressen und Subnetzen verstehen. Beginnen Sie mit [diesem Artikel zu den Grundlagen von Adressen und Subnetzen](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Viele weitere Artikel und Videos finden Sie online.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Wenn Sie bereits das Tutorial [Integrieren von Azure Functions in ein virtuelles Azure-Netzwerk mithilfe privater Endpunkte](./functions-create-vnet.md) absolviert haben, können Sie direkt mit dem [Erstellen einer HTTP-Triggerfunktion](#create-function) fortfahren.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

1. Wählen Sie im Menü des Azure-Portals die Option **Ressource erstellen** aus. Wählen Sie im Azure Marketplace die Option **Netzwerk** > **Virtuelles Netzwerk** aus.

1. Geben Sie unter **Virtuelles Netzwerk erstellen** die Einstellungen aus der folgenden Tabelle ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Subscription | Wählen Sie Ihr Abonnement aus.|
    | Resource group | Wählen Sie **Neue erstellen** aus, geben Sie *myResourceGroup* ein, und wählen Sie **OK** aus. |
    | Name | Geben Sie *myResourceGroup-vnet* ein. |
    | Standort | Wählen Sie **USA, Osten** aus.|

1. Wählen Sie **Weiter: IP-Adressen** aus, und geben Sie unter **IPv4-Adressraum** den Adressraum *10.10.0.0/16* ein.

1. Wählen Sie **Subnetz hinzufügen** aus, und geben Sie unter **Subnetzname** den Namen *Tutorial-Net* und unter **Subnetzadressbereich** den Bereich *10.10.1.0/24* ein.

    ![Registerkarte „IP-Adressen“ für die VNET-Erstellung](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. Wählen Sie **Hinzufügen** und anschließend **Überprüfen + erstellen** aus. Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **Erstellen** aus.

1. Wählen Sie unter **Virtuelles Netzwerk erstellen** die Option **Erstellen** aus.

Erstellen Sie als Nächstes eine Funktions-App im [Premium-Tarif](functions-premium-plan.md). Dieser Plan ermöglicht eine Skalierung ohne Server und unterstützt gleichzeitig die Integration in ein virtuelles Netzwerk.

## <a name="create-a-function-app-in-a-premium-plan"></a>Erstellen einer Funktions-App in einem Premium-Tarif

In diesem Tutorial erfahren Sie, wie Sie Ihre Funktions-App in einem [Premium-Plan](functions-premium-plan.md)erstellen. Die gleiche Funktionalität ist auch verfügbar, wenn ein [Dedizierter (App Service)-Plan](dedicated-plan.md) verwendet wird.

> [!NOTE]  
> Wählen Sie .NET als Runtimestapel und Windows als Betriebssystem aus, um in diesem Tutorial optimale Ergebnisse zu erzielen. Erstellen Sie Ihre Funktions-App außerdem in der gleichen Region wie Ihr virtuelles Netzwerk.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>Verbinden Ihrer Funktions-App mit dem virtuellen Netzwerk

Nun können Sie Ihre Funktions-App mit dem virtuellen Netzwerk verbinden.

1. Wählen Sie in Ihrer Funktions-App im linken Menü die Option **Netzwerk** und anschließend unter **VNET-Integration** die Option **Klicken Sie hier, um die Konfiguration auszuführen.** aus.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="Auswählen von „Netzwerk“ in der Funktions-App":::

1. Wählen Sie auf der Seite **VNET-Integration** die Option **VNET hinzufügen** aus.

1. Verwenden Sie unter **Netzwerkfeaturestatus** die Einstellungen in der Tabelle unterhalb der Abbildung:

    ![Definieren des virtuellen Netzwerks für die Funktions-App](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | Das virtuelle Netzwerk ist dasjenige, das Sie zuvor erstellt haben. |
    | **Subnetz** | Neues Subnetz erstellen | Erstellen Sie ein Subnetz in dem virtuellen Netzwerk zur Verwendung durch Ihre Funktions-App. Die VNET-Integration muss so konfiguriert werden, dass ein leeres Subnetz verwendet wird. |
    | **Subnetzname** | Function-Net | Name des neuen Subnetzes. |
    | **Adressblock des virtuellen Netzwerks** | 10.10.0.0/16 | Es sollte nur ein Adressblock definiert sein. |
    | **Subnetzadressblock** | 10.10.2.0/24   | Die Subnetzgröße begrenzt die Gesamtanzahl von Instanzen, auf die Ihre Funktions-App im Premium-Plan aufskaliert werden kann. Dieses Beispiel verwendet ein `/24`-Subnetz mit 254 verfügbaren Hostadressen. Dieses Subnetz ist überdimensioniert, aber einfach zu berechnen. |

1. Klicken Sie auf **OK**, um das Subnetz hinzuzufügen. Schließen Sie die Seiten **VNET-Integration** und **Netzwerkfeaturestatus**, um zur Seite Ihrer Funktions-App zurückzukehren.

Die Funktions-App hat nun Zugriff auf das virtuelle Netzwerk. Fügen Sie der Funktions-App als Nächstes eine über HTTP ausgelöste Funktion hinzu.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Erstellen einer HTTP-Triggerfunktion

1. Wählen Sie im linken Menü des Fensters **Funktionen** die Option **Funktionen** aus, und wählen Sie dann im obersten Menü **Hinzufügen** aus. 
 
1. Wählen Sie im Fenster **Neue Funktion** die Option **HTTP-Trigger** aus, und übernehmen Sie den Standardnamen für **Neue Funktion**, oder geben Sie einen neuen Namen ein. 

1. Ersetzen Sie unter **Programmieren und testen** den mittels Vorlage generierten Code des C#-Skripts (.csx) durch den folgenden Code: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    Durch diesen Code wird eine externe Website aufgerufen, die die IP-Adresse des Aufrufers (in diesem Fall: die verwendete Funktion) zurückgibt. Dadurch können Sie ganz einfach die ausgehende IP-Adresse ermitteln, die von Ihrer Funktions-App verwendet wird.

Nun können Sie die Funktion ausführen und die aktuellen ausgehenden IP-Adressen überprüfen.

## <a name="verify-current-outbound-ips"></a>Überprüfen aktueller ausgehender IP-Adressen

Nun können Sie die Funktion ausführen. Überprüfen Sie jedoch zuerst im Portal, welche ausgehenden IP-Adressen von der Funktions-App verwendet werden.  

1. Wählen Sie in Ihrer Funktions-App die Option **Eigenschaften** aus, und überprüfen Sie das Feld **Ausgehende IP-Adressen**.

    ![Anzeigen der ausgehenden IP-Adressen der Funktions-App](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. Kehren Sie nun zu Ihrer HTTP-Triggerfunktion zurück, und wählen Sie **Programmieren und testen** > **Testen/Ausführen** aus.

    ![Testen der Funktion](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. Wählen Sie **Ausführen** aus, um die Funktion auszuführen, und wechseln Sie dann zu **Ausgabe**. 

    ![Ausgabe der Testfunktion](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. Vergewissern Sie sich, dass es sich bei der IP-Adresse im HTTP-Antworttext um einen der Werte aus den ausgehenden IP-Adressen handelt, die Sie sich zuvor angesehen haben.

Nun können Sie eine öffentliche IP-Adresse erstellen und ein NAT-Gateway verwenden, um diese ausgehende IP-Adresse zu ändern.

## <a name="create-public-ip"></a>Öffentliche IP-Adresse erstellen

1. Wählen Sie in Ihrer Ressourcengruppe **Hinzufügen** aus, suchen Sie im Azure Marketplace nach **Öffentliche IP-Adresse**, und wählen Sie **Erstellen** aus. Verwenden Sie die Einstellungen aus der Tabelle unterhalb der Abbildung:

    ![Erstellen einer öffentlichen IP-Adresse](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | Einstellung      | Vorgeschlagener Wert  |
    | ------------ | ---------------- |
    | **IP-Version** | IPv4 |
    | **SKU** | Standard |
    | **Tier** | Länderspezifisch |
    | **Name** | Outbound-IP |
    | **Abonnement** | Vergewissern Sie sich, dass Ihr Abonnement angezeigt wird. | 
    | **Ressourcengruppe** | „myResourceGroup“ (oder der Name, den Sie Ihrer Ressourcengruppe zugewiesen haben) |
    | **Location** | „USA, Osten“ (oder der Standort, den Sie Ihren anderen Ressourcen zugewiesen haben) |
    | **Verfügbarkeitszone** | Keine Zone |

1. Wählen Sie **Erstellen** aus, um die Bereitstellung zu übermitteln.

1. Navigieren Sie nach Abschluss der Bereitstellung zu Ihrer neu erstellten öffentlichen IP-Adressressource, und sehen Sie sich die IP-Adresse in der **Übersicht** an.

    ![Anzeigen der öffentlichen IP-Adresse](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>Erstellen eines NAT Gateways

Erstellen Sie als Nächstes das NAT-Gateway. Im [vorherigen Tutorial für virtuelle Netzwerke](functions-create-vnet.md) war `Function-Net` der vorgeschlagene Subnetzname und `MyResourceGroup-vnet` der vorgeschlagene Name für das virtuelle Netzwerk.

1. Wählen Sie in Ihrer Ressourcengruppe **Hinzufügen** aus, suchen Sie im Azure Marketplace nach **NAT-Gateway**, und wählen Sie **Erstellen** aus. Verwenden Sie auf der Registerkarte **Basiseinstellungen** die Einstellungen aus der Tabelle unterhalb der Abbildung:

    ![Erstellen eines NAT Gateways](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | Einstellung      | Vorgeschlagener Wert  |
    | ------------ | ---------------- |  
    | **Abonnement** | Ihr Abonnement | 
    | **Ressourcengruppe** | „myResourceGroup“ (oder der Name, den Sie Ihrer Ressourcengruppe zugewiesen haben) |
    | **Name des NAT-Gateways** | myNatGateway |
    | **Region** | „USA, Osten“ (oder der Standort, den Sie Ihren anderen Ressourcen zugewiesen haben) |
    | **Verfügbarkeitszone** | Keine |

1. Wählen Sie **Weiter: Ausgehende IP-Adresse** aus. Wählen Sie im Feld **Öffentliche IP-Adressen** die zuvor erstellte öffentliche IP-Adresse aus. Lassen Sie **Präfixe für öffentliche IP-Adressen** leer.

1. Wählen Sie **Weiter: Subnetz** aus. Wählen Sie im Feld **Virtuelles Netzwerk** die Ressource *myResourceGroup-vnet* sowie das Subnetz *Function-Net* aus.

    ![Auswählen des Subnetzes](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. Wählen Sie die Option **Überprüfen und erstellen** und dann **Erstellen** aus, um die Bereitstellung zu übermitteln.

Nach Abschluss der Bereitstellung kann das NAT-Gateway Datenverkehr aus dem Subnetz Ihrer Funktions-App an das Internet weiterleiten.

## <a name="update-function-configuration"></a>Aktualisieren der Funktionskonfiguration

Nun müssen Sie eine Anwendungseinstellung vom Typ `WEBSITE_VNET_ROUTE_ALL` mit dem Wert `1` hinzufügen.  Durch diese Einstellung wird erzwungen, dass ausgehender Datenverkehr über das virtuelle Netzwerk und das zugehörige NAT-Gateway geleitet wird. Ohne diese Einstellung wird Internetdatenverkehr nicht über das integrierte virtuelle Netzwerk geleitet, und es werden die gleichen ausgehenden IP-Adressen angezeigt. 

1. Navigieren Sie im Azure-Portal zu Ihrer Funktions-App, und wählen Sie im Menü auf der linken Seite die Option **Konfiguration** aus.

1. Wählen Sie unter **Anwendungseinstellungen** die Option **+ Neue Anwendungseinstellung** aus, und füllen Sie die Felder mit den folgenden Werten aus:

    |Feldname  |Wert |
    |---|---|
    |**Name**    |WEBSITE_VNET_ROUTE_ALL|
    |**Wert**   |1|

1. Wählen Sie **OK** aus, um das Dialogfeld „Neue Anwendungseinstellung“ zu schließen.

1. Wählen Sie **Speichern** und anschließend **Weiter** aus, um die Einstellungen zu speichern.

Die Funktions-App ist jetzt so konfiguriert, dass Datenverkehr über das zugehörige virtuelle Netzwerk geleitet wird.

## <a name="verify-new-outbound-ips"></a>Überprüfen der neuen ausgehenden IP-Adressen

Wiederholen Sie [die Schritte von vorhin](#verify-current-outbound-ips), um die Funktion erneut auszuführen. Nun sollte in der Funktionsausgabe die ausgehende IP-Adresse angezeigt werden, die Sie in der NAT konfiguriert haben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Im Rahmen dieses Tutorials wurden Ressourcen erstellt. Für diese Ressourcen fallen je nach [Kontostatus](https://azure.microsoft.com/account/) und [Dienstpreisen](https://azure.microsoft.com/pricing/) Kosten an. Löschen Sie die Ressourcen, wenn Sie sie nicht mehr benötigen, um zusätzliche Kosten zu vermeiden. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Netzwerkoptionen von Azure Functions](functions-networking-options.md)