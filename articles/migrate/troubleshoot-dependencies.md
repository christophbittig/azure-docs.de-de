---
title: Behandeln von Problemen mit der Abhängigkeitsanalyse mit oder ohne Agent
description: Unterstützung bei der Abhängigkeitsvisualisierung in Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 671820e4c3ca3a4b8069f6be6d2a884a97da0739
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535633"
---
# <a name="troubleshoot-dependency-visualization"></a>Behandeln von Problemen bei der Abhängigkeitsvisualisierung

Dieser Artikel hilft Ihnen bei der Behandlung von Problemen bei der Abhängigkeitsanalyse mit und ohne Agent, die _nur für VMware-Server verfügbar_ ist. [Erfahren Sie mehr](concepts-dependency-visualization.md) über die Typen der Abhängigkeitsvisualisierung, die in Azure Migrate unterstützt werden.

## <a name="visualize-dependencies-for-1-hour-with-agentless-dependency-analysis"></a>Visualisieren von Abhängigkeiten für mehr als eine Stunde mit Abhängigkeitsanalyse ohne Agent

Mit der Abhängigkeitsanalyse ohne Agent können Sie Abhängigkeiten für eine Dauer von bis zu 30 Tagen visualisieren oder in eine Karte exportieren.

## <a name="visualize-dependencies-for-10-servers-with-agentless-dependency-analysis"></a>Visualisieren von Abhängigkeiten für mehr als zehn Server mit Abhängigkeitsanalyse ohne Agent

Azure Migrate verfügt über eine Power BI-Vorlage, die Sie zum gleichzeitigen Visualisieren der Netzwerkverbindungen vieler Server und zum Filtern nach Prozess und Server verwenden können. [Hier](how-to-create-group-machine-dependencies-agentless.md#visualize-network-connections-in-power-bi) erfahren Sie mehr darüber, wie Sie die Abhängigkeiten für viele Server gemeinsam visualisieren.

## <a name="dependencies-export-csv-shows-unknown-process-with-agentless-dependency-analysis"></a>Export-CSV für Abhängigkeiten zeigt „Unbekannter Prozess“ mit Abhängigkeitsanalyse ohne Agent
Bei der Abhängigkeitsanalyse ohne Agent werden die Prozessnamen auf Grundlage der besten Leistung aufgezeichnet. In bestimmten Szenarien ist es nicht möglich, die Prozessnamen an beiden Enden der Abhängigkeit zu ermitteln, obwohl der Quell- und Zielservername sowie der Zielport aufgezeichnet werden. In solchen Fällen wird der Prozess als „Unbekannter Prozess“ gekennzeichnet.

## <a name="unable-to-export-dependency-data-in-a-csv-due-to-the-error-403-this-request-is-not-authorized-to-perform-this-operation"></a>Abhängigkeitsdaten können aufgrund des Fehlers „403: Diese Anforderung ist zum Durchführen dieses Vorgangs nicht autorisiert.“ nicht in eine CSV-Datei exportiert werden.
Wenn Ihr Azure Migrate-Projekt über Konnektivität mit privaten Endpunkten verfügt, sollte die Anforderung zum Exportieren von Abhängigkeitsdaten von einem Client initiiert werden, der über ein privates Netzwerk mit dem virtuellen Azure-Netzwerk verbunden ist. Öffnen Sie zum Beheben dieses Fehlers das Azure-Portal in Ihrem lokalen Netzwerk oder auf dem Applianceserver, und versuchen Sie den Export erneut.

## <a name="common-agentless-dependency-analysis-errors"></a>Häufige Fehler bei der Abhängigkeitsanalyse ohne Agent

Azure Migrate unterstützt die Abhängigkeitsanalyse ohne Agent mithilfe von „Azure Migrate: Ermittlung und Bewertung“. Die Abhängigkeitsanalyse ohne Agent wird derzeit nur für VMware unterstützt. [Erfahren Sie mehr](how-to-create-group-machine-dependencies-agentless.md) über die Anforderungen für Abhängigkeitsanalyse ohne Agent.

Die Liste mit Fehlern bei der Abhängigkeitsanalyse ohne Agent wird in der unten stehenden Tabelle zusammengefasst.

> [!Note]
> Die gleichen Fehler können auch bei der Softwareinventur auftreten, da sie der gleichen Methodik wie die Abhängigkeitsanalyse ohne Agent folgt, um die erforderlichen Daten zu erfassen.

| **Fehler** | **Ursache** | **Aktion** |
|--|--|--|
| **9000:** Der Status der VMware-Tools auf dem Server kann nicht erkannt werden. | VMware Tools sind möglicherweise nicht auf dem Server installiert, oder die installierte Version ist beschädigt. | Stellen Sie sicher, dass eine höhere Version als 10.2.1 der VMware-Tools auf dem Server installiert ist und ausgeführt wird. |
| **9001:** VMware-Tools sind nicht auf dem System installiert. | VMware Tools sind möglicherweise nicht auf dem Server installiert, oder die installierte Version ist beschädigt. | Stellen Sie sicher, dass eine höhere Version als 10.2.1 der VMware-Tools auf dem Server installiert ist und ausgeführt wird. |
| **9002:** VMware Tools werden nicht auf dem Server ausgeführt. | VMware Tools sind möglicherweise nicht auf dem Server installiert, oder die installierte Version ist beschädigt. | Stellen Sie sicher, dass eine höhere Version als 10.2.0 der VMware-Tools auf dem Server installiert ist und ausgeführt wird. |
| **9003:** Der auf dem Server ausgeführte Betriebssystemtyp wird nicht unterstützt. | Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux. | Nur Windows- und Linux-Betriebssystemtypen werden unterstützt. Wenn der Server tatsächlich Windows oder Linux verwendet, überprüfen Sie den in vCenter Server angegebenen Betriebssystemtyp. |
| **9004:** Der Server weist keinen Ausführungszustand auf. | Der Server ist ausgeschaltet. | Stellen Sie sicher, dass der Server ausgeführt wird. |
| **9005:** Der auf dem Server ausgeführte Betriebssystemtyp wird nicht unterstützt. | Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux. | Nur Windows- und Linux-Betriebssystemtypen werden unterstützt. Das Betriebssystem \<FetchedParameter> wird derzeit nicht unterstützt. |
| **9006**: Die URL, die zum Herunterladen der Ermittlungsmetadatendatei vom Server erforderlich ist, ist leer. | Dies kann ein vorübergehendes Problem sein, da der Ermittlungs-Agent auf der Appliance nicht wie erwartet funktioniert. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9007:** Der Prozess, mit dem das Skript zum Erfassen der Metadaten ausgeführt wird, wurde auf dem Server nicht gefunden. | Dies kann ein vorübergehendes Problem sein, da der Ermittlungs-Agent auf der Appliance nicht wie erwartet funktioniert. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9008:** Der Status des Prozesses, der zum Erfassen der Metadaten auf dem Server ausgeführt wird, kann nicht abgerufen werden. | Dies kann ein vorübergehendes Problem aufgrund eines internen Fehlers sein. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9009:** Die Windows-Benutzerkontensteuerung (User Account Control, UAC) verhindert die Ausführung von Ermittlungsvorgängen auf dem Server. | Die Einstellungen für die Windows-Benutzerkontensteuerung verhindern die Ermittlung installierter Anwendungen auf dem Server. | Verringern Sie auf dem beeinträchtigten Server die Ebene der Einstellungen für die **Benutzerkontensteuerung** in der Systemsteuerung. |
| **9010:** Der Server ist ausgeschaltet. | Der Server ist ausgeschaltet. | Stellen Sie sicher, dass der Server eingeschaltet ist. |
| **9011:** Die Datei mit den ermittelten Metadaten kann auf dem Server nicht gefunden werden. | Dies kann ein vorübergehendes Problem aufgrund eines internen Fehlers sein. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9012:** Die Datei mit den ermittelten Metadaten auf dem Server ist leer. | Dies kann ein vorübergehendes Problem aufgrund eines internen Fehlers sein. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9013:** Bei jeder Anmeldung beim Server wird ein neues temporäres Benutzerprofil erstellt. | Bei jeder Anmeldung beim Server wird ein neues temporäres Benutzerprofil erstellt. | Senden Sie eine Supportanfrage an Microsoft, um dieses Problem zu beheben. |
| **9014:** Die Datei mit den ermittelten Metadaten kann aufgrund eines Fehlers auf dem ESXi-Host nicht abgerufen werden. Fehlercode: %ErrorCode; Details: %ErrorMessage | Auf dem ESXi-Host \<HostName> ist ein Fehler aufgetreten. Fehlercode: %ErrorCode; Details: %ErrorMessage | Stellen Sie sicher, dass Port 443 auf dem ESXi-Host geöffnet ist, auf dem der Server ausgeführt wird.<br/><br/> [Erfahren Sie mehr](troubleshoot-dependencies.md#error-9014-httpgetrequesttoretrievefilefailed) darüber, wie Sie das Problem beheben können.|
| **9015:** Für das vCenter Server-Benutzerkonto, das für die Serverermittlung bereitgestellt wird, sind keine Berechtigungen für Gastvorgänge aktiviert. | Die erforderlichen Berechtigungen für Gastvorgänge wurden für das vCenter Server-Benutzerkonto nicht aktiviert. | Stellen Sie sicher, dass für das vCenter Server-Benutzerkonto Berechtigungen für **Virtual Machines** > **Gastvorgänge** aktiviert sind, um mit dem Server zu interagieren und die erforderlichen Daten zu pullen. <br/><br/> [Erfahren Sie mehr](tutorial-discover-vmware.md#prepare-vmware) über das Einrichten des vCenter Server-Kontos mit den erforderlichen Berechtigungen. |
| **9016:** Die Metadaten können nicht ermittelt werden, da der Agent für Gastvorgänge auf dem Server veraltet ist. | Entweder sind die VMware-Tools nicht auf dem Server installiert, oder die installierte Version ist nicht auf dem neuesten Stand. | Stellen Sie sicher, dass die aktuelle Version der VMware-Tools auf dem Server installiert ist und ausgeführt wird. Die Version der VMware-Tools muss Version 10.2.1 oder höher sein. |
| **9017:** Die Datei mit den ermittelten Metadaten kann auf dem Server nicht gefunden werden. | Dies kann ein vorübergehendes Problem aufgrund eines internen Fehlers sein. | Senden Sie eine Supportanfrage an Microsoft, um dieses Problem zu beheben. |
| **9018:** PowerShell ist nicht auf dem Server installiert. | PowerShell wurde auf dem Server nicht gefunden. | Stellen Sie sicher, dass PowerShell Version 2.0 oder höher auf dem Server installiert ist. <br/><br/> [Hier](troubleshoot-dependencies.md#error-9018-powershellnotfound) erfahren Sie mehr darüber, wie Sie das Problem beheben können.|
| **9019:** Die Metadaten können aufgrund von Gastvorgangsfehlern auf dem Server nicht ermittelt werden. | Es sind Fehler bei VMware-Gastvorgängen auf dem Server aufgetreten. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | Stellen Sie sicher, dass die Serveranmeldeinformationen für die Appliance gültig sind und der Benutzername in den Anmeldeinformationen das UPN-Format (User Principal Name, Benutzerprinzipalname) aufweist. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9020:** Die Datei, in der die ermittelten Metadaten auf dem Server gespeichert werden sollen, kann nicht erstellt werden. | Die Rolle, die mit den für die Appliance bereitgestellten Anmeldeinformationen verknüpft ist, oder eine lokale Gruppenrichtlinie schränkt die Erstellung der Datei im erforderlichen Ordner ein. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Überprüfen Sie, ob die für die Appliance bereitgestellten Anmeldeinformationen über die Berechtigung zum Erstellen von Dateien für den Ordner \<folder path/folder name> auf dem Server verfügen. <br/>2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über die erforderlichen Berechtigungen verfügen, geben Sie entweder andere an, oder bearbeiten Sie vorhandene Anmeldeinformationen. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9021:** Die Datei, in der die ermittelten Metadaten gespeichert werden sollen, kann nicht im richtigen Pfad auf dem Server erstellt werden. | Die VMware Tools melden einen falschen Dateipfad zum Erstellen der Datei. | Stellen Sie sicher, dass eine höhere Version als 10.2.0 der VMware-Tools auf dem Server installiert ist und ausgeführt wird. |
| **9022:** Der Zugriff zum Ausführen des Cmdlets „Get-WmiObject“ auf dem Server wird verweigert. | Die Rolle, die den für die Appliance bereitgestellten Anmeldeinformationen zugeordnet ist, oder eine lokale Gruppenrichtlinie schränkt den Zugriff auf das WMI-Objekt ein. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Überprüfen Sie, ob die für die Appliance bereitgestellten Anmeldeinformationen über Administratorrechte zum Erstellen von Dateien verfügen und WMI aktiviert ist. <br/>2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über die erforderlichen Berechtigungen verfügen, geben Sie entweder andere an, oder bearbeiten Sie vorhandene Anmeldeinformationen. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.)<br/><br/> [Erfahren Sie mehr](troubleshoot-dependencies.md#error-9022-getwmiobjectaccessdenied) darüber, wie Sie das Problem beheben können.|
| **9023:** PowerShell kann nicht ausgeführt werden, da der Wert der Umgebungsvariablen „%SystemRoot%“ leer ist. | Der Wert der Umgebungsvariablen „%SystemRoot%“ für den Server ist leer. | 1. Überprüfen Sie, ob die Umgebungsvariable einen leeren Wert zurückgibt, indem Sie den Befehl „echo %systemroot%“ auf dem betroffenen Server ausführen. <br/>2. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9024:** Die Ermittlung kann nicht durchgeführt werden, weil der Wert der Umgebungsvariablen „%TEMP%“ leer ist. | Der Wert der %TEMP%-Umgebungsvariablen für den Server ist leer. | 1. Überprüfen Sie, ob die Umgebungsvariable einen leeren Wert zurückgibt, indem Sie den Befehl „echo %temp%“ auf dem betroffenen Server ausführen. <br/>2. Wenn das Problem weiterhin besteht, übermitteln Sie eine Supportanfrage an Microsoft. |
| **9025:** Die Ermittlung kann nicht durchgeführt werden, da PowerShell auf dem Server beschädigt ist. | PowerShell ist auf dem Server beschädigt. | Installieren Sie PowerShell neu, und überprüfen Sie, ob PowerShell auf dem betroffenen Server ausgeführt wird. |
| **9026:** Auf dem Server können keine Gastvorgänge ausgeführt werden. | Der aktuelle Status des Servers lässt nicht zu, dass Gastvorgänge ausgeführt werden. | 1. Stellen Sie sicher, dass der betroffene Server aktiv ist und ausgeführt wird.<br/>2. Wenn das Problem weiterhin besteht, übermitteln Sie eine Supportanfrage an Microsoft. |
| **9027:** Die Metadaten können nicht ermittelt werden, da der Agent für Gastvorgänge nicht auf dem Server ausgeführt wird. | Es konnte keine Verbindung mit dem Agent für Gastvorgänge auf dem Server hergestellt werden. | Stellen Sie sicher, dass eine höhere Version als 10.2.0 der VMware-Tools auf dem Server installiert ist und ausgeführt wird. |
| **9028:** Die Datei, in der die ermittelten Metadaten gespeichert werden soll, kann aufgrund von unzureichendem Speicher auf dem Server nicht erstellt werden. | Es fehlt ausreichend Speicherplatz auf dem Serverdatenträger. | Stellen Sie sicher, dass genügend Speicherplatz im Datenträgerspeicher des betroffenen Servers zur Verfügung steht. |
| **9029:** Die für die Appliance bereitgestellten Anmeldeinformationen verfügen nicht über Zugriffsberechtigungen zum Ausführen von PowerShell. | Die für die Appliance bereitgestellten Anmeldeinformationen verfügen nicht über Zugriffsberechtigungen zum Ausführen von PowerShell. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Stellen Sie sicher, dass die Anmeldeinformationen für die Appliance auf dem Server auf PowerShell zugreifen können.<br/>2. Wenn die Anmeldeinformationen für die Appliance nicht über den erforderlichen Zugriff verfügen, geben Sie entweder andere an, oder bearbeiten Sie vorhandene Anmeldeinformationen. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9030:** Die ermittelten Metadaten können nicht erfasst werden, da sich der ESXi-Host, auf dem der Server gehostet wird, in einem nicht verbundenen Zustand befindet. | Der ESXi-Host, auf dem sich der Server befindet, befindet sich in einem nicht verbundenen Zustand. | Stellen Sie sicher, dass sich der ESXi-Host, der den Server ausführt, in einem verbundenen Zustand befindet. |
| **9031:** Die ermittelten Metadaten können nicht erfasst werden, da der ESXi-Host, auf dem der Server gehostet wird, nicht antwortet. | Der ESXi-Host, auf dem sich der Server befindet, ist in einem ungültigen Zustand. | Stellen Sie sicher, dass der ESXi-Host, der den Server ausführt, ausgeführt wird und verbunden ist. |
| **9032:** Aufgrund eines internen Fehlers kann keine Ermittlung durchgeführt werden. | Das aufgetretene Problem beruht auf einem internen Fehler. | Führen Sie die [auf dieser Website](troubleshoot-dependencies.md#error-9032-invalidrequest) beschriebenen Schritte aus, um das Problem zu beheben. Wenn das Problem weiterhin besteht, öffnen Sie eine Microsoft-Supportanfrage. |
| **9033:** Die Ermittlung kann nicht durchgeführt werden, da der Benutzername der Anmeldeinformationen, die auf der Appliance für den Server bereitgestellt werden, ungültige Zeichen enthält. | Die Anmeldeinformationen auf der Appliance enthalten ungültige Zeichen im Benutzernamen. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | Stellen Sie sicher, dass die Anmeldeinformationen auf der Appliance keine ungültigen Zeichen im Benutzernamen enthalten. Sie können zum Appliancekonfigurations-Manager zurückkehren, um die Anmeldeinformationen zu bearbeiten. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9034:** Die Ermittlung kann nicht durchgeführt werden, da der Benutzername der Anmeldeinformationen, die auf der Appliance für den Server bereitgestellt werden, nicht das UPN-Format aufweist. | Die Anmeldeinformationen auf der Appliance weisen keinen Benutzernamen im UPN-Format auf. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | Stellen Sie sicher, dass die Anmeldeinformationen auf der Appliance einen Benutzernamen im UPN-Format aufweisen. Sie können zum Appliancekonfigurations-Manager zurückkehren, um die Anmeldeinformationen zu bearbeiten. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9035:** Die Ermittlung kann nicht durchgeführt werden, da der PowerShell-Sprachmodus nicht richtig festgelegt ist. | Der PowerShell-Sprachmodus ist nicht auf **Full Language** festgelegt. | Stellen Sie sicher, dass der PowerShell-Sprachmodus auf **Full Language** festgelegt ist. |
| **9036:** Die Ermittlung kann nicht durchgeführt werden, da der Benutzername der Anmeldeinformationen, die auf der Appliance für den Server bereitgestellt werden, nicht das UPN-Format aufweist. | Die Anmeldeinformationen auf der Appliance weisen keinen Benutzernamen im UPN-Format auf. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | Stellen Sie sicher, dass die Anmeldeinformationen auf der Appliance einen Benutzernamen im UPN-Format aufweisen. Sie können zum Appliancekonfigurations-Manager zurückkehren, um die Anmeldeinformationen zu bearbeiten. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9037:** Die Metadatensammlung wird aufgrund einer langen Antwortzeit vom Server vorübergehend angehalten. | Der Server braucht zu lange, um zu antworten. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **10000:** Der auf dem Server ausgeführte Betriebssystemtyp wird nicht unterstützt. | Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux. | Nur Windows- und Linux-Betriebssystemtypen werden unterstützt. Das Betriebssystem \<GuestOSName> wird derzeit nicht unterstützt. |
| **10001:** Das Skript, das zum Erfassen von Ermittlungsmetadaten erforderlich ist, wurde auf dem Server nicht gefunden. | Das Skript, das zum Ausführen der Ermittlung erforderlich ist, wurde möglicherweise gelöscht oder aus dem erwarteten Speicherort entfernt. | Senden Sie eine Supportanfrage an Microsoft, um dieses Problem zu beheben. |
| **10002:** Für die Ermittlungsvorgänge ist ein Timeout auf dem Server aufgetreten. | Dies kann ein vorübergehendes Problem sein, da der Ermittlungs-Agent auf der Appliance nicht wie erwartet funktioniert. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn dies nicht der Fall ist, führen Sie die [auf dieser Website](troubleshoot-dependencies.md#error-10002-scriptexecutiontimedoutonvm) beschriebenen Schritte aus, um das Problem zu beheben. Wenn das Problem weiterhin besteht, öffnen Sie eine Microsoft-Supportanfrage.|
| **10003:** Der Prozess zur Ausführung der Ermittlungsvorgänge wurde mit einem Fehler beendet. | Der Prozess zur Ausführung der Ermittlungsvorgänge wurde aufgrund eines Fehlers plötzlich beendet.| Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **10004:** Es wurden keine Anmeldeinformationen auf der Appliance für den Serverbetriebssystemtyp angegeben. | Die Anmeldeinformationen für den Typ des Serverbetriebssystems wurden auf der Appliance nicht hinzugefügt. | 1. Stellen Sie sicher, dass Sie die Anmeldeinformationen für den Betriebssystemtyp des betroffenen Servers auf der Appliance hinzufügen.<br/> 2. Sie können jetzt mehrere Serveranmeldeinformationen auf der Appliance hinzufügen. |
| **10005:** Die auf der Appliance für den Server bereitgestellten Anmeldeinformationen sind ungültig. | Die auf der Appliance bereitgestellten Anmeldeinformationen sind ungültig. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Stellen Sie sicher, dass die auf der Appliance angegebenen Anmeldeinformationen gültig sind und der Zugriff auf den Server mit diesen Anmeldeinformationen möglich ist.<br/> 2. Sie können jetzt mehrere Serveranmeldeinformationen auf der Appliance hinzufügen.<br/> 3. Wechseln Sie zurück zum Appliance-Konfigurations-Manager, um andere Anmeldeinformationen anzugeben oder die vorhandenen Anmeldeinformationen zu bearbeiten. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) <br/><br/> [Hier](troubleshoot-dependencies.md#error-10005-guestcredentialnotvalid) erfahren Sie mehr darüber, wie Sie das Problem beheben können.|
| **10006:** Der auf dem Server ausgeführte Betriebssystemtyp wird nicht unterstützt. | Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux. | Nur Windows- und Linux-Betriebssystemtypen werden unterstützt. Das Betriebssystem \<GuestOSName> wird derzeit nicht unterstützt. |
| **10007:** Die vom Server ermittelten Metadaten können nicht verarbeitet werden. | Fehler beim Analysieren des Inhalts der Datei, die die ermittelten Metadaten enthält. | Senden Sie eine Supportanfrage an Microsoft, um dieses Problem zu beheben. |
| **10008:** Die Datei, in der die ermittelten Metadaten auf dem Server gespeichert werden sollen, kann nicht erstellt werden. | Die Rolle, die mit den für die Appliance oder eine Gruppenrichtlinie lokal bereitgestellten Anmeldeinformationen verknüpft ist, schränkt die Erstellung von Dateien im erforderlichen Ordner ein. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Überprüfen Sie, ob die für die Appliance bereitgestellten Anmeldeinformationen über die Berechtigung zum Erstellen von Dateien für den Ordner \<folder path/folder name> auf dem Server verfügen.<br/>2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über die erforderlichen Berechtigungen verfügen, geben Sie entweder andere an, oder bearbeiten Sie vorhandene Anmeldeinformationen. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **10009:** Die ermittelten Metadaten können nicht in die Datei auf dem Server geschrieben werden. | Die Rolle, die den für die Appliance oder eine Gruppenrichtlinie lokal bereitgestellten Anmeldeinformationen zugeordnet ist, schränkt das Schreiben in die Datei auf dem Server ein. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Überprüfen Sie, ob die auf der Appliance bereitgestellten Anmeldeinformationen über die Berechtigung zum Schreiben in den Ordner \<folder path/folder name> auf dem Server verfügen.<br/>2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über die erforderlichen Berechtigungen verfügen, geben Sie entweder andere an, oder bearbeiten Sie vorhandene Anmeldeinformationen. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **10010:** Die Ermittlung kann nicht durchgeführt werden, da der Befehl „%CommandName;“, der zum Erfassen von Metadaten erforderlich ist, auf dem Server fehlt. | Das Paket, das den Befehl „%CommandName;“ enthält, ist nicht auf dem Server installiert. | Stellen Sie sicher, dass das Paket, das den Befehl „%CommandName;“ enthält, auf dem Server installiert ist. |
| **10011:** Die auf der Appliance bereitgestellten Anmeldeinformationen wurden verwendet, um sich bei einer interaktiven Sitzung an- und abzumelden. | Die interaktive An- und Abmeldung erzwingt, dass die Registrierungsschlüssel im Profil des verwendeten Kontos entladen werden. Durch diese Bedingung sind die Schlüssel für zukünftige Verwendung nicht mehr verfügbar. | Verwenden Sie die [auf dieser Website](/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error#resolutionus/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error) dokumentierten Lösungsmethoden. |
| **10012:** Es wurden keine Anmeldeinformationen auf der Appliance für den Server angegeben. | Entweder wurden keine Anmeldeinformationen für den Server angegeben, oder Sie haben Domänenanmeldeinformationen mit falschem Domänennamen auf der Appliance angegeben. [Hier ](troubleshoot-dependencies.md#error-10012-credentialnotprovided) erfahren Sie m ehr über die Ursache dieses Fehlers. | 1. Stellen Sie sicher, dass auf der Appliance Anmeldeinformationen für den Server angegeben werden und der Zugriff auf den Server mit diesen Anmeldeinformationen möglich ist. <br/>2. Sie können jetzt mehrere Anmeldeinformationen für die Appliance für Server hinzufügen. Wechseln Sie zurück zum Appliancekonfigurations-Manager, um Anmeldeinformationen für den Server anzugeben.|


## <a name="error-970-dependencymapinsufficientprivilegesexception"></a>Error 970: DependencyMapInsufficientPrivilegesException

### <a name="cause"></a>Ursache
Der Fehler tritt in der Regel bei Linux-Servern auf, wenn Sie keine Anmeldeinformationen mit den erforderlichen Berechtigungen für die Appliance angegeben haben.

### <a name="remediation"></a>Behebung
Sie haben zwei Möglichkeiten:

- Stellen Sie sicher, dass Sie ein Stammbenutzerkonto angegeben haben.
- Vergewissern Sie sich, dass ein Konto die folgenden Berechtigungen für Dateien in „/bin/netstat“ und „/bin/ls“ hat:
   - CAP_DAC_READ_SEARCH
   - CAP_SYS_PTRACE

So überprüfen Sie, ob das auf der Appliance bereitgestellte Benutzerkonto über die erforderlichen Berechtigungen verfügt:

1. Melden Sie sich bei dem Server, auf dem dieser Fehler aufgetreten ist, mit demselben Benutzerkonto wie in der Fehlermeldung erwähnt an.
1. Führen Sie in Azure Shell die folgenden Befehle aus. Wenn Sie nicht über die erforderlichen Berechtigungen für die Abhängigkeitsanalyse ohne Agent verfügen, werden Fehler angezeigt.

   ````
   ps -o pid,cmd | grep -v ]$
   netstat -atnp | awk '{print $4,$5,$7}'
   ````
1. Legen Sie die erforderlichen Berechtigungen für die Dateien „/bin/netstat“ und „/bin/ls“ fest, indem Sie die folgenden Befehle ausführen:

   ````
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
   ````
1. Sie können überprüfen, ob die oben genannten Befehle dem Benutzerkonto die erforderlichen Berechtigungen zugewiesen haben:

   ````
   getcap /usr/bin/ls
   getcap /usr/bin/netstat
   ````
1. Führen Sie die in Schritt 2 bereitgestellten Befehle erneut aus, um eine erfolgreiche Ausgabe zu erhalten.


## <a name="error-9014-httpgetrequesttoretrievefilefailed"></a>Error 9014: HTTPGetRequestToRetrieveFileFailed

### <a name="cause"></a>Ursache
Das Problem tritt auf, wenn der VMware-Ermittlungs-Agent auf der Appliance versucht, die Ausgabedatei mit Abhängigkeitsdaten aus dem Serverdateisystem über den ESXi-Host herunterzuladen, auf dem der Server gehostet wird.

### <a name="remediation"></a>Behebung
- Sie können die TCP-Konnektivität mit dem ESXi-Host _(in der Fehlermeldung angegebener Name)_ an Port 443 (muss auf ESXi-Hosts geöffnet sein, um Abhängigkeitsdaten zu pullen) von der Appliance testen. Öffnen Sie PowerShell auf dem Applianceserver, und führen Sie den folgenden Befehl aus:
    ````
    Test -NetConnection -ComputeName <Ip address of the ESXi host> -Port 443
    ````

- Wenn der Befehl erfolgreiche Konnektivität zurückgibt, navigieren Sie zu **Azure Migrate-Projekt** > **Ermittlung und Bewertung** > **Übersicht** > **Verwalten** > **Appliances**, und wählen Sie den Appliancenamen und dann **Dienste aktualisieren** aus.

## <a name="error-9018-powershellnotfound"></a>Error 9018: PowerShellNotFound

### <a name="cause"></a>Ursache
Der Fehler tritt in der Regel bei Servern auf, auf denen Windows Server 2008 oder niedriger ausgeführt wird.

### <a name="remediation"></a>Behebung
Installieren Sie die erforderliche PowerShell-Version (2.0 oder höher) an diesem Speicherort auf dem Server: ($SYSTEMROOT)\System32\WindowsPowershell\v1.0\powershell.exe. [Hier](/powershell/scripting/windows-powershell/install/installing-windows-powershell) erfahren Sie mehr über die Installation von PowerShell unter Windows Server.

Nachdem Sie die erforderliche PowerShell-Version installiert haben, überprüfen Sie, ob der Fehler behoben wurde, indem Sie die [auf dieser Website](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli) beschriebenen Schritte ausführen.

## <a name="error-9022-getwmiobjectaccessdenied"></a>Error 9022: GetWMIObjectAccessDenied

### <a name="remediation"></a>Behebung
Stellen Sie sicher, dass das in der Appliance bereitgestellte Benutzerkonto Zugriff auf den WMI-Namespace und Unternamespaces besitzt. So legen Sie den Zugriff fest:

1.  Wechseln Sie zu dem Server, der diesen Fehler meldet.
1.  Suchen Sie im **Startmenü** nach **Ausführen**, und wählen Sie diese Option aus. Geben Sie im Dialogfeld **Ausführen** die Zeichenfolge **wmimgmt.msc** in das Textfeld **Öffnen** ein, und drücken Sie die **EINGABETASTE**.
1.  Die wmimgmt-Konsole wird geöffnet, in der im linken Bereich **WMI-Steuerung (lokal)** angezeigt wird. Klicken Sie mit der rechten Maustaste darauf, und wählen Sie im Menü **Eigenschaften** aus.
1.  Wählen Sie im Dialogfeld **Eigenschaften von WMI-Steuerung (lokal)** die Registerkarte **Sicherheiten** aus.
1.  Wählen Sie auf der Registerkarte **Sicherheiten** die Option **Sicherheit** aus, um das Dialogfeld **Sicherheit für ROOT** zu öffnen.
1.  Wählen Sie **Erweitert** aus, um das Dialogfeld **Erweiterte Sicherheitseinstellungen für Root** zu öffnen. 
1.  Wählen Sie **Hinzufügen** aus, um das Dialogfeld **Berechtigungseintrag für Root** zu öffnen.
1.  Klicken Sie auf **Prinzipal auswählen**, um das Dialogfeld **Benutzer, Computer, Dienstkonten oder Gruppen auswählen** zu öffnen.
1. Wählen Sie die Benutzernamen oder Gruppen aus, denen Sie Zugriff auf die WMI gewähren möchten, und wählen Sie dann **OK** aus.
1. Stellen Sie sicher, dass Sie Ausführungsberechtigungen erteilen, und wählen Sie in der Dropdownliste **Gilt für** die Option **Dieser Namespace und Unternamespaces** aus.
1. Wählen Sie **Übernehmen** aus, um die Einstellungen zu speichern und alle Dialogfelder zu schließen.

Nachdem Sie den erforderlichen Zugriff erteilt haben, überprüfen Sie, ob der Fehler behoben wurde, indem Sie die [auf dieser Website](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli) beschriebenen Schritte ausführen.

## <a name="error-9032-invalidrequest"></a>Error 9032: InvalidRequest

### <a name="cause"></a>Ursache
Dieses Problem kann mehrere Gründe haben. Ein Grund könnte sein, dass der im Appliancekonfigurations-Manager angegebene Benutzername (Serveranmeldeinformationen) ungültige XML-Zeichen enthält. Ungültige Zeichen verursachen einen Fehler beim Analysieren der SOAP-Anforderung.

### <a name="remediation"></a>Behebung
- Vergewissern Sie sich, dass der Benutzername der Serveranmeldeinformationen keine ungültigen XML-Zeichen enthält und das Format username@domain.com aufweist. Dieses Format wird häufig als UPN-Format bezeichnet.
- Nachdem Sie die Anmeldeinformationen auf der Appliance bearbeitet haben, können Sie überprüfen, ob der Fehler behoben wurde, indem Sie die [auf dieser Website](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli) beschriebenen Schritte ausführen.


## <a name="error-10002-scriptexecutiontimedoutonvm"></a>Error 10002: ScriptExecutionTimedOutOnVm

### <a name="cause"></a>Ursache
- Dieser Fehler tritt auf, wenn der Server langsam ist oder nicht reagiert und für das Skript, das zum Pullen der Abhängigkeitsdaten ausgeführt wird, ein Timeout auftritt.
- Wenn der Ermittlungs-Agent diesen Fehler auf dem Server erkennt, versucht die Appliance danach keine Abhängigkeitsanalyse ohne Agent auf dem Server, um eine Überlastung des nicht reagierenden Servers zu vermeiden.
- Der Fehler tritt weiterhin auf, bis Sie das Problem mit dem Server überprüfen und den Ermittlungsdienst neu starten.

### <a name="remediation"></a>Behebung
1. Melden Sie sich bei dem Server an, auf dem dieser Fehler auftritt.
1. Führen Sie die folgenden Befehle in PowerShell aus:
   ````
   Get-WMIObject win32_operatingsystem;
   Get-WindowsFeature  | Where-Object {$_.InstallState -eq 'Installed' -or ($_.InstallState -eq $null -and $_.Installed -eq 'True')};
   Get-WmiObject Win32_Process;
   netstat -ano -p tcp | select -Skip 4;
   ````
1. Wenn Befehle das Ergebnis nach wenigen Sekunden ausgeben, können Sie zu **Azure Migrate-Projekt** > **Ermittlung und Bewertung** > **Übersicht** > **Verwalten** > **Appliances** navigieren und anschließend den Appliancenamen und dann **Dienste aktualisieren** auswählen, um den Ermittlungsdienst neu zu starten.
1. Wenn für die Befehle ein Timeout auftritt, ohne dass eine Ausgabe erfolgt, gilt Folgendes:

   - Finden Sie heraus, welche Prozesse eine hohe CPU- oder Arbeitsspeicherauslastung auf dem Server verursachen.
   - Versuchen Sie, für diesen Server mehr Kerne oder Arbeitsspeicher bereitzustellen, und führen Sie die Befehle noch einmal aus.

## <a name="error-10005-guestcredentialnotvalid"></a>Error 10005: GuestCredentialNotValid

### <a name="remediation"></a>Behebung
- Stellen Sie die Gültigkeit der Anmeldeinformationen _(der im Fehler angegebene Anzeigename)_ sicher, indem Sie im Konfigurations-Manager der Appliance **Anmeldeinformationen erneut überprüfen** auswählen.
- Achten Sie darauf, dass Sie sich mit den gleichen Anmeldeinformationen, die auf der Appliance bereitgestellt wurden, beim betroffenen Server anmelden können.
- Sie können versuchen, anstelle des Administratorkontos ein anderes Benutzerkonto (für dieselbe Domäne, wenn der Server in die Domäne eingebunden ist) für diesen Server zu verwenden.
- Das Problem kann auftreten, wenn die Kommunikation zwischen dem globalen Katalog und dem Domänencontroller unterbrochen ist. Sie überprüfen dies, indem Sie ein neues Benutzerkonto auf dem Domänencontroller erstellen und dasselbe Konto auf der Appliance bereitstellen. Möglicherweise müssen Sie auch den Domänencontroller neu starten.
- Nachdem Sie die Wartungsschritte ausgeführt haben, überprüfen Sie, ob der Fehler behoben wurde, indem Sie die [auf dieser Website](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli) beschriebenen Schritte ausführen.

## <a name="error-10012-credentialnotprovided"></a>Error 10012: CredentialNotProvided

### <a name="cause"></a>Ursache
Dieser Fehler tritt auf, wenn Sie im Appliancekonfigurations-Manager Domänenanmeldeinformationen mit einem falschen Domänennamen angegeben haben. Wenn Sie beispielsweise Domänenanmeldeinformationen mit dem Benutzernamen user@abc.com angegeben haben, als Domänenname jedoch „def.com“ angegeben wurde, werden diese Anmeldeinformationen nicht verwendet, wenn der Server mit def.com verbunden ist, und Sie erhalten diese Fehlermeldung.

### <a name="remediation"></a>Behebung
- Navigieren Sie zum Appliancekonfigurations-Manager, um Serveranmeldeinformationen hinzuzufügen oder vorhandene Anmeldeinformationen wie in der Ursache erläutert zu bearbeiten.
- Nachdem Sie die Wartungsschritte ausgeführt haben, überprüfen Sie, ob der Fehler behoben wurde, indem Sie die [auf dieser Website](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli) beschriebenen Schritte ausführen.

## <a name="mitigation-verification-by-using-vmware-powercli"></a>Entschärfungsüberprüfung mit VMware PowerCLI

Nachdem Sie die Schritte zur Entschärfung der oben aufgeführten Fehler durchgeführt haben, können Sie überprüfen, ob die Fehler behoben wurden, indem Sie über den Applianceserver einige PowerCLI-Befehle ausführen. Wenn die Befehle erfolgreich ausgeführt werden, bedeutet dies, dass das Problem behoben wurde. Andernfalls müssen Sie die Wartungsschritte noch einmal ausführen.

1. Führen Sie die folgenden Befehle aus, um PowerCLI auf dem Applianceserver einzurichten:
   ````
   Install-Module -Name VMware.PowerCLI -AllowClobber
   Set-PowerCLIConfiguration -InvalidCertificateAction Ignore
   ````
1. Stellen Sie von der Appliance aus eine Verbindung mit vCenter Server her, indem Sie die IP-Adresse von vCenter Server im Befehl und die Anmeldeinformationen an der Eingabeaufforderung angeben:
   ````
   Connect-VIServer -Server <IPAddress of vCenter Server>
   ````
1. Stellen Sie von der Appliance aus eine Verbindung mit dem Zielserver her, indem Sie den Servernamen und die Serveranmeldeinformationen (wie für die Appliance angegeben) eingeben:
   ````
   $vm = get-VM <VMName>
   $credential = Get-Credential
   ````
1. Führen Sie für die Abhängigkeitsanalyse ohne Agent die folgenden Befehle aus, um eine erfolgreiche Ausgabe zu bestätigen:

      - Für Windows-Server:
    
          ```` 
          Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'Get-WmiObject Win32_Process'" -GuestCredential $credential 
      
          Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'netstat -ano -p tcp'" -GuestCredential $credential 
          ```` 
      - Für Linux-Server:
          ````
          Invoke-VMScript -VM $vm -ScriptText "ps -o pid,cmd | grep -v ]$" -GuestCredential $credential
    
          Invoke-VMScript -VM $vm -ScriptText "netstat -atnp | awk '{print $4,$5,$7}'" -GuestCredential $credential
          ````
1. Nachdem Sie bestätigt haben, dass die Entschärfung funktioniert hat, navigieren Sie zu **Azure Migrate-Projekt** > **Ermittlung und Bewertung** > **Übersicht** > **Verwalten** > **Appliances**, und wählen den Appliancenamen und dann **Dienste aktualisieren** aus, um einen neuen Ermittlungszyklus zu starten.

## <a name="my-log-analytics-workspace-isnt-listed-when-you-try-to-configure-the-workspace-in-azure-migrate-for-agent-based-dependency-analysis"></a>Beim Versuch, den Log Analytics-Arbeitsbereich in Azure Migrate für die Abhängigkeitsanalyse ohne Agent zu konfigurieren, wird der Arbeitsbereich aufgeführt
Azure Migrate unterstützt derzeit die Erstellung von OMS-Arbeitsbereichen in den Regionen „USA, Osten“, „Asien, Südosten“ und „Europa, Westen“. Wenn der Arbeitsbereich außerhalb von Azure Migrate in einer beliebigen anderen Region erstellt wird, kann er derzeit keinem Projekt zugeordnet werden.

## <a name="agent-based-dependency-visualization-in-azure-government"></a>Agent-basierte Abhängigkeitsvisualisierung in Azure Government

Die Agent-basierte Abhängigkeitsanalyse wird in Azure Government nicht unterstützt. Verwenden Sie die Abhängigkeitsanalyse ohne Agent, die _nur für VMware-Server verfügbar ist_.

## <a name="agent-based-dependencies-dont-show-after-agent-installation"></a>Agent-basierte Abhängigkeiten werden nach der Installation von Agents nicht angezeigt.

Nachdem Sie die Agents für die Abhängigkeitsvisualisierung auf lokalen virtuellen Computern installiert haben, dauert es in Azure Migrate üblicherweise 15 bis 30 Minuten, bis die Abhängigkeiten im Portal angezeigt werden. Stellen Sie nach einer Wartezeit von mehr als 30 Minuten sicher, dass Microsoft Monitoring Agent (MMA) eine Verbindung zum Log Analytics-Arbeitsbereich herstellen kann.

Für virtuelle Windows-Computer:
1. Starten Sie MMA in der Systemsteuerung.
1. Stellen Sie unter **Microsoft Monitoring Agent-Eigenschaften** > **Azure Log Analytics (OMS)** sicher, dass der **Status** für den Arbeitsbereich grün angezeigt wird.
1. Wenn der Status nicht grün angezeigt wird, entfernen Sie den Arbeitsbereich, und fügen Sie ihn erneut zu MMA hinzu.

    ![Screenshot: MMA-Status](./media/troubleshoot-assessment/mma-properties.png)

Stellen Sie bei virtuellen Linux-Computern sicher, dass die Installationsbefehle für MMA und den Abhängigkeits-Agent erfolgreich ausgeführt wurden. Weitere Informationen zur Problembehandlung finden Sie auf [dieser Website](../azure-monitor/vm/service-map.md#post-installation-issues).

## <a name="supported-operating-systems-for-agent-based-dependency-analysis"></a>Unterstützte Betriebssysteme für Agent-basierte Abhängigkeitsanalyse

- **MMS-Agent:** die unterstützten [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)- und [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems)-Betriebssysteme
- **Dependency-Agent:** die unterstützten [Windows- und Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems)-Betriebssysteme

## <a name="visualize-dependencies-for-1-hour-with-agent-based-dependency-analysis"></a>Visualisieren von Abhängigkeiten für mehr als eine Stunde mit Agent-basierter Abhängigkeitsanalyse

Obwohl Sie bei der Abhängigkeitsanalyse ohne Agent mit Azure Migrate zu einem bestimmten Datum im letzten Monat zurückgehen können, können Sie die Abhängigkeiten jedoch nur für einen Zeitraum von maximal einer Stunde visualisieren. So können Sie beispielsweise mithilfe der Zeitraumfunktionalität im Abhängigkeitsdiagramm Abhängigkeiten für den gestrigen Tag, jedoch nur für einen Zeitraum von einer Stunde, anzeigen. Sie können Azure Monitor-Protokolle zum [Abfragen der Abhängigkeitsdaten](./how-to-create-group-machine-dependencies.md) über einen längeren Zeitraum verwenden.

## <a name="visualize-dependencies-for-10-servers-with-agent-based-dependency-analysis"></a>Visualisieren von Abhängigkeiten für mehr als 10 Server mit Agent-basierter Abhängigkeitsanalyse

Sie können in Azure Migrate mit der Abhängigkeitsanalyse ohne Agent [Abhängigkeiten für Gruppen](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) mit bis zu zehn VMs visualisieren. Teilen Sie bei größeren Gruppen die virtuellen Computer in kleinere Gruppen auf, um die Abhängigkeiten zu visualisieren.

## <a name="servers-show-install-agent-for-agent-based-dependency-analysis"></a>Server zeigen „Agent installieren“ für Agent-basierte Abhängigkeitsanalyse an

Nach dem Migrieren von Servern mit aktivierter Abhängigkeitsvisualisierung zu Azure zeigen Server möglicherweise aufgrund des folgenden Verhaltens die Aktion **Agent installieren** anstelle von **Abhängigkeiten anzeigen** an:

- Nach der Migration zu Azure werden die lokalen Server ausgeschaltet, und die entsprechenden virtuellen Computer werden in Azure hochgefahren. Diese Server beziehen eine andere MAC-Adresse.
- Server haben möglicherweise auch eine andere IP-Adresse, je nachdem, ob Sie die lokale IP-Adresse beibehalten haben oder nicht.
- Wenn sich sowohl Mac- als auch IP-Adressen von den lokalen Adressen unterscheiden, verknüpft Azure Migrate die lokalen Server nicht mit Service Map-Abhängigkeitsdaten. In diesem Fall wird die Option zum Installieren des Agents anstatt die Option zum Anzeigen von Abhängigkeiten angezeigt.
- Nach einer Testmigration zu Azure bleiben die lokalen Server erwartungsgemäß eingeschaltet. Entsprechende Server, die in Azure hochgefahren wurden, erhalten andere MAC-Adressen und ggf. auch andere IP-Adressen. Sofern Sie ausgehenden Datenverkehr von Azure Monitor-Protokollen von diesen Servern nicht sperren, ordnet Azure Migrate den lokalen Servern keine Service Map-Abhängigkeitsdaten zu. In diesem Fall wird die Option zum Installieren von Agents anstatt zum Anzeigen von Abhängigkeiten angezeigt.

## <a name="capture-network-traffic"></a>Erfassen des Netzwerkdatenverkehrs

So sammeln Sie Protokolle zum Netzwerkdatenverkehr:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Drücken Sie F12, um die Entwicklertools zu starten. Deaktivieren Sie ggf. die Einstellung **Einträge beim Navigieren löschen**.
1. Wählen Sie die Registerkarte **Netzwerk** aus, und starten Sie die Erfassung von Netzwerkdatenverkehr:
   - Wählen Sie in Chrome die Option **Protokoll speichern**. Die Aufzeichnung sollte automatisch gestartet werden. Ein roter Kreis gibt an, dass der Datenverkehr erfasst wird. Wenn der rote Kreis nicht angezeigt wird, wählen Sie zum Starten den schwarzen Kreis aus.
   - In Microsoft Edge und Internet Explorer sollte die Aufzeichnung automatisch starten. Ist dies nicht der Fall, wählen Sie die grüne Wiedergabeschaltfläche aus.
1. Versuchen Sie, den Fehler zu reproduzieren.
1. Wenn der Fehler während der Aufzeichnung aufgetreten ist, beenden Sie die Aufzeichnung, und speichern Sie eine Kopie der aufgezeichneten Aktivität:
   - Klicken Sie in Chrome mit der rechten Maustaste, und wählen Sie **Als HAR mit Inhalt speichern** aus. Durch diese Aktion werden die Protokolle komprimiert und als HTTP-Archiv (HAR-Datei) exportiert.
   - Wählen Sie in Microsoft Edge oder Internet Explorer das Symbol **Aufgezeichneten Datenverkehr exportieren** aus. Durch diese Aktion werden die Protokolle komprimiert und exportiert.
1. Wählen Sie die Registerkarte **Konsole** aus, um sie auf Warnungen oder Fehler zu überprüfen. So speichern Sie das Konsolenprotokoll:
   - Klicken Sie in Chrome mit der rechten Maustaste auf eine beliebige Stelle im Konsolenprotokoll. Wählen Sie **Speichern unter** aus, um das Protokoll zu exportieren und zu komprimieren.
   - Klicken Sie in Microsoft Edge oder Internet Explorer mit der rechten Maustaste auf die Fehler, und wählen Sie **Alle kopieren** aus.
1. Schließen Sie die Entwicklertools.


## <a name="next-steps"></a>Nächste Schritte

[Erstellen](how-to-create-assessment.md) Sie eine Bewertung oder [passen](how-to-modify-assessment.md) Sie sie an.
