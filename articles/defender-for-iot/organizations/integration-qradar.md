---
title: QRadar-Integration
description: Konfigurieren Sie die Integration der Defender für IoT-Lösung mit QRadar.
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 26808482ad9fabd528d2ec7e0d846c301c2c2ebf
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341787"
---
# <a name="about-the-qradar-integration"></a>Informationen zur QRadar-Integration

Defender für IoT ist die einzige von Blue Team-Experten entwickelte ICS- und IoT-Plattform für Cybersicherheit mit einer Erfolgsbilanz für den Schutz kritischer nationaler Infrastrukturen und die einzige Plattform mit patentierter ICS-fähiger Bedrohungsanalyse und maschinellem Lernen.

Defender für IoT hat seine kontinuierliche ICS-Plattform zur Bedrohungsüberwachung in IBM QRadar integriert. 

Dies sind einige Vorteile der Integration:

- Die Möglichkeit, Azure Defender für IoT-Warnungen an IBM QRadar weiterzuleiten, um für eine einheitliche IT- und OT-Sicherheitsüberwachung und -Governance zu sorgen.

- Die Möglichkeit, sowohl IT- als auch OT-Umgebungen aus der Vogelperspektive zu betrachten. Dadurch können Sie mehrstufige Angriffe, die oft IT- und OT-Grenzen überschreiten, erkennen und darauf reagieren.

- Integration in vorhandene SOC-Workflows.

## <a name="configuring-syslog-listener-for-qradar"></a>Konfigurieren des Syslog-Listeners für QRadar

So konfigurieren Sie den Syslog-Listener für den Einsatz bei QRadar:

1. Melden Sie sich bei QRadar an.

1. Wählen Sie im linken Bereich **Administrator** > **Datenquellen** aus.

   [:::image type="content" source="media/integration-qradar/log.png" alt-text="Wählen Sie aus den verfügbaren Optionen die gewünschten Protokollquellen aus.":::](media/integration-qradar/log.png#lightbox)

1. Wählen Sie im Fenster „Datenquellen“ die Option **Protokollquellen** aus.

   [:::image type="content" source="media/integration-qradar/modal.png" alt-text="Nachdem Sie „Syslog“ ausgewählt haben, wird das modale Fenster geöffnet.":::](media/integration-qradar/modal.png#lightbox)

1. Wählen Sie im Fenster **Modal** die Option **Hinzufügen** aus.

   :::image type="content" source="media/integration-qradar/source.png" alt-text="Fügen Sie eine Protokollquelle hinzu, indem Sie die entsprechenden Felder ausfüllen.":::

1. Legen Sie im Dialogfeld **Add a log source** (Protokollquelle hinzufügen) die folgenden Parameter fest:

   - **Log Source Name** (Protokollquellenname): `<XSense Name>`
   
   - **Log Source Description** (Protokollquellenbeschreibung): `<XSense Name>`
   
   - **Log Source Type** (Protokollquellentyp): `Universal LEEF`

   - **Protocol Configuration** (Protokollkonfiguration): `Syslog`
   
   - **Log Source Identifier** (Protokollquellenbezeichner): `<XSenseName>`
   
   > [!NOTE]
   > Der Name des Protokollquellenbezeichners darf kein Leerzeichen enthalten. Es wird empfohlen, jedes Leerzeichen durch einen Unterstrich zu ersetzen.

1. Wählen Sie **Speichern** aus.

1. Wählen Sie **Deploy Changes** (Änderungen bereitstellen) aus.

:::image type="content" source="media/integration-qradar/deploy.png" alt-text="Screenshot: Ansicht „Deploy Changes“ (Änderungen bereitstellen)":::

## <a name="deploying-defender-for-iot-platform-qid"></a>Bereitstellen der Defender für IoT-Plattform-QID

QID ist ein Ereignisbezeichner in QRadar. Alle Defenders für IoT-Plattform-Berichte werden unter demselben Ereignis (XSense-Warnung) gekennzeichnet.

**So stellen Sie Xsense QID bereit**:

1. Melden Sie sich an der QRadar-Konsole an.

1. Erstellen Sie eine Datei mit dem Namen `xsense_qids`.

1. Verwenden Sie in der Datei den folgenden Befehl: `,XSense Alert,XSense Alert Report From <XSense Name>,5,7001`.

1. Führen Sie aus: `sudo /opt/qradar/bin/qidmap_cli.sh -i -f <path>/xsense_qids`. Daraufhin wird die Meldung angezeigt, dass die QID erfolgreich bereitgestellt wurde.

## <a name="setting-up-qradar-forwarding-rules"></a>Einrichten von QRadar-Weiterleitungsregeln

Konfigurieren Sie in der Defender für IoT-Appliance eine QRadar-Weiterleitungsregel. Ordnen Sie die Regel auf der lokalen Verwaltungskonsole zu.

**So definieren Sie QRadar-Benachrichtigungen in der Defender für IoT-Appliance**:

1. Wählen Sie im seitlichen Menü **Weiterleitung** aus.

   :::image type="content" source="media/integration-qradar/create.png" alt-text="Erstellen einer Weiterleitungsregel":::

1. Legen Sie die Aktion auf **QRadar** fest.

1. Konfigurieren Sie die IP-Adresse für QRadar und die Zeitzone.

1. Klicken Sie auf **Senden**.

**So ordnen Sie Benachrichtigungen an QRadar im Central Manager zu**:

1. Wählen Sie im seitlichen Menü **Weiterleitung** aus.

1. Wählen Sie in der grafischen Benutzeroberfläche für QRadar unter „QRadar“ die Option **Protokollaktivität** aus.

1. Wählen Sie **Filter hinzufügen** aus, und legen Sie die folgenden Parameter fest:
   - Parameter: `Log Sources [Indexed]`
   - Operator: `Equals`
   - Protokollquellengruppe: `Other`
   - Protokollquelle: `<Xsense Name>`

1. Doppelklicken Sie in XSense auf einen unbekannten Bericht.

1. Wählen Sie **Map Event** (Ereignis zuordnen) aus.

1. Wählen Sie auf der Seite „Modales Protokollquellenereignis“ Folgendes aus:
   - „High-Level Kategorie“ – „Verdächtige Aktivität“ + „Low-Level Kategorie“ – „Unbekanntes verdächtiges Ereignis“ + „Protokoll“
   - „Quelltyp“ – „beliebig“

1. Klicken Sie auf **Suchen**.

1. Wählen Sie in den Ergebnissen die Zeile mit dem Namen „XSense“ und dann **OK** aus.

Von jetzt an werden alle XSense-Berichte als „XSense-Warnungen“ gekennzeichnet.

## <a name="adding-custom-fields-to-alerts"></a>Hinzufügen von benutzerdefinierten Feldern zu Warnungen

**So fügen Sie benutzerdefinierten Feldern Warnungen hinzu**:

1. Wählen Sie **Extract Property** (Eigenschaft extrahieren) aus.

1. Wählen Sie **RegEx Based** (RegEx-basiert) aus.

1. Konfigurieren Sie die folgenden Felder:
   - „Neue Eigenschaft“: _Wählen Sie aus der folgenden Liste aus_
      - Xsense Alert Description (Xsense-Warnungsbeschreibung)
      - Xsense Alert ID (Xsense-Warnungs-ID)
      - Xsense Alert Score (Xsense-Warnungsbewertung)
      - Xsense Alert Title (Xsense-Warnungstitel)
      - Xsense Destination Name (Xsense-Zielname)
      - Xsense Direct Redirect RegEx (Xsense Weiterleiten Umleiten)
      - Xsense Sender IP (IP-Adresse des Xsense-Absenders)
      - Xsense Sender Name (Xsense-Absendername)
      - Xsense Alert Engine (Xsense-Warnungsengine)
      - Xsense Source Device Name (Xsense-Quellgerätename)
   - Aktivieren Sie **Optimize Parsing** (Analyse optimieren).
   - Feldtyp: `AlphaNumeric`
   - Aktivieren Sie **Aktiviert**.
   - Protokollquellentyp:`Universal LEAF`
   - Protokollquelle: `<Xsense Name>`
   - Ereignisname (sollte bereits als „XSense-Warnung“ festgelegt sein)
   - Erfassungsgruppe: 1
   - RegEx:
      - Xsense Alert Description RegEx (Xsense-Warnungsbeschreibung RegEx): `msg=(.*)(?=\t)`
      - Xsense Alert ID RegEx (Xsense-Warnungs-ID RegEx): `alertId=(.*)(?=\t)`
      - Xsense Alert Score RegEx (Xsense-Warnungsbewertung RegEx): `Detected score=(.*)(?=\t)`
      - Xsense Alert Title RegEx (Xsense-Warnungstitel RegEx): `title=(.*)(?=\t)`
      - Xsense Destination Name RegEx (Xsense-Zielname RegEx): `dstName=(.*)(?=\t)`
      - Xsense Direct Redirect RegEx (Xsense Weiterleiten Umleiten RegEx): `rta=(.*)(?=\t)`
      - Xsense Sender IP RegEx (IP-Adresse des Xsense-Absenders RegEx): `reporter=(.*)(?=\t)`
      - Xsense Sender Name RegEx (Xsense-Absendername RegEx): `senderName=(.*)(?=\t)`
      - Xsense Alert Engine RegEx (Xsense-Warnungsengine RegEx): `engine =(.*)(?=\t)`
      - Xsense Source Device Name RegEx (Xsense-Quellgerätename RegEx): `src`

## <a name="defining-defender-for-iot-appliance-name"></a>Definieren des Namens der Defender für IoT-Appliance

Sie können den Namen der Plattform jederzeit ändern.

Beim Erstellen von Standorten und Zuweisen von Appliances zu Zonen in der lokalen Verwaltungskonsole sollten Sie jeder Appliance einen aussagekräftigen Namen zuweisen. Beispielsweise bedeutet „Motorräder-FL Einheit 2“, dass diese Appliance die Einheit Nr. 2 in der Fertigungslinie „Motorräder“ schützt. 

Es ist wichtig, dass Sie einen aussagekräftigen Namen für Ihre Appliance auswählen, weil dieser Name an die Protokolle übergeben wird. Bei der Überprüfung von Protokollen wird jeder Warnung ein Sensor angefügt. Anhand des Sensornamens können Sie identifizieren, welcher Sensor mit der jeweiligen Warnung in Zusammenhang steht.

**So ändern Sie den Appliancenamen**:

1. Wählen Sie im seitlichen Menü den aktuellen Appliancenamen aus. Daraufhin wird das Dialogfeld **Konfiguration der Verwaltungskonsole bearbeiten** angezeigt.

   :::image type="content" source="media/integration-qradar/edit-management-console.png" alt-text="Ändern Sie den Namen Ihrer Konsole.":::

1. Geben Sie im Feld „Name“ einen Namen ein, und wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Warnungsinformationen weiterleiten](how-to-forward-alert-information-to-partners.md).
