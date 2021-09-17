---
title: 'Tutorial: Vernetzte Abfallwirtschaft mit Azure IoT | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die Anwendungsvorlage für vernetzte Abfallwirtschaft für IoT Central bereitstellen und verwenden.
author: miriambrus
ms.author: miriamb
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1ed898b02f2c30c3dcb043903bd3c3261d088539
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183231"
---
# <a name="tutorial-deploy-and-walk-through-the-connected-waste-management-application-template"></a>Tutorial: Bereitstellen und Durchlaufen einer Anwendungsvorlage für vernetzte Abfallwirtschaft

Verwenden Sie die IoT Central-Anwendungsvorlage *Vernetzte Abfallwirtschaft* und die Anweisungen in diesem Artikel, um eine End-to-End-Lösung für die vernetzte Abfallwirtschaft zu entwickeln.

:::image type="content" source="media/tutorial-connectedwastemanagement/concepts-connected-waste-management-architecture-1.png" alt-text="Architektur für vernetzte Abfallwirtschaft":::

### <a name="devices-and-connectivity"></a>Geräte und Konnektivität

Geräte im öffentlichen Raum (z. B. Abfallbehälter) können per LPWAN (Low-Power Wide Area Network) oder über einen externen Netzbetreiber vernetzt werden. Nutzen Sie für diese Gerätetypen [Azure IoT Central-Geräte-Bridge](../core/howto-build-iotc-device-bridge.md), um Ihre Gerätedaten an Ihre IoT-Anwendung in Azure IoT Central zu senden. Sie können auch Gerätegateways verwenden, die IP-fähig sind und direkt mit IoT Central verbunden werden können.

### <a name="iot-central"></a>IoT Central

Azure IoT Central ist eine IoT-App-Plattform, die eine schnelle Erstellung und Bereitstellung einer IoT-Lösung ermöglicht. Sie können Ihre Lösung mit Branding versehen, anpassen und in Dienste von Drittanbietern integrieren.

Wenn Sie Ihre intelligenten Abfallwirtschaftsgeräte mit IoT Central verbinden, stellt die Anwendung Befehls-, Steuerungs-, Überwachungs- und Alarmfunktionen, eine Benutzeroberfläche mit integrierter rollenbasierter Zugriffssteuerung (RBAC), konfigurierbare Dashboards und Erweiterungsoptionen zur Verfügung.

### <a name="extensibility-and-integrations"></a>Erweiterbarkeit und Integrationen

Sie können Ihre IoT-Anwendung in IoT Central erweitern und haben folgende Optionen:

* Transformieren und Integrieren Ihrer IoT-Daten für erweiterte Analysen, z. B. für das Training von Machine Learning-Modellen durch kontinuierlichen Datenexport aus der IoT Central-Anwendung
* Automatisieren von Workflows in anderen Systemen, indem in der IoT Central-Anwendung Aktionen über Power Automate oder Webhooks ausgelöst werden
* Programmgesteuertes Zugreifen auf Ihre IoT-Anwendung in IoT Central über IoT Central-APIs

### <a name="business-applications"></a>Geschäftsanwendungen

Sie können IoT-Daten verwenden, um verschiedene Geschäftsanwendungen in einem Abfallentsorgungsunternehmen zu betreiben. Bei einer Lösung für vernetzte Abfallwirtschaft können Sie beispielsweise die Aussendung von Müllfahrzeugen optimieren. Diese Optimierung kann basierend auf IoT-Sensordaten von vernetzten Abfallbehältern erfolgen. Sie können in Ihrer [IoT Central-Anwendung für vernetzte Abfallwirtschaft](./tutorial-connected-waste-management.md) Regeln und Aktionen in [Connected Field Service](/dynamics365/field-service/connected-field-service) konfigurieren und festlegen, damit Warnungen erstellt werden. Konfigurieren Sie Power Automate-Regeln in IoT Central, um Workflows anwendungs- und dienstübergreifend zu automatisieren. Darüber hinaus können Informationen basierend auf Dienstaktivitäten in Connected Field Service zurück an Azure IoT Central gesendet werden.

Sie können die folgenden Integrationsprozesse problemlos mit IoT Central und Connected Field Service konfigurieren:

* Azure IoT Central kann Informationen zu Geräteanomalien zur Diagnose an Connected Field Service senden.
* Mit Connected Field Service können Vorfälle oder Arbeitsaufträge erstellt werden, die über Geräteanomalien ausgelöst werden.
* Connected Field Service kann auch genutzt werden, um Techniker für die Inspektion einzuplanen, damit Ausfallzeiten verhindert werden.
* Das Gerätedashboard von Azure IoT Central kann mit relevanten Dienst- und Zeitplaninformationen aktualisiert werden.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]

> * Verwenden der Azure IoT Central-Vorlage *Vernetzte Abfallwirtschaft* für die App-Erstellung
> * Erkunden und Anpassen des Dashboards 
> * Erkunden der Gerätevorlage für vernetzte Abfallbehälter
> * Erkunden von simulierten Geräten
> * Erkunden und Konfigurieren von Regeln
> * Konfigurieren von Aufträgen
> * Anpassen des Brandings Ihrer Anwendung

## <a name="prerequisites"></a>Voraussetzungen

* Zum Bereitstellen dieser App müssen keine besonderen Voraussetzungen erfüllt werden.
* Sie können den Tarif „Free“ oder ein Azure-Abonnement verwenden.

## <a name="create-connected-waste-management-application"></a>Erstellen einer Anwendung für vernetzte Abfallwirtschaft

1. Navigieren Sie zur Buildwebsite für [Azure IoT Central](https://aka.ms/iotcentral). Melden Sie sich dann mit einem persönlichen Microsoft-Konto oder mit einem Geschäfts-, Schul- oder Unikonto an. Wählen Sie auf der linken Navigationsleiste **Erstellen** und anschließend die Registerkarte **Behörden** aus: :::image type="content" source="media/tutorial-connectedwastemanagement/iot-central-government-tab-overview.png" alt-text="Vorlage für vernetzte Abfallwirtschaft":::

1. Wählen Sie unter **Connected waste management** (Vernetzte Abfallwirtschaft) die Option **App erstellen** aus.

Weitere Informationen finden Sie unter [Erstellen einer IoT Central-Anwendung](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Einführung in die Anwendung

In den nächsten Abschnitten werden die wichtigsten Features der Anwendung behandelt:

### <a name="dashboard"></a>Dashboard 

Nach dem Bereitstellen der Anwendungsvorlage ist das Standarddashboard **Wide World waste management dashboard** (Wide World-Dashboard für Abfallwirtschaft).

:::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-1.png" alt-text="Screenshot: Wide World-Dashboard für die Abfallwirtschaft":::


Als Lösungsentwickler können Sie Ansichten im Dashboard für Bediener erstellen und anpassen. Sehen wir uns zunächst das Dashboard etwas genauer an. 

>[!NOTE]
>Alle im Dashboard angezeigten Daten basieren auf simulierten Gerätedaten. Weitere Informationen hierzu folgen im nächsten Abschnitt. 

Das Dashboard umfasst verschiedene Kacheln:

* **Bildkachel für das Entsorgungsunternehmen „Wide World Waste“:** Die erste Kachel auf dem Dashboard ist eine Bildkachel des fiktiven Abfallentsorgungsunternehmens „Wide World Waste“. Sie können die Kachel anpassen und Ihr eigenes Bild einfügen oder das Bild entfernen. 

* **Bildkachel für Abfallbehälter:** Mithilfe von Bild- und Inhaltskacheln können Sie eine visuelle Darstellung des zu überwachenden Geräts sowie eine Beschreibung erstellen. 

* **KPI-Kachel für Füllstand:** Auf dieser Kachel wird ein Wert angezeigt, der von einem *Füllstandssensor* in einem Abfallbehälter gemeldet wird. Der Füllstandssensor sowie andere Abfallbehältersensoren für *Geruchsmessung* oder *Gewicht* können remote überwacht werden. Ein Operator kann dann geeignete Maßnahmen ergreifen und beispielsweise ein Fahrzeug der Stadtreinigung losschicken. 

* **Umgebungskarte für die Abfallüberwachung:** Diese Kachel basiert auf Azure Maps und kann direkt in Azure IoT Central konfiguriert werden. Auf der Kartenkachel wird der [Gerätestandort](../core/howto-use-location-data.md) angezeigt. Bewegen Sie den Mauszeiger auf die Karte, und probieren Sie die Steuerelemente wie „Vergrößern“, „Verkleinern“ und „Erweitern“ aus.

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-map.png" alt-text="Screenshot: Vorlage für vernetzte Abfallwirtschaft: Karte auf dem Dashboard":::



* **Balkendiagramm für Füllstand, Geruch und Gewicht:** Sie können einzelne oder mehrere Arten von Gerätetelemetriedaten in einem Balkendiagramm visualisieren. Sie können das Balkendiagramm auch erweitern.  

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-bar-chart.png" alt-text="Screenshot: Vorlage für vernetzte Abfallwirtschaft: Balkendiagramm auf dem Dashboard":::


* **Field Services:** Das Dashboard enthält einen Link für die Integration in Dynamics 365 for Field Service aus Ihrer Azure IoT Central-Anwendung. Beispielsweise können Sie Field Services nutzen, um Tickets für die Abfallsammlung zu erstellen. 

### <a name="customize-the-dashboard"></a>Anpassen des Dashboards 

Sie können das Dashboard anpassen, indem Sie das Menü **Bearbeiten** auswählen. Anschließend können Sie neue Kacheln hinzufügen oder bereits vorhandene Kacheln konfigurieren. So sieht das Dashboard im Bearbeitungsmodus aus: 

:::image type="content" source="media/tutorial-connectedwastemanagement/edit-dashboard.png" alt-text="Screenshot: Vorlage für vernetzte Abfallwirtschaft: Dashboard im Bearbeitungsmodus":::


Sie können auch **+ Neu** auswählen, um ein neues Dashboard zu erstellen und es neu zu konfigurieren. Sie können mehrere Dashboards verwenden und über das Dashboardmenü zwischen Ihren Dashboards wechseln. 

### <a name="explore-the-device-template"></a>Erkunden der Gerätevorlage

Eine Gerätevorlage in Azure IoT Central definiert die Funktionen eines Geräts und kann Telemetriedaten, Eigenschaften oder Befehle umfassen. Als Lösungsentwickler können Sie Gerätevorlagen definieren, die die Funktionen der zu vernetzenden Geräte darstellen. 

Die Anwendung für vernetzte Abfallwirtschaft beinhaltet eine Beispielgerätevorlage für vernetzte Abfallbehälter.

Zeigen Sie die Gerätevorlage wie folgt an:

1. Wählen Sie in Azure IoT Central im linken Bereich Ihrer App die Option **Gerätevorlagen** aus. 

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template.png" alt-text="Screenshot: Liste mit den Gerätevorlagen in der Anwendung":::


1. Wählen Sie in der Liste **Gerätevorlagen** die Option **Connected Waste Bin** (Vernetzter Abfallbehälter) aus.

1. Sehen Sie sich die Funktionen der Gerätevorlage an. Wie Sie sehen, werden Sensoren wie **Fill level** (Füllstand), **Odor meter** (Geruchsmessung), **Weight** (Gewicht) und **Location** (Standort) definiert.

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template-connected-bin.png" alt-text="Screenshot: Details der Gerätevorlage für vernetzte Abfallbehälter":::


### <a name="customize-the-device-template"></a>Anpassen der Gerätevorlage

Versuchen Sie, Folgendes anzupassen:

1. Wählen Sie im Gerätevorlagenmenü die Option **Anpassen** aus.
1. Navigieren Sie zum Telemetrietyp **Odor meter** (Geruchsmessung).
1. Ändern Sie unter **Anzeigename** den Namen **Odor meter** (Geruchsmessung) in **Odor level** (Geruchsstärke).
1. Versuchen Sie, die Maßeinheit zu ändern, oder legen Sie **Minimalwert** und **Maximalwert** fest.
1. Wählen Sie **Speichern** aus. 

### <a name="add-a-cloud-property"></a>Hinzufügen einer Cloudeigenschaft 

Gehen Sie dabei folgendermaßen vor:

1. Wählen Sie im Gerätevorlagenmenü die Option **Cloudeigenschaft** aus.
1. Wählen Sie **+ Cloudeigenschaft hinzufügen** aus. In Azure IoT Central können Sie eine Eigenschaft hinzufügen, die für das Gerät relevant ist, aber nicht von einem Gerät gesendet werden soll. Bei einer Cloudeigenschaft kann es sich beispielsweise um einen Warnungsschwellenwert handeln, der für einen bestimmten Installationsbereich oder für bestimmte Ressourcen- oder Wartungsinformationen gilt. 
1. Wählen Sie **Speichern** aus. 
 
### <a name="views"></a>Sichten 

Die Gerätevorlage für vernetzte Abfallbehälter verfügt über vordefinierte Ansichten. Erkunden Sie die Ansichten, und aktualisieren Sie sie, falls gewünscht. Mit den Ansichten wird definiert, wie die Gerätedaten für Operatoren angezeigt werden und wie sie Cloudeigenschaften festlegen können. 

:::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template-views.png" alt-text="Screenshot: Vorlage für vernetzte Abfallwirtschaft: Gerätevorlagenansichten":::


### <a name="publish"></a>Veröffentlichen 

Falls Sie Änderungen vorgenommen haben, denken Sie daran, die Gerätevorlage zu veröffentlichen. 

### <a name="create-a-new-device-template"></a>Erstellen einer neuen Gerätevorlage 

Wählen Sie zum Erstellen einer neuen Gerätevorlage die Option **+ Neu** aus, und führen Sie die entsprechenden Schritte aus. Sie können eine benutzerdefinierte Gerätevorlage von Grund auf neu erstellen oder eine Gerätevorlage aus dem Azure-Gerätekatalog auswählen. 

### <a name="explore-simulated-devices"></a>Erkunden von simulierten Geräten

In Azure IoT Central können Sie simulierte Geräte erstellen, um Ihre Gerätevorlage und Ihre Anwendung zu testen. 

Die Anwendung für vernetzte Abfallwirtschaft verfügt über zwei simulierte Geräte, die der Gerätevorlage für vernetzte Abfallbehälter zugeordnet sind. 

### <a name="view-the-devices"></a>Anzeigen der Geräte

1. Wählen Sie im linken Bereich von Azure IoT Central die Option **Geräte** aus. 

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-devices.png" alt-text="Screenshot: Vorlage für vernetzte Abfallwirtschaft: Geräte":::


1. Wählen Sie das Gerät **Connected Waste Bin** (Vernetzter Abfallbehälter) aus.  

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-devices-bin-1.png" alt-text="Screenshot: Vorlage für vernetzte Abfallwirtschaft: Geräteeigenschaften":::


1. Navigieren Sie zur Registerkarte **Cloudeigenschaften**. Ändern Sie den Schwellenwert für die Warnung bei vollem Behälter (**Bin full alert threshold**) von **95** in **100**. 

Erkunden Sie die Registerkarten **Geräteeigenschaften** und **Gerätedashboard**. 

> [!NOTE]
> Alle Registerkarten wurden über die Ansichten der Gerätevorlage konfiguriert.

### <a name="add-new-devices"></a>Hinzufügen neuer Geräte

Sie können neue Geräte hinzufügen, indem Sie auf der Registerkarte **Geräte** die Option **+ Neu** auswählen. 

## <a name="explore-and-configure-rules"></a>Erkunden und Konfigurieren von Regeln

In Azure IoT Central können Sie Regeln zur automatischen Überwachung von Gerätetelemetriedaten erstellen und festlegen, dass Aktionen ausgelöst werden sollen, wenn einzelne oder mehrere Bedingungen erfüllt sind. Mögliche Aktionen sind beispielsweise das Senden von E-Mail-Benachrichtigungen, das Auslösen einer Aktion in Power Automate oder das Starten einer Webhookaktion zum Senden von Daten an andere Dienste.

Die Anwendung Vernetzte Abfallwirtschaft verfügt über vier Beispielregeln.

### <a name="view-rules"></a>Anzeigen von Regeln

1. Wählen Sie im linken Bereich von Azure IoT Central die Option **Regeln** aus.

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-rules.png" alt-text="Screenshot: Vorlage für vernetzte Abfallwirtschaft: Regeln":::


1. Wählen Sie **Bin full alert** (Warnung: Behälter voll) aus.

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-bin-full-alert.png" alt-text="Screenshot: Warnung für vollen Behälter":::


 1. Durch **Bin full alert** (Warnung: Behälter voll) wird folgende Bedingung überprüft: **Füllstand ist größer oder gleich dem Schwellenwert für die Warnung bei vollem Behälter.**

    Der Schwellenwert für die Warnung bei vollem Behälter (**Bin full alert threshold**) ist eine in der Gerätevorlage für vernetzte Abfallbehälter definierte Cloudeigenschaft. 

Als Nächstes erstellen wir eine E-Mail-Aktion.

### <a name="create-an-email-action"></a>Erstellen einer E-Mail-Aktion

In der Liste **Aktionen** der Regel können Sie eine E-Mail-Aktion konfigurieren:
1. Wählen Sie **+ E-Mail** aus. 
1. Geben Sie unter **Anzeigename** den Namen **High pH alert** (Warnung: Hoher pH-Wert) ein.
1. Geben Sie unter **An** die E-Mail-Adresse ein, die Ihrem Azure IoT Central-Konto zugeordnet ist. 
1. Geben Sie optional eine Notiz ein, die in den Text der E-Mail aufgenommen werden soll.
1. Wählen Sie **Fertig** > **Speichern** aus. 

Nun erhalten Sie eine E-Mail, wenn die konfigurierte Bedingung erfüllt ist.

>[!NOTE]
>Von der Anwendung wird jedes Mal eine E-Mail gesendet, wenn eine Bedingung erfüllt ist. Deaktivieren Sie die Regel, um den Empfang von E-Mails über die automatisierte Regel zu beenden. 
  
Wenn Sie eine neue Regel erstellen möchten, können Sie im linken Bereich unter **Regeln** die Option **+ Neu** auswählen.

## <a name="configure-jobs"></a>Konfigurieren von Aufträgen

In Azure IoT Central können Sie Aufträge verwenden, um Geräte- oder Cloudeigenschaften für mehrere Geräte zu aktualisieren. Sie können auch Aufträge verwenden, um Gerätebefehle auf mehreren Geräten auszulösen. Der Workflow wird von Azure IoT Central für Sie automatisiert. 

1. Wählen Sie im linken Bereich von Azure IoT Central die Option **Aufträge** aus. 
1. Wählen Sie **+ Neu** aus, und konfigurieren Sie eine beliebige Anzahl von Aufträgen. 

## <a name="customize-your-application"></a>Anpassen Ihrer Anwendung 

Als Lösungsersteller können Sie verschiedene Einstellungen ändern, um die Benutzeroberfläche in Ihrer Anwendung anzupassen.

### <a name="change-the-application-theme"></a>Ändern des Anwendungsdesigns

Gehen Sie dabei folgendermaßen vor:
1. Navigieren Sie zu **Verwaltung** > **Anwendung anpassen**.
1. Wählen Sie **Ändern** aus, um ein Bild auszuwählen, das Sie als **Anwendungslogo** hochladen möchten.
1. Wählen Sie **Ändern** aus, um ein Bild auszuwählen, das Sie als **Browsersymbol** (Bild, das auf Browsertabs angezeigt wird) hochladen möchten.
1. Sie können auch hexadezimale HTML-Farbcodes hinzufügen, um die standardmäßigen Browserfarben zu ersetzen. Verwenden Sie hierzu die Felder **Header** und **Akzent**.

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-customize-your-application.png" alt-text="Screenshot: Vorlage für vernetzte Abfallwirtschaft: Anpassen Ihrer Anwendung":::


1. Sie können auch Anwendungsbilder ändern. Wählen Sie **Verwaltung** > **Anwendungseinstellungen** > **Bild auswählen** aus, um ein Bild auszuwählen, das Sie als Anwendungsbild hochladen möchten.
1. Abschließend können Sie noch das Design ändern, indem Sie im Mastertitel der Anwendung die Option **Einstellungen** auswählen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiterverwenden möchten, sollten Sie sie mit den folgenden Schritten löschen:

1. Wählen Sie im linken Bereich Ihrer Azure IoT Central-App die Option **Verwaltung** aus.
1. Wählen Sie **Anwendungseinstellungen** > **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für den vernetzten Wasserverbrauch](./tutorial-water-consumption-monitoring.md)
