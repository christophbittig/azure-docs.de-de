---
title: Qradar mit Azure Defender für loT integrieren
description: In diesem Tutorial lernen Sie, wie Sie Qradar in Azure Defender for loT integrieren können.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/12/2021
ms.custom: template-tutorial
ms.openlocfilehash: ec55a652b59f7d45e01ef22e62bd046473ef8559
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124839048"
---
# <a name="tutorial-integrate-qradar-with-azure-defender-for-iot"></a>Tutorial: Qradar mit Azure Defender für loT integrieren

In diesem Tutorial lernen Sie, wie Sie QRadar mit Azure Defender for loT integrieren und nutzen können.

Defender for IoT ist die einzige ICS- und IoT-Cybersicherheitsplattform mit patentierter ICS-sensitiver Bedrohungsanalyse und maschinellem Lernen.

Defender für IoT hat seine kontinuierliche ICS-Plattform zur Bedrohungsüberwachung in IBM QRadar integriert.

Dies sind einige Vorteile der Integration:

- Die Möglichkeit, Azure Defender für IoT-Warnungen an IBM QRadar weiterzuleiten, um für eine einheitliche IT- und OT-Sicherheitsüberwachung und -Governance zu sorgen.

- Die Möglichkeit, sich einen Überblick über IT- und OT-Umgebungen zu verschaffen. Ermöglicht die Erkennung von und Reaktion auf mehrstufige Angriffe, die häufig IT- und OT-Grenzen überschreiten.

- Integration in vorhandene SOC-Workflows.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren von Syslog-Listener für QRadar
> * Bereitstellung von Defender für die LoT-Plattform QID
> * Einrichten von QRadar-Weiterleitungsregeln
> * Benachrichtigungen auf QRadar in der Management-Konsole zuordnen
> * Hinzufügen von benutzerdefinierten Feldern zu Warnungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Es gibt keine Voraussetzungen für dieses Tutorial.

## <a name="configure-syslog-listener-for-qradar"></a>Konfigurieren von Syslog-Listener für QRadar

**Konfigurieren von Syslog-Listener für die Zusammenarbeit mit QRadar**:

1. Melden Sie sich bei QRadar an.

1. Wählen Sie im linken Bereich **Administrator** > **Datenquellen** aus.

1. Wählen Sie im Fenster „Datenquellen“ die Option **Protokollquellen** aus.

   [:::image type="content" source="media/tutorial-qradar/log.png" alt-text="Screenshot vom Auswählen einer Protokollquelle aus den verfügbaren Optionen.":::](media/tutorial-qradar/log.png#lightbox)

1. Wählen Sie im Fenster **Modal** die Option **Hinzufügen** aus.

    [:::image type="content" source="media/tutorial-qradar/modal.png" alt-text="Screenshot vom Öffnen des modalen Fensters nach Auswählen von Syslog.":::](media/tutorial-qradar/modal.png#lightbox)

1. Legen Sie im Dialogfeld **Add a log source** (Protokollquelle hinzufügen) die folgenden Parameter fest:

    :::image type="content" source="media/tutorial-qradar/source.png" alt-text="Screenshot vom Hinzufügen einer Protokollquelle durch Ausfüllen der entsprechenden Felder.":::

   - **Log Source Name** (Protokollquellenname): `<Sensor name>`

   - **Log Source Description** (Protokollquellenbeschreibung): `<Sensor name>`

   - **Log Source Type** (Protokollquellentyp): `Universal LEEF`

   - **Protocol Configuration** (Protokollkonfiguration): `Syslog`

   - **Log Source Identifier** (Protokollquellenbezeichner): `<Sensor name>`

   > [!NOTE]
   > Der Name des Protokollquellenbezeichners darf kein Leerzeichen enthalten. Es wird empfohlen, jedes Leerzeichen durch einen Unterstrich zu ersetzen.

1. Wählen Sie **Speichern** aus.

1. Wählen Sie **Deploy Changes** (Änderungen bereitstellen) aus.

   :::image type="content" source="media/tutorial-qradar/deploy.png" alt-text="Screenshot: Ansicht „Deploy Changes“ (Änderungen bereitstellen)":::

## <a name="deploy-defender-for-iot-platform-qid"></a>Bereitstellung von Defender für die LoT-Plattform QID

QID ist ein Ereignisbezeichner in QRadar. Alle Berichte des Defenders für die loT-Plattform sind unter demselben Ereignis (Sensor-Alarm) markiert.

**Um Defender for loT-Plattform QID bereitzustellen**:

1. Melden Sie sich an der QRadar-Konsole an.

1. Erstellen Sie eine Datei mit dem Namen `xsense_qids`.

1. Verwenden Sie in der Datei den folgenden Befehl: `,XSense Alert,XSense Alert Report From <XSense Name>,5,7001`.

1. Führen Sie aus: `sudo /opt/qradar/bin/qidmap_cli.sh -i -f <path>/xsense_qids`. Daraufhin wird die Meldung angezeigt, dass die QID erfolgreich bereitgestellt wurde.

## <a name="setup-qradar-forwarding-rules"></a>Einrichten von QRadar-Weiterleitungsregeln

Damit die Integration funktioniert, müssen Sie in der Defender for loT-Appliance eine Qradar-Weiterleitungsregel einrichten.

**So definieren Sie QRadar-Benachrichtigungen in der Defender für IoT-Appliance**:

1. Wählen Sie im seitlichen Menü **Weiterleitung** aus.

1. Wählen Sie **Weiterleitungsregel erstellen** aus.

1. Legen Sie die Aktion auf **QRadar** fest.

    :::image type="content" source="media/tutorial-qradar/create.png" alt-text="Screenshot des Fensters Weiterleitungsregel erstellen.":::

1. Konfigurieren Sie die IP-Adresse für QRadar und die Zeitzone.

1. Klicken Sie auf **Senden**.

## <a name="map-notifications-to-qradar"></a>Zuordnen von Benachrichtigungen zu QRadar

Die Regel muss dann auf der lokalen Verwaltungskonsole zugeordnet werden.

**Um die Benachrichtigungen zu QRadar zuzuordnen**:

1. Melden Sie sich bei der Verwaltungskonsole an.

1. Wählen Sie im linken Seitenbereich **Weiterleitung**.

1. Wählen Sie in der grafischen Benutzeroberfläche für QRadar unter „QRadar“ die Option **Protokollaktivität** aus.

1. Wählen Sie **Filter hinzufügen** aus, und legen Sie die folgenden Parameter fest:
   - Parameter: `Log Sources [Indexed]`
   - Operator: `Equals`
   - Protokollquellengruppe: `Other`
   - Protokollquelle: `<Xsense Name>`

1. Doppelklicken Sie auf einen unbekannten Bericht des Sensors.

1. Wählen Sie **Map Event** (Ereignis zuordnen) aus.

1. Wählen Sie auf der Seite „Modales Protokollquellenereignis“ Folgendes aus:
   - „High-Level Kategorie“ – „Verdächtige Aktivität“ + „Low-Level Kategorie“ – „Unbekanntes verdächtiges Ereignis“ + „Protokoll“
   - „Quelltyp“ – „beliebig“

1. Klicken Sie auf **Suchen**.

1. Wählen Sie in den Ergebnissen die Zeile mit dem Namen „XSense“ und dann **OK** aus.

Alle Sensorberichte sind von nun an als Sensorwarnungen markiert.

## <a name="add-custom-fields-to-the-alerts"></a>Fügen Sie benutzerdefinierte Felder zu den Warnungen hinzu

**So fügen Sie benutzerdefinierten Feldern Warnungen hinzu**:

1. Wählen Sie **Extract Property** (Eigenschaft extrahieren) aus.

1. Wählen Sie **RegEx Based** (RegEx-basiert) aus.

1. Konfigurieren Sie die folgenden Felder:
   - „Neue Eigenschaft“: _Wählen Sie aus der folgenden Liste aus_
      - Beschreibung der Sensorwarnung
      - Sensorwarnung-ID
      - Sensorwarnung-Bewertung
      - Sensorwarnung-Titel
      - Sensorziel-Name
      - Sensor Direkte Umleitung
      - Sensor Absender IP
      - Sensor Absender Name
      - Sensorwarnung-Engine
      - Sensor Quellgerät Name
   - Aktivieren Sie **Optimize Parsing** (Analyse optimieren).
   - Feldtyp: `AlphaNumeric`
   - Aktivieren Sie **Aktiviert**.
   - Protokollquellentyp:`Universal LEAF`
   - Protokollquelle: `<Sensor Name>`
   - Ereignisname (sollte bereits als Sensorwarnung eingestellt sein)
   - Erfassungsgruppe: 1
   - RegEx:
      - Sensorwarnung Beschreibung RegEx: `msg=(.*)(?=\t)`
      - Sensorwarnung-ID RegEx: `alertId=(.*)(?=\t)`
      - Sensorwarnung-Bewertung RegEx: `Detected score=(.*)(?=\t)`
      - Sensorwarnung Titel RegEx: `title=(.*)(?=\t)`
      - Sensorziel-Name RegEx: `dstName=(.*)(?=\t)`
      - Sensor Direkte Umleitung RegEx: `rta=(.*)(?=\t)`
      - Sensor Absender IP: RegEx: `reporter=(.*)(?=\t)`
      - Sensor Absender Name RegEx: `senderName=(.*)(?=\t)`
      - Sensor-Alarmierungsmodul RegEx: `engine =(.*)(?=\t)`
      - Sensor Quellgerät Name RegEx: `src`

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es müssen keine Ressourcen bereinigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie mit der QRadar-Integration beginnen können. Fahren Sie mit dem folgenden Tutorial fort, um zu erfahren, wie Sie [ServiceNow mit Azure Defender für IoT integrieren](tutorial-servicenow.md):

> [!div class="nextstepaction"]
> [Integrieren von ServiceNow mit Azure Defender für IoT](tutorial-servicenow.md)
