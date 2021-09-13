---
title: Problembehandlung bei der laufenden Serverermittlung, Softwareinventur und SQL-Ermittlung
description: Hilfe zur Serverermittlung, Softwareinventur und SQL- und Web-Apps-Ermittlung
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 124ca1ad5f8af9ab2dd08382588c8933db8a09eb
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122343594"
---
# <a name="troubleshoot-the-ongoing-server-discovery-software-inventory-sql-and-web-app-discovery"></a>Problembehandlung bei der laufenden Serverermittlung, Softwareinventur, SQL- und Web-App-Ermittlung

Dieser Artikel hilft Ihnen bei der Behandlung von Problemen mit der laufenden Serverermittlung, der Softwareinventur und der Ermittlung von SQL Server-Instanzen und -Datenbanken.

## <a name="discovered-servers-not-showing-in-portal"></a>Ermittelte Server werden nicht im Portal angezeigt

**Fehler**

Dieser Fehler wird angezeigt, wenn die Server noch nicht im Portal angezeigt werden und der Ermittlungsstatus **Ermittlung wird ausgeführt** angezeigt wird.
 
**Wartung**

Wenn die Server nicht im Portal angezeigt werden, warten Sie einige Minuten, da die Ermittlung von Servern, die auf einem vCenter Server ausgeführt werden, etwa 15 Minuten dauert, 2 Minuten für jeden Hyper-V-Host, hinzugefügt auf der Appliance für die Ermittlung von Servern, die auf dem Host ausgeführt werden, und eine Minute für die Ermittlung der einzelnen Server, die auf dem physischen Gerät hinzugefügt wurden.

Wenn sich der Status immer noch nicht ändert, gehen Sie wie folgt vor:

- Wählen Sie **Aktualisieren** auf der Registerkarte **Server** aus, um die Anzahl der ermittelten Server „Azure Migrate: Ermittlung und Bewertung“ und „Azure Migrate: Servermigration“ anzuzeigen.

Wenn der obige Schritt nicht funktioniert und Sie VMware-Server ermitteln:

1. Stellen Sie sicher, dass das von Ihnen angegebene vCenter-Konto über ordnungsgemäß festgelegte Berechtigungen mit Zugriff auf mindestens einen Server verfügt.
1. Überprüfen Sie in VMware, ob dem vCenter-Konto auf Ebene des vCenter-VM-Ordners Zugriff gewährt wird. Azure Migrate kann Server auf VMware nicht ermitteln. [Weitere Informationen](set-discovery-scope.md) zur Bereichsermittlung.

## <a name="server-data-not-updating-in-portal"></a>Serverdaten werden im Portal nicht aktualisiert

**Fehler**

Dieser Fehler wird angezeigt, wenn die ermittelten Server nicht im Portal angezeigt werden oder die Serverdaten veraltet sind. 

**Wartung**

Warten Sie einige Minuten, da es bis zu 30 Minuten dauert, bis Änderungen an den ermittelten Serverkonfigurationsdaten im Portal angezeigt werden, und einige Stunden, bis Änderungen an den Softwareinventurdaten angezeigt werden. Sollten nach dieser Zeit immer noch keine Daten vorhanden sein, aktualisieren Sie die Daten wie folgt:

1. Wählen Sie unter **Windows, Linux und SQL Server**, **Azure Migrate: Ermittlung und Bewertung**, die Option **Übersicht** aus.
1. Wählen Sie unter **Verwalten** die Option **Appliances** aus.
1. Wählen Sie **Dienste aktualisieren** aus.
Warten Sie, bis der Aktualisierungsvorgang abgeschlossen wurde. Sie sollten nun aktuelle Informationen sehen.

## <a name="deleted-servers-appear-in-portal"></a>Gelöschte Server werden im Portal angezeigt

**Fehler**

Dieser Fehler wird angezeigt, wenn die gelöschten Server weiterhin im Portal angezeigt werden.

**Wartung**

Wenn die Daten weiterhin angezeigt werden, warten Sie 30 Minuten, und gehen Sie wie folgt vor:

Wählen Sie unter **Windows, Linux und SQL Server**, **Azure Migrate: Ermittlung und Bewertung**, die Option **Übersicht** aus.
1. Wählen Sie unter **Verwalten** die Option **Appliances** aus.
1. Wählen Sie **Dienste aktualisieren** aus.
Warten Sie, bis der Aktualisierungsvorgang abgeschlossen wurde. Sie sollten nun aktuelle Informationen sehen.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Ich habe eine CSV-Datei importiert, sehe aber, dass die Ermittlung ausgeführt wird

Dieser Status wird angezeigt, wenn das Hochladen Ihrer CSV-Datei aufgrund eines Überprüfungsfehlers fehlgeschlagen ist. 

**Wartung**

Importieren Sie die CSV-Datei noch mal. Sie können den Fehlerbericht des vorherigen Hochladevorgangs herunterladen und der Anleitung zur Fehlerbehebung in der Datei folgen, um die Fehler zu beheben. Laden Sie den Fehlerbericht im Abschnitt **Importdetails** auf der Seite **Server ermitteln** herunter.

## <a name="do-not-see-software-inventory-details-even-after-updating-guest-credentials"></a>Es werden auch nach dem Aktualisieren der Gastanmeldeinformationen keine Softwareinventardetails angezeigt

**Wartung**

Die Softwareinventarermittlung wird einmal alle 24 Stunden ausgeführt. Je nach Anzahl der ermittelten Server kann dies einige Minuten dauern. Wenn die Details sofort angezeigt werden sollen, führen Sie die folgenden Schritte zur Aktualisierung aus.

1. Wählen Sie unter **Windows, Linux und SQL Server**, **Azure Migrate: Ermittlung und Bewertung**, die Option **Übersicht** aus.
1. Wählen Sie unter **Verwalten** die Option **Appliances** aus.
1. Wählen Sie **Dienste aktualisieren** aus.
Warten Sie, bis der Aktualisierungsvorgang abgeschlossen wurde. Sie sollten nun aktuelle Informationen sehen.

## <a name="unable-to-export-software-inventory"></a>Export des Softwareinventars nicht möglich

**Fehler**

Dieser Fehler wird angezeigt, wenn Sie nicht über die Berechtigung „Mitwirkender“ verfügen.

**Wartung**

Stellen Sie sicher, dass der Benutzer, der den Bestand aus dem Portal herunterlädt, über die Berechtigung „Mitwirkender“ für das Abonnement verfügt.

## <a name="common-software-inventory-errors"></a>Häufige Softwareinventurfehler

Azure Migrate unterstützt die Softwareinventur mit der Azure Migrate-Ermittlung und -Bewertung. Die Softwareinventur wird derzeit nur für VMware unterstützt. [Weitere Informationen](how-to-discover-applications.md) zu den Anforderungen für die Softwareinventur.

Die Liste der Softwareinventurfehler ist in der folgenden Tabelle zusammengefasst.

> [!Note]
> Die gleichen Fehler können auch bei der Abhängigkeitsanalyse ohne Agent auftreten, da sie der gleichen Methodik wie die Softwareinventur folgt, um die erforderlichen Daten zu erfassen.

| **Fehler** | **Ursache** | **Aktion** |
|--|--|--|
| **9000**: Der Status der VMware Tools auf dem Server kann nicht erkannt werden. | VMware Tools sind möglicherweise nicht auf dem Server installiert, oder die installierte Version ist beschädigt. | Stellen Sie sicher, dass die VMware Tools mit einer höheren Version als 10.2.1 auf dem Server installiert sind und ausgeführt werden. |
| **9001**: VMware Tools sind nicht auf dem System installiert. | VMware Tools sind möglicherweise nicht auf dem Server installiert, oder die installierte Version ist beschädigt. | Stellen Sie sicher, dass die VMware Tools mit einer höheren Version als 10.2.1 auf dem Server installiert sind und ausgeführt werden. |
| **9002**: VMware Tools werden nicht auf dem Server ausgeführt. | VMware Tools sind möglicherweise nicht auf dem Server installiert, oder die installierte Version ist beschädigt. | Stellen Sie sicher, dass VMware Tools mit einer höheren Version als 10.2.0 auf dem Server installiert sind und ausgeführt werden. |
| **9003**: Der auf dem Server ausgeführte Betriebssystemtyp wird nicht unterstützt. | Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux. | Nur Windows- und Linux-Betriebssystemtypen werden unterstützt. Wenn der Server tatsächlich Windows oder Linux verwendet, überprüfen Sie den in vCenter Server angegebenen Betriebssystemtyp. |
| **9004**: Der Server weist keinen Ausführungsstatus auf. | Der Server ist ausgeschaltet. | Stellen Sie sicher, dass der Server ausgeführt wird. |
| **9005**: Der auf dem Server ausgeführte Betriebssystemtyp wird nicht unterstützt. | Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux. | Nur Windows- und Linux-Betriebssystemtypen werden unterstützt. \<FetchedParameter>-Betriebssystem wird derzeit nicht unterstützt. |
| **9006**: Die URL, die zum Herunterladen der Ermittlungsmetadatendatei vom Server erforderlich ist, ist leer. | Dies kann ein vorübergehendes Problem sein, da der Ermittlungs-Agent auf der Appliance nicht wie erwartet funktioniert. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9007**: Der Prozess, mit dem das Skript zum Erfassen der Metadaten ausgeführt wird, wurde auf dem Server nicht gefunden. | Dies kann ein vorübergehendes Problem sein, da der Ermittlungs-Agent auf der Appliance nicht wie erwartet funktioniert. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9008**: Der Status des Prozesses, der zum Erfassen der Metadaten auf dem Server ausgeführt wird, kann nicht abgerufen werden. | Dies kann ein vorübergehendes Problem aufgrund eines internen Fehlers sein. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9009**: Die Windows-Benutzerkontensteuerung (UAC) verhindert die Ausführung von Ermittlungsvorgängen auf dem Server. | Die Einstellungen für die Windows-Benutzerkontensteuerung (UAC) verhindern die Ermittlung installierter Anwendungen auf dem Server. | Verringern Sie auf dem beeinträchtigten Server die Ebene der Einstellungen für die Benutzerkontensteuerung in der Systemsteuerung. |
| **9010**: Der Server ist ausgeschaltet. | Der Server ist ausgeschaltet. | Stellen Sie sicher, dass der Server eingeschaltet ist. |
| **9011**: Die Datei, die die ermittelten Metadaten enthält, kann auf dem Server nicht gefunden werden. | Dies kann ein vorübergehendes Problem aufgrund eines internen Fehlers sein. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9012**: Die Datei, die die ermittelten Metadaten auf dem Server enthält, ist leer. | Dies kann ein vorübergehendes Problem aufgrund eines internen Fehlers sein. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9013**: Bei jeder Anmeldung auf dem Server wird ein neues temporäres Benutzerprofil erstellt. | Bei jeder Anmeldung auf dem Server wird ein neues temporäres Benutzerprofil erstellt. | Senden Sie eine Microsoft-Supportanfrage, um dieses Problem zu beheben. |
| **9014**: Die Datei mit den ermittelten Metadaten kann aufgrund eines Fehlers auf dem ESXi-Host nicht abgerufen werden.  Fehlercode: %ErrorCode; Details: %ErrorMessage | Auf dem ESXi-Host \<HostName> ist ein Fehler aufgetreten.  Fehlercode: %ErrorCode; Details: %ErrorMessage | Stellen Sie sicher, dass Port 443 auf dem ESXi-Host geöffnet ist, auf dem der Server ausgeführt wird.<br/><br/> [Erfahren Sie mehr](troubleshoot-discovery.md#error-9014-httpgetrequesttoretrievefilefailed) darüber, wie Sie das Problem beheben können.|
| **9015**: Für das vCenter Server-Benutzerkonto, das für die Serverermittlung bereitgestellt wird, sind keine Berechtigungen für Gastvorgänge aktiviert. | Die erforderlichen Berechtigungen für Gastvorgänge wurden für das vCenter Server-Benutzerkonto nicht aktiviert. | Stellen Sie sicher, dass für das vCenter Server-Benutzerkonto Berechtigungen für „Virtual Machines > Gastvorgänge“ aktiviert sind, um mit dem Server zu interagieren und die erforderlichen Daten zu pullen. <br/><br/> [Erfahren Sie mehr](tutorial-discover-vmware.md#prepare-vmware) über das Einrichten des vCenter Server-Kontos mit den erforderlichen Berechtigungen. |
| **9016**: Die Metadaten können nicht ermittelt werden, da der Agent für Gastvorgänge auf dem Server veraltet ist. | Entweder sind die VMware Tools nicht auf dem Server installiert, oder die installierte Version ist nicht auf dem neuesten Stand. | Stellen Sie sicher, dass die VMware Tools auf dem Server installiert sind und ausgeführt werden. Die Version der VMware-Tools muss Version 10.2.1 oder höher sein. |
| **9017**: Die Datei, die die ermittelten Metadaten enthält, kann auf dem Server nicht gefunden werden. | Dies kann ein vorübergehendes Problem aufgrund eines internen Fehlers sein. | Senden Sie eine Microsoft-Supportanfrage, um dieses Problem zu beheben. |
| **9018**: PowerShell ist nicht auf dem Server installiert. | PowerShell wurde auf dem Server nicht gefunden. | Stellen Sie sicher, dass PowerShell Version 2.0 oder höher auf dem Server installiert ist. <br/><br/> [Erfahren Sie mehr](troubleshoot-discovery.md#error-9018-powershellnotfound) darüber, wie Sie das Problem beheben können.|
| **9019**: Die Metadaten können aufgrund von Gastvorgangsfehlern auf dem Server nicht ermittelt werden. | Es sind Fehler bei VMware-Gastvorgängen auf dem Server aufgetreten. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: ```<FriendlyNameOfCredentials>.``` | Stellen Sie sicher, dass die für die Appliance angegebenen Serveranmeldeinformationen gültig sind und der Benutzername in den Anmeldeinformationen das UPN-Format aufweist. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9020**: Die Datei, die die ermittelten Metadaten auf dem Server enthalten muss, kann nicht erstellt werden. | Die Rolle, die mit den für die Appliance oder eine Gruppenrichtlinie lokal bereitgestellten Anmeldeinformationen verknüpft ist, schränkt die Erstellung von Dateien im erforderlichen Ordner ein. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: ```<FriendlyNameOfCredentials>.``` | 1. Überprüfen Sie, ob die auf der Appliance bereitgestellten Anmeldeinformationen über die Berechtigung zum Erstellen von Dateien für den Ordner \<folder path/folder name> auf dem Server verfügen. <br/>2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über die erforderlichen Berechtigungen verfügen, geben Sie entweder andere an, oder bearbeiten Sie vorhandene Anmeldeinformationen. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9021**: Die Datei, die die ermittelten Metadaten im richtigen Pfad auf dem Server enthalten muss, kann nicht erstellt werden. | Die VMware Tools melden einen falschen Dateipfad zum Erstellen der Datei. | Stellen Sie sicher, dass VMware Tools mit einer höheren Version als 10.2.0 auf dem Server installiert sind und ausgeführt werden. |
| **9022**: Der Zugriff zum Ausführen des Cmdlets Get-WmiObject auf dem Server wird verweigert. | Die Rolle, die den für die Appliance oder eine Gruppenrichtlinie lokal bereitgestellten Anmeldeinformationen zugeordnet ist, schränkt den Zugriff auf das WMI-Objekt ein. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Überprüfen Sie, ob die für die Appliance bereitgestellten Anmeldeinformationen über Administratorrechte zum Erstellen von Dateien verfügen und WMI aktiviert ist. <br/> 2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über die erforderlichen Berechtigungen verfügen, geben Sie entweder andere an, oder bearbeiten Sie vorhandene Anmeldeinformationen. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.)<br/><br/> [Erfahren Sie mehr](troubleshoot-discovery.md#error-9022-getwmiobjectaccessdenied) darüber, wie Sie das Problem beheben können.|
| **9023**: PowerShell kann nicht ausgeführt werden, weil der Wert der %SystemRoot%-Umgebungsvariablen leer ist. | Der Wert der %SystemRoot“-Umgebungsvariablen für den Server ist leer. | 1. Überprüfen Sie, ob die Umgebungsvariable einen leeren Wert zurückgibt, indem Sie den Befehl echo %systemroot% auf dem betroffenen Server ausführen. <br/> 2. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9024**: Die Ermittlung kann nicht durchgeführt werden, weil der Wert der %TEMP%-Umgebungsvariablen leer ist. | Der Wert der %TEMP%-Umgebungsvariablen für den Server ist leer. | 1. Überprüfen Sie, ob die Umgebungsvariable einen leeren Wert zurückgibt, indem Sie den Befehl echo %temp% auf dem betroffenen Server ausführen. <br/> 2. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9025**: Die Ermittlung kann nicht ausgeführt werden. PowerShell ist auf dem Server beschädigt. | PowerShell ist auf dem Server beschädigt. | Installieren Sie PowerShell neu, und überprüfen Sie, ob PowerShell auf dem betroffenen Server ausgeführt wird. |
| **9026**: Auf dem Server können keine Gastvorgänge ausgeführt werden. | Der aktuelle Status des Servers lässt nicht zu, dass Gastvorgänge ausgeführt werden. | 1. Stellen Sie sicher, dass der betroffene Server aktiv ist und ausgeführt wird.<br/> 2. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **9027**: Die Metadaten können nicht ermittelt werden, da der Agent für Gastvorgänge nicht auf dem Server ausgeführt wird. | Es konnte keine Verbindung mit dem Agent für Gastvorgänge auf dem Server hergestellt werden. | Stellen Sie sicher, dass VMware Tools mit einer höheren Version als 10.2.0 auf dem Server installiert sind und ausgeführt werden. |
| **9028**: Die Datei, die die ermittelten Metadaten enthalten muss, kann aufgrund von unzureichendem Speicher auf dem Server nicht erstellt werden. | Es fehlt ausreichend Speicherplatz auf dem Serverdatenträger. | Stellen Sie sicher, dass genügend Speicherplatz im Datenträgerspeicher des betroffenen Servers zur Verfügung steht. |
| **9029**: Die für die Appliance bereitgestellten Anmeldeinformationen verfügen nicht über Zugriffsberechtigungen zum Ausführen von PowerShell. | Die für die Appliance bereitgestellten Anmeldeinformationen verfügen nicht über Zugriffsberechtigungen zum Ausführen von PowerShell. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Stellen Sie sicher, dass die für die Appliance bereitgestellten Anmeldeinformationen auf PowerShell auf dem Server zugreifen können.<br/> 2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über den erforderlichen Zugriff verfügen, geben Sie entweder andere an, oder bearbeiten Sie vorhandene Anmeldeinformationen. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9030**: Die ermittelten Metadaten können nicht erfasst werden, da sich der ESXi-Host, auf dem der Server gehostet wird, in einem nicht verbundenen Zustand befindet. | Der ESXi-Host, auf dem sich der Server befindet, befindet sich in einem nicht verbundenen Zustand. | Stellen Sie sicher, dass sich der ESXi-Host, der den Server ausführt, in einem verbundenen Zustand befindet. |
| **9031**: Die ermittelten Metadaten können nicht erfasst werden, da sich der ESXi-Host, auf dem der Server gehostet wird, nicht antwortet. | Der ESXi-Host, auf dem sich der Server befindet, befindet sich in einem ungültigen Zustand. | Stellen Sie sicher, dass der ESXi-Host, der den Server ausführt, ausgeführt wird und verbunden ist. |
| **9032**: Aufgrund eines internen Fehlers kann keine Ermittlung durchgeführt werden. | Das aufgetretene Problem beruht auf einem internen Fehler. | Führen Sie die [hier](troubleshoot-discovery.md#error-9032-invalidrequest) beschriebenen Schritte aus, um das Problem zu beheben. Wenn das Problem weiterhin besteht, öffnen Sie eine Microsoft-Supportanfrage. |
| **9033**: Ermittlung kann nicht erfolgen, weil der Benutzername der Anmeldeinformationen, die auf der Appliance für den Server bereitgestellt werden, ungültige Zeichen enthält. | Die auf der Appliance bereitgestellten Anmeldeinformationen enthalten ungültige Zeichen im Benutzernamen. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | Stellen Sie sicher, dass die für die Appliance bereitgestellten Anmeldeinformationen keine ungültigen Zeichen im Benutzernamen enthalten. Sie können zum Appliancekonfigurations-Manager zurückkehren, um die Anmeldeinformationen zu bearbeiten. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9034**: Ermittlung kann nicht erfolgen, weil der Benutzername der Anmeldeinformationen, die auf der Appliance für den Server bereitgestellt werden, nicht das UPN-Format aufweist. | Die für die Appliance bereitgestellten Anmeldeinformationen weisen nicht den Benutzernamen im UPN-Format auf. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | Stellen Sie sicher, dass die für die Appliance bereitgestellten Anmeldeinformationen den Benutzernamen im UPN-Format (User Principal Name) aufweisen. Sie können zum Appliancekonfigurations-Manager zurückkehren, um die Anmeldeinformationen zu bearbeiten. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9035**: Ermittlung kann nicht erfolgen, weil der PowerShell-Sprachmodus nicht richtig festgelegt ist. | Der PowerShell-Sprachmodus ist nicht auf „Full Language“ festgelegt. | Stellen Sie sicher, dass der PowerShell-Sprachmodus auf „FullLanguage“ festgelegt ist. |
| **9036**: Ermittlung kann nicht erfolgen, weil der Benutzername der Anmeldeinformationen, die auf der Appliance für den Server bereitgestellt werden, nicht das UPN-Format aufweist. | Die für die Appliance bereitgestellten Anmeldeinformationen weisen nicht den Benutzernamen im UPN-Format auf. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | Stellen Sie sicher, dass die für die Appliance bereitgestellten Anmeldeinformationen den Benutzernamen im UPN-Format (User Principal Name) aufweisen. Sie können zum Appliancekonfigurations-Manager zurückkehren, um die Anmeldeinformationen zu bearbeiten. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **9037**: Die Metadatensammlung wird aufgrund einer langen Antwortzeit vom Server vorübergehend angehalten. | Der Server braucht zu lange, um zu antworten. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **10000**: Der auf dem Server ausgeführte Betriebssystemtyp wird nicht unterstützt. | Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux. | Nur Windows- und Linux-Betriebssystemtypen werden unterstützt. \<GuestOSName>-Betriebssystem wird derzeit nicht unterstützt. |
| **10001**: Das Skript, das zum Erfassen von Ermittlungsmetadaten erforderlich ist, wurde auf dem Server nicht gefunden. | Das Skript, das zum Ausführen der Ermittlung erforderlich ist, wurde möglicherweise gelöscht oder aus dem erwarteten Speicherort entfernt. | Senden Sie eine Microsoft-Supportanfrage, um dieses Problem zu beheben. |
| **10002**: Für die Ermittlungsvorgänge ist ein Timeout auf dem Server aufgetreten. | Dies kann ein vorübergehendes Problem sein, da der Ermittlungs-Agent auf der Appliance nicht wie erwartet funktioniert. | Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn dies nicht der Fall ist, führen Sie die [hier](troubleshoot-discovery.md#error-10002-scriptexecutiontimedoutonvm) beschriebenen Schritte aus, um das Problem zu beheben. Wenn das Problem weiterhin besteht, öffnen Sie eine Microsoft-Supportanfrage.|
| **10003**: Der Prozess zur Ausführung der Ermittlungsvorgänge wurde mit einem Fehler beendet. | Der Prozess zur Ausführung der Ermittlungsvorgänge wurde aufgrund eines Fehlers plötzlich beendet.| Das Problem sollte im nächsten Zyklus innerhalb von 24 Stunden automatisch behoben werden. Wenn das Problem weiterhin besteht, senden Sie eine Microsoft-Supportanfrage. |
| **10004**: Es wurden keine Anmeldeinformationen für die Appliance für den Serverbetriebssystemtyp angegeben. | Die Anmeldeinformationen für den Serverbetriebssystemtyp wurden auf der Appliance nicht hinzugefügt. | 1. Stellen Sie sicher, dass Sie die Anmeldeinformationen für den Betriebssystemtyp des betroffenen Servers auf der Appliance hinzufügen.<br/> 2. Sie können jetzt mehrere Serveranmeldeinformationen auf der Appliance hinzufügen. |
| **10005**: Die auf der Appliance für den Server bereitgestellten Anmeldeinformationen sind ungültig. | Die auf der Appliance bereitgestellten Anmeldeinformationen sind ungültig. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: ```\<FriendlyNameOfCredentials>.``` | 1. Stellen Sie sicher, dass die für die Appliance angegebenen Anmeldeinformationen gültig sind und der Zugriff auf den Server mit diesen Anmeldeinformationen möglich ist.<br/> 2. Sie können jetzt mehrere Serveranmeldeinformationen auf der Appliance hinzufügen.<br/> 3. Wechseln Sie zurück zum Appliance-Konfigurations-Manager, um andere Anmeldeinformationen anzugeben oder die vorhandenen Anmeldeinformationen zu bearbeiten. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) <br/><br/> [Erfahren Sie mehr](troubleshoot-discovery.md#error-10005-guestcredentialnotvalid) darüber, wie Sie das Problem beheben können.|
| **10006**: Der auf dem Server ausgeführte Betriebssystemtyp wird nicht unterstützt. | Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux. | Nur Windows- und Linux-Betriebssystemtypen werden unterstützt. \<GuestOSName>-Betriebssystem wird derzeit nicht unterstützt. |
| **10007**: Die vom Server ermittelten Metadaten können nicht verarbeitet werden. | Fehler beim Analysieren des Inhalts der Datei, die die ermittelten Metadaten enthält. | Senden Sie eine Microsoft-Supportanfrage, um dieses Problem zu beheben. |
| **10008**: Die Datei, die die ermittelten Metadaten auf dem Server enthalten muss, kann nicht erstellt werden. | Die Rolle, die mit den für die Appliance oder eine Gruppenrichtlinie lokal bereitgestellten Anmeldeinformationen verknüpft ist, schränkt die Erstellung von Dateien im erforderlichen Ordner ein. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: ```<FriendlyNameOfCredentials>.``` | 1. Überprüfen Sie, ob die auf der Appliance bereitgestellten Anmeldeinformationen über die Berechtigung zum Erstellen von Dateien für den Ordner \<folder path/folder name> auf dem Server verfügen.<br/> 2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über die erforderlichen Berechtigungen verfügen, geben Sie entweder andere an, oder bearbeiten Sie vorhandene Anmeldeinformationen. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **10009**: Die ermittelten Metadaten können in der Datei auf dem Server nicht geschrieben werden. | Die Rolle, die den für die Appliance oder eine Gruppenrichtlinie lokal bereitgestellten Anmeldeinformationen zugeordnet ist, schränkt das Schreiben in die Datei auf dem Server ein. Das Problem ist beim Versuch aufgetreten, die folgenden Anmeldeinformationen auf dem Server zu verwenden: \<FriendlyNameOfCredentials>. | 1. Überprüfen Sie, ob die auf der Appliance bereitgestellten Anmeldeinformationen über Schreibberechtigung für den Ordner <Ordnerpfad/Ordnername> auf dem Server verfügen.<br/> 2. Wenn die für die Appliance bereitgestellten Anmeldeinformationen nicht über die erforderlichen Berechtigungen verfügen, geben Sie entweder andere an, oder bearbeiten Sie vorhandene Anmeldeinformationen. (Suchen Sie den Anzeigenamen der Anmeldeinformationen, die von Azure Migrate in den möglichen Fällen versucht wurden.) |
| **10010**: Ermittlung kann nicht erfolgen, weil der Befehl %CommandName;, der zum Erfassen von Metadaten erforderlich ist, auf dem Server fehlt. | Das Paket, das den Befehl %CommandName; enthält, ist nicht auf dem Server installiert. | Stellen Sie sicher, dass das Paket, das den Befehl %CommandName; enthält, auf dem Server installiert ist. |
| **10011**: Die auf der Appliance bereitgestellten Anmeldeinformationen wurden verwendet, um sich für eine interaktive Sitzung anzumelden und abzumelden. | Die interaktive Anmeldung und Abmeldung erzwingt, dass die Registrierungsschlüssel im Profil des Kontos entladen werden, das verwendet wird. Durch diese Bedingung sind die Schlüssel für zukünftige Verwendung nicht mehr verfügbar. | Verwenden Sie die [hier](/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error#resolutionus/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error) dokumentierten Lösungsmethoden. |
| **10012**: Es wurden keine Anmeldeinformationen für die Appliance für den Server angegeben. | Entweder wurden keine Anmeldeinformationen für den Server angegeben, oder Sie haben Domänenanmeldeinformationen mit falschem Domänennamen auf der Appliance angegeben. [Erfahren Sie mehr](troubleshoot-discovery.md#error-10012-credentialnotprovided) über die Ursache dieses Fehlers. | 1. Stellen Sie sicher, dass Anmeldeinformationen für die Appliance für den Server angegeben werden und der Zugriff auf den Server mit diesen Anmeldeinformationen möglich ist. <br/> 2. Sie können jetzt mehrere Anmeldeinformationen für die Appliance für Server hinzufügen. Wechseln Sie zurück zum Appliancekonfigurations-Manager, um Anmeldeinformationen für den Server anzugeben.|

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

Nachdem Sie die erforderliche PowerShell-Version installiert haben, können Sie überprüfen, ob der Fehler behoben wurde, indem Sie die [hier](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli) beschriebenen Schritte ausführen.

## <a name="error-9022-getwmiobjectaccessdenied"></a>Error 9022: GetWMIObjectAccessDenied

### <a name="remediation"></a>Behebung
Stellen Sie sicher, dass das in der Appliance bereitgestellte Benutzerkonto Zugriff auf den WMI-Namespace und Unternamespaces besitzt. Sie können den Zugriff festlegen, indem Sie die folgenden Schritte ausführen:
1.  Wechseln Sie zu dem Server, der diesen Fehler meldet.
1. Suchen Sie im Startmenü nach **Ausführen**, und wählen Sie diese Option aus. Geben Sie im Dialogfeld **Ausführen** *wmimgmt.msc* in das Textfeld **Öffnen:** ein, und drücken Sie die EINGABETASTE.
1. Die wmimgmt-Konsole wird geöffnet, in der im linken Bereich **WMI-Steuerung (lokal)** angezeigt wird. Klicken Sie mit der rechten Maustaste darauf, und wählen Sie im Menü **Eigenschaften** aus.
1. Wählen Sie im Dialogfeld „Eigenschaften von WMI-Steuerung (lokal)“ die Registerkarte **Sicherheiten** aus.
1. Klicken Sie auf die Schaltfläche **Sicherheit**; das Dialogfeld **Sicherheit für ROOT** wird geöffnet.
1. Klicken Sie auf die Schaltfläche **Erweitert**, um das Dialogfeld **Erweiterte Sicherheitseinstellungen für Root** zu öffnen. 
1. Klicken Sie auf die Schaltfläche **Hinzufügen**, um das Dialogfeld **Berechtigungseintrag für Root** zu öffnen.
1. Klicken Sie auf **Prinzipal auswählen**, um das Dialogfeld **Benutzer, Computer, Dienstkonten oder Gruppen auswählen** zu öffnen.
1. Wählen Sie die Benutzernamen oder Gruppen aus, denen Sie Zugriff auf die WMI gewähren möchten, und klicken Sie auf OK.
1. Stellen Sie sicher, dass Sie Ausführungsberechtigungen erteilen, und wählen Sie *Dieser Namespace und Unternamespaces* in der Dropdownliste **Gilt für:** aus.
1. Klicken Sie auf die Schaltfläche **Übernehmen**, um die Einstellungen zu speichern und alle Dialogfelder zu schließen.

Nachdem Sie den erforderlichen Zugriff erhalten haben, können Sie überprüfen, ob der Fehler behoben wurde, indem Sie die [hier](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli) beschriebenen Schritte ausführen.

## <a name="error-9032-invalidrequest"></a>Error 9032: InvalidRequest

### <a name="cause"></a>Ursache
Es kann mehrere Gründe für dieses Problem geben. Einer der Gründe ist, dass der im Appliancekonfigurations-Manager angegebene Benutzername (Serveranmeldeinformationen) ungültige XML-Zeichen aufweist, die zu einem Fehler beim Analysieren der SOAP-Anforderung führen.

### <a name="remediation"></a>Behebung
- Vergewissern Sie sich, dass der Benutzername der Serveranmeldeinformationen keine ungültigen XML-Zeichen enthält und im username@domain.com-Format vorliegt, das auch als UPN-Format bekannt ist.
- Nachdem Sie die Anmeldeinformationen auf der Appliance bearbeitet haben, können Sie überprüfen, ob der Fehler behoben wurde, indem Sie die [hier](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli) beschriebenen Schritte ausführen.


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
1. Wenn Befehle das Ergebnis nach wenigen Sekunden ausgeben, können Sie zu „Azure Migrate-Projekt > Ermittlung und Bewertung > Übersicht > Verwalten > Appliances“ navigieren, den Appliancenamen auswählen und dann **Dienste aktualisieren** auswählen, um den Ermittlungsdienst neu zu starten.
1. Wenn für die Befehle ein Timeout auftritt, ohne dass eine Ausgabe erfolgt, gilt Folgendes: 
- Sie müssen herausfinden, welcher Prozess eine hohe CPU- oder Arbeitsspeicherauslastung auf dem Server verursacht.
- Sie können versuchen, für diesen Server mehr Kerne/Arbeitsspeicher bereitzustellen, und die Befehle noch mal ausführen.

## <a name="error-10005-guestcredentialnotvalid"></a>Error 10005: GuestCredentialNotValid

### <a name="remediation"></a>Behebung
- Stellen Sie die Gültigkeit der Anmeldeinformationen _(der Anzeigename wird im Fehler angegeben)_ sicher, indem Sie im Konfigurations-Manager der Appliance auf „Anmeldeinformationen erneut überprüfen“ klicken.
- Stellen Sie sicher, dass Sie sich mit den gleichen Anmeldeinformationen, die in der Appliance angegeben sind, beim betroffenen Server anmelden können.
- Sie können versuchen, anstelle des Administratorkontos ein anderes Benutzerkonto (für dieselbe Domäne, wenn der Server in die Domäne eingebunden ist) für diesen Server zu verwenden.
- Das Problem kann auftreten, wenn die Kommunikation zwischen dem globalen Katalog und dem Domänencontroller unterbrochen ist. Sie können dies überprüfen, indem Sie ein neues Benutzerkonto im Domänencontroller erstellen und dasselbe Konto in der Appliance bereitstellen. Möglicherweise ist auch ein Neustart des Domänencontrollers erforderlich.
- Nachdem Sie die Wartungsschritte ausgeführt haben, können Sie überprüfen, ob der Fehler behoben wurde, indem Sie die [hier](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli) beschriebenen Schritte ausführen.

## <a name="error-10012-credentialnotprovided"></a>Error 10012: CredentialNotProvided

### <a name="cause"></a>Ursache
Dieser Fehler tritt auf, wenn Sie Domänenanmeldeinformationen mit einem falschen Domänennamen für den Appliancekonfigurations-Manager angegeben haben. Wenn Sie z. B. Domänenanmeldeinformationen mit dem Benutzernamen user@abc.com angegeben haben, der Domänenname jedoch als def.com angegeben wurde, werden diese Anmeldeinformationen nicht verwendet, wenn der Server mit def.com verbunden ist, und Sie erhalten diese Fehlermeldung.

### <a name="remediation"></a>Behebung
- Navigieren Sie zum Appliancekonfigurations-Manager, um Serveranmeldeinformationen hinzuzufügen oder vorhandene Anmeldeinformationen wie in der Ursache erläutert zu bearbeiten.
- Nachdem Sie die Wartungsschritte ausgeführt haben, können Sie überprüfen, ob der Fehler behoben wurde, indem Sie die [hier](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli) beschriebenen Schritte ausführen.

## <a name="mitigation-verification-using-vmware-powercli"></a>Entschärfungsüberprüfung mit VMware PowerCLI
Nachdem Sie die Schritte zur Behebung der oben aufgeführten Fehler durchgeführt haben, können Sie überprüfen, ob die Fehler behoben wurden, indem Sie über den Applianceserver einige PowerCLI-Befehle ausführen. Wenn die Befehle erfolgreich ausgeführt werden, bedeutet dies, dass das Problem jetzt behoben ist. Andernfalls müssen Sie die Überprüfung wiederholen und die Wartungsschritte erneut ausführen.

1. Führen Sie die folgenden Befehle aus, um PowerCLI auf dem Applianceserver einzurichten:
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
4. Führen Sie für die Softwareinventur die folgenden Befehle aus, um eine erfolgreiche Ausgabe zu bestätigen:

  - Für Windows-Server:

      ```` 
        Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'Get-WMIObject win32_operatingsystem'" -GuestCredential $credential

        Invoke-VMScript -VM $vm -ScriptText "powershell.exe Get-WindowsFeature" -GuestCredential $credential
      ```` 
   - Für Linux-Server:
      ````
      Invoke-VMScript -VM $vm -ScriptText "ls" -GuestCredential $credential
      ````
5. Führen Sie für Abhängigkeitsanalyse ohne Agent die folgenden Befehle aus, um eine erfolgreiche Ausgabe zu bestätigen:

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
6. Nachdem Sie bestätigt haben, dass die Entschärfung funktioniert hat, können Sie zu „Azure Migrate-Projekt > Ermittlung und Bewertung > Übersicht > Verwalten > Appliances“ navigieren, den Appliancenamen auswählen und dann **Dienste aktualisieren** auswählen, um einen neuen Ermittlungszyklus zu starten.

## <a name="discovered-sql-server-instances-and-databases-not-in-portal"></a>Ermittelte SQL Server-Instanzen und -Datenbanken nicht im Portal

Nach dem Initiieren der Ermittlung auf der Appliance kann es bis zu 24 Stunden dauern, bis die Inventurdaten im Portal angezeigt werden.

Wenn Sie keine Anmeldeinformationen für die Windows- oder SQL Server-Authentifizierung für den Appliance-Konfigurations-Manager angegeben haben, fügen Sie die Anmeldeinformationen hinzu, damit sie von der Appliance zum Herstellen einer Verbindung mit den entsprechenden SQL Server-Instanzen verwendet werden können.

Sobald die Verbindung hergestellt wurde, sammelt die Appliance Konfigurations- und Leistungsdaten von SQL Server-Instanzen und -Datenbanken. Die SQL Server-Konfigurationsdaten werden alle 24 Stunden aktualisiert, und die Leistungsdaten werden alle 30 Sekunden aufgezeichnet. Daher kann es bis zu 24 Stunden dauern, bis Änderungen an den Eigenschaften der SQL Server-Instanz und -Datenbanken im Portal angezeigt werden, zum Beispiel der Datenbankstatus, der Kompatibilitätsgrad und weitere Eigenschaften.

## <a name="sql-server-instance-is-showing-up-in-not-connected-state-on-portal"></a>SQL Server-Instanz mit Zustand „Nicht verbunden“ im Portal

Klicken Sie zum Anzeigen der Probleme, die bei der Ermittlung von SQL Server-Instanzen und -Datenbanken aufgetreten sind, auf der Seite **Ermittelte Server** Ihres Projekts in der Spalte „Verbindungsstatus“ auf den Status „Nicht verbunden“.

Die Erstellung einer Bewertung auf Servern mit SQL-Instanzen, die nicht vollständig erkannt wurden oder nicht verbunden sind, kann dazu führen, dass für die Bereitschaft „Unbekannt“ angegeben wird.

## <a name="common-sql-server-instances-and-database-discovery-errors"></a>Häufige Fehler bei der Ermittlung von SQL Server-Instanzen und -Datenbanken

Azure Migrate unterstützt die Ermittlung von SQL Server-Instanzen und -Datenbanken, die auf lokalen Computern ausgeführt werden, mit Azure Migrate: Ermittlung und Bewertung. Die SQL-Ermittlung wird derzeit nur für VMware unterstützt. Weitere Informationen zum Einstieg finden Sie im [Tutorial zur Ermittlung](tutorial-discover-vmware.md).

Typische Fehler bei der SQL-Ermittlung sind in der Tabelle zusammengefasst.

| **Fehler** | **Ursache** | **Aktion** | **Leitfaden**
|--|--|--|--|
|30000: Die dieser SQL Server-Instanz zugeordneten Anmeldeinformationen haben nicht funktioniert.|Entweder sind manuell zugeordnete Anmeldeinformationen ungültig, oder automatisch zugeordnete Anmeldeinformationen können nicht mehr auf die SQL Server-Instanz zugreifen.|Fügen Sie Anmeldeinformationen für SQL Server auf der Appliance hinzu, und warten Sie bis zum nächsten SQL-Ermittlungszyklus, oder erzwingen Sie eine Aktualisierung.| - |
|30001: Von der Appliance aus kann keine Verbindung mit der SQL Server-Instanz hergestellt werden.|1. Die Appliance hat keine Netzwerk-Sichtverbindung zum SQL Server.<br/>2. Die Firewall blockiert die Verbindung zwischen SQL Server und der Appliance.|1. Machen Sie SQL Server für die Appliance erreichbar.<br/>2. Lassen Sie eingehende Verbindungen von der Appliance zum SQL Server zu.| - | 
|30003: Das Zertifikat ist nicht vertrauenswürdig.|Auf dem Computer, auf dem SQL Server ausgeführt wird, ist kein vertrauenswürdiges Zertifikat installiert.|Richten Sie ein vertrauenswürdiges Zertifikat auf dem Server ein. [Weitere Informationen](/troubleshoot/sql/connect/error-message-when-you-connect)| [Ansicht](/troubleshoot/sql/connect/error-message-when-you-connect) |
|30004: Unzureichende Berechtigungen.|Dieser Fehler kann dadurch verursacht werden, dass keine ausreichenden Berechtigungen zum Überprüfen von SQL Server-Instanzen vorhanden sind. |Weisen Sie den für die Appliance angegebenen Anmeldeinformationen bzw. dem angegebenen Konto die Rolle „sysadmin“ zu, um SQL Server-Instanzen und -Datenbanken zu ermitteln. [Weitere Informationen](/sql/t-sql/statements/grant-server-permissions-transact-sql)| [Ansicht](/sql/t-sql/statements/grant-server-permissions-transact-sql) |
|30005: SQL Server-Anmeldung konnte aufgrund eines Problems mit der Standard-Masterdatenbank keine Verbindung herstellen.|Entweder ist die Datenbank ungültig, oder der Anmeldename besitzt keine CONNECT-Berechtigung für die Datenbank.|Verwenden Sie ALTER LOGIN, um die Standarddatenbank auf die Masterdatenbank festzulegen.<br/>Weisen Sie den für die Appliance angegebenen Anmeldeinformationen bzw. dem angegebenen Konto die Rolle „sysadmin“ zu, um SQL Server-Instanzen und -Datenbanken zu ermitteln. [Weitere Informationen](/sql/relational-databases/errors-events/mssqlserver-4064-database-engine-error)| [Ansicht](/sql/relational-databases/errors-events/mssqlserver-4064-database-engine-error) |
|30006: SQL Server-Anmeldung kann nicht mit der Windows-Authentifizierung verwendet werden.|1. Die Anmeldung ist möglicherweise eine SQL Server-Anmeldung, aber der Server akzeptiert nur Windows-Authentifizierung.<br/>2. Sie versuchen, eine Verbindung mit SQL Server-Authentifizierung herzustellen, aber die Anmeldeinformationen sind in SQL Server nicht vorhanden.<br/>3. Die Anmeldung verwendet möglicherweise die Windows-Authentifizierung, aber die Anmeldung ist ein nicht erkannter Windows-Prinzipal. Ein nicht erkannter Windows-Prinzipal bedeutet, dass die Anmeldung nicht von Windows überprüft werden kann. Das konnte daran liegen, dass die Windows-Anmeldung von einer nicht vertrauenswürdigen Domäne stammt.|Wenn Sie versuchen, eine Verbindung mithilfe der SQL Server-Authentifizierung herzustellen, vergewissern Sie sich, dass SQL Server im gemischten Authentifizierungsmodus konfiguriert ist und die SQL Server-Anmeldung vorhanden ist.<br/>Wenn Sie versuchen, eine Verbindung mit Windows-Authentifizierung herzustellen, überprüfen Sie, dass Sie ordnungsgemäß bei der richtigen Domäne angemeldet sind. [Weitere Informationen](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)| [Ansicht](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error) |
|30007: Kennwort abgelaufen.|Das Kennwort für das Konto ist abgelaufen.|Das Kennwort für die SQL Server-Anmeldung ist möglicherweise abgelaufen. Setzen Sie das Kennwort zurück, und/oder verlängern Sie das Datum für das Kennwort. [Weitere Informationen](/sql/relational-databases/native-client/features/changing-passwords-programmatically)| [Ansicht](/sql/relational-databases/native-client/features/changing-passwords-programmatically) |
|30008: Das Kennwort muss geändert werden.|Das Kennwort für das Konto muss geändert werden.|Ändern Sie das Kennwort der für die SQL Server-Ermittlung angegebenen Anmeldeinformation. [Weitere Informationen](/previous-versions/sql/sql-server-2008-r2/cc645934(v=sql.105))| [Ansicht](/previous-versions/sql/sql-server-2008-r2/cc645934(v=sql.105)) |
|30009: Ein interner Fehler ist aufgetreten.|Interner Fehler beim Ermitteln von SQL Server-Instanzen und -Datenbanken. |Falls das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support.| - |
|30010: Es wurden keine Datenbanken gefunden.|Auf der ausgewählten Serverinstanz wurden keine Datenbanken gefunden.|Weisen Sie den für die Appliance angegebenen Anmeldeinformationen bzw. dem angegebenen Konto die Rolle „sysadmin“ zu, um SQL-Datenbanken zu ermitteln.| - |
|30011: Interner Fehler beim Bewerten einer SQL-Instanz oder -Datenbank.|Interner Fehler beim Ausführen der Bewertung.|Falls das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support.| - |
|30012: SQL-Verbindungsfehler.|1. Die Firewall auf dem Server hat die Verbindung abgelehnt.<br/>2. Der SQL Server-Browserdienst (sqlbrowser) wurde nicht gestartet.<br/>3. Keine Antwort von SQL Server auf die Clientanforderung, da der Server wahrscheinlich nicht gestartet wurde.<br/>4. Der SQL Server-Client kann keine Verbindung mit dem Server herstellen. Dieser Fehler ist möglicherweise darauf zurückzuführen, dass der Server nicht für die Annahme von Remoteverbindungen konfiguriert ist.<br/>5. Der SQL Server-Client kann keine Verbindung mit dem Server herstellen. Der Fehler ist möglicherweise darauf zurückzuführen, dass entweder der Name des Servers durch den Client nicht aufgelöst werden kann oder der Name des Servers falsch ist.<br/>6. Die TCP- oder Named Pipe-Protokolle sind nicht aktiviert.<br/>7. Der angegebene Name der SQL Server-Instanz ist ungültig.|Verwenden Sie [diesen](https://go.microsoft.com/fwlink/?linkid=2153317) interaktiven Benutzerleitfaden für die Behandlung des Verbindungsproblems. Nachdem Sie die Schritte im Leitfaden befolgt haben, warten Sie 24 Stunden, bis die Daten im Dienst aktualisiert wurden. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support.| [Ansicht](https://go.microsoft.com/fwlink/?linkid=2153317) |
|30013: Fehler beim Herstellen einer Verbindung mit der SQL Server-Instanz.|1. Der SQL Server-Name kann von der Appliance nicht aufgelöst werden.<br/>2. SQL Server lässt keine Remoteverbindungen zu.|Wenn Sie SQL Server von der Appliance aus anpingen können, warten Sie 24 Stunden, und prüfen Sie dann, ob das Problem automatisch gelöst ist. Wenn nicht, wenden Sie sich an den Microsoft-Support. [Weitere Informationen](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)| [Ansicht](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error) |
|30014: Benutzername oder Kennwort ist falsch.| Dieser Fehler kann auf einen Authentifizierungsfehler aufgrund eines ungültigen Kennworts oder Benutzernamens zurückzuführen sein.|Geben Sie Anmeldeinformationen mit gültigem Benutzernamen und Kennwort an. [Weitere Informationen](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)| [Ansicht](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error) |
|30015: Interner Fehler beim Ermitteln der SQL-Instanz.|Beim Ermitteln der SQL-Instanz ist ein interner Fehler aufgetreten.|Falls das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support.| - |
|30016: Aufgrund eines Timeouts konnte keine Verbindung mit der Instanz „%instance;“ hergestellt werden.| Dieses Problem kann auftreten, wenn die Firewall auf dem Server die Verbindung ablehnt.|Überprüfen Sie, ob die Firewall der SQL Server-Instanz für die Annahme von Verbindungen konfiguriert ist. Wenden Sie sich an den Microsoft-Support, wenn der Fehler weiterhin besteht. [Weitere Informationen](/sql/relational-databases/errors-events/mssqlserver-neg2-database-engine-error)| [Ansicht](/sql/relational-databases/errors-events/mssqlserver-neg2-database-engine-error) |
|30017: Interner Fehler.|Ausnahmefehler.|Falls das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support.| - |
|30018: Interner Fehler.|Interner Fehler beim Erfassen von Daten wie z. B. der Größe der temporären Datenbank, der Dateigröße der SQL-Instanz usw.|Warten Sie 24 Stunden, und wenden Sie sich an den Microsoft-Support, falls das Problem weiterhin besteht.| - |
|30019: Ein interner Fehler ist aufgetreten.|Interner Fehler beim Erfassen von Leistungsmetriken wie z. B. der Arbeitsspeicherauslastung einer Datenbank oder Instanz.|Warten Sie 24 Stunden, und wenden Sie sich an den Microsoft-Support, falls das Problem weiterhin besteht.| - |

## <a name="common-web-apps-discovery-errors"></a>Häufige Fehler bei der Ermittlung von Web-Apps

Azure Migrate unterstützt die Ermittlung von ASP.NET-Web-Apps, die auf lokalen Computern ausgeführt werden, mit Azure Migrate: Ermittlung und Bewertung. Die Web-Apps-Ermittlung wird derzeit nur für VMware unterstützt. Weitere Informationen zum Einstieg finden Sie im [Tutorial zur Ermittlung](tutorial-discover-vmware.md).

Typische Fehler bei der Web-Apps-Ermittlung sind in der Tabelle zusammengefasst.

| **Fehler** | **Ursache** | **Aktion** |
|--|--|--|
| **40001**: Das IIS-Verwaltungskonsolenfeature ist nicht aktiviert. | Die IIS-Web-App-Ermittlung verwendet die Verwaltungs-API, die in der lokalen Version von IIS enthalten ist, um die IIS-Konfiguration zu lesen. Diese API ist verfügbar, wenn das IIS-Verwaltungskonsolenfeature von IIS aktiviert ist. Entweder ist dieses Feature nicht aktiviert, oder die Version des Betriebssystems wird für die IIS-Web-App-Ermittlung nicht unterstützt.| Stellen Sie sicher, dass die Rolle „Webserver (IIS)“, einschließlich der IIS-Verwaltungskonsole (Teil der Verwaltungstools), aktiviert ist und dass das Serverbetriebssystem die Version Windows Server 2008 R2 oder höher aufweist.|
| **40002**: Von der Appliance aus kann keine Verbindung mit dem Server hergestellt werden. | Fehler bei der Verbindung mit dem Server aufgrund ungültiger Anmeldeinformationen oder eines nicht verfügbaren Computers. | Stellen Sie sicher, dass die für den Server angegebenen Anmeldeinformationen korrekt sind und dass der Server online ist und WS-Management PowerShell-Remoteverbindungen akzeptiert. |
| **40003**: Aufgrund ungültiger Anmeldeinformationen kann keine Verbindung mit dem Server hergestellt werden. | Fehler bei der Verbindung mit dem Server aufgrund ungültiger Anmeldeinformationen. | Stellen Sie sicher, dass die für den Server angegebenen Anmeldeinformationen korrekt sind und WS-Management PowerShell-Remoting aktiviert ist. |
| **40004**: Der Zugriff auf die IIS-Konfiguration ist nicht möglich. | Keine oder unzureichende Berechtigungen. | Vergewissern Sie sich, dass es sich bei den für den Server angegebenen Benutzeranmeldeinformationen um Anmeldeinformationen auf Administratorebene handelt und dass der Benutzer über Berechtigungen für den Zugriff auf Dateien im IIS-Verzeichnis (%windir%\System32\inetsrv\config) und IIS-Serverkonfigurationsverzeichnis (%windir%\System32\inetsrv\config) verfügt. |
| **40005**: IIS-Ermittlung kann nicht abgeschlossen werden. | Fehler beim Abschließen der Ermittlung auf der VM. Dies kann auf Probleme mit dem Zugriff auf die Konfiguration auf dem Server zurückzuführen sein. Fehler: „%detailedMessage;“. | Vergewissern Sie sich, dass es sich bei den für den Server angegebenen Benutzeranmeldeinformationen um Anmeldeinformationen auf Administratorebene handelt und dass der Benutzer über Berechtigungen für den Zugriff auf Dateien im IIS-Verzeichnis (%windir%\System32\inetsrv\config) und IIS-Serverkonfigurationsverzeichnis (%windir%\System32\inetsrv\config) verfügt. Wenden Sie sich dann mit den Fehlerdetails an den Support. |
| **40006**: Nicht kategorisierte Ausnahme. | Neues Fehlerszenario. | Wenden Sie sich mit Fehlerdetails und Protokollen an den Microsoft-Support. Protokolle finden Sie auf dem Applianceserver unter dem Pfad C:\ProgramData\Microsoft Azure\Logs. |
| **40007**: Für den Webserver wurden keine Web-Apps gefunden. | Der Webserver verfügt über keine gehosteten Anwendungen. | Überprüfen Sie die Webserverkonfiguration. |

## <a name="next-steps"></a>Nächste Schritte

Richten Sie eine Appliance für [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md) oder [physische Server](how-to-set-up-appliance-physical.md) ein.