---
title: Sichern von Dateien auf virtuellen Azure Stack-Computern
description: Verwenden Sie Azure Backup, um Azure Stack-Dateien und -Anwendungen in Ihrer Azure Stack-Umgebung zu sichern und wiederherzustellen.
ms.topic: conceptual
ms.date: 11/11/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: a6c92485ec89e06f9bf545181fe7e6341cd7bff4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284434"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Sichern von Dateien und Anwendungen in Azure Stack

Sie können Azure Backup verwenden, um Dateien und Anwendungen in Azure Stack zu schützen (oder zu sichern). Installieren Sie Microsoft Azure Backup Server als virtuellen Computer, der unter Azure Stack ausgeführt wird, um Dateien und Anwendungen zu sichern. Sie können die Dateien schützen, die auf einem beliebigen Azure Stack-Server in demselben virtuellen Netzwerk ausgeführt werden. Nachdem Sie Azure Backup Server installiert haben, fügen Sie Azure-Datenträger hinzu, um den lokalen Speicher zu vergrößern, der für die kurzfristige Sicherung von Daten verfügbar ist. Azure Backup Server nutzt Azure-Speicher für die langfristige Aufbewahrung.

> [!NOTE]
> Azure Backup Server und System Center Data Protection Manager (DPM) funktionieren zwar ähnlich, aber DPM wird für die Verwendung mit Azure Stack nicht unterstützt.
>

In diesem Artikel wird nicht die Installation von Azure Backup Server in der Azure Stack-Umgebung behandelt. Informationen zur Installation von Azure Backup Server in Azure Stack finden Sie im Artikel [Installieren von Azure Backup Server in Azure Stack](backup-mabs-install-azure-stack.md).

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Sichern von Dateien und Ordnern in Azure Stack-VMs in Azure

Um Azure Backup Server zum Schutz von Dateien auf virtuellen Azure Stack-Computern zu konfigurieren, öffnen Sie die Azure Backup Server-Konsole. Sie verwenden die Konsole, um Schutzgruppen zu konfigurieren und die Daten auf Ihren virtuellen Computern zu schützen.

1. Wählen Sie in der Azure Backup Server-Konsole **Schutz** und dann auf der Symbolleiste **Neu** aus, um den **Assistenten zum Erstellen einer neuen Schutzgruppe zu** öffnen.

   ![Konfigurieren des Schutzes in der Azure Backup Server-Konsole](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Das Öffnen des Assistenten kann einige Sekunden dauern. Wählen Sie im geöffneten Assistenten **Weiter** aus, um mit dem Bildschirm **Schutzgruppentyp auswählen** fortzufahren.

   ![Der Assistent zum Erstellen einer neuen Schutzgruppe wird geöffnet.](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Wählen Sie auf dem Bildschirm **Schutzgruppentyp auswählen** die Option **Server** und dann **Weiter** aus.

    ![Schutzgruppentyp auswählen](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Der Bildschirm **Gruppenmitglieder auswählen** wird geöffnet.

    ![Auswählen von Gruppenmitgliedern](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Wählen Sie auf dem Bildschirm **Gruppenmitglieder auswählen** die Option **+** aus, um die Liste mit Unterelementen zu erweitern. Aktivieren Sie das Kontrollkästchen für alle Elemente, die Sie schützen möchten. Nachdem alle Elemente ausgewählt wurden, wählen Sie **Weiter** aus.

    ![Wählen Sie die einzelnen Elemente aus, die Sie schützen möchten.](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Microsoft empfiehlt, alle Daten, für die eine gemeinsame Schutzrichtlinie gilt, in einer Schutzgruppe zusammenzufassen. Ausführliche Informationen zur Planung und Bereitstellung von Schutzgruppen finden Sie im System Center DPM-Artikel [Bereitstellen von Schutzgruppen](/system-center/dpm/create-dpm-protection-groups).

4. Geben Sie auf dem Bildschirm **Datenschutzmethode auswählen** einen Namen für die Schutzgruppe ein. Aktivieren Sie das Kontrollkästchen **Ich möchte kurzfristigen Schutz per:** und **Ich möchte Onlineschutz**. Wählen Sie **Weiter** aus.

    ![Auswählen der Datenschutzmethode](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Um **Ich möchte Onlineschutz** auszuwählen, müssen Sie zuerst **Ich möchte kurzfristigen Schutz per: Datenträger** auswählen. Azure Backup Server sorgt nicht mittels Bandtechnologie für Schutz, weshalb ein Datenträger die einzige Wahl für kurzfristigen Schutz ist.

5. Wählen Sie auf dem Bildschirm **Kurzfristige Ziele angeben**, wie lange die auf dem Datenträger gespeicherten Wiederherstellungspunkte beibehalten und wann inkrementelle Sicherungen gespeichert werden sollen. Wählen Sie **Weiter** aus.

    > [!IMPORTANT]
    > Sie sollten Sicherungsdaten für die Wiederherstellung des Betriebs **nicht** länger als fünf Tage auf Datenträgern aufbewahren, die an Azure Backup Server angefügt sind.
    >

    ![Kurzfristige Ziele angeben](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    Anstatt ein Intervall für inkrementelle Sicherungen auszuwählen, wählen Sie **Direkt vor einem Wiederherstellungspunkt** aus, um eine schnelle vollständige Sicherung unmittelbar vor jedem geplanten Wiederherstellungspunkt durchzuführen. Wenn Sie Anwendungsworkloads schützen, erstellt Azure Backup Server Wiederherstellungspunkte gemäß dem Zeitplan für die Synchronisierungsfrequenz (sofern die Anwendung inkrementelle Sicherungen unterstützt). Wenn die Anwendung keine inkrementellen Sicherungen unterstützt, führt Azure Backup Server eine schnelle vollständige Sicherung durch.

    Geben Sie für **Dateiwiederherstellungspunkte** an, wann Wiederherstellungspunkte erstellt werden sollen. Wählen Sie **Ändern** aus, um die Uhrzeiten und Wochentage festzulegen, an denen Wiederherstellungspunkte erstellt werden.

6. Überprüfen Sie auf dem Bildschirm **Datenträgerzuordnungen überprüfen** den Speicherplatz im Speicherpool, der der Schutzgruppe zugeordnet ist.

    **Gesamtdatengröße** ist die Größe der Daten, die Sie sichern möchten. **Bereitzustellender Speicherplatz** für Azure Backup Server ist der für die Schutzgruppe empfohlene Speicherplatz. Azure Backup Server wählt auf Grundlage der Einstellungen das ideale Sicherungsvolume. Sie können jedoch in „Details zur Datenträgerzuordnung“ das gewählte Sicherungsvolume ändern. Wählen Sie für die Workloads im Dropdownmenü den bevorzugten Speicher aus. Durch Ihre Änderungen verändern sich im Bereich „Verfügbarer Speicherplatz“ die Werte für „Gesamtspeicher“ und „Freier Speicher“. Nicht ausreichend bereitgestellter Speicherplatz ist die Menge an Speicher, die laut Vorschlag von Azure Backup Server zum Sicherstellen künftiger reibungsloser Sicherungen dem Volume hinzugefügt werden sollte.

7. Wählen Sie unter **Replikaterstellungsmethode auswählen** aus, wie die erste vollständige Datenreplikation erfolgen soll. Wenn Sie sich für die Replikation über das Netzwerk entscheiden, empfiehlt Azure, hierfür eine Nebenzeit auszuwählen. Ziehen Sie bei großen Datenmengen oder nicht optimalen Netzwerkbedingungen die Replikation der Daten mit Wechselmedien in Betracht.

8. Legen Sie unter **Konsistenzprüfungsoptionen auswählen** fest, wie Konsistenzprüfungen automatisiert werden sollen. Aktivieren Sie für Konsistenzprüfungen die Ausführung nur bei inkonsistenter Datenreplikation oder nach einem Zeitplan. Wenn Sie keine automatische Konsistenzprüfung konfigurieren möchten, können Sie jederzeit eine manuelle Überprüfung wie folgt ausführen:
    * Klicken Sie im Bereich **Schutz** der Azure Backup Server-Konsole mit der rechten Maustaste auf die Schutzgruppe, und wählen Sie **Konsistenzprüfung ausführen** aus.

9. Wenn Sie die Sicherung in Azure ausgewählt haben, vergewissern Sie sich, dass auf der Seite **Online zu schützende Daten angeben** die Workloads ausgewählt sind, die Sie in Azure sichern möchten.

10. Geben Sie unter **Onlinesicherungszeitplan angeben** an, wann inkrementelle Sicherungen in Azure erfolgen sollen.

    Sie können tägliche, wöchentliche, monatliche und jährliche Sicherungen planen und Datum und Uhrzeit ihrer Ausführung festlegen. Sicherungen können bis zu zweimal täglich erfolgen. Bei jeder Ausführung eines Sicherungsauftrags wird ein Datenwiederherstellungspunkt in Azure anhand der Kopie der Sicherungsdaten erstellt, die auf dem Azure Backup Server-Datenträger gespeichert sind.

11. Wählen Sie auf der Seite **Onlineaufbewahrungsrichtlinie angeben** aus, wie die Wiederherstellungspunkte aus den täglichen, wöchentlichen, monatlichen und jährlichen Sicherungen in Azure aufbewahrt werden.

12. Geben Sie auf der Seite **Onlinereplikation wählen** an, wie die erste vollständige Replikation der Daten erfolgt.

13. Überprüfen Sie unter **Zusammenfassung** Ihre Einstellungen. Wenn Sie **Gruppe erstellen** auswählen, erfolgt die erstmalige Datenreplikation. Nach Abschluss der Datenreplikation wird der Status der Schutzgruppe auf der Seite **Status** als **OK** angezeigt. Der erstmalige Sicherungsauftrag erfolgt entsprechend den Einstellungen der Schutzgruppe.

## <a name="recover-file-data"></a>Wiederherstellen von Dateidaten

Verwenden Sie die Azure Backup Server-Konsole, um Daten auf dem virtuellen Computer wiederherzustellen.

1. Wählen Sie in der Azure Backup Server-Konsole auf der Navigationsleiste **Wiederherstellung** aus, und suchen Sie die Daten, die Sie wiederherstellen möchten. Wählen Sie die Daten im Ergebnisbereich aus.

2. Im Kalender im Abschnitt „Wiederherstellungspunkte“ bedeuten fett gedruckte Datumsangaben, dass Wiederherstellungspunkte verfügbar sind. Wählen Sie das Datum aus, für das die Wiederherstellung durchgeführt werden soll.

3. Wählen Sie im Bereich **Wiederherstellbares Element** das Element aus, das Sie wiederherstellen möchten.

4. Wählen Sie im Bereich **Aktionen** die Option **Wiederherstellen** aus, um den Wiederherstellungs-Assistenten zu öffnen.

5. Sie können Daten wie folgt wiederherstellen:

    * **Am ursprünglichen Speicherort wiederherstellen**: Wenn der Clientcomputer über VPN verbunden ist, funktioniert diese Option nicht. Wählen Sie stattdessen einen anderen Speicherort aus, und kopieren Sie Daten von diesem Speicherort.
    * **An anderem Speicherort wiederherstellen**

6. Geben Sie die Wiederherstellungsoptionen an:

    * Wählen Sie für **Wiederherstellungsverhalten für vorhandene Versionen** entweder **Kopie erstellen**, **Überspringen** oder **Überschreiben** aus. „Überschreiben“ ist nur für das Wiederherstellen am ursprünglichen Speicherort verfügbar.
    * Wählen Sie für **Sicherheit wiederherstellen** entweder **Sicherheitseinstellungen des Zielcomputers anwenden** oder **Sicherheitseinstellungen der Wiederherstellungspunktversion anwenden**.
    * Wählen Sie für **Netzwerk-Bandbreiteneinschränkung** die Option **Ändern** aus, um die Netzwerk-Bandbreiteneinschränkung zu aktivieren.
    * **Benachrichtigung**: Wählen Sie **Eine E-Mail senden, wenn diese Wiederherstellung abgeschlossen ist** aus, und geben Sie dann die Empfänger an, die die Benachrichtigung erhalten sollen. Trennen Sie die E-Mail-Adressen durch Kommas.
    * Wählen Sie **Weiter** aus, nachdem Sie Ihre Auswahl getroffen haben.

7. Überprüfen Sie Ihre Wiederherstellungseinstellungen, und wählen Sie **Wiederherstellen** aus.

    >[!Note]
    >Alle Synchronisierungsaufträge für das ausgewählte Wiederherstellungselement werden abgebrochen, während der Wiederherstellungsauftrag ausgeführt wird.

Wenn Sie Modern Backup Storage(MBS) verwenden, wird die Wiederherstellung durch Endbenutzer für Dateiserver nicht unterstützt. Diese Art der Wiederherstellung weist eine Abhängigkeit vom Volumeschattenkopie-Dienst auf, den Modern Backup Storage nicht verwendet. Wenn die Wiederherstellung durch Endbenutzer aktiviert ist, führen Sie die folgenden Schritte aus, um Daten wiederherzustellen:

1. Navigieren Sie zu den geschützten Dateien, klicken Sie mit der rechten Maustaste auf den Dateinamen, und wählen Sie **Eigenschaften** aus.

2. Wählen Sie im Menü **Eigenschaften** die Option **Vorherige Versionen** und dann die Version aus, die Sie wiederherstellen möchten.

## <a name="view-azure-backup-server-with-a-vault"></a>Anzeigen von Azure Backup Server mit einem Tresor

Mit folgenden Schritten können Sie Azure Backup Server-Entitäten im Azure-Portal anzeigen:

1. Recovery Services-Tresor öffnen
2. Sicherungsinfrastruktur auswählen
3. Sicherungsverwaltungsserver anzeigen

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Verwendung von Azure Backup Server zum Schützen anderer Workloads finden Sie in einem der folgenden Artikel:

* [Informationen zu Azure Backup-Diensten](./backup-overview.md)
* [Informationen zu Azure AD](../active-directory/fundamentals/active-directory-whatis.md)
* [Informationen zum Azure Tresor für Wiederherstellungsdienste (Recovery Services)](./backup-azure-recovery-services-vault-overview.md)
* [Informationen zu Azure Storage](../storage/common/storage-introduction.md)
* [Informationen zu Azure Stack Hub](/azure-stack/operator/azure-stack-overview)
* [Sichern einer SharePoint-Farm](./backup-mabs-sharepoint-azure-stack.md)
* [Sichern einer SQL Server-Instanz](./backup-mabs-sql-azure-stack.md)
