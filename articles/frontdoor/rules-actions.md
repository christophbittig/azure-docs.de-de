---
title: Konfigurieren von Azure Front Door Regelaktionen
description: Dieser Artikel enthält eine Liste der verschiedenen Aktionen, die Sie mit einem Azure Front Door-Regelsatz durchführen können.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/10/2021
ms.author: yuajia
ms.openlocfilehash: c072cf341049e5e2fc8631fdb628f04bdf719b97
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350387"
---
# <a name="azure-front-door-rules-actions"></a>Konfigurieren von Azure Front Door Regelaktionen

Ein Azure Front Door [Regelmodul](front-door-rules-engine.md) und Azure Front Door Standard/Premium [Regelsatz](standard-premium/concept-rule-set.md) bestehen aus Regeln mit einer Kombination aus Übereinstimmungsbedingungen und Aktionen. In diesem Artikel werden die Aktionen, die Sie in einem Azure Front Door Standard/Premium-Regelmodul oder -Regelsatz verwenden können, ausführlich beschrieben. Die Aktion definiert das Verhalten, das auf einen Anforderungstyp angewendet wird, der von einer oder mehreren Vergleichsbedingungen identifiziert wird. Eine Regel kann bis zu fünf Aktionen enthalten.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Vorschau) befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Die folgenden Aktionen können auch im Azure Front Door-Regelsatz verwendet werden.  

## <a name="cache-expiration"></a>Cacheablauf

Verwenden Sie diese **Cacheablauf**-Aktion, um die Gültigkeitsdauer (TTL) des Endpunkts für Anforderungen zu überschreiben, die die Bedingungen für die Regelübereinstimmung angeben.

> [!NOTE]
> Ursprünge können angeben, dass bestimmte Antworten nicht zwischengespeichert werden, indem der `Cache-Control`-Header mit dem Wert `no-cache`, `private` oder `no-store` verwendet wird . Unter diesen Umständen wird der Inhalt von der Vorderseite niemals zwischengespeichert, und diese Aktion hat keine Auswirkungen.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-------|------------------|
| Cacheverhalten | <ul><li>**Cache umgehen:** Der Inhalt sollte nicht zwischengespeichert werden. Legen Sie in ARM-Vorlagen die `cacheBehavior`-Eigenschaft auf `BypassCache` fest.</li><li>**Außerkraftsetzung:** Der vom Ursprung zurückgegebene TTL-Wert wird mit dem in der Aktion angegebenen Wert überschrieben. Dieses Verhalten wird nur angewendet, wenn die Antwort zwischengespeichert werden kann. Legen Sie in ARM-Vorlagen die `cacheBehavior`-Eigenschaft auf `Override` fest.</li><li>**Einstellen, falls fehlend:** Wenn vom Ursprung kein TTL-Wert zurückgegeben wird, legt die Regel den TTL-Wert auf den in der Aktion angegebenen Wert fest. Dieses Verhalten wird nur angewendet, wenn die Antwort zwischengespeichert werden kann. Legen Sie in ARM-Vorlagen die `cacheBehavior`-Eigenschaft auf `SetIfMissing` fest.</li></ul> |
| Cachedauer | Wenn das _Cacheverhalten_ auf `Override` oder `Set if missing` festgelegt ist, müssen diese Felder die zu verwendende Cachedauer angeben. Die maximale Dauer beträgt 366 Tage.<ul><li>Im Azure-Portal: Geben Sie die Tage, Stunden, Minuten und Sekunden an.</li><li>In ARM-Vorlagen: Geben Sie die Dauer im Format `d.hh:mm:ss` an. |

### <a name="example"></a>Beispiel

In diesem Beispiel überschreiben wir den Cacheablauf auf 6 Stunden, für übereinstimmende Anforderungen, die noch keine Cachedauer angeben.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="./media/rules-actions/cache-expiration.png" alt-text="Screenshot des Portals, das die Cacheablauf-Aktion anzeigt.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a>Abfragezeichenfolge für Cacheschlüssel

Verwenden Sie die Aktion **Cache-Schhlüsselabfragezeichenfolge**, um den Cacheschlüssel basierend auf den Abfragezeichenfolgen zu ändern. Der Cacheschlüssel ist die Methode, mit der die Front-Door eindeutige Anforderungen zum Zwischenspeichern identifiziert.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-------|------------------|
| Verhalten | <ul><li>**Einschließen:** Die in den Parametern angegebenen Abfragezeichenfolgen werden eingeschlossen, wenn der Cacheschlüssel generiert wird. Legen Sie in ARM-Vorlagen die `queryStringBehavior`-Eigenschaft auf `Include` fest.</li><li>**Cache jeder eindeutigen URL:** Jede eindeutige URL verfügt über einen eigenen Cacheschlüssel. Verwenden Sie in ARM-Vorlagen den `queryStringBehavior` von `IncludeAll`.</li><li>**Ausschließen:** Die in den Parametern angegebenen Abfragezeichenfolgen werden ausgeschlossen, wenn der Cacheschlüssel generiert wird. Legen Sie in ARM-Vorlagen die `queryStringBehavior`-Eigenschaft auf `Exclude` fest.</li><li>**Anfragezeichenfolge ignorieren:** Beim Generieren des Cacheschlüssels werden Abfragezeichenfolgen nicht berücksichtigt. Legen Sie in ARM-Vorlagen die `queryStringBehavior`-Eigenschaft auf `ExcludeAll` fest.</li></ul>  |
| Parameter | Die Liste der Namen der Abfragezeichenfolgen-Parameter, die durch Kommas getrennt sind. |

### <a name="example"></a>Beispiel

In diesem Beispiel ändern wir den Cacheschlüssel so, dass er einen Abfragezeichenfolgen-Parameter mit dem Namen `customerId` enthält.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="./media/rules-actions/cache-key-query-string.png" alt-text="Screenshot des Portals mit Aktion der Cacheschlüssel-Abfragezeichenfolgen.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a>Anforderungsheader ändern

Verwenden Sie die Aktion **Anforderungsheader ändern**, um die Header in der Anforderung zu ändern, wenn sie an ihren Ursprung gesendet wird.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-------|------------------|
| Betreiber | <ul><li>**Anfügen:** Der in Headername angegebene Header wird mit dem angegebenen Wert zur Anforderung hinzugefügt. Wenn der Header bereits vorhanden ist, wird der Wert mithilfe der Zeichenfolgenverkettung an den vorhandenen Headerwert angefügt. Es werden keine Trennzeichen hinzugefügt. Verwenden Sie in ARM-Vorlagen den `headerAction` von `Append`.</li><li>**Überschreiben:** Der in Headername angegebene Header wird mit dem angegebenen Wert zur Anforderung hinzugefügt. Wenn der Header bereits vorhanden ist, wird der vorhandene Wert von dem angegebenen Wert überschrieben. Verwenden Sie in ARM-Vorlagen den `headerAction` von `Overwrite`.</li><li>**Löschen:** Wenn der in der Regel angegebene Header vorhanden ist, wird der Header aus der Anforderung gelöscht. Verwenden Sie in ARM-Vorlagen den `headerAction` von `Delete`.</li></ul> |
| Headername | Der Name des zu ändernden Headers. |
| Headerwert | Der Wert, der angefügt oder überschrieben werden soll. |

### <a name="example"></a>Beispiel

In diesem Beispiel fügen wir den Wert `AdditionalValue` an den `MyRequestHeader`-Anforderungsheader an. Wenn der Ursprung den Antwortheader auf den Wert `ValueSetByClient` festgelegt hat, hätte der Anforderungsheader nach dem Anwenden dieser Aktion den Wert `ValueSetByClientAdditionalValue`.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="./media/rules-actions/modify-request-header.png" alt-text="Screenshot des Portals mit Aktion zum Ändern des Anforderungsheaders.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a>Antwortheader ändern

Verwenden Sie die Aktion **Antwortheader ändern**, um Header zu ändern, die in Antworten vorhanden sind, bevor sie an die Clients zurückgegeben werden.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-------|------------------|
| Betreiber | <ul><li>**Anfügen:** Der in Headername angegebene Header wird mit dem angegebenen Wert zur Antwort hinzugefügt. Wenn der Header bereits vorhanden ist, wird der Wert mithilfe der Zeichenfolgenverkettung an den vorhandenen Headerwert angefügt. Es werden keine Trennzeichen hinzugefügt. Verwenden Sie in ARM-Vorlagen den `headerAction` von `Append`.</li><li>**Überschreiben:** Der in Headername angegebene Header wird mit dem angegebenen Wert zur Antwort hinzugefügt. Wenn der Header bereits vorhanden ist, wird der vorhandene Wert von dem angegebenen Wert überschrieben. Verwenden Sie in ARM-Vorlagen den `headerAction` von `Overwrite`.</li><li>**Löschen:** Wenn der in der Regel angegebene Header vorhanden ist, wird der Header aus der Antwort gelöscht.  Verwenden Sie in ARM-Vorlagen den `headerAction` von `Delete`.</li></ul> |
| Headername | Der Name des zu ändernden Headers. |
| Headerwert | Der Wert, der angefügt oder überschrieben werden soll. |

### <a name="example"></a>Beispiel

In diesem Beispiel löschen wir den Header mit dem Namen `X-Powered-By` aus den Antworten, bevor sie an den Client zurückgegeben werden.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="./media/rules-actions/modify-response-header.png" alt-text="Screenshot des Portals mit Aktion zum Ändern des Antwortheaders.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a>URL Redirect

Verwenden Sie die **URL-Umleitungs**-Aktion, um Clients an eine neue URL umzuleiten. Clients werden eine Umleitungsantwort von Front Door gesendet.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|----------|------------------|
| Umleitungstyp | Der Antworttyp aus, der an den Anforderer zurückgegeben werden soll. <ul><li>Im Azure-Portal: Found („gefunden“) (302), Moved („verschoben“) (301), Temporary Redirect („temporäre Umleitung“) (307), Permanent Redirect („permanente Umleitung“) (308).</li><li>In ARM-Vorlagen: `Found`, `Moved`, `TemporaryRedirect`, `PermanentRedirect`</li></ul> |
| Umleitungsprotokoll | <ul><li>Im Azure-Portal: `Match Request`, `HTTP`, `HTTPS`</li><li>In ARM-Vorlagen: `MatchRequest`, `Http`, `Https`</li></ul> |
| Zielhost | Der Hostname, an den die Anforderung umgeleitet werden soll. Lassen Sie das Feld leer, um den eingehenden Host beizubehalten. |
| Zielpfad | Der bei der Umleitung zu verwendende Pfad. Fügen Sie die führende `/` ein. Lassen Sie das Feld leer, um den eingehenden Pfad beizubehalten. |
| Abfragezeichenfolge | Die bei der Umleitung zu verwendende Abfragezeichenfolge. Fügen Sie den führenden `?` nicht ein. Lassen Sie das Feld leer, um die eingehende Abfragezeichenfolge beizubehalten. |
| Zielfragment | Das bei der Umleitung zu verwendende Fragment. Lassen Sie das Feld leer, um das eingehende Fragment beizubehalten. |

### <a name="example"></a>Beispiel

In diesem Beispiel wird die Anforderung an `https://contoso.com/exampleredirection?clientIp={client_ip}` umgeleitet, während das Fragment beibehalten wird. Eine temporäre HTTP-Umleitung (307) wird verwendet. Die IP-Adresse des Clients wird anstelle des `{client_ip}`-Tokens innerhalb der URL mithilfe der `client_ip` [Servervariablen](#server-variables) verwendet.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="./media/rules-actions/url-redirect.png" alt-text="Screenshot des Portals, das URL-Umleitungsaktion anzeigt.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a>URL Rewrite

Verwenden Sie die **URL-Rewrite**-Aktion, um den Pfad einer Anforderung auf dem Weg zu Ihrem Ursprung erneut zu generieren.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|----------|------------------|
| Quellmuster | Definieren Sie das Quellmuster im URL-Pfad, das ersetzt werden soll. Derzeit verwendet das Quellmuster eine auf Präfixen basierende Übereinstimmung. Verwenden Sie einen Schrägstrich (`/`) als Quellmusterwert, um alle URL-Pfade abzugleichen. |
| Destination | Definieren Sie den Zielpfad, der für das erneute Generieren verwendet werden soll. Der Zielpfad überschreibt das Quellmuster. |
| Nicht übereinstimmenden Pfad beibehalten | Wenn _Ja_ ausgewählt ist, wird der verbleibende Pfad nach dem Quellmuster an den neuen Zielpfad angefügt. |

### <a name="example"></a>Beispiel

In diesem Beispiel werden alle Anforderungen in den `/redirection`-Pfad neu geschrieben, und der Rest des Pfads wird nicht beibehalten.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="./media/rules-actions/url-rewrite.png" alt-text="Screenshot des Portals, das die URL-Rewriteaktion anzeigt.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="origin-group-override"></a> Ursprungsgruppe außer Kraft setzen

Verwenden Sie die Aktion **Ursprungsgruppe außer Kraft setzen**, um die Ursprungsgruppe zu ändern, an die die Anforderung geroutet werden soll.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|----------|------------------|
| Ursprungsgruppe | Die Ursprungsgruppe, an die die Anforderung geroutet werden soll. Dadurch wird die in der Front Door-Endpunktroute angegebene Konfiguration außer Kraft gesetzt. |

### <a name="example"></a>Beispiel

In diesem Beispiel werden alle übereinstimmenden Anforderungen unabhängig von der Konfiguration in der Front Door-Endpunktroute an eine Ursprungsgruppe mit dem Namen `SecondOriginGroup` geroutet.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="./media/rules-actions/origin-group-override.png" alt-text="Screenshot des Portals mit der Aktion „ Ursprungsgruppe außer Kraft setzen“.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "OriginGroupOverride",
  "parameters": {
    "originGroup": {
      "id": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Cdn/profiles/<profile-name>/originGroups/SecondOriginGroup"
    },
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleOriginGroupOverrideActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'OriginGroupOverride'
  parameters: {
    originGroup: {
      id: '/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Cdn/profiles/<profile-name>/originGroups/SecondOriginGroup'
    }
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleOriginGroupOverrideActionParameters'
  }
}
```

---

## <a name="server-variables"></a>Servervariablen

Regelsatz-Servervariablen ermöglichen den Zugriff auf strukturierte Informationen über die Anforderung. Sie können Servervariablen zum dynamischen Ändern von Anforderungs-/Antwortheadern oder URL-Rewrite-Pfaden/Abfragezeichenfolgen, z. B. beim Laden einer neuen Seite oder beim Veröffentlichen eines Formulars verwenden.

### <a name="supported-variables"></a>Unterstützte Variablen

| Variablenname    | BESCHREIBUNG                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | Die IP-Adresse der direkten Verbindung mit dem Azure Front Door-Edge. Wenn der Client einen HTTP-Proxy oder einen Lastenausgleich zum Senden der Anforderung verwendet hat, ist der Wert von `socket_ip` die IP-Adresse des Proxys oder Lastenausgleichs.                                                                      |
| `client_ip`      | Die IP-Adresse des Clients, der die ursprüngliche Anforderung gestellt hat. Wenn in der Anforderung ein `X-Forwarded-For`-Header vorhanden ist, wird die Client-IP-Adresse aus dem Header ausgewählt.                                                                                                               |
| `client_port`    | Der IP-Port des Clients, der die Anforderung gestellt hat.                                                                                                                                                                                                                                          |
| `hostname`       | Der Hostname in der Anforderung des Client.                                                                                                                                                                                                                                             |
| `geo_country`    | Gibt das Herkunftsland/die Herkunftsregion der anfordernden Person über den jeweiligen Länder-/Regionscode an.                                                                                                                                                                                                       |
| `http_method`    | Die Methode, die für die URL-Anforderung verwendet wird, z. B. `GET` oder `POST`.                                                                                                                                                                                                                         |
| `http_version`   | Das Anforderungsprotokoll. Normalerweise `HTTP/1.0` , `HTTP/1.1` oder `HTTP/2.0`.                                                                                                                                                                                                                      |
| `query_string`   | Die Liste der Variablen/Wert-Paare nach dem „?“ in der angeforderten URL.<br />Beispiel: In der Anforderung `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` lautet der `query_string`-Wert von „Host“ `id=123&title=fabrikam`.                                                      |
| `request_scheme` | Das Anforderungsschema: `http` oder `https`.                                                                                                                                                                                                                                                    |
| `request_uri`    | Der vollständige ursprüngliche Anforderungs-URI (mit Argumenten).<br />Beispiel: In der Anforderung `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` lautet der `request_uri`-Wert von „Host“ `/article.aspx?id=123&title=fabrikam`.                                                                     |
| `ssl_protocol`   | Das Protokoll einer hergestellten TLS-Verbindung.                                                                                                                                                                                                                                            |
| `server_port`    | Der Port des Servers, der eine Anforderung akzeptiert hat.                                                                                                                                                                                                                                           |
| `url_path`       | Identifiziert die bestimmte Ressource auf dem Host, auf die der Webclient zugreifen möchte. Dies ist der Teil des Anforderungs-URI ohne die Argumente.<br />Beispiel: In der Anforderung `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` lautet der Wert von `uri_path` `/article.aspx`. |

### <a name="server-variable-format"></a>Format der Servervariablen    

Server Variablen können in den folgenden Formaten angegeben werden:

* `{variable}`: Schließt die gesamte Servervariable ein. Wenn z. B. die Client-IP-Adresse `111.222.333.444` lautet, würde das `{client_ip}`-Token als `111.222.333.444` ausgewertet werden.
* `{variable:offset}`: Schließen Sie die Servervariable nach einem bestimmten Offset bis zum Ende der Variablen ein. Der Offset ist NULL basiert. Wenn z. B. die Client-IP-Adresse `111.222.333.444` lautet, würde das `{client_ip:3}`-Token als `.222.333.444` ausgewertet werden.
* `{variable:offset:length}`: Schließt die Servervariable nach einem bestimmten Offset bis zur angegebenen Länge ein. Der Offset ist NULL basiert. Wenn z. B. die Client-IP-Adresse `111.222.333.444` lautet, würde das `{client_ip:4:3}`-Token als `222` ausgewertet werden.

### <a name="supported-actions"></a>Unterstützte Aktionen

Servervariablen werden für die folgenden Aktionen unterstützt:

* Abfragezeichenfolge für Cacheschlüssel
* Anforderungsheader ändern
* Antwortheader ändern
* URL Redirect
* URL Rewrite

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Regelsätze in Azure Front Door Standard/Premium](standard-premium/concept-rule-set.md).
* Weitere Informationen zum [Azure Front Door-Regelmodul](front-door-rules-engine.md).
* Weitere Informationen zu [Vergleichsbedingungen für Regelsätze](rules-match-conditions.md).
