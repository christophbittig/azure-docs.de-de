---
title: 'Automatisches Migrieren von Azure Virtual Desktop (klassisch) (Vorschau): Azure'
description: Hier erfahren Sie, wie Sie mithilfe des Migrationsmoduls automatisch von Azure Virtual Desktop (klassisch) zu Azure Virtual Desktop migrieren.
author: Heidilohr
ms.topic: how-to
ms.date: 09/15/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 6d5fed6547a32382413c1a128b1a9003bf099c88
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471761"
---
# <a name="migrate-automatically-from-azure-virtual-desktop-classic-preview"></a>Automatisches Migrieren von Azure Virtual Desktop (klassisch) (Vorschau)

> [!IMPORTANT]
> Das Migrationsmodultool für Azure Virtual Desktop befindet sich derzeit in der Phase „Public Preview“.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Mit dem Migrationsmodultool (Vorschau) können Sie Ihre Organisation automatisch von Azure Virtual Desktop (klassisch) zu Azure Virtual Desktop migrieren. In diesem Artikel erfahren Sie, wie Sie das Tool verwenden können. 

## <a name="requirements"></a>Anforderungen

Bevor Sie das Migrationsmodul verwenden können, müssen die folgenden Voraussetzungen erfüllt sein:

- Ein Azure-Abonnement, in dem Sie neue Azure-Dienstobjekte erstellen.

- Sie müssen der Rolle „Mitwirkender“, mit der Sie Azure-Objekte in Ihrem Abonnement erstellen, und der Rolle „Benutzerzugriffsadministrator“ zugewiesen sein, mit der Sie Benutzer Anwendungsgruppen zuweisen.

- Sie benötigen mindestens die Berechtigung „RDS-Mitwirkender“ (Remotedesktopdienste) für einen RDS-Mandanten oder die spezifischen Hostpools, die Sie migrieren.

- Die neueste Version des PowerShell-Moduls Microsoft.RdInfra.RDPowershell. 

- Die neueste Version des PowerShell-Moduls Az.DesktopVirtualization. 

- Die neueste Version des PowerShell-Moduls Az.Resources. 

- Installation des Migrationsmoduls auf Ihrem Computer.

- PowerShell oder PowerShell ISE zum Ausführen der in diesem Artikel vorgestellten Skripts. Das PowerShell-Modul Microsoft.RdInfra.RD funktioniert nicht in PowerShell Core.

>[!IMPORTANT]
>Bei der Migration werden derzeit nur Dienstobjekte in der geografischen Region „USA“ erstellt. Wenn Sie versuchen, Ihre Dienstobjekte in eine andere Geografie zu migrieren, funktioniert dies nicht. Wenn es in Ihrer Bereitstellung von Azure Virtual Desktop (klassisch) mehr als 200 App-Gruppen gibt, ist ebenfalls keine Migration möglich. Eine Migration ist nur möglich, wenn Sie Ihre Umgebung so neu erstellen, dass die Anzahl von App-Gruppen in Ihrem Azure Active Directory-Mandanten (Azure AD) verringert wird.

## <a name="prepare-your-powershell-environment"></a>Vorbereitung der PowerShell-Umgebung

Zunächst müssen Sie Ihre PowerShell-Umgebung auf den Migrationsprozess vorbereiten.

So bereiten Sie Ihre PowerShell-Umgebung vor

1. Stellen Sie zunächst sicher, dass Sie über die neueste Version der Module Az.DesktopVirtualization und Az.Resources verfügen, indem Sie die folgenden Cmdlets ausführen:

    ```powershell
    Get-Module Az.Resources
    Get-Module Az.DesktopVirtualization
    https://www.powershellgallery.com/packages/Az.DesktopVirtualization/
    https://www.powershellgallery.com/packages/Az.Resources/
    ```

    Falls nicht, installieren und importieren Sie die Module, indem Sie die folgenden Cmdlets ausführen:

    ```powershell
    Install-module Az.Resources
    Import-module Az.Resources
    Install-module Az.DesktopVirtualization
    Import-module Az.DesktopVirtualization
    ```

2. Deinstallieren Sie als Nächstes das aktuelle PowerShell-Modul RDInfra, indem Sie dieses Cmdlet ausführen:

    ```powershell
    Uninstall-Module -Name Microsoft.RDInfra.RDPowershell -AllVersions
    ```

3. Installieren Sie anschließend das Modul RDPowershell mit diesem Cmdlet:

    ```powershell
    Install-Module -Name Microsoft.RDInfra.RDPowershell -RequiredVersion 1.0.3414.0 -force
    Import-module Microsoft.RDInfra.RDPowershell
    ```

4. Nachdem Sie alle Komponenten installiert haben, führen Sie dieses Cmdlet aus, um sicherzustellen, dass Sie über die richtigen Versionen der Module verfügen:

    ```powershell
    Get-Module Microsoft.RDInfra.RDPowershell
    ```

5. Nun installieren und importieren Sie das Migrationsmodul, indem Sie die folgenden Cmdlets ausführen:

    ```powershell
    Install-Module -Name PackageManagement -Repository PSGallery -Force
    Install-Module -Name PowerShellGet -Repository PSGallery -Force
    # Then restart shell
    Install-Module -Name Microsoft.RdInfra.RDPowershell.Migration -RequiredVersion 1.0.3725-Prerelease -AllowPrerelease -AllowClobber
    Import-Module <Full path to the location of the migration module>\Microsoft.RdInfra.RDPowershell.Migration.psd1
    ```

6. Wenn Sie fertig sind, melden Sie sich in Ihrem PowerShell-Fenster bei Azure Virtual Desktop (klassisch) an:

    ```powershell
    Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
    ```

7. Melden Sie sich bei Azure Resource Manager an:

    ```powershell
    Login-AzAccount
    ```

8. Wenn Sie mehrere Abonnements haben, wählen Sie mit diesem Cmdlet das Abonnement aus, zu dem Sie Ihre Ressourcen migrieren möchten:

    ```powershell
    Select-AzSubscription -Subscriptionid <subID>
    ```

9. Registrieren Sie im Azure-Portal den Ressourcenanbieter für das ausgewählte Abonnement.

10. Schließlich müssen Sie den Anbieter registrieren. Dazu stehen zwei Möglichkeiten zur Verfügung:
    - Wenn Sie PowerShell verwenden möchten, führen Sie dieses Cmdlet aus:
       
       ```powershell
       Register-AzResourceProvider -ProviderNamespace Microsoft.DesktopVirtualization
       ```
    
    - Wenn das Azure-Portal bevorzugen, öffnen Sie es, und melden Sie sich an. Wechseln Sie dann zu **Abonnements**, und wählen Sie den Namen des gewünschten Abonnements aus. Wechseln Sie anschließend zu **Ressourcenanbieter** > **Microsoft.DesktopVirtualization**, und wählen Sie **Erneut registrieren** aus. Auf der Benutzeroberfläche sind noch keine Änderungen zu sehen, aber Ihre PowerShell-Umgebung sollte nun für die Ausführung des Moduls bereit sein.

## <a name="migrate-azure-virtual-desktop-classic-resources-to-azure-resource-manager"></a>Migrieren von Azure Virtual Desktop-Ressourcen (klassisch) zu Azure Resource Manager

Da Ihre PowerShell-Umgebung nun bereit ist, können Sie den Migrationsprozess einleiten.

So migrieren Sie Azure Virtual Desktop-Ressourcen (klassisch) zu Azure Resource Manager

1. Wenn Sie vor der Migration wissen möchten, wie die vorhandenen klassischen Ressourcen neuen Azure Resource Manager-Ressourcen zugeordnet werden, führen Sie dieses Cmdlet aus:
    
    ```powershell
    Get-RdsHostPoolMigrationMapping
    ```

    Mit **Get-RdsHostPoolMigrationMapping** können Sie eine CSV-Datei mit einer Zuordnung der Ressourcen erstellen. Wenn der Name Ihres Mandanten beispielsweise „Contoso“ lautet und Sie Ihre Zuordnungsdatei in der Datei „contosouser“ speichern möchten, führen Sie ein Cmdlet wie das folgende aus:

    ```powershell
    Get-RdsHostPoolMigrationMapping -Tenant Contoso -HostPool Office -Location EastUS -OutputFile 'C:\\Users\contosouser\OneDrive - Microsoft\Desktop\mapping.csv'
    ```

2. Führen Sie als Nächstes das Cmdlet **Start-RdsHostPoolMigration** aus, um zu entscheiden, ob ein einzelner Hostpool oder alle Hostpools innerhalb eines Mandanten migriert werden sollen.

   Beispiel:

   ```powershell
   Start-RdsHostPoolMigration -Tenant Contoso -Location WestUS
   ```

   Wenn Sie Ihre Ressourcen in einen bestimmten Hostpool migrieren möchten, fügen Sie den Hostpoolnamen ein. Wenn Sie beispielsweise den Hostpool mit dem Namen „Office“ verschieben möchten, führen Sie einen Befehl wie den folgenden aus:

   ```powershell
   Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments $false -Location EastUS
   ```

   Wenn Sie keinen Arbeitsbereichsnamen angeben, erstellt das Modul basierend auf dem Mandantennamen automatisch einen für Sie. Wenn Sie jedoch einen bestimmten Arbeitsbereich bevorzugen, können Sie seine Ressourcen-ID wie folgt eingeben:

   ```powershell
   Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments -Location EastUS -Workspace <Resource ID of workspacename>
   ```
    
   Wenn Sie einen bestimmten Arbeitsbereich wünschen, aber dessen Ressourcen-ID nicht kennen, führen Sie dieses Cmdlet aus:

   ```powershell
   Get-AzWvdWorkspace -WorkspaceName <workspace> -ResourceGroupName <resource group> |fl
   ```

   Sie müssen auch für die vorhandenen Benutzerzuweisungen einen Benutzerzuweisungsmodus angeben:

      - Wählen Sie **Kopieren**, um alle Benutzerzuweisungen aus Ihren bisherigen App-Gruppen in Azure Resource Manager-App-Gruppen zu kopieren. Benutzer sehen Feeds für beide Versionen ihrer Clients.
      - Wählen Sie **Keine**, wenn Sie die Benutzerzuweisungen nicht ändern möchten. Später können Sie Benutzer oder Benutzergruppen über das Azure-Portal, PowerShell oder die API App-Gruppen zuweisen. Benutzer können Feeds nur unter Verwendung der (klassischen) Azure Virtual Desktop-Clients sehen.

   Sie können pro Abonnement nur 2.000 Benutzerzuweisungen kopieren, sodass Ihr Grenzwert abhängt, wie viele Zuweisungen bereits in Ihrem Abonnement vorhanden sind. Das Modul berechnet den Grenzwert anhand der Anzahl der bereits erfolgten Zuweisungen. Wenn Sie nicht genügend Zuweisungen zum Kopieren haben, erhalten Sie die Fehlermeldung „Unzureichendes Rollenzuweisungskontingent zum Kopieren von Benutzerzuweisungen. Führen Sie zum Migrieren den Befehl ohne den Schalter -CopyUserAssignments erneut aus.“

3. Nachdem Sie die Befehle ausgeführt haben, dauert es bis zu 15 Minuten, bis das Modul die Dienstobjekte erstellt hat. Wenn Sie Benutzerzuweisungen kopiert oder verschoben haben sollten, verlängert sich die Zeit, die das Modul für die Einrichtung benötigt, entsprechend.

   Nachdem das Cmdlet **Start-RdsHostPoolMigration** ausgeführt wurde, sollten Sie Folgendes sehen:

      - Azure-Dienstobjekte für den von Ihnen angegebenen Mandanten oder Hostpool

      - Zwei neue Ressourcengruppen:

         - Eine Ressourcengruppe namens „Tenantname“, die Ihren Arbeitsbereich enthält.

         - Eine Ressourcengruppe namens Tenantname_originalHostPoolName, die den Hostpool und Desktop-App-Gruppen enthält.

      - Alle Benutzer, die Sie in den neu erstellten App-Gruppen veröffentlicht haben.

      - Virtuelle Computer sind sowohl in vorhandenen als auch in neuen Hostpools verfügbar, um während des Migrationsprozesses Ausfallzeiten für Benutzer zu vermeiden. Dadurch können Benutzer eine Verbindung mit derselben Benutzersitzung herstellen.

   Da es sich bei diesen neuen Azure-Dienstobjekten um Azure Resource Manager-Objekte handelt, kann das Modul keine RBAC-Berechtigungen (Role-Based Access Control, rollenbasierte Zugriffssteuerung) oder Diagnoseeinstellungen für sie festlegen. Daher müssen Sie die RBAC-Berechtigungen und -Einstellungen für diese Objekte manuell aktualisieren.

   Nachdem das Modul die anfänglichen Benutzerverbindungen überprüft hat, können Sie die App-Gruppe nach Wunsch auch für weitere Benutzer oder Benutzergruppen veröffentlichen.

   >[!NOTE]
   >Wenn Sie App-Gruppen nach der Migration in eine andere Ressourcengruppe verschieben, nachdem Sie Benutzern Berechtigungen zugewiesen haben, werden alle RBAC-Rollen entfernt. Sie müssen Benutzern erneut RBAC-Berechtigungen zuweisen.

4. Wenn Sie alle (klassischen) Azure Virtual Desktop-Dienstobjekte löschen möchten, führen Sie **Complete-RdsHostPoolMigration** aus, um den Migrationsprozess fertig zu stellen. Mit diesem Cmdlet werden alle (klassischen) Azure Virtual Desktop-Objekte gelöscht, sodass nur die neuen Azure-Objekte übrig bleiben. Die Benutzer können den Feed für die neu erstellten App-Gruppen nur auf ihren Clients sehen. Nachdem dieser Befehl ausgeführt wurde, können Sie den (klassischen) Azure Virtual Desktop-Mandanten sicher löschen, um den Vorgang abzuschließen.

   Beispiel:

   ```powershell
   Complete-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   Wenn Sie einen bestimmten Hostpool vervollständigen möchten, können Sie den Hostpoolnamen in das Cmdlet einbeziehen. Wenn Sie beispielsweise einen Hostpool mit dem Namen „Office“ vervollständigen möchten, wählen Sie einen Befehl wie den folgenden:

    ```powershell
    Complete-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
    ```

    Dadurch werden alle Dienstobjekte gelöscht, die von Azure Virtual Desktop (klassisch) erstellt wurden. Es bleiben nur die neuen Azure-Objekte übrig. Benutzer können auf ihren Clients nur den Feed für die neu erstellten App-Gruppen sehen. Nach Abschluss der Migration müssen Sie den Mandanten in Azure Virtual Desktop (klassisch) explizit löschen.

5. Wenn Sie Ihre Meinung zur Migration geändert haben und den Vorgang rückgängig machen möchten, führen Sie das Cmdlet **Revert-RdsHostPoolMigration** aus.
    
   Beispiel:

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   Wenn Sie einen bestimmten Hostpool wiederherstellen möchten, können Sie den Hostpoolnamen in den Befehl einbeziehen. Wenn Sie z. B. einen Hostpool namens „Office“ wiederherstellen möchten, geben Sie Folgendes ein:

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
   ```

   Dieses Cmdlet löscht alle neu erstellten Azure-Dienstobjekte. Ihren Benutzern wird auf ihren Clients nur der Feed für (klassische) Azure Virtual Desktop-Objekte angezeigt.

   Das Cmdlet löscht jedoch nicht den Arbeitsbereich, den das Modul erstellt hat, oder die zugehörige Ressourcengruppe. Sie müssen diese Elemente manuell löschen.

6. Wenn Sie Ihre (klassischen) Azure Virtual Desktop-Dienstobjekte noch nicht löschen, jedoch die Migration testen möchten, können Sie **Set-RdsHostPoolHidden** ausführen.

    Beispiel:

    ```powershell
    Set-RdsHostPoolHidden -Tenant Contoso -Hostpool Office -Hidden $true -Location WestUS
    ```

    Wenn Sie den Status auf TRUE festlegen, werden die (klassischen) Azure Virtual Desktop-Ressourcen ausblendet. Bei Festlegung auf FALSE werden die Ressourcen Ihren Benutzern angezeigt.

    Der Parameter *-Hostpool* ist optional. Mit diesem Parameter können Sie einen bestimmten (klassischen) Azure Virtual Desktop-Hostpool ausblenden.

    Dieses Cmdlet blendet den Benutzerfeed und die Dienstobjekte von Azure Virtual Desktop (klassisch) aus, statt sie zu löschen. Dies geschieht jedoch in der Regel nur zu Testzwecken und gilt nicht als abgeschlossene Migration. Zum Abschließen der Migration müssen Sie den Befehl **Complete-RdsHostPoolMigration** ausführen. Andernfalls können Sie ihre Bereitstellung wiederherstellen, indem Sie **Revert-RdsHostPoolMigration** ausführen.

## <a name="troubleshoot-automatic-migration"></a>Problembehandlung bei der automatischen Migration

In diesem Abschnitt wird erläutert, wie Sie beim Migrationsmodul häufig aufgetretene Probleme beheben.

### <a name="i-cant-access-the-tenant"></a>Kein Zugriff auf den Mandanten

Probieren Sie zunächst die folgenden beiden Schritte:

- Stellen Sie sicher, dass Ihr Administratorkonto die erforderlichen Berechtigungen für den Zugriff auf den Mandanten hat.
- Führen Sie auf dem Mandanten **Get-RdsTenant** aus.

Wenn diese beiden Schritte möglich sind, führen Sie das Cmdlet **Set-RdsMigrationContext** aus, um den RDS- und ADAL-Kontext für Ihre Migration zu festlegen:

1. Erstellen Sie den RDS-Kontext, indem Sie das Cmdlet **Add-RdsAccount** ausführen.       

2. Sie finden den RDS-Kontext in der globalen Variablen *$rdMgmtContext*.         

3. Sie finden den ADAL-Kontext in der globalen Variablen *$AdalContext*.

4. Führen Sie **Set-RdsMigrationContext** mit den Variablen aus, die Sie in diesem Format gefunden haben:

   ```powershell
   Set-RdsMigrationContext -RdsContext <rdscontext> -AdalContext <adalcontext>
   ```

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie erfahren möchten, wie Sie Ihre Bereitstellung stattdessen manuell migrieren, lesen Sie [Manuelles Migrieren von Azure Virtual Desktop (klassisch)](manual-migration.md).

Nach der Migration können Sie sich in [unseren Tutorials](create-host-pools-azure-marketplace.md) über die Funktionsweise von Azure Virtual Desktop informieren. Informationen zu erweiterten Verwaltungsfunktionen finden Sie unter [Erweitern eines vorhandenen Hostpools](expand-existing-host-pool.md) und [Anpassen der RDP-Eigenschaften](customize-rdp-properties.md).

Informationen zu Dienstobjekten finden Sie unter [Azure Virtual Desktop-Umgebung](environment-setup.md).
