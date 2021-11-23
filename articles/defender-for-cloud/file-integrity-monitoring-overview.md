---
title: Überwachung der Dateiintegrität in Microsoft Defender für Cloud
description: In dieser Anleitung erfahren Sie, wie Sie die Überwachung der Dateiintegrität (File Integrity Monitoring, FIM) in Microsoft Defender für Cloud konfigurieren.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 8ceb46b1a70397d73379df3f2d9f1027feabf49b
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526143"
---
# <a name="file-integrity-monitoring-in-microsoft-defender-for-cloud"></a>Überwachung der Dateiintegrität in Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In dieser Anleitung erfahren Sie, wie Sie die Überwachung der Dateiintegrität (File Integrity Monitoring, FIM) in Microsoft Defender für Cloud konfigurieren.


## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|Erfordert [Microsoft Defender für Server](defender-for-servers-introduction.md)<br>Mithilfe des Log Analytics-Agents lädt die Überwachung der Dateiintegrität Daten in den Log Analytics-Arbeitsbereich hoch. Die Gebühren für diese Daten hängen von der hochgeladenen Datenmenge ab. Weitere Informationen erhalten Sie unter [Log Analytics – Preise](https://azure.microsoft.com/pricing/details/log-analytics/).|
|Erforderliche Rollen und Berechtigungen:|Der **Besitzer des Arbeitsbereichs** kann FIM aktivieren/deaktivieren (weitere Informationen finden Sie unter [Azure-Rolles für Log Analytics](/services-hub/health/azure-roles#azure-roles)).<br>**Leser** können Ergebnisse anzeigen.|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet)<br>Wird nur in Regionen unterstützt, in denen die Änderungsnachverfolgungslösung von Azure Automation verfügbar ist.<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Geräte mit [Azure Arc](../azure-arc/servers/overview.md)-Unterstützung.<br>Siehe [Unterstützte Regionen für einen verknüpften Log Analytics-Arbeitsbereich](../automation/how-to/region-mappings.md).<br>Informieren Sie sich über die [Änderungsnachverfolgung](../automation/change-tracking/overview.md).|
|||

## <a name="what-is-fim-in-defender-for-cloud"></a>Was ist FIM in Defender für Cloud?
Die Überwachung der Dateiintegrität, auch als Überwachung von Änderungen bezeichnet, untersucht unter anderem Betriebssystemdateien und Windows-Registrierungen, Anwendungssoftware und Linux-Systemdateien auf Änderungen, die auf einen Angriff hindeuten. 

Defender für Cloud empfiehlt Entitäten zur Überwachung mit FIM. Sie können auch eigene FIM-Richtlinien oder Entitäten für die Überwachung definieren. Die Überwachung der Dateiintegrität informiert Sie über verdächtige Aktivitäten wie z. B.:

- Erstellen und Löschen von Dateien und Registrierungsschlüsseln
- Änderungen an Dateien (an der Dateigröße, der Zugriffssteuerungsliste und dem Hash des Inhalts)
- Änderungen an Registrierungen (an der Größe, der Zugriffssteuerungsliste, am Typ und am Inhalt)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Auswerten der Liste der Entitäten, für die eine Überwachung der Dateiintegrität empfohlen wird
> * Definieren Ihrer eigenen benutzerdefinierten Regeln für die Überwachung der Dateiintegrität
> * Überwachen von Änderungen an Ihren überwachten Entitäten
> * Verwenden von Platzhaltern, um die verzeichnisübergreifende Nachverfolgung zu vereinfachen


## <a name="how-does-fim-work"></a>Wie funktioniert die Überwachung der Dateiintegrität?

Der Log Analytics-Agent lädt Daten in den Log Analytics-Arbeitsbereich hoch. Der aktuelle Zustand dieser Elemente wird mit dem Zustand während des vorherigen Scans verglichen, und Sie werden von der Überwachung der Dateiintegrität benachrichtigt, wenn verdächtige Änderungen vorgenommen wurden.

Die Überwachung der Dateiintegrität verwendet die Azure-Lösung „Änderungsnachverfolgung“, um Änderungen in Ihrer Umgebung nachzuverfolgen und zu identifizieren. Wenn die Überwachung der Dateiintegrität aktiviert ist, verfügen Sie über eine Ressource für die **Änderungsnachverfolgung** vom Typ **Lösung**. Ausführliche Informationen zur Häufigkeit der Datensammlung finden Sie unter [Details zur Datensammlung für die Änderungsnachverfolgung](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection).

> [!NOTE]
> Wenn Sie die Ressource **Änderungsnachverfolgung** entfernen, deaktivieren Sie damit auch das Feature „Überwachung der Dateiintegrität“ in Defender für Cloud.

## <a name="which-files-should-i-monitor"></a>Welche Dateien sollten überwacht werden?

Beim Auswählen der zu überwachenden Dateien sollten Sie die Dateien berücksichtigen, die für Ihr System und Ihre Anwendungen unentbehrlich sind. Überwachen Sie Dateien, bei denen Sie keine ungeplanten Änderungen erwarten. Wenn Sie Dateien auswählen, die häufig von Anwendungen oder dem Betriebssystem geändert werden (z. B. Protokoll- und Textdateien), werden viele Meldungen generiert, die das Identifizieren eines Angriffs erschweren.

Defender für Cloud bietet die folgende Liste empfohlener Elemente zur Überwachung auf der Grundlage bekannter Angriffsmuster.

|Linux-Dateien|Windows-Dateien|Windows-Registrierungsschlüssel (HKLM = HKEY_LOCAL_MACHINE)|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*.conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini\boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Programme\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini\boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>Aktivieren der Überwachung der Dateiintegrität 

FIM ist nur auf den Seiten von Defender für Cloud im Azure-Portal verfügbar. Derzeit gibt es keine REST-API für die Arbeit mit der Überwachung der Dateiintegrität.

1. Wählen Sie im **Workloadschutz**-Dashboard im Bereich **Erweiterter Schutz** die Option **Überwachung der Dateiintegrität** aus.

   :::image type="content" source="./media/file-integrity-monitoring-overview/open-file-integrity-monitoring.png" alt-text="Starten der Überwachung der Dateiintegrität." lightbox="./media/file-integrity-monitoring-overview/open-file-integrity-monitoring.png":::

    Die Seite zur Konfiguration der **Überwachung der Dateiintegrität** wird geöffnet.

    Folgende Informationen werden für jeden Arbeitsbereich angegeben:

    - Die Gesamtanzahl der Änderungen, die in der letzten Woche aufgetreten sind (wenn die Überwachung der Dateiintegrität im Arbeitsbereich deaktiviert ist, wird „–“ angezeigt)
    - Die Gesamtanzahl der physischen und virtuellen Computer, die Berichte an den Arbeitsbereich senden
    - Der geografische Standort des Arbeitsbereichs
    - Das Azure-Abonnement, dem der Arbeitsbereich unterliegt

1. Auf dieser Seite können Sie folgende Aktionen durchführen:

    - Zugreifen auf den Status und die Einstellungen der einzelnen Arbeitsbereiche

    - ![Symbol für das Tarifupgrade.][4] Upgraden Sie den Arbeitsbereich, um erweiterte Sicherheitsfeatures zu verwenden. Dieses Symbol zeigt an, dass der Arbeitsbereich oder das Abonnement nicht durch Microsoft Defender für Server geschützt wird. Um die FIM-Features zu verwenden, muss Ihr Abonnement mit diesem Plan geschützt werden. Weitere Informationen finden Sie unter [Erweiterte Sicherheitsfeatures von Microsoft Defender für Cloud](enhanced-security-features-overview.md).

    - ![Symbol „Aktivieren“][3] Aktivieren Sie die Überwachung der Dateiintegrität auf allen Computern im Arbeitsbereich, und konfigurieren Sie die entsprechenden Optionen. Dieses Symbol zeigt an, dass die Überwachung der Dateiintegrität für den Arbeitsbereich nicht aktiviert ist.

        :::image type="content" source="./media/file-integrity-monitoring-overview/workspace-list-fim.png" alt-text="Aktivieren der Überwachung der Dateiintegrität für einen bestimmten Arbeitsbereich.":::


    > [!TIP]
    > Wenn keine Schaltfläche zum Aktivieren oder Aktualisieren vorhanden ist und der Bereich leer ist, bedeutet dies, dass die Überwachung der Dateiintegrität bereits für den Arbeitsbereich aktiviert ist.


1. Wählen Sie **AKTIVIEREN** aus. Die Details des Arbeitsbereichs einschließlich der Anzahl der Windows- und Linux-Computer im Arbeitsbereich werden angezeigt.

    :::image type="content" source="./media/file-integrity-monitoring-overview/workspace-fim-status.png" alt-text="Seite mit den Arbeitsbereichsdetails für die Überwachung der Dateiintegrität.":::

   Die empfohlenen Einstellungen für Windows und Linux werden ebenfalls aufgeführt.  Erweitern Sie **Windows-Dateien**, **Registrierung** und **Linux-Dateien**, um die vollständige Liste der empfohlenen Elemente anzuzeigen.

1. Deaktivieren Sie die Kontrollkästchen für alle empfohlenen Entitäten, die nicht von der Überwachung der Dateiintegrität überwacht werden sollen.

1. Klicken Sie auf **Überwachung der Dateiintegrität anwenden**, um die Überwachung der Dateiintegrität zu aktivieren.

> [!NOTE]
> Sie können die Einstellungen jederzeit ändern. Weitere Informationen finden Sie im Folgenden unter [Edit monitored entities (Bearbeiten überwachter Entitäten)](#edit-monitored-entities).



## <a name="audit-monitored-workspaces"></a>Überprüfen überwachter Arbeitsbereiche 

Das Dashboard **Überwachung der Dateiintegrität** zeigt die Arbeitsbereiche an, für den die Überwachung der Dateiintegrität aktiviert ist. Das Dashboard „Überwachung der Dateiintegrität“ wird geöffnet, nachdem Sie die Überwachung der Dateiintegrität für einen Arbeitsbereich aktiviert haben oder wenn Sie einen Arbeitsbereich im Fenster **Überwachung der Dateiintegrität** auswählen, für den diese bereits aktiviert ist.

:::image type="content" source="./media/file-integrity-monitoring-overview/fim-dashboard.png" alt-text="Das Dashboard der Überwachung der Dateiintegrität und die verschiedenen Informationsbereiche.":::

Im Dashboard „Überwachung der Dateiintegrität“ für einen Arbeitsbereich werden folgende Details angezeigt:

- Die Gesamtanzahl der mit dem Arbeitsbereich verbundenen Computer
- Die Gesamtanzahl der Änderungen, die im ausgewählten Zeitraum aufgetreten sind
- Eine Aufschlüsselung des Änderungstyps (Dateien, Registrierungen)
- Eine Aufschlüsselung der Änderungskategorie (Geändert, Hinzugefügt, Entfernt)

Wählen Sie oben im Dashboard die Option **Filter** aus, um den Zeitraum zu ändern, für den Änderungen angezeigt werden.

:::image type="content" source="./media/file-integrity-monitoring-overview/dashboard-filter.png" alt-text="Zeitraumfilter für das Dashboard der Überwachung der Dateiintegrität.":::

Auf der Registerkarte **Server** werden die Computer aufgelistet, die Meldungen an diesen Arbeitsbereich ausgeben. Für jeden Computer führt das Dashboard Folgendes auf:

- Die Gesamtanzahl der Änderungen, die im ausgewählten Zeitraum aufgetreten sind
- Eine Aufschlüsselung der Gesamtanzahl der Änderungen in Änderungen von Dateien oder Registrierungen

Wenn Sie einen Computer auswählen, wird die Abfrage zusammen mit den Ergebnissen angezeigt, die die Änderungen identifizieren, die während des ausgewählten Zeitraums für den Computer vorgenommen wurden. Sie können eine Änderung erweitern, um weitere Informationen zu erhalten.

:::image type="content" source="./media/file-integrity-monitoring-overview/query-machine-changes.png" alt-text="Protokollanalyseabfrage mit den Änderungen, die durch die Überwachung der Dateiintegrität von Microsoft Defender für Cloud identifiziert wurden" lightbox="./media/file-integrity-monitoring-overview/query-machine-changes.png":::

Die Registerkarte **Änderungen** (siehe unten) führt alle Änderungen auf, die im ausgewählten Zeitraum am Arbeitsbereich durchgeführt wurden. Für jede geänderte Entität führt das Dashboard Folgendes auf:

- Computer, auf dem die Änderung aufgetreten ist
- Den Änderungstyp (Registrierung oder Datei)
- Die Änderungskategorie (Geändert, Hinzugefügt, Entfernt)
- Das Datum und die Uhrzeit der Änderung

:::image type="content" source="./media/file-integrity-monitoring-overview/changes-tab.png" alt-text="Registerkarte „Änderungen bei der Überwachung der Dateiintegrität in Microsoft Defender für Cloud“" lightbox="./media/file-integrity-monitoring-overview/changes-tab.png":::

**Änderungsdetails** wird geöffnet, wenn Sie eine Änderung in das Suchfeld eingeben oder eine Entität auswählen, die in der Registerkarte **Änderungen** aufgeführt ist.

:::image type="content" source="./media/file-integrity-monitoring-overview/change-details.png" alt-text="Überwachung der Dateiintegrität in Microsoft Defender für Cloud mit dem Detailbereich für eine Änderung" lightbox="./media/file-integrity-monitoring-overview/change-details.png":::

## <a name="edit-monitored-entities"></a>Bearbeiten von überwachten Entitäten

1. Wählen Sie im Dashboard **Überwachung der Dateiintegrität** für einen Arbeitsbereich **Einstellungen** aus der Symbolleiste aus. 

    :::image type="content" source="./media/file-integrity-monitoring-overview/file-integrity-monitoring-dashboard-settings.png" alt-text="Zugreifen auf die Einstellungen der Überwachung der Dateiintegrität für einen Arbeitsbereich." lightbox="./media/file-integrity-monitoring-overview/file-integrity-monitoring-dashboard-settings.png":::

   **Arbeitsbereichskonfiguration** wird mit Registerkarten für jeden Elementtyp geöffnet, der überwacht werden kann:

      - Windows-Registrierung
      - Windows-Dateien
      - Linux-Dateien
      - Dateiinhalte
      - Windows-Dienste

      Jede Registerkarte führt die Entitäten auf, die Sie in dieser Kategorie bearbeiten können. Für jede aufgeführte Entität identifiziert Defender für Cloud, ob FIM aktiviert (true) oder deaktiviert (false) ist. Bearbeiten Sie die Entität, um FIM zu aktivieren oder zu deaktivieren.

    :::image type="content" source="./media/file-integrity-monitoring-overview/file-integrity-monitoring-workspace-configuration.png" alt-text="Arbeitsbereichskonfiguration für die Überwachung der Datenintegrität in Microsoft Defender für Cloud":::

1. Wählen Sie auf einer der Registerkarten einen Eintrag aus, und bearbeiten Sie alle verfügbaren Felder im Bereich **Edit for Change Tracking** (Für die Änderungsnachverfolgung bearbeiten). Zu den Optionen gehören:

    - Aktivieren (True) (Wahr) oder deaktivieren (False) (Falsch) Sie die Überwachung der Dateiintegrität.
    - Geben Sie den Namen der Entität an, oder ändern Sie diesen.
    - Geben Sie den Wert oder Pfad an, oder ändern Sie diesen.
    - Löschen Sie die Entität.

1. Verwerfen oder speichern Sie Ihre Änderungen.


## <a name="add-a-new-entity-to-monitor"></a>Hinzufügen einer neuen zu überwachenden Entität

1. Wählen Sie im Dashboard **Überwachung der Dateiintegrität** für einen Arbeitsbereich **Einstellungen** aus der Symbolleiste aus. 

    Die **Arbeitsbereichskonfiguration** wird geöffnet.

1. Wählen Sie unter **Arbeitsbereichskonfiguration**

    1. die Registerkarte für den Entitätstyp aus, den Sie hinzufügen möchten: Windows-Registrierung, Windows-Dateien, Linux-Dateien, Dateiinhalt oder Windows-Dienste. 
    1. Wählen Sie **Hinzufügen** aus. 

        In diesem Beispiel wurde **Linux-Dateien** ausgewählt.

        :::image type="content" source="./media/file-integrity-monitoring-overview/file-integrity-monitoring-add-element.png" alt-text="Hinzufügen eines Elements zur Überwachung in der Dateiintegritätsüberwachung von Microsoft Defender für Cloud" lightbox="./media/file-integrity-monitoring-overview/file-integrity-monitoring-add-element.png":::

1. Wählen Sie **Hinzufügen**. Das Fenster **Add for Change Tracking** (Für die Änderungsnachverfolgung hinzufügen) wird geöffnet.

1. Geben Sie die notwendigen Informationen ein, und wählen Sie **Speichern** aus.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Ordner- und Pfadüberwachung mithilfe von Platzhaltern

Verwenden Sie Platzhalter, um die verzeichnisübergreifende Nachverfolgung zu vereinfachen. Beim Konfigurieren der Ordnerüberwachung mithilfe von Platzhaltern gelten die folgenden Regeln:
-   Platzhalter werden zum Nachverfolgen mehrerer Dateien benötigt.
-   Platzhalter können nur im letzten Segment eines Pfads verwendet werden, z.B. „C:\Ordner\Datei“ oder „/etc/*.conf“.
-   Wenn eine Umgebungsvariable einen ungültigen Pfad enthält, ist die Überprüfung zwar erfolgreich, bei der Ausführung der Inventur tritt jedoch ein Fehler für den Pfad auf.
-   Vermeiden Sie beim Festlegen des Pfads die Verwendung allgemeiner Pfade wie „c:\*.*“, da in diesem Fall zu viele Ordner durchlaufen werden müssen.

## <a name="disable-fim"></a>Deaktivieren der Überwachung der Dateiintegrität
Sie können die Überwachung der Dateiintegrität deaktivieren. Die Überwachung der Dateiintegrität verwendet die Azure-Lösung „Änderungsnachverfolgung“, um Änderungen in Ihrer Umgebung nachzuverfolgen und zu identifizieren. Wenn Sie die Überwachung der Dateiintegrität deaktivieren, wird die Lösung für die Änderungsnachverfolgung aus dem ausgewählten Arbeitsbereich entfernt.

So deaktivieren Sie die Überwachung der Dateiintegrität

1. Wählen Sie im Dashboard **Überwachung der Dateiintegrität** für einen Arbeitsbereich **Deaktivieren** aus.

    :::image type="content" source="./media/file-integrity-monitoring-overview/disable-file-integrity-monitoring.png" alt-text="Deaktivieren der Überwachung der Dateiintegrität über die Einstellungsseite.":::

1. Wählen Sie **Entfernen**.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie die Überwachung der Datenintegrität (FIM) in Defender für Cloud verwenden. Weitere Informationen zu Defender für Cloud finden Sie auf den folgenden Seiten:

* [Festlegen von Sicherheitsrichtlinien:](tutorial-security-policy.md) Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
* [Verwalten von Sicherheitsempfehlungen:](review-security-recommendations.md) Erfahren Sie, wie Empfehlungen Ihnen beim Schutz Ihrer Azure-Ressourcen helfen.
* [Azure Security Blog](/archive/blogs/azuresecurity/)(Blog zur Azure-Sicherheit): Hier finden Sie Neuigkeiten und Informationen zur Azure-Sicherheit.

<!--Image references-->
[3]: ./media/file-integrity-monitoring-overview/enable.png
[4]: ./media/file-integrity-monitoring-overview/upgrade-plan.png
