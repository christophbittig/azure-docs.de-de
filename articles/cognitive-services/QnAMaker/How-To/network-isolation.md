---
title: Netzwerkisolation
description: Benutzer können den öffentlichen Zugriff auf QnA Maker-Ressourcen einschränken.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 87726db1f0747c3f9383168321bc3054c685c8a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131038719"
---
# <a name="recommended-settings-for-network-isolation"></a>Empfohlene Einstellungen für die Netzwerkisolation

Gehen Sie wie hier beschrieben vor, um den öffentlichen Zugriff auf QnA Maker-Ressourcen einzuschränken. Schützen Sie eine Cognitive Services-Ressource durch [Konfigurieren des virtuellen Netzwerks](../../cognitive-services-virtual-networks.md?tabs=portal) vor öffentlichem Zugriff.

## <a name="restrict-access-to-app-service-qna-runtime"></a>Einschränken des Zugriffs auf App Service (QnA-Runtime)

Mit dem Diensttag `CognitiveServicesMangement` können Sie den Zugriff auf eingehenden Datenverkehr mithilfe entsprechender Regeln auf die App Service- oder ASE-Netzwerksicherheitsgruppe (App Service Environment) beschränken.Weitere Informationen zu Diensttags finden Sie im Artikel  [Diensttags in virtuellen Netzwerken](../../../virtual-network/service-tags-overview.md). 

### <a name="regular-app-service"></a>Reguläre App Service-Instanz

1. Öffnen Sie Cloud Shell (PowerShell) über das Azure-Portal.
2. Führen Sie den folgenden Befehl im PowerShell-Fenster unten auf der Seite aus:

```ps
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "<resource group name>" -WebAppName "<app service name>" -Name "cognitive services Tag" -Priority 100 -Action Allow -ServiceTag "CognitiveServicesManagement" 
```
3.  Vergewissern Sie sich, dass die hinzugefügte Zugriffsregel auf der Registerkarte **Netzwerk** im Abschnitt **Zugriffseinschränkungen** vorhanden ist:  

    > [!div class="mx-imgBorder"]
    > [ ![Screenshot der Zugriffseinschränkungsregel]( ../media/network-isolation/access-restrictions.png) ](  ../media/network-isolation/access-restrictions.png#lightbox)

4. Für den Zugriff auf den **Testbereich** im Portal https://qnamaker.ai fügen Sie die **öffentliche IP-Adresse des Computers hinzu**, von dem aus Sie auf das Portal zugreifen möchten. Wählen Sie auf der Seite **Zugriffseinschränkungen** die Option **Regel hinzufügen** aus, und gestatten Sie den Zugriff auf Ihre Client-IP-Adresse. 

    > [!div class="mx-imgBorder"]
    > [ ![Screenshot der Zugriffseinschränkungsregel mit hinzugefügter öffentlicher IP-Adresse]( ../media/network-isolation/public-address.png) ](  ../media/network-isolation/public-address.png#lightbox)

### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Konfigurieren der App Service-Umgebung zum Hosten von QnA Maker App Service

Die App Service-Umgebung (App Service Environment, ASE) kann zum Hosten der QnA Maker App Service-Instanz verwendet werden. Führen Sie diese Schritte aus:

1. Erstellen Sie eine [neue Azure Cognitive Search-Ressource](https://ms.portal.azure.com/#create/Microsoft.Search).
2. Erstellen Sie eine externe ASE mit App Service.
    - Anleitungen dazu finden Sie in diesem [App Service-Schnellstart](../../../app-service/environment/create-external-ase.md#create-an-ase-and-an-app-service-plan-together). Dieser Vorgang kann ein bis zwei Stunden dauern.
    - Zum Schluss verfügen Sie über einen App Service-Endpunkt, der Folgendem ähnelt: `https://<app service name>.<ASE name>.p.azurewebsite.net` 
    - Beispiel: `https:// mywebsite.myase.p.azurewebsite.net`  
3. Fügen Sie die folgenden App Service-Konfigurationen hinzu:
    
    | Name                       | Wert                                                     |
    |:---------------------------|:----------------------------------------------------------| 
    | PrimaryEndpointKey         | `<app service name>-PrimaryEndpointKey`                   | 
    | AzureSearchName            | `<Azure Cognitive Search Resource Name from step #1>`     | 
    | AzureSearchAdminKey        | `<Azure Cognitive Search Resource admin Key from step #1>`| 
    | QNAMAKER_EXTENSION_VERSION | `latest`                                                  |
    | DefaultAnswer              | `no answer found`                                         |

4. Fügen Sie den CORS-Ursprung „*“ für App Service hinzu, um den Zugriff auf den Testbereich des Portals https://qnamaker.ai zu ermöglichen. **CORS** befindet sich unter dem API-Header im App Service-Bereich.

    > [!div class="mx-imgBorder"]
    > [ ![Screenshot der CORS-Schnittstelle auf der App Service-Benutzeroberfläche]( ../media/network-isolation/cross-orgin-resource-sharing.png) ](  ../media/network-isolation/cross-orgin-resource-sharing.png#lightbox)

5. Erstellen Sie eine QnA Maker Cognitive Services-Instanz (Microsoft.CognitiveServices/accounts) mithilfe von Azure Resource Manager. Der QnA Maker-Endpunkt sollte auf den oben erstellten App Service-Endpunkt (`https:// mywebsite.myase.p.azurewebsite.net`) festgelegt werden. Hier finden Sie eine [Azure Resource Manager-Beispielvorlage, die Sie als Referenz verwenden können](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/QnAMakerASEArmTemplate).

### <a name="related-questions"></a>Verwandte Fragen

#### <a name="can-qna-maker-be-deployed-to-an-internal-ase"></a>Kann QnA Maker in einer internen ASE bereitgestellt werden?

Der Hauptgrund für die Verwendung einer externen ASE ist, dass das QnA Maker-Dienst-Back-End (Erstellungs-APIs) die App Service-Instanz über das Internet erreichen kann. Sie können sie jedoch trotzdem schützen, indem Sie eine Einschränkung für den Zugriff auf eingehenden Datenverkehr hinzufügen, damit nur Verbindungen von Adressen zulässig sind, die dem Diensttag `CognitiveServicesManagement` zugeordnet sind.

Wenn Sie dennoch eine interne ASE verwenden möchten, müssen Sie diese spezifische QnA Maker-App in der ASE in einer öffentlichen Domäne über das TLS/SSL-Zertifikat des App-Gateway-DNS verfügbar machen. Weitere Informationen finden Sie in diesem [Artikel zur Unternehmensbereitstellung von App Services](/azure/architecture/reference-architectures/enterprise-integration/ase-standard-deployment).

## <a name="restrict-access-to-cognitive-search-resource"></a>Einschränken des Zugriffs auf Cognitive Search-Ressourcen

Die Cognitive Search-Instanz kann über einen privaten Endpunkt isoliert werden, nachdem die QnA Maker-Ressourcen erstellt wurden. Führen Sie die folgenden Schritte aus, um den Zugriff zu sperren:

1. Erstellen Sie ein neues [virtuelles Netzwerk (VNET)](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM), oder verwenden Sie ein vorhandenes VNET der ASE (App Service Environment).
2. Öffnen Sie die VNET-Ressource, und erstellen Sie dann auf der Registerkarte **Subnetze** zwei Subnetze: eines für die App Service-Instanz **(appservicesubnet)** und ein weiteres Subnetz **(searchservicesubnet)** für die Cognitive Search-Ressource ohne Delegierung. 

    > [!div class="mx-imgBorder"]
    > [ ![Screenshot der Benutzeroberfläche für Subnetze virtueller Netzwerke]( ../media/network-isolation/subnets.png) ](  ../media/network-isolation/subnets.png#lightbox)

3. Ändern Sie in der Cognitive Search-Dienstinstanz auf der Registerkarte **Netzwerk** die Einstellung für „Endpunktkonnektivität (Daten)“ von „Öffentlich“ in „Privat“. Dieser Vorgang ist ein zeitintensiver Prozess und **kann bis zu 30 Minuten in Anspruch nehmen**.

    > [!div class="mx-imgBorder"]
    > [ ![Screenshot der Benutzeroberfläche für das Netzwerk mit der Umschaltfläche „Öffentlich/Privat“]( ../media/network-isolation/private.png) ](  ../media/network-isolation/private.png#lightbox)

4. Nachdem die Search-Ressource in „Privat“ geändert wurde, wählen Sie **+ Privater Endpunkt** aus.
    - **Registerkarte „Grundlagen“** : Stellen Sie sicher, dass Sie den Endpunkt in derselben Region wie die Search-Ressource erstellen.
    - **Registerkarte „Ressource“** : Wählen Sie die erforderliche Search-Ressource vom Typ `Microsoft.Search/searchServices` aus.

    > [!div class="mx-imgBorder"]
    > [ ![Screenshot der Benutzeroberfläche zum Erstellen eines privaten Endpunkts]( ../media/network-isolation/private-endpoint.png) ](  ../media/network-isolation/private-endpoint.png#lightbox)

    - **Registerkarte „Konfiguration“** : Verwenden Sie das in Schritt 2 erstellte  Subnetz (searchservicesubnet) im VNET. Wählen Sie anschließend im Abschnitt **Integration von privatem DNS** das entsprechende Abonnement aus, und erstellen Sie eine neue private DNS-Zone namens **privatelink.search.windows.net**.

     > [!div class="mx-imgBorder"]
     > [ ![Screenshot der Benutzeroberfläche zum Erstellen eines privaten Endpunkts mit ausgefüllten Subnetzfeld]( ../media/network-isolation/subnet.png) ](  ../media/network-isolation/subnet.png#lightbox)

5. Aktivieren Sie die VNET-Integration für die reguläre App Service-Instanz. Sie können diesen Schritt für die ASE überspringen, da diese bereits Zugriff auf das VNET hat.
    - Navigieren Sie zum Abschnitt **Netzwerk** der App Service-Instanz, und öffnen Sie **VNET-Integration**.
    - Erstellen Sie eine Verknüpfung zu dem in Schritt 2 erstellten Subnetz (appservicevnet) im dedizierten App Service-VNET.
    
     > [!div class="mx-imgBorder"]
     > [ ![Screenshot der Benutzeroberfläche für die VNET-Integration]( ../media/network-isolation/integration.png) ](  ../media/network-isolation/integration.png#lightbox)

[Erstellen Sie private Endpunkte](../reference-private-endpoint.md) für die Azure Search-Ressource.

Gehen Sie wie hier beschrieben vor, um den öffentlichen Zugriff auf QnA Maker-Ressourcen einzuschränken. Schützen Sie eine Cognitive Services-Ressource durch [Konfigurieren des virtuellen Netzwerks](../../cognitive-services-virtual-networks.md?tabs=portal) vor öffentlichem Zugriff.

Nachdem Sie den Zugriff auf Cognitive Service-Ressourcen basierend auf VNET eingeschränkt haben, können Sie die Wissensdatenbanken im Portal https://qnamaker.ai über Ihr lokales Netzwerk oder Ihren lokalen Browser durchsuchen.
- Gewähren Sie Zugriff auf das [lokale Netzwerk](../../cognitive-services-virtual-networks.md?tabs=portal#configuring-access-from-on-premises-networks).
- Gewähren Sie Zugriff auf Ihren [lokalen Browser/Computer](../../cognitive-services-virtual-networks.md?tabs=portal#managing-ip-network-rules).
- Fügen Sie auf der Registerkarte **Netzwerk** im Abschnitt **Firewall** die öffentliche IP-Adresse des Computers hinzu. `portal.azure.com` zeigt standardmäßig die öffentliche IP-Adresse des aktuellen Browsercomputers an. Wählen Sie diesen Eintrag aus, und wählen Sie dann **Speichern** aus.

     > [!div class="mx-imgBorder"]
     > [ ![Screenshot der Benutzeroberfläche zur Konfiguration von Firewall und virtuellen Netzwerken]( ../media/network-isolation/firewall.png) ](  ../media/network-isolation/firewall.png#lightbox)
