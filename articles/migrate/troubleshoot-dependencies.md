---
title: Behandeln von Problemen mit der Abhängigkeitsanalyse mit oder ohne Agent
description: Unterstützung bei der Abhängigkeitsvisualisierung in Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 7e618052a71f73c5559860fa09b495e9be2a14d9
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290966"
---
# <a name="troubleshoot-dependency-visualization"></a>Behandeln von Problemen bei der Abhängigkeitsvisualisierung

Dieser Artikel hilft Ihnen bei der Behandlung von Problemen bei der Abhängigkeitsanalyse mit und ohne Agent _(nur für VMware-Server verfügbar)_ . [Erfahren Sie mehr](concepts-dependency-visualization.md) über die Typen der Abhängigkeitsvisualisierung, die in Azure Migrate unterstützt werden.


## <a name="visualize-dependencies-for--1-hour-with-agentless-dependency-analysis"></a>Visualisieren von Abhängigkeiten für > 1 Stunde mit Abhängigkeitsanalyse ohne Agent

Mit der Abhängigkeitsanalyse ohne Agent können Sie Abhängigkeiten für eine Dauer von bis zu 30 Tagen visualisieren oder in eine Karte exportieren.

## <a name="visualized-dependencies-for--10-servers-with-agentless-dependency-analysis"></a>Visualisieren von Abhängigkeiten für > 10 Server mit Abhängigkeitsanalyse ohne Agent

Azure Migrate verfügt über eine Power BI-Vorlage, die Sie zum gleichzeitigen Visualisieren der Netzwerkverbindungen vieler Server und zum Filtern nach Prozess und Server verwenden können. [Erfahren Sie mehr](how-to-create-group-machine-dependencies-agentless.md#visualize-network-connections-in-power-bi) darüber, wie Sie die Abhängigkeiten für viele Server gemeinsam visualisieren.

## <a name="dependencies-export-csv-shows-unknown-process-with-agentless-dependency-analysis"></a>Export-CSV für Abhängigkeiten zeigt „Unbekannter Prozess“ mit Abhängigkeitsanalyse ohne Agent
Bei der Abhängigkeitsanalyse ohne Agent werden die Prozessnamen auf Grundlage der besten Leistung aufgezeichnet. In bestimmten Szenarien ist es nicht möglich, die Prozessnamen an beiden Enden der Abhängigkeit zu ermitteln, obwohl der Quell- und Zielservername sowie der Zielport aufgezeichnet werden. In solchen Fällen wird der Prozess als „_Unbekannter Prozess_“ gekennzeichnet.

## <a name="common-agentless-dependency-analysis-errors"></a>Häufige Fehler bei der Abhängigkeitsanalyse ohne Agent

Azure Migrate unterstützt Abhängigkeitsanalyse ohne Agent mithilfe von Azure Migrate: Ermittlung und Bewertung. Abhängigkeitsanalyse ohne Agent wird derzeit nur für VMware unterstützt. [Erfahren Sie mehr](how-to-create-group-machine-dependencies-agentless.md) über die Anforderungen für Abhängigkeitsanalyse ohne Agent.

Die Liste der Abhängigkeitsanalysefehler ohne Agent wird in der folgenden Tabelle zusammengefasst.

> [!Note]
> Die gleichen Fehler können auch bei der Softwareinventarisierung auftreten, da sie der gleichen Methodik wie Abhängigkeitsanalyse ohne Agent folgt, um die erforderlichen Daten zu erfassen.

| **Fehler** | **Ursache** | **Aktion** |
|--|--|--|
| **9000**: Der Status der VMware-Tools auf dem Server kann nicht erkannt werden. | VMware-Tools sind möglicherweise nicht auf dem Server installiert, oder die installierte Version ist beschädigt. | Stellen Sie sicher, dass die VMware-Tools mit einer höheren Version als 10.2.1 auf dem Server installiert sind und ausgeführt werden. |
| **9001**: VMware-Tools sind nicht auf dem System installiert. | VMware-Tools sind möglicherweise nicht auf dem Server installiert, oder die installierte Version ist beschädigt. | Stellen Sie sicher, dass die VMware-Tools mit einer höheren Version als 10.2.1 auf dem Server installiert sind und ausgeführt werden. |
| **9002**: VMware-Tools werden nicht auf dem Server ausgeführt. | VMware-Tools sind möglicherweise nicht auf dem Server installiert, oder die installierte Version ist beschädigt. | Stellen Sie sicher, dass VMware-Tools mit einer höheren Version als 10.2.0 auf dem Server installiert sind und ausgeführt werden. |
| **9003**: Der auf dem Server ausgeführte Betriebssystemtyp wird nicht unterstützt. | Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux. | Nur Windows- und Linux-Betriebssystemtypen werden unterstützt. Wenn der Server tatsächlich Windows oder Linux verwendet, überprüfen Sie den in vCenter Server angegebenen Betriebssystemtyp. |
| **9004**: Der Server eist keinen Ausführungsstatus auf. | Der Server weist einen ausgeschalteten Status auf. | Stellen Sie sicher, dass der Server ausgeführt wird. |
| **9005**: Der auf dem Server ausgeführte Betriebssystemtyp wird nicht unterstützt. | Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux. | Nur Windows- und Linux-Betriebssystemtypen werden unterstützt. \<FetchedParameter>-Betriebssystem wird derzeit nicht unterstützt. |
| **9006**: Die URL, die zum Herunterladen der Ermittlungsmetadatendatei vom Server erforderlich ist, ist leer. | Dies kann ein vorübergehendes Problem sein, da der Ermittlungs-Agent auf der Appliance nicht wie erwartet funktioniert. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9007**: Der Prozess, mit dem das Skript zum Erfassen der Metadaten ausgeführt wird, wurde auf dem Server nicht gefunden. | Dies kann ein vorübergehendes Problem sein, da der Ermittlungs-Agent auf der Appliance nicht wie erwartet funktioniert. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9008**: Der Status des Prozesses, der zum Erfassen der Metadaten auf dem Server ausgeführt wird, kann nicht abgerufen werden. | Dies kann aufgrund eines internen Fehlers ein vorübergehendes Problem sein. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9009**: Die Windows-Benutzerkontensteuerung (UAC) verhindert die Ausführung von Ermittlungsvorgängen auf dem Server. | Die Einstellungen für die Windows-Benutzerkontensteuerung (UAC) verhindern die Ermittlung installierter Anwendungen auf dem Server. | Verringern Sie auf dem beeinträchtigten Server die Ebene der Einstellungen für die Benutzerkontensteuerung in der Systemsteuerung. |
| **9010**: Der Server ist ausgeschaltet. | Der Server weist einen ausgeschalteten Status auf. | Stellen Sie sicher, dass sich der Server in einem eingeschalteten Zustand befindet. |
| **9011**: Die Datei, die die ermittelten Metadaten enthält, kann auf dem Server nicht gefunden werden. | Dies kann aufgrund eines internen Fehlers ein vorübergehendes Problem sein. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9012**: Die Datei, die die ermittelten Metadaten auf dem Server enthält, ist leer. | Dies kann aufgrund eines internen Fehlers ein vorübergehendes Problem sein. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9013**: Bei jeder Protokollierung auf dem Server wird jedes Mal ein neues temporäres Benutzerprofil erstellt. | Bei jeder Protokollierung auf dem Server wird jedes Mal ein neues temporäres Benutzerprofil erstellt. | Senden Sie einen Microsoft-Supportfall, um dieses Problem zu beheben. |
| **9014**: Die Datei mit den ermittelten Metadaten kann aufgrund eines Fehlers auf dem ESXi-Host nicht abgerufen werden.  Fehlercode: %ErrorCode; Details: %ErrorMessage | Auf dem ESXi-Host ist ein Fehler \<HostName> aufgetreten.  Fehlercode: %ErrorCode; Details: %ErrorMessage | Stellen Sie sicher, dass Port 443 auf dem ESXi-Host geöffnet ist, auf dem der Server ausgeführt wird.<br/><br/> [Erfahren Sie mehr](troubleshoot-dependencies.md#error-9014-httpgetrequesttoretrievefilefailed) darüber, wie Sie das Problem beheben können.|
| **9015**: Für das vCenter Server-Benutzerkonto, das für die Serverermittlung bereitgestellt wird, sind keine Gastbetriebsrechte aktiviert. | Die erforderlichen Berechtigungen von Gastvorgängen wurden für das vCenter Server-Benutzerkonto nicht aktiviert. | Stellen Sie sicher, dass für das vCenter Server-Benutzerkonto Berechtigungen für „Virtual Machines > Gastvorgänge“ aktiviert sind, um mit dem Server zu interagieren und die erforderlichen Daten zu pullen. <br/><br/> [Erfahren Sie mehr](tutorial-discover-vmware.md#prepare-vmware) über das Einrichten des vCenter Server-Kontos mit den erforderlichen Berechtigungen. |
| **9016**: Die Metadaten können nicht ermittelt werden, da der Gastbetriebs-Agent auf dem Server veraltet ist. | Entweder sind die VMware-Tools nicht auf dem Server installiert, oder die installierte Version ist nicht auf dem neuesten Stand. | Stellen Sie sicher, dass die VMware-Tools auf dem Server installiert sind und ausgeführt werden. Die Version der VMware-Tools muss Version 10.2.1 oder höher sein. |
| **9017**: Die Datei, die die ermittelten Metadaten enthält, kann auf dem Server nicht gefunden werden. | Dies kann aufgrund eines internen Fehlers ein vorübergehendes Problem sein. | Senden Sie einen Microsoft-Supportfall, um dieses Problem zu beheben. |
| **9018**: PowerShell ist nicht auf dem Server installiert. | PowerShell wurde auf dem Server nicht gefunden. | Stellen Sie sicher, dass PowerShell Version 2.0 oder höher auf dem Server installiert ist. <br/><br/> [Erfahren Sie mehr](troubleshoot-dependencies.md#error-9018-powershellnotfound) darüber, wie Sie das Problem beheben können.|
| **9019**: Die Metadaten können aufgrund von Gastvorgangsfehlern auf dem Server nicht ermittelt werden. | Fehler bei VMware-Gastvorgängen auf dem Server. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: <FriendlyNameOfCredentials>. | Stellen Sie sicher, dass die für die Appliance angegebenen Serveranmeldeinformationen gültig sind und der Benutzername in den Anmeldeinformationen das UPN-Format aufweist. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9020**: Die Datei, die die ermittelten Metadaten auf dem Server enthalten muss, kann nicht erstellt werden. | Die Rolle, die mit den für die Appliance oder eine Gruppenrichtlinie lokal bereitgestellten Anmeldeinformationen verknüpft ist, schränkt die Erstellung von Dateien im erforderlichen Ordner ein. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: <FriendlyNameOfCredentials>. | 1. Überprüfen Sie, ob die auf der Appliance bereitgestellten Anmeldeinformationen über die Berechtigung zum Erstellen von Dateien für den Ordner \<folder path/folder name> auf dem Server verfügen. <br/>2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über die erforderlichen Berechtigungen verfügen, geben Sie entweder einen anderen Satz von Anmeldeinformationen an, oder bearbeiten Sie einen vorhandenen Satz. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9021**: Die Datei, die die ermittelten Metadaten im richtigen Pfad auf dem Server enthalten muss, kann nicht erstellt werden. | Die VMware-Tools melden einen falschen Dateipfad zum Erstellen der Datei. | Stellen Sie sicher, dass VMware-Tools mit einer höheren Version als 10.2.0 auf dem Server installiert sind und ausgeführt werden. |
| **9022**: Der Zugriff zum Ausführen des Cmdlets Get-WmiObject auf dem Server wird verweigert. | Die Rolle, die den für die Appliance oder eine Gruppenrichtlinie lokal bereitgestellten Anmeldeinformationen zugeordnet ist, schränkt den Zugriff auf das WMI-Objekt ein. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Überprüfen Sie, ob die für die Appliance bereitgestellten Anmeldeinformationen über Administratorrechte zum Erstellen von Dateien verfügen und WMI aktiviert ist. <br/> 2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über die erforderlichen Berechtigungen verfügen, geben Sie entweder einen anderen Satz von Anmeldeinformationen an, oder bearbeiten Sie einen vorhandenen Satz. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.)<br/><br/> [Erfahren Sie mehr](troubleshoot-dependencies.md#error-9022-getwmiobjectaccessdenied) darüber, wie Sie das Problem beheben können.|
| **9023**: PowerShell kann nicht ausgeführt werden, weil der Wert der %SystemRoot%-Umgebungsvariablen leer ist. | Der Wert der %SystemRoot“-Umgebungsvariablen für den Server ist leer. | 1. Überprüfen Sie, ob die Umgebungsvariable einen leeren Wert zurückgibt, indem Sie den Befehl echo %systemroot% auf dem betroffenen Server ausführen. <br/> 2. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9024**: Die Ermittlung kann nicht durchgeführt werden, weil der Wert der %TEMP%-Umgebungsvariablen leer ist. | Der Wert der %TEMP%-Umgebungsvariablen für den Server ist leer. | 1. Überprüfen Sie, ob die Umgebungsvariable einen leeren Wert zurückgibt, indem Sie den Befehl echo %temp% auf dem betroffenen Server ausführen. <br/> 2. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9025**: Die Ermittlung kann nicht ausgeführt werden. PowerShell ist auf dem Server beschädigt. | PowerShell ist auf dem Server beschädigt. | Installieren Sie PowerShell neu, und überprüfen Sie, ob die Anwendung auf dem betroffenen Server ausgeführt wird. |
| **9026**: Auf dem Server können keine Gastvorgänge ausgeführt werden. | Der aktuelle Status des Servers lässt nicht zu, dass Gastvorgänge ausgeführt werden. | 1. Stellen Sie sicher, dass der betroffene Server aktiv ist und ausgeführt wird.<br/> 2. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9027**: Die Metadaten können nicht ermittelt werden, da der Gastbetriebs-Agent nicht auf dem Server ausgeführt wird. | Es konnte keine Verbindung mit dem Gastbetriebs-Agent auf dem Server hergestellt werden. | Stellen Sie sicher, dass VMware-Tools mit einer höheren Version als 10.2.0 auf dem Server installiert sind und ausgeführt werden. |
| **9028**: Die Datei, die die ermittelten Metadaten enthalten muss, kann aufgrund von unzureichendem Speicher auf dem Server nicht erstellt werden. | Es fehlt ausreichend Speicherplatz auf dem Serverdatenträger. | Stellen Sie sicher, dass genügend Speicherplatz im Datenträgerspeicher des betroffenen Servers zur Verfügung steht. |
| **9029**: Die für die Appliance bereitgestellten Anmeldeinformationen verfügen nicht über Zugriffsberechtigungen zum Ausführen von PowerShell. | Die für die Appliance bereitgestellten Anmeldeinformationen verfügen nicht über Zugriffsberechtigungen zum Ausführen von PowerShell. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Stellen Sie sicher, dass die für die Appliance bereitgestellten Anmeldeinformationen auf PowerShell auf dem Server zugreifen können.<br/> 2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über den erforderlichen Zugriff verfügen, geben Sie entweder einen anderen Satz von Anmeldeinformationen an, oder bearbeiten Sie einen vorhandenen Satz. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9030**: Die ermittelten Metadaten können nicht erfasst werden, da sich der ESXi-Host, auf dem der Server gehostet wird, in einem nicht verbundenen Zustand befindet. | Der ESXi-Host, auf dem sich der Server befindet, befindet sich in einem nicht verbundenen Zustand. | Stellen Sie sicher, dass sich der ESXi-Host, der den Server ausführt, in einem verbundenen Zustand befindet. |
| **9031**: Die ermittelten Metadaten können nicht erfasst werden, da sich der ESXi-Host, auf dem der Server gehostet wird, nicht antwortet. | Der ESXi-Host, auf dem sich der Server befindet, befindet sich in einem ungültigen Zustand. | Stellen Sie sicher, dass der ESXi-Host, der den Server ausführt, einen ausgeführten Zustand aufweist und verbunden ist. |
| **9032**: Aufgrund eines internen Fehlers kann keine Ermittlung durchgeführt werden. | Das aufgetretene Problem beruht auf einem internen Fehler. | Führen Sie die [hier](troubleshoot-dependencies.md#error-9032-invalidrequest) beschriebenen Schritte aus, um das Problem zu beheben. Wenn das Problem weiterhin besteht, öffnen Sie eine Microsoft-Supportanfrage. |
| **9033**: Ermittlung kann nicht erfolgen, weil der Benutzername der Anmeldeinformationen, die auf der Appliance für den Server bereitgestellt werden, ungültige Zeichen enthält. | Die auf der Appliance bereitgestellten Anmeldeinformationen enthalten ungültige Zeichen im Benutzernamen. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | Stellen Sie sicher, dass die für die Appliance bereitgestellten Anmeldeinformationen keine ungültigen Zeichen im Benutzernamen enthalten. Sie können zum Appliancekonfigurations-Manager zurückkehren, um die Anmeldeinformationen zu bearbeiten. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9034**: Ermittlung kann nicht erfolgen, weil der Benutzername der Anmeldeinformationen, die auf der Appliance für den Server bereitgestellt werden, nicht das UPN-Format aufweist. | Die für die Appliance bereitgestellten Anmeldeinformationen weisen nicht den Benutzernamen im UPN-Format auf. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | Stellen Sie sicher, dass die für die Appliance bereitgestellten Anmeldeinformationen den Benutzernamen im UPN-Format (User Principal Name) aufweisen. Sie können zum Appliancekonfigurations-Manager zurückkehren, um die Anmeldeinformationen zu bearbeiten. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9035**: Ermittlung kann nicht erfolgen, weil der PowerShell-Sprachmodus nicht richtig festgelegt ist. | Der PowerShell-Sprachmodus ist nicht auf „Full Language“ festgelegt. | Stellen Sie sicher, dass der PowerShell-Sprachmodus auf „FullLanguage“ festgelegt ist. |
| **9036**: Ermittlung kann nicht erfolgen, weil der Benutzername der Anmeldeinformationen, die auf der Appliance für den Server bereitgestellt werden, nicht das UPN-Format aufweist. | Die für die Appliance bereitgestellten Anmeldeinformationen weisen nicht den Benutzernamen im UPN-Format auf. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | Stellen Sie sicher, dass die für die Appliance bereitgestellten Anmeldeinformationen den Benutzernamen im UPN-Format (User Principal Name) aufweisen. Sie können zum Appliancekonfigurations-Manager zurückkehren, um die Anmeldeinformationen zu bearbeiten. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9037**: Die Metadatensammlung wird aufgrund einer langen Antwortzeit vom Server vorübergehend angehalten. | Der Server braucht zu lange, um zu antworten. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **10000**: Der auf dem Server ausgeführte Betriebssystemtyp wird nicht unterstützt. | Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux. | Nur Windows- und Linux-Betriebssystemtypen werden unterstützt. \<GuestOSName>-Betriebssystem wird derzeit nicht unterstützt. |
| **10001**: Das Skript, das zum Erfassen von Ermittlungsmetadaten erforderlich ist, wurde auf dem Server nicht gefunden. | Das Skript, das zum Ausführen der Ermittlung erforderlich ist, wurde möglicherweise gelöscht oder aus dem erwarteten Speicherort entfernt. | Senden Sie einen Microsoft-Supportfall, um dieses Problem zu beheben. |
| **10002**: Für die Ermittlungsvorgänge ist ein Timeout auf dem Server aufgetreten. | Dies kann ein vorübergehendes Problem sein, da der Ermittlungs-Agent auf der Appliance nicht wie erwartet funktioniert. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn dies nicht der Fall ist, führen Sie die [hier](troubleshoot-dependencies.md#error-10002-scriptexecutiontimedoutonvm) beschriebenen Schritte aus, um das Problem zu beheben. Wenn das Problem weiterhin besteht, öffnen Sie eine Microsoft-Supportanfrage.|
| **10003**: Der Prozess zur Ausführung der Ermittlungsvorgänge wurde mit einem Fehler beendet. | Der Prozess zur Ausführung der Ermittlungsvorgänge wurde aufgrund eines Fehlers plötzlich beendet.| Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **10004**: Es wurden keine Anmeldeinformationen für die Appliance für den Serverbetriebssystemtyp angegeben. | Die Anmeldeinformationen für den Serverbetriebssystemtyp wurden auf der Appliance nicht hinzugefügt. | 1. Stellen Sie sicher, dass Sie die Anmeldeinformationen für den Betriebssystemtyp des betroffenen Servers auf der Appliance hinzufügen.<br/> 2. Sie können jetzt mehrere Serveranmeldeinformationen auf der Appliance hinzufügen. |
| **10005**: Die auf der Appliance für den Server bereitgestellten Anmeldeinformationen sind ungültig. | Die auf der Appliance bereitgestellten Anmeldeinformationen sind ungültig. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Stellen Sie sicher, dass die für die Appliance angegebenen Anmeldeinformationen gültig sind und der Zugriff auf den Server mit diesen Anmeldeinformationen möglich ist.<br/> 2. Sie können jetzt mehrere Serveranmeldeinformationen auf der Appliance hinzufügen.<br/> 3. Wechseln Sie zurück zum Appliance-Konfigurations-Manager, um andere Anmeldeinformationen anzugeben oder die vorhandenen Anmeldeinformationen zu bearbeiten. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) <br/><br/> [Erfahren Sie mehr](troubleshoot-dependencies.md#error-10005-guestcredentialnotvalid) darüber, wie Sie das Problem beheben können.|
| **10006**: Der auf dem Server ausgeführte Betriebssystemtyp wird nicht unterstützt. | Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux. | Nur Windows- und Linux-Betriebssystemtypen werden unterstützt. \<GuestOSName>-Betriebssystem wird derzeit nicht unterstützt. |
| **10007**: Die vom Server ermittelten Metadaten können nicht verarbeitet werden. | Fehler beim Analysieren des Inhalts der Datei, die die ermittelten Metadaten enthält. | Senden Sie einen Microsoft-Supportfall, um dieses Problem zu beheben. |
| **10008**: Die Datei, die die ermittelten Metadaten auf dem Server enthalten muss, kann nicht erstellt werden. | Die Rolle, die mit den für die Appliance oder eine Gruppenrichtlinie lokal bereitgestellten Anmeldeinformationen verknüpft ist, schränkt die Erstellung von Dateien im erforderlichen Ordner ein. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: <FriendlyNameOfCredentials>. | 1. Überprüfen Sie, ob die auf der Appliance bereitgestellten Anmeldeinformationen über die Berechtigung zum Erstellen von Dateien für den Ordner \<folder path/folder name> auf dem Server verfügen.<br/> 2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über die erforderlichen Berechtigungen verfügen, geben Sie entweder einen anderen Satz von Anmeldeinformationen an, oder bearbeiten Sie einen vorhandenen Satz. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **10009**: Die ermittelten Metadaten können in der Datei auf dem Server nicht geschrieben werden. | Die Rolle, die den für die Appliance oder eine Gruppenrichtlinie lokal bereitgestellten Anmeldeinformationen zugeordnet ist, schränkt das Schreiben in die Datei auf dem Server ein. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Überprüfen Sie, ob die auf der Appliance bereitgestellten Anmeldeinformationen über Schreibberechtigung für den Ordner <Ordnerpfad/Ordnername> auf dem Server verfügen.<br/> 2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über die erforderlichen Berechtigungen verfügen, geben Sie entweder einen anderen Satz von Anmeldeinformationen an, oder bearbeiten Sie einen vorhandenen Satz. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **10010**: Ermittlung kann nicht erfolgen, weil der Befehl %CommandName;, der zum Erfassen von Metadaten erforderlich ist, auf dem Server fehlt. | Das Paket, das den Befehl %CommandName; enthält, ist nicht auf dem Server installiert. | Stellen Sie sicher, dass das Paket, das den Befehl %CommandName; enthält, auf dem Server installiert ist. |
| **10011**: Die auf der Appliance bereitgestellten Anmeldeinformationen wurden verwendet, um sich für eine interaktive Sitzung anzumelden und abzumelden. | Die interaktive Anmeldung und Abmeldung erzwingt, dass die Registrierungsschlüssel im Profil des Kontos entladen werden, das verwendet wird. Durch diese Bedingung sind die Schlüssel für zukünftige Verwendung nicht mehr verfügbar. | Verwenden Sie die [hier](/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error#resolutionus/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error) dokumentierten Lösungsmethoden. |
| **10012**: Es wurden keine Anmeldeinformationen für die Appliance für den Server angegeben. | Entweder wurden keine Anmeldeinformationen für den Server angegeben, oder Sie haben Domänenanmeldeinformationen mit falschem Domänennamen auf der Appliance angegeben. [Erfahren Sie mehr](troubleshoot-dependencies.md#error-10012-credentialnotprovided) über die Ursache dieses Fehlers. | 1. Stellen Sie sicher, dass Anmeldeinformationen für die Appliance für den Server angegeben werden und der Zugriff auf den Server mit diesen Anmeldeinformationen möglich ist. <br/> 2. Sie können jetzt mehrere Anmeldeinformationen auf der Appliance für Server hinzufügen. Wechseln Sie zurück zum Appliancekonfigurations-Manager, um Anmeldeinformationen für den Server anzugeben.|


## <a name="error-970-dependencymapinsufficientprivilegesexception"></a>Error 970: DependencyMapInsufficientPrivilegesException

### <a name="cause"></a>Ursache
Der Fehler tritt in der Regel bei Linux-Servern auf, wenn Sie keine Anmeldeinformationen mit den erforderlichen Berechtigungen für die Appliance angegeben haben.

### <a name="remediation"></a>Behebung
- Stellen Sie sicher, dass Sie entweder ein Stammbenutzerkonto angegeben haben, ODER
- Ein Konto mit den folgenden Berechtigungen für Dateien in „/bin/netstat“ und „/bin/ls“:
   - CAP_DAC_READ_SEARCH
   - CAP_SYS_PTRACE
- Führen Sie die folgenden Schritte aus, um zu überprüfen, ob das auf der Appliance bereitgestellte Benutzerkonto über die erforderlichen Berechtigungen verfügt:
1. Melden Sie sich bei dem Server, auf dem dieser Fehler aufgetreten ist, mit demselben Benutzerkonto wie in der Fehlermeldung erwähnt an.
2. Führen Sie in der Shell die folgenden Befehle aus. Wenn Sie nicht über die erforderlichen Berechtigungen für Abhängigkeitsanalyse ohne Agent verfügen, erhalten Sie Fehler:

   ````
   ps -o pid,cmd | grep -v ]$
   netstat -atnp | awk '{print $4,$5,$7}'
   ````
3. Legen Sie die erforderlichen Berechtigungen für die Dateien „/bin/netstat“ und „/bin/ls“ fest, indem Sie die folgenden Befehle ausführen:

   ````
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
   ````
4. Sie können überprüfen, ob die oben genannten Befehle dem Benutzerkonto die erforderlichen Berechtigungen zugewiesen haben:

   ````
   getcap /usr/bin/ls
   getcap /usr/bin/netstat
   ````
5. Führen Sie die in Schritt 2 bereitgestellten Befehle erneut aus, um eine erfolgreiche Ausgabe zu erhalten.


## <a name="error-9014-httpgetrequesttoretrievefilefailed"></a>Error 9014: HTTPGetRequestToRetrieveFileFailed

### <a name="cause"></a>Ursache
Das Problem tritt auf, wenn der VMware-Ermittlungs-Agent in der Appliance versucht, die Ausgabedatei mit Abhängigkeitsdaten aus dem Serverdateisystem über den ESXi-Host herunterzuladen, auf dem der Server gehostet wird.

### <a name="remediation"></a>Behebung
- Sie können die TCP-Konnektivität mit dem ESXi-Host _(in der Fehlermeldung angegebener Name)_ an Port 443 (muss auf ESXi-Hosts geöffnet sein, um Abhängigkeitsdaten zu pullen) von der Appliance testen, indem Sie PowerShell auf dem Applianceserver öffnen und den folgenden Befehl ausführen:
    ````
    Test -NetConnection -ComputeName <Ip address of the ESXi host> -Port 443
    ````
- Wenn der Befehl erfolgreiche Konnektivität zurückgibt, können Sie zum „Azure Migrate-Projekt > Ermittlung und Bewertung > Übersicht > Verwalten > Appliances“ navigieren, den Appliancenamen auswählen und **Dienste aktualisieren** auswählen.

## <a name="error-9018-powershellnotfound"></a>Error 9018: PowerShellNotFound

### <a name="cause"></a>Ursache
Der Fehler tritt in der Regel bei Servern auf, auf denen Windows Server 2008 oder niedriger ausgeführt wird.

### <a name="remediation"></a>Behebung
Sie müssen die erforderliche PowerShell-Version (2.0 oder höher) an diesem Speicherort auf dem Server installieren: ($SYSTEMROOT)\System32\WindowsPowershell\v1.0\powershell.exe. [Erfahren Sie mehr](/powershell/scripting/windows-powershell/install/installing-windows-powershell) über die Installation von PowerShell in Windows Server.

Nachdem Sie die erforderliche PowerShell-Version installiert haben, können Sie überprüfen, ob der Fehler behoben wurde, indem Sie die [hier](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli) beschriebenen Schritte ausführen.

## <a name="error-9022-getwmiobjectaccessdenied"></a>Error 9022: GetWMIObjectAccessDenied

### <a name="remediation"></a>Behebung
Stellen Sie sicher, dass das in der Appliance bereitgestellte Benutzerkonto Zugriff auf den WMI-Namespace und Unternamespaces besitzt. Sie können den Zugriff festlegen, indem Sie die folgenden Schritte ausführen:
1.  Wechseln Sie zu dem Server, der diesen Fehler meldet.
2.  Suchen Sie im Startmenü nach „Ausführen“, und wählen Sie diese Option aus. Geben Sie im Dialogfeld „Ausführen“ „wmimgmt.msc“ in das Textfeld „Öffnen:“ ein, und drücken Sie die EINGABETASTE.
3.  Die wmimgmt-Konsole wird geöffnet, in der im linken Bereich „WMI-Steuerung (lokal)“ angezeigt wird. Klicken Sie mit der rechten Maustaste darauf, und wählen Sie im Menü „Eigenschaften“ aus.
4.  Wählen Sie im Dialogfeld „Eigenschaften von WMI-Steuerung (lokal)“ die Registerkarte „Sicherheiten“ aus.
5.  Wählen Sie auf der Registerkarte „Sicherheiten“ die Schaltfläche „Sicherheit“ aus, um das Dialogfeld „Sicherheit für ROOT“ zu öffnen.
7.  Klicken Sie auf die Schaltfläche „Erweitert“, um das Dialogfeld „Erweiterte Sicherheitseinstellungen für Root“ zu öffnen. 
8.  Wählen Sie die Schaltfläche „Hinzufügen“ aus, um das Dialogfeld „Berechtigungseintrag für Root“ zu öffnen.
9.  Klicken Sie auf „Prinzipal auswählen“, um das Dialogfeld „Benutzer, Computer, Dienstkonten oder Gruppen auswählen“ zu öffnen.
10. Wählen Sie die Benutzernamen oder Gruppen aus, denen Sie Zugriff auf die WMI gewähren möchten, und klicken Sie auf „OK“.
11. Stellen Sie sicher, dass Sie Ausführungsberechtigungen erteilen, und wählen Sie „Dieser Namespace und Unternamespaces“ in der Dropdownliste „Gilt für:“ aus.
12. Wählen Sie die Schaltfläche „Übernehmen“ aus, um die Einstellungen zu speichern und alle Dialogfelder zu schließen.

Nachdem Sie den erforderlichen Zugriff erhalten haben, können Sie überprüfen, ob der Fehler behoben wurde, indem Sie die [hier](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli) beschriebenen Schritte ausführen.

## <a name="error-9032-invalidrequest"></a>Error 9032: InvalidRequest

### <a name="cause"></a>Ursache
Es kann mehrere Gründe für dieses Problem geben. Einer der Gründe ist, dass der im Appliancekonfigurations-Manager angegebene Benutzername (Serveranmeldeinformationen) ungültige XML-Zeichen aufweist, was zu einem Fehler beim Analysieren der SOAP-Anforderung führt.

### <a name="remediation"></a>Behebung
- Vergewissern Sie sich, dass der Benutzername der Serveranmeldeinformationen keine ungültigen XML-Zeichen enthält und im username@domain.com-Format vorliegt, das auch als UPN-Format bekannt ist.
- Nachdem Sie die Anmeldeinformationen auf der Appliance bearbeitet haben, können Sie überprüfen, ob der Fehler behoben wurde, indem Sie die [hier](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli) beschriebenen Schritte ausführen.


## <a name="error-10002-scriptexecutiontimedoutonvm"></a>Error 10002: ScriptExecutionTimedOutOnVm

### <a name="cause"></a>Ursache
- Dieser Fehler tritt auf, wenn der Server langsam ist oder nicht reagiert und für das Skript, das zum Pullen der Abhängigkeitsdaten ausgeführt wird, ein Timeout auftritt.
- Sobald der Ermittlungs-Agent diesen Fehler auf dem Server erkennt, versucht die Appliance danach keine Abhängigkeitsanalyse ohne Agent auf dem Server, um eine Überlastung des nicht reagierenden Servers zu vermeiden.
- Daher tritt der Fehler weiterhin auf, bis Sie das Problem mit dem Server überprüfen und den Ermittlungsdienst neu starten.

### <a name="remediation"></a>Behebung
1. Melden Sie sich bei dem Server an, auf dem dieser Fehler auftritt.
1. Führen Sie die folgenden Befehle in PowerShell aus:
   ````
   Get-WMIObject win32_operatingsystem;
   Get-WindowsFeature  | Where-Object {$_.InstallState -eq 'Installed' -or ($_.InstallState -eq $null -and $_.Installed -eq 'True')};
   Get-WmiObject Win32_Process;
   netstat -ano -p tcp | select -Skip 4;
   ````
3. Wenn Befehle das Ergebnis nach wenigen Sekunden ausgeben, können Sie zu „Azure Migrate-Projekt > Ermittlung und Bewertung > Übersicht > Verwalten > Appliances“ navigieren, den Appliancenamen auswählen und dann **Dienste aktualisieren** auswählen, um den Ermittlungsdienst neu zu starten.
4. Wenn für die Befehle ein Timeout auftritt, ohne dass eine Ausgabe erfolgt 
- Sie müssen herausfinden, welcher Prozess eine hohe CPU- oder Arbeitsspeicherauslastung auf dem Server verursacht.
- Sie können versuchen, für diesen Server mehr Kerne/Arbeitsspeicher bereitzustellen, und die Befehle erneut ausführen.

## <a name="error-10005-guestcredentialnotvalid"></a>Error 10005: GuestCredentialNotValid

### <a name="remediation"></a>Behebung
- Stellen Sie die Gültigkeit der Anmeldeinformationen _(der Anzeigename wird im Fehler angegeben)_ sicher, indem Sie im Konfigurations-Manager der Appliance auf „Anmeldeinformationen erneut validieren“ klicken.
- Stellen Sie sicher, dass Sie sich mit den gleichen Anmeldeinformationen, die in der Appliance angegeben sind, beim betroffenen Server anmelden können.
- Sie können versuchen, anstelle des Administratorkontos ein anderes Benutzerkonto (für dieselbe Domäne, wenn der Server in die Domäne eingebunden ist) für diesen Server zu verwenden.
- Das Problem kann auftreten, wenn die Kommunikation zwischen dem globalen Katalog und dem Domänencontroller unterbrochen ist. Sie können dies überprüfen, indem Sie ein neues Benutzerkonto im Domänencontroller erstellen und dasselbe Konto in der Appliance bereitstellen. Möglicherweise ist auch einen Neustart des Domänencontrollers erforderlich.
- Nachdem Sie die Wartungsschritte ausgeführt haben, können Sie überprüfen, ob der Fehler behoben wurde, indem Sie die [hier](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli) beschriebenen Schritte ausführen.

## <a name="error-10012-credentialnotprovided"></a>Error 10012: CredentialNotProvided

### <a name="cause"></a>Ursache
Dieser Fehler tritt auf, wenn Sie Domänenanmeldeinformationen mit einem falschen Domänennamen für den Appliancekonfigurations-Manager angegeben haben. Wenn Sie z. B. Domänenanmeldeinformationen mit dem Benutzernamen user@abc.com angegeben haben, der Domänenname jedoch als def.com angegeben wurde, werden diese Anmeldeinformationen nicht verwendet, wenn der Server mit def.com verbunden ist, und Sie erhalten diese Fehlermeldung.

### <a name="remediation"></a>Behebung
- Navigieren Sie zum Appliancekonfigurations-Manager, um Serveranmeldeinformationen hinzuzufügen oder vorhandene Anmeldeinformationen wie in der Ursache erläutert zu bearbeiten.
- Nachdem Sie die Wartungsschritte ausgeführt haben, können Sie überprüfen, ob der Fehler behoben wurde, indem Sie die [hier](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli) beschriebenen Schritte ausführen.

## <a name="mitigation-verification-using-vmware-powercli"></a>Entschärfungsüberprüfung mit VMware PowerCLI

Nachdem Sie die Schritte zur Behebung der oben aufgeführten Fehler durchgeführt haben, können Sie überprüfen, ob die Fehler behoben wurden, indem Sie über den Applianceserver einige PowerCLI-Befehle ausführen. Wenn die Befehle erfolgreich ausgeführt werden, bedeutet dies, dass das Problem jetzt behoben ist. Andernfalls müssen Sie die Überprüfung wiederholen und die Wartungsschritte erneut ausführen.

1. Führen Sie die folgenden Befehle aus, um PowerCLI auf dem Applianceserver einrichten:
   ````
   Install-Module -Name VMware.PowerCLI -AllowClobber
   Set-PowerCLIConfiguration -InvalidCertificateAction Ignore
   ````
2. Stellen Sie von der Appliance aus eine Verbindung mit vCenter Server her, indem Sie die IP-Adresse von vCenter Server im Befehl und die Anmeldeinformationen in der Eingabeaufforderung angeben:
   ````
   Connect-VIServer -Server <IPAddress of vCenter Server>
   ````
3. Stellen Sie von der Appliance aus eine Verbindung mit dem Zielserver her, indem Sie den Servernamen und die Serveranmeldeinformationen (wie für die Appliance angegeben) angeben:
   ````
   $vm = get-VM <VMName>
   $credential = Get-Credential
   ````
4. Führen Sie für Abhängigkeitsanalyse ohne Agent die folgenden Befehle aus, um eine erfolgreiche Ausgabe zu bestätigen:

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
5. Nachdem Sie bestätigt haben, dass die Entschärfung funktioniert hat, können Sie zu „Azure Migrate-Projekt > Ermittlung und Bewertung > Übersicht > Verwalten > Appliances“ navigieren, den Appliancenamen auswählen und dann **Dienste aktualisieren** auswählen, um einen neuen Ermittlungszyklus zu starten.


## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-azure-migrate-for-agent-based-dependency-analysis"></a>Beim Versuch, den Log Analytics-Arbeitsbereich in Azure Migrate zu konfigurieren, wird der Arbeitsbereich für Abhängigkeitsanalyse ohne Agent nicht aufgeführt
Azure Migrate unterstützt derzeit die Erstellung von OMS-Arbeitsbereichen in den Regionen „USA, Osten“, „Asien, Südosten“ und „Europa, Westen“. Wenn der Arbeitsbereich außerhalb von Azure Migrate in einer beliebigen anderen Region erstellt wird, kann er derzeit keinem Projekt zugeordnet werden.

## <a name="agent-based-dependency-visualization-in-azure-government"></a>Agent-basierte Abhängigkeitsvisualisierung in Azure Government

Die Agent-basierte Abhängigkeitsanalyse wird in Azure Government nicht unterstützt. Verwenden Sie die Abhängigkeitsanalyse ohne Agent _(nur für VMware-Server verfügbar)_ .

## <a name="agent-based-dependencies-dont-show-after-agent-install"></a>Agent-basierte Abhängigkeiten werden nicht nach der Installation von Agents nicht angezeigt

Nachdem Sie die Agents für die Abhängigkeitsvisualisierung auf lokalen virtuellen Computern installiert haben, benötigt Azure Migrate üblicherweise 15 bis 30 Minuten, um die Abhängigkeiten im Portal anzuzeigen. Stellen Sie nach einer Wartezeit von mehr als 30 Minuten sicher, dass Microsoft Monitoring Agent (MMA) eine Verbindung zum Log Analytics-Arbeitsbereich herstellen kann.

Für virtuelle Windows-Computer:
1. Starten Sie MMA in der Systemsteuerung.
2. Stellen Sie unter **Microsoft Monitoring Agent-Eigenschaften** > **Azure Log Analytics (OMS)** sicher, dass der **Status** für den Arbeitsbereich grün angezeigt wird.
3. Wenn der Status nicht grün angezeigt wird, entfernen Sie den Arbeitsbereich, und fügen Sie ihn erneut zu MMA hinzu.

    ![MMA-Status](./media/troubleshoot-assessment/mma-properties.png)

Stellen Sie bei virtuellen Linux-Computern sicher, dass die Installationsbefehle für MMA und den Abhängigkeits-Agent erfolgreich ausgeführt wurden. Weitere Informationen zur Problembehandlung finden Sie [hier](../azure-monitor/vm/service-map.md#post-installation-issues).

## <a name="supported-operating-systems-for-agent-based-dependency-analysis"></a>Unterstützte Betriebssysteme für Agent-basierte Abhängigkeitsanalyse

- **MMS-Agent**: Überprüfen Sie die unterstützten [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)- und [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems)-Betriebssysteme.
- **Dependency-Agent**: die unterstützten [Windows- und Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems)-Betriebssysteme.

## <a name="visualize-dependencies-for--1-hour-with-agent-based-dependency-analysis"></a>Visualisieren von Abhängigkeiten für > 1 Stunde mit Agent-basierter Abhängigkeitsanalyse

Obwohl Sie bei der Abhängigkeitsanalyse ohne Agent mit Azure Migrate um bis zu einen Monat zu einem bestimmten Datum zurückgehen können, können Sie die Abhängigkeiten jedoch nur für einen Zeitraum von maximal einer Stunde visualisieren. So können Sie beispielsweise mithilfe der Zeitraumfunktionalität im Abhängigkeitsdiagramm Abhängigkeiten für den gestrigen Tag, jedoch nur für einen Zeitraum von einer Stunde, anzeigen. Sie können jedoch zum [Abfragen der Abhängigkeitsdaten](./how-to-create-group-machine-dependencies.md) über einen längeren Zeitraum Azure Monitor-Protokolle verwenden.

## <a name="visualized-dependencies-for--10-servers-with-agent-based-dependency-analysis"></a>Visualisieren von Abhängigkeiten für > 10 Server mit Agent-basierter Abhängigkeitsanalyse

Sie können in Azure Migrate mit der Abhängigkeitsanalyse ohne Agent [Abhängigkeiten für Gruppen](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) mit bis zu zehn VMs visualisieren. Bei größeren Gruppen empfiehlt es sich für die visuelle Darstellung von Abhängigkeiten, die virtuellen Computer in kleinere Gruppen aufzuteilen.

## <a name="servers-show-install-agent-for-agent-based-dependency-analysis"></a>Server zeigen „Agent installieren“ für Agent-basierte Abhängigkeitsanalyse an

Nach dem Migrieren von Servern mit aktivierter Abhängigkeitsvisualisierung zu Azure zeigen Server möglicherweise aufgrund des folgenden Verhaltens die Aktion „Agent installieren“ anstelle von „Abhängigkeiten anzeigen“ an:

- Nach der Migration zu Azure werden die lokalen Server ausgeschaltet, und die entsprechenden virtuellen Computer werden in Azure hochgefahren. Diese Server beziehen eine andere MAC-Adresse.
- Server haben möglicherweise auch eine andere IP-Adresse, je nachdem, ob Sie die lokale IP-Adresse beibehalten haben oder nicht.
- Wenn sich sowohl Mac- als auch IP-Adressen von den lokalen Adressen unterscheiden, verknüpft Azure Migrate die lokalen Server nicht mit Service Map-Abhängigkeitsdaten. In diesem Fall wird die Option zum Installieren des Agents anstatt die Option zum Anzeigen von Abhängigkeiten angezeigt.
- Nach einer Testmigration zu Azure bleiben die lokalen Server erwartungsgemäß eingeschaltet. Entsprechende Server, die in Azure hochgefahren wurden, erhalten andere MAC-Adressen und ggf. auch andere IP-Adressen. Sofern Sie ausgehenden Datenverkehr von Azure Monitor-Protokollen von diesen Servern nicht sperren, ordnet Azure Migrate den lokalen Servern keine Service Map-Abhängigkeitsdaten zu und zeigt daher die Option zum Installieren von Agents anstelle der Option zum Anzeigen von Abhängigkeiten an.

## <a name="capture-network-traffic"></a>Erfassen des Netzwerkdatenverkehrs

Sammeln Sie Protokolle zum Netzwerkdatenverkehr wie folgt:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Drücken Sie F12, um die Entwicklertools zu starten. Deaktivieren Sie ggf. die Einstellung **Einträge beim Navigieren löschen**.
3. Wählen Sie die Registerkarte **Netzwerk** aus, und starten Sie die Erfassung von Netzwerkdatenverkehr:
   - Wählen Sie in Chrome die Option **Protokoll speichern**. Die Aufzeichnung sollte automatisch gestartet werden. Ein roter Kreis gibt an, dass der Datenverkehr erfasst wird. Wenn der rote Kreis nicht angezeigt wird, wählen Sie zum Starten den schwarzen Kreis aus.
   - In Microsoft Edge und Internet Explorer sollte die Aufzeichnung automatisch gestartet werden. Ist dies nicht der Fall, wählen Sie die grüne Wiedergabeschaltfläche aus.
4. Versuchen Sie, den Fehler zu reproduzieren.
5. Nachdem der Fehler während der Aufzeichnung aufgetreten ist, beenden Sie die Aufzeichnung, und speichern Sie eine Kopie der aufgezeichneten Aktivität:
   - Klicken Sie in Chrome mit der rechten Maustaste, und wählen Sie **Als HAR mit Inhalt speichern** aus. Durch diese Aktion werden die Protokolle komprimiert und als HTTP-Archiv (HAR-Datei) exportiert.
   - Wählen Sie in Microsoft Edge oder Internet Explorer das Symbol **Aufgezeichneten Datenverkehr exportieren** aus. Durch diese Aktion werden die Protokolle komprimiert und exportiert.
6. Wählen Sie die Registerkarte **Konsole** aus, um sie auf Warnungen oder Fehler zu überprüfen. So speichern Sie das Konsolenprotokoll:
   - Klicken Sie in Chrome mit der rechten Maustaste auf eine beliebige Stelle im Konsolenprotokoll. Wählen Sie **Speichern unter** aus, um das Protokoll zu exportieren und zu komprimieren.
   - Klicken Sie in Microsoft Edge oder Internet Explorer mit der rechten Maustaste auf die Fehler, und wählen Sie **Alle kopieren** aus.
7. Schließen Sie die Entwicklertools.


## <a name="next-steps"></a>Nächste Schritte

[Erstellen](how-to-create-assessment.md) Sie eine Bewertung oder [passen](how-to-modify-assessment.md) Sie sie an.