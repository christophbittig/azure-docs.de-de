---
title: Überwachen mit mehrstufigen Webtests – Azure Application Insights
description: Einrichten von mehrstufigen Webtests zur Überwachung Ihrer Webanwendungen mit Azure Application Insights
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: 6e0cbbc772b9eb2f3ad245fcb9728b047d2d7126
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993224"
---
# <a name="multi-step-web-tests"></a>Webtests mit mehreren Schritten

Sie können eine aufgezeichnete Sequenz von URLs und Interaktionen mithilfe einer Website über mehrstufige Webtests überwachen. In diesem Artikel wird der Prozess ausführlich beschrieben, mit dem Sie mit Visual Studio Enterprise einen mehrstufigen Webtest erstellen können.

> [!IMPORTANT]
> [Mehrstufige Webtests wurden eingestellt](https://azure.microsoft.com/updates/retirement-notice-transition-to-custom-availability-tests-in-application-insights/). Es empfiehlt sich, [TrackAvailability](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)  zu verwenden, um anstelle von mehrstufigen Webtests  [benutzerdefinierte Verfügbarkeitstests](availability-azure-functions.md)  zu übermitteln. Mit TrackAvailability() und benutzerdefinierten Verfügbarkeitstests können Sie Tests auf beliebigen Computeressourcen ausführen und mithilfe von C# ganz einfach neue Tests erstellen.

> [!NOTE]
> Mehrstufige Webtests werden in der [Azure Government](../../azure-government/index.yml)-Cloud **nicht unterstützt**.


Mehrstufige Webtests werden als klassische Tests kategorisiert und befinden sich im Bereich „Verfügbarkeit“ unter **Klassischen Test hinzufügen**.

## <a name="multi-step-webtest-alternative"></a>Alternative für mehrstufige Webtests

Mehrstufige Webtests sind von Visual Studio-Webtestdateien abhängig. Es wurde [angekündigt](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/), dass Visual Studio 2019 die letzte Version mit Webtestfunktionen sein wird. Hiermit wird ausdrücklich darauf hingewiesen, dass zwar keine neuen Features hinzugefügt werden, aber die Webtestfunktionen in Visual Studio 2019 während des gesamten Supportlebenszyklus des Produkts weiterhin unterstützt werden. 

Es empfiehlt sich, [TrackAvailability](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) zu verwenden, um anstelle von mehrstufigen Webtests [benutzerdefinierte Verfügbarkeitstests](./availability-azure-functions.md) zu übermitteln. Dies ist die langfristig unterstützte Lösung für Testszenarien mit mehreren Anforderungen oder Authentifizierungen. Mit TrackAvailability() und benutzerdefinierten Verfügbarkeitstests können Sie Tests auf beliebigen Computeressourcen ausführen und mithilfe von C# ganz einfach neue Tests erstellen.

## <a name="pre-requisites"></a>Voraussetzungen

* Visual Studio 2017 Enterprise oder höher
* Visual Studio-Tools für Webleistungs- und Auslastungstests

Sie finden die Testingtools im **Visual Studio-Installer** > **Einzelne Komponenten** > **Debuggen und Testen** > **Tools für Webleistung und Auslastungstests**.

![Screenshot: Visual Studio-Installer mit aktivierter Option „Individuelle Komponenten“ und einem Kontrollkästchen neben dem Element „Tools für Webleistung und Auslastungstests“](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Für mehrstufige Webtests fallen zusätzliche Kosten an. Weitere Informationen erhalten Sie im [offiziellen Leitfaden für Preise](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Aufzeichnung eines mehrstufigen Webtests 

> [!WARNING]
> Die Verwendung der mehrstufigen Aufzeichnung wird nicht mehr empfohlen. Die Aufzeichnung wurde für statische HTML-Seiten mit grundlegenden Interaktionen entwickelt und bietet keine Funktionalität für moderne Webseiten.

Anleitungen zum Erstellen von Visual Studio-Webtests finden Sie in der [offiziellen Dokumentation zu Visual Studio 2019](/visualstudio/test/how-to-create-a-web-service-test).

## <a name="upload-the-web-test"></a>Hochladen des Webtests

1. Wählen Sie im Application Insights-Portal im Bereich „Verfügbarkeit“ die Option **Klassischen Test hinzufügen** aus, und wählen Sie dann **Mehrstufig** als *SKU* aus.
2. Laden Sie Ihren mehrstufigen Webtest hoch.
3. Legen Sie die Teststandorte, Häufigkeit und Warnungsparameter fest.
4. Klicken Sie auf **Erstellen**.

### <a name="frequency--location"></a>Häufigkeit und Standort

|Einstellung| Erklärung
|----|----|----|
|**Testhäufigkeit**| Legt fest, wie oft der Test von jedem Teststandort aus ausgeführt wird. Mit einer Standardfrequenz von fünf Minuten und fünf Teststandorten wird Ihre Website im Durchschnitt jede Minute getestet.|
|**Teststandorte**| Die Orte, von denen aus unsere Server Webanforderungen an Ihre URL senden. **Es wird empfohlen, mindestens fünf Teststandorte festzulegen**, um sicherzustellen, dass Sie Probleme mit Ihrer Website von Netzwerkproblemen unterscheiden können. Sie können bis zu 16 Standorte auswählen.

### <a name="success-criteria"></a>Erfolgskriterien

|Einstellung| Erklärung
|----|----|----|
| **Testtimeout** |Reduzieren Sie diesen Wert, um über langsame Antworten benachrichtigt zu werden. Der Test wird als ein Fehler gezählt, wenn die Antworten von Ihrer Website nicht innerhalb dieses Zeitraums empfangen wurden. Bei Auswahl von **Abhängige Anforderungen analysieren** müssen alle Bilder, Styledateien, Skripts und anderen abhängigen Ressourcen innerhalb dieses Zeitraums empfangen werden.|
| **HTTP-Antwort** | Der zurückgegebene Statuscode, der als Erfolg gezählt wird. 200 ist der Code, der angibt, dass eine normale Webseite zurückgegeben wurde.|
| **Inhaltsübereinstimmung** | Eine Zeichenfolge, z. B. „Willkommen!“ Wir vergewissern uns, dass in jeder Antwort eine exakte Übereinstimmung unter Berücksichtigung der Groß-und Kleinschreibung vorkommt. Dies muss eine Zeichenfolge in Klartext, ohne Platzhalter sein. Vergessen Sie nicht, diese zu aktualisieren, wenn sich der Seiteninhalt ändert. **Inhaltsübereinstimmungen werden nur für englische Zeichen unterstützt.** |

### <a name="alerts"></a>Alerts

|Einstellung| Erklärung
|----|----|----|
|**Near-realtime (Preview)** (Nahezu in Echtzeit (Vorschauversion)) | Es wird empfohlen, Warnungen zu verwenden, die nahezu in Echtzeit angezeigt werden. Diese Art von Warnung ist bereits vollständig konfiguriert, sobald Sie einen Verfügbarkeitstest erstellt haben.  |
|**Schwellenwert für den Warnungsspeicherort**|Es wird ein Mindestwert von 3/5 Standorten empfohlen. Das optimale Verhältnis zwischen dem Schwellenwert für den Warnungsspeicherort und der Anzahl von Teststandorten lautet **Warnungsschwellenwert für Standort** = **Anzahl von Teststandorten -2, bei einer Mindestanzahl von fünf Teststandorten.**|

## <a name="configuration"></a>Konfiguration

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Einfügen von Zeiten und beliebigen Zahlen in einen Test

Angenommen, Sie testen ein Tool, das zeitabhängige Daten, wie z. B. Bestände, aus einem externen Feed abruft. Beim Aufzeichnen eines Webtests müssen Sie bestimmte Zeiten verwenden, aber als Parameter des Tests – StartTime und EndTime – festlegen.

![Screenshot der Stock-App](./media/availability-multistep/app-insights-72webtest-parameters.png)

Wenn Sie den Test ausführen, sollte "EndTime" immer die aktuelle Uhrzeit widerspiegeln, und "StartTime" sollte der Uhrzeit vor 15 Minuten entsprechen.

Mithilfe des Datums-/Uhrzeit-Plug-Ins für Webtests können Zeiten parametrisiert werden.

1. Fügen Sie ein Webtest-Plug-In für jeden gewünschten Variablenparameterwert hinzu. Wählen Sie in der Webtest-Symbolleiste **Webtest-Plug-In hinzufügen** aus.
    
    ![Hinzufügen eines Plug-Ins für Webtests](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    In diesem Beispiel verwenden wir zwei Instanzen des Datums-/Uhrzeit-Plug-Ins. Eine Instanz gilt für „vor 15 Minuten“ und eine weitere für „jetzt“.

2. Öffnen Sie die Eigenschaften der einzelnen Plug-Ins. Geben Sie einen Namen ein, und legen Sie es so fest, dass die aktuelle Uhrzeit verwendet wird. Legen Sie für eines der Plug-Ins "Add Minutes = -15" fest.

    ![Kontextparameter](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Verwenden Sie im Webtestparameter {{Plug-In-Name}} zum Verweisen auf einen Plug-In-Namen.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Laden Sie nun den Test in das Portal hoch. Bei jeder Ausführung des Tests werden die dynamischen Werte verwendet.

### <a name="dealing-with-sign-in"></a>Umgang mit der Anmeldung

Wenn sich Benutzer bei Ihrer App anmelden, stehen Ihnen verschiedene Optionen für die Anmeldungssimulation zur Verfügung, damit Sie Seiten testen können, die auf die Anmeldung folgen. Der verwendete Ansatz hängt vom Typ der von der App bereitgestellten Sicherheit ab.

In allen Fällen sollten Sie in der Anwendung ein Konto erstellen, das nur Testzwecken dient. Schränken Sie die Berechtigungen dieses Testkontos nach Möglichkeit ein, damit ausgeschlossen ist, dass sich die Webtests auf echte Benutzer auswirken.

**Einfacher Benutzername und Kennwort** Zeichnen Sie wie gewöhnlich einen Webtest auf. Löschen Sie zuerst Cookies.

**SAML-Authentifizierung**

|Eigenschaftenname| BESCHREIBUNG|
|----|-----|
| Benutzergruppen-URI | Der Benutzergruppen-URI des SAML-Tokens.  Dies ist der URI für Access Control Service (ACS), einschließlich ACS-Namespace und -Hostname. |
| Zertifikatkennwort | Das Kennwort für das Clientzertifikat, das Zugriff auf den eingebetteten privaten Schlüssel gewährt. |
| Clientzertifikat  | Der Wert des Clientzertifikatss mit privatem Schlüssel im Base64-codierten Format. |
| Namensbezeichner | Der Namensbezeichner für das Token |
| Nicht nach | Die Zeitspanne, in der das Token gültig ist.  Die Standardeinstellung ist 5 Minuten. |
| Nicht vor | Die Zeitspanne, in der ein in der Vergangenheit erstelltes Token gültig ist (zur Berücksichtigung von Zeitabweichungen).  Der Standardwert ist (minus) 5 Minuten. |
| Parametername des Zielkontexts | Der Kontextparameter, der die generierte Assertion empfängt. |


**Geheimer Clientschlüssel** Verwenden Sie diese Route, wenn die Anmelderoute Ihrer App einen geheimen Clientschlüssel umfasst. Azure Active Directory (AAD) ist ein Beispiel für einen Dienst, bei dem eine Anmeldung mit geheimem Clientschlüssel bereitgestellt wird. In AAD ist der geheime Clientschlüssel der App-Schlüssel.

Hier ist ein Beispiel für einen Webtest einer Azure-Web-App mit App-Schlüssel angegeben:

![Beispielscreenshot](./media/availability-multistep/client-secret.png)

Rufen Sie das Token aus AAD mit dem geheimen Clientschlüssel (AppKey) ab.
Extrahieren Sie das Bearertoken aus der Antwort.
Rufen Sie die API mit dem Bearertoken im Autorisierungsheader auf.
Stellen Sie sicher, dass es sich bei dem Webtest um einen richtigen Client handelt – er also über eine eigene App in AAD verfügt –, und verwenden Sie dessen Client-ID und App-Schlüssel („clientId“ und „appkey“). Ihr getesteter Dienst verfügt ebenfalls über eine eigene App in AAD: Der appID-URI dieser App wird im Webtest im Feld „resource“ angegeben.

### <a name="open-authentication"></a>Offene Authentifizierung
Ein Beispiel für die offene Authentifizierung ist die Anmeldung mit Ihrem Microsoft- oder Google-Konto. Viele Apps, die OAuth verwenden, stellen die Alternative mit dem geheimen Clientschlüssel bereit. Ihre erste Taktik sollte also darin bestehen, diese Möglichkeit zu untersuchen.

Wenn bei Ihrem Test die Anmeldung mit OAuth erforderlich ist, lautet die allgemeine Vorgehensweise wie folgt:

Verwenden Sie ein Tool wie Fiddler, um den Datenverkehr zwischen Ihrem Webbrowser, der Authentifizierungswebsite und Ihrer App zu untersuchen.
Führen Sie mindestens zwei Anmeldungen auf verschiedenen Computern bzw. in verschiedenen Browsern oder mit großen zeitlichen Abständen durch (damit Token ablaufen können).
Vergleichen Sie verschiedene Sitzungen, um das von der authentifizierenden Website zurückgegebene Token zu identifizieren, das nach der Anmeldung dann an Ihren App-Server übergeben wird.
Zeichnen Sie einen Webtests mit Visual Studio auf.
Parametrisieren Sie die Token, indem Sie den Parameter festlegen, wenn das Token vom Authentifikator zurückgegeben wird, und ihn in der Abfrage an die Website verwenden. (Visual Studio versucht, den Test zu parametrisieren, aber die Token werden nicht richtig parametrisiert.)

## <a name="troubleshooting"></a>Problembehandlung

[Artikel zur Problembehandlung](troubleshoot-availability.md) (Dedicated)

## <a name="next-steps"></a>Nächste Schritte

* [Availability Alerts (Verfügbarkeitswarnungen)](availability-alerts.md)
* [Url ping web tests (URL-Pingtests)](monitor-web-app-availability.md)
