---
title: Logik für die Azure Firewall-Regelverarbeitung
description: Azure Firewall verfügt über NAT-Regeln, Netzwerkregeln und Anwendungsregeln. Die Regeln werden gemäß dem Regeltyp verarbeitet.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/09/2021
ms.author: victorh
ms.openlocfilehash: ec914df62c98ea7948c198949b34e37aa3c485d3
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137358"
---
# <a name="configure-azure-firewall-rules"></a>Konfigurieren von Azure Firewall-Regeln
Sie können NAT-Regeln, Netzwerkregeln und Anwendungsregeln in Azure Firewall entweder mithilfe klassischer Regeln oder der Firewallrichtlinie konfigurieren. Azure Firewall verweigert standardmäßig jeglichen Datenverkehr, bis Regeln zum Zulassen von Datenverkehr manuell konfiguriert werden.

## <a name="rule-processing-using-classic-rules"></a>Regelverarbeitung mithilfe klassischer Regeln

Regelsammlungen werden entsprechend dem Regeltyp in Prioritätsreihenfolge verarbeitet – niedrigere Zahlen bis höhere Zahlen von 100 bis 65.000. Der Name einer Regelsammlung darf nur Buchstaben, Ziffern, Unterstriche, Punkte oder Bindestriche enthalten. Er muss mit einem Buchstaben oder einer Zahl beginnen und mit einem Buchstaben, einer Zahl oder einem Unterstrich enden. Die maximale Namenslänge ist 80 Zeichen.

Es empfiehlt sich, die Prioritätsnummern der Regelsammlung zunächst in Inkrementen von 100 (100, 200, 300 usw.) aufzuteilen, damit Sie bei Bedarf Platz zum Hinzufügen weiterer Regelsammlungen haben.

## <a name="rule-processing-using-firewall-policy"></a>Regelverarbeitung mithilfe der Firewallrichtlinie

Mit der Firewallrichtlinie werden Regeln in Regelsammlungen und Regelsammlungsgruppen organisiert. Regelsammlungsgruppen enthalten null oder mehr Regelsammlungen. Regelsammlungen sind vom Typ NAT, Netzwerk oder Anwendungen. Sie können mehrere Regelsammlungstypen innerhalb einer einzelnen Regelgruppe definieren. Sie können null oder mehr Regeln in einer Regelsammlung definieren. Regeln in einer Regelsammlung müssen denselben Typ (NAT, Netzwerk oder Anwendung) aufweisen.    

Regeln werden basierend auf der Priorität der Regelsammlungsgruppe und der Priorität der Regelsammlung verarbeitet. Die Priorität ist eine beliebige Zahl zwischen 100 (höchste Priorität) und 65.000 (niedrigste Priorität). Regelsammlungsgruppen mit der höchsten Priorität werden zuerst verarbeitet. Innerhalb einer Regelsammlungsgruppe werden Regelsammlungen mit der höchsten Priorität (niedrigste Zahl) zuerst verarbeitet.  

Wenn eine Firewallrichtlinie von einer übergeordneten Richtlinie geerbt wird, haben Regelsammlungsgruppen in der übergeordneten Richtlinie immer Vorrang und zwar unabhängig von der Priorität einer untergeordneten Richtlinie.  

> [!NOTE]
> Anwendungsregeln werden immer nach Netzwerkregeln verarbeitet, die wiederum nach DNAT-Regeln verarbeitet werden, unabhängig von der Priorität der Regelsammlungsgruppe oder Regelsammlung und der Richtlinienvererbung.

Nachfolgend ist eine Beispielrichtlinie aufgeführt:


|Name  |type  |Priority  |Regeln  |Geerbt von
|---------|---------|---------|---------|-------|
|BaseRCG1      |Regelsammlungsgruppe           |200         |8         |Übergeordnete Richtlinie|
|DNATRc1     |DNAT-Regelsammlung         |  600       |   7      |Übergeordnete Richtlinie|
|NetworkRc1     |Netzwerkregelsammlung  | 800        |    1     |Übergeordnete Richtlinie|
|BaseRCG2  |Regelsammlungsgruppe         |300         | 3        |Übergeordnete Richtlinie|
|AppRCG2     |Anwendungsregelsammlung | 1200        |2         |Übergeordnete Richtlinie
|NetworkRC2     |Netzwerkregelsammlung         |1300         |    1     |Übergeordnete Richtlinie|
|ChildRCG1  | Regelsammlungsgruppe        | 300        |5         |-|
|ChAppRC1     |Anwendungsregelsammlung         |  700       | 3        |-|
|ChNetRC1       |   Netzwerkregelsammlung      |    900     |    2     |-|
|ChildRCG2      |Regelsammlungsgruppe         | 650        |    9     |-|
|ChNetRC2      |Netzwerkregelsammlung         |    1100     |  2       |-|
|ChAppRC2      |     Anwendungsregelsammlung    |2000         |7         |-|
|ChDNATRC3     | DNAT-Regelsammlung        | 3000        |  2       |-|

Die Regeln werden in der folgenden Reihenfolge verarbeitet: DNATRC1, DNATRC3, ChDNATRC3, NetworkRC1, NetworkRC2, ChNetRC1, ChNetRC2, AppRC2, ChAppRC1, ChAppRC2.

Weitere Informationen über Firewall-Richtlinien-Regelsätze finden Sie unter [Azure Firewall-Richtlinien-Regelsätze](policy-rule-sets.md).

### <a name="threat-intelligence"></a>Threat Intelligence

Wenn Sie das Threat Intelligence-gestützte Filtern aktivieren, haben diese Regeln die höchste Priorität und werden immer zuerst verarbeitet (vor Netzwerk- und Anwendungsregeln). Threat Intelligence-gestütztes Filtern kann den Datenverkehr ablehnen, bevor konfigurierte Regeln verarbeitet werden. Weitere Informationen finden Sie unter [Threat Intelligence-gestütztes Filtern für Azure Firewall](threat-intel.md).

### <a name="idps"></a>IDPS

Wenn IDPS im *Warnmodus* konfiguriert ist, arbeitet die IDPS-Engine parallel zur Regelverarbeitungslogik und generiert Warnungen bei übereinstimmenden Signaturen sowohl für eingehende als auch für ausgehende Datenflüsse.Bei einer IDPS-Signaturübereinstimmung wird eine Warnung in den Firewallprotokollen erfasst. Da die IDPS-Engine jedoch parallel zur Regelverarbeitungs-Engine arbeitet, kann Datenverkehr, der durch Anwendungs-/Netzwerkregeln verweigert/zugelassen wird, dennoch einen weiteren Protokolleintrag generieren. 

Wenn IDPS im *Warn- und Verweigerungsmodus* konfiguriert ist, ist die IDPS-Engine inline und wird nach der Regelverarbeitungs-Engine aktiviert. Daher generieren beide Engines Warnungen und können übereinstimmende Datenflüsse blockieren.  

Sitzungen, die von IDPS verworfen werden, blockieren automatisch den Datenfluss. Daher wird kein RST auf TCP-Ebene gesendet.Da IDPS den Datenverkehr immer überprüft, nachdem die Netzwerk-/Anwendungsregel abgeglichen (Zulassen/Verweigern) und in Protokollen markiert wurde, kann eine weitere *Verwerfen*-Nachricht protokolliert werden, wenn IDPS entscheidet, die Sitzung aufgrund einer Signaturübereinstimmung zu verweigern. 

Wenn die TLS-Überprüfung aktiviert ist, wird sowohl unverschlüsselter als auch verschlüsselter Datenverkehr überprüft.  

## <a name="outbound-connectivity"></a>Ausgehende Konnektivität

### <a name="network-rules-and-applications-rules"></a>Netzwerkregeln und Anwendungsregeln

Wenn Sie Netzwerkregeln und Anwendungsregeln konfigurieren, werden die Netzwerkregeln in der Prioritätsreihenfolge vor den Anwendungsregeln angewendet. Die Regeln können zur Beendigung von Vorgängen führen. Wenn also eine Übereinstimmung in einer Netzwerkregel gefunden wird, werden keine anderen Regeln mehr verarbeitet. Wenn IDPS konfiguriert ist, wird der gesamte übermittelte Datenverkehr überprüft, und bei einer Signaturübereinstimmung kann IDPS eine Warnung ausgeben und/oder verdächtigen Datenverkehr blockieren.  

Wenn sich keine Übereinstimmung für eine Netzwerkregel ergibt und als Protokoll HTTP, HTTPS oder MSSQL verwendet wird, wird das Paket von den Anwendungsregeln in der Reihenfolge ihrer Priorität ausgewertet.  

Bei HTTP sucht Azure Firewall anhand des Hostheaders nach einer Übereinstimmung mit einer Anwendungsregel. Bei HTTPS sucht Azure Firewall nur anhand von SNI nach einer Übereinstimmung mit einer Anwendungsregel.  

Sowohl bei HTTP als auch bei TLS-überprüftem HTTPS ignoriert die Firewall die Ziel-IP-Adresse des Pakets und verwendet die vom DNS aufgelöste IP-Adresse aus dem Hostheader. Die Firewall erwartet die Portnummer im Hostheader, andernfalls wird der Standardport 80 angenommen. Wenn der tatsächliche TCP-Port nicht mit dem Port im Hostheader übereinstimmt, wird der Datenverkehr verworfen.Die DNS-Auflösung erfolgt durch Azure DNS oder durch ein benutzerdefiniertes DNS, wenn dies in der Firewall konfiguriert ist.  

> [!NOTE]
> Sowohl HTTP- als auch HTTPS-Protokolle (mit TLS-Überprüfung) werden von Azure Firewall immer mit XFF-Header (X-Forwarded-For) versehen, der der ursprünglichen Quell-IP-Adresse entspricht.  

Wenn eine Anwendungsregel eine TLS-Überprüfung enthält, werden SNI, Hostheader und auch die URL von der Firewallregel-Engine verarbeitet, um der Regel zu entsprechen. 

Falls sich innerhalb der Anwendungsregeln immer noch keine Übereinstimmung ergibt, wird das Paket anhand der Regelsammlung der Infrastruktur ausgewertet. Wenn sich auch hierbei keine Übereinstimmung ergibt, wird das Paket standardmäßig abgelehnt. 

> [!NOTE]
> Netzwerkregeln können für  *TCP*,  *UDP*,  *ICMP* oder ein beliebiges IP-Protokoll ( *Any*) konfiguriert werden. Letzteres bezieht sich auf alle im Dokument zu IANA-Protokollnummern (Internet Assigned Numbers Authority) definierten IP-Protokolle. Wenn ein Zielport explizit konfiguriert ist, wird die Regel in eine TCP- und UDP-Regel übersetzt. Vor dem 9. November 2020 bezog sich ein beliebiges IP-Protokoll ( *Any*) auf TCP, UDP oder ICMP. Daher haben Sie möglicherweise vor diesem Datum eine Regel mit **Protocol = Any** und **destination ports = '*'** konfiguriert. Wenn kein IP-Protokoll wie aktuell definiert zugelassen werden soll, ändern Sie die Regel so, dass die gewünschten Protokolle (TCP, UDP oder ICMP) explizit konfiguriert werden. 

## <a name="inbound-connectivity"></a>Eingehende Konnektivität

### <a name="dnat-rules-and-network-rules"></a>DNAT-Regeln und Netzwerkregeln

Eingehende Internetkonnektivität kann aktiviert werden, indem DNAT (Destination Network Address Translation) konfiguriert wird. Die Vorgehensweise wird unter [Tutorial: Filtern von eingehendem Datenverkehr per Azure Firewall-DNAT im Azure-Portal](tutorial-firewall-dnat.md) beschrieben. NAT-Regeln werden in der Priorität vor Netzwerkregeln angewendet. Wenn sich eine Übereinstimmung ergibt, wird eine implizite entsprechende Netzwerkregel hinzugefügt, um den übersetzten Datenverkehr zuzulassen. Aus Sicherheitsgründen besteht die empfohlene Vorgehensweise darin, eine bestimmte Internetquelle hinzuzufügen, um DNAT-Zugriff auf das Netzwerk zu gewähren und die Verwendung von Platzhaltern zu vermeiden.

Anwendungsregeln werden nicht für eingehende Verbindungen angewendet. Wenn Sie also eingehenden HTTP/S-Datenverkehr filtern möchten, sollten Sie Web Application Firewall (WAF) verwenden. Weitere Informationen finden Sie unter [Was ist die Azure Web Application Firewall?](../web-application-firewall/overview.md).

## <a name="examples"></a>Beispiele

Die folgenden Beispiele verdeutlichen die Ergebnisse einiger dieser Regelkombinationen.

### <a name="example-1"></a>Beispiel 1

Die Verbindung mit „google.com“ ist aufgrund einer übereinstimmenden Netzwerkregel zulässig.

**Netzwerkregel**

- Aktion: Allow


|name  |Protocol  |Quellentyp  |`Source`  |Zieltyp  |Zieladresse  |Zielports|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-web     |TCP|IP-Adresse|*|IP-Adresse|*|80, 443

**Anwendungsregel**

- Aktion: Verweigern

|name  |Quellentyp  |`Source`  |Protokoll:Port|Ziel-FQDNs|
|---------|---------|---------|---------|----------|----------|
|Deny-google     |IP-Adresse|*|http:80,https:443|google.com

**Ergebnis**

Die Verbindung mit „google.com“ ist zulässig, da das Paket mit der *Allow-web*-Netzwerkregel übereinstimmt. An dieser Stelle wird die Regelverarbeitung angehalten.

### <a name="example-2"></a>Beispiel 2

Der SSH-Datenverkehr wird abgelehnt, da er durch eine *Deny*-Netzwerkregelsammlung mit höherer Priorität blockiert wird.

**Netzwerkregelsammlung 1**

- Name: Allow-collection
- Priorität: 200
- Aktion: Allow

|name  |Protocol  |Quellentyp  |`Source`  |Zieltyp  |Zieladresse  |Zielports|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-SSH     |TCP|IP-Adresse|*|IP-Adresse|*|22

**Netzwerkregelsammlung 2**

- Name: Deny-collection
- Priorität: 100
- Aktion: Verweigern

|name  |Protocol  |Quellentyp  |`Source`  |Zieltyp  |Zieladresse  |Zielports|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|IP-Adresse|*|IP-Adresse|*|22

**Ergebnis**

SSH-Verbindungen werden abgelehnt, da sie durch eine Netzwerkregelsammlung mit höherer Priorität blockiert werden. An dieser Stelle wird die Regelverarbeitung angehalten.

## <a name="rule-changes"></a>Regeländerungen

Wenn Sie eine Regel ändern, um zuvor zugelassenen Datenverkehr abzulehnen, werden alle relevanten vorhandenen Sitzungen verworfen.

## <a name="3-way-handshake-behavior"></a>3-Wege-Handshake-Verhalten

Als zustandsbehafteter Dienst führt Azure Firewall einen TCP-3-Wege-Handshake für zulässigen Datenverkehr von einer Quelle zum Ziel durch.Beispiel: VNet-A zu VNet-B.

Das Erstellen einer Zulassungsregel von VNet-A zu VNet-B bedeutet nicht, dass neue initiierte Verbindungen von VNet-B zu VNet-A zugelassen werden.

Daher ist es nicht erforderlich, eine explizite Ablehnungsregel von VNet-B zu VNet-A zu erstellen. Wenn Sie diese Verweigerungsregel erstellen, unterbrechen Sie den 3-Wege-Handshake von der ursprünglichen Erlaubnisregel von VNet-A zu VNet-B. 

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie eine [Azure Firewall bereitstellen und konfigurieren](tutorial-firewall-deploy-portal.md).
