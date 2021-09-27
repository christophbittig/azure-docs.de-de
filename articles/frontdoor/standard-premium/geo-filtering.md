---
title: Geofilterung in einer Domäne für Azure Front Door Standard/Premium | Microsoft Dokumenation
description: In diesem Artikel erfahren Sie mehr über die Geofilterungsrichtlinie für Azure Front Door Standard/Premium
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2021
ms.author: duau
ms.reviewer: amsriva
ms.openlocfilehash: add0e50e178e56e243b6137d24f2be245b99a82d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440790"
---
# <a name="geo-filtering-on-a-domain-for-azure-front-door-standardpremium-preview"></a>Geofilterung auf einer Domäne für Azure Front Door Standard/Premium (Vorschau)

Standardmäßig reagiert Azure Front Door auf alle Benutzeranforderungen unabhängig davon, woher die Anforderung stammt. In einigen Szenarien ist jedoch unter Umständen eine länder-/regionsbasierte Einschränkung des Zugriffs auf Ihre Webanwendungen wünschenswert. Der Web Application Firewall-Dienst (WAF) in Front Door ermöglicht es Ihnen, eine Richtlinie mit benutzerdefinierten Schutzregeln für einen bestimmten Pfad an Ihrem Endpunkt zu definieren, um den Zugriff aus bestimmten Ländern/Regionen zuzulassen oder zu blockieren.

Eine WAF-Richtlinie enthält einige benutzerdefinierte Regeln. Die Regel umfasst Übereinstimmungsbedingungen, eine Aktion und eine Priorität. In einer Übereinstimmungsbedingung werden eine Übereinstimmungsvariable, ein Operator und ein Übereinstimmungswert definiert. Eine Geofilterungsregel setzt sich aus der Übereinstimmungsvariable „REMOTE_ADDR“, dem Operator „GeoMatch“ und einem aus zwei Buchstaben bestehenden Länder-/Regionscode als Wert zusammen. Die Landeskennzahl „ZZ“ oder das Land „Unbekannt“ erfassen IP-Adressen, die noch keinem Land/keiner Region in unserem Dataset zugeordnet sind. Sie können „ZZ“ zu Ihrer Vergleichsbedingung hinzufügen, um False Positive-Ergebnisse zu vermeiden. Sie können eine GeoMatch-Bedingung mit einer Zeichenfolgen-Übereinstimmungsbedingung vom Typ „REQUEST_URI“ kombinieren, um eine pfadbasierte Geofilterungsregel zu erstellen.

Sie können über das [Azure-Portal](../../web-application-firewall/afds/waf-front-door-create-portal.md) oder über eine [Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-geo-filtering) eine Geofilterungsrichtlinie für Ihre Front Door konfigurieren.

> [!IMPORTANT]
> Geben Sie den Landeskennzahl **ZZ** an, wenn Sie die Funktion Geofilterung verwenden. Die Landeskennzahl **ZZ** (oder das Land *Unbekannt*) erfasst IP-Adressen, die noch keinem Land in unserem Dataset zugeordnet sind. Dadurch werden falsch positive Ergebnisse vermieden.

## <a name="countryregion-code-reference"></a>Referenz zu Länder-/Regionscodes

|Länder-/Regionscode | Name des Landes / der Region |
| ----- | ----- |
| AD | Andorra |
| AE | Vereinigte Arabische Emirate|
| AF | Afghanistan|
| Verfügbarkeitsgruppe | Antigua und Barbuda|
| AL | Albanien|
| AM | Armenien|
| AO | Angola|
| AR | Argentinien|
| AS | Amerikanisch-Samoa|
| AT | Österreich|
| AU | Australien|
| RP | Aserbaidschan|
| BA | Bosnien und Herzegowina|
| BB | Barbados|
| BD | Bangladesch|
| BE | Belgien|
| BF | Burkina Faso|
| BG | Bulgarien|
| BH | Bahrain|
| BI | Burundi|
| BJ | Benin|
| BL | St. Barthélemy|
| BN | Brunei Darussalam|
| BO | Bolivien|
| BR | Brasilien|
| BS | Bahamas|
| BT | Bhutan|
| BW | Botsuana|
| BY | Belarus|
| BZ | Belize|
| CA | Canada|
| CD | Demokratische Republik Kongo|
| CG | Republik Kongo |
| CF | Zentralafrikanische Republik|
| CH | Schweiz|
| CI | Côte d'Ivoire|
| CL | Chile|
| CM | Kamerun|
| CN | China|
| CO | Kolumbien|
| CR | Costa Rica|
| CU | Kuba|
| CV | Cabo Verde|
| CY | Zypern|
| CZ | Tschechische Republik|
| DE | Deutschland|
| DK | Dänemark|
| DO | Dominikanische Republik|
| DZ | Algerien|
| EC | Ecuador|
| EE | Estland|
| EG | Ägypten|
| ES | Spanien|
| ET | Äthiopien|
| FI | Finnland|
| FJ | Fidschi|
| FM | Föderierte Staaten von Mikronesien|
| BV | Frankreich|
| GB | United Kingdom|
| GE | Georgien|
| GF | Französisch-Guayana|
| GH | Ghana|
| GN | Guinea|
| GP | Guadeloupe|
| GR | Griechenland|
| GT | Guatemala|
| GY | Guayana|
| HK | Hongkong (SAR)|
| HN | Honduras|
| HR | Kroatien|
| HT | Haiti|
| HU | Ungarn|
| id | Indonesien|
| IE | Irland|
| BY | Israel|
| IN | Indien|
| IQ | Irak|
| IR | Islamische Republik Iran|
| IS | Island|
| IT | Italien|
| JM | Jamaika|
| JO | Jordan|
| JP | Japan|
| KE | Kenia|
| KG | Kirgisistan|
| KH | Kambodscha|
| KI | Kiribati|
| KN | St. Kitts und Nevis|
| KP | Demokratische Volksrepublik Korea|
| KR | Republik Korea|
| KW | Kuwait|
| HE | Kaimaninseln|
| KZ | Kasachstan|
| LA | Demokratische Volksrepublik Laos|
| LB | Libanon|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Litauen|
| LU | Luxemburg|
| LV | Lettland|
| LY | Libyen |
| NI | Marokko|
| MD | Republik Moldau|
| MG | Madagaskar|
| MK | Nordmazedonien|
| ML | Mali|
| MM | Myanmar|
| BB | Mongolei|
| MO | Macau (SAR)|
| MQ | Martinique|
| MR | Mauretanien|
| MT | Malta|
| MV | Malediven|
| MW | Malawi|
| MX | Mexiko|
| MY | Malaysia|
| MZ | Mosambik|
| Nicht verfügbar | Namibia|
| NE | Niger|
| NG | Nigeria|
| NI | Nicaragua|
| NL | Niederlande|
| Nein | Norwegen|
| NP | Nepal|
| NR | Nauru|
| NZ | Neuseeland|
| OM | Oman|
| PA | Panama|
| PE | Peru|
| PH | Philippinen|
| PK | Pakistan|
| PL | Polen|
| PR | Puerto Rico|
| PT | Portugal|
| PW | Palau|
| PY | Paraguay|
| QA | Katar|
| RE | Réunion|
| RO | Rumänien|
| RS | Serbien|
| RU | Russische Föderation|
| RW | Ruanda|
| SA | Saudi-Arabien|
| SD | Sudan|
| SE | Schweden|
| SG | Singapur|
| SI | Slowenien|
| SK | Slowakei|
| SN | Senegal|
| SO | Somalia|
| SR | Surinam|
| SS | Südsudan|
| SV | El Salvador|
| SY | Arabische Republik Syrien|
| SZ | Swasiland|
| TC | Turks- und Caicosinseln|
| TG | Togo|
| TH | Thailand|
| TN | Tunesien|
| TR | Türkei|
| TT | Trinidad und Tobago|
| TW | Taiwan|
| TZ | Vereinigte Republik Tansania|
| UA | Ukraine|
| UG | Uganda|
| US | USA|
| UY | Uruguay|
| UZ | Usbekistan|
| VC | St. Vincent und die Grenadinen|
| VE | Venezuela|
| VG | Britische Jungferninseln|
| VI | Amerikanische Jungferninseln|
| VN | Vietnam|
| ZA | Südafrika|
| ZM | Sambia|
| ZW | Simbabwe|

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Erstellen einer Instanz von Front Door Standard/Premium](create-front-door-portal.md).
- Informationen zum [Einrichten einer WAF-Richtlinie für Geofilterung](../../web-application-firewall/afds/waf-front-door-create-portal.md).
