---
title: 'Tutorial: Überwachung der Wasserqualität in Azure IoT | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die Anwendungsvorlage zum Überwachen der Wasserqualität für IoT Central bereitstellen und nutzen.
author: miriambrus
ms.author: miriamb
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 481a085b1fd5fec55cd34f885dfcda40fec6f5e7
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179082"
---
# <a name="tutorial-deploy-and-walk-through-the-water-quality-monitoring-application"></a>Tutorial: Bereitstellen und Durchlaufen der Anwendung zum Überwachen der Wasserqualität

Verwenden Sie die IoT Central-Anwendungsvorlage *Überwachung der Wasserqualität* und die Anleitungen in diesem Artikel, um eine End-to-End-Lösung für die Überwachung der Wasserqualität zu entwickeln.


![Architektur zur Überwachung der Wasserqualität](./media/tutorial-waterqualitymonitoring/concepts-water-quality-monitoring-architecture1.png)

### <a name="devices-and-connectivity"></a>Geräte und Konnektivität

Wasserverwaltungslösungen nutzen intelligente Wasserwirtschaftsgeräte wie Strömungsmessgeräte, Geräte zur Überwachung der Wasserqualität, intelligente Ventile und Leckdetektoren.

Geräte in intelligenten Wasserlösungen können über Low-Power Wide Area Networks (LPWAN) oder über einen externen Netzwerkbetreiber eine Verbindung herstellen. Nutzen Sie bei diesen Gerätetypen die [Azure IoT Central-Geräte-Bridge](../core/howto-build-iotc-device-bridge.md), um Ihre Gerätedaten an Ihre IoT-Anwendung in Azure IoT Central zu senden. Sie können auch Gerätegateways verwenden, die IP-fähig sind und mit IoT Central direkt verbunden werden können.

### <a name="iot-central"></a>IoT Central

Azure IoT Central ist eine IoT-App-Plattform, mit der Sie eine IoT-Lösung schnell erstellen und bereitstellen können. Sie können Ihre Lösung mit Branding versehen, anpassen und in Dienste von Drittanbietern integrieren.

Wenn Sie Ihre intelligenten Wasserwirtschaftsgeräte mit IoT Central verbinden, stellt die Anwendung Befehls-, Steuerungs-, Überwachungs- und Warnungsfunktionen, eine Benutzeroberfläche mit integrierter rollenbasierter Zugriffssteuerung (RBAC), konfigurierbare Dashboards sowie Erweiterungsoptionen zur Verfügung.

### <a name="extensibility-and-integrations"></a>Erweiterbarkeit und Integrationen

Sie können Ihre IoT-Anwendung in IoT Central erweitern und haben folgende Optionen:

* Transformieren und Integrieren Ihrer IoT-Daten für erweiterte Analysen, z. B. für das Training von Machine Learning-Modellen, durch kontinuierlichen Datenexport aus der IoT Central-Anwendung.
* Automatisieren von Workflows in anderen Systemen, indem in der IoT Central-Anwendung Aktionen über Power Automate oder Webhooks ausgelöst werden.
* Programmgesteuertes Zugreifen auf Ihre IoT-Anwendung in IoT Central über IoT Central-APIs.

### <a name="business-applications"></a>Geschäftsanwendungen

Sie können mithilfe von IoT-Daten verschiedene Geschäftsanwendungen in einem Wasserversorgungsunternehmen betreiben. In Ihrer [IoT Central-Anwendung für die Überwachung des Wasserverbrauchs](tutorial-water-consumption-monitoring.md) können Sie Regeln und Aktionen konfigurieren und sie so festlegen, dass in [Connected Field Service](/dynamics365/field-service/connected-field-service) Warnungen erstellt werden. Konfigurieren Sie Regeln für Power Automate in IoT Central, um Workflows anwendungs- und dienstübergreifend zu automatisieren. Darüber hinaus können Informationen – basierend auf Dienstaktivitäten in Connected Field Service – zurück an Azure IoT Central gesendet werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]

> * Verwenden der Vorlage **Überwachung der Wasserqualität** für die Erstellung einer entsprechenden Anwendung
> * Erkunden und Anpassen eines Dashboards
> * Erkunden einer Gerätevorlage für die Überwachung der Wasserqualität
> * Erkunden von simulierten Geräten
> * Erkunden und Konfigurieren von Regeln
> * Konfigurieren von Aufträgen
> * Anpassen des Brandings von Anwendungen per Whitelabeling

## <a name="prerequisites"></a>Voraussetzungen

* Zum Bereitstellen dieser App müssen keine besonderen Voraussetzungen erfüllt werden.
* Sie können den Tarif „Free“ oder ein Azure-Abonnement verwenden.

## <a name="create-water-quality-monitoring-application"></a>Erstellen der Anwendung zur Überwachung der Wasserqualität

Gehen Sie wie folgt vor, um die Anwendung zu erstellen:

1. Navigieren Sie zur Buildwebsite für [Azure IoT Central](https://aka.ms/iotcentral). Melden Sie sich dann mit einem persönlichen Microsoft-Konto oder mit einem Geschäfts-, Schul- oder Unikonto an. Wählen Sie auf der linken Navigationsleiste **Erstellen** und dann die Registerkarte **Behörden** aus: :::image type="content" source="media/tutorial-waterqualitymonitoring/iot-central-government-tab-overview1.png" alt-text="Anwendungsvorlage":::

1. Wählen Sie unter **Überwachung der Wasserqualität** die Option **App erstellen** aus.

Weitere Informationen finden Sie unter [Erstellen einer IoT Central-Anwendung](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Einführung in die Anwendung

In den nächsten Abschnitten werden die wichtigsten Features der Anwendung behandelt:

### <a name="dashboard"></a>Dashboard

Nachdem Sie die Anwendung erstellt haben, wird der Bereich mit dem **Wide World Water-Dashboard zur Wasserqualität** geöffnet.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-dashboard1.png" alt-text="Das Dashboard für die Überwachung der Wasserqualität.":::

Als Lösungsentwickler können Sie im Dashboard Ansichten für Bediener erstellen und anpassen. Erkunden Sie aber zunächst das Dashboard, bevor Sie die Anpassungen durchführen.

Alle im Dashboard angezeigten Daten basieren auf simulierten Gerätedaten. Dies wird im nächsten Abschnitt beschrieben.

Das Dashboard enthält die folgenden Arten von Kacheln:

* **Wide World Water Utility-Bildkachel**: Die erste Kachel oben links im Dashboard ist eine Bildkachel für das fiktive Versorgungsunternehmen „Wide World Water“. Sie können die Kachel anpassen, um Ihr eigenes Bild zu verwenden, oder Sie können sie entfernen.

* **KPI-Kachel für pH-Mittelwert**: KPI-Kacheln, z. B. **Average pH in the last 30 minutes** (pH-Mittelwert in den letzten 30 Minuten) sind oben im Dashboardbereich angeordnet. Sie können KPI-Kacheln anpassen und dafür jeweils einen anderen Typ und Zeitbereich festlegen.

* **Karte mit Bereich der Wasserüberwachung**: Für Azure IoT Central wird Azure Maps verwendet. Sie können dieses Feature direkt in Ihrer Anwendung festlegen, um den [Gerätestandort](../core/howto-use-location-data.md) anzuzeigen. Sie können Ihrem Gerät auch die Standortinformationen Ihrer Anwendung zuordnen und dann Azure Maps verwenden, um die Informationen auf einer Karte anzuzeigen. Bewegen Sie den Mauszeiger auf die Karte, und probieren Sie die Steuerelemente aus.

* **Wärmebild zur Verteilung des pH-Mittelwerts**: Sie können verschiedene Visualisierungsdiagramme auswählen, um die Gerätetelemetriedaten so anzuzeigen, wie dies für Ihre Anwendung am besten geeignet ist.

* **Liniendiagramm zu wichtigen Qualitätsindikatoren**: Sie können die Gerätetelemetriedaten so visualisieren, dass sie als Liniendiagramm mit einem bestimmten Zeitbereich dargestellt werden.  

* **Balkendiagramm zur Konzentration chemischer Substanzen**: Sie können Gerätetelemetriedaten als Balkendiagramm visualisieren.

* **Kachel „Reset sensors parameters“ (Sensorparameter zurücksetzen)** : Das Dashboard enthält eine Kachel für Aktionen, die ein Operator direkt über das Dashboard für die Überwachung initiieren kann. Das Zurücksetzen der Eigenschaften eines Geräts ist ein Beispiel für Aktionen dieser Art.

* **Kacheln mit Eigenschaftenlisten**: Das Dashboard verfügt über mehrere Kacheln mit Eigenschaften, die für Informationen zu Schwellenwert, Geräteintegrität und Wartung stehen.

### <a name="customize-the-dashboard"></a>Anpassen des Dashboards

Als Lösungsentwickler können Sie im Dashboard Ansichten für Bediener anpassen.

1. Wählen Sie die Option **Bearbeiten** aus, um den Bereich **Wide World Water-Dashboard zur Wasserqualität** anzupassen. Sie können das Dashboard anpassen, indem Sie Befehle im Menü **Bearbeiten** auswählen. Wenn sich das Dashboard im Modus „Bearbeiten“ befindet, können Sie neue Kacheln hinzufügen oder die vorhandenen Dateien konfigurieren.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/edit-dashboard.png" alt-text="Bearbeiten Ihres Dashboards.":::

1. Wählen Sie die Option **+ Neu** aus, um ein neues Dashboard zu erstellen, das Sie konfigurieren können. Sie können mehrere Dashboards nutzen und über das Dashboardmenü dazwischen navigieren.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Erkunden einer Gerätevorlage für die Überwachung der Wasserqualität

Mit einer Gerätevorlage in Azure IoT Central werden die Funktionen eines Geräts definiert. Die verfügbaren Funktionen sind Telemetrie, Eigenschaften und Befehle. Als Lösungsentwickler können Sie in Azure IoT Central Gerätevorlagen für die Funktionen der angeschlossenen Geräte definieren. Außerdem können Sie simulierte Geräte erstellen, um Ihre Gerätevorlage und die Anwendung zu testen.

Die von Ihnen erstellte Anwendung zur Überwachung der Wasserqualität verfügt über eine Gerätevorlage zur Überwachung der Wasserqualität.

Zeigen Sie die Gerätevorlage wie folgt an:

1. Wählen Sie in Azure IoT Central ganz links im Bereich für Ihre Anwendung die Option **Gerätevorlagen** aus.
1. Wählen Sie in der Liste der Gerätevorlagen **Überwachung der Wasserqualität**  aus, um diese Gerätevorlage zu öffnen.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-device-template.png" alt-text="Die Gerätevorlage.":::

### <a name="customize-the-device-template"></a>Anpassen der Gerätevorlage

Üben Sie das Anpassen der folgenden Einstellungen für Gerätevorlagen:

1. Wählen Sie im Gerätevorlagenmenü die Option **Anpassen** aus.
1. Navigieren Sie zum Telemetrietyp **Temperatur**.
1. Ändern Sie den Wert **Anzeigename** in **Reported temperature** (Gemeldete Temperatur).
1. Ändern Sie die Maßeinheit, oder legen Sie Werte für **Minimalwert** und **Maximalwert** fest.
1. Wählen Sie **Speichern** aus.

#### <a name="add-a-cloud-property"></a>Hinzufügen einer Cloudeigenschaft

1. Wählen Sie im Gerätevorlagenmenü die Option **Cloudeigenschaften** aus.
1. Wählen Sie zum Hinzufügen einer neuen Cloudeigenschaft die Option **+ Cloudeigenschaft hinzufügen** aus. In Azure IoT Central können Sie eine Eigenschaft hinzufügen, die für ein Gerät relevant ist, aber für die das Senden durch das Gerät nicht erwartet wird. Ein Beispiel für eine Eigenschaft dieser Art ist ein Warnungsschwellenwert, der für einen bestimmten Installationsbereich oder bestimmte Ressourcen- oder Wartungsinformationen gilt.
1. Geben Sie unter **Anzeigename** den **Installationsbereich** ein, und wählen Sie unter **Schema** die Option **Zeichenfolge** aus.
1. Wählen Sie **Speichern** aus.

### <a name="explore-views"></a>Erkunden von Ansichten

Die Gerätevorlage „Überwachung der Wasserqualität“ verfügt über vordefinierte Ansichten. Mit den Ansichten wird definiert, wie die Gerätedaten für Bediener angezeigt werden und wie diese die Cloudeigenschaften festlegen können. Erkunden Sie die Ansichten, und üben Sie das Vornehmen von Änderungen.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-device-template-views.png" alt-text="Ansichten der Gerätevorlage.":::

### <a name="publish-the-device-template"></a>Veröffentlichen der Gerätevorlage

Achten Sie beim Vornehmen von Änderungen darauf, dass Sie die Option **Veröffentlichen** auswählen, um die Gerätevorlage zu veröffentlichen.

### <a name="create-a-new-device-template"></a>Erstellen einer neuen Gerätevorlage

1. Wählen Sie auf der Seite **Gerätevorlagen** die Option **+ Neu** aus, um eine neue Gerätevorlage zu erstellen, und führen Sie die Schritte des Erstellungsprozesses aus.
1. Erstellen Sie eine benutzerdefinierte Gerätevorlage, oder wählen Sie im Azure IoT-Gerätekatalog eine Gerätevorlage aus.

## <a name="explore-simulated-devices"></a>Erkunden von simulierten Geräten

Die Anwendung zur Überwachung der Wasserqualität, die Sie mit der Anwendungsvorlage erstellt haben, verfügt über zwei simulierte Geräte. Diese Geräte sind der Gerätevorlage „Überwachung der Wasserqualität“ zugeordnet.

### <a name="view-the-devices"></a>Anzeigen der Geräte

1. Wählen Sie in Ihrer Anwendung im Bereich ganz links die Option **Geräte** aus.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-devices.png" alt-text="Geräte":::

1. Wählen Sie ein simuliertes Gerät aus.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitor-device1.png" alt-text="Auswählen von Gerät 1":::

1. Ändern Sie auf der Registerkarte **Cloudeigenschaften** den Wert unter **Acidity (pH) threshold** (Azidität (pH): Schwellenwert) von **8** in **9**. Wählen Sie anschließend **Speichern** aus.
1. Erkunden Sie die Registerkarten **Geräteeigenschaften** und **Gerätedashboard**.

> [!NOTE]
> Alle Registerkarten wurden über **Ansichten der Gerätevorlage** konfiguriert.

### <a name="add-new-devices"></a>Hinzufügen neuer Geräte

1. Wählen Sie auf der Registerkarte **Geräte** die Option **+ Neu** aus, um ein neues Gerät hinzuzufügen.
1. Verwenden Sie die vorgeschlagene **Geräte-ID**, oder geben Sie Ihre eigene ein. Sie können auch einen **Gerätenamen** für Ihr neues Gerät eingeben.
1. Wählen Sie unter **Gerätevorlage** die Option **Überwachung der Wasserqualität** aus. 
1. Vergewissern Sie sich, dass **Dieses Gerät simulieren?** auf **Ja** festgelegt ist, wenn Sie ein simuliertes Gerät erstellen möchten. 
1. Klicken Sie auf **Erstellen**.  

## <a name="explore-and-configure-rules"></a>Erkunden und Konfigurieren von Regeln

In Azure IoT Central können Sie Regeln erstellen, mit denen die Gerätetelemetrie automatisch überwacht wird. Diese Regeln lösen eine Aktion aus, wenn eine entsprechende Bedingung erfüllt ist. Eine mögliche Aktion ist das Senden von E-Mail-Benachrichtigungen. Andere Möglichkeiten sind eine Power Automate-Aktion oder eine Webhookaktion zum Senden von Daten an andere Dienste.

Die von Ihnen erstellte Anwendung zur Überwachung der Wasserqualität verfügt über zwei vorkonfigurierte Regeln.

### <a name="view-rules"></a>Anzeigen von Regeln

1. Wählen Sie in Ihrer Anwendung im Bereich ganz links die Option **Regeln** aus.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-rules.png" alt-text="Regeln":::

1. Wählen Sie die Option **High pH alert** (Warnung: Hoher pH-Wert) aus. Dies ist eine der vorkonfigurierten Regeln in der Anwendung.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-high-ph-alert.png" alt-text="Die Warnungsregel für hohen pH-Wert.":::

   Die Regel **High pH alert** (Warnung: Hoher pH-Wert) ist so konfiguriert, dass überprüft wird, ob der Wert für die Azidität (pH-Wert) höher als 8 ist.

Fügen Sie der Regel als Nächstes eine E-Mail-Aktion hinzu:

1. Wählen Sie **+ E-Mail** aus.
1. Geben Sie im Feld **Anzeigename** den Namen **High pH alert** (Warnung: Hoher pH-Wert) ein.
1. Geben Sie im Feld **An** die E-Mail-Adresse ein, die Ihrem Azure IoT Central-Konto zugeordnet ist.
1. Geben Sie optional eine Notiz ein, die in den Text der E-Mail aufgenommen werden soll.
1. Wählen Sie **Fertig** aus, um die Aktion fertig zu stellen.
1. Legen Sie die Regel auf **Aktiviert** fest, und wählen Sie **Speichern** aus.

Sie sollten nach einigen Minuten eine E-Mail mit dem Hinweis erhalten, dass die konfigurierte Bedingung erfüllt wurde.

> [!NOTE]
> Von der Anwendung wird jedes Mal eine E-Mail gesendet, wenn eine Bedingung erfüllt ist. Wählen Sie die Option **Deaktivieren** für eine Regel aus, um den Empfang von automatisierten E-Mails über diese Regel zu beenden.
  
Wählen Sie zum Erstellen einer neuen Regel ganz links in Ihrer Anwendung die Option **Regeln** und dann **+ Neu** aus.

## <a name="configure-jobs"></a>Konfigurieren von Aufträgen

Mit Azure IoT Central-Aufträgen können Sie Updates für Geräte- oder Cloudeigenschaften auf mehreren Geräten auslösen. Sie können auch Aufträge verwenden, um Gerätebefehle auf mehreren Geräten auszulösen. Der Workflow wird von Azure IoT Central für Sie automatisiert.

1. Wählen Sie in Ihrer Anwendung im Bereich ganz links die Option **Aufträge** aus.
1. Wählen Sie **+ Neuer Auftrag** aus, und konfigurieren Sie eine beliebige Anzahl von Aufträgen.

## <a name="customize-your-application"></a>Anpassen Ihrer Anwendung

Als Lösungsersteller können Sie verschiedene Einstellungen ändern, um die Benutzeroberfläche in Ihrer Anwendung anzupassen.

1. Wählen Sie **Verwaltung** > **Anwendung anpassen** aus.
1. Wählen Sie unter **Titellogo** die Option **Ändern** aus, um das Bild auszuwählen, das als Logo hochgeladen werden soll.
1. Wählen Sie unter **Browsersymbol** die Option **Ändern** aus, um das Bild für die Anzeige auf Browsertabs auszuwählen.
1. Unter **Browserfarben** können Sie die Standardwerte durch hexadezimale HTML-Farbcodes ersetzen.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-customize-your-application1.png" alt-text="Anpassen Ihrer Anwendung":::

### <a name="update-the-application-image"></a>Aktualisieren des Anwendungsbilds

1. Wählen Sie **Verwaltung** > **Ihre Anwendung** aus.

1. Wählen Sie **Ändern** aus, um ein Bild auszuwählen, das Sie als Anwendungsbild hochladen möchten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie Ihre Anwendung nicht weiterverwenden möchten, sollten Sie sie mit den folgenden Schritten löschen:

1. Öffnen Sie in Ihrer Anwendung ganz links die Registerkarte **Verwaltung**.
1. Wählen Sie **Ihre Anwendung** und dann die Schaltfläche **Löschen** aus.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-application-settings-delete-app1.png" alt-text="Löschen Ihrer Anwendung.":::
