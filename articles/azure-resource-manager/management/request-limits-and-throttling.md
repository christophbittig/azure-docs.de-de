---
title: Anforderungsgrenzwerte und -drosselung
description: Beschreibt, wie eine Begrenzung von Azure Resource Manager-Anforderungen genutzt wird, wenn Abonnementgrenzwerte erreicht wurden.
ms.topic: conceptual
ms.date: 11/15/2021
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 781c958e1a75e87c3f042e80282909e132730978
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523386"
---
# <a name="throttling-resource-manager-requests"></a>Begrenzen von Resource Manager-Anforderungen

In diesem Artikel wird beschrieben, wie Anforderungen durch Azure Resource Manager gedrosselt werden. Sie erfahren, wie die Anzahl der verbleibenden Anforderungen vor Erreichen des Grenzwerts nachverfolgt wird, und wie Sie bei Erreichen des Grenzwerts reagieren können.

Die Drosselung erfolgt auf zwei Ebenen. Azure Resource Manager drosselt Anforderungen für das Abonnement und den Mandanten. Wenn die Anforderung unterhalb der Drosselungsgrenzwerte für das Abonnement und den Mandanten liegt, leitet Resource Manager die Anforderung an den Ressourcenanbieter weiter. Der Ressourcenanbieter wendet Drosselungsgrenzwerte an, die auf seinen Betrieb zugeschnitten sind. Die folgende Abbildung zeigt, wie die Drosselung beim Weiterleiten einer Anforderung vom Benutzer an Azure Resource Manager und den Ressourcenanbieter angewendet wird.

![Anforderungsdrosselung](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Abonnement- und Mandantengrenzwerte

Alle Vorgänge auf Abonnement- und Mandantenebene unterliegen Drosselungsgrenzwerten. Bei Abonnementanforderungen wird Ihre Abonnement-ID übergeben, z. B. beim Abrufen der Ressourcengruppen in Ihrem Abonnement. In Mandantenanforderungen ist Ihre Abonnement-ID nicht enthalten (etwa beim Abrufen gültiger Azure-Standorte).

In der folgenden Tabelle sind die Standardgrenzwerte für die Drosselung pro Stunde aufgeführt.

| `Scope` | Operationen (Operations) | Begrenzung |
| ----- | ---------- | ------- |
| Subscription | Lesevorgänge | 12000 |
| Subscription | Löschvorgänge | 15000 |
| Subscription | Schreibvorgänge | 1200 |
| Tenant | Lesevorgänge | 12000 |
| Tenant | Schreibvorgänge | 1200 |

Diese Grenzwerte gelten für den Sicherheitsprinzipal (Benutzer oder Anwendung), von dem die Anforderungen stammen, sowie für die Abonnement-ID bzw. die Mandanten-ID. Falls Ihre Anforderungen von mehreren Sicherheitsprinzipalen stammen, liegen die Grenzwerte für das Abonnement oder den Mandanten über 12.000 bzw. 1.200 Anforderungen pro Stunde.

Diese Grenzwerte gelten für jede Azure Resource Manager-Instanz. In jeder Azure-Region sind mehrere Instanzen vorhanden, und Azure Resource Manager wird in allen Azure-Regionen bereitgestellt.  In der Praxis sind die Grenzwerte also höher als diese Grenzwerte. Die Anforderungen von einem Benutzer werden in der Regel von unterschiedlichen Azure Resource Manager-Instanzen verarbeitet.

Die verbleibenden Anforderungen werden in den [Werten im Antwortheader](#remaining-requests) zurückgegeben.

## <a name="resource-provider-limits"></a>Grenzwerte der Ressourcenanbieter

Ressourcenanbieter wenden ihre eigenen Drosselungsgrenzwerte an. Da Resource Manager die Drosselung nach Prinzipal-ID und nach Resource Manager-Instanz vornimmt, empfängt der Ressourcenanbieter möglicherweise mehr Anforderungen als die Standardgrenzwerte im vorherigen Abschnitt.

In diesem Abschnitt werden die Drosselungsgrenzwerte einiger häufig genutzter Ressourcenanbieter erläutert.

### <a name="storage-throttling"></a>Speicherdrosselung

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Netzwerkdrosselung

Der Ressourcenanbieter Microsoft.Network wendet die folgenden Drosselungsgrenzwerte an:

| Vorgang | Begrenzung |
| --------- | ----- |
| Schreib-/Löschvorgänge (PUT) | 1000 pro 5 Minuten |
| Lesevorgänge (GET) | 10000 pro 5 Minuten |

> [!NOTE]
> Für **Azure Privates DNS** gilt ein Drosselungslimit von 500 Lesevorgängen (GET) pro 5 Minuten.
>

### <a name="compute-throttling"></a>Computedrosselung

Informationen zu Drosselungsgrenzwerten für Computevorgänge finden Sie unter [Behandeln von API-Drosselungsfehlern – Compute](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors).

Verwenden Sie zum Überprüfen von VM-Instanzen innerhalb einer VM-Skalierungsgruppe die [Vorgänge von Virtual Machine Scale Sets](/rest/api/compute/virtualmachinescalesetvms). Verwenden Sie beispielsweise die [List-Abfrage für VM-Skalierungsgruppen](/rest/api/compute/virtualmachinescalesetvms/list) mit Parametern zum Überprüfen des Betriebszustands von VM-Instanzen. Diese API reduziert die Anzahl der Anforderungen.

### <a name="azure-resource-graph-throttling"></a>Azure Resource Graph-Drosselung

[Azure Resource Graph](../../governance/resource-graph/overview.md) begrenzt die Anzahl der Anforderungen nach seinen Vorgängen. Die Schritte in diesem Artikel zum Bestimmen der verbleibenden Anforderungen und zum Reagieren bei Erreichen des Grenzwerts gelten auch für Resource Graph. Resource Graph legt jedoch einen eigenen Grenzwert und eine eigene Rücksetzrate fest. Weitere Informationen finden Sie unter [Resource Graph-Drosselungsheader](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

### <a name="other-resource-providers"></a>Andere Ressourcenanbieter

Informationen zur Drosselung bei anderen Ressourcenanbietern finden Sie hier:

* [Anleitung zur Drosselung von Azure Key Vault](../../key-vault/general/overview-throttling.md)
* [AKS-Problembehandlung](../../aks/troubleshooting.md#im-receiving-429---too-many-requests-errors)

## <a name="error-code"></a>Fehlercode

Wenn Sie den Grenzwert erreichen, erhalten Sie den HTTP-Statuscode **429 Zu viele Anforderungen**. Die Antwort enthält einen **Retry-After**-Wert, der die Anzahl von Sekunden angibt, die Ihre Anwendung warten muss (oder im Ruhezustand verbleibt), bevor die nächste Anforderung gesendet wird. Wenn Sie eine Anforderung senden, bevor der Retry-Wert verstrichen ist, wird Ihre Anforderung nicht verarbeitet, und es wird ein neuer Retry-Wert zurückgegeben.

Nachdem Sie über den jeweiligen Zeitraum gewartet haben, können Sie die Verbindung zu Azure auch schließen und erneut öffnen. Durch Zurücksetzen der Verbindung können Sie eine Verbindung mit einer anderen Azure Resource Manager-Instanz herstellen.

Wenn Sie ein Azure SDK verwenden, verfügt das SDK möglicherweise über eine Konfiguration für die automatische Wiederholung. Weitere Informationen finden Sie unter [Wiederholungsanleitung für Azure-Dienste](/azure/architecture/best-practices/retry-service-specific).

Einige Ressourcenanbieter geben 429 zurück, um ein temporäres Problem zu melden. Das Problem kann eine Überlastbedingung sein, die nicht direkt durch Ihre Anforderung verursacht wird. Es kann sich aber auch um einen vorübergehenden Fehler in Bezug auf den Status der Zielressource oder der abhängigen Ressource handeln. Der Netzwerkressourcenanbieter gibt z.B. 429 mit dem Fehlercode **RetryableErrorDueToAnotherOperation** zurück, wenn die Zielressource durch einen anderen Vorgang gesperrt ist. Um festzustellen, ob der Fehler durch die Drosselung oder eine vorübergehende Bedingung entsteht, sehen Sie sich die Fehlerdetails in der Antwort an.

## <a name="remaining-requests"></a>Verbleibende Anforderungen

Sie können die Anzahl der verbleibenden Anforderungen durch Untersuchen der Antwortheader bestimmen. Leseanforderungen geben im Header einen Wert für die Anzahl der verbleibenden Leseanforderungen zurück. Schreibanforderungen enthalten einen Wert für die Anzahl der verbleibenden Schreibanforderungen. Die folgende Tabelle beschreibt die Antwortheader, die Sie auf diese Werte untersuchen können:

| Antwortheader | BESCHREIBUNG |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-deletes |Verbleibende abonnementbezogene Löschvorgänge. Dieser Wert wird für Löschvorgänge zurückgegeben. |
| x-ms-ratelimit-remaining-subscription-reads |Verbleibende abonnementbezogene Lesevorgänge. Dieser Wert wird für Lesevorgänge zurückgegeben. |
| x-ms-ratelimit-remaining-subscription-writes |Verbleibende abonnementbezogene Schreibvorgänge. Dieser Wert wird für Schreibvorgänge zurückgegeben. |
| x-ms-ratelimit-remaining-tenant-reads |Verbleibende mandantenbezogene Lesevorgänge |
| x-ms-ratelimit-remaining-tenant-writes |Verbleibende mandantenbezogene Schreibvorgänge |
| x-ms-ratelimit-remaining-subscription-resource-requests |Verbleibende abonnementbezogene Ressourcenanforderungen.<br /><br />Dieser Headerwert wird nur zurückgegeben, wenn ein Dienst den standardmäßigen Grenzwert außer Kraft gesetzt hat. Resource Manager fügt diesen Wert anstelle der Lese- oder Schreibvorgänge des Abonnements hinzu. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Verbleibende abonnementbezogene Ressourcensammlungsanforderungen.<br /><br />Dieser Headerwert wird nur zurückgegeben, wenn ein Dienst den standardmäßigen Grenzwert außer Kraft gesetzt hat. Dieser Wert gibt die Anzahl der verbleibenden Sammlungsanforderungen an (Auflisten von Ressourcen). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Verbleibende mandantenbezogene Ressourcenanforderungen.<br /><br />Dieser Header wird nur für Anforderungen auf Mandantenebene hinzugefügt, und nur dann, wenn ein Dienst den standardmäßigen Grenzwert außer Kraft gesetzt hat. Resource Manager fügt diesen Wert anstelle der Lese- oder Schreibvorgänge des Mandanten hinzu. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Verbleibende mandantenbezogene Ressourcensammlungsanforderungen.<br /><br />Dieser Header wird nur für Anforderungen auf Mandantenebene hinzugefügt, und nur dann, wenn ein Dienst den standardmäßigen Grenzwert außer Kraft gesetzt hat. |

Der Ressourcenanbieter kann auch Antwortheader mit Informationen zu verbleibenden Anforderungen zurückgeben. Informationen zu Antwortheadern, die vom Computeressourcenanbieter zurückgegeben werden, finden Sie unter [Aufrufrate für Informationsantwortkopfzeilen](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Abrufen der Headerwerte

Das Abrufen dieser Headerwerte in Ihrem Code oder Skript unterscheidet sich nicht vom Abrufen anderer Headerwerte. 

Beispiel: In **C#** rufen Sie den Headerwert aus einem **HttpWebResponse**-Objekt mit dem Namen **response** mit dem folgenden Code ab:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

In **PowerShell** rufen Sie den Headerwert aus einem Invoke-WebRequest-Vorgang ab.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Ein vollständiges PowerShell-Beispiel finden Sie unter [Check ARM Limits for a Given Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI) (Überprüfen der ARM-Grenzwerte für ein bestimmtes Abonnement).

Wenn Sie zum Debuggen die übrigen Anforderungen anzeigen möchten, können Sie in Ihrem **PowerShell**-Cmdlet den Parameter **-Debug** angeben.

```powershell
Get-AzResourceGroup -Debug
```

So wird eine Vielzahl von Werten zurückgegeben, einschließlich des folgenden Antwortwerts:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Um den Schreibgrenzwert abzurufen, verwenden Sie einen Schreibvorgang: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

So wird eine Vielzahl von Werten zurückgegeben, einschließlich der folgenden Werte:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

In der **Azure-CLI** rufen Sie den Headerwert mithilfe der ausführlicheren Option ab.

```azurecli
az group list --verbose --debug
```

So wird eine Vielzahl von Werten zurückgegeben, einschließlich der folgenden Werte:

```output
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Um den Schreibgrenzwert abzurufen, verwenden Sie einen Schreibvorgang: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

So wird eine Vielzahl von Werten zurückgegeben, einschließlich der folgenden Werte:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Nächste Schritte

* Ein vollständiges PowerShell-Beispiel finden Sie unter [Check ARM Limits for a Given Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI) (Überprüfen der ARM-Grenzwerte für ein bestimmtes Abonnement).
* Weitere Informationen zu Grenzwerten und Kontingenten finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Informationen zum Arbeiten mit asynchronen REST-Anforderungen finden Sie unter [Nachverfolgen asynchroner Vorgänge in Azure](async-operations.md).
