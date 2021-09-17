---
title: 'Tutorial: In-Store Analytics in Azure IoT | Microsoft-Dokumentation'
description: In diesem Tutorial wird gezeigt, wie Sie eine Anwendung für In-Store-Analytics für den Einzelhandel in IoT Central bereitstellen und verwenden.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 08/17/2021
ms.openlocfilehash: 93438e9726dca4fe74da3bbfc9bd06533115e74e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439077"
---
# <a name="tutorial-deploy-and-walk-through-the-in-store-analytics-application-template"></a>Tutorial: Bereitstellen und Durchlaufen der Anwendungsvorlage für In-Store-Analytics

Verwenden Sie die IoT Central-Anwendungsvorlage *In-Store-Analytics* und die Anweisungen in diesem Artikel, um eine End-to-End-Lösung für In-Store Analytics zu entwickeln.

:::image type="content" source="media/tutorial-in-store-analytics-create-app/store-analytics-architecture-frame.png" alt-text="Azure IoT Central: Store-Analyse":::

- Ein Satz von IoT-Sensoren zum Senden von Telemetriedaten an ein Gatewaygerät
- Gatewaygeräte senden Telemetrie und aggregierte Erkenntnisse an IoT Central
- Kontinuierlicher Datenexport an den gewünschten Azure-Dienst zur Bearbeitung
- Daten können im gewünschten Format strukturiert und an einen Speicherdienst gesendet werden
- Geschäftsanwendungen können Daten abfragen und Erkenntnisse generieren, die Einzelhandelsvorgänge stützen

Werfen wir einen Blick auf die Schlüsselkomponenten, die bei Lösungen zur In-Store-Analyse in der Regel eine Rolle spielen.

## <a name="condition-monitoring-sensors"></a>Sensoren zur Zustandsüberwachung

Eine IoT-Lösung beginnt mit einer Reihe von Sensoren, die aussagekräftige Signale in einer Einzelhandelsumgebung erfassen. Dies wird durch verschiedene Arten von Sensoren ganz am linken Rand des obigen Architekturdiagramms wiedergespiegelt.

## <a name="gateway-devices"></a>Gatewaygeräte

Viele IoT-Sensoren können unformatierte Signale direkt in die Cloud oder in ein Gatewaygerät in ihrer Nähe einspeisen. Das Gatewaygerät führt vor dem Senden von zusammengefassten Einblicken an eine IoT Central-Anwendung eine Datenaggregation auf dem Edge aus. Die Gatewaygeräte sind ggf. außerdem für die Weiterleitung von Befehls- und Steuerungsvorgängen an die Sensorgeräte zuständig. 

## <a name="iot-central-application"></a>IoT Central-Anwendung

Die Azure IoT Central-Anwendung erfasst Daten von unterschiedlichen Arten von IoT-Sensoren sowie von Gatewaygeräten innerhalb der Einzelhandelsumgebung und generiert eine Reihe aussagekräftiger Erkenntnisse.

Azure IoT Central bietet darüber hinaus eine maßgeschneiderte Benutzeroberfläche für den Betreiber des Geschäfts, die ihm die Überwachung und Verwaltung der Infrastrukturgeräte aus der Ferne erlaubt.

## <a name="data-transform"></a>Datentransformation

Die Azure IoT Central-Anwendung kann in einer Lösung so konfiguriert werden, dass sie unformatierte oder aggregierte Erkenntnisse an eine Reihe von Azure-PaaS-Diensten (Platform-as-a-Service) exportiert, die Datenbearbeitung durchführen und diese Erkenntnisse anreichern, bevor sie in einer Geschäftsanwendung landen. 

## <a name="business-application"></a>Geschäftsanwendung

Die IoT-Daten können die Basis verschiedener Geschäftsanwendungen bilden, die in einer Einzelhandelsumgebung bereitgestellt sind. Ein Manager oder Mitarbeiter eines Einzelhandelsunternehmens kann diese Anwendungen nutzen, um geschäftliche Erkenntnisse zu visualisieren und in Echtzeit zielgerichtete Maßnahmen zu ergreifen. Informationen zum Erstellen eines Power BI-Echtzeit-Dashboards für Ihr Einzelhandelsteam finden Sie in [diesem Tutorial](./tutorial-in-store-analytics-create-app.md).

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> - Verwenden der Azure IoT Central-Vorlage **In-store analytics - checkout** zum Erstellen einer Filialenanwendung
> - Anpassen der Anwendungseinstellungen
> - Erstellen und Anpassen von IoT-Gerätevorlagen
> - Verbinden von Geräten mit Ihrer Anwendung
> - Hinzufügen von Regeln und Aktionen zum Überwachen von Umgebungsbedingungen

## <a name="prerequisites"></a>Voraussetzungen

- Zum Bereitstellen dieser App müssen keine besonderen Voraussetzungen erfüllt werden.
- Sie können den Tarif „Free“ oder ein Azure-Abonnement verwenden.

## <a name="create-in-store-analytics-application"></a>Erstellen einer Anwendung für In-Store-Analytics

Gehen Sie wie folgt vor, um die Anwendung zu erstellen:

1. Navigieren Sie zur Buildwebsite für [Azure IoT Central](https://aka.ms/iotcentral). Melden Sie sich dann mit einem persönlichen Microsoft-Konto oder mit einem Geschäfts-, Schul- oder Unikonto an. Wählen Sie auf der linken Navigationsleiste **Erstellen** und anschließend die Registerkarte **Einzelhandel** aus: :::image type="content" source="media/tutorial-in-store-analytics-create-app/iotc-retail-homepage.png" alt-text="Vorlage für vernetzte Logistik":::

1. Wählen Sie unter **In-Store-Analyse – Bezahlvorgang** die Option **App erstellen** aus.

Weitere Informationen finden Sie unter [Erstellen einer IoT Central-Anwendung](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Einführung in die Anwendung

In den nächsten Abschnitten werden die wichtigsten Features der Anwendung behandelt:

### <a name="customize-application-settings"></a>Anpassen der Anwendungseinstellungen

Sie können verschiedene Einstellungen ändern, um die Benutzeroberfläche in Ihrer Anwendung anzupassen. In diesem Abschnitt wählen Sie ein vordefiniertes Anwendungsdesign aus. Optional erfahren Sie, wie Sie ein benutzerdefiniertes Design erstellen und das Bild der Anwendung aktualisieren. Mit einem benutzerdefinierten Design können Sie die Farben des Anwendungsbrowsers, das Browsersymbol und das Anwendungslogo festlegen, das im Mastertitel angezeigt wird.

So wählen Sie ein vordefiniertes Anwendungsdesign aus:

1. Wählen Sie im Mastertitel die Option **Einstellungen** aus.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/settings-icon.png" alt-text="Azure IoT Central-Anwendungseinstellungen":::

2. Wählen Sie ein neues **Design** aus.

3. Wählen Sie **Speichern** aus.

Anstatt ein vordefiniertes Design zu verwenden, können Sie ein benutzerdefiniertes erstellen. Wenn Sie zum Anpassen der Anwendung und zum Vervollständigen des Tutorials einen Satz Beispielbilder verwenden möchten, laden Sie die [Contoso-Beispielbilder](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail) herunter.

So erstellen Sie eine benutzerdefinierte Design:

1. Erweitern Sie den linken Bereich, wenn dies noch nicht geschehen ist.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/dashboard-expand.png" alt-text="Linker Bereich in Azure IoT Central":::

1. Wählen Sie **Verwaltung > Anwendung anpassen** aus.

1. Verwenden Sie die Schaltfläche **Ändern**, um ein Bild auszuwählen, das als **Anwendungslogo** hochgeladen werden soll. Geben Sie optional einen Wert für **Alternativtext für Logo** an. 

1. Verwenden Sie die Schaltfläche **Ändern**, um ein Bild für das **Browsersymbol** auszuwählen, das auf den Registerkarten des Browsers angezeigt wird.

1. Optional können Sie die standardmäßigen **Browserfarben** durch Hinzufügen von hexadezimalen HTML-Farbcodes ersetzen. Geben Sie für **Header** den Code *#008575* ein.  Geben Sie für **Akzente** den Code *#A1F3EA* ein. 

1. Wählen Sie **Speichern** aus. 

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/select-application-logo.png" alt-text="Angepasstes Azure IoT Central-Logo":::

    Nach dem Speichern aktualisiert die Anwendung die Browserfarben, das Logo im Mastertitel und das Browsersymbol.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/saved-application-settings.png" alt-text="Aktualisierte Azure IoT Central-Anwendungseinstellungen":::

So aktualisieren Sie das Anwendungsbild:

1. Wählen Sie **Verwaltung > Anwendungseinstellungen** aus.

1. Verwenden Sie die Schaltfläche **Bild auswählen**, um ein Bild auszuwählen, das als Anwendungsbild hochgeladen werden soll. Dieses Bild erscheint auf der Anwendungskachel auf der Seite **Meine Apps** des IoT Central-Anwendungs-Managers.

1. Wählen Sie **Speichern** aus.

1. Navigieren Sie optional zur Ansicht **Meine Apps** auf der Website des [Azure IoT Central-Anwendungs-Managers](https://aka.ms/iotcentral). Auf der Anwendungskachel wird das aktualisierte Anwendungsbild angezeigt.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/customize-application-image.png" alt-text="Angepasstes Azure IoT Central-Anwendungsbild":::

### <a name="create-device-templates"></a>Erstellen von Gerätevorlagen

Sie können Gerätevorlagen anlegen, mit denen Sie und die Bediener der Anwendung Geräte konfigurieren und verwalten können. Zum Erstellen einer Vorlage können Sie eine benutzerdefinierte Vorlage erstellen, eine vorhandene Vorlagendatei importieren oder eine Vorlage aus dem Azure IoT-Gerätekatalog importieren. Nachdem Sie eine Gerätevorlage erstellt und angepasst haben, können Sie diese verwenden, um echte Geräte mit Ihrer Anwendung zu verbinden. Optional können Sie eine Gerätevorlage verwenden, um zu Testzwecken simulierte Geräte zu generieren.

Die Anwendungsvorlage **In-store analytics - checkout** enthält Gerätevorlagen für verschiedene Geräte.  Es gibt Gerätevorlagen für zwei der drei Geräte, die Sie in der Anwendung verwenden. Die RuuviTag-Gerätevorlage ist nicht in der Anwendungsvorlage **In-store analytics - checkout** enthalten. In diesem Abschnitt fügen Sie Ihrer Anwendung eine Gerätevorlage für RuuviTag-Sensoren hinzu.

So fügen Sie eine RuuviTag-Gerätevorlage zu Ihrer Anwendung hinzu:

1. Wählen Sie im linken Bereich **Gerätevorlagen** aus.

1. Wählen Sie **+ Neu** aus, um eine neue Gerätevorlage zu erstellen.

1. Suchen Sie im Azure IoT-Gerätekatalog die Gerätevorlage für den Multisensor **RuuviTag**. 

1. Klicken Sie auf **Weiter: Anpassen**.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png" alt-text="Der Screenshot hebt die Schaltfläche „Weiter: Anpassen“ hervor.":::

1. Klicken Sie auf **Erstellen**. Die Anwendung fügt die RuuviTag-Gerätevorlage hinzu.

1. Wählen Sie im linken Bereich **Gerätevorlagen** aus. Auf der Seite werden alle Gerätevorlagen angezeigt, die in der Anwendungsvorlage und der gerade hinzugefügten RuuviTag-Gerätevorlage enthalten sind.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/device-templates-list.png" alt-text="Azure IoT Central-Gerätevorlage für den RuuviTag-Sensor":::

### <a name="customize-device-templates"></a>Anpassen von Gerätevorlagen

Es gibt drei Möglichkeiten, um Gerätevorlagen in Ihrer Anwendung anzupassen. Die erste Möglichkeit besteht darin, die nativen integrierten Schnittstellen in Ihren Geräten anzupassen, indem Sie die Gerätefunktionen ändern. So können Sie beispielsweise bei einem Temperatursensor Details wie den Anzeigenamen der Temperaturschnittstelle, den Datentyp, die Maßeinheiten sowie den minimalen und maximalen Betriebsbereich ändern. 

Eine zweite Möglichkeit ist die Anpassung Ihrer Gerätevorlagen durch Hinzufügen von Cloudeigenschaften. Cloudeigenschaften sind kein Teil der integrierten Gerätefunktionen. Cloudeigenschaften sind benutzerdefinierte Daten, die Ihre Azure IoT Central-Anwendung erstellt, speichert und mit Ihren Geräten verknüpft. Ein Beispiel für eine Cloudeigenschaft sind etwa berechnete Werte oder auch Metadaten wie ein Standort, den Sie mit einer Reihe von Geräten verknüpfen möchten.

Die dritte Möglichkeit für das Anpassen von Gerätevorlagen ist das Erstellen benutzerdefinierter Ansichten. Mit Ansichten können Bediener Telemetrie- und Metadaten für Ihre Geräte visualisieren, wie z. B. die Gerätemetriken und die -integrität.

Hier verwenden Sie die ersten beiden Methoden, um die Gerätevorlage für Ihre RuuviTag-Sensoren anzupassen.

So passen Sie die integrierten Schnittstellen der RuuviTag-Gerätevorlage an:

1. Wählen Sie im linken Bereich **Gerätevorlagen** aus. 

1. Wählen Sie die Vorlage für RuuviTag-Sensoren aus. 

1. Blenden Sie den linken Bereich aus. In der Zusammenfassung der Vorlage werden die Gerätefunktionen angezeigt.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png" alt-text="Zusammenfassung der Azure IoT Central-Gerätevorlage für den RuuviTag-Sensor":::

1. Wählen Sie im Menü der RuuviTag-Gerätevorlage die Option **Anpassen** aus. 

1. Suchen Sie in der Funktionenliste den Telemetrietyp `humidity`. Das ist das Zeilenelement mit dem bearbeitbaren Wert **Luftfeuchtigkeit** für den *Anzeigename*.

In den folgenden Schritten passen Sie den Telemetrietyp `humidity` für die RuuviTag-Sensoren an. Optional können Sie einige der anderen Telemetrietypen anpassen.

Nehmen Sie für den Telemetrietyp `humidity` die folgenden Änderungen vor:

1. Wählen Sie das Steuerelement **Erweitern** aus, um die Schemadetails für die Zeile zu erweitern.

1. Ändern Sie den **Anzeigename** von *Luftfeuchtigkeit* in einen benutzerdefinierten Wert wie *Relative Luftfeuchtigkeit*.

1. Ändern Sie die Option **Semantischer Typ** von *Keiner* in *Luftfeuchtigkeit*.  Optional können Sie in der erweiterten Schemaansicht Schemawerte für den Telemetrietyp „Luftfeuchtigkeit“ festlegen. Mit den Schemaeinstellungen können Sie detaillierte Validierungsanforderungen für die Daten erstellen, die Ihre Sensoren nachverfolgen. Beispielsweise können Sie für eine bestimmte Schnittstelle minimale und maximale Betriebsbereichswerte festlegen.

1. Wählen Sie **Speichern**, um Ihre Änderungen zu speichern.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png" alt-text="Der Screenshot zeigt den Bildschirm „Anpassen“ und hebt die Schaltfläche „Speichern“ hervor.":::

So fügen Sie eine Cloudeigenschaft zu einer Gerätevorlage in der Anwendung hinzu:

1. Wählen Sie im Menü der RuuviTag-Gerätevorlage die Option **Cloudeigenschaften** aus.

1. Wählen Sie **Cloudeigenschaft hinzufügen** aus. 

Geben Sie die folgenden Werte an, um eine benutzerdefinierte Eigenschaft zum Speichern des Standorts der einzelnen Geräte zu erstellen:

1. Geben Sie für **Anzeigename** den Wert *Standort* ein. Dieser Wert wird automatisch in das Feld **Name** kopiert. Dies ist eine benutzerfreundliche Bezeichnung für die Eigenschaft. Sie können den kopierten Wert verwenden oder ihn ändern.

1. Wählen Sie in der Dropdownliste **Schema** die Option *Zeichenfolge* aus. Mit einem Zeichenfolgentyp können Sie basierend auf der Vorlage eine Zeichenfolge für den Standortnamen einem beliebigen Gerät zuordnen. Beispielsweise können Sie jedem Gerät einen Bereich in einem Laden zuordnen.

1. Legen Sie für **Mindestlänge** *2* fest. 

1. Stellen Sie die Option **Leerzeichen kürzen** auf **Ein**.

1. Wählen Sie **Speichern** aus, um die benutzerdefinierte Cloudeigenschaft zu speichern.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png" alt-text="Anpassung der Azure IoT Central-Gerätevorlage für den RuuviTag-Sensor":::

1. Wählen Sie **Veröffentlichen**. 

    Wenn Sie eine Gerätevorlage veröffentlichen, wird sie für die Bediener der Anwendung sichtbar. Nachdem Sie eine Vorlage veröffentlicht haben, erstellen Sie damit zu Testzwecken simulierte Geräte oder verbinden Sie echte Geräte mit Ihrer Anwendung. Wenn bereits Geräte mit Ihrer Anwendung verbunden sind, werden beim Veröffentlichen einer benutzerdefinierten Vorlage die Änderungen auf die Geräte übertragen.

### <a name="add-devices"></a>Hinzufügen von Geräten

Nachdem Sie Gerätevorlagen erstellt und angepasst haben, können Sie nun Geräte hinzufügen. 

Für dieses Tutorial verwenden Sie zum Erstellen der Anwendung den folgenden Satz an echten und simulierten Geräten:

- Ein echtes Rigado C500-Gateway
- Zwei echte RuuviTag-Sensoren
- Einen simulierten **Auslastungssensor**. Der simulierte Sensor ist in der Anwendungsvorlage enthalten, sodass Sie ihn nicht mehr erstellen müssen. 

> [!NOTE]
> Wenn Sie keine echten Geräte haben, können Sie dieses Tutorial trotzdem absolvieren. Erstellen Sie dafür einfach simulierte RuuviTag-Sensoren. Die folgenden Anweisungen enthalten Schritte zum Erstellen eines simulierten RuuviTag-Sensors. Sie müssen kein simuliertes Gateway erstellen.

Führen Sie die Schritte in den folgenden beiden Artikeln aus, um eine Verbindung mit einem echten Rigado-Gateway und RuuviTag-Sensoren herzustellen. Kehren Sie zu diesem Tutorial zurück, wenn Sie fertig sind. Da Sie in diesem Tutorial bereits Gerätevorlagen erstellt haben, müssen Sie diese in der folgenden Anleitung nicht erneut erstellen.

- Informationen zum Verbinden eines Rigado-Gateways finden Sie in [Verbinden eines Rigado Cascade 500 mit Ihrer Azure IoT Central-Anwendung](../core/howto-connect-rigado-cascade-500.md).
- Informationen zum Verbinden eines RuuviTag-Sensors finden Sie in [Verbinden eines RuuviTag-Sensors mit Ihrer Azure IoT Central-Anwendung](../core/howto-connect-ruuvi.md). Mithilfe dieser Anweisungen können Sie bei Bedarf auch zwei simulierte Sensoren erstellen.

### <a name="add-rules-and-actions"></a>Hinzufügen von Regeln und Aktionen

Als Teil der Verwendung von Sensoren in Ihrer Azure IoT Central-Anwendung zur Überwachung von Umgebungsbedingungen können Sie Regeln erstellen, mit denen Aktionen ausgeführt werden, wenn bestimmte Bedingungen erfüllt sind. Eine Regel ist einer Gerätevorlage und einem oder mehreren Geräten zugeordnet und enthält Bedingungen, die aufgrund von Gerätetelemetriedaten oder Ereignissen erfüllt sein müssen. Einer Regel sind zudem eine oder mehrere Aktionen zugeordnet. Die Aktionen können das Senden von E-Mail-Benachrichtigungen oder das Auslösen einer Webhook-Aktion zum Senden von Daten an andere Dienste umfassen. Die Anwendungsvorlage **In-store analytics - checkout** enthält einige vordefinierte Regeln für die in der Anwendung verwendeten Geräte.

In diesem Abschnitt erstellen Sie eine neue Regel, die die maximale relative Luftfeuchtigkeit basierend auf den Telemetriedaten des RuuviTag-Sensors überprüft. Sie fügen der Regel eine Aktion hinzu, damit die Anwendung eine E-Mail sendet, wenn die Luftfeuchtigkeit den maximalen Wert überschreitet. 

So erstellen Sie eine Regel: 

1. Erweitern Sie den linken Bereich.

1. Wählen Sie **Regeln** aus.

1. Wählen Sie **+ Neu** aus.

1. Geben Sie *Luftfeuchtigkeitsgrad* als Namen der Regel ein. 

1. Wählen Sie in **Bereiche** die RuuviTag-Gerätevorlage aus. Die von Ihnen definierte Regel wird basierend auf dieser Vorlage für alle Sensoren angewendet. Optional können Sie einen Filter erstellen, der die Regel nur auf eine definierte Teilmenge der Sensoren anwendet. 

1. Wählen Sie `Relative humidity` als **Telemetrie** aus. Dies ist die Gerätefunktion, die Sie in einem vorherigen Schritt angepasst haben.

1. Wählen Sie `Is greater than` als den **Bediener** aus. 

1. Geben Sie als **Wert** einen gängigen oberen Bereich für die Luftfeuchtigkeit im Innenbereich für Ihre Umgebung ein. Geben Sie z. B. *65* ein. Sie haben eine Bedingung für Ihre Regel festgelegt, die eintritt, wenn die relative Luftfeuchtigkeit an einem echten oder simulierten RuuviTag-Sensor diesen Wert überschreitet. Möglicherweise müssen Sie den Wert abhängig vom normalen Luftfeuchtigkeitsbereich in Ihrer Umgebung nach oben oder unten anpassen.  

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/rules-add-conditions.png" alt-text="Azure IoT Central: Hinzufügen von Regelbedingungen":::

So fügen Sie der Regel eine Aktion hinzu:

1. Wählen Sie **+ E-Mail** aus.

1. Geben Sie *Benachrichtigung bei hoher Luftfeuchtigkeit* als benutzerfreundlichen **Anzeigename** für die Aktion ein. 

1. Geben Sie unter **An** die Ihrem Konto zugeordnete E-Mail-Adresse ein. Wenn Sie eine andere E-Mail-Adresse verwenden, muss die von Ihnen verwendete Adresse einem Benutzer zugeordnet sein, der der Anwendung hinzugefügt wurde. Der Benutzer muss sich auch mindestens einmal an- und abmelden.

1. Geben Sie optional eine Notiz ein, die in den Text der E-Mail aufgenommen werden soll.

1. Wählen Sie **Fertig** aus, um die Aktion fertig zu stellen.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/rules-add-action.png" alt-text="Azure IoT Central: Hinzufügen von Aktionen zu Regeln":::

1. Wählen Sie **Speichern** aus, um die neue Regel zu speichern und zu aktivieren. 

    Innerhalb weniger Minuten sollten die ersten E-Mails beim angegebenen E-Mail-Konto eingehen. Die Anwendung sendet jedes Mal eine E-Mail, wenn ein Sensor anzeigt, dass die Luftfeuchtigkeit den in Ihrer Bedingung angegebenen Wert überschritten hat.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

* Verwenden der Azure IoT Central-Vorlage **In-store analytics - checkout** zum Erstellen einer Filialenanwendung
* Anpassen der Anwendungseinstellungen
* Erstellen und Anpassen von IoT-Gerätevorlagen
* Verbinden von Geräten mit Ihrer Anwendung
* Hinzufügen von Regeln und Aktionen zum Überwachen von Umgebungsbedingungen

Nachdem Sie nun eine Azure IoT Central-Anwendung zur Überwachung von Umgebungsbedingungen erstellt haben, wird als Nächstes dieser Schritt empfohlen:

> [!div class="nextstepaction"]
> [Anpassen des Dashboards](./tutorial-in-store-analytics-customize-dashboard.md)
