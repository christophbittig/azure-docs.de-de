---
title: Konfigurieren von Vergleichsbedingungen für Azure Front Door Standard/Premium-Regelsätze
description: Dieser Artikel enthält eine Liste der verschiedenen Vergleichsbedingungen, die für einen Azure Front Door Standard/Premium-Regelsatz verfügbar sind.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: 0765cfdc99024fbac0b117dfc4ae6bad6f04b292
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453552"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Vergleichsbedingungen für Regelsätze in Azure Front Door Standard/Premium (Vorschau)

> [!Note]
> Diese Dokumentation ist für Azure Front Door Standard/Premium (Vorschau) gedacht. Suchen Sie nach Informationen zu Azure Front Door? Informationen finden Sie [hier](../front-door-overview.md).

Eine Regel in einem Azure Front Door Standard/Premium-[Regelsatz](concept-rule-set.md) besteht aus 0 oder mehr Vergleichsbedingungen und einer Aktion. In diesem Artikel werden die Vergleichsbedingungen, die Sie in einem Azure Front Door Standard/Premium-Regelsatz verwenden können, ausführlich beschrieben.

Der erste Teil einer Regel besteht aus einer Übereinstimmungsbedingung oder aus mehreren Übereinstimmungsbedingungen. Eine Regel kann aus bis zu 10 Übereinstimmungsbedingungen bestehen. Eine Übereinstimmungsbedingung gibt bestimmte Typen von Anforderungen an, für die definierte Aktionen ausgeführt werden. Wenn Sie mehrere Übereinstimmungsbedingungen verwenden, werden die Übereinstimmungsbedingungen mit UND-Logik gruppiert. Für alle Übereinstimmungsbedingungen, die mehrere Werte unterstützen, wird OR-Logik verwendet.

Sie können eine Übereinstimmungsbedingung für Folgendes verwenden:

* Filtern von Anforderungen, die auf einer bestimmten IP-Adresse, einem bestimmten Land oder einer bestimmten Region basieren.
* Filtern von Anforderungen nach Headerinformationen
* Filtern von Anforderungen von Mobil- oder Desktopgeräten.
* Filtern von Anforderungen nach Anforderungsdateiname und Anforderungsdateierweiterung
* Filtern von Anforderungen nach Anforderungs-URL, Protokoll, Pfad, Abfragezeichenfolge, POST-Argumenten usw.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Vorschau) befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="device-type"></a><a name="IsDevice"></a> Gerätetyp

Verwenden Sie die Übereinstimmungsbedingung **Gerätetyp**, um Anforderungen zu identifizieren, die von einem mobilen oder einem Desktop-Gerät gestellt wurden.  

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-------|------------------|
| Betreiber | <ul><li>Im Azure Portal: `Equal`, `Not Equal`</li><li>In ARM-Vorlagen: `Equal`; verwenden Sie die `negateCondition`-Eigenschaft, um _Nicht Gleich_ anzugeben. |
| Wert | `Mobile`, `Desktop` |

### <a name="example"></a>Beispiel

In diesem Beispiel stimmen wir alle Anforderungen ab, die erkannt wurden, wenn Sie von einem mobilen Gerät stammen.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="Screenshot des Portals mit der Art der Übereinstimmungsbedingung":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> Post args

Verwenden Sie die Übereinstimmungsbedingung **Post args**, um Anforderungen auf Grundlage der Argumente im Text einer POST-Anforderung zu identifizieren. Eine einzelne Übereinstimmungsbedingung entspricht einem einzelnen Argument aus dem Text der POST-Anforderung. Sie können mehrere Werte angeben, die mit der OR-Logik kombiniert werden.

> [!NOTE]
> Die Übereinstimmungsbedingung **Post args** funktioniert mit dem `application/x-www-form-urlencoded`-Inhaltstyp.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-|-|
| Post args | Ein Zeichenfolgenwert, der den Namen des POST-Arguments darstellt. |
| Betreiber | Beliebiger Operator aus der [Standardoperator-Liste](#operator-list). |
| Wert | Mindestens ein Zeichenfolgenwert oder ganzzahliger Wert, der den Wert des zu abgleichenden Post-Arguments darstellt. Wenn mehrere Werte angegeben werden, werden Sie mit der OR-Logik ausgewertet. |
| Umwandlung der Groß-/Kleinschreibung | `Lowercase`, `Uppercase` |

### <a name="example"></a>Beispiel

In diesem Beispiel stimmen wir alle Post-Anforderungen ab, bei denen ein `customerName`-Argument im Anforderungstext bereitgestellt wird und der Wert von `customerName` mit dem Buchstaben `J` oder`K`beginnt. Wir verwenden eine Falltransformation, um die Eingabewerte in Großbuchstaben zu konvertieren, sodass Werte übereinstimmen, die mit `J`, `j`, `K` und `k` beginnen.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="Screenshot des Portals mit der Übereinstimmungsbedingung „post args“.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> Abfragezeichenfolge

Verwenden Sie die Übereinstimmungsbedingung **Abfragezeichenfolge** zum Identifizieren von Anforderungen, die eine bestimmte Abfragezeichenfolge enthalten. Sie können mehrere Werte angeben, die mit der OR-Logik kombiniert werden.

> [!NOTE]
> Die gesamte Abfragezeichenfolge wird mit einer einzelnen Zeichenfolge ohne den führenden `?` abgeglichen.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-|-|
| Betreiber | Beliebiger Operator aus der [Standardoperator-Liste](#operator-list). |
| Abfragezeichenfolge | Mindestens ein Zeichenfolgenwert oder ganzzahliger Wert, der den Wert der abzugleichenden Abfragezeichenfolge darstellt. Die `?` nicht am Anfang der Abfragezeichenfolge einbeziehen. Wenn mehrere Werte angegeben werden, werden Sie mit der OR-Logik ausgewertet. |
| Umwandlung der Groß-/Kleinschreibung | `Lowercase`, `Uppercase` |

### <a name="example"></a>Beispiel

In diesem Beispiel stimmen wir mit allen Anforderungen ab, bei denen die Abfrage Zeichenfolge die Zeichenfolge `language=en-US` enthält. Wir möchten, dass die Übereinstimmungsbedingung die Groß-/Kleinschreibung beachtet, also transformieren wir den Fall nicht.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="Screenshot des Portals mit der Übereinstimmungsbedingung der Abfragezeichenfolge.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> Remoteaddresse

Die Übereinstimmungsbedingung für die **Remoteadresse** identifiziert Anforderungen anhand des Standorts oder anhand der IP-Adresse des Anfordernden. Sie können mehrere Werte angeben, die mit der OR-Logik kombiniert werden.

* Verwenden Sie CIDR-Notation, wenn Sie IP-Adressblöcke angeben. Das heißt, die Syntax für einen IP-Adressblock besteht aus der IP-Basisadresse, gefolgt von einem Schrägstrich und der Präfixgröße. Beispiel:
    * **IPv4-Beispiel:** `5.5.5.64/26` entspricht allen Anforderungen, die von den Adressen 5.5.5.64 bis 5.5.5.127 eingehen.
    * **IPv6-Beispiel:** Die Angabe `1:2:3:/48` führt zur Übereinstimmung mit eingehenden Anforderungen von den Adressen 1:2:3:0:0:0:0:0 bis 1:2:3: ffff:ffff:ffff:ffff:ffff.
* Bei mehreren IP-Adressen und IP-Adressblöcken wird die OR-Logik verwendet.
    * **IPv4-Beispiel**: Wenn Sie die beiden IP-Adressen `1.2.3.4` und `10.20.30.40` hinzufügen, ist die Bedingung erfüllt, wenn Anforderungen von der Adresse 1.2.3.4 oder 10.20.30.40 eintreffen.
    * **IPv6-Beispiel**: Wenn Sie die beiden IP-Adressen `1:2:3:4:5:6:7:8` und `10:20:30:40:50:60:70:80` hinzufügen, ist die Bedingung bei allen Anforderungen von den Adressen 1:2:3:4:5:6:7:8 und 10:20:30:40:50:60:70:80 erfüllt.
* Die Remote-Adresse stellt die ursprüngliche Client-IP dar, die entweder aus der Netzwerkverbindung oder typischerweise aus dem X-Forwarded-For Anforderungsheader stammt, wenn sich der Benutzer hinter einem Proxy befindet.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-|-|
| Betreiber | <ul><li>Im Azure Portal: `Geo Match`, `Geo Not Match`, `IP Match`, oder `IP Not Match`</li><li>In ARM-Vorlagen: `GeoMatch`, `IPMatch` ; verwenden Sie die `negateCondition` -Eigenschaft, um die _Geo Not Match_ oder _IP Not Match_ anzugeben.</li></ul> |
| Wert | <ul><li>Für die `IP Match`- oder `IP Not Match`-Operatoren oder: Geben Sie einen oder mehrere IP-Adressbereich(e) an. Wenn mehrere IP-Adressbereiche angegeben werden, werden Sie mit der OR-Logik ausgewertet.</li><li>Für den `Geo Match`- oder `Geo Not Match`-Operator: Geben Sie einen oder mehrere Speicherorte mithilfe Ihres Ländercodes an.</li></ul> |

### <a name="example"></a>Beispiel

In diesem Beispiel entsprechen wir allen Anforderungen, bei denen die Anforderung nicht aus den USA stammt.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="Screenshot des Portals mit der Übereinstimmungsbedingung einer Remote-Adresse.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> Anforderungstext

Der **Anforderungstext** identifiziert Anforderungen auf der Grundlage eines bestimmten Texts, der im Textkörper der Anforderung vorhanden ist. Sie können mehrere Werte angeben, die mit der OR-Logik kombiniert werden.

> [!NOTE]
> Wenn ein Anforderungstext die Größe von 64 KB überschreitet, werden nur die ersten 64 KB für die Übereinstimmungsbedingung des **Anforderungstexts** berücksichtigt.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-|-|
| Betreiber | Beliebiger Operator aus der [Standardoperator-Liste](#operator-list). |
| Wert | Mindestens ein Zeichenfolgenwert oder ganzzahliger Wert, der den Wert des abzugleichenden Anforderungstexts darstellt. Wenn mehrere Werte angegeben werden, werden Sie mit der OR-Logik ausgewertet. |
| Umwandlung der Groß-/Kleinschreibung | `Lowercase`, `Uppercase` |

### <a name="example"></a>Beispiel

In diesem Beispiel stimmen wir alle Anforderungen ab, bei denen der Anforderungstext die `ERROR`-Zeichenfolge enthält. Wir transformieren den Anforderungstext vor dem Auswerten der Entsprechung in Großbuchstaben, sodass `error` und andere Fallvariationen diese Übereinstimmungsbedingung ebenfalls auslöst.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="Screenshot des Portals mit Übereinstimmungsbedingung des Anforderungstexts.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> Dateiname der Anforderung

Die Übereinstimmungsbedingung **Dateiname der Anforderung** identifiziert Anforderungen, die den angegebenen Dateinamen in der Anforderungs-URL enthalten. Sie können mehrere Werte angeben, die mit der OR-Logik kombiniert werden.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-|-|
| Betreiber | Beliebiger Operator aus der [Standardoperator-Liste](#operator-list). |
| Wert | Mindestens ein Zeichenfolgenwert oder ganzzahliger Wert, der den Wert des abzugleichenden Dateinamen der Anforderung darstellt. Wenn mehrere Werte angegeben werden, werden Sie mit der OR-Logik ausgewertet. |
| Umwandlung der Groß-/Kleinschreibung | `Lowercase`, `Uppercase` |

### <a name="example"></a>Beispiel

In diesem Beispiel stimmen wir alle Anforderungen ab, bei denen der Name der Anforderungsdatei `media.mp4` ist. Wir transformieren den Dateinamen vor dem Auswerten der Entsprechung in Kleinbuchstaben, sodass `MEDIA.MP4` und andere Fallvariationen diese Übereinstimmungsbedingung ebenfalls auslöst.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="Screenshot des Portals mit Übereinstimmungsbedingung des Anforderungsdateinamens.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> Dateierweiterung der Anforderung

Die Übereinstimmungsbedingung **Dateierweiterung der Anforderung** identifiziert Anforderungen, die die angegebenen Dateierweiterungen in der Anforderungs-URL enthalten. Sie können mehrere Werte angeben, die mit der OR-Logik kombiniert werden.

> [!NOTE]
> Schließen Sie keinen führenden Zeitraum ein. Verwenden Sie z. B. `html` statt `.html`.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-|-|
| Betreiber | Beliebiger Operator aus der [Standardoperator-Liste](#operator-list). |
| Wert | Mindestens ein Zeichenfolgenwert oder ganzzahliger Wert, der den Wert der abzugleichenden Dateierweiterung der Anforderung darstellt. Schließen Sie keinen führenden Zeitraum ein. Wenn mehrere Werte angegeben werden, werden Sie mit der OR-Logik ausgewertet. |
| Umwandlung der Groß-/Kleinschreibung | `Lowercase`, `Uppercase` |

### <a name="example"></a>Beispiel

In diesem Beispiel stimmen wir alle Anforderungen ab, bei denen die Erweiterung der Anforderungsdatei `pdf` oder `docx` ist. Wir transformieren die Dateierweiterung der Anforderung vor dem Auswerten der Entsprechung in Kleinbuchstaben, sodass `PDF`, `DocX` und andere Fallvariationen diese Übereinstimmungsbedingung ebenfalls auslöst.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="Screenshot des Portals mit Übereinstimmungsbedingung der Anforderungsdateierweiterung.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> Anforderungsheader

Mit der Übereinstimmungsbedingung für den **Anforderungsheader** werden Anforderungen identifiziert, die einen bestimmten Header in der Anforderung enthalten. Mit dieser Übereinstimmungsbedingung können Sie überprüfen, ob ein Header einen beliebigen Wert hat, oder ob der Header mit einem angegebenen Wert übereinstimmt. Sie können mehrere Werte angeben, die mit der OR-Logik kombiniert werden.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-|-|
| Headername | Ein Zeichenfolgenwert, der den Namen des POST-Arguments darstellt. |
| Betreiber | Beliebiger Operator aus der [Standardoperator-Liste](#operator-list). |
| Wert | Mindestens ein Zeichenfolgenwert oder ganzzahliger Wert, der den Wert des abzugleichenden Anforderungsheaders darstellt. Wenn mehrere Werte angegeben werden, werden Sie mit der OR-Logik ausgewertet. |
| Umwandlung der Groß-/Kleinschreibung | `Lowercase`, `Uppercase` |

### <a name="example"></a>Beispiel

In diesem Beispiel stimmen wir allen Anforderungen zu, bei denen die Anforderung einen Header mit dem Namen `MyCustomHeader` enthält, unabhängig von dessen Wert.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="Screenshot des Portals mit Übereinstimmungsbedingung des Anforderungsheaders.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> Anforderungsmethode

Die Übereinstimmungsbedingung der **Anforderungsmethode** identifiziert Anforderungen, die die angegebene HTTP-Anforderungsmethode verwenden. Sie können mehrere Werte angeben, die mit der OR-Logik kombiniert werden.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-|-|
| Betreiber | <ul><li>Im Azure Portal: `Equal`, `Not Equal`</li><li>In ARM-Vorlagen: `Equal`; verwenden Sie die `negateCondition`-Eigenschaft, um _Nicht Gleich_ anzugeben. |
| Anforderungsmethode | Mindestens eine HTTP-Methode aus: `GET`, `POST`, `PUT`, `DELETE`, `HEAD`, `OPTIONS`, `TRACE`. Wenn mehrere Werte angegeben werden, werden Sie mit der OR-Logik ausgewertet. |

### <a name="example"></a>Beispiel

In diesem Beispiel stimmen wir alle Anforderungen ab, die die Anforderungsmethode `DELETE` nutzt.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="Screenshot des Portals mit Übereinstimmungsbedingung der Anforderungsmethode.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> Anforderungspfad

Die Übereinstimmungsbedingung **Anforderungspfad** identifiziert Anforderungen, die den angegebenen Pfad in der Anforderungs-URL enthalten. Sie können mehrere Werte angeben, die mit der OR-Logik kombiniert werden.

> [!NOTE]
> Der Pfad ist der Teil der URL nach dem Hostnamen und einem Schrägstrich. In der URL `https://www.contoso.com/files/secure/file1.pdf` lautet der Pfad z. B. `files/secure/file1.pdf`.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-|-|
| Betreiber | Beliebiger Operator aus der [Standardoperator-Liste](#operator-list). |
| Wert | Mindestens ein Zeichenfolgenwert oder ganzzahliger Wert, der den Wert des abzugleichenden Anforderungspfades darstellt. Fügen Sie den führenden Schrägstrich nicht ein. Wenn mehrere Werte angegeben werden, werden Sie mit der OR-Logik ausgewertet. |
| Umwandlung der Groß-/Kleinschreibung | `Lowercase`, `Uppercase` |

### <a name="example"></a>Beispiel

In diesem Beispiel stimmen wir alle Anforderungen ab, bei denen der Pfad der Anforderungsdatei mit `files/secure/` beginnt. Wir transformieren die Dateierweiterung der Anforderung vor dem Auswerten der Entsprechung in Kleinbuchstaben, sodass Anforderungen an `files/SECURE/` und andere Fallvariationen diese Übereinstimmungsbedingung ebenfalls auslöst.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="Screenshot des Portals mit Übereinstimmungsbedingung des Anforderungspfades.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> Anforderungsprotokoll

Mit der Übereinstimmungsbedingung **Anforderungsprotokoll** werden Anforderungen identifiziert, die das angegebene Protokoll (HTTP oder HTTPS) verwenden.

> [!NOTE]
> Das *Protokoll* wird manchmal auch als *Schema* bezeichnet.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-|-|
| Betreiber | <ul><li>Im Azure Portal: `Equal`, `Not Equal`</li><li>In ARM-Vorlagen: `Equal`; verwenden Sie die `negateCondition`-Eigenschaft, um _Nicht Gleich_ anzugeben. |
| Anforderungsmethode | `HTTP`, `HTTPS` |

### <a name="example"></a>Beispiel

In diesem Beispiel stimmen wir alle Anforderungen ab, die das Anforderungsprotokoll `HTTP` nutzen.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="Screenshot des Portals mit Übereinstimmungsbedingung des Anforderungsprotokolls.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> Anforderungs-URL

Identifiziert Anforderungen, die mit der angegebenen URL übereinstimmen. Die gesamte URL wird ausgewertet, einschließlich des Protokolls und der Abfragezeichenfolge, jedoch nicht des Fragments. Sie können mehrere Werte angeben, die mit der OR-Logik kombiniert werden.

> [!TIP]
> Wenn Sie diese Regelbedingung verwenden, achten Sie darauf, dass Sie das Protokoll einschließen. Verwenden Sie beispielsweise `https://www.contoso.com` anstelle von nur `www.contoso.com`.

### <a name="properties"></a>Eigenschaften

| Eigenschaft | Unterstützte Werte |
|-|-|
| Betreiber | Beliebiger Operator aus der [Standardoperator-Liste](#operator-list). |
| Wert | Mindestens ein Zeichenfolgenwert oder ganzzahliger Wert, der den Wert der abzugleichenden Anforderungs-URL darstellt. Wenn mehrere Werte angegeben werden, werden Sie mit der OR-Logik ausgewertet. |
| Umwandlung der Groß-/Kleinschreibung | `Lowercase`, `Uppercase` |

### <a name="example"></a>Beispiel

In diesem Beispiel stimmen wir alle Anforderungen ab, bei denen die URL der Anforderung mit `https://api.contoso.com/customers/123` beginnt. Wir transformieren die Dateierweiterung der Anforderung vor dem Auswerten der Entsprechung in Kleinbuchstaben, sodass Anforderungen an `https://api.contoso.com/Customers/123` und andere Fallvariationen diese Übereinstimmungsbedingung ebenfalls auslöst.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="Screenshot des Portals mit Übereinstimmungsbedingung der Anforderungs-URL.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a>Operatorliste

Für Regeln, die Werte aus der Liste der Standardoperatoren akzeptieren, sind die folgenden Operatoren gültig:

| Operator                   | BESCHREIBUNG                                                                                                                    | ARM-Vorlagen-Support                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Any                        | Ergibt ein Match, wenn ein beliebiger Wert vorhanden ist, unabhängig davon, worum es sich dabei handelt.                                                                     | `operator`: `Any`                                               |
| Equal                      | Ergibt ein Match, wenn der Wert exakt mit der angegebenen Zeichenfolge übereinstimmt.                                                                   | `operator`: `Equal`                                             |
| Enthält                   | Ergibt ein Match, wenn der Wert die angegebene Zeichenfolge enthält.                                                                          | `operator`: `Contains`                                          |
| Kleiner als                  | Ergibt ein Match, wenn die Länge des Werts kleiner als die angegebene Ganzzahl ist.                                                       | `operator`: `LessThan`                                          |
| Größer als               | Ergibt ein Match, wenn die Länge des Werts größer als die angegebene Ganzzahl ist.                                                    | `operator`: `GreaterThan`                                       |
| Kleiner als oder gleich         | Ergibt ein Match, wenn die Länge des Werts kleiner als oder gleich der angegebenen Ganzzahl ist.                                           | `operator`: `LessThanOrEqual`                                   |
| Größer als oder gleich      | Ergibt ein Match, wenn die Länge des Werts größer als oder gleich der angegebenen Ganzzahl ist.                                        | `operator`: `GreaterThanOrEqual`                                |
| Beginnt mit                | Ergibt ein Match, wenn der Wert mit der angegebenen Zeichenfolge beginnt.                                                                       | `operator`: `BeginsWith`                                        |
| Endet mit                  | Ergibt ein Match, wenn der Wert mit der angegebenen Zeichenfolge endet.                                                                         | `operator`: `EndsWith`                                          |
| RegEx                      | Ergibt ein Match, wenn der Wert dem angegebenen regulären Ausdruck entspricht. [Weitere Informationen finden Sie weiter unten.](#regular-expressions)        | `operator`: `RegEx`                                             |
| Keine                    | Ergibt ein Match, wenn kein Wert vorhanden ist.                                                                                                | `operator`: `Any` und `negateCondition` : `true`                |
| Ungleich                  | Ergibt ein Match, wenn der Wert nicht mit der angegebenen Zeichenfolge übereinstimmt.                                                                    | `operator`: `Equal` und `negateCondition` : `true`              |
| Not Contains               | Ergibt ein Match, wenn der Wert nicht die angegebene Zeichenfolge enthält.                                                                  | `operator`: `Contains` und `negateCondition` : `true`           |
| Nicht kleiner als              | Ergibt ein Match, wenn die Länge des Werts nicht kleiner als die angegebene Ganzzahl ist.                                                   | `operator`: `LessThan` und `negateCondition` : `true`           |
| Nicht größer als           | Ergibt ein Match, wenn die Länge des Werts nicht größer als die angegebene Ganzzahl ist.                                                | `operator`: `GreaterThan` und `negateCondition` : `true`        |
| Nicht kleiner als oder gleich     | Ergibt ein Match, wenn die Länge des Werts nicht kleiner als oder gleich der angegebenen Ganzzahl ist.                                       | `operator`: `LessThanOrEqual` und `negateCondition` : `true`    |
| Nicht größer als oder gleich | Ergibt ein Match, wenn die Länge des Werts nicht größer als oder gleich der angegebenen Ganzzahl ist.                                    | `operator`: `GreaterThanOrEqual` und `negateCondition` : `true` |
| Beginnt nicht mit            | Ergibt ein Match, wenn der Wert nicht mit der angegebenen Zeichenfolge beginnt.                                                               | `operator`: `BeginsWith` und `negateCondition` : `true`         |
| Endet nicht mit              | Ergibt ein Match, wenn der Wert nicht mit der angegebenen Zeichenfolge endet.                                                                 | `operator`: `EndsWith` und `negateCondition` : `true`           |
| Nicht RegEx                  | Ergibt ein Match, wenn der Wert nicht dem angegebenen regulären Ausdruck entspricht. [Weitere Informationen finden Sie weiter unten.](#regular-expressions) | `operator`: `RegEx` und `negateCondition` : `true`              |

> [!TIP]
> Für numerische Operatoren wie *Kleiner als* oder *Größer als oder gleich* basiert der verwendete Vergleich auf der Länge. Der Wert in der Übereinstimmungsbedingung muss eine ganze Zahl sein, die die Länge bestimmt, die Sie vergleichen möchten.

### <a name="regular-expressions"></a><a name="regular-expressions"></a>Reguläre Ausdrücke

Reguläre Ausdrücke unterstützen die folgenden Vorgänge nicht:

* Rückverweise und Erfassung von Teilausdrücken.
* Willkürliche Assertionen mit einer Nullbreite.
* Unterroutinenverweise und rekursive Muster.
* Bedingte Muster.
* Rückverfolgung von Steuerelementverben.
* Die `\C` Einzelbyte-Anweisung – „\C“.
* Die `\R` Anweisung für Zeilenvorschubübereinstimmung – „\R“.
* Die `\K` Startanweisung zum Zurücksetzen der Übereinstimmung – „\K“.
* Callouts und eingebetteter Code.
* Atomische Gruppierung und besitzanzeigende Quantifizierer.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Regelsätze](concept-rule-set.md).
* Erfahren Sie, wie Sie Ihren[ ersten Regelsatz konfigurieren](how-to-configure-rule-set.md).
* Weitere Informationen über [Regelsatzaktionen](concept-rule-set-actions.md)
