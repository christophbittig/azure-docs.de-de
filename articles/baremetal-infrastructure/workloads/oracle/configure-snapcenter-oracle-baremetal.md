---
title: Konfigurieren von SnapCenter für Oracle in der BareMetal-Infrastruktur
description: Erfahren Sie, wie Sie SnapCenter für Oracle in der BareMetal-Infrastruktur konfigurieren können.
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/05/2021
ms.openlocfilehash: edc75bd8d5fb2a64e22b2dd8f53fceda092161c3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676103"
---
# <a name="configure-snapcenter-for-oracle-on-baremetal-infrastructure"></a>Konfigurieren von SnapCenter für Oracle in der BareMetal-Infrastruktur

In diesem Artikel werden die Schritte zum Konfigurieren von NetApp SnapCenter zur Ausführung von Oracle in der BareMetal-Infrastruktur erläutert.

## <a name="add-storage-hosts-to-snapcenter"></a>Hinzufügen von Speicherhosts zu SnapCenter

Zunächst fügen wir Speicherhosts zum SnapCenter hinzu. 

Wenn Sie die SnapCenter-Sitzung beginnen und die Sicherheitsausnahme speichern, startet die Anwendung. Melden Sie sich im SnapCenter auf Ihrem virtuellen Computer (VM) mit Ihren AD DS-Anmeldeinformationen an.

https://\<hostname\>:8146/

Nun können Sie sowohl einen Produktionsspeicherort als auch einen sekundären Speicherort hinzufügen.

### <a name="add-the-production-storage-location"></a>Hinzufügen des Produktionsspeicherorts

So fügen Sie Ihren virtuellen Produktionsspeichercomputer (SVM) hinzu:

1. Wählen Sie im linken Menü die Option **Speichersysteme** und dann **+ Neu** aus.

2. Geben Sie die Informationen zum **Hinzufügen des Speichersystems** ein:

      - Speichersystem: Geben Sie die von Microsoft Operations bereitgestellte SVM-IP-Adresse ein.
      - Geben Sie den erstellten Benutzernamen ein. Die Voreinstellung ist **snap center**.
      - Geben Sie das Kennwort ein, das beim Ändern des [Kundenkennworts durch](set-up-snapcenter-to-route-traffic.md#steps-to-integrate-snapcenter) Microsoft Operations mithilfe von REST erstellt wurde.
      - Lassen Sie die Option **Autosupport-Benachrichtigung für fehlgeschlagene Vorgänge an das Speichersystem senden** deaktiviert.
      - Wählen Sie **SnapCenter-Ereignisse in syslog protokollieren** aus.

3. Klicken Sie auf **Submit** (Senden).

    Nachdem der Speicher überprüft wurde, wird die IP-Adresse des hinzugefügten Speichersystems mit dem eingegebenen Benutzernamen angezeigt.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/oracle-baremetal-snapcenter-add-production-storage-complete.png" alt-text="Screenshot: IP-Adresse des hinzugefügten Speichersystems":::

    Der Standardwert ist eine SVM pro Mandant. Wenn ein Kunde mehrere Mandanten hat, empfiehlt es sich, alle SVMs hier in SnapCenter zu konfigurieren.

### <a name="add-secondary-storage-location"></a>Hinzufügen eines sekundären Speicherorts

Zum Hinzufügen des Speicherorts für die Notfallwiederherstellung muss das Peering für die Kundenabonnements sowohl am primären Standort als auch am DR-Standort durchgeführt werden. Wenden Sie sich an Microsoft Operations, um Hilfe zu erhalten.

Das Hinzufügen eines sekundären Speicherorts ähnelt dem Hinzufügen des primären Speicherorts. Nach dem Peering des primären Standorts und des Speicherorts für die Notfallwiederherstellung  sollte der Zugriff auf den sekundären Speicherort jedoch durch Pingen des Speichers am sekundären Standort überprüft werden. 

>[!NOTE]
>Wenn das Pingen nicht erfolgreich ist, liegt dies in der Regel daran, dass auf dem Host für das virtuelle Client-LAN (VLAN) keine Standardroute vorhanden ist.

Nachdem das Pingen überprüft wurde, wiederholen Sie die Schritte, die Sie zum Hinzufügen des primären Speichers verwendet haben, nur jetzt für den Speicherort für die Notfallwiederherstellung auf einem DR-Host. Es wird empfohlen, dasselbe Zertifikat an beiden Standorten zu verwenden. Allerdings ist dies nicht zwingend erforderlich.

## <a name="set-up-oracle-hosts"></a>Einrichten von Oracle-Hosts

>[!NOTE]
>Dieser Prozess gilt für alle Oracle-Hosts unabhängig von ihrem Standort: Produktion oder Notfallwiederherstellung.

1. Vor dem Hinzufügen der Hosts zu SnapCenter und dem Installieren der SnapCenter-Plug-Ins muss JAVA 1.8 installiert werden. Überprüfen Sie, ob Java 1.8 auf jedem Host installiert ist, bevor Sie fortfahren.

2. Erstellen Sie auf jedem RAC-Host (Oracle Real Application Clusters) denselben Nicht-Stamm-Benutzer und fügen Sie ihn in /etc/sudoers hinzu. Sie müssen ein neues Kennwort angeben.

3. Nachdem der Benutzer erstellt wurde, muss er in /etc/sudoers mit expliziten Berechtigungen hinzugefügt werden. Diese Berechtigungen finden Sie, indem Sie zum folgenden Speicherort auf der SnapCenter-VM navigieren: C:\ProgramData\NetApp\SnapCenter\Package Repository. Dort öffnen Sie die Datei oracle\_checksum.

4. Kopieren Sie je nach sudo-Paket die entsprechenden Befehlsreihen, wobei LINUXUSER durch den neu erstellten Benutzernamen ersetzt wird. 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/netapp-snapcenter-oracle-checksum-details.png" alt-text="Screenshot: Details der oracle_checksum-Datei":::

    Der Befehlssatz für das sudo-Paket 1.8.7 oder höher wird in /etc/sudoers kopiert.

5. Nachdem der Benutzer in sudoers hinzugefügt wurde, wählen Sie in SnapCenter **Einstellungen** > **Anmeldeinformationen** > **Neu** aus.

6. Füllen Sie das Feld mit den Anmeldeinformationen wie folgt aus:

    - **Name der Anmeldeinformationen**: Geben Sie einen Namen an, der Benutzername und sudoers identifiziert.
    - **Authentifizierung**: Linux
    - **Benutzername**: Geben Sie den neu erstellten Benutzernamen an.
    - **Kennwort**: \<enter password\>
    - Aktivieren Sie das Kontrollkästchen **sudo-Berechtigungen verwenden**.
    
7. Klicken Sie auf **OK**.

8. Wählen Sie im linken Navigationsbereich die Option **Hosts** und anschließend **+ Hinzufügen** aus.

9. Geben Sie für **Host hinzufügen** die folgenden Werte ein:

    - **Hosttyp**: Linux
    - **Hostname**: Geben Sie entweder den FQDN des primären RAC-Hosts oder die IP-Adresse des primären RAC-Hosts ein.
    - **Anmeldeinformationen**: Wählen Sie im Dropdown die neu erstellten Anmeldeinformationen für sudoers aus.
    - Aktivieren Sie das Kontrollkästchen **Oracle Database**.

    >[!NOTE]
    >Sie müssen eine der tatsächlichen Oracle-Host-IP-Adressen eingeben. Die Eingabe einer Knoten-VIP oder einer Scan-IP wird nicht unterstützt.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-details.png" alt-text="Screenshot: Details des neuen Hosts":::
    
10. Wählen Sie **Weitere Optionen** aus. Stellen Sie sicher, dass **Alle Hosts im Oracle-RAC hinzufügen** ausgewählt ist. Wählen Sie **Speichern** und dann **Senden** aus.

11. Das Plug-In-Installationsprogramm versucht nun, mit der angegebenen IP-Adresse zu kommunizieren. Wenn die Kommunikation erfolgreich ist, wird die Identität des bereitgestellten Oracle-RAC-Hosts durch Auswählen von **Bestätigen und Senden** bestätigt.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-confirm-fingerprint.png" alt-text="Screenshot: Fingerabdruck des neuen Hosts":::

12. Nachdem die anfängliche Oracle-RAC bestätigt wurde, versucht SnapCenter, alle anderen Oracle-RAC-Server als Teil des Clusters zu bestätigen und mit diesen zu kommunizieren. Wählen Sie **Andere bestätigen und Senden** aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-fingerprint-authentication.png" alt-text="Screenshot: Authentifizierung des Fingerabdrucks":::

    Für verwaltete Hosts wird eine Statusanzeige eingeblendet, auf der Sie sehen, dass SnapCenter das ausgewählte Oracle-Plug-In installiert. Der Installationsfortschritt kann überprüft werden, indem Sie sich die Protokolle unter /opt/NetApp/snapcenter/scc/logs/DISCOVER-PLUGINS\_0.log auf einem der Oracle-RAC-Hosts ansehen. 

    Nachdem die Plug-Ins erfolgreich installiert wurden, erscheint die folgende Anzeige.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-installed-plugins.png" alt-text="Screenshot: Alle installierten SnapCenter-Plug-Ins":::

## <a name="add-credentials-for-oracle-recovery-manager"></a>Hinzufügen von Anmeldeinformationen für Oracle Recovery Manager

Die Authentifizierung für den RMAN-Katalog (Oracle Recovery Manager) erfolgt mittels der RMAN-Katalogdatenbank. Wenn Sie einen externen Katalogmechanismus konfiguriert und Ihre Datenbank für die Katalogisierung einer Datenbank registriert haben, müssen Sie die RMAN-Katalogauthentifizierung hinzufügen. Die RMAN-Anmeldeinformationen können zu einem beliebigen Zeitpunkt hinzugefügt werden.

1. Wählen Sie in SnapCenter im linken Menü **Einstellungen** und dann **Anmeldeinformationen** aus. Klicken Sie in der Ecke oben rechts auf **Neu**.

2. Geben Sie den **Namen der Anmeldeinformationen** ein, um die in SnapCenter gespeicherten RMAN-Anmeldeinformationen aufzurufen. Wählen Sie in der Dropdownliste zur **Authentifizierung** die Option **Oracle RMAN-Katalog zur Authentifizierung** aus. Geben Sie Ihren Benutzernamen und Ihr Kennwort ein. Klicken Sie auf **OK**.

3. Nachdem die Anmeldeinformationen hinzugefügt wurden, müssen die Datenbankeinstellungen in SnapCenter geändert werden. Wählen Sie die Datenbank in Ressourcen aus. Wählen Sie in der oberen rechten Ecke des Hauptfensters **Datenbankeinstellungen** aus.

4. Wählen Sie in der Anzeige „Datenbankeinstellungen“ die Option **Datenbank konfigurieren** aus.

5. Klappen Sie die Option **Configure RMAN Catalog Settings (RMAN-Katalogeinstellungen konfigurieren)** aus. Wählen Sie in der Dropdownliste **Vorhandene Anmeldeinformationen verwenden**, die zuvor für diese Datenbank festgelegt wurde, die entsprechende Option aus. Fügen Sie den TNS-Namen für die entsprechende Datenbank hinzu. Wählen Sie **OK** aus.

    >[!NOTE]
    >Im vorherigen Schritt sollten für jede eindeutige Kombination aus Benutzername und Kennwörtern unterschiedliche Anmeldeinformationen erstellt werden. Wenn Sie möchten, akzeptiert SnapCenter einen einzelnen Satz von Anmeldeinformationen für alle Datenbanken.
    >
    >Obwohl RMAN-Anmeldeinformationen zur Datenbank hinzugefügt wurden, wird RMAN nur katalogisiert, wenn die Katalogsicherung mit Oracle Recovery Manager (RMAN) auch für jede Schutzrichtlinie überprüft wird, wie im folgenden Abschnitt zum Erstellen von Schutzrichtlinien (gemäß Ihren Sicherungsrichtlinien) erläutert.

## <a name="create-protection-policies"></a>Schutzrichtlinien erstellen

Nachdem Ihre Hosts erfolgreich zu SnapCenter hinzugefügt wurden, können Sie Ihre Schutzrichtlinien erstellen. 

Wählen Sie im linken Menü die Option **Ressourcen** aus. SnapCenter zeigt alle identifizierten Datenbanken an, die auf den RAC-Hosts vorhanden waren. Die Einzelinstanztypen für bn6p1X und Raster werden im Rahmen des Schutzschemas ignoriert. Der Status aller sollte an diesem Punkt „Nicht geschützt“ lauten.

Wie in der [Übersicht](netapp-snapcenter-integration-oracle-baremetal.md#oracle-disaster-recovery-architecture) erläutert, weisen verschiedene Dateitypen unterschiedliche Häufigkeiten für Momentaufnahmen auf. Diese ermöglichen eine lokale RPO von etwa 15 Minuten, da alle 15 Minuten Archivprotokollsicherungen erstellt werden. Die Datendateien werden dann in längeren Zeitintervallen gesichert, z. B. stündlich oder länger. Daher werden zwei verschiedene Richtlinien erstellt.

Nachdem die RAC-Datenbank(en) identifiziert wurden, werden mehrere Schutzrichtlinien erstellt (einschließlich einer Richtlinie für Datendateien und Steuerelementdateien und einer anderen Richtlinie für Archivprotokolle).

### <a name="datafiles-protection-policy"></a>Richtlinie zum Schutz von Datendateien

Folgen Sie diesen Schritten, um eine Schutzrichtlinie für Datendateien zu erstellen.

1. Klicken Sie in SnapCenter im linken Menü auf **Einstellungen** und dann im oberen Menü auf **Richtlinien**. Wählen Sie **Neu** aus.

2. Wählen Sie das Optionsfeld für **Datendateien und Steuerelementdateien** aus und scrollen Sie nach unten.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-new-database-backup-policy.png" alt-text="Screenshot: Details zur neuen Datenbanksicherungsrichtlinie":::

3. Wählen Sie das Optionsfeld für **stündlich** aus. Wenn die Integration in RMAN für Katalogzwecke gewünscht ist und die RMAN-Anmeldeinformationen bereits hinzugefügt wurden, aktivieren Sie das Kontrollkästchen **Katalogsicherung mit Oracle Recovery Manager (RMAN)** . Wählen Sie **Weiter** aus. Wenn die Katalogsicherung überprüft wird, müssen [RMAN-Anmeldeinformationen hinzugefügt werden](#add-credentials-for-oracle-recovery-manager), damit die Katalogisierung erfolgt.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-database-backup-policy-frequency.png" alt-text="Screenshot: Optionen zum Auswählen der Planungshäufigkeit in Ihrer Sicherungsrichtlinie":::

    >[!IMPORTANT]
    >Für die Planungsrichtlinie muss eine Auswahl getroffen werden, auch wenn eine andere Sicherungshäufigkeit als stündlich oder täglich usw. gewünscht ist. Die tatsächliche Sicherungshäufigkeit wird später im Prozess festgelegt. Wählen Sie nur dann „keine“ aus, wenn alle Sicherungen im Rahmen dieser Richtlinie nur bei Bedarf erfolgen sollen.

4. Wählen Sie im linken Menü **Datenaufbewahrung** aus. Es gibt zwei Arten von Aufbewahrungseinstellungen, die für eine Sicherungsrichtlinie festgelegt werden. Die erste Aufbewahrungseinstellung ist die maximale Anzahl bedarfsbasierter Momentaufnahmen, die aufbewahrt werden sollen. Basierend auf Ihren Sicherungsrichtlinien wird eine festgelegte Anzahl von Momentaufnahmen aufbewahrt (siehe die 48 bedarfsabhängigen Momentaufnahmen im folgenden Bsp., die 14 Tage lang aufbewahrt werden). Legen Sie die entsprechende bedarfsabhängige Aufbewahrungsrichtlinie gemäß Ihren Sicherungsrichtlinien fest.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-new-database-backup-policy-retention.png" alt-text="Screenshot: Aufbewahrungseinstellung der Datenbanksicherungsrichtlinie":::

5. Die nächste Einstellung für die Aufbewahrung ist die geplante Anzahl von Momentaufnahmen, die basierend auf dem vorherigen Eintrag stündlich, täglich, wöchentlich usw. gespeichert werden sollen. Wählen Sie **Weiter** aus.

6. Aktivieren Sie in der Anzeige **Replikation** das Kontrollkästchen **SnapMirror nach Erstellung einer lokalen Momentaufnahmekopie** aus und klicken Sie dann auf **Weiter**. Die anderen Einträge verbleiben in der Standardeinstellung.

    >[!NOTE]
    >SnapVault wird in der Oracle-BareMetal-Infrastruktur-Umgebung derzeit nicht unterstützt.

    Die Replikation kann später hinzugefügt werden, indem Sie zur Anzeige Schutzrichtlinie zurückkehren und im linken Menü die Optionen **Schutzrichtlinie ändern** und **Replikation** auswählen.

7. Auf der Seite **Skript** geben Sie alle Skripts ein, die vor oder nach der Oracle-Sicherung ausgeführt werden müssen. Derzeit werden Skripts im Rahmen des SnapCenter-Prozesses nicht unterstützt. Wählen Sie **Weiter** aus.

8. Wählen Sie im linken Menü die Option **Überprüfung** aus. Wenn Sie die Möglichkeit haben möchten, Momentaufnahmen auf Wiederherstellbarkeit zu überprüfen, aktivieren Sie das Kontrollkästchen neben stündlich. Derzeit werden keine Überprüfungsskriptbefehle unterstützt. Wählen Sie **Weiter** aus.

    >[!NOTE]
    >Der tatsächliche Speicherort und Zeitplan des Überprüfungsprozesses wird in einem späteren Abschnitt hinzugefügt: Zuweisen von Schutzrichtlinien zu Ressourcen.

9. Überprüfen Sie auf der Seite **Zusammenfassung**, ob alle Einstellungen wie erwartet eingegeben wurden und wählen Sie **Fertigstellen** aus.

### <a name="archive-logs-protection-policy"></a>Schutzrichtlinie für Archivprotokolle

Führen Sie die oben genannten Schritte erneut aus, um die Schutzrichtlinie für Archivprotokolle zu erstellen. Wählen Sie in Schritt 2 anstelle von „Datendateien und Steuerelementdateien“ das Optionsfeld **Protokolle archivieren** aus.

## <a name="assign-protection-policies-to-resources"></a>Zuweisen von Schutzrichtlinien zu Ressourcen

Nachdem eine Richtlinie erstellt wurde, muss sie einer Ressource zugeordnet werden, damit SnapCenter mit der Ausführung innerhalb dieser Richtlinie beginnen kann.

### <a name="datafiles-resource-group"></a>Datendateien-Ressourcengruppe

1. Wählen Sie im linken Menü **Ressourcen** und dann in der oberen rechten Ecke des Hauptfensters  **Neue Ressourcengruppe** aus.

2. Fügen Sie den **Namen** für diese Ressourcengruppe und alle Tags hinzu, um die Durchsuchbarkeit zu vereinfachen.

    >[!NOTE]
    >Es wird empfohlen, die Option **Benutzerdefiniertes Namensformat für Momentaufnahmekopie verwenden** zu aktivieren und die folgenden Einträge hinzuzufügen: **$RessourcenGruppe**, **$Richtlinie** und **$ZeitplanTyp**. Dadurch wird sichergestellt, dass das Momentaufnahmepräfix dem SnapCenter-Standard entspricht und dass der Name der Momentaufnahme die erforderlichen Details auf einen Blick sichtbar macht. Wenn Sie **Benutzerdefiniertes Namensformat für Momentaufnahmekopie verwenden** deaktiviert lassen, wird jeder Eintrag unter **Name** zum Präfix für die erstellten Momentaufnahmen. Stellen Sie sicher, dass der eingegebene Name die Datenbank identifiziert und was geschützt wird, z. B. Datendateien und Steuerelementdateien. 

3. Fügen Sie unter Sicherungseinstellungen **/u95** hinzu, um Archivprotokolle auszuschließen.

4. Verschieben Sie auf der Seite Ressource alle Datenbanken, die durch dieselbe Sicherungsschutzrichtlinie geschützt sind, von „Verfügbare Ressourcen“ in „Ausgewählte Ressourcen“. Fügen Sie die Oracle-Datenbankinstanzen nicht für jeden Host hinzu, sondern nur die Datenbanken. Wählen Sie **Weiter** aus.

5. Wählen Sie die Schutzrichtlinie für Datendateien und Steuerelementdateien aus, die Sie zuvor erstellt haben. Nachdem Sie die Richtlinie ausgewählt haben, wählen Sie den Bleistift unter Zeitpläne konfigurieren aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-datafiles-resource-group-available-policies.png" alt-text="Screenshot: Auswählen der Schutzrichtlinie zum Konfigurieren von Zeitplänen":::

6. Fügen Sie den Zeitplan für die ausgewählte Richtlinie hinzu, die ausgeführt werden soll. Stellen Sie sicher, dass das Startdatum nach dem aktuellen Datum und der aktuellen Uhrzeit liegt.

    >[!NOTE]
    >Die geplante Häufigkeit muss nicht mit der Häufigkeit übereinstimmen, die beim Erstellen der Richtlinie ausgewählt wurde. Für stündlich wird empfohlen, die Einstellung „Jede Stunde wiederholen“ zu belassen. Die Startzeit definiert dann stündlich die Startzeit der Sicherung. Stellen Sie sicher, dass sich der Zeitplan für den Schutz der Datendateien nicht mit dem Zeitplan für den Schutz der Archivprotokolle überschneidet, da zu einem bestimmten Zeitpunkt nur eine Sicherung aktiv sein kann.

7. Durch die Überprüfung wird sichergestellt, dass die erstellte Momentaufnahme verwendet werden kann. Der Überprüfungsprozess ist umfangreich (einschließlich der Erstellung von Klonen aller Oracle-Datenbankvolumen, dem Einbinden der Datenbank auf den primären Host und dem Überprüfen der Wiederherstellbarkeit). Dieser Vorgang ist sehr zeitaufwändig und ressourcenintensiv. Wenn Sie den Überprüfungszeitplan konfigurieren möchten, wählen Sie das **+** -Zeichen aus.

    >[!NOTE]
    >Der Überprüfungsprozess belegt für einen beträchtlichen Zeitraum Ressourcen auf dem Host. Wenn Sie die Überprüfung hinzufügen, sollten Sie dies auf einem Host am sekundären Standort tun (falls verfügbar).
    
    Der folgende Screenshot zeigt die Überprüfung einer neuen Ressourcengruppe, für die bei ihrer Erstellung die Replikation nicht aktiviert wurde und die Momentaufnahmen nicht repliziert wurden. 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-resource-group-verification-schedules.png" alt-text="Screenshot: Konfigurieren des Überprüfungszeitplans für die Ressourcengruppe":::

    Wenn die Replikation aktiviert ist und für die Überprüfung ein Host am Standort für die Notfallwiederherstellung verwendet wird, fahren Sie mit Schritt 2 im folgenden Unterabschnitt fort. Mit diesem Schritt können Sie sekundäre Locators laden, um Sicherungen am sekundären Standort zu überprüfen.

### <a name="add-verification-schedule-for-new-resource-group"></a>Hinzufügen eines Überprüfungszeitplans für eine neue Ressourcengruppe

1. Klicken Sie entweder auf **Überprüfung nach Sicherung ausführen** oder **Geplante Überprüfung ausführen** und wählen Sie in der Dropdownliste eine vorab geplante Häufigkeit aus. Wenn die Notfallwiederherstellung aktiviert ist, können Sie **Überprüfen am sekundären Speicherort** auswählen, um die Häufigkeit am DR-Standort auszuführen, was wiederum die Ressourcenbelastung in der Produktion reduziert. Wählen Sie **OK** aus.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-resource-group-add-verification-schedules.png" alt-text="Screenshot: Hinzufügen eines Überprüfungszeitplans":::

    Wenn die Überprüfung nicht erforderlich oder bereits lokal eingerichtet ist, fahren Sie mit dem SMTP-Setup (Schritt 5) weiter unten fort.

2. Wenn Sie das SnapMirror-Update aktiviert haben (bei der Erstellung der Datendateischutzrichtlinie) und ein sekundärer Speicherort hinzugefügt wurde, erkennt SnapCenter die Replikation zwischen den beiden Standorten. In diesem Fall erscheint eine Anzeige, in der Sie sekundäre Locators laden können, um Sicherungen am sekundären Standort zu überprüfen. Wählen Sie **Load locators (Locators laden)** aus.

3. Nachdem Sie Load Locators ausgewählt haben, zeigt SnapCenter die gefundenen SnapMirror-Beziehungen (mit den enthaltenen Datendateien und Steuerelementdateien) an. Diese sollten mit dem von Microsoft Operations bereitgestellten Notfallwiederherstellungsframework übereinstimmen. Wählen Sie unter „Zeitpläne konfigurieren“ den Bleistift aus.

4. Aktivieren Sie das Kontrollkästchen **Am sekundären Speicherort überprüfen**. 

5. Wenn ein SMTP-Server verfügbar ist, kann SnapCenter eine E-Mail an SnapCenter-Administratoren senden. Geben Sie Folgendes ein, wenn Sie eine E-Mail senden möchten.

    - **E-Mail-Einstellung**: Geben Sie Ihre Einstellung für die Häufigkeit des E-Mail-Empfangs ein.
    - **Von**: Geben Sie die E-Mail-Adresse ein, von der SnapCenter E-Mails sendet.
    - **An**: Geben Sie die E-Mail-Adresse ein, an  die SnapCenter E-Mails sendet.
    - **Betreff**: Geben Sie den Betreff ein, den SnapCenter beim Senden der E-Mail verwendet.
    - Aktivieren Sie das Kontrollkästchen **Auftragsbericht anfügen**.
    - Wählen Sie **Weiter** aus.

6. Überprüfen Sie auf der Seite Zusammenfassung alle Einstellungen und klicken Sie auf Fertigstellen.

Nachdem eine Ressourcengruppe erstellt wurde, führen Sie zum Identifizieren der Ressourcengruppe folgende Schritte durch:

1. Wählen Sie im linken Menü die Option **Ressourcen** aus.
2. Klicken Sie im Hauptfenster neben Ansicht auf das Dropdownmenü und wählen Sie **Ressourcengruppe** aus.
 
    Wenn es sich um Ihre erste Ressourcengruppe handelt, ist nur die neu erstellte Ressourcengruppe für Datendateien und Steuerelementdateien vorhanden. Wenn Sie auch eine Archivprotokollressourcengruppe erstellt haben, sehen Sie diese ebenfalls.

### <a name="archive-log-resource-group"></a>Archivprotokollressourcengruppe

Um Ihrer Archivprotokollressourcengruppe Schutzrichtlinien zuzuweisen, führen Sie die gleichen Schritte aus, die Sie beim Zuweisen von Schutzrichtlinien zu Ihrer Datendatei-Ressourcengruppe befolgt haben. Dies sind die einzigen Unterschiede:

- Fügen Sie in Schritt 3 nicht **/u95** hinzu, um Archivprotokolle auszuschließen. Lassen Sie dieses Feld leer.
- In Schritt 6 wird empfohlen, Archivprotokolle alle 15 Minuten zu sichern.
- Die Registerkarte „Überprüfung“ ist für Archivprotokolle leer. 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie eine bedarfsorientierte Sicherung Ihrer Oracle Database in SnapCenter erstellen:

> [!div class="nextstepaction"]
> [Bedarfsgesteuertes Erstellen von Sicherungskopien](create-on-demand-backup-oracle-baremetal.md)
