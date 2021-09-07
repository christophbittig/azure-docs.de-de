---
title: Azure App Service-Zugriffseinschränkungen
description: Es wird beschrieben, wie Sie Ihre App in Azure App Service schützen, indem Sie Zugriffseinschränkungen einrichten.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: afadabb0503cde997858fce1ff9d1e82ac6d2f89
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112679518"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Einrichten von Azure App Service-Zugriffseinschränkungen

Durch die Einrichtung von Zugriffseinschränkungen können Sie eine nach Priorität sortierte Zulassungs-/Verweigerungsliste definieren, über die der Zugriff auf Ihre App gesteuert wird. Die Liste kann IP-Adressen oder Azure Virtual Network-Subnetze enthalten. Wenn mindestens ein Eintrag vorhanden ist, enthält die Liste am Ende einen impliziten Eintrag vom Typ *Alle ablehnen*.

Die Funktion für die Zugriffseinschränkung kann für alle Workloads genutzt werden, die von Azure App Service gehostet werden. Diese Workloads können Web-Apps, API-Apps, Linux-Apps, Linux-Container-Apps und Funktionen umfassen.

Wenn eine Anforderung an Ihre App gesendet wird, wird die VON-Adresse anhand der Regeln in Ihrer Liste mit den Zugriffseinschränkungen ausgewertet. Wenn die VON-Adresse in einem Subnetz enthalten ist, das mit Dienstendpunkten für Microsoft.Web konfiguriert ist, wird das Quellsubnetz mit den VNET-Regeln in Ihrer Liste mit den Zugriffseinschränkungen verglichen. Wenn der Zugriff für die Adresse basierend auf den Regeln in der Liste nicht zugelassen wird, antwortet der Dienst mit dem Statuscode [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

Die Funktion „Zugriffseinschränkungen“ wird in den App Service-Front-End-Rollen implementiert, die den Workerhosts vorgeschaltet sind, auf denen Ihr Code ausgeführt wird. Es handelt sich somit bei Zugriffseinschränkungen im Grunde um Zugriffssteuerungslisten (ACLs) für das Netzwerk.

Die Möglichkeit, den Zugriff auf Ihre Web-App auf ein virtuelles Azure-Netzwerk zu beschränken, basiert auf der Nutzung von [Dienstendpunkten][serviceendpoints]. Mit Dienstendpunkten können Sie den Zugriff auf einen mehrinstanzenfähigen Dienst auf ausgewählte Subnetze beschränken. Der Datenverkehr für Apps, die in einer App Service-Umgebung gehostet werden, kann hierdurch nicht eingeschränkt werden. In einer App Service-Umgebung können Sie den Zugriff auf Ihre App steuern, indem Sie IP-Adressregeln anwenden.

> [!NOTE]
> Die Dienstendpunkte müssen sowohl für die Netzwerkseite als auch für den Azure-Dienst aktiviert werden, der für die Aktivierung verwendet wird. Eine Liste der Azure-Dienste, die Dienstendpunkte unterstützen, finden Sie unter [Virtual Network-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md).
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="Diagramm: Ablauf für Zugriffseinschränkungen":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>Verwalten von Zugriffseinschränkungsregeln im Portal

Gehen Sie wie folgt vor, um Ihrer App eine Zugriffseinschränkungsregel hinzuzufügen:

1. Melden Sie sich beim Azure-Portal an.

1. Wählen Sie im linken Bereich die Option **Netzwerk** aus.

1. Wählen Sie im Bereich **Netzwerk** unter **Zugriffseinschränkungen** die Option **Zugriffseinschränkungen konfigurieren** aus.

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Screenshot: App Service-Bereich mit Netzwerkoptionen im Azure-Portal":::

1. Sehen Sie sich auf der Seite **Zugriffseinschränkungen** die Liste mit den Zugriffseinschränkungsregeln an, die für Ihre App definiert sind.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Screenshot der Seite „Zugriffseinschränkungen“ im Azure-Portal mit der Liste mit den Zugriffseinschränkungsregeln, die für die ausgewählte App definiert sind.":::

   In der Liste werden alle aktuellen Einschränkungen angezeigt, die auf die App angewendet werden. Falls Sie über eine VNET-Einschränkung für Ihre App verfügen, wird in der Tabelle angezeigt, ob die Dienstendpunkte für Microsoft.Web aktiviert sind. Falls für Ihre App keine Einschränkungen definiert sind, ist der Zugriff auf die App von jedem Ort aus möglich.

### <a name="add-an-access-restriction-rule"></a>Hinzufügen einer Zugriffseinschränkungsregel

Wählen Sie zum Hinzufügen einer Zugriffseinschränkungsregel zu Ihrer App im Bereich **Zugriffseinschränkungen** die Option **Regel hinzufügen** aus. Nachdem Sie eine Regel hinzugefügt haben, ist sie sofort wirksam. 

Regeln werden in der Reihenfolge ihrer Priorität erzwungen, wobei mit der niedrigsten Zahl in der Spalte **Priorität** begonnen wird. Bereits nach dem Hinzufügen von nur einer Regel gilt implizit *Alle ablehnen*.

Gehen Sie beim Erstellen einer Regel im Bereich **Zugriffseinschränkung hinzufügen** wie folgt vor:

1. Wählen Sie unter **Aktion** entweder die Option **Zulassen** oder **Ablehnen** aus.  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="Screenshot des Bereichs „IP-Einschränkung hinzufügen“.":::

1. Geben Sie optional einen Namen und eine Beschreibung für die Regel ein.
1. Geben Sie im Feld **Priorität** einen Prioritätswert ein.
1. Wählen Sie in der Dropdownliste **Typ** den Typ der Regel aus.

Die unterschiedlichen Typen von Regeln werden in den folgenden Abschnitten beschrieben.

> [!NOTE]
> - Es gibt einen Grenzwert von 512 Zugriffseinschränkungsregeln. Falls Sie mehr als 512 Zugriffseinschränkungsregeln benötigen, empfehlen wir Ihnen, die Installation eines eigenständigen Sicherheitsprodukts zu erwägen, z. B. Azure Front Door, Azure App Gateway oder eine alternative WAF.
>
#### <a name="set-an-ip-address-based-rule"></a>Festlegen einer Regel auf Basis einer IP-Adresse

Befolgen Sie die Schritte des vorherigen Abschnitts, aber achten Sie auf die folgende Ergänzung:
* Wählen Sie in Schritt 4 in der Dropdownliste **Typ** die Option **IPv4** oder **IPv6** aus. 

Geben Sie den **IP-Adressblock** sowohl für IPv4- als auch für IPv6-Adressen in CIDR-Notation (Classless Inter-Domain Routing) an. Zum Angeben einer Adresse können Sie beispielsweise *1.2.3.4/32* verwenden, wobei die ersten vier Oktette für Ihre IP-Adresse stehen und mit */32* die Maske angegeben wird. Die IPv4-CIDR-Notation für alle Adressen ist 0.0.0.0/0. Weitere Informationen zur CIDR-Notation finden Sie unter [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

#### <a name="set-a-service-endpoint-based-rule"></a>Festlegen einer dienstendpunktbasierten Regel

* Wählen Sie in Schritt 4 in der Dropdownliste **Typ** die Option **Virtuelles Netzwerk** aus.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="Screenshot des Bereichs „Einschränkung hinzufügen“ mit ausgewähltem Typ „Virtuelles Netzwerk“.":::

Geben Sie in den Dropdownlisten **Abonnement**, **Virtuelles Netzwerk** und **Subnetz** die Elemente an, auf die Sie den Zugriff einschränken möchten.

Die Verwendung von Dienstendpunkten ermöglicht das Einschränken des Zugriffs auf ausgewählte Subnetze virtueller Azure-Netzwerke. Falls für das von Ihnen ausgewählte Subnetz nicht bereits Dienstendpunkte mit Microsoft.Web aktiviert sind, wird dies automatisch durchgeführt, sofern Sie nicht das Kontrollkästchen **Fehlende Microsoft.Web-Dienstendpunkte ignorieren** aktivieren. In einem Szenario, bei dem Sie Dienstendpunkte in der App aktivieren möchten, aber nicht im Subnetz, hängt es vor allem davon ab, ob Sie über die Berechtigungen für die Aktivierung im Subnetz verfügen. 

Falls die Dienstendpunkte im Subnetz bei Ihnen von einer anderen Person aktiviert werden müssen, sollten Sie das Kontrollkästchen **Fehlende Microsoft.Web-Dienstendpunkte ignorieren** aktivieren. Ihre App wird für Dienstendpunkte konfiguriert, weil damit zu rechnen ist, dass diese später im Subnetz aktiviert werden. 

Sie können Dienstendpunkte nicht nutzen, um den Zugriff auf Apps einzuschränken, die in einer App Service-Umgebung ausgeführt werden. Wenn Ihre App in einer App Service-Umgebung enthalten ist, können Sie den Zugriff darauf steuern, indem Sie IP-Zugriffsregeln anwenden. 

Bei Dienstendpunkten können Sie Ihre App mit Anwendungsgateways oder anderen WAF-Geräten (Web Application Firewall) konfigurieren. Sie können auch Anwendungen mit mehreren Ebenen und sicheren Back-Ends konfigurieren. Weitere Informationen finden Sie unter [App Service-Netzwerkfunktionen](networking-features.md) und [Application Gateway-Integration mit Dienstendpunkten](networking/app-gateway-with-service-endpoints.md).

> [!NOTE]
> - Dienstendpunkte werden derzeit nicht für Web-Apps unterstützt, für die IP-basierte TLS/SSL-Bindungen mit einer virtuellen IP (VIP) verwendet werden.
>
#### <a name="set-a-service-tag-based-rule"></a>Festlegen einer diensttagbasierten Regel

* Wählen Sie in Schritt 4 in der Dropdownliste **Typ** die Option **Diensttag** aus.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png?v2" alt-text="Screenshot des Bereichs „Einschränkung hinzufügen“ mit ausgewähltem Typ „Diensttag“.":::

Jedes Diensttag steht für eine Liste von IP-Adressbereichen aus Azure-Diensten. Eine Liste dieser Dienste und Verknüpfungen mit den spezifischen Bereichen finden Sie in der [Diensttagdokumentation][servicetags].

Alle verfügbaren Diensttags werden in Zugriffseinschränkungsregeln unterstützt. Der Einfachheit halber ist im Azure-Portal nur eine Liste der gängigsten Tags verfügbar. Verwenden Sie Azure Resource Manager-Vorlagen oder Skripts, um komplexere Regeln zu konfigurieren, z. B. für regionale Bereiche. Diese Tags sind über das Azure-Portal verfügbar:

* ActionGroup
* ApplicationInsightsAvailability
* AzureCloud
* AzureCognitiveSearch
* AzureEventGrid
* AzureFrontDoor.Backend
* AzureMachineLearning
* AzureTrafficManager
* LogicApps

### <a name="edit-a-rule"></a>Bearbeiten einer Regel

1. Wählen Sie auf der Seite **Zugriffseinschränkungen** die Regel aus, die Sie bearbeiten möchten, um mit der Bearbeitung einer vorhandenen Zugriffseinschränkungsregel zu beginnen.

1. Nehmen Sie im Bereich **Zugriffseinschränkung bearbeiten** Ihre Änderungen vor, und wählen Sie anschließend die Option **Regel aktualisieren** aus. Änderungen sind sofort wirksam. Dies gilt auch für Änderungen der Prioritätsreihenfolge.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Screenshot des Dialogfelds „Zugriffseinschränkung bearbeiten“ im Azure-Portal mit den Feldern für eine vorhandene Zugriffseinschränkungsregel.":::

   > [!NOTE]
   > Wenn Sie eine Regel bearbeiten, können Sie nicht zwischen den Regeltypen wechseln. 

### <a name="delete-a-rule"></a>Löschen einer Regel

Wählen Sie zum Löschen einer Regel auf der Seite **Zugriffseinschränkungen** neben der Regel, die Sie löschen möchten, die Auslassungszeichen ( **...** ) und dann die Option **Entfernen** aus.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="Screenshot der Seite „Zugriffseinschränkungen“ mit Auslassungszeichen und Option „Entfernen“ für die zu löschende Zugriffseinschränkungsregel.":::

## <a name="access-restriction-advanced-scenarios"></a>Erweiterte Szenarien für die Zugriffseinschränkung
In den folgenden Abschnitten werden einige erweiterte Szenarien beschrieben, in denen Zugriffseinschränkungen verwendet werden.

### <a name="filter-by-http-header"></a>Filtern nach HTTP-Header

Im Rahmen einer beliebigen Regel können Sie weitere Filter nach HTTP-Header hinzufügen. Die folgenden HTTP-Headernamen werden unterstützt:
* X-Forwarded-For
* X-Forwarded-Host
* X-Azure-FDID
* X-FD-HealthProbe

Für jeden Headernamen können Sie bis zu acht durch Trennzeichen getrennte Werte hinzufügen. Die Filter nach HTTP-Header werden nach der Regel selbst ausgewertet, und nur wenn beide Bedingungen erfüllt sind, wird die Regel angewandt.

### <a name="multi-source-rules"></a>Regeln für mehrere Quellen

Mithilfe von Regeln für mehrere Quellen können Sie bis zu acht IP-Bereiche oder acht Diensttags in einer einzigen Regel kombinieren. Dies kann hilfreich sein, wenn Sie über mehr als 512 IP-Adressbereiche verfügen oder logische Regeln erstellen möchten, bei denen mehrere IP-Adressbereiche mit einem einzelnen Filter für HTTP-Header kombiniert werden.

Regeln mit mehreren Quellen werden auf die gleiche Weise definiert wie Regeln mit einer Quelle, wobei die einzelnen Bereiche durch Kommas voneinander getrennt werden.

PowerShell-Beispiel:

  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Multi-source rule" -IpAddress "192.168.1.0/24,192.168.10.0/24,192.168.100.0/24" `
    -Priority 100 -Action Allow
  ```

### <a name="block-a-single-ip-address"></a>Blockieren einer einzelnen IP-Adresse

Wenn Sie Ihre erste Zugriffseinschränkungsregel hinzufügen, fügt der Dienst eine explizite Regel vom Typ *Alle ablehnen* mit der Priorität 2147483647 hinzu. In der Praxis ist die explizite Regel *Alle ablehnen* die letzte Regel, die ausgeführt wird. Hiermit wird der Zugriff auf alle IP-Adressen blockiert, der nicht durch eine *Zulassen*-Regel explizit zugelassen ist.

Für ein Szenario, bei dem Sie eine einzelne oder mehrere IP-Adressen explizit blockieren, aber den Zugriff auf alles andere zulassen möchten, sollten Sie eine explizite Regel vom Typ *Alle zulassen* hinzufügen.

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Screenshot der Seite „Zugriffseinschränkungen“ im Azure-Portal mit einer einzelnen blockierten IP-Adresse.":::

### <a name="restrict-access-to-an-scm-site"></a>Beschränken des Zugriffs auf eine SCM-Website 

Neben der Möglichkeit, den Zugriff auf Ihre App zu steuern, können Sie auch den Zugriff auf die von der App verwendete SCM-Website einschränken. Die SCM-Website fungiert sowohl als Web Deploy-Endpunkt als auch als Kudu-Konsole. Sie können die Zugriffseinschränkungen für die SCM-Website getrennt von der App zuweisen oder den gleichen Satz mit Einschränkungen sowohl für die App als auch für die SCM-Website verwenden. Wenn Sie das Kontrollkästchen **Gleiche Einschränkungen wie \<app name>** aktivieren, wird alles abgeblendet. Wenn Sie das Kontrollkästchen deaktivieren, werden Ihre Einstellungen für die SCM-Website wieder angewendet. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Screenshot der Seite „Zugriffseinschränkungen“ im Azure-Portal ohne festgelegte Zugriffseinschränkungen für die SCM-Website oder die App.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance"></a>Einschränken des Zugriffs auf eine bestimmte Azure Front Door-Instanz
Datenverkehr von Azure Front Door zu Ihrer Anwendung stammt aus einer bekannten Gruppe von IP-Bereichen, die im Diensttag „AzureFrontDoor.Backend“ definiert sind. Mithilfe einer Einschränkungsregel für Diensttags können Sie den Ursprung des Datenverkehrs auf ausschließlich Azure Front Door einschränken. Um sicherzustellen, dass der Datenverkehr nur von Ihrer spezifischen Instanz stammt, müssen Sie die eingehenden Anforderungen noch weiter filtern, basierend auf dem eindeutigen HTTP-Header, der von Azure Front Door gesendet wird.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-frontdoor.png?v2" alt-text="Screenshot der Seite „Zugriffsbeschränkungen“ im Azure-Portal, der zeigt, wie die Azure Front Door-Einschränkung hinzugefügt wird":::

PowerShell-Beispiel:

  ```azurepowershell-interactive
  $afd = Get-AzFrontDoor -Name "MyFrontDoorInstanceName"
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
    -HttpHeader @{'x-azure-fdid' = $afd.FrontDoorId}
  ```

## <a name="manage-access-restriction-rules-programmatically"></a>Programmgesteuertes Verwalten von Zugriffseinschränkungsregeln

Sie können Zugriffseinschränkungen programmgesteuert hinzufügen, indem Sie eine der folgenden Vorgehensweisen wählen: 

* Verwenden Sie die [Azure CLI](/cli/azure/webapp/config/access-restriction). Beispiel:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

   > [!NOTE]
   > Das Arbeiten mit Diensttags, HTTP-Headern oder Regeln mit mehreren Quellen in Azure CLI erfordert mindestens Version 2.23.0. Sie können die Version des installierten Moduls wie folgt überprüfen: ```az version```

* Mithilfe von [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule) Beispiel:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > Das Arbeiten mit Diensttags, HTTP-Headern oder Regeln mit mehreren Quellen in Azure PowerShell erfordert mindestens Version 5.7.0. Sie können die Version des installierten Moduls wie folgt überprüfen: ```Get-InstalledModule -Name Az```

Sie können Werte auch manuell festlegen, indem Sie wie folgt vorgehen:

* Verwenden Sie in der App-Konfiguration in Azure Resource Manager einen PUT-Vorgang der [Azure-REST-API](/rest/api/azure/). Der Speicherort dieser Informationen in Azure Resource Manager lautet:

  management.azure.com/subscriptions/**Abonnement-ID**/resourceGroups/**Ressourcengruppen**/providers/Microsoft.Web/sites/**Web-App-Name**/config/web?api-version=2020-06-01

* Verwenden Sie eine Resource Manager-Vorlage. Sie können beispielsweise „resources.azure.com“ verwenden und den ipSecurityRestrictions-Block bearbeiten, um den erforderlichen JSON-Code hinzuzufügen.

  Die JSON-Syntax für das obige Beispiel lautet:

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```
  Die JSON-Syntax für ein erweitertes Beispiel, das ein Diensttag und eine HTTP-Header-Einschränkung verwendet, lautet:
  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "AzureFrontDoor.Backend",
          "tag": "ServiceTag",
          "action": "Allow",
          "priority": 100,
          "name": "Azure Front Door example",
          "headers": {
            "x-azure-fdid": [
              "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ]
          }
        }
      ]
    }
  }
  ```
## <a name="set-up-azure-functions-access-restrictions"></a>Einrichten von Azure Functions-Zugriffseinschränkungen

Zugriffseinschränkungen stehen auch für Funktions-Apps mit derselben Funktionalität sowie für App Service-Pläne zur Verfügung. Wenn Sie Zugriffseinschränkungen aktivieren, führt dies gleichzeitig dazu, dass der Code-Editor im Azure-Portal für alle unzulässigen IP-Adressen deaktiviert wird.

## <a name="next-steps"></a>Nächste Schritte
[Zugriffseinschränkungen für Azure Functions](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Application Gateway-Integration mit Dienstendpunkten](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md
