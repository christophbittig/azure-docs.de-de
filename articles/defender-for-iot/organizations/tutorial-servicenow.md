---
title: Integrieren von ServiceNow in Microsoft Defender für IoT
description: In diesem Tutorial erfahren Sie, wie Sie ServiceNow in Microsoft Defender für IoT integrieren.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 9b01aa92848bc69bff37dc75be8e59350bc9f3f3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343551"
---
# <a name="tutorial-integrate-servicenow-with-microsoft-defender-for-iot"></a>Tutorial: Integrieren von ServiceNow in Microsoft Defender für IoT

In diesem Tutorial erfahren Sie, wie Sie ServiceNow mit Microsoft Defender für IoT integrieren und verwenden.

Die Integration von Defender für IoT in ServiceNow bietet eine neue Ebene zentralisierter Sichtbarkeit, Überwachung und Kontrolle für IoT- und OT-Szenarien. Diese überbrückten Plattformen ermöglichen automatisierte Gerätesichtbarkeit und Bedrohungsverwaltung von bislang nicht erreichbaren ICS- und IoT-Geräten.

Die ServiceNow-Konfigurationsverwaltungsdatenbank (Configuration Management Database, CMDB) wird durch einen umfangreichen Satz von Geräteattributen angereichert und ergänzt, die von der Defender für IoT-Plattform gepusht werden. Damit werden umfassende und kontinuierliche Einblicke in die Gerätelandschaft sichergestellt. So können Sie über eine zentrale Benutzeroberfläche die Überwachung und Reaktion steuern. 

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Herunterladen der Defender für IoT-Anwendung in ServiceNow
> * Einrichten von Defender für IoT für die Kommunikation mit ServiceNow
> * Erstellen von Zugriffstoken in ServiceNow
> * Senden von Defender für IoT-Geräteattributen an ServiceNow
> * Einrichten der Integration mithilfe eines HTTPS-Proxys
> * Anzeigen von Defender für IoT-Erkennungen in ServiceNow
> * Anzeigen verbundener Geräte

## <a name="prerequisites"></a>Voraussetzungen

### <a name="software-requirements"></a>Softwareanforderungen

Zugriff auf ServiceNow und Defender für IoT 

- ServiceNow Service Management Version 3.0.2

- Defender für IoT, Patch 2.8.11.1 oder höher

> [!Note]
> Wenn Sie Defender für IoT bereits mit ServiceNow integriert haben und über die lokale Verwaltungskonsole ein Upgrade durchführen, sollten frühere Daten, die von Defender für IoT-Sensoren empfangen wurden, in ServiceNow gelöscht werden.

### <a name="architecture"></a>Architektur

- **Architektur der lokalen Verwaltungskonsole:** Richten Sie eine lokale Verwaltungskonsole für die Kommunikation mit einer ServiceNow-Instanz ein. Die lokale Verwaltungskonsole überträgt Sensordaten über die REST-API an die Defender für IoT-Anwendung.

    Um Ihr System zur Verwendung einer lokalen Verwaltungskonsole einzurichten, müssen Sie die ServiceNow-Synchronisierung, Weiterleitungsregeln und Proxykonfigurationen in Sensoren deaktivieren, falls sie eingerichtet wurden.

- **Sensorarchitektur:** Wenn Sie Ihre Umgebung so einrichten möchten, dass sie eine direkte Kommunikation zwischen Sensoren und ServiceNow beinhaltet, definieren Sie für jeden Sensor die ServiceNow-Synchronisierung, Weiterleitungsregeln und die Proxykonfiguration (wenn ein Proxy erforderlich ist).

## <a name="download-the-defender-for-iot-application-in-servicenow"></a>Herunterladen der Defender für IoT-Anwendung in ServiceNow

Für den Zugriff auf die Defender für IoT-Anwendung in ServiceNow müssen Sie die Anwendung aus dem ServiceNow-App-Store herunterladen. 

**So greifen Sie in ServiceNow auf die Defender für IoT-Anwendung zu**

1. Navigieren Sie zum [ServiceNow-App-Store](https://store.servicenow.com/).

1. Suchen Sie nach `Defender for IoT` oder `CyberX IoT/ICS Management`.

   :::image type="content" source="media/tutorial-servicenow/search-results.png" alt-text="Screenshot des Suchbildschirms in ServiceNow":::

1. Wählen Sie die Anwendung aus.

   :::image type="content" source="media/tutorial-servicenow/cyberx-app.png" alt-text="Screenshot des Suchbildschirms mit Ergebnissen":::

1. Wählen Sie **Request App** (App anfordern) aus.

   :::image type="content" source="media/tutorial-servicenow/sign-in.png" alt-text="Anmelden bei der Anwendung mit Ihren Anmeldeinformationen":::

1. Melden Sie sich an, und laden Sie die Anwendung herunter.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>Einrichten von Defender für IoT für die Kommunikation mit ServiceNow

Konfigurieren Sie Defender für IoT zur Übertragung von Warnungsinformationen an die ServiceNow-Tabellen. Defender für IoT-Warnungen werden in ServiceNow als Sicherheitsvorfälle angezeigt. Dazu können Sie eine Weiterleitungsregel in Defender für IoT definieren, um Warnungsinformationen an ServiceNow zu senden.

**So pushen Sie Warnungsinformationen an die ServiceNow-Tabellen**

1. Melden Sie sich bei der lokalen Verwaltungskonsole an.

1. Wählen Sie im linken Bereich die Option **Forwarding** (Weiterleitung) aus.

1. Wählen Sie die Schaltfläche :::image type="icon" source="media/tutorial-servicenow/plus-icon.png" border="false"::: aus.

    :::image type="content" source="media/tutorial-servicenow/forwarding-rule.png" alt-text="Screenshot des Fensters „Create Forwarding Rule“ (Weiterleitungsregel erstellen)":::

1. Geben Sie einen Regelnamen ein.

1. Definieren Sie die Kriterien, nach denen die Weiterleitungsregel in Defender für IoT ausgelöst wird. Durch Verwenden von Kriterien für Weiterleitungsregeln können Sie das Volumen der von Defender für IoT an ServiceNow gesendeten Informationen genau bestimmen und verwalten. Die folgenden Optionen sind verfügbar:

    - **Schweregrade:** Dies gibt den Mindestschweregrad für die Weiterleitung von Vorfällen an. Wenn z. B. **Gering** ausgewählt ist, werden Warnungen mit einem geringen Schweregrad und alle Warnungen mit höheren Schweregraden weitergeleitet. Die Schweregrade sind in Defender für IoT vordefiniert.

    - **Protokolle:** Die Weiterleitungsregel nur auslösen, wenn der erkannte Datenverkehr über bestimmte Protokolle ausgeführt wurde. Wählen Sie die gewünschten oder alle Protokolle in der Dropdownliste aus.

    - **Engines:** Wählen Sie die erforderlichen oder alle Engines aus. Warnungen von ausgewählten Engines werden gesendet.

1. Vergewissern Sie sich, dass **Report Alert Notifications** (Warnungen melden) ausgewählt ist.

1. Wählen Sie im Bereich „Aktionen“ die Option **Hinzufügen** und dann **ServiceNow** aus.

    :::image type="content" source="media/tutorial-servicenow/select-servicenow.png" alt-text="Auswählen von ServiceNow in den Dropdownoptionen":::

1. Geben Sie die ServiceNow-Aktionsparameter ein:

    :::image type="content" source="media/tutorial-servicenow/parameters.png" alt-text="Eingeben der ServiceNow-Aktionsparameter":::

1. Legen Sie im Bereich **Aktionen** die folgenden Parameter fest:

      | Parameter | BESCHREIBUNG |
      |--|--|
      | Domain | Geben Sie die IP-Adresse des ServiceNow-Servers ein. |
      | Username | Geben Sie den Benutzernamen für den ServiceNow-Server ein. |
      | Kennwort | Geben Sie das Kennwort für den ServiceNow-Server ein. |
      | Client-ID | Geben Sie die Client-ID ein, die Sie für Defender für IoT auf der Seite **Application Registries** (Anwendungsregistrierungen) in ServiceNow erhalten haben. |
      | Geheimer Clientschlüssel | Geben Sie den geheimen Clientschlüssel ein, den Sie für Defender für IoT auf der Seite **Application Registries** (Anwendungsregistrierungen) in ServiceNow erstellt haben. |
      | Berichttyp | **Vorfälle:** Weiterleiten einer Liste von Warnungen, die in ServiceNow angezeigt werden, mit der Vorfall-ID und einer kurzen Beschreibung der einzelnen Warnungen.<br /><br />**Defender für IoT-Anwendung:** Weiterleiten der vollständigen Warnungsinformationen, einschließlich Sensordetails, Engine, Quell- und Zieladresse. Die Informationen werden an Defender für IoT in der ServiceNow-Anwendung weitergeleitet. |

1. Wählen Sie **SAVE** (SPEICHERN) aus. 

Defender für IoT-Warnungen werden nun in ServiceNow als Vorfälle angezeigt.

## <a name="create-access-tokens-in-servicenow"></a>Erstellen von Zugriffstoken in ServiceNow

Damit ServiceNow mit Defender für IoT kommunizieren kann, ist ein Token erforderlich.

Sie benötigen die `Client ID` und das `Client Secret`, die Sie beim Erstellen der Defender für IoT-Weiterleitungsregeln eingegeben haben. Über die Weiterleitungsregeln werden die Warnungsinformationen an ServiceNow weitergeleitet. Außerdem benötigen Sie diese Daten beim Konfigurieren von Defender für IoT zum Pushen von Geräteattributen an ServiceNow-Tabellen.

## <a name="send-defender-for-iot-device-attributes-to-servicenow"></a>Senden von Defender für IoT-Geräteattributen an ServiceNow

Sie konfigurieren Defender für IoT zur Übertragung von umfangreichen Geräteattributen an ServiceNow-Tabellen. Zum Senden von Attributen an ServiceNow müssen Sie die lokale Verwaltungskonsole einer ServiceNow-Instanz zuordnen. Dadurch wird die Kommunikation und Authentifizierung der Defender für IoT-Plattform mit dieser Instanz sichergestellt.

**So fügen Sie eine ServiceNow-Instanz hinzu**

1. Melden Sie sich bei der lokalen Verwaltungskonsole von Defender für IoT an.

1. Wählen Sie im Bereich „Integration“ der lokalen Verwaltungskonsole **Systemeinstellungen** und dann **ServiceNow** aus.

      :::image type="content" source="media/tutorial-servicenow/servicenow.png" alt-text="Screenshot der Auswahl der Schaltfläche „ServiceNow“":::

1. Geben Sie im Dialogfeld „ServiceNow Sync“ (ServiceNow-Synchronisierung) die folgenden Synchronisierungsparameter ein.

    :::image type="content" source="media/tutorial-servicenow/sync.png" alt-text="Screenshot des Dialogfelds für die ServiceNow-Synchronisierung":::

     Parameter | Beschreibung |
    |--|--|
    | Enable Sync (Synchronisierung aktivieren) | Aktivieren und deaktivieren Sie die Synchronisierung nach dem Definieren von Parametern. |
    | Synchronisierungshäufigkeit (Minuten) | Standardmäßig werden die Informationen alle 60 Minuten an ServiceNow übermittelt. Der Mindestwert ist 5 Minuten. |
    | ServiceNow-Instanz | Geben Sie die URL der ServiceNow-Instanz ein. |
    | Client-ID | Geben Sie die Client-ID ein, die Sie für Defender für IoT auf der Seite **Application Registries** (Anwendungsregistrierungen) in ServiceNow erhalten haben. |
    | Geheimer Clientschlüssel | Geben Sie den geheimen Clientschlüssel ein, den Sie für Defender für IoT auf der Seite **Application Registries** (Anwendungsregistrierungen) in ServiceNow erstellt haben. |
    | Username | Geben Sie den Benutzernamen für die Instanz ein. |
    | Kennwort | Geben Sie das Kennwort für die Instanz ein. |

1. Wählen Sie **SAVE** (SPEICHERN) aus.

Vergewissern Sie sich, dass die lokale Verwaltungskonsole mit der ServiceNow-Instanz verbunden ist, indem Sie das Datum für Letzte Synchronisierung überprüfen.

:::image type="content" source="media/tutorial-servicenow/sync-confirmation.png" alt-text="Screenshot der Kommunikationsüberprüfung anhand der letzten Synchronisierung":::

## <a name="set-up-the-integrations-using-an-https-proxy"></a>Einrichten der Integrationen mithilfe eines HTTPS-Proxys

Beim Einrichten der Integration von Defender für IoT und ServiceNow erfolgt die Kommunikation zwischen der lokalen Verwaltungskonsole und dem ServiceNow-Server über Port 443. Wenn sich der ServiceNow-Server hinter einem Proxy befindet, können Sie nicht den Standardport verwenden.

Defender für IoT unterstützt einen HTTPS-Proxy in der ServiceNow-Integration durch die Änderung des für die Integration verwendeten Standardports.

**So konfigurieren Sie den Proxy**

1. Bearbeiten Sie die globalen Eigenschaften in der lokalen Verwaltungskonsole mithilfe des folgenden Befehls:

    ```bash
    sudo vim /var/cyberx/properties/global.properties
    ```

2. Fügen Sie die folgenden Parameter hinzu:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Wählen Sie dann **Speichern und beenden** aus.

4. Setzen Sie die lokale Verwaltungskonsole mit dem folgenden Befehl zurück: 

    ```bash
    sudo monit restart all
    ```

Nach der Festlegung der Konfiguration werden alle ServiceNow-Daten mithilfe des konfigurierten Proxys weitergeleitet.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>Anzeigen von Defender für IoT-Erkennungen in ServiceNow

In diesem Artikel werden die in ServiceNow angezeigten Geräteattribute und Warnungsinformationen beschrieben.

**So zeigen Sie Geräteattribute an**

1. Melden Sie sich bei ServiceNow an.

2. Navigieren Sie zu **CyberX Platform** (CyberX-Plattform).

3. Navigieren Sie zu **Inventory** (Bestand) oder **Alert** (Warnung).

   [:::image type="content" source="media/tutorial-servicenow/alert-list.png" alt-text="Screenshot des Inventars oder der Warnung":::](media/tutorial-servicenow/alert-list.png#lightbox)

## <a name="view-connected-devices"></a>Anzeigen verbundener Geräte

So zeigen Sie verbundene Geräte an

1. Wählen Sie ein Gerät und dann die für das Gerät aufgelistete **Appliance** aus.

    :::image type="content" source="media/tutorial-servicenow/appliance.png" alt-text="Screenshot der Auswahl der gewünschten Appliance in der Liste":::

1. Wählen Sie im Dialogfeld **Device Details** (Gerätedetails) die Option **Connected Devices** (Verbundene Geräte) aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es müssen keine Ressourcen bereinigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die ersten Schritte bei der Integration von ServiceNow kennengelernt. Informieren Sie sich nun über die [Cisco-Integration](./tutorial-forescout.md).

> [!div class="nextstepaction"]
> [Schaltfläche „Nächste Schritte“](./tutorial-forescout.md)
