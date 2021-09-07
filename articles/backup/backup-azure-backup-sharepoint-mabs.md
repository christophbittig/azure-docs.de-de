---
title: Sichern einer SharePoint-Farm in Azure mit MABS
description: Verwenden Sie Azure Backup Server zum Sichern und Wiederherstellen Ihrer SharePoint-Daten. Dieser Artikel stellt die benötigten Informationen bereit, um Ihre SharePoint-Farm so zu konfigurieren, dass Sie die gewünschten Daten in Azure speichern können. Sie können geschützte SharePoint-Daten vom Datenträger oder aus Azure wiederherstellen.
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 2c52ace2515fbb1423c2ca3be75dfde837e6dd5c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339425"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>Sichern einer SharePoint-Farm in Azure mit MABS

Die Vorgehensweise zum Sichern einer SharePoint-Farm mithilfe von Microsoft Azure Backup Server (MABS) in Microsoft Azure ähnelt der Vorgehensweise zum Sichern anderer Datenquellen. Azure Backup ermöglicht die Verwendung eines flexiblen Sicherungszeitplans, mit dem Sie tägliche, wöchentliche, monatliche oder jährliche Sicherungspunkte erstellen und Aufbewahrungsrichtlinienoptionen für unterschiedliche Sicherungspunkte konfigurieren können. Mit MABS können Sie lokale Festplattenkopien speichern, um die Wiederherstellung zu beschleunigen. Außerdem können Sie Kopien zur kostengünstigen, langfristigen Aufbewahrung in Azure speichern.

Das Sichern von SharePoint auf Azure mit MABS ist ein Vorgang, der dem lokalen Sichern von SharePoint auf DPM (Data Protection Manager) ähnlich ist. Besondere Überlegungen zu Azure finden Sie in diesem Artikel.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Von SharePoint unterstützte Versionen und entsprechende Sicherungsszenarien

Eine Liste der unterstützten SharePoint-Versionen und der MABS-Versionen, die zu deren Sicherung erforderlich sind, finden Sie in der [MABS-Sicherungsmatrix](./backup-mabs-protection-matrix.md).

## <a name="before-you-start"></a>Vorbereitung

Vor dem Sichern einer SharePoint-Farm in Azure müssen Sie ein paar Punkte bestätigen.

### <a name="whats-not-supported"></a>Nicht unterstützte Funktionen

* Der MABS-Schutz einer SharePoint-Farm erstreckt sich nicht auf Suchindizes oder Anwendungsdienstdatenbanken. Für diese Datenbanken muss der Schutz separat konfiguriert werden.

* Von MABS werden keine SharePoint-SQL Server-Datenbanken gesichert, die sich auf Freigaben von Dateiservern mit horizontaler Skalierung (Scale Out File Server, SOFS) befinden.

### <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass für den Schutz von Workloads mit Microsoft Azure Backup alle [Voraussetzungen](backup-azure-dpm-introduction.md#prerequisites-and-limitations) erfüllt sind. Die Voraussetzungen umfassen folgende Aufgaben: Erstellen eines Sicherungstresors, Herunterladen der Anmeldeinformationen, Installieren des Azure Backup-Agents und Registrieren der Azure Backup Server-Instanz beim Tresor.

Zusätzliche Voraussetzungen und Einschränkungen:

* Wenn Sie SharePoint schützen, werden standardmäßig alle Inhaltsdatenbanken (sowie die SharePoint_Config- und SharePoint_AdminContent*-Datenbanken) geschützt. Wenn Sie Anpassungen wie Suchindizes, Vorlagen, Anwendungsdienst-Datenbanken oder den Benutzerprofildienst hinzufügen möchten, müssen Sie diese separat für den Schutz konfigurieren. Stellen Sie sicher, dass Sie den Schutz für alle Ordner aktivieren, die diese Typen von Features oder Anpassungsdateien enthalten.

* Sie können SharePoint-Datenbanken nicht als SQL Server-Datenquelle schützen. Sie können einzelne Datenbanken aus einer Farmsicherung wiederherstellen.

* Beachten Sie, dass MABS als **lokales System** ausgeführt wird und zum Sichern von SQL Server-Datenbanken Systemadministratorberechtigungen für dieses Konto für den SQL Server benötigt. Legen Sie auf dem SQL Server, den Sie sichern möchten, „NT AUTHORITY\SYSTEM“ auf **sysadmin** fest.

* Pro zehn Millionen Farmelemente müssen mindestens 2 GB Speicherplatz auf dem Volume verfügbar sein, auf dem sich der MABS-Ordner befindet. Dieser Speicherplatz wird für die Kataloggenerierung benötigt. Damit Sie MABS zur Durchführung einer bestimmten Wiederherstellung von Elementen (Websitesammlungen, Websites, Listen, Dokumentbibliotheken, Ordner, einzelne Dokumente und Listenelemente) verwenden können erstellt die Kataloggenerierung eine Liste mit den URLs aus den einzelnen Inhaltsdatenbanken. Sie können die URL-Liste im Bereich für wiederherstellbare Elemente (im Aufgabenbereich „Wiederherstellung“ der MABS-Verwaltungskonsole) anzeigen.

* Wenn sich in der SharePoint-Farm SQL Server-Datenbanken befinden, die mit SQL Server-Aliasen konfiguriert wurden, installieren Sie die SQL Server-Clientkomponenten auf dem Front-End-Webserver, der von MABS geschützt wird.

* Der Schutz von Elementen im Anwendungsspeicher wird mit SharePoint 2013 nicht unterstützt.

* Der Schutz von Remote-FILESTREAM wird von MABS nicht unterstützt. FILESTREAM sollte Bestandteil der Datenbank sein.

## <a name="configure-backup"></a>Konfigurieren der Sicherung

Um die SharePoint-Farm zu sichern, konfigurieren Sie den Schutz für SharePoint mithilfe von „ConfigureSharePoint.exe“, und erstellen Sie dann eine Schutzgruppe in MABS.

1. **Führen Sie ConfigureSharePoint.exe aus**: Dieses Tool konfiguriert den SharePoint VSS Writer-Dienst \(WSS\) und stellt dem Schutz-Agent mit Anmeldeinformationen für die SharePoint-Farm zur Verfügung. Nachdem Sie den Schutz-Agent bereitgestellt haben, befindet sich die Datei „ConfigureSharePoint.exe“ im Ordner „`<MABS Installation Path\>\bin`“ auf dem Front\-End-Webserver.  Wenn Sie über mehrere WFE-Server verfügen, müssen brauchen Sie ihn nur auf einem der Server zu installieren. Führen Sie die folgenden Schritte aus:

    * Navigieren Sie auf dem WFE-Server an einer Eingabeaufforderung zu `\<MABS installation location\>\\bin\\`, und führen Sie `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]` aus, wobei Folgendes gilt:

        * **EnableSharePointProtection** aktiviert den Schutz der SharePoint-Farm, aktiviert den VSS Writer und registriert die Identität der DCOM-Anwendung „WssCmdletsWrapper“, die als der Benutzer ausgeführt wird, dessen Anmeldeinformationen mit dieser Option eingegeben werden. Dieses Konto muss Farmadministrator und auch ein lokaler Administrator auf dem Front\-End-Webserver sein.

        * **EnableSPSearchProtection** aktiviert den Schutz von WSS 3.0 SP Search durch Verwendung des Registrierungsschlüssels „SharePointSearchEnumerationEnabled“ unter „HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\\“ auf dem Front\-End-Webserver und registriert die Identität der DCOM-Anwendung „WssCmdletsWrapper“, die als der Benutzer ausgeführt wird, dessen Anmeldeinformationen mit dieser Option eingegeben werden. Dieses Konto muss Farmadministrator und auch ein lokaler Administrator auf dem Front\-End-Webserver sein.

        * Mit **ResolveAllSQLAliases** werden alle von SharePoint VSS Writer gemeldeten Aliase angezeigt und für den entsprechenden Computer mit SQL Server aufgelöst. Außerdem werden die entsprechenden aufgelösten Instanznamen angezeigt. Wenn die Server gespiegelt sind, wird auch den gespiegelten Server angezeigt. Alle Aliase, die für den Computer mit SQL Server nicht aufgelöst werden, werden gemeldet.

        * **SetTempPath** legt die Umgebungsvariablen TEMP und TMP auf den angegebenen Pfad fest. Bei der Wiederherstellung auf Elementebene tritt ein Fehler auf, wenn umfangreiche Websitesammlungen, Websites, Listen oder Elemente wiederhergestellt werden und nicht genügend Speicherplatz im temporären Ordner des Farmadministrators vorhanden ist. Mit dieser Option können Sie für den Ordnerpfad der temporären Dateien ein Volume verwenden, das über genügend Speicherplatz zum Speichern der wiederherzustellenden Websitesammlung oder Website verfügt.

    * Geben Sie die Administratoranmeldeinformationen für die Farm an. Dieses Konto muss der lokalen Administratorgruppe auf dem WFE-Server angehören. Wenn der Farmadministrator kein lokaler Administrator ist, erteilen Sie auf dem WFE-Server die folgenden Berechtigungen:

        * Erteilen Sie der Gruppe **WSS_Admin_WPG** Vollzugriff auf den MABS-Ordner (`%Program Files%\Data Protection Manager\DPM\`).

        * Erteilen Sie der Gruppe **WSS_Admin_WPG** Lesezugriff auf den MABS-Registrierungsschlüssel (`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager`).

        Wenn sich die Administratoranmeldeinformationen für die SharePoint-Farm nach dem Ausführen von „ConfigureSharePoint.exe“ ändern, muss „ConfigureSharePoint.exe“ erneut ausgeführt werden.

1. Um eine Schutzgruppe zu erstellen, wählen Sie **Schutz** > **Aktionen** > **Schutzgruppe erstellen** aus, um den **Assistenten zum Erstellen einer neuen Schutzgruppe** in der MABS-Konsole zu öffnen.

1. Wählen Sie unter **Schutzgruppentyp auswählen** die Option **Server** aus.

1. Erweitern Sie in **Gruppenmitglieder auswählen** den Server, auf dem sich die WFE-Rolle befindet. Wenn mehrere WFE-Server vorhanden sind, wählen Sie den Server aus, auf dem Sie die Datei „ConfigureSharePoint.exe“ konfiguriert haben.

    Wenn Sie den Coputer erweitern, auf dem SharePoint ausgeführt wird, wird VSS von MABS abgefragt, um zu ermitteln, welche Daten MABS schützen kann. Wenn es sich bei der SharePoint-Datenbank um eine Remotedatenbank handelt, stellt MABS eine Verbindung mit der Datenbank her. Wenn SharePoint-Datenquellen nicht angezeigt werden, überprüfen Sie, ob der VSS Writer auf dem Computer ausgeführt wird, auf dem SharePoint ausgeführt wird, und auf einer beliebigen Remoteinstanz SQL Server. Stellen Sie anschließend sicher, dass der MABS-Agent sowohl auf dem Computer, auf dem SharePoint ausgeführt wird, als auch auf der Remoteinstanz von SQL Server installiert ist. Stellen Sie außerdem sicher, dass SharePoint-Datenbanken nicht an anderer Stelle als SQL Server-Datenbanken geschützt werden.

1. Geben Sie unter **Datenschutzmethode auswählen** an, wie Sie die kurz- und langfristige Sicherung handhaben möchten. Die kurzfristige Sicherung erfolgt immer zuerst auf Datenträger, mit der Option der Sicherung vom Datenträger in die Azure\-Cloud mit Azure Backup \(kurz\- oder langfristig\).

1. Geben Sie unter **Kurzfristige Ziele auswählen** an, wie die Sicherung in kurzfristigem Speicher auf Datenträger erfolgen soll.   Geben Sie unter **Beibehaltungsdauer** an, wie lange die Daten auf dem Datenträger beibehalten werden sollen. Geben Sie unter **Synchronisierungsfrequenz** an, wie oft eine inkrementelle Sicherung auf Datenträger erfolgen soll. Wenn Sie kein Sicherungsintervall festlegen möchten, können Sie „Direkt vor einem Wiederherstellungspunkt“ aktivieren, damit MABS unmittelbar vor jedem geplanten Wiederherstellungspunkt eine schnelle vollständige Sicherung ausführt.

1. Überprüfen Sie auf der Seite „Datenträgerzuordnungen überprüfen“ den Speicherplatz im Speicherpool, der der Schutzgruppe zugeordnet ist.

    **Gesamtdatengröße** ist die Größe der Daten, die Sie sichern möchten. **Bereitzustellender Speicherplatz für MABS** ist der für die Schutzgruppe von MABS empfohlene Speicherplatz. MABS wählt auf Grundlage der Einstellungen das ideale Sicherungsvolume. Sie können jedoch in **Details zur Datenträgerzuordnung** das gewählte Sicherungsvolume ändern. Wählen Sie für die Workloads im Dropdownmenü den bevorzugten Speicher aus. Durch Ihre Änderungen verändern sich im Bereich **Verfügbarer Speicherplatz** die Werte für **Gesamtspeicher** und **Freier Speicher**. Nicht ausreichend bereitgestellter Speicherplatz ist die Menge an Speicher, die laut Vorschlag von MABS zum Sicherstellen künftiger reibungsloser Sicherungen dem Volume hinzugefügt werden sollte.

1. Wählen Sie unter **Replikaterstellungsmethode auswählen** aus, wie die erste vollständige Datenreplikation erfolgen soll.  Wenn Sie die Replikation über das Netzwerk auswählen, empfehlen wir, eine Nebenzeit zu wählen. Ziehen Sie bei großen Datenmengen oder nicht optimalen Netzwerkbedingungen die Offlinereplikation der Daten mit Wechselmedien in Betracht.

1. Legen Sie unter **Konsistenzprüfungsoptionen auswählen** fest, wie Konsistenzprüfungen automatisiert werden sollen. Sie können eine Überprüfung aktivieren, die nur bei inkonsistenten Replikatdaten oder gemäß einem festgelegten Zeitplan ausgeführt wird. Wenn Sie keine automatische Konsistenzprüfung konfigurieren möchten, können Sie jederzeit eine manuelle Überprüfung ausführen, indem Sie im Bereich **Schutz** der MABS-Konsole mit der rechten Maustaste auf die Schutzgruppe klicken und **Konsistenzprüfung ausführen** auswählen.

1. Wenn Sie die Sicherung in die Cloud mit Azure Backup ausgewählt haben, vergewissern Sie sich, dass auf der Seite **Online zu schützende Daten angeben** die Workloads ausgewählt sind, die Sie in Azure sichern möchten.

1. Geben Sie unter **Onlinesicherungszeitplan angeben** an, wie oft inkrementelle Sicherungen in Azure erfolgen sollen. Sie können tägliche, wöchentliche, monatliche und jährliche Sicherungen planen und Datum und Uhrzeit ihrer Ausführung festlegen. Sicherungen können bis zu zweimal täglich erfolgen. Bei jeder Ausführung einer Sicherung wird ein Datenwiederherstellungspunkt in Azure anhand der gesicherten Daten erstellt, die auf dem MABS-Datenträger gespeichert sind.

1. Auf der Seite **Onlineaufbewahrungsrichtlinie angeben** können Sie auswählen, wie die Wiederherstellungspunkte aus den täglichen, wöchentlichen, monatlichen und jährlichen Sicherungen in Azure aufbewahrt werden.

1. Geben Sie auf der Seite **Onlinereplikation wählen** an, wie die erste vollständige Replikation der Daten erfolgt. Sie können eine Replikation über das Netzwerk wählen oder eine Offlinesicherung (sog. Offlineseeding) durchführen. Die Offlinesicherung erfolgt mithilfe der Importfunktion von Azure. [Weitere Informationen](./backup-azure-backup-import-export.md).

1. Überprüfen Sie auf der Seite **Zusammenfassung** Ihre Einstellungen. Nach Auswahl von **Gruppe erstellen** erfolgt die erste Replikation der Daten. Nach Abschluss der Replikation wird der Status der Schutzgruppe auf der Seite **Status** als **OK** angezeigt. Die Sicherung erfolgt anschließend gemäß den Schutzgruppeneinstellungen.

## <a name="monitoring"></a>Überwachung

Nach der Erstellung der Schutzgruppe erfolgt die erste Replikation, und MABS beginnt mit der Sicherung und Synchronisierung der SharePoint-Daten. MABS überwacht die Erstsynchronisierung sowie die nachfolgenden Sicherungen.  Sie können die SharePoint-Daten auf verschiedene Weise überwachen:

* Mithilfe der MABS-Standardüberwachung können Sie Benachrichtigungen für die proaktive Überwachung durch Veröffentlichen von Warnungen und Konfigurieren von Benachrichtigungen einrichten. Sie können per E-Mail Benachrichtigungen für kritische Warnungen, Warnungen oder Informationen sowie für den Status von instanziierten Wiederherstellungen senden.

* Wenn Sie Operations Manager verwenden, können Sie Warnungen zentral veröffentlichen.

### <a name="set-up-monitoring-notifications"></a>Einrichten der Überwachung von Benachrichtigungen

1. Wählen Sie in der MABS-Administratorkonsole **Überwachung** > **Aktion** > **Optionen** aus.

2. Wählen Sie **SMTP-Server** aus, geben Sie den Servernamen, den Port und die E-Mail-Adresse ein, über die Benachrichtigungen gesendet werden. Die Adresse muss gültig sein.

3. Geben Sie unter **Authentifizierter SMTP-Server** einen Benutzernamen und ein Kennwort ein. Der Benutzername und das Kennwort müssen zum Domänenkontonamen der Person gehören, deren Absenderadresse im vorherigen Schritt beschrieben wurde. Andernfalls tritt bei der Benachrichtigungsübermittlung ein Fehler auf.

4. Wählen Sie zum Testen der SMTP-Servereinstellungen **Test-E-Mail senden** aus, geben Sie die E-Mail-Adresse ein, an die die Testnachricht von MABS gesendet werden soll, und wählen Sie dann **OK** aus. Wählen Sie **Optionen** > **Benachrichtigungen** und dann die Warnungstypen aus, über die Empfänger benachrichtigt werden möchten. Geben Sie im Feld **Empfänger** die E-Mail-Adressen aller Empfänger ein, an die MABS die Benachrichtigungskopien senden soll.

### <a name="publish-operations-manager-alerts"></a>Veröffentlichen von Operations Manager-Warnungen

1. Wählen Sie in der MABS-Administratorkonsole **Überwachung** > **Aktion** > **Optionen** > **Warnungsveröffentlichung** > **Aktive Warnungen veröffentlichen** aus.

2. Nach der Aktivierung der **Warnungsveröffentlichung** werden alle vorhandene MABS-Warnungen, die möglicherweise eine Benutzeraktion erfordern, im Ereignisprotokoll **MABS-Warnungen** veröffentlicht. Der Operations Manager-Agent, der auf dem MABS-Server installiert ist, veröffentlicht dann diese Warnungen für Operations Manager und setzt die Aktualisierung der Konsole fort, wenn neue Warnungen generiert werden.

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Wiederherstellen eines SharePoint-Elements vom Datenträger mit MABS

Im folgenden Beispiel wurde *Recovering SharePoint item* versehentlich gelöscht und muss wiederhergestellt werden.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Öffnen Sie die **MABS-Verwaltungskonsole**. Alle von MABS geschützten SharePoint-Farmen werden auf der Registerkarte **Schutz** angezeigt.

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Wechseln Sie zum Wiederherstellen des Elements zunächst zur Registerkarte **Wiederherstellung** .

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Sie können SharePoint mithilfe einer platzhalterbasierten Suche innerhalb eines Wiederherstellungspunkt-Bereichs nach *Recovering SharePoint item* durchsuchen.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Wählen Sie in den Suchergebnissen den entsprechenden Wiederherstellungspunkt aus, klicken Sie mit der rechten Maustaste auf das Element, und wählen Sie **Wiederherstellen**.
5. Sie können auch andere Wiederherstellungspunkte durchsuchen und eine Datenbank oder ein Element für die Wiederherstellung auswählen. Wählen Sie **Datum > Wiederherstellungszeit** und anschließend die korrekte Option für **Datenbank > SharePoint-Farm > Wiederherstellungspunkt > Element**.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Klicken Sie mit der rechten Maustaste auf das Element, und wählen Sie **Wiederherstellen**, um den **Wiederherstellungs-Assistenten** zu öffnen. Wählen Sie **Weiter** aus.

    ![Wiederherstellungsauswahl prüfen](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Wählen Sie den gewünschten Wiederherstellungstyp und anschließend **Weiter** aus.

    ![Wiederherstellungstyp](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Bei Auswahl von **In ursprünglicher Site wiederherstellen** im Beispiel wird das Element in der ursprünglichen SharePoint-Website wiederhergestellt.
   >
   >
8. Wählen Sie den gewünschten **Wiederherstellungsprozess** aus.

   * Wählen Sie **Ohne Wiederherstellungsfarm wiederherstellen** aus, wenn sich die SharePoint-Farm nicht geändert hat und dem Wiederherstellungspunkt entspricht, der wiederhergestellt wird.
   * Wählen Sie **Mithilfe einer Wiederherstellungsfarm wiederherstellen** , wenn sich die SharePoint-Farm seit der Erstellung des Wiederherstellungspunkts geändert hat.

     ![Wiederherstellungsprozess](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Geben Sie einen SQL Server-Instanz-Stagingort für die vorübergehende Wiederherstellung der Datenbank und eine Stagingdateifreigabe auf MABS sowie dem Server an, auf dem SharePoint ausgeführt wird, um das Element wiederherzustellen.

    ![Staging Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS verknüpft die Inhaltsdatenbank, die als Host für das SharePoint-Element fungiert, mit der temporären SQL Server-Instanz. MABS stellt das Element aus der Inhaltsdatenbank wieder her und platziert es am Stagingdateispeicherort auf MABS. Das am Stagingort wiederhergestellte Element muss nun an den Stagingort in der SharePoint-Farm exportiert werden.

    ![Staging Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Wählen Sie **Wiederherstellungsoptionen angeben**, und wenden Sie Sicherheitseinstellungen auf die SharePoint-Farm oder die Sicherheitseinstellungen des Wiederherstellungspunkts an. Wählen Sie **Weiter** aus.

    ![Wiederherstellungsoptionen](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Sie können auch die Nutzung der Netzwerkbandbreite drosseln. Dies minimiert die Auswirkungen auf den Produktionsserver während der Produktion.
    >
    >
11. Überprüfen Sie die Zusammenfassung, und wählen Sie anschließend **Wiederherstellen** aus, um die Wiederherstellung der Datei zu initiieren.

    ![Wiederherstellungszusammenfassung](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Klicken Sie auf die Registerkarte **Überwachung** der **MABS-Administratorkonsole**, um den **Status** der Wiederherstellung anzuzeigen.

    ![Wiederherstellungsstatus](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Die Datei ist nun wiederhergestellt. Sie können die SharePoint-Site aktualisieren, um die wiederhergestellte Datei zu überprüfen.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>Azure-basiertes Wiederherstellen einer SharePoint-Datenbank mit MABS

1. Durchsuchen Sie zum Wiederherstellen einer SharePoint-Inhaltsdatenbank verschiedene Wiederherstellungspunkte (siehe oben), und wählen Sie den wiederherzustellenden Wiederherstellungspunkt aus.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Doppelklicken Sie auf den SharePoint-Wiederherstellungspunkt, um die verfügbaren SharePoint-Kataloginformationen anzuzeigen.

   > [!NOTE]
   > Da die SharePoint-Farm mit langfristiger Aufbewahrung in Azure geschützt ist, sind auf dem MABS-Server keine Kataloginformationen (Metadaten) verfügbar. Wenn also eine Zeitpunktwiederherstellung einer SharePoint-Inhaltsdatenbank erforderlich ist, muss die SharePoint-Farm neu katalogisiert werden.
   >
   >
3. Wählen Sie **Neu katalogisieren** aus.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Das Statusfenster für die **Neukatalogisierung der Cloud** wird geöffnet.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Nach Abschluss der Katalogisierung ändert sich der Status in *Erfolgreich*. Klicken Sie auf **Schließen**.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Wählen Sie das SharePoint-Objekt aus, das auf der MABS-Registerkarte **Wiederherstellung** angezeigt wird, um die Struktur der Inhaltsdatenbank abzurufen. Klicken Sie mit der rechten Maustaste auf das Element, und wählen Sie **Wiederherstellen** aus.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Führen Sie nun die weiter oben in diesem Artikel beschriebenen Wiederherstellungsschritte für die Wiederherstellung einer SharePoint-Inhaltsdatenbank vom Datenträger aus.

## <a name="switching-the-front-end-web-server"></a>Wechseln des Front-End-Webservers

Wenn Sie über mehrere Front-End-Webserver verfügen und den Server, den MABS zum Schützen der Farm verwendet, wechseln möchten, befolgen Sie die Anweisungen:

Im folgenden Verfahren wird das Beispiel für eine Serverfarm mit zwei Front-End-Webservern verwendet, *Server1* und *Server2*. MABS verwendet *Server1*, um die Farm zu schützen. Ändern Sie den von MABS verwendeten Front-End-Webserver in *Server2*, damit Sie *Server1* aus der Farm entfernen können.

> [!NOTE]
> Wenn der Front-End-Webserver, der von MABS zum Schutz der Farm verwendet wird, nicht verfügbar ist, verwenden Sie das folgende Verfahren, um den Front-End-Webserver zu ändern, indem Sie mit Schritt 4 beginnen.

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>So ändern Sie den Front-End-Webserver, den MABS zum Schutz der Farm verwendet

1. Beenden Sie den SharePoint VSS Writer-Dienst auf *Server1*, indem Sie den folgenden Befehl an einer Eingabeaufforderung ausführen:

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. Öffnen Sie auf *Server1* den Registrierungs-Editor, und navigieren Sie zum folgenden Registrierungsschlüssel:

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. Überprüfen Sie alle Werte, die im Unterschlüssel „VssAccessControl“ aufgeführt sind. Wenn ein Eintrag den Wert „0“ aufweist und ein anderer VSS Writer unter den zugehörigen Kontoanmeldeinformationen ausgeführt wird, ändern Sie die Wert in „1“.

1. Installieren Sie einen Schutz-Agent auf *Server2*.

   > [!WARNING]
   > Sie können nur Web-Front-End-Server nur wechseln, wenn sich beide Server in derselben Domäne befinden.

1. Ändern Sie auf *Server2* an der Eingabeaufforderung das Verzeichnis in `_MABS installation location_\bin\`, und führen Sie **ConfigureSharepoint** aus. Weitere Informationen zu ConfigureSharePoint finden Sie unter [Konfigurieren der Sicherung](#configure-backup).

1. Wählen Sie die Schutzgruppe aus, zu der die Serverfarm gehört, und wählen Sie dann **Schutzgruppe ändern** aus.

1. Erweitern Sie im Assistenten zum Ändern von Schutzgruppen *Server2* auf der Seite **Gruppenmitglieder auswählen**, wählen Sie die Serverfarm aus, und schließen Sie dann den Assistenten ab.

   Eine Konsistenzprüfung wird gestartet.

1. Sobald Sie Schritt 6 durchgeführt haben, können Sie das Volume aus der Schutzgruppe entfernen.

## <a name="remove-a-database-from-a-sharepoint-farm"></a>Entfernen einer Datenbank aus einer SharePoint-Farm

Wenn eine Datenbank aus einer SharePoint-Farm entfernt wird, überspringt MABS die Sicherung dieser Datenbank und fährt mit der Sicherung anderer Datenbanken in der SharePoint-Farm fort. Außerdem wird der Sicherungsadministrator benachrichtigt.

### <a name="mabs-alert---farm-configuration-changed"></a>MABS-Warnung: Änderung der Farmkonfiguration

Dies ist eine Warnung, die in Microsoft Azure Backup Server (MABS) generiert wird, wenn der automatische Schutz einer SharePoint Datenbank nicht erfolgreich ist. Weitere Informationen zur Warnungsursache finden Sie im Bereich **Warnungsdetails**.

Gehen Sie folgendermaßen vor, um die Warnung aufzulösen:

1. Klären Sie mit dem SharePoint-Administrator, ob die Datenbank tatsächlich aus der Farm entfernt wurde. Wenn die Datenbank aus der Farm entfernt wurde, muss anschließend der aktive Schutz für sie in MABS entfernt werden.
1. So entfernen Sie den aktiven Schutz für die Datenbank:
   1. Klicken Sie in der **MABS-Verwaltungskonsole** in der Navigationsleiste auf **Schutz**.
   1. Klicken Sie im **Anzeigebereich** mit der rechten Maustaste auf die Schutzgruppe für die SharePoint-Farm, und klicken Sie dann auf **Schutz des Mitglieds beenden**.
   1. Klicken Sie im Dialogfeld **Schutz beenden** auf **Geschützte Daten beibehalten**.
   1. Klicken Sie auf **Schutz beenden**.

Sie können die SharePoint-Farm über den Assistenten **Schutzgruppe ändern** erneut schützen. Wenn der Schutz erneut angewendet werden soll, wählen Sie den SharePoint-Front-End-Server aus, und klicken Sie auf **Aktualisieren**, um den Cache der SharePoint-Datenbank zu aktualisieren. Wählen Sie anschließend die SharePoint-Farm aus, und fahren Sie fort.

## <a name="next-steps"></a>Nächste Schritte

Siehe den Artikel [Sichern eines Exchange-Servers](backup-azure-exchange-mabs.md).
Siehe den Artikel [Sichern eines SQL Server-Servers](backup-azure-sql-mabs.md).
