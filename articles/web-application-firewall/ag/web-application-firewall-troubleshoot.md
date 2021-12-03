---
title: Problembehandlung – Azure Web Application Firewall
description: Dieser Artikel enthält Informationen zur Problembehandlung für die Web Application Firewall (WAF) für Azure Application Gateway.
services: web-application-firewall
author: KumudD
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: kumud
ms.topic: conceptual
ms.openlocfilehash: 9db2e6a3c0ee90a81b4f6cb363c3513314c18944
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550986"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Problembehandlung für die Web Application Firewall (WAF) für Azure Application Gateway

Ihnen stehen einige Möglichkeiten zur Verfügung, wenn Anforderungen, die Ihre Web Application Firewall (WAF) durchlaufen sollten, blockiert werden.

Stellen Sie zunächst sicher, dass Sie die [WAF-Übersicht](ag-overview.md) und die Dokumente zur [WAF-Konfiguration](application-gateway-waf-configuration.md) gelesen haben. Vergewissern Sie sich außerdem, dass Sie die [WAF-Überwachung](../../application-gateway/application-gateway-diagnostics.md) aktiviert haben. In diesen Artikeln wird die Funktionsweise der WAF und der WAF-Regelsätze sowie der Zugriff auf WAF-Protokolle erläutert.

Die OWASP-Regelsätze sind so konzipiert, dass sie standardmäßig sehr streng sind und mithilfe von WAF an die spezifischen Anforderungen der Anwendung oder Organisation angepasst werden können. Es ist ganz normal und wird in vielen Fällen tatsächlich erwartet, Ausschlüsse und benutzerdefinierte Regeln zu erstellen und sogar Regeln zu deaktivieren, die möglicherweise Probleme oder falsch positive Ergebnisse verursachen. Richtlinien pro Site und pro URI ermöglichen, dass diese Änderungen sich nur auf bestimmte Sites/URIs auswirken, sodass sich keine Änderungen auch auf andere Sites auswirken müssen, auf denen möglicherweise nicht dieselben Probleme auftreten. 

## <a name="understanding-waf-logs"></a>Grundlegendes zu WAF-Protokollen

Der Zweck von WAF-Protokollen ist es, jede Anforderung anzuzeigen, die von der WAF abgeglichen oder blockiert wird. Es handelt sich um eine Auflistung aller ausgewerteten Anforderungen, die abgeglichen oder blockiert werden. Wenn Sie feststellen, dass die WAF eine Anforderung blockiert, bei der das nicht geschehen sollte (ein falsch positives Ergebnis), stehen Ihnen einige Möglichkeiten zur Verfügung. Nehmen Sie zunächst eine Eingrenzung vor, und suchen Sie nach der betreffenden Anforderung. Durchsuchen Sie die Protokolle, um den jeweiligen URI, den Zeitstempel oder die Transaktions-ID der Anforderung zu ermitteln. Wenn Sie die zugehörigen Protokolleinträge gefunden haben, können Sie mit Maßnahmen für die falsch positiven Ergebnisse beginnen.

Angenommen, Sie haben einen berechtigten Datenverkehr, der die Zeichenfolge *1=1* enthält und die WAF durchlaufen soll. Beim Ausführen der Anforderung blockiert die WAF Datenverkehr, der die Zeichenfolge *1=1* in einem Parameter oder Feld enthält. Diese Zeichenfolge steht häufig mit einem Angriff durch Einschleusung von SQL-Befehlen in Verbindung. Sie können die Protokolle durchsuchen und den Zeitstempel der Anforderung sowie die Regeln, die eine Blockierung/Übereinstimmung ergaben, ermitteln.

Im folgenden Beispiel sehen Sie, dass vier Regeln während der gleichen Anforderung ausgelöst werden (mithilfe des Felds „TransactionId“). Die erste Regel ergibt eine Übereinstimmung, weil der Benutzer eine numerische/IP-Adresse für die Anforderung verwendet hat. Dadurch erhöht sich die Anomaliebewertung um drei, da es sich um eine Warnung handelt. Die nächste Regel, die eine Übereinstimmung ergibt, ist 942130. Dies ist die Regel, nach der Sie suchen. Sie sehen *1=1* im Feld `details.data`. Dadurch wird die Anomaliebewertung nochmals um drei erhöht, da es sich ebenfalls um eine Warnung handelt. Im Allgemeinen erhöht jede Regel mit der Aktion **Matched** die Anomaliebewertung. An diesem Punkt weist die Anomaliebewertung den Wert sechs auf. Weitere Informationen finden Sie unter [Anomaliebewertungsmodus](ag-overview.md#anomaly-scoring-mode).

Die letzten beiden Protokolleinträge zeigen, dass die Anforderung blockiert wurde, da die Anomaliebewertung hoch genug war. Diese Einträge weisen eine andere Aktion als die anderen beiden auf. Sie zeigen an, dass sie die Anforderung tatsächlich *blockiert* haben. Diese Regeln sind obligatorisch und können nicht deaktiviert werden. Sie sollten nicht als Regeln, sondern eher als Kerninfrastruktur der internen WAF-Elemente angesehen werden.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Beheben falsch positiver Ergebnisse

Mit diesen Informationen und dem Wissen, dass Regel 942130 eine Übereinstimmung mit der Zeichenfolge *1=1* ergab, haben Sie einige Möglichkeiten, um zu verhindern, dass dadurch der Datenverkehr blockiert wird:

- Verwenden einer Ausschlussliste

   Weitere Informationen zu Ausschlusslisten finden Sie unter [WAF-Konfiguration](application-gateway-waf-configuration.md#waf-exclusion-lists).
- Deaktivieren der Regel

### <a name="using-an-exclusion-list"></a>Verwenden eine Ausschlussliste

Um eine fundierte Entscheidung zum Umgang mit einem falsch positiven Ergebnis zu treffen, müssen Sie sich mit den von Ihrer Anwendung verwendeten Technologien vertraut machen. Angenommen, es ist kein SQL-Server in Ihrem Technologiestapel vorhanden, und Sie erhalten falsch positive Ergebnisse im Zusammenhang mit diesen Regeln. Durch Deaktivieren dieser Regeln wird nicht unbedingt die Sicherheit geschwächt.

Ein Vorteil der Verwendung einer Ausschlussliste ist, dass nur ein bestimmter Teil einer Anforderung deaktiviert wird. Das bedeutet jedoch, dass ein bestimmter Ausschluss für den gesamten Datenverkehr gilt, der die WAF durchläuft, da es sich um eine globale Einstellung handelt. Dies könnte beispielsweise zu einem Problem führen, wenn *1=1* eine gültige Anforderung im Text für eine bestimmte App ist, dies aber bei anderen Apps nicht der Fall ist. Ein weiterer Vorteil ist, dass Sie zwischen dem Ausschluss von Text, Headern und Cookies wählen können, wenn eine bestimmte Bedingung erfüllt ist, statt die gesamte Anforderung auszuschließen.

Gelegentlich gibt es Fälle, in denen bestimmte Parameter auf eine möglicherweise nicht intuitive Weise an die WAF übergeben werden. Es gibt beispielsweise ein Token, das übergeben wird, wenn die Authentifizierung mithilfe von Azure Active Directory erfolgt. Dieses Token, *__RequestVerificationToken*, wird normalerweise als ein Anforderungscookie übergeben. In einigen Fällen jedoch, in denen Cookies deaktiviert sind, wird dieses Token auch als ein Anforderungsattribut oder Argument übergeben. In diesem Fall müssen Sie sicherstellen, dass *__RequestVerificationToken* auch als ein **Anforderungsattributname** zur Ausschlussliste hinzugefügt wird.

![Ausschlüsse](../media/web-application-firewall-troubleshoot/exclusion-list.png)

In diesem Beispiel möchten Sie den **Anforderungsattributnamen** ausschließen, der *text1* entspricht. Dies ist offensichtlich, da Sie den Attributnamen in den Firewallprotokollen sehen können: **data: Matched Data: 1=1 found within ARGS:text1: 1=1**. Das Attribut ist **text1**. Einige andere Möglichkeiten zum Auffinden dieses Attributnamens finden Sie unter [Suchen von Anforderungsattributnamen](#finding-request-attribute-names).

![WAF-Ausschlusslisten](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Deaktivieren von Regeln

Eine weitere Möglichkeit zum Umgehen eines falsch positiven Ergebnisses ist das Deaktivieren der Regel, die eine Übereinstimmung mit der Eingabe ergab, die von der WAF als schädlich angesehen wird. Da Sie die WAF-Protokolle analysiert und die Regel auf 942130 eingeschränkt haben, können Sie sie im Azure-Portal deaktivieren. Informationen finden Sie unter [Anpassen von Web Application Firewall-Regeln mit dem Azure-Portal](application-gateway-customize-waf-rules-portal.md).

Ein Vorteil der Deaktivierung einer Regel ist: Wenn Sie wissen, dass der gesamte Datenverkehr, der eine bestimmte Bedingung enthält, die normalerweise blockiert wird, gültiger Datenverkehr ist, können Sie diese Regel für die gesamte WAF deaktivieren. Falls es sich allerdings nur in einem bestimmten Anwendungsfall um gültigen Datenverkehr handelt, erzeugen Sie ein Sicherheitsrisiko, wenn Sie diese Regel für die gesamte WAF deaktivieren, da es sich um eine globale Einstellung handelt.

Wenn Sie Azure PowerShell verwenden möchten, finden Sie Informationen dazu unter [Anpassen von Web Application Firewall-Regeln über PowerShell](application-gateway-customize-waf-rules-powershell.md). Wenn Sie Azure CLI verwenden möchten, finden Sie Informationen dazu unter [Anpassen von Web Application Firewall-Regeln mit der Azure CLI](application-gateway-customize-waf-rules-cli.md).

![WAF-Regeln](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Suchen von Anforderungsattributnamen

Mithilfe von [Fiddler](https://www.telerik.com/fiddler) überprüfen Sie einzelne Anforderungen und bestimmen, welche bestimmten Felder einer Webseite aufgerufen werden. Dies kann helfen, bestimmte Felder mithilfe von Ausschlusslisten von der Überprüfung auszuschließen.

In diesem Beispiel können Sie sehen, dass das Feld, in dem die Zeichenfolge *1=1* eingegeben wurde, den Namen **text1** hat.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-1.png" alt-text="Screenshot des Progress Telerik Fiddler-Webdebuggers. Auf der Registerkarte „Raw“ wird „1 = 1“ hinter dem Namen „text1“ angezeigt." border="false":::

Dies ist ein Feld, das Sie ausschließen können. Weitere Informationen zu Ausschlusslisten finden Sie unter [WAF-Anforderungsgrößenlimits und Ausschlusslisten](application-gateway-waf-configuration.md#waf-exclusion-lists). Sie können die Auswertung in diesem Fall ausschließen, indem Sie den folgenden Ausschluss konfigurieren:

![WAF-Ausschluss](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Sie können auch die Firewallprotokolle überprüfen, um Informationen dazu zu erhalten, was Sie der Ausschlussliste hinzufügen müssen. Informationen zum Aktivieren der Protokollierung finden Sie unter [Back-End-Integrität, Ressourcenprotokolle und Metriken für Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

Überprüfen Sie das Firewallprotokoll, und sehen Sie sich die Datei „PT1H.json“ für die Stunde an, in der die zu untersuchende Anforderung aufgetreten ist.

In diesem Beispiel sehen Sie, dass vier Regeln mit derselben Transaktions-ID vorhanden sind und diese jeweils zum exakt gleichen Zeitpunkt auftraten:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Aufgrund Ihrer Kenntnisse über die Funktionsweise der CRS-Regelsätze und da Ihnen bekannt ist, dass der Regelsatz CRS 3.0 mit einem System für die Anomaliebewertung arbeitet (siehe [Web Application Firewall für Azure Application Gateway](ag-overview.md)), wissen Sie, dass die beiden unteren Regeln mit der Eigenschaft **action: Blocked** auf Grundlage der Gesamtbewertung der Anomalie blockiert werden. Die Regeln, auf die Sie sich konzentrieren müssen, sind die beiden ersten Regeln.

Der erste Eintrag wird protokolliert, da der Benutzer eine numerische IP-Adresse für das Navigieren zu Application Gateway verwendet hat, was in diesem Fall ignoriert werden kann.

Der zweite Eintrag (Regel 942130) ist der interessante. Wie Sie in den Details sehen können, stimmt er mit einem Muster (1=1) überein, und das Feld hat den Namen **text1**. Führen Sie dieselben Schritte wie zuvor aus, um den **Anforderungsattributnamen** **gleich** **1=1** auszuschließen.

## <a name="finding-request-header-names"></a>Suchen von Anforderungsheadernamen

Fiddler erweist sich auch beim Suchen nach Anforderungsheadernamen als ein nützliches Tool. Im folgenden Screenshot sehen Sie die Header für diese GET-Anforderung, darunter *Content-Type*, *User-Agent* usw.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-2.png" alt-text="Screenshot des Progress Telerik Fiddler-Webdebuggers. Auf der Registerkarte „Raw“ werden Details zum Anforderungsheader wie „Connection“, „Content-Type“ und „User-Agent“ angezeigt." border="false":::

Eine weitere Möglichkeit zum Anzeigen von Anforderungs- und Antwortheadern sind in den Entwicklertools von Chrome enthalten. Drücken Sie F12, oder klicken Sie mit der rechten Maustaste, und wählen Sie **Überprüfen** -> **Entwicklertools** und anschließend die Registerkarte **Netzwerk** aus. Laden Sie eine Webseite, und klicken Sie auf die Anforderung, die Sie untersuchen möchten.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Suchen nach Anforderungscookienamen

Wenn die Anforderung Cookies enthält, können Sie die Registerkarte **Cookies** auswählen, um sie in Fiddler anzuzeigen.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Beschränken globaler Parameter zur Vermeidung falsch positiver Ergebnisse

- Deaktivieren der Anforderungstextüberprüfung

   Durch Deaktivieren von **Anforderungstext überprüfen** werden die Anforderungstexte des gesamten Datenverkehrs nicht von der WAF ausgewertet. Dies kann nützlich sein, wenn Sie wissen, dass die Anforderungstexte für Ihre Anwendung nicht schädlich sind.

   Bei Deaktivierung dieser Option wird nur der Anforderungstext nicht überprüft. Die Header und Cookies werden weiterhin untersucht, sofern nicht einzelne davon mithilfe der Ausschlusslistenfunktion ausgeschlossen wurden.

- Dateigrößenbeschränkungen

   Durch Beschränken der Dateigröße für Ihre WAF schränken Sie die Möglichkeit eines Angriffs auf Ihre Webserver ein. Wenn das Hochladen großer Dateien zulässig ist, steigt das Risiko, dass Ihr Back-End überlastet wird. Das Beschränken der Dateigröße auf einen normalen Anwendungsfall für Ihre Anwendung ist lediglich eine weitere Möglichkeit, Angriffe zu verhindern.

   > [!NOTE]
   > Wenn Sie wissen, dass Ihre App niemals Dateiuploads oberhalb einer bestimmten Größe benötigt, können Sie dies durch Festlegen eines Grenzwerts einschränken.

## <a name="firewall-metrics-waf_v1-only"></a>Firewallmetriken (nur WAF_v1)

Für v1 Web Application Firewalls sind nun die folgenden Metriken im Portal verfügbar: 

1. Web Application Firewall – Anzahl blockierter Anforderungen: Die Anzahl der blockierten Anforderungen.
2. Web Application Firewall – Anzahl blockierter Regeln: Alle Regeln, die eine Übereinstimmung aufwiesen **und** für die die Anforderung blockiert wurde.
3. Web Application Firewall – Regelverteilung gesamt: Alle Regeln, die während der Auswertung eine Übereinstimmung aufgewiesen haben.
     
Um Metriken zu aktivieren, wählen Sie die Registerkarte **Metriken** im Portal aus, und wählen Sie dann eine der drei Metriken aus.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie [Konfigurieren der Web Application Firewall auf Application Gateway](tutorial-restrict-web-traffic-powershell.md).
