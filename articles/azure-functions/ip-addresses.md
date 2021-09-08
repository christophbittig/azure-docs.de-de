---
title: IP-Adressen in Azure Functions
description: Erfahren Sie, wie Sie eingehende und ausgehende IP-Adressen für Funktionen-Apps finden und wodurch diese geändert werden.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: a884edd23fa1538fcc2b00c80190eab6699e1e47
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414483"
---
# <a name="ip-addresses-in-azure-functions"></a>IP-Adressen in Azure Functions

In diesem Artikel erfahren Sie mehr über die folgenden Konzepte im Zusammenhang mit IP-Adressen von Funktions-Apps:

* Finden der IP-Adressen, die derzeit von einer Funktions-App verwendet werden.
* Bedingungen, die bewirken, dass Funktions-App-IP-Adressen geändert werden.
* Einschränken der IP-Adressen, die auf eine Funktions-App zugreifen können.
* Definieren dedizierter IP-Adressen für eine Funktions-App.

IP-Adressen werden Funktionen-Apps zugeordnet, nicht einzelnen Funktionen. Eingehende HTTP-Anforderungen können die eingehende IP-Adresse nicht verwenden, um einzelne Funktionen aufzurufen. Sie müssen den Standarddomänennamen (functionappname.azurewebsites.net) oder einen benutzerdefinierten Domänennamen verwenden.

## <a name="function-app-inbound-ip-address"></a>Eingehende IP-Adresse einer Funktionen-App

Jede Funktionen-App verfügt über eine einzelne eingehende IP-Adresse. So finden Sie diese IP-Adresse:

# <a name="azure-portal"></a>[Azure-Portal](#tab/portal)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zur Funktionen-App.
3. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus. Die eingehende IP-Adresse wird unter **Virtuelle IP-Adresse** angezeigt.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azurecli)

Verwenden Sie das `nslookup`-Hilfsprogramm auf Ihrem lokalen Clientcomputer:

```command
nslookup <APP_NAME>.azurewebsites.net
```

---

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Ausgehende IP-Adressen einer Funktions-App

Jede Funktionen-App verfügt über einen Satz von verfügbaren ausgehenden IP-Adressen. Jede ausgehende Verbindung von einer Funktion, beispielsweise mit einer Back-End-Datenbank, verwendet eine der verfügbaren ausgehenden IP-Adressen als IP-Ursprungsadresse. Im Vorfeld ist nicht klar, welche IP-Adresse eine bestimmte Verbindung verwendet. Daher muss Ihr Back-End-Dienst seine Firewall für alle ausgehenden IP-Adressen der App öffnen.

So finden Sie die verfügbaren ausgehenden IP-Adressen einer Funktionen-App:

# <a name="azure-portal"></a>[Azure-Portal](#tab/portal)

1. Melden Sie sich beim [Azure-Ressourcen-Explorer](https://resources.azure.com) an.
2. Wählen Sie **Abonnements > {Ihr Abonnement} > Anbieter > Microsoft.Web > Websites**.
3. Suchen Sie im JSON-Panel die Site mit einer `id`-Eigenschaft, die auf den Namen Ihrer Funktionen-App endet.
4. Prüfen Sie `outboundIpAddresses` und `possibleOutboundIpAddresses`. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azurecli)

```azurecli-interactive
az functionapp show --resource-group <GROUP_NAME> --name <APP_NAME> --query outboundIpAddresses --output tsv
az functionapp show --resource-group <GROUP_NAME> --name <APP_NAME> --query possibleOutboundIpAddresses --output tsv
```
---

Der Satz der `outboundIpAddresses` steht derzeit für die Funktionen-App zur Verfügung. Der Satz der `possibleOutboundIpAddresses` enthält IP-Adressen, die nur zur Verfügung stehen, wenn für die Funktionen-App [eine Skalierung auf andere Tarife](#outbound-ip-address-changes) möglich ist.

> [!NOTE]
> Bei der Skalierung einer im [Verbrauchstarif](consumption-plan.md) oder [Premium-Tarif](functions-premium-plan.md) ausgeführten Funktions-App wird möglicherweise ein neuer Bereich von ausgehenden IP-Adressen zugewiesen. Wenn Sie einen dieser Pläne ausführen, können Sie sich nicht auf die gemeldeten ausgehenden IP-Adressen verlassen, um eine endgültige Positivliste zu erstellen. Um alle potenziellen ausgehenden Adressen einzubeziehen, die während der dynamischen Skalierung verwendet werden, müssen Sie das gesamte Rechenzentrum auf Ihre Positivliste setzen.

## <a name="data-center-outbound-ip-addresses"></a>Ausgehende IP-Adressen eines Rechenzentrums

Wenn Sie einer Whitelist die von Ihrer Funktions-Apps verwendeten ausgehenden IP-Adressen hinzufügen möchten, haben Sie die zusätzliche Möglichkeit, das Rechenzentrum der Funktions-App (Azure-Region) auf eine Whitelist zu setzen. Sie können [eine JSON-Datei herunterladen, die IP-Adressen für alle Azure-Rechenzentren auflistet](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Suchen Sie dann das JSON-Fragment, das der Region entspricht, in der Ihre Funktions-App ausgeführt wird.

Beispielsweise könnte die Positivliste für Westeuropa etwa folgendem JSON-Fragment ähneln:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Informationen dazu, wann diese Datei aktualisiert wird und wann die IP-Adresse geändert wird, finden Sie im Abschnitt **Details** auf der [Download Center-Seite](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Änderungen der eingehenden IP-Adresse

Die eingehende IP-Adresse **kann** geändert werden, wenn Sie:

- eine Funktionen App löschen und in einer anderen Ressourcengruppe neu erstellen.
- die letzte Funktionen-App in einer Kombination aus Ressourcengruppe und Region löschen und neu erstellen.
- eine TLS-Bindung löschen, z. B. während der [Zertifikaterneuerung](../app-service/configure-ssl-certificate.md#renew-certificate).

Wenn Ihre Funktions-App in einem [Verbrauchstarif](consumption-plan.md) oder [Premium-Tarif](functions-premium-plan.md) ausgeführt wird, kann sich die eingehende IP-Adresse auch ändern, selbst wenn Sie noch keine der [oben aufgeführten](#inbound-ip-address-changes) Aktionen ausgeführt haben.

## <a name="outbound-ip-address-changes"></a>Änderungen der ausgehenden IP-Adresse

Die relative Stabilität der ausgehenden IP-Adresse hängt vom Hostingplan ab.  

### <a name="consumption-and-premium-plans"></a>Verbrauchs- und Premium-Pläne

Aufgrund des Verhaltens der automatischen Skalierung kann sich die ausgehende IP-Adresse jederzeit ändern, wenn sie in einem [Verbrauchsplan](consumption-plan.md) oder einem [Premium-Plan](functions-premium-plan.md) ausgeführt wird. 

Falls Sie die ausgehende IP-Adresse Ihrer Funktions-App steuern müssen, z. B. wenn Sie sie einer Positivliste hinzufügen müssen, erwägen Sie die Implementierung eines [NAT-Gateways für virtuelle Netzwerke](#virtual-network-nat-gateway-for-outbound-static-ip) während der Ausführung in einem Premium-Plan. Dazu können Sie auch in einem dedizierten Plan (App Service) ausführen.

### <a name="dedicated-plans"></a>Dedizierte Pläne

Bei der Ausführung in dedizierten (App Service-)Plänen kann sich der Satz der verfügbaren ausgehenden IP-Adressen für eine Funktions-App ändern, wenn Sie:

* Maßnahmen ergreifen, durch die sich die eingehende IP-Adresse ändern kann.
* den dedizierten (App Service-)Plan wechseln. Alle möglichen ausgehenden IP-Adressen, die Ihre App in allen Tarifen verwenden kann, sind in der `possibleOutboundIPAddresses`-Eigenschaft aufgelistet. Siehe [Ermitteln der ausgehenden IP-Adressen](#find-outbound-ip-addresses).

#### <a name="forcing-an-outbound-ip-address-change"></a>Erzwingen der Änderung einer ausgehenden IP-Adresse

Gehen Sie folgendermaßen vor, um die Änderung einer ausgehenden IP-Adresse in einem dedizierten (App Service-)Plan zu erzwingen:

1. Skalieren Sie Ihren App Service-Plan zwischen Standard- und Premium v2-Tarifen hoch oder herunter.

2. Warten Sie 10 Minuten.

3. Skalieren Sie zur Ausgangssituation zurück.

## <a name="ip-address-restrictions"></a>Einschränkungen für IP-Adressen

Sie können eine Liste der IP-Adressen konfigurieren, denen Sie den Zugriff auf die Funktionen-App gestatten oder verweigern möchten. Weitere Informationen finden Sie unter [Statische Azure App Service-IP-Einschränkungen](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Dedizierte IP-Adressen

Wenn Ihre Funktions-App statische, dedizierte IP-Adressen erfordert, können Sie verschiedene Strategien erforschen. 

### <a name="virtual-network-nat-gateway-for-outbound-static-ip"></a>NAT-Gateway eines virtuellen Netzwerks für ausgehende statische IP-Adressen

Sie können die IP-Adresse des von Ihren Funktionen ausgehenden Datenverkehrs steuern, indem Sie mithilfe eines NAT-Gateways eines virtuellen Netzwerks Datenverkehr über eine statische öffentliche IP-Adresse weiterleiten. Sie können diese Topologie für Ausführungen in einem [Premium-Plan](functions-premium-plan.md) oder in einem [dedizierten Plan (App Service)](dedicated-plan.md) verwenden. Weitere Informationen finden Sie unter [Tutorial: Steuern der IP-Adresse für ausgehenden Datenverkehr von Azure Functions mit einem Virtual Network NAT-Gateway in Azure](functions-how-to-use-nat-gateway.md).

### <a name="app-service-environments"></a>App Service-Umgebungen

Um die vollständige Kontrolle sowohl über Eingangs- als auch Ausgangs-IP-Adressen zu erhalten, empfehlen wir [App Service-Umgebungen](../app-service/environment/intro.md) (die [Dienstebene „Isoliert“](https://azure.microsoft.com/pricing/details/app-service/) von App Service-Plänen). Weitere Informationen finden Sie unter [ASE-IP-Adressen](../app-service/environment/network-info.md#ase-ip-addresses) und [Steuern von eingehendem Datenverkehr in eine App Service-Umgebung](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

So finden Sie heraus, ob Ihre Funktionen-App in einer App Service-Umgebung ausgeführt wird:

# <a name="azure-porta"></a>[Azure Portal](#tab/portal)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zur Funktionen-App.
3. Klicken Sie auf die Registerkarte **Übersicht**.
4. Die App Service-Plantarif wird unter **App Service-Plan/Tarif** angezeigt. Der App Service Environment-Tarif ist **isoliert**.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azurecli)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

---

Die `sku` der App Service-Umgebung ist `Isolated`.

## <a name="next-steps"></a>Nächste Schritte

IP-Änderungen sind häufig durch die Skalierung Ihrer Funktionen-App begründet. [Erfahren Sie mehr über die Skalierung Ihrer Funktionen-App](functions-scale.md).
