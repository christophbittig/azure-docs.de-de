---
title: Behandeln von Problemen im ITSM-Connector
description: Hier erfahren Sie, wie Sie allgemeine Probleme im ITSM-Connector beheben.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: d6e41cd80e27fe673d04592c61e590c09645619e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430358"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>Behandeln von Problemen im ITSM-Connector

Dieser Artikel enthält Informationen zu allgemeinen Problemen im ITSM-Connector sowie zu deren Behandlung.

Azure Monitor informiert Sie proaktiv mithilfe von Warnungen, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Diese Warnungen ermöglichen es Ihnen, Probleme zu identifizieren und zu behandeln, bevor sie von Benutzern Ihres Systems bemerkt werden.

Sie können auswählen, wie Sie Warnungen erhalten möchten. Dazu können Sie zwischen E-Mail, SMS und Webhook wählen oder sogar eine Lösung automatisieren. 

Alternativ können Sie sich auch über den ITSM-Connector benachrichtigen lassen. Mit dem ITSM-Connector können die Warnungen an ein externes Ticketsystem wie ServiceNow gesendet werden.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>Analysieren von Incident- und Änderungsanforderungsdaten mithilfe des Dashboards

Basierend auf Ihrer Konfiguration, die Sie beim Einrichten einer Verbindung vorgenommenen haben, kann der ITSM-Connector bis zu 120 Tage an Incident- und Änderungsanforderungsdaten synchronisieren. Informationen zum Ermitteln des Protokolldatensatzschemas für diese Daten finden Sie im Artikel [Aus dem ITSM-Produkt synchronisierte Daten](./itsmc-synced-data.md).

Sie können die Incident- und die Änderungsanforderungsdaten über das Dashboard des ITSM-Connectors visualisieren:

![Screenshot des Dashboards des ITSM-Connectors](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Das Dashboard enthält auch Informationen zum Connectorstatus. Diese Informationen können als Ausgangspunkt für die Analyse von Verbindungsproblemen verwenden. Weitere Informationen finden Sie unter [Untersuchung von Fehlern auf dem Dashboard](./itsmc-dashboard.md).

## <a name="use-service-map-to-visualize-incidents"></a>Visualisieren von Vorfällen mithilfe der Dienstzuordnung

Zudem lassen sich die mit den betroffenen Computern synchronisierten Incidents innerhalb der Dienstzuordnungslösung visuell darstellen.

Service Map ermittelt automatisch die Anwendungskomponenten auf Windows- und Linux-Systemen und stellt die Kommunikation zwischen Diensten dar. In dieser Lösung können Sie die Server nach ihrer Funktion anzeigen: als verbundene Systeme, die wichtige Dienste bereitstellen. 

Die Dienstzuordnungslösung zeigt Verbindungen zwischen Servern, Prozessen und Ports über die gesamte TCP-Verbindungsarchitektur an. Außer der Installation eines Agents ist keine Konfiguration erforderlich. Weitere Informationen finden Sie unter [Verwenden der Dienstzuordnung](../vm/service-map.md).

Mithilfe der Dienstzuordnung können Sie die in ITSM-Lösungen erstellten Service Desk-Elemente wie im folgenden Beispiel anzeigen:

![Screenshot des Log Analytics-Bildschirms](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>Lösen von Problemen

In den folgenden Abschnitten werden allgemeine Symptome, mögliche Ursachen und Lösungen beschrieben. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>Fehler bei einer Verbindung mit dem ITSM-System und der Meldung „Fehler beim Speichern der Verbindung“

**Ursache:** Es kann eine der folgenden Ursachen vorliegen:

* Die Anmeldeinformationen sind nicht korrekt.
* Die Berechtigungen sind unzureichend.
* Für Service Manager-Verbindungen: Die Web-App wurde nicht ordnungsgemäß bereitgestellt.

**Lösung:**

* Bei ServiceNow-, Cherwell- und Provance-Verbindungen:
  * Vergewissern Sie sich, dass Sie Benutzername, Kennwort, Client-ID und geheimen Clientschlüssel für jede der Verbindungen richtig eingegeben haben.  
  * Stellen Sie für ServiceNow sicher, dass Sie im entsprechenden ITSM-Produkt über [ausreichende Berechtigungen](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) verfügen.

* Bei Service Manager-Verbindungen:  
  * Stellen Sie sicher, dass die Web-App erfolgreich bereitgestellt und die Hybridverbindung erstellt wird. Navigieren Sie zur Web-App-URL, wie in der [Dokumentation zum Herstellen einer Hybridverbindung](./itsmc-connections-scsm.md#configure-the-hybrid-connection) erläutert, um zu überprüfen, ob die Verbindung mit dem lokalen Service Manager-Computer erfolgreich hergestellt wird.  

### <a name="duplicate-work-items-are-created"></a>Doppelte Arbeitselemente werden erstellt.

**Ursache:** Es kann eine der beiden folgenden Ursachen vorliegen:

* Für die Warnung sind mehrere ITSM-Aktionen definiert.
* Die Warnung wurde behandelt.

**Lösung:** Es gibt zwei mögliche Lösungen:

* Stellen Sie sicher, dass pro Warnung nur eine einzige ITSM-Aktionsgruppe vorhanden ist.
* Vom ITSM-Connector werden keine Statusaktualisierungen bei übereinstimmenden Arbeitselementen unterstützt, wenn eine Warnung aufgelöst wurde. Erstellen Sie ein neues behandeltes Arbeitselement.

### <a name="work-items-are-not-created"></a>Es werden keine Arbeitselemente erstellt.

**Ursache:** Für dieses Symptom kann es eine Reihe von Gründen geben:

* Der Code wurde aufseiten von ServiceNow-Seite geändert.
* Die Berechtigungen sind nicht korrekt konfiguriert.
* Die ServiceNow-Ratenbegrenzungen sind zu hoch oder zu niedrig.
* Ein Aktualisierungstoken ist abgelaufen.
* Der ITSM-Connector wurde gelöscht.

**Lösung:** Überprüfen Sie auf dem [Dashboard](itsmc-dashboard.md) die Fehler im Abschnitt für den Connectorstatus. Überprüfen Sie anschließend die [allgemeinen Fehler und deren Lösungen](itsmc-dashboard-errors.md).

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>Erstellen einer ITSM-Aktion für eine Aktionsgruppe nicht möglich

**Ursache:** Für eine neu erstellte ITSM-Connectorinstanz muss die Erstsynchronisierung noch abgeschlossen werden.

**Lösung:** Überprüfen Sie die [allgemeinen Fehler und deren Lösungen](itsmc-dashboard-errors.md).

### <a name="sync-connection"></a>Synchronisieren der Verbindung 

**Ursache:** Für dieses Symptom kann es eine Reihe von Gründen geben:

* Vorlagen werden nicht als Teil des Dropdownmenüs für Aktionsdefinitionen angezeigt, und eine Fehlermeldung wird eingeblendet: „Die Vorlagenkonfiguration kann nicht abgerufen werden. Weitere Informationen finden Sie in den Connectorprotokollen.“
* Werte werden nicht in den Dropdownlisten der Standardfelder als Teil der Aktionsdefinition angezeigt, und eine Fehlermeldung wird eingeblendet: „Für die folgenden Felder wurden keine Werte gefunden: \<field names\>.“
* Incidents/Ereignisse werden nicht in ServiceNow erstellt.

**Lösung**: 
* [Synchronisieren Sie den Connector](itsmc-resync-servicenow.md).
* Überprüfen Sie auf dem [Dashboard](itsmc-dashboard.md) die Fehler im Abschnitt für den Connectorstatus. Überprüfen Sie anschließend die [allgemeinen Fehler und deren Lösungen](itsmc-dashboard-errors.md).
