---
title: Geofilterung in einer Domäne für Azure Front Door Service
description: Diese Artikel enthält Informationen zur Geofilterungsrichtlinie für Azure Front Door Service.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: victorh
ms.openlocfilehash: 6d4997253c1b6acfd66d4b126a9249956b18242f
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305402"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door-service"></a>Was ist die Geofilterung in einer Domäne für Azure Front Door Service?

Standardmäßig reagiert Azure Front Door auf alle Benutzeranforderungen unabhängig davon, woher die Anforderung stammt. In einigen Szenarien ist jedoch unter Umständen eine länder-/regionsbasierte Einschränkung des Zugriffs auf Ihre Webanwendungen wünschenswert. Der Web Application Firewall-Dienst (WAF) in Front Door ermöglicht es Ihnen, eine Richtlinie mit benutzerdefinierten Schutzregeln für einen bestimmten Pfad an Ihrem Endpunkt zu definieren, um den Zugriff aus bestimmten Ländern/Regionen zuzulassen oder zu blockieren.

Eine WAF-Richtlinie enthält einige benutzerdefinierte Regeln. Die Regel umfasst Übereinstimmungsbedingungen, eine Aktion und eine Priorität. In einer Übereinstimmungsbedingung werden eine Übereinstimmungsvariable, ein Operator und ein Übereinstimmungswert definiert. Eine Geofilterungsregel setzt sich aus der Übereinstimmungsvariable „REMOTE_ADDR“, dem Operator „GeoMatch“ und einem aus zwei Buchstaben bestehenden Länder-/Regionscode als Wert zusammen. Die Landeskennzahl „ZZ“ oder das Land „Unbekannt“ erfassen IP-Adressen, die noch keinem Land/keiner Region in unserem Dataset zugeordnet sind. Sie können „ZZ“ zu Ihrer Vergleichsbedingung hinzufügen, um False Positive-Ergebnisse zu vermeiden. Sie können eine GeoMatch-Bedingung mit einer Zeichenfolgen-Übereinstimmungsbedingung vom Typ „REQUEST_URI“ kombinieren, um eine pfadbasierte Geofilterungsregel zu erstellen.

Eine Geofilterungsrichtlinie für Front Door kann über [Azure PowerShell](../../frontdoor/front-door-tutorial-geo-filtering.md) oder mithilfe einer [Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-geo-filtering) konfiguriert werden.

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

- Informieren Sie sich über [Sicherheit für die Anwendungsschicht mit Front Door](../../frontdoor/front-door-application-security.md).
- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](../../frontdoor/quickstart-create-front-door.md).
