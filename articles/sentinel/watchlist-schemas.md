---
title: Schemas für Microsoft Sentinel-Watchlistvorlagen | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Schemas, die in jeder integrierten Watchlistvorlage in Microsoft Sentinel verwendet werden.
author: batamig
ms.author: bagold
ms.service: microsoft-sentinel
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: 0d322f7412e32de0c668c3dd82341da81eb12738
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516891"
---
# <a name="microsoft-sentinel-built-in-watchlist-template-schemas-public-preview"></a>In Microsoft Sentinel integrierte Watchlistvorlagenschemas (öffentliche Vorschau)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel erfahren Sie mehr über die Schemas, die in jeder integrierten Watchlistvorlage in Microsoft Sentinel verwendet werden. Weitere Informationen finden Sie unter [Erstellen einer neuen Watchlist mithilfe einer Vorlage (öffentliche Vorschau)](watchlists.md#create-a-new-watchlist-using-a-template-public-preview).

> [!IMPORTANT]
> Die Microsoft Sentinel-Watchlistvorlagen befinden sich derzeit in der VORSCHAU. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>


## <a name="high-value-assets"></a>Anlagen mit hohem Wert

In der Watchlist „Anlagen mit hohem Wert“ sind Geräte, Ressourcen und andere Ressourcen aufgeführt, die einen kritischen Wert in der Organisation haben. Sie enthält die folgenden Felder:

| Feldname | Format                              | Beispiel                                                                                                                                | Obligatorisch/Optional |
| ---------- | ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| **Datenobjekttyp** | String                              | `Device`, `Azure resource`, `AWS resource`, `URL`, `SPO`, `File share`, `Other`                                                                      | Obligatorisch.          |
| **Ressourcen-ID**   | Zeichenfolge, abhängig vom Ressourcentyp | `/subscriptions/d1d8779d-38d7-4f06-91db-9cbc8de0176f/resourceGroups/SOC-Purview/providers/Microsoft.Storage/storageAccounts/purviewadls` | Obligatorisch.          |
| **Datenobjektname** | String                              | `Microsoft.Storage/storageAccounts/purviewadls`                                                                                          | Optional           |
| **Ressourcen-FQDN** | FQDN                                | `Finance-SRv.local.microsoft.com`                                                                                                        | Obligatorisch.          |
| **IP-Adresse** | IP                                  | `1.1.1.1`                                                                                                                                | Optional           |
| **Tags**       | List                                | `["SAW user","Blue Ocean team"] `                                                                                                        | Optional           |
| | | | |

## <a name="vip-users"></a>VIP-Benutzer

Die Watchlist VIP-Benutzer listet Benutzerkonten von Mitarbeitern auf, die einen hohen Auswirkungswert in der Organisation haben, und enthält die folgenden Werte:

| Feldname          | Format | Beispiel                                             | Obligatorisch/Optional |
| ------------------- | ------ | --------------------------------------------------- | ------------------ |
| **Benutzerbezeichner**     | UID    | `52322ec8-6ebf-11eb-9439-0242ac130002`                | Optional           |
| **Benutzer-AAD-Objekt-ID**  | SID    | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | Optional           |
| **Benutzer-ON-PREM-SID**    | SID    | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | Optional           |
| **Benutzerprinzipalname** | UPN    | `JeffL@seccxp.ninja`                                  | Obligatorisch.          |
| **Tags**                | List   | `["SAW user","Blue Ocean team"]`                      | Optional           |
| | | | |

## <a name="network-mapping"></a>Netzwerkzuordnung

Die Watchlist Netzwerkzuordnung listet IP-Subnetze und deren jeweiligen Organisationskontext auf und enthält die folgenden Felder:

| Feldname | Format       | Beispiel                      | Obligatorisch/Optional |
| ---------- | ------------ | ---------------------------- | ------------------ |
| **IP-Subnetz**  | Subnetzbereich |` 198.51.100.0/24 - 198….../22` | Obligatorisch.          |
| **Bereichsname** | String       | `DMZ`                          | Optional           |
| **Tags**       | List         | `["Example","Example"]`        | Optional           |
| | | | |

## <a name="terminated-employees"></a>Ausgeschiedene Mitarbeiter

Die Watchlist Ausgeschiedene Mitarbeiter listet Benutzerkonten von Mitarbeitern auf, die ausgeschieden sind oder ausscheiden werden, und enthält die folgenden Felder:

| Feldname          | Format                                                                          | Beispiel                              | Obligatorisch/Optional |
| ------------------- | ------------------------------------------------------------------------------- | ------------------------------------ | ------------------ |
| **Benutzerbezeichner**     | UID                                                                             | `52322ec8-6ebf-11eb-9439-0242ac130002` | Optional           |
| **Benutzer-AAD-Objekt-ID**  | SID                                                                             | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e` | Optional           |
| **Benutzer-ON-PREM-SID**    | SID                                                                             | `S-1-12-1-4141952679-1282074057-123`   | Optional           |
| **Benutzerprinzipalname** | UPN                                                                             | `JeffL@seccxp.ninja`                  | Obligatorisch.          |
| **UserState**           | String <br><br>Es wird `Notified` oder `Terminated` empfohlen. | `Terminated`                           | Obligatorisch.          |
| **Benachrichtigungsdatum**  | Zeitstempel – Tag                                                                 | `01.12.20`                             | Optional           |
| **Kündigungsdatum**    | Zeitstempel – Tag                                                                 | `01.01.21`                            | Obligatorisch.          |
| **Tags**                | List                                                                            | `["SAW user","Amba Wolfs team"]`       | Optional           |
| | | | |


## <a name="identity-correlation"></a>Identitätskorrelation

Die Watchlist Identitätskorrelation listet verwandte Benutzerkonten auf, zur selben Person gehören, und enthält die folgenden Felder:

| Feldname                       | Format  | Beispiel                                             | Obligatorisch/Optional |
| -------------------------------- | ------- | --------------------------------------------------- | ------------------ |
| **Benutzerbezeichner**                  | UID     | `52322ec8-6ebf-11eb-9439-0242ac130002`                | Optional           |
| **Benutzer-AAD-Objekt-ID**               | SID     | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | Optional           |
| **Benutzer-ON-PREM-SID**                 | SID     | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | Optional           |
| **Benutzerprinzipalname**              | UPN     | `JeffL@seccxp.ninja`                                  | Obligatorisch.          |
| **Mitarbeiter-ID**                      | String  | `8234123`                                             | Optional           |
| **E-Mail**                            | Email   | `JeffL@seccxp.ninja`                                  | Optional           |
| **Zugeordnete privilegierte Konto-ID** | UID/SID | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | Optional           |
| **Zugeordnetes privilegiertes Konto**    | UPN     | `Admin@seccxp.ninja`                                  | Optional           |
| **Tags**                             | List    | `["SAW user","Amba Wolfs team"]`                      | Optional           |
| | | | |

## <a name="service-accounts"></a>Dienstkonten

Die Watchlist Dienstkonten listet Dienstkonten und deren Besitzer auf und enthält die folgenden Felder:

| Feldname                | Format | Beispiel                                             | Obligatorisch/Optional |
| ------------------------- | ------ | --------------------------------------------------- | ------------------ |
| **Dienstbezeichner**        | UID    | `1111-112123-12312312-123123123`                      | Optional           |
| **Dienst-AAD-Objekt-ID**     | SID    | `11123-123123-123123-123123`                          | Optional           |
| **Dienst-ON-PREM-SID**       | SID    | `S-1-12-1-3123123-123213123-12312312-2916039507`      | Optional           |
| **Dienstprinzipalname**    | UPN    | `myserviceprin@contoso.com`                           | Obligatorisch.          |
| **Benutzer-ID des Besitzers**     | UID    | `52322ec8-6ebf-11eb-9439-0242ac130002`                | Optional           |
| **Benutzer-AAD-Objekt-ID des Besitzers**  | SID    | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | Optional           |
| **Benutzer-ON-PREM-SID des Besitzers**    | SID    | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | Optional           |
| **Benutzerprinzipalname des Besitzers** | UPN    | `JeffL@seccxp.ninja`                                  | Obligatorisch.          |
| **Tags**                      | List   | `["Automation Account","GitHub Account"]`             | Optional           |
| | | | |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Verwenden von Microsoft Sentinel-Watchlists](watchlists.md).
