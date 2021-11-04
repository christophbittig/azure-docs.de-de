---
title: Problembehandlung bei Azure Monitor Application Insights für Java
description: Erfahren Sie, wie Sie Probleme mit dem Java-Agent für Azure Monitor Application Insights beheben.
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: dc78df7075c53b05a5dbd51182711de54a799180
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455205"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Handbuch zur Problembehandlung: Azure Monitor Application Insights für Java

In diesem Artikel werden einige der häufigsten Probleme behandelt, die beim Instrumentieren einer Java-Anwendung mit dem Java-Agent für Application Insights auftreten können. Außerdem werden die Schritte zum Beheben dieser Probleme beschrieben. Application Insights ist ein Feature des Azure Monitor-Plattformdiensts.

## <a name="check-the-self-diagnostic-log-file"></a>Überprüfen der Protokolldatei zur Selbstdiagnose

Standardmäßig erzeugt Application Insights Java 3.x eine Protokolldatei mit dem Namen `applicationinsights.log` in demselben Verzeichnis, in dem sich auch die Datei `applicationinsights-agent-3.2.2.jar` befindet.

Diese Protokolldatei sollten Sie als Erstes auf Hinweise auf aufgetretene Probleme untersuchen.

## <a name="jvm-fails-to-start"></a>Fehler beim Starten der JVM

Wenn beim Starten der JVM die Fehlermeldung „Fehler beim Öffnen der ZIP-Datei oder JAR-Manifest fehlt“ angezeigt wird, versuchen Sie erneut, die JAR-Datei des Agents herunterzuladen, da sie möglicherweise während der Dateiübertragung beschädigt wurde.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Durchführen eines Upgrades vom Application Insights SDK für Java 2.x

Wenn Sie bereits das Application Insights Java 2.x SDK in Ihrer Anwendung nutzen, können Sie es weiterhin verwenden.
Der Application Insights Java 3.x-Agent erkennt es und erfasst sowie korreliert alle benutzerdefinierten Telemetriedaten, die über das 2.x SDK gesendet werden, während gleichzeitig alle vom 2.x SDK durchgeführten automatischen Sammlungen unterdrückt werden, um doppelte Telemetriedaten zu verhindern.
Weitere Informationen finden Sie unter [Upgrade des Application Insights Java 2.x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Durchführen eines Upgrades von Application Insights für Java 3.0 (Vorschau)

Wenn Sie ein Upgrade vom Agent der Vorschauversion von Java 3.0 durchführen, überprüfen Sie alle [Konfigurationsoptionen](./java-standalone-config.md) sorgfältig. Die JSON-Struktur wurde im Release 3.0 für die allgemeine Verfügbarkeit (General Availability, GA) grundlegend geändert.

Zu diesen Änderungen zählen die folgenden:

-  Der Name der Konfigurationsdatei wurde von `ApplicationInsights.json` in `applicationinsights.json` geändert.
-  Der Knoten `instrumentationSettings` ist nicht mehr vorhanden. Alle Inhalte in `instrumentationSettings` wurden auf die Stammebene verschoben. 
-  Konfigurationsknoten wie `sampling`, `jmxMetrics`, `instrumentation` und `heartbeat` wurden aus `preview` auf die Stammebene verschoben.

## <a name="some-logging-is-not-auto-collected"></a>Keine automatische Erfassung bestimmter Protokolle

Eine Protokollierung erfolgt nur, wenn erstens der für das Protokollierungsframework konfigurierte Schwellenwert und zweitens auch der in Application Insights konfigurierte Schwellenwert erreicht wird.

Wenn Ihr Protokollierungsframework z. B. so konfiguriert ist, dass es `WARN` (und höher) aus Paket `com.example` protokolliert, und Application Insights so konfiguriert ist, dass es `INFO` (und höher) aufzeichnet, dann wird Application Insights nur `WARN` (und höher) aus Paket `com.example` aufzeichnen.

Wenn Sie wissen möchten, ob eine bestimmte Protokollierungsanweisung den konfigurierten Schwellenwert des Protokollierungsframeworks erreicht, überprüfen Sie am besten, ob er in Ihrem normalen Anwendungsprotokoll (z. B. in einer Datei oder an der Konsole) angezeigt wird.

Beachten Sie außerdem, dass die Protokollmeldung (zusammen mit Details zum Ausnahmeobjekt) im Azure-Portal in der Tabelle `exceptions` und nicht in der Tabelle `traces`angezeigt wird, wenn ein Ausnahmeobjekt an die Protokollierung übergeben wird.

Weitere Informationen finden Sie unter [Automatisch gesammelte Protokolle](./java-standalone-config.md#auto-collected-logging).

## <a name="import-ssl-certificates"></a>Importieren von SSL-Zertifikaten

Dieser Abschnitt hilft Ihnen bei der Problembehandlung und möglichen Behebung von Ausnahmen im Zusammenhang mit SSL-Zertifikaten bei Verwendung des Java-Agents.

Nachfolgend sehen Sie zwei verschiedene Möglichkeiten zum Beheben dieses Problems:
* Bei Verwendung eines standardmäßigen Java-Keystores
* Bei Verwendung eines benutzerdefinierten Java-Keystores

Wenn Sie nicht sicher sind, welche Methode Sie nutzen sollen, überprüfen Sie, ob Sie ein JVM-Argument `-Djavax.net.ssl.trustStore=...` besitzen.
Wenn Sie _nicht_ über ein solches JVM-Argument verfügen, verwenden Sie aller Wahrscheinlichkeit nach den standardmäßigen Java-Keystore.
Wenn Sie ein solches JVM-Argument _besitzen_, verwenden Sie aller Wahrscheinlichkeit nach einen benutzerdefinierten Keystore, und das JVM-Argument weist Sie auf Ihren benutzerdefinierten Keystore hin.

### <a name="if-using-the-default-java-keystore"></a>Bei Verwendung des standardmäßigen Java-Keystores:

Der standardmäßige Java-Keystore verfügt in der Regel bereits über alle Stammzertifikate der Zertifizierungsstelle. Es kann jedoch einige Ausnahmen geben, z. B. kann das Zertifikat für den Erfassungsendpunkt von einem anderen Stammzertifikat signiert sein. Deshalb werden die folgenden drei Schritte zur Behebung dieses Problems empfohlen:

1.  Überprüfen Sie, ob das Stammzertifikat, das zum Signieren des Application Insights-Endpunkts verwendet wurde, bereits im standardmäßigen Keystore vorhanden ist. Die Zertifikate der vertrauenswürdigen Zertifizierungsstelle sind standardmäßig in `$JAVA_HOME/jre/lib/security/cacerts` gespeichert. Verwenden Sie den folgenden Befehl, um Zertifikate in einem Java-Keystore aufzulisten:
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    Sie können die Ausgabe wie folgt in eine temporäre Datei umleiten (ist später leicht zu durchsuchen):
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. Sobald Sie über die Liste der Zertifikate verfügen, führen Sie [diese Schritte](#steps-to-download-ssl-certificate) aus, um das Stammzertifikat herunterzuladen, das zum Signieren des Application Insights-Endpunkts verwendet wurde.

    Nachdem Sie das Zertifikat heruntergeladen haben, generieren Sie mit dem folgenden Befehl einen SHA-1-Hash für das Zertifikat:
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    Kopieren Sie den SHA-1-Wert, und prüfen Sie, ob dieser Wert in der Datei „temp.txt“ enthalten ist, die Sie zuvor gespeichert haben.  Wenn Sie den SHA-1-Wert in der temporären Datei nicht finden können, weist dies darauf hin, dass das heruntergeladene Stammzertifikat im standardmäßigen Java-Keystore fehlt.


3. Importieren Sie das Stammzertifikat mithilfe des folgenden Befehls in den standardmäßigen Java-Keystore:
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    In diesem Fall lautet der Befehl:
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>Bei Verwendung eines benutzerdefinierten Java-Keystores:

Wenn Sie einen benutzerdefinierten Java-Keystore verwenden, müssen Sie ggf. die SSL-Stammzertifikate für die Application Insights-Endpunkte importieren.
Es werden die folgenden zwei Schritte zur Behebung dieses Problems empfohlen:
1. Führen Sie [diese Schritte](#steps-to-download-ssl-certificate) aus, um das Stammzertifikat vom Application Insights-Endpunkt herunterzuladen.
2. Verwenden Sie den folgenden Befehl, um das SSL-Stammzertifikat in den benutzerdefinierten Java-Keystore zu importieren:
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>Schritte zum Herunterladen des SSL-Zertifikats

1.  Öffnen Sie Ihren bevorzugten Browser, und navigieren Sie zur `IngestionEndpoint`-URL in der Verbindungszeichenfolge, die zum Instrumentieren Ihrer Anwendung verwendet wird.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="Screenshot einer Application Insights-Verbindungszeichenfolge" lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  Wählen Sie im Browser das Symbol **Websiteinformationen anzeigen** (Schlosssymbol) und dann die Option **Zertifikat** aus.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Screenshot der Option „Zertifikat“ in den Websiteinformationen" lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  Anstelle des untergeordneten Zertifikats sollten Sie das Stammzertifikat wie nachfolgend gezeigt herunterladen. Später müssen Sie auf „Zertifikatpfad“ klicken, das Stammzertifikat auswählen und dann auf „Zertifikat anzeigen“ klicken. Daraufhin wird ein neues Zertifikatmenü angezeigt, und Sie können das Zertifikat aus dem neuen Menü herunterladen.

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="Screenshot zum Auswählen des Stammzertifikats" lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  Wechseln Sie zur Registerkarte **Details**, und wählen Sie **In Datei kopieren** aus.
5.  Wählen Sie die Schaltfläche **Weiter**, anschließend das Format **Base64-codiert X.509 (.CER)** und dann erneut **Weiter** aus.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Screenshot des Zertifikatexport-Assistenten mit ausgewähltem Format" lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  Geben Sie die Datei an, in der Sie das SSL-Zertifikat speichern möchten. Wählen Sie anschließend **Weiter** > **Fertig stellen** aus. Die Meldung „Der Exportvorgang wurde erfolgreich abgeschlossen“ sollte angezeigt werden.

> [!WARNING]
> Sie müssen diese Schritte erneut ausführen, um vor Ablauf des aktuellen Zertifikats ein neues Zertifikat zu erhalten. Informationen zum Ablauf finden Sie im Dialogfeld **Zertifikat** auf der Registerkarte **Details**.
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Screenshot mit Details zum SSL-Zertifikat" lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
