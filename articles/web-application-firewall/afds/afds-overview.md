---
title: Was ist Azure Web Application Firewall für Azure Front Door?
description: Erfahren Sie, wie Azure Web Application Firewall für den Azure Front Door-Dienst Ihre Webanwendungen vor böswilligen Angriffen schützt.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 06/09/2021
ms.author: victorh
ms.openlocfilehash: 09c184f9f3e62d8f26a2baaaa3fb31f06f4dbfca
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292906"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Azure Web Application Firewall für Azure Front Door

Azure Web Application Firewall (WAF) für Azure Front Door bietet zentralen Schutz für Ihre Webanwendungen. WAF schützt Ihre Webdienste von gängigen Exploits und Sicherheitsrisiken. Die Lösung gewährleistet eine hohe Verfügbarkeit Ihres Diensts für Benutzer und unterstützt Sie bei der Erfüllung von Complianceanforderungen.

WAF für Front Door ist eine globale und zentrale Lösung. Sie wird an Edgestandorten des Azure-Netzwerks auf der ganzen Welt bereitgestellt. WAF-fähige Webanwendungen untersuchen jede eingehende Anforderung, die von Front Door im Edgebereich des Netzwerks übermittelt wird. 

WAF vereitelt Angriffe in der Nähe der Angriffsquellen, bevor sie Ihr virtuelles Netzwerk erreichen. Sie erhalten globalen bedarfsgerechten Schutz ohne Leistungseinbußen. Eine WAF-Richtlinie kann problemlos mit einem beliebigen Front Door-Profil in Ihrem Abonnement verknüpft werden. Neue Regeln lassen sich innerhalb weniger Minuten bereitstellen, um schnell auf veränderte Bedrohungsmuster zu reagieren.

![Azure Web Application Firewall](../media/overview/wafoverview.png)

Azure-Front-Door führt [Vorschauversionen von zwei neuen SKUs](../../frontdoor/standard-premium/overview.md)ein: Die Front Door Standard und die Front Door Premium SKU. WAF ist in der Front Door Premium SKU mit vollständigem Funktionsumfang nativ integriert. Bei der Standard-SKU von Front Door werden nur [benutzerdefinierte Regeln](#custom-authored-rules) unterstützt.

## <a name="waf-policy-and-rules"></a>WAF-Richtlinien und -Regeln

Sie können eine [WAF-Richtlinie](waf-front-door-create-portal.md) konfigurieren und diese Richtlinie einem oder mehreren Front Door-Front-Ends als Schutz zuordnen. Eine WAF-Richtlinie besteht aus zwei verschiedenen Arten von Sicherheitsregeln:

- benutzerdefinierte Regeln, die vom Kunden erstellt werden

- verwaltete Regelsätze (d.h. eine Sammlung von vorkonfigurierten Regelsätzen, die von Azure verwaltet werden)

Wenn beides vorhanden ist, werden die benutzerdefinierten Regeln vor den Regeln eines verwalteten Regelsatzes verarbeitet. Eine Regel besteht aus einer Übereinstimmungsbedingung, einer Priorität und einer Aktion. Folgende Aktionstypen werden unterstützt: ALLOW, BLOCK, LOG und REDIRECT. Sie können eine vollständig angepasste Richtlinie erstellen, die Ihre speziellen Anforderungen an die Anwendungssicherheit erfüllt, indem Sie verwaltete und benutzerdefinierte Regeln kombinieren.

Die Regeln innerhalb einer Richtlinie werden nach Priorität verarbeitet. Bei der Priorität handelt es sich um eine eindeutige ganze Zahl, die die Reihenfolge der Regelverarbeitung definiert. Kleinere ganzzahlige Wert stehen für eine höhere Priorität und werden vor Regeln mit einem höheren ganzzahligen Wert ausgewertet. Sobald eine Übereinstimmung mit einer Regel erkannt wird, wird die entsprechende Aktion, die in der Regel definiert wurde, auf die Anforderung angewendet. Nach der Verarbeitung einer derartigen Übereinstimmung werden Regeln mit niedrigerer Priorität nicht weiter verarbeitet.

Einer von Front Door bereitgestellten Webanwendung kann jeweils nur eine WAF-Richtlinie zugeordnet werden. Sie können jedoch eine Front Door-Konfiguration auch ohne eine zugehörige WAF-Richtlinie erstellen. Ist eine WAF-Richtlinie vorhanden, wird sie in allen unseren Edgestandorten repliziert, um bei den Sicherheitsrichtlinien auf der ganzen Welt Konsistenz zu gewährleisten.

## <a name="waf-modes"></a>WAF-Modi

Für die Ausführung der WAF-Richtlinie können die beiden folgenden Modi konfiguriert werden:

- **Erkennungsmodus:** Im Erkennungsmodus überwacht WAF nur die Anforderung und die entsprechende WAF-Regel und protokolliert die Ergebnisse im WAF-Protokoll. Es werden keine weiteren Aktionen ausgeführt. Sie können die Protokollierung von Diagnosedaten für Front Door aktivieren. Navigieren Sie bei Verwendung des Portals zum Abschnitt **Diagnose**.

- **Schutzmodus:** Im Schutzmodus führt WAF die angegebene Aktion aus, wenn eine Anforderung einer Regel entspricht. Nachdem eine Entsprechung gefunden wurde, werden keine weiteren Regeln mit niedrigerer Priorität mehr ausgewertet. Jede Anforderung mit einer Regelübereinstimmung wird außerdem in den WAF-Protokollen protokolliert.

## <a name="waf-actions"></a>WAF-Aktionen

Für WAF-Kunden stehen die folgenden Aktionen zur Auswahl, die beim Erfüllen einer Regelbedingung durch eine Anforderung ausgeführt werden:

- **Zulassen**:  Die Anforderung passiert die WAF und wird an das Back-End weitergeleitet. Diese Anforderung kann mit Regeln niedrigerer Priorität nicht mehr gesperrt werden.
- **BLOCK:** Die Anforderung wird gesperrt. WAF sendet eine Antwort an den Client, ohne die Anforderung an das Back-End weiterzuleiten.
- **LOG:**  Die Anforderung wird in den WAF-Protokollen protokolliert, und WAF setzt den Vorgang mit dem Auswerten von Regeln mit niedriger Priorität fort.
- **REDIRECT:** WAF leitet die Anforderung an den angegebenen URI weiter. Der URI wird als Einstellung auf Richtlinienebene angegeben. Nach der Konfiguration werden alle Anforderungen, die der Aktion **REDIRECT** entsprechen, an diesen URI gesendet.

## <a name="waf-rules"></a>WAF-Regeln

Eine WAF-Richtlinie kann aus zwei Arten von Sicherheitsregeln bestehen: benutzerdefinierte, vom Kunden erstellte Regeln und verwaltete Regelsätze (von Azure verwaltete, vorkonfigurierte Regelsätze).

### <a name="custom-authored-rules"></a>Benutzerdefinierte Regeln

Sie können benutzerdefinierte WAF-Regeln wie folgt konfigurieren:

- **Liste zugelassener und gesperrter IP-Adressen**: Sie können den Zugriff auf Ihre Webanwendungen auf der Grundlage einer Liste von Client-IP-Adressen (oder IP-Adressbereichen) steuern. Unterstützt werden sowohl IPv4- als auch IPv6-Adresstypen. Diese Liste können Sie so konfigurieren, dass Anforderungen, deren Quell-IP-Adresse mit einer IP-Adresse in der Liste übereinstimmt, entweder gesperrt oder zugelassen werden.

- **Geografiebasierte Zugriffssteuerung**: Sie können den Zugriff auf Ihre Webanwendungen auf der Grundlage des Ländercodes einer Client-IP-Adresse zu steuern.

- **Zugriffssteuerung auf Basis von HTTP-Parametern:** Sie können Zeichenfolgenübereinstimmungen in HTTP/HTTPS-Anforderungsparametern als Grundlage für Regeln verwenden.  Hierzu zählen beispielsweise Abfragezeichenfolgen, POST-Argumente, Anforderungs-URI, Anforderungsheader und Anforderungstext.

- **Zugriffssteuerung auf Basis der Anforderungsmethode**: Sie können Regeln verwenden, die auf der HTTP-Anforderungsmethode der Anforderung basieren. Beispiel wären etwa GET, PUT und HEAD.

- **Größenbeschränkung:** Sie können Regeln verwenden, die auf der Länge bestimmter Teile einer Anforderung (Abfragezeichenfolge, URI, Anforderungstext oder Ähnliches) basieren.

- **Ratenbegrenzung**: Mit einer Regel für die Ratenbegrenzung kann außergewöhnlich hoher Datenverkehr von einer beliebigen Client-IP-Adresse eingeschränkt werden. Sie können einen Schwellenwert für die Anzahl der Webanforderungen konfigurieren, die von einer Client-IP-Adresse während einer Minute zulässig sind. Diese Regel unterscheidet sich von der benutzerdefinierten Regel mit einer Liste zugelassener oder gesperrter IP-Adressen, die alle Anforderungen von einer Client-IP-Adresse entweder zulässt oder blockiert. Für eine präzise Ratensteuerung kann die Ratenbegrenzung mit weiteren Übereinstimmungsbedingungen (etwa mit HTTP(S)-Parametern) kombiniert werden.

### <a name="azure-managed-rule-sets"></a>Von Azure verwaltete Regelsätze

Von Azure verwaltete Regelsätze bieten eine einfache Möglichkeit zum Bereitstellen von Schutz vor allgemeinen Sicherheitsbedrohungen. Da Azure diese Regelsätze verwaltet, werden die Regeln zum Schutz vor neuen Angriffssignaturen aktualisiert, wenn dies erforderlich ist. Der von Azure verwaltete Standardregelsatz enthält Regeln für die folgenden Bedrohungskategorien:

- Cross-Site-Scripting
- Java-Angriffe
- Local File Inclusion
- PHP Code-Injection
- Remotebefehlsausführung
- Remote File Inclusion
- Session Fixation
- Schutz vor Einschleusung von SQL-Befehlen
- Protokollangreifer

Benutzerdefinierte Regeln werden immer vor den Regeln im Standardregelsatz ausgewertet. Wenn eine Anforderung einer benutzerdefinierten Regel entspricht, wird die entsprechende Regelaktion angewendet. Die Anforderung wird entweder gesperrt oder an das Back-End weitergeleitet. Es werden keine weiteren benutzerdefinierten Regeln oder Regeln im Standardregelsatz verarbeitet. Der Standardregelsatz kann auch aus WAF-Richtlinien entfernt werden.

Weitere Informationen finden Sie unter [DRS-Regelgruppen und -Regeln der Web Application Firewall](waf-front-door-drs.md).


### <a name="bot-protection-rule-set-preview"></a>Bot-Schutzregelsatz (Vorschauversion)

Sie können einen verwalteten Bot-Schutzregelsatz aktivieren, damit bei Anforderungen aus bekannten Bot-Kategorien benutzerdefinierte Aktionen ausgeführt werden. 

Es werden drei Bot-Kategorien unterstützt: „Ungültig“, „Gültig“ und „Unbekannt“. Bot-Signaturen werden von der WAF-Plattform verwaltet und dynamisch aktualisiert.

Zu ungültigen Bots zählen Bots mit schädlicher IP-Adresse sowie Bots mit gefälschter Identität. Schädliche IP-Adressen stammen aus dem Microsoft Threat Intelligence-Feed und werden stündlich aktualisiert. [Der Intelligente Sicherheitsgraph](https://www.microsoft.com/security/operations/intelligence) (Intelligent Security Graph) ist die Grundlage für Microsofts Bedrohungsanalyse und wird von mehreren Diensten genutzt, darunter Microsoft Defender für Cloud.

Zu den guten Bots gehören auch validierte Suchmaschinen. Zur Kategorie „Unbekannt“ zählen weitere Bot-Gruppen, die sich selbst als Bots identifiziert haben. Beispiele wären etwa Agents für die Marktanalyse, für das Abrufen von Feeds oder für die Datensammlung. 

Unbekannte Bots werden über veröffentlichte Benutzer-Agents ohne zusätzliche Überprüfung klassifiziert. Sie können benutzerdefinierte Aktionen festlegen, um Bots der unterschiedlichen Kategorien zu sperren, zuzulassen, zu protokollieren oder umzuleiten.

![Bot-Schutzregelsatz](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Der Bot-Schutzregelsatz befindet sich derzeit in der öffentlichen Vorschau und wird mit einer Vorschau-SLA (Vereinbarung zum Servicelevel) bereitgestellt. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.  Weitere Informationen finden Sie unter [Ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wenn der Bot-Schutz aktiviert ist, werden eingehende Anforderungen, die mit Bot-Regeln übereinstimmen, im Protokoll „FrontdoorWebApplicationFirewallLog“ protokolliert. Sie können auf WAF-Protokolle über ein Speicherkonto, über Event Hub oder über Log Analytics zugreifen.

## <a name="configuration"></a>Konfiguration

Alle Arten von WAF-Regeln können über das Azure-Portal, mithilfe von REST-APIs, unter Verwendung von Azure Resource Manager-Vorlagen und per Azure PowerShell konfiguriert und bereitgestellt werden.

## <a name="monitoring"></a>Überwachung

Die Überwachung für WAF für Front Door ist in Azure Monitor integriert und dient zum Nachverfolgen von Warnungen und zum einfachen Überwachen von Datenverkehrstrends.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Web Application Firewall für Azure Application Gateway](../ag/ag-overview.md)
