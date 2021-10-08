---
title: Erneutes Generieren von HTTP-Headern und URLs mit Azure Application Gateway
description: In diesem Artikel erhalten Sie eine Übersicht über das erneute Generieren von HTTP-Headern und einer URL in Azure Application Gateway.
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: c4e4af8fb14c48988a593261365dcfde6c7a0657
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577277"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Umschreiben von HTTP-Headern und einer URL mithilfe von Application Gateway

Mit Application Gateway können Sie den ausgewählten Inhalt von Anforderungen und Antworten umschreiben. Mit diesem Feature können Sie URLs übersetzen, Zeichenfolgenparameter abfragen und Anforderungs- und Antwortheader ändern. Außerdem können Sie Bedingungen hinzufügen, um sicherzustellen, dass die URL oder die angegebenen Header nur dann neu generiert werden, wenn bestimmte Bedingungen erfüllt sind. Diese Bedingungen basieren auf den Anforderungs- und Antwortinformationen.

>[!NOTE]
>Die Features zum Umschreiben von HTTP-Headern und einer URL sind nur für die [SKU Application Gateway v2](application-gateway-autoscaling-zone-redundant.md) verfügbar.

## <a name="rewrite-types-supported"></a>Unterstützte Typen für das Umschreiben

### <a name="request-and-response-headers"></a>Anforderungs- und Antwortheader

HTTP-Header ermöglichen einem Client und Server das Übergeben von zusätzlichen Informationen mit einer Anforderung oder Antwort. Durch das erneute Generieren dieser HTTP-Header können Sie verschiedene wichtige Aufgaben umsetzen, z.B. das Hinzufügen von sicherheitsbezogenen Headerfeldern wie HSTS/X-XSS-Protection, das Entfernen von Antwortheaderfeldern, über die sensible Informationen offengelegt werden können, und das Entfernen von Portinformationen aus X-Forwarded-For-Headern.

Application Gateway ermöglicht Ihnen das Hinzufügen, Entfernen oder Aktualisieren von HTTP-Anforderungs- und -Antwortheadern, während die Anforderung/Antwort-Pakete zwischen dem Client und den Back-End-Pools verschoben werden.

Informationen zum Umschreiben von Anforderungs- und Antwortheadern mit Application Gateway über das Azure-Portal finden Sie [hier](rewrite-url-portal.md).

![Abbildung](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Unterstützte Header**

Sie können alle Header in Anforderungen und Antworten mit Ausnahme von Host-, Verbindungs- und Upgradeheader umschreiben. Sie können auch über das Anwendungsgateway benutzerdefinierte Header erstellen und den Anforderungen und Antworten hinzufügen, die über das Gateway weitergeleitet werden.

### <a name="url-path-and-query-string"></a>URL-Pfad und Abfragezeichenfolge

Mit der Funktion zum Umschreiben einer URL in Application Gateway können Sie folgende Aktionen ausführen:

* Umschreiben von Hostnamen, Pfad und Abfragezeichenfolge der Anforderungs-URL 

* Wählen Sie aus, ob Sie die URLs aller Anforderungen eines Listeners oder nur die Anforderungen umschreiben möchten, die einer oder mehreren festgelegten Bedingungen entsprechen. Diese Bedingungen basieren auf den Anforderungs- und Antworteigenschaften (Anforderung, Header, Antwortheader und Servervariablen).

* Weiterleiten der Anforderung (Auswählen des Back-End-Pools) basierend auf der ursprünglichen URL oder der neu generierten URL

Informationen zum Umschreiben der URL mit Application Gateway über das Azure-Portal finden Sie [hier](rewrite-url-portal.md).

![Diagramm, das den Prozess zum Umschreiben einer URL mit Application Gateway beschreibt](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>Aktionen für das erneute Generieren

Mit Aktionen für das Umschreiben geben Sie die URL, Anforderungs- und Antwortheader an, die Sie umschreiben möchten, sowie den neu festzulegenden Wert. Der Wert einer URL oder eines neuen oder vorhandenen Headers kann auf die folgenden Typen von Werten festgelegt werden:

* Text
* Anforderungsheader. Um einen Anforderungsheader festzulegen, müssen Sie die Syntax {http_req_ *Headername*} verwenden.
* Antwortheader. Um einen Antwortheader festzulegen, müssen Sie die Syntax {http_resp_ *Headername*} verwenden.
* Servervariable. Um eine Servervariable festzulegen, müssen Sie die Syntax {var_ *serverVariable*} verwenden. Sehen Sie sich die Liste mit den unterstützten Servervariablen an.
* Eine Kombination aus Text, Anforderungsheader, Antwortheader und Servervariable. 

## <a name="rewrite-conditions"></a>Bedingungen für das Umschreiben

Sie können optional mit Bedingungen für das Umschreiben den Inhalt der HTTP(S)-Anforderungen und -Antworten auswerten, um nur dann eine Umschreibung auszuführen, wenn eine oder mehrere Bedingungen erfüllt sind. Das Anwendungsgateway wertet mit diesen Typen von Variablen den Inhalt von Anforderungen und Antworten aus:

* HTTP-Header in der Anforderung
* HTTP-Header in der Antwort
* Application Gateway-Servervariablen

Mit einer Bedingung können Sie ermitteln, ob eine angegebene Variable vorhanden ist, ob eine angegebene Variable mit einem bestimmten Wert übereinstimmt oder ob eine angegebene Variable einem bestimmten Muster entspricht. 


### <a name="pattern-matching"></a>Musterabgleich 

Application Gateway verwendet für den Musterabgleich in der Bedingung reguläre Ausdrücke. Mit der [PCRE-Bibliothek (Perl Compatible Regular Expressions)](https://www.pcre.org/) können Sie Muster für reguläre Ausdrücke einrichten, mit denen die Bedingungen übereinstimmen sollen. Weitere Informationen zur Syntax von regulären Ausdrücken finden Sie auf der [Perl-Mainpage für reguläre Ausdrücke](https://perldoc.perl.org/perlre.html).

### <a name="capturing"></a>Aufzeichnen

Um eine Teilzeichenfolge für die spätere Verwendung zu erfassen, schließen Sie das Teilmuster, das in der Definition der RegEx-Bedingung mit ihr übereinstimmt, in Klammern ein. Das erste Paar von Klammern speichert die Teilzeichenfolge im Register 1, das zweite Paar in 2 usw. Sie können beliebig viele Klammern verwenden. Perl definiert immer mehr nummerierte Variablen, mit denen Sie diese erfassten Zeichenfolgen darstellen können. Einige Beispiele aus der [Referenz](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm): 

*  /(\d)(\d)/ – entspricht zwei Ziffern, die in den Gruppen 1 und 2 erfasst werden 

* /(\d+)/ – entspricht einer oder mehrere Ziffern, die alle in Gruppe 1 erfasst werden 

* /(\d)+/ – entspricht einer Ziffer ein- oder mehrmals und erfasst die letzte Entsprechung in Gruppe 1

Nach der Erfassung können Sie im Aktionssatz mit dem folgenden Format darauf verweisen:

* Bei der Erfassung eines Anforderungsheaders müssen Sie {http_req_Headername_Gruppennummer} verwenden, z. B. {http_req_User-Agent_1} oder {http_req_User-Agent_2}
* Bei der Erfassung eines Antwortheaders müssen Sie {http_resp_Headername_Gruppennummer} verwenden, z. B. {http_resp_Location_1} oder {http_resp_Location_2}
* Für eine Servervariable müssen Sie {var_Servervariablenname_Gruppennummer} verwenden, z. B. {var_uri_path_1} oder {var_uri_path_2}

Wenn Sie den gesamten Wert verwenden möchten, sollten Sie die Nummer nicht angeben. Verwenden Sie einfach das Format {http_req_Headername} usw. ohne die Gruppennummer.

## <a name="server-variables"></a>Servervariablen

Application Gateway speichert mit Servervariablen nützliche Informationen zum Server, zur Verbindung mit dem Client und zur momentanen Anforderung an die Verbindung. Beispiele für gespeicherte Informationen sind die IP-Adresse und der Webbrowsertyp des Clients. Servervariablen ändern sich dynamisch, wenn z.B. eine neue Seite geladen oder ein Formular gesendet wird. Anhand dieser Variablen können Sie Bedingungen für das erneute Generieren und Header für das erneute Generieren auswerten. Um den Wert von Servervariablen zum Umschreiben von Headern zu verwenden, müssen Sie diese Variablen in der Syntax {var_ *Servervariablenname*} angeben.

Das Application Gateway unterstützt die folgenden Servervariablen:

|   Variablenname    |                   BESCHREIBUNG                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | Das Headerfeld „X-Forwarded-For“ der Clientanforderung mit der Variable `client_ip` (in dieser Tabelle unten erläutert), die im Format IP1, IP2, IP3 usw. angefügt ist. Ist das Feld „X-Forwarded-For“ im Clientanforderungsheader nicht vorhanden, ist die Variable `add_x_forwarded_for_proxy` gleich der Variablen `$client_ip`.   Diese Variable ist besonders hilfreich, wenn Sie den X-Forwarded-For-Header, der von Application Gateway festgelegt wurde, umschreiben möchten, sodass der Header nur die IP-Adresse und keine Portinformationen enthält. |
| ciphers_supported         | Eine Liste der Verschlüsselungen, die vom Client unterstützt werden.               |
| ciphers_used              | Die Verschlüsselungszeichenfolge, die für eine eingerichtete TLS-Verbindung verwendet wird. |
| client_ip                 | Die IP-Adresse des Clients, von dem das Anwendungsgateway die Anforderung empfangen hat. Befindet sich ein Reverseproxy vor dem Anwendungsgateway und dem ursprünglichen Client, gibt `client_ip` die IP-Adresse des Reverseproxys zurück. |
| client_port               | Der Port des Clients.                                             |
| client_tcp_rtt            | Informationen zur TCP-Verbindung des Clients. Verfügbar auf Systemen, die die Socketoption „TCP_INFO“ unterstützen. |
| client_user               | Wenn HTTP-Authentifizierung verwendet wird, der Benutzername, der bei der Authentifizierung angegeben wird. |
| host                      | In dieser Reihenfolge: Hostname aus der Anforderungszeile, Hostname aus dem Anforderungsheaderfeld „Host“ oder der Servername, der mit einer Anforderung übereinstimmt. Beispiel: In der Anforderung `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` lautet der Wert von „Host“ `contoso.com`. |
| cookie_ *Name*             | Das *name*-Cookie.                                           |
| http_method               | Die Methode, die für die URL-Anforderung verwendet wird. Beispielsweise GET oder POST. |
| http_status               | Der Sitzungsstatus. Beispielsweise 200, 400 oder 403.           |
| http_version              | Das Anforderungsprotokoll. In der Regel HTTP/1.0, HTTP/1.1 oder HTTP/2.0. |
| query_string              | Die Liste der Variablen/Wert-Paare nach dem „?“ in der angeforderten URL. Beispiel: In der Anforderung `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` lautet der Wert von „query_string“ `id=123&title=fabrikam`. |
| received_bytes            | Die Länge der Anforderung (einschließlich Anforderungszeile, Header und Anforderungstext). |
| request_query             | Die Argumente in der Anforderungszeile.                           |
| request_scheme            | Das Anforderungsschema: „http“ oder „https“.                           |
| request_uri               | Der vollständige ursprüngliche Anforderungs-URI (mit Argumenten). Beispiel: In der Anforderung `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` lautet der Wert von „request_uri“ `/article.aspx?id=123&title=fabrikam`. |
| sent_bytes                | Die Anzahl der an einen Client gesendeten Bytes.                        |
| server_port               | Der Port des Servers, der eine Anforderung akzeptiert hat.              |
| ssl_connection_protocol   | Das Protokoll einer hergestellten TLS-Verbindung.               |
| ssl_enabled               | „On“, wenn die Verbindung im TLS-Modus ausgeführt wird. Andernfalls eine leere Zeichenfolge. |
| uri_path                  | Identifiziert die spezifische Ressource auf dem Host, auf die der Webclient zugreifen möchte. Dies ist der Teil des Anforderungs-URI ohne die Argumente. Beispiel: In der Anforderung `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` lautet der Wert von „uri_path“ `/article.aspx`. |

### <a name="mutual-authentication-server-variables-preview"></a>Servervariablen für die gegenseitige Authentifizierung (Vorschau)

Application Gateway unterstützt die folgenden Servervariablen für Szenarien der gegenseitigen Authentifizierung. Verwenden Sie diese Servervariablen auf die gleiche Weise wie die oben beschriebenen anderen Servervariablen. 

|   Variablenname    |                   BESCHREIBUNG                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | Das Clientzertifikat im PEM-Format für eine hergestellte SSL-Verbindung. |
| client_certificate_end_date| Das Enddatum des Clientzertifikats. |
| client_certificate_fingerprint| Der SHA1-Fingerabdruck des Clientzertifikats für eine hergestellte SSL-Verbindung. |
| client_certificate_issuer | Die Zeichenfolge „Aussteller-DN“ des Clientzertifikats für eine hergestellte SSL-Verbindung. |
| client_certificate_serial | Die Seriennummer des Clientzertifikats für eine hergestellte SSL-Verbindung.  |
| client_certificate_start_date| Das Startdatum des Clientzertifikats. |
| client_certificate_subject| Die Zeichenfolge „Antragsteller-DN“ des Clientzertifikats für eine hergestellte SSL-Verbindung. |
| client_certificate_verification| Das Ergebnis der Überprüfung des Clientzertifikats: *SUCCESS*, *FAILED:\<reason\>* oder *NONE*, wenn kein Zertifikat vorhanden war. | 

## <a name="rewrite-configuration"></a>Konfiguration für das erneute Generieren

Zum Konfigurieren einer Umschreibungsregel müssen Sie einen Regelsatz für das Umschreiben erstellen und die Konfiguration für die Umschreibungsregel hinzufügen.

Ein Umschreibungsregelsatz enthält Folgendes:

* **Routingregelzuordnung für die Anforderung:** Die Konfiguration für das Umschreiben wird dem Quelllistener über die Routingregel zugeordnet. Bei Verwendung einer einfachen Routingregel wird die Konfiguration für das Umschreiben einem Quelllistener zugeordnet und fungiert als Umschreibung eines globalen Headers. Beim Verwendung einer pfadbasierten Routingregel wird die Konfiguration der Umschreibung in der URL-Pfadzuordnung definiert. In diesem Fall gilt sie nur für den bestimmten Pfadbereich einer Site. Sie können mehrere solche Sätze für das Umschreiben erstellen, und jeder dieser Sätze kann auf mehrere Listener angewandt werden. Allerdings können Sie auf einen bestimmten Listener nur einen Satz für das erneute Generieren anwenden.

* **Bedingung für das erneute Generieren**: Dies ist eine optionale Konfiguration. Bedingungen für das erneute Generieren werten den Inhalt von HTTP(S)-Anforderungen und -Antworten aus. Die Aktion für das erneute Generieren wird ausgeführt, wenn die HTTP(S)-Anforderung oder -Antwort die Bedingung für das erneute Generieren erfüllt. Wenn Sie der Aktion mehr als eine Bedingung zuordnen, erfolgt die Aktion nur, wenn alle Bedingungen erfüllt sind. Das heißt also, dass der Vorgang ein logischer UND-Vorgang ist.

* **Typ umschreiben:** Es stehen drei Arten von Umschreibungen zur Verfügung:
   * Umschreiben von Anforderungsheadern 
   * Umschreiben von Antwortheadern
   * Umschreiben von URL-Komponenten
      * **URL-Pfad:** Der Wert, in den der Pfad umgeschrieben werden soll. 
      * **URL-Abfragezeichenfolge:** Der Wert, in den die Abfragezeichenfolge umgeschrieben werden soll. 
      * **Pfadzuordnung neu auswerten:** Legt fest, ob die URL-Pfadzuordnung erneut ausgewertet werden soll. Wenn diese Option deaktiviert bleibt, wird der ursprüngliche URL-Pfad verwendet, um eine Übereinstimmung für das Pfadmuster in der URL-Pfadzuordnung zu ermitteln. Wenn diese Option auf TRUE festgelegt ist, wird die URL-Pfadzuordnung erneut ausgewertet, um die Übereinstimmung mit dem umgeschriebenen Pfad zu überprüfen. Das Aktivieren dieses Schalters hilft beim Routing der Anforderung an einen anderen Back-End-Pool nach dem Umschreiben.

## <a name="rewrite-configuration-common-pitfalls"></a>Häufige Probleme bei der Konfiguration der Umschreibung (Rewrite)

* Das Aktivieren von „Pfadzuordnung neu auswerten“ ist für grundlegende Anforderungsroutingregeln nicht zulässig. So wird eine Endlosschleife bei der Auswertung einer grundlegenden Routingregel verhindert.

* Es muss mindestens eine bedingte Umschreibungsregel oder eine Umschreibungsregel vorhanden sein, für die „Pfadzuordnung neu auswerten“ für pfadbasierte Routingregeln nicht aktiviert ist, um eine Endlosschleife bei der Auswertung einer pfadbasierten Routingregel zu verhindern.

* Eingehende Anforderungen werden mit Fehlercode 500 beendet, wenn eine Schleife dynamisch basierend auf Clienteingaben erstellt wird. In einem solchen Szenario setzt die Application Gateway-Instanz die Verarbeitung anderer Anforderungen ohne Beeinträchtigung fort.

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>Verwenden der Umschreibung von URLs (URL-Rewrite) oder Hostheadern mit Web Application Firewall (WAF_v2 SKU)

Wenn Sie URL-Rewrite oder das Umschreiben von Hostheadern konfigurieren, erfolgt die WAF-Auswertung nach der Änderung der Anforderungsheader- oder URL-Parameter (nach dem Umschreiben). Wenn Sie die Konfiguration von URL-Rewrite oder der Umschreibung von Hostheadern auf der Application Gateway-Instanz entfernen, erfolgt die WAF-Auswertung vor dem Umschreiben des Headers. Diese Reihenfolge stellt sicher, dass WAF-Regeln auf die endgültige Anforderung angewendet werden, die von Ihrem Back-End-Pool empfangen wird.

Angenommen, Sie verwenden die folgende Regel zur Headerumschreibung für den Header `"Accept" : "text/html"`: Wenn der Wert des Headers `"Accept"` mit `"text/html"` übereinstimmt, soll der Wert in `"image/png"` umgeschrieben werden.

Wenn nur das Umschreiben von Headern konfiguriert ist, erfolgt die WAF-Auswertung für `"Accept" : "text/html"`. Wenn Sie jedoch URL-Rewrite oder das Umschreiben von Hostheadern konfigurieren, wird die WAF-Auswertung für `"Accept" : "image/png"` durchgeführt.

>[!NOTE]
> URL-Rewrite-Vorgänge führen erwartungsgemäß zu einer geringfügigen Zunahme der CPU-Auslastung Ihrer WAF Application Gateway-Instanz. Es wird empfohlen, die [Metrik für die CPU-Auslastung](high-traffic-support.md) für einen kurzen Zeitraum zu überwachen, nachdem Sie die URL-Rewrite-Regeln für die WAF Application Gateway-Instanz aktiviert haben.

### <a name="common-scenarios-for-header-rewrite"></a>Häufige Szenarien für die Headerumschreibungen

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Entfernen von Portinformationen aus dem X-Forwarded-For-Header

Application Gateway fügt in alle Anforderungen einen X-Forwarded-For-Header ein, bevor es die Anforderungen an das Back-End weiterleitet. Dieser Header ist eine durch Trennzeichen getrennte Liste von IP-Ports. Es gibt möglicherweise Szenarien, in denen Back-End-Server nur die Header benötigen, um IP-Adressen zu enthalten. Sie können mit dem erneuten Generieren von Headern die Portinformationen aus dem X-Forwarded-For-Header entfernen. Dies kann beispielsweise erreicht werden, indem der Header auf die Servervariable „add_x_forwarded_for_proxy“ festgelegt wird. Alternativ können Sie auch die Variable „client_ip“ verwenden:

![Entfernen des Ports](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Ändern einer Umleitungs-URL

Wenn eine Back-End-Anwendung eine Umleitungsantwort sendet, empfiehlt es sich u.U., den Client an eine andere als die von der Back-End-Anwendung angegebene URL umzuleiten. Beispielsweise sollten Sie dies tun, wenn ein App-Dienst hinter einem Anwendungsgateway gehostet wird und erfordert, dass der Client eine Umleitung zu seinem relativen Pfad durchführt. (Beispielsweise eine Umleitung von „contoso.azurewebsites.net/path1“ zu „contoso.azurewebsites.net/path2“.)

Da App Service ein mehrinstanzenfähiger Dienst ist, nutzt er den Hostheader in der Anforderung zur Weiterleitung der Anforderung an den richtigen Endpunkt. App-Dienste haben den Standarddomänennamen „\*.azurewebsites.net“ (z. B. „contoso.azurewebsites.net“), der sich vom Domänennamen des Anwendungsgateways (z. B. „contoso.com“) unterscheidet. Da die ursprüngliche Anforderung vom Client den Domänennamen des Anwendungsgateways „contoso.com“ als Hostnamen aufweist, ändert das Anwendungsgateway nun den Hostnamen in „contoso.azurewebsites.net“. Diese Änderung wird vorgenommen, damit der App-Dienst die Anforderung an den richtigen Endpunkt weiterleiten kann.

Wenn der App Service eine Umleitungsantwort sendet, verwendet er den gleichen Hostnamen im Adressheader seiner Antwort wie in der Anforderung, die er vom Anwendungsgateway empfängt. So sendet der Client die Anforderung direkt an `contoso.azurewebsites.net/path2` und durchläuft nicht das Anwendungsgateway (`contoso.com/path2`). Das Umgehen des Anwendungsgateways ist nicht wünschenswert.

Sie können dieses Problem durch Festlegen des Hostnamens im Adressheader des Anwendungsgateway-Domänennamens lösen.

Hier sind die Schritte zum Ersetzen des Hostnamens:

1. Erstellen Sie eine Regel zum erneuten Generieren mit einer Bedingung, die prüft, ob der Adressheader in der Antwort „azurewebsites.net“ enthält. Geben Sie das Muster `(https?):\/\/.*azurewebsites\.net(.*)$` ein.
2. Führen Sie eine Aktion zum erneuten Generieren des Adressheaders durch, damit sie den Hostnamen des Anwendungsgateways hat. Geben Sie hierzu `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` als Headerwert ein. Alternativ können Sie auch die Servervariable `host` verwenden, um den Hostnamen entsprechend der ursprünglichen Anforderung festzulegen.

![Ändern des Adressheaders](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementieren von HTTP-Sicherheitsheadern, um Sicherheitsrisiken zu verhindern

Sie können verschiedene Sicherheitsrisiken beheben, indem Sie die erforderlichen Header in die Anwendungsantwort implementieren. Zu diesen Sicherheitsheadern zählen X-XSS-Protection, Strict-Transport-Security und Content-Security-Policy. Über Application Gateway können Sie diese Header für alle Antworten festlegen.

![Sicherheitsheader](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Löschen von unerwünschten Headern

Möglicherweise möchten Sie Header entfernen, die vertrauliche Informationen aus einer HTTP-Antwort anzeigen. Beispielsweise möchten Sie möglicherweise Informationen wie den Namen des Back-End-Servers, Betriebssystem oder Bibliothek entfernen. Sie können diese Header mit dem Anwendungsgateway entfernen:

![Löschen des Headers](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Überprüfen des Vorhandenseins eines Headers

Sie können einen HTTP-Anforderungs- oder -Antwortheader auf das Vorhandensein einer Header- oder Servervariablen untersuchen. Diese Untersuchung ist hilfreich, wenn Sie nur dann erneut Header generieren möchten, wenn der betreffende Header auch vorhanden ist.

![Überprüfen des Vorhandenseins eines Headers](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Häufige Szenarien für die URL-Umschreibungen

#### <a name="parameter-based-path-selection"></a>Parameterbasierte Pfadauswahl

Für Szenarien, in denen Sie den Back-End-Pool basierend auf dem Wert eines Headers, auf einem Teil der URL oder auf der Abfragezeichenfolge in der Anforderung auswählen möchten, können Sie eine Kombination aus URL-Umschreibungsfunktion und pfadbasiertem Routing verwenden. Wenn Sie z. B. über eine Verkaufswebsite verfügen, auf der die Produktkategorie als Abfragezeichenfolge in der URL übergeben wird, und Sie die Anforderung basierend auf der Abfragezeichenfolge an das Back-End weiterleiten möchten, gehen Sie wie folgt vor:

**Step1:**  Erstellen Sie eine Pfadzuordnung, wie in der folgenden Abbildung dargestellt:

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="URL-Umschreibungsszenario 1-1":::

**Schritt 2 (a):** Erstellen Sie einen Umschreibungssatz mit drei Umschreibungsregeln: 

* Die erste Regel verfügt über eine Bedingung, die die Variable *query_string* auf *category=shoes* überprüft, und eine Aktion, die den URL-Pfad in /*listing1* umschreibt. Außerdem ist **Pfadzuordnung neu auswerten** aktiviert.

* Die zweite Regel überprüft als Bedingung die Variable *query_string* auf *category=bags* und führt eine Aktion aus, die den URL-Pfad in /*listing2* umschreibt. **Pfadzuordnung neu auswerten** ist aktiviert.

* Die dritte Regel verfügt über eine Bedingung, die die Variable *query_string* auf *category=accessories* überprüft, und enthält eine Aktion, die den URL-Pfad in /*listing3* umschreibt. Erneut ist **Pfadzuordnung neu auswerten** aktiviert.

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="URL-Umschreibungsszenario 1-2":::

 

**Schritt 2 (b):** Weisen Sie diesen Umschreibungssatz dem Standardpfad der obigen pfadbasierten Regel zu.

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="URL-Umschreibungsszenario 1-3":::

Wenn der Benutzer nun *contoso.com/listing?category=any* anfordert, erhält er den Standardpfad, da keines der Pfadmuster in der Pfadzuordnung (/listing1, /listing2, /listing3) übereinstimmt. Da Sie diesem Pfad den obigen Umschreibungssatz zugeordnet haben, wird dieser Umschreibungssatz ausgewertet. Da die Abfragezeichenfolge keiner der Bedingungen der drei Umschreibungsregeln in diesem Umschreibungssatz entspricht, wird keine Umschreibungsaktion durchgeführt. Die Anforderung wird also unverändert an das Back-End weitergeleitet, das dem Standardpfad zugeordnet ist (in diesem Fall *GenericList*).

Wenn der Benutzer *contoso.com/listing?category=shoes* anfordert, wird wieder der Standardpfad verwendet. In diesem Fall entspricht die Bedingung jedoch der ersten Regel. Aus diesem Grund wird die der Bedingung zugeordnete Aktion ausgeführt, die den URL-Pfad in /*listing1* umschreibt und die Pfadzuordnung neu auswertet. Nachdem die Pfadzuordnung neu ausgewertet wurde, entspricht die Anforderung nun dem Pfad, der dem Muster */listing1* zugeordnet ist, und die Anforderung wird an das Back-End weitergeleitet, das mit diesem Muster verknüpft ist. In diesem Fall ist das „ShoesListBackendPool“.

>[!NOTE]
>Dieses Szenario kann auf beliebige Header- oder Cookiewerte, URL-Pfade, Abfragezeichenfolgen oder Servervariablen ausgeweitet werden. Die Grundlage bilden dabei immer die von Ihnen definierten Bedingungen, sodass Sie Anforderungen basierend auf diesen Bedingungen weiterleiten können.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Umschreiben von Abfragezeichenfolgenparametern basierend auf der URL

Gehen Sie vom Szenario einer Verkaufswebsite aus, auf der der für Benutzer sichtbare Link einfach und lesbar sein sollte, während hingegen der Back-End-Server die Abfragezeichenfolgenparameter benötigt, um den richtigen Inhalt anzuzeigen.

In diesem Fall kann Application Gateway Parameter aus der URL erfassen und der Abfragezeichenfolge Schlüssel-Wert-Paare aus der URL hinzufügen. Nehmen Sie beispielsweise an, der Benutzer möchte `https://www.contoso.com/fashion/shirts` in `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` umschreiben. Dies kann durch die folgende URL-Umschreibungskonfiguration erreicht werden.

**Bedingung:** Servervariable `uri_path` entspricht dem Muster `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="URL-Umschreibungsszenario 2-1":::

**Aktion:** Festlegen des URL-Pfads auf `buy.aspx` und der Abfragezeichenfolge auf `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="URL-Umschreibungsszenario 2-2":::

Eine Schrittanleitung für das oben beschriebene Szenario finden Sie unter [Erneutes Generieren einer URL mit Azure Application Gateway: Azure-Portal](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>Vergleich von URL-Umschreibung und URL-Umleitung

Bei einer URL-Umschreibung wird die URL von Application Gateway umgeschrieben, bevor die Anforderung an das Back-End gesendet wird. Dadurch wird nicht geändert, was Benutzern im Browser angezeigt wird, da die Änderungen für den Benutzer nicht sichtbar erfolgen.

Bei einer URL-Umleitung sendet Application Gateway eine Umleitungsantwort mit der neuen URL an den Client. Dazu muss der Client wiederum seine Anforderung erneut an die neue in der Umleitung bereitgestellte URL senden. Die URL, die der Benutzer im Browser sieht, wird durch die neue URL ersetzt.

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Umschreibung und Umleitung":::

## <a name="limitations"></a>Einschränkungen

- Wenn eine Antwort über mehrere Header gleichen Namens verfügt, führt das erneute Generieren des Werts eines dieser Header zum Löschen der anderen Header in der Antwort. Dies kann in der Regel bei Set-Cookie-Headern auftreten, da eine Antwort mehrere Set-Cookie-Header enthalten kann. Ein solches Szenario liegt vor, wenn Sie einen App-Dienst mit einem Anwendungsgateway verwenden und cookiebasierte Sitzungsaffinität auf dem Anwendungsgateway konfiguriert haben. In diesem Fall enthält die Antwort zwei Set-Cookie-Header: einen vom App-Dienst verwendeten (z. B. `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net`) und einen anderen für die Anwendungsgatewayaffinität (z. B. `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`). Das Umschreiben einer der Set-Cookie-Header in diesem Szenario kann dazu führen, dass der andere Set-Cookie-Header aus der Antwort entfernt wird.
- Das erneute Generieren wird nicht unterstützt, wenn das Anwendungsgateway so konfiguriert ist, dass die Anforderungen umgeleitet werden oder eine benutzerdefinierte Fehlerseite angezeigt wird.
- Wie in [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27) festgelegt, können Headernamen sämtliche alphanumerischen Zeichen und Sonderzeichen beinhalten. Derzeit wird das Sonderzeichen „Unterstrich“ (\_) in Headernamen nicht unterstützt.
- Verbindungs- und Upgradeheader können nicht umgeschrieben werden.

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über das Umschreiben von HTTP-Headern mit Application Gateway über das Azure-Portal.](rewrite-http-headers-portal.md)
- [Erfahren Sie mehr über das Umschreiben von URLs mit Application Gateway über das Azure-Portal.](rewrite-url-portal.md)
