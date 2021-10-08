---
title: Konfigurieren der operativen Sicherung für Azure-Blobs
description: Informationen zum Konfigurieren und Verwalten der operativen Sicherung für Azure-Blobs
ms.topic: conceptual
ms.date: 09/28/2021
ms.openlocfilehash: 24a0f31a35342b53835563fdcca9754abd5d57ee
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153898"
---
# <a name="configure-operational-backup-for-azure-blobs"></a>Konfigurieren der operativen Sicherung für Azure-Blobs

Mit Azure Backup können Sie die operative Sicherung zum Schutz von Blockblobs in Ihren Speicherkonten einfach konfigurieren. In diesem Artikel wird erläutert, wie Sie die operative Sicherung für ein oder mehrere Speicherkonten über das Azure-Portal konfigurieren. In dem Artikel werden die folgenden Themen behandelt:

- Wissenswertes vor dem Start
- Erstellen eines Sicherungstresors
- Erteilen von Berechtigungen für den Sicherungstresor auf den zu schützenden Speicherkonten
- Erstellen einer Sicherungsrichtlinie
- Konfigurieren der operativen Sicherung für ein oder mehrere Speicherkonten
- Auswirkungen auf die Sicherung von Speicherkonten

## <a name="before-you-start"></a>Vorbereitung

- Die operative Sicherung von Blobs ist eine lokale Sicherungslösung, die Daten für eine bestimmte Dauer im Quellspeicherkonto selbst verwaltet. Bei dieser Lösung wird keine zusätzliche Kopie der Daten im Tresor aufbewahrt.
- Mit dieser Lösung können Sie Ihre Daten bis zu 360 Tage lang für die Wiederherstellung aufbewahren. Lange Aufbewahrungszeiträume können jedoch zu einer längeren Dauer des Wiederherstellungsvorgangs führen.
- Die Lösung kann nur für Wiederherstellungen im Quellspeicherkonto verwendet werden und kann dazu führen, dass Daten überschrieben werden.
- Wenn Sie einen Container aus dem Speicherkonto löschen, indem Sie den Vorgang Container löschen aufrufen, kann dieser Container nicht mit einem Wiederherstellungsvorgang wiederhergestellt werden. Löschen Sie die einzelnen Blobs, anstatt einen ganzen Container zu löschen, wenn Sie sie möglicherweise später wiederherstellen möchten. Außerdem empfiehlt Microsoft, zusätzlich zur operativen Sicherung das vorläufige Löschen für Container zu aktivieren, um ein versehentliches Löschen von Containern zu verhindern.
- Vergewissern Sie sich, dass der Anbieter **Microsoft.DataProtection** für Ihr Abonnement registriert ist.
- Unter [Unterstützungsmatrix](blob-backup-support-matrix.md) erfahren Sie mehr über unterstützte Szenarien, Einschränkungen und Verfügbarkeit.

## <a name="create-a-backup-vault"></a>Erstellen eines Sicherungstresors

Ein [Sicherungstresor](backup-vault-overview.md) ist eine Verwaltungsentität, in der die im Lauf der Zeit erstellten Wiederherstellungspunkte gespeichert werden. Er bietet eine Schnittstelle zum Durchführen von sicherungsbezogenen Vorgängen. Hierzu gehören das Erstellen von bedarfsgesteuerten Sicherungen, Durchführen von Wiederherstellungen und Erstellen von Sicherungsrichtlinien. Die operative Sicherung von Blobs ist zwar eine lokale Sicherung und „speichert“ keine Daten im Tresor, der Tresor wird jedoch für verschiedene Verwaltungsvorgänge benötigt.

>[!NOTE]
>Der Sicherungstresor ist eine neue Ressource, die für die Sicherung neuer unterstützter Workloads verwendet wird und sich von dem bereits vorhandenen Recovery Services-Tresor unterscheidet.

Anweisungen zum Erstellen eines Sicherungstresors finden Sie in der [Dokumentation zum Sicherungstresor](backup-vault-overview.md#create-a-backup-vault).

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>Erteilen von Berechtigungen für den Sicherungstresor auf Speicherkonten

Die operative Sicherung schützt auch das Speicherkonto (das die zu schützenden Blobs enthält) vor versehentlichem Löschen, indem eine sicherungseigene Löschsperre angewendet wird. Hierfür ist es erforderlich, dass der Sicherungstresor über bestimmte Berechtigungen auf den Speicherkonten verfügt, die geschützt werden müssen. Aus Gründen der Benutzerfreundlichkeit wurden diese minimalen Berechtigungen unter der Rolle **Mitwirkender für Speicherkontosicherung** zusammengefasst. 

Es wird empfohlen, diese Rolle dem Sicherungstresor zuzuweisen, bevor Sie die Sicherung konfigurieren. Sie können die Rollenzuweisung jedoch auch beim Konfigurieren der Sicherung vornehmen. [Erfahren Sie mehr](#using-backup-center) über das Konfigurieren der Sicherung über Backup Center. 

Führen Sie die folgenden Schritte aus, um die erforderliche Rolle für Speicherkonten zuzuweisen, die Sie schützen müssen:

>[!NOTE]
>Sie können die Rollen dem Tresor auch nach Belieben auf Abonnement- oder Ressourcengruppenebene zuweisen.

1. Navigieren Sie in dem zu schützenden Speicherkonto zur Registerkarte **Access Control (IAM)** im linken Navigationsbereich.
1. Wählen Sie **Rollenzuordnungen hinzufügen** aus, um die gewünschte Rolle zuzuweisen.

    ![Hinzufügen von Rollenzuweisungen](./media/blob-backup-configure-manage/add-role-assignments.png)

1. Im Bereich „Rollenzuweisung hinzufügen“:

    1. Wählen Sie unter **Rolle** die Option **Mitwirkender für Speicherkontosicherung** aus.
    1. Wählen Sie unter **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.
    1. Suchen Sie nach dem Sicherungstresor, den Sie zum Sichern von Blobs in diesem Speicherkonto verwenden möchten, und wählen Sie ihn in den Suchergebnissen aus.
    1. Wählen Sie **Speichern** aus.

        ![Rollenzuweisungsoptionen](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >Die Zuweisung der Rolle kann bis zu 10 Minuten dauern.

## <a name="create-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

Eine Sicherungsrichtlinie regelt in der Regel die Aufbewahrung und den Zeitplan für Ihre Sicherungen. Da die operative Sicherung für Blobs kontinuierlicher Natur ist, benötigen Sie keinen Zeitplan für die Durchführung von Sicherungen. Die Richtlinie ist im Grunde genommen erforderlich, um den Aufbewahrungszeitraum festzulegen. Sie können die Sicherungsrichtlinie verwenden und wiederverwenden, um die Sicherung für mehrere Speicherkonten in einem Tresor zu konfigurieren.

Hier finden Sie die Schritte zum Erstellen einer Sicherungsrichtlinie für die operative Sicherung Ihrer Blobs:

1. Navigieren Sie in Ihrem Sicherungstresor zu **Sicherungsrichtlinien**, und wählen Sie **+ Hinzufügen** aus, um mit der Erstellung einer Sicherungsrichtlinie zu beginnen.

    ![Sicherungsrichtlinien](./media/blob-backup-configure-manage/backup-policies.png)

1. Geben Sie auf der Registerkarte **Grundlagen** einen Namen für die Sicherungsrichtlinie ein, und wählen Sie **Azure-Blobs** als Datenquellentyp aus. Sie können auch die Details für den ausgewählten Tresor anzeigen.

    ![Erstellen der Sicherungsrichtlinie](./media/blob-backup-configure-manage/create-backup-policy.png)

    >[!NOTE]
    >Obwohl die **Sicherungsspeicherredundanz** des Tresors angezeigt wird, gilt die Redundanz nicht wirklich für die operative Sicherung von Blobs, da die Sicherung lokaler Natur ist und keine Daten im Sicherungstresor gespeichert werden. Der Sicherungstresor hier ist die Verwaltungsentität, die Sie bei der Verwaltung des Schutzes von Blockblobs in ihren Speicherkonten unterstützt.

1. Geben Sie auf der Registerkarte **Sicherungsrichtlinie** die Details zur Aufbewahrung an. Sie sehen, dass bereits eine Aufbewahrungsregel mit der Bezeichnung **Standard** mit einem Aufbewahrungszeitraum von 30 Tagen vorhanden ist. Wenn Sie den Aufbewahrungszeitraum bearbeiten möchten, verwenden Sie das Symbol **Aufbewahrungsregel bearbeiten**, um die Dauer der Aufbewahrung der Daten anzugeben und zu bearbeiten. Sie können eine Aufbewahrungsdauer von bis zu 360 Tagen angeben.

    ![Registerkarte „Sicherungsrichtlinie“](./media/blob-backup-configure-manage/backup-policy-tab.png)

    >[!NOTE]
    >Eine Wiederherstellung über einen längeren Zeitraum kann dazu führen, dass die Wiederherstellungsvorgänge länger dauern. Zudem ist die erforderliche Zeit zum Wiederherstellen einer Gruppe von Daten von der Anzahl der Schreib- und Löschvorgänge abhängig, die während des Wiederherstellungszeitraums vorgenommen werden. Beispielsweise dauert bei einem Konto mit 1 Million Objekten, in dem pro Tag 3.000 Objekte hinzugefügt und 1.000 Objekte gelöscht werden, die Wiederherstellung zu einem 30 Tage in der Vergangenheit liegenden Zeitpunkt ungefähr zwei Stunden. Bei einem Konto mit einer solchen Änderungsrate sollte weder ein Aufbewahrungszeitraum von mehr als 90 Tagen noch eine Wiederherstellung zu einem so weit in der Vergangenheit liegenden Zeitpunkt angewendet werden.

1. Überprüfen Sie im Bereich **Überprüfen + Erstellen** alle Details für die Richtlinie, und wählen Sie anschließend **Erstellen** aus, um die Erstellung der Richtlinie abzuschließen. Eine Benachrichtigung zur Bestätigung wird angezeigt, sobald die Sicherungsrichtlinie erstellt wurde und zur Verwendung bereit ist.

    ![Richtlinie zum Überprüfen und Erstellen](./media/blob-backup-configure-manage/review-create.png)

## <a name="configure-backup"></a>Konfigurieren der Sicherung

Die Sicherung von Blobs wird auf der Ebene des Speicherkontos konfiguriert. So sind alle Blobs im Speicherkonto mit der operativen Sicherung geschützt.

Sie können die Sicherung für mehrere Speicherkonten über Backup Center konfigurieren. Sie können die Sicherung für ein Speicherkonto auch mithilfe der Eigenschaften für den **Schutz von Daten** des Speicherkontos konfigurieren. In diesem Abschnitt werden beide Möglichkeiten zum Konfigurieren der Sicherung erläutert.

### <a name="using-backup-center"></a>Verwenden von Backup Center

So beginnen Sie mit dem Konfigurieren der Sicherung

1. Suchen Sie in der Suchleiste nach **Backup Center**.

1. Navigieren Sie zu **Übersicht** ->  **+ Sicherung**.

    ![Backup Center-Übersicht](./media/blob-backup-configure-manage/backup-center-overview.png)

1. Wählen Sie auf der Registerkarte **Initiieren: Sicherung konfigurieren** die Option **Azure-Blobs (Azure Storage)** als Datenquellentyp aus.

    ![Registerkarte „Initiieren: Sicherung konfigurieren“](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. Geben Sie auf der Registerkarte **Grundlagen** die Option **Azure-Blobs (Azure Storage)** als Datenquellentyp an, und wählen Sie den Sicherungstresor aus, dem Sie Ihre Speicherkonten zuordnen möchten.<br></br>In demselben Bereich können Sie Details zum ausgewählten Tresor anzeigen.

    ![Registerkarte „Grundeinstellungen“](./media/blob-backup-configure-manage/basics-tab.png)

    >[!NOTE]
    >Nur operative Sicherungen werden für Blobs aktiviert, die Sicherungen im Quellspeicherkonto (und nicht im Sicherungstresor) speichern. Daher gilt der für den Tresor ausgewählte Redundanztyp für Sicherungsspeicher nicht für die Sicherung von Blobs.

1. Wählen Sie die Sicherungsrichtlinie aus, die Sie zum Festlegen der Aufbewahrung verwenden möchten.<br></br>Im unteren Teil des Bildschirms können Sie die Details der ausgewählten Richtlinie anzeigen. In der Spalte „Speicher für operative Daten“ wird die in der Richtlinie definierte Aufbewahrungsdauer angezeigt. **Operativ** impliziert, dass die Daten lokal im Quellspeicherkonto verwaltet werden.
    
    ![Auswählen der Sicherungsrichtlinie](./media/blob-backup-configure-manage/choose-backup-policy.png)

    Sie können auch eine neue Sicherungsrichtlinie erstellen. Wählen Sie dazu **Neu erstellen** aus, und führen Sie die folgenden Schritte aus:
    
    1. Geben Sie einen Namen für die Richtlinie an, die Sie erstellen möchten.<br></br>Stellen Sie sicher, dass in den anderen Feldern der richtige Datenquellentyp und Tresorname angezeigt werden.
    
    1. Wählen Sie auf der Registerkarte **Sicherungsrichtlinie** das Symbol **Aufbewahrungsregel bearbeiten** für die Aufbewahrungsregel aus, um die Dauer der Datenaufbewahrung zu ändern.<br></br>Sie können eine Aufbewahrungsdauer von bis zu **360** Tagen festlegen. 
    
        >[!NOTE]
        >Sicherungen sind zwar vom Aufbewahrungszeitraum nicht betroffen, der Wiederherstellungsvorgang zum Wiederherstellen älterer Sicherungen kann jedoch länger dauern.

       ![Erstellen einer neuen Sicherungsrichtlinie](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. Wählen Sie **Überprüfen + erstellen** aus, um die Sicherungsrichtlinie zu erstellen.

1. Wählen Sie die erforderlichen Speicherkonten zum Konfigurieren des Schutzes von Blobs aus. Sie können mehrere Speicherkonten auf einmal auswählen und dann auf Auswählen klicken.<br></br>Stellen Sie jedoch sicher, dass dem ausgewählten Tresor die erforderliche Azure RBAC-Rolle (rollenbasierte Zugriffssteuerung) zum Konfigurieren der Sicherung in Speicherkonten zugewiesen wurde. Erfahren Sie mehr über das [Erteilen von Berechtigungen für den Sicherungstresor auf Speicherkonten](#grant-permissions-to-the-backup-vault-on-storage-accounts).<br></br>Wenn die Rolle nicht zugewiesen ist, können Sie sie beim Konfigurieren der Sicherung weiterhin zuweisen. Weitere Informationen finden Sie unter Schritt 7.

    ![Überprüfen von Berechtigungen für den Tresor](./media/blob-backup-configure-manage/verify-vault-permissions.png)

    Azure Backup überprüft, ob der Tresor über ausreichende Berechtigungen verfügt, um die Konfiguration der Sicherung auf den ausgewählten Speicherkonten zu ermöglichen. Es dauert eine Weile, bis die Überprüfungen abgeschlossen sind.
    
    ![Berechtigungen zum Konfigurieren der Sicherung](./media/blob-backup-configure-manage/permissions-for-configuring-backup.png)

1. Nach Abschluss der Überprüfungen informiert die Spalte **Sicherungsbereitschaft** darüber, ob der Sicherungstresor über ausreichende Berechtigungen zum Konfigurieren von Sicherungen für jedes Speicherkonto verfügt.

   ![Informationen zu Backup-Tresorberechtigungen](./media/blob-backup-configure-manage/information-of-backup-vault-permissions.png)

    Wenn bei der Überprüfung Fehler angezeigt werden (für zwei der in der obigen Abbildung aufgeführten Speicherkonten), haben Sie diesen [Speicherkonten](#grant-permissions-to-the-backup-vault-on-storage-accounts) nicht die Rolle **Mitwirkender für Speicherkontosicherung** zugewiesen. Außerdem können Sie hier die erforderliche Rolle basierend auf Ihren aktuellen Berechtigungen zuweisen. Die Fehlermeldung bietet Ihnen Informationen, ob Sie über die erforderlichen Berechtigungen verfügen, damit Sie die entsprechende Maßnahme ergreifen können:

    - **Rollenzuweisung nicht erfolgt:** Dieser Fehler (wie für das Element _blobbackupdemo3_ in der obigen Abbildung dargestellt) gibt an, dass Sie (der Benutzer) über Berechtigungen zum Zuweisen der Rolle **Mitwirkender für Speicherkontosicherung** und der anderen erforderlichen Rollen für das Speicherkonto zum Tresor verfügen. Wählen Sie die Rollen aus, und klicken Sie auf der Symbolleiste auf **Fehlende Rollen zuweisen**. Dadurch wird dem Sicherungstresor automatisch die erforderliche Rolle zugewiesen, und es wird auch eine erneute automatische Überprüfung ausgelöst.<br><br>Manchmal kann die Rollenweitergabe eine Weile dauern (bis zu 10 Minuten), was dazu führt, dass die erneute Überprüfung nicht erfolgreich durchgeführt wird. Warten Sie in einem solchen Szenario einige Minuten, und klicken Sie auf die Schaltfläche „Erneut überprüfen“, um die Überprüfung zu wiederholen.
    
    - **Unzureichende Berechtigungen für die Rollenzuweisung:** Dieser Fehler (wie für das Element _blobbackupdemo4_ in der obigen Abbildung gezeigt) weist darauf hin, dass der Tresor nicht über die erforderliche Rolle zum Konfigurieren der Sicherung verfügt und Sie (der Benutzer) nicht über ausreichende Berechtigungen zum Zuweisen der erforderlichen Rolle verfügen. Um die Rollenzuweisung zu vereinfachen, können Sie mit Backup die Rollenzuweisungsvorlage herunterladen, die Sie für Benutzer mit Berechtigungen zum Zuweisen von Rollen für Speicherkonten freigeben können. Wählen Sie hierzu solche Speicherkonten aus, und klicken Sie auf **Rollenzuweisungsvorlage herunterladen**, um die Vorlage herunterzuladen.<br><br>Nachdem die Rollen zugewiesen wurden, können Sie sie für die entsprechenden Benutzer freigeben. Klicken Sie bei erfolgreicher Zuweisung der Rolle auf **Erneut überprüfen**, um die Berechtigungen erneut zu überprüfen, und konfigurieren Sie dann die Sicherung.
        >[!NOTE]
        >Die Vorlage würde nur Details für ausgewählte Speicherkonten enthalten. Wenn also mehrere Benutzer Rollen für verschiedene Speicherkonten zuweisen müssen, können Sie entsprechend unterschiedliche Vorlagen auswählen und herunterladen.
1. Wenn die Überprüfung für alle ausgewählten Speicherkonten erfolgreich war, fahren Sie mit **Überprüfen und konfigurieren** für die Sicherung fort.<br><br>Sie erhalten Benachrichtigungen zum Status der Schutzkonfiguration und deren Abschluss.

### <a name="using-data-protection-settings-of-the-storage-account"></a>Verwenden von Datenschutzeinstellungen des Speicherkontos

Sie können die Sicherung für Blobs in einem Speicherkonto direkt über die Datenschutzeinstellungen des Speicherkontos konfigurieren. 

1. Wechseln Sie zu dem Speicherkonto, für das Sie die Sicherung für Blobs konfigurieren möchten, und navigieren Sie dann im linken Bereich (unter **Datenverwaltung**) zu **Datenschutz**.

1. In den verfügbaren Datenschutzoptionen können Sie mit der ersten Option die operative Sicherung mithilfe von Azure Backup aktivieren.

    ![Operative Sicherung mit Azure Backup](./media/blob-backup-configure-manage/operational-backup-using-azure-backup.png)

1. Aktivieren Sie das Kontrollkästchen zum **Aktivieren der operativen Sicherung mit Azure Backup**. Wählen Sie dann den Sicherungstresor und die Sicherungsrichtlinie aus, die Sie zuordnen möchten.<br><br>Sie können den vorhandenen Tresor und eine vorhandene Richtlinie auswählen oder bei Bedarf neue erstellen.

    >[!IMPORTANT]
    >Sie sollten dem ausgewählten Tresor die Rolle **Mitwirkender für Speicherkontosicherung** zugewiesen haben. Erfahren Sie mehr über das [Erteilen von Berechtigungen für den Sicherungstresor auf Speicherkonten](#grant-permissions-to-the-backup-vault-on-storage-accounts).
    
    - Wenn Sie die erforderliche Rolle bereits zugewiesen haben, klicken Sie auf **Speichern**, um die Konfiguration der Sicherung abzuschließen. Folgen Sie den Portalbenachrichtigungen, um den Fortschritt der Konfiguration der Sicherung nachzuverfolgen.
    - Wenn Sie sie noch nicht zugewiesen haben, klicken Sie auf **Identität verwalten**, und führen Sie die folgenden Schritte aus, um die Rollen zuzuweisen. 

        ![Aktivieren der operativen Sicherung mit Azure Backup](./media/blob-backup-configure-manage/enable-operational-backup-with-azure-backup.png)


        1. Wenn Sie auf **Identität verwalten** klicken, gelangen Sie zum Blatt „Identität“ des Speicherkontos. 
        
        1. Klicken Sie auf **Rollenzuweisung hinzufügen**, um die Rollenzuweisung zu initiieren.

            ![Hinzufügen einer Rollenzuweisung zum Initiieren der Rollenzuweisung](./media/blob-backup-configure-manage/add-role-assignment-to-initiate-role-assignment.png)


        1. Wählen Sie den Bereich, das Abonnement, die Ressourcengruppe oder das Speicherkonto aus, das Sie der Rolle zuweisen möchten.<br><br>Es wird empfohlen, die Rolle auf Ressourcengruppenebene zuzuweisen, wenn Sie die operative Sicherung für Blobs für mehrere Speicherkonten konfigurieren möchten.

        1. Wählen Sie in der Dropdownliste **Rolle** die Rolle **Mitwirkender für Speicherkontosicherung** aus.

            ![Auswählen der Rolle „Mitwirkender für Speicherkontosicherung“](./media/blob-backup-configure-manage/select-storage-account-backup-contributor-role.png)


        1. Klicken Sie auf **Speichern**, um die Rollenzuweisung abzuschließen.<br><br>Sie werden über das Portal benachrichtigt, sobald dies erfolgreich abgeschlossen wurde. Sie können auch die neue Rolle anzeigen, die der Liste der vorhandenen Rollen für den ausgewählten Tresor hinzugefügt wurde.

            ![Abschließen der Rollenzuweisung](./media/blob-backup-configure-manage/finish-role-assignment.png)

        1. Klicken Sie rechts oben auf das Abbrechensymbol (**x**), um zum Blatt **Datenschutz** des Speicherkontos zurückzukehren.<br><br>Sobald Sie zurück sind, fahren Sie mit der Konfiguration der Sicherung fort.

## <a name="effects-on-backed-up-storage-accounts"></a>Auswirkungen auf gesicherte Speicherkonten

Sobald die Sicherung konfiguriert ist, werden Änderungen an den Blockblobs in den Speicherkonten nachverfolgt, und die Daten werden entsprechend der Sicherungsrichtlinie aufbewahrt. Beachten Sie die folgenden Änderungen in den Speicherkonten, für die die Sicherung konfiguriert ist:

- Die folgenden Funktionen sind für das Speicherkonto aktiviert. Diese können auf der Registerkarte **Datenschutz** des Speicherkontos angezeigt werden.
  - Point-in-Time-Wiederherstellung für Container: Mit der in der Sicherungsrichtlinie festgelegten Aufbewahrungsdauer
  - Vorläufiges Löschen für Blobs: Mit der in der Sicherungsrichtlinie festgelegten Aufbewahrungsdauer +5 Tage
  - Versionsverwaltung für Blobs
  - Blobänderungsfeed

  Wenn für das für die Sicherung konfigurierte Speicherkonto bereits die **Point-in-Time-Wiederherstellung für Container** oder das **Vorläufige Löschen für Blobs** aktiviert war (bevor die Sicherung konfiguriert wurde), stellt Azure Backup eine Aufbewahrung für mindestens die in der Sicherungsrichtlinie definierte Dauer sicher. Daher gilt für jede Eigenschaft Folgendes:

  - Die Aufbewahrungsdauer in der Sicherungsrichtlinie ist länger als die ursprünglich im Speicherkonto angegebene Aufbewahrungsdauer: Die Aufbewahrungsdauer auf dem Speicherkonto wird entsprechend der Sicherungsrichtlinie geändert
  - Die Aufbewahrung in der Sicherungsrichtlinie ist kürzer als die ursprünglich im Speicherkonto angegebene Aufbewahrungsdauer: Die Aufbewahrungsdauer auf dem Speicherkonto wird bei der ursprünglich festgelegten Dauer belassen.

  ![Registerkarte „Datenschutz“](./media/blob-backup-configure-manage/data-protection.png)

- Eine **Löschsperre** wird von Azure Backup auf das geschützte Speicherkonto angewendet. Die Sperre dient zum Schutz vor dem versehentlichen Löschen des Speicherkontos. Dies kann unter **Speicherkonto** > **Sperren** angezeigt werden.

    ![Löschen von Sperren](./media/blob-backup-configure-manage/delete-lock.png)

## <a name="manage-operational-backup"></a>Verwalten der operativen Sicherung

Sie können Backup Center als zentralisierte Benutzeroberfläche für die Verwaltung aller Sicherungen verwenden. Im Zusammenhang mit der operativen Sicherung für Azure-Blobs können Sie mit Backup Center folgende Aktionen ausführen:

- Wie oben beschrieben, können Sie es zum Erstellen von Sicherungstresor und -richtlinien verwenden. Sie können auch alle Tresore und Richtlinien unter den ausgewählten Abonnements anzeigen.
- Backup Center bietet Ihnen eine einfache Möglichkeit zum Überwachen des Schutzzustands geschützter Speicherkonten und von Speicherkonten, für die derzeit keine Sicherung konfiguriert ist.
- Sie können die Sicherung für beliebige Speicherkonten über die Schaltfläche **+ Sicherung** konfigurieren.
- Sie können Wiederherstellungen mithilfe der Schaltfläche **Wiederherstellen** initiieren und über **Sicherungsaufträge** nachverfolgen. Weitere Informationen zum Ausführen von Wiederherstellungen finden Sie unter [Wiederherstellungen von Azure-Blobs](blob-backup-support-matrix.md).
- Analysieren Sie die Sicherungsverwendung mithilfe von Sicherungsberichten.

    ![Backup Center](./media/blob-backup-configure-manage/backup-center.png)

Weitere Informationen finden Sie in der [Übersicht über Backup Center](backup-center-overview.md).

## <a name="stopping-protection"></a>Beenden des Schutzes

Sie können die operative Sicherung für Ihr Speicherkonto gemäß Ihren Anforderungen beenden.

>[!NOTE]
>Durch das Beenden des Schutzes wird die Zuordnung des Speicherkontos zum Sicherungstresor (und den Sicherungstools wie Backup Center) aufgehoben, und die konfigurierte Zeitpunktwiederherstellung für Blobs, die Versionsverwaltung und die Änderungsfeeds werden nicht deaktiviert.

Führen Sie die folgenden Schritte aus, um die Sicherung für ein Speicherkonto zu beenden:

1. Navigieren Sie zur Sicherungsinstanz für das zu sichernde Speicherkonto.<br><br>Zu dieser können Sie aus dem Speicherkonto über **Speicherkonto** -> **Datenschutz** -> **Sicherungseinstellungen verwalten** oder direkt aus dem Backup Center über **Backup Center** -> **Sicherungsinstanzen** -> „Suche nach dem Speicherkontonamen“ navigieren.

    ![Standort des Speicherkontos](./media/blob-backup-configure-manage/storage-account-location.png)

    ![Standort des Speicherkontos über Backup Center](./media/blob-backup-configure-manage/storage-account-location-through-backup-center.png)


1. Klicken Sie in der Sicherungsinstanz auf **Löschen**, um die operative Sicherung für das bestimmte Speicherkonto zu beenden. 
 
    ![Beenden der operativen Sicherung](./media/blob-backup-configure-manage/stop-operational-backup.png)

Nach dem Beenden der Sicherung können Sie andere Speicherdatenschutzfunktionen (die zum Konfigurieren der Sicherung aktiviert sind) auf dem Blatt „Datenschutz“ des Speicherkontos deaktivieren.


## <a name="next-steps"></a>Nächste Schritte

[Wiederherstellen von Azure-Blobs](blob-restore.md)
