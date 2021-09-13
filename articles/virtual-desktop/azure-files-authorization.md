---
title: Autorisieren eines Azure Virtual Desktop-Hostpools für Azure Files – Azure
description: Autorisieren eines Azure Virtual Desktop-Hostpools zur Verwendung von Azure Files
author: Heidilohr
ms.topic: how-to
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 2593552fbad04ada5e903ba1eb6a2613e3ee55a0
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446857"
---
# <a name="authorize-an-account-for-azure-files"></a>Autorisieren eines Kontos für Azure Files

In diesem Artikel wird gezeigt, wie Sie einen Azure Virtual Desktop-Hostpool autorisieren, Azure Files zu verwenden.

## <a name="requirements"></a>Requirements (Anforderungen)

Bevor Sie beginnen, benötigen Sie Folgendes:

- Ein mit Azure Active Directory (Azure AD) synchronisiertes Active Directory Domain Services-Konto (AD DS)
- Berechtigungen zum Erstellen einer Gruppe in AD DS
- Ein Speicherkonto und die erforderlichen Berechtigungen zum Erstellen eines neuen Speicherkontos, falls erforderlich
- Einen virtuellen Computer (VM) oder einen physischen Computer, der AD DS beigetreten ist, für das Sie über die Berechtigung für den Zugriff verfügen
- Einen Azure Virtual Desktop-Hostpool, in dem alle Sitzungshosts einer Domäne beigetreten sind

## <a name="create-a-security-group-in-active-directory-domain-services"></a>Erstellen einer Sicherheitsgruppe in Active Directory Domain Services

Zunächst müssen Sie eine Sicherheitsgruppe in AD DS erstellen. Diese Sicherheitsgruppe wird in späteren Schritten verwendet, um Berechtigungen auf Freigabeebene und für die NTFS-Dateifreigabe (New Technology File System) zu erteilen.

>[!NOTE]
>Wenn Sie eine vorhandene Sicherheitsgruppe verwenden möchten, wählen Sie den Namen dieser Gruppe aus, anstatt eine neue Gruppe zu erstellen.

So erstellen Sie eine Sicherheitsgruppe

1. Öffnen Sie eine Remotesitzung mit der VM oder dem physischen Computer, die AD DS beigetreten sind, das Sie der Sicherheitsgruppe hinzufügen möchten.

2. Öffnen Sie **Active Directory-Benutzer und -Computer**.

3. Klicken Sie unter dem Domänenknoten mit der rechten Maustaste auf den Namen Ihres Computers. Wählen Sie im Dropdownmenü **Neu** > **Gruppe** aus.

4. Geben Sie im Fenster **Neues Objekt – Gruppe** den Namen der neuen Gruppe ein, und wählen Sie dann die folgenden Werte aus:

    - Wählen Sie für **Gruppenbereich** die Option **Global** aus.
    - Wählen Sie für **Gruppentyp** die Option **Sicherheit** aus.

5. Klicken Sie mit der rechten Maustaste auf die neue Gruppe, und wählen Sie **Eigenschaften** aus.

6. Wählen Sie im Fenster **Eigenschaften** die Registerkarte **Mitglieder** aus.

7. Wählen Sie **Hinzufügen...** aus.

8. Wählen Sie im Fenster **Benutzer, Kontakte, Computer, Dienstkonten oder Gruppen auswählen** die Option **Objekttypen…** aus. > **Computer**. Klicken Sie auf **OK**, wenn Sie fertig sind.

9. Geben Sie im Fenster **Geben Sie die Namen der auszuwählenden Objekte ein** die Namen aller Sitzungshosts ein, die Sie in die Sicherheitsgruppe einbeziehen möchten.

10. Wählen Sie **Namen überprüfen** und dann den Namen des zu verwendenden Sitzungshosts aus der angezeigten Liste aus.

11. Wählen Sie **OK** und anschließend **Übernehmen** aus.

>[!NOTE]
>Es kann bis zu einer Stunde dauern, bis neue Sicherheitsgruppen mit Azure AD synchronisiert sind.

## <a name="create-a-storage-account"></a>Erstellen eines Speicherkontos

Wenn Sie noch kein Speicherkonto erstellt haben, folgen Sie zunächst den Anweisungen unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md). Wenn Sie ein neues Speicherkonto erstellen, stellen Sie sicher, dass Sie auch eine neue Dateifreigabe erstellen.

>[!NOTE]
>Wenn Sie ein Speicherkonto vom Typ **Premium** erstellen, stellen Sie sicher, dass **Kontoart** auf **FileStorage** festgelegt ist.

## <a name="get-rbac-permissions"></a>Abrufen von RBAC-Berechtigungen

So rufen Sie RBAC-Berechtigungen ab

1. Wählen Sie das Speicherkonto aus, das Sie verwenden möchten.

2. Wählen Sie **Zugriffssteuerung (IAM)** und anschließend **Hinzufügen** aus. Als nächstes wählen Sie aus dem Dropdownmenü die Option **Rollenzuweisungen hinzufügen** aus.

3. Wählen Sie auf dem Bildschirm **Rollenzuweisung hinzufügen** die folgenden Werte aus:

    - Wählen Sie für **Rolle** die Option **Speicherdateidaten-SMB-Freigabemitwirkender** aus.
    - Wählen Sie für **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.
    - Wählen Sie für **Abonnement** die Option **Auf Basis Ihrer Umgebung** aus.
    - Wählen Sie für **Auswählen** den Namen der Active Directory-Gruppe aus, die Ihre Sitzungshosts enthält.

4. Wählen Sie **Speichern** aus.

## <a name="join-your-storage-account-to-ad-ds"></a>Beitritt Ihres Speicherkontos zu AD DS

Als nächstes muss das Speicherkonto AD DS beitreten. So verknüpfen Sie Ihr Konto mit AD DS

1. Öffnen Sie eine Remotesitzung auf einem virtuellen oder physischen Computer, der AD DS beigetreten ist.

      >[!NOTE]
      > Führen Sie das Skript mit lokalen AD DS-Anmeldeinformationen aus, die mit Ihrer Azure AD-Umgebung synchronisiert sind. Die lokalen AD DS-Anmeldeinformationen müssen über die Berechtigungen der Azure-Rolle „Speicherkontobesitzer“ oder „Mitwirkender“ verfügen.

2. Laden Sie [die neueste Version von AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases) herunter und entpacken Sie sie.

3. Öffnen Sie **PowerShell** im Modus mit erhöhten Rechten.

4. Führen Sie das folgende Cmdlet aus, um die Ausführungsrichtlinie festzulegen:
    
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

5. Wechseln Sie dann in den Ordner, in den Sie AzfileHybrid entpackt haben, und führen Sie den folgenden Befehl aus:

    ```powershell
    .\\CopyToPSPath.ps1
    ```

6. Importieren Sie anschließend das AzFilesHybrid-Modul, indem Sie das folgende Cmdlet ausführen:
   
   ```powershell
   Import-Module -Name AzFilesHybrid
   ```

7. Führen Sie dann das folgende Cmdlet aus, um eine Verbindung mit Azure AD herzustellen:
   
   ```powershell
   Connect-AzAccount
   ```

8. Legen Sie die folgenden Parameter fest, und ersetzen Sie dabei die Platzhalter durch die für Ihr Szenario relevanten Werte:

    ```powershell
    $SubscriptionId = "<your-subscription-id-here>"

    $ResourceGroupName = "<resource-group-name-here>"

    $StorageAccountName = "<storage-account-name-here>"
    ```

9.  Führen Sie abschließend den folgenden Befehl aus:

    ```powershell
    Join-AzStorageAccountForAuth `

    -ResourceGroupName $ResourceGroupName `

    -StorageAccountName $StorageAccountName `

    -DomainAccountType "ComputerAccount" `

    -OrganizationalUnitDistinguishedName "<ou-here>" `

    -EncryptionType "'RC4','AES256'"
    ```

## <a name="get-ntfs-level-permissions"></a>Abrufen von Berechtigungen auf NTFS-Ebene

Damit Sie sich mit AD DS-Computerkonten anhand eines Azure Files-Speicherkontos authentifizieren können, müssen wir zusätzlich zu der zuvor eingerichteten RBAC-Berechtigung auch Berechtigungen auf NTFS-Ebene zuweisen.

So weisen Sie NTFS-Berechtigungen zu

1. Öffnen Sie das Azure-Portal, und navigieren Sie zu dem Speicherkonto, das wir zu AD DS hinzugefügt haben.

2. Wählen Sie **Zugriffsschlüssel** aus, und kopieren Sie den Wert in das Feld **Schlüssel1**.

3. Starten Sie eine Remotesitzung auf dem virtuellen oder physischen Computer, der AD DS beigetreten ist.

4. Öffnen Sie eine Eingabeaufforderung im Modus mit erhöhten Rechten.

5. Führen Sie den folgenden Befehl aus, und ersetzen Sie dabei die Platzhalter durch die für Ihre Bereitstellung relevanten Werte:

    ```cmd
    net use <desired-drive-letter>:
    \\<storage-account-name>.file.core.windows.net\<share-name>
    /user:Azure\<storage-account-name> <storage-account-key>
    ```

    >[!NOTE]
    >Wenn Sie diesen Befehl ausführen, sollte die Ausgabe wie folgt lauten: „Der Befehl wurde erfolgreich ausgführt“. Wenn dies nicht der Fall ist, überprüfen Sie Ihre Eingaben und versuchen Sie es erneut.

6. Öffnen Sie **Datei-Explorer**, und suchen Sie den Laufwerksbuchstaben, den Sie im Befehl in Schritt 5 verwendet haben.

7. Klicken Sie mit der rechten Maustaste auf den Laufwerksbuchstaben, und wählen Sie dann im Dropdownmenü die Option **Eigenschaften** > **Sicherheit** aus.

8. Wählen Sie **Bearbeiten** und dann **Hinzufügen…** aus.

    >[!NOTE]
    >Stellen Sie sicher, dass der Domänenname Ihrem AD DS-Domänennamen entspricht. Wenn dies nicht der Fall ist, bedeutet dies, dass das Speicherkonto nicht der Domäne beigetreten ist. Sie müssen ein der Domäne beigetretenes Konto verwenden, um fortfahren zu können.

9. Geben Sie bei der entsprechenden Aufforderung Ihre Administratoranmeldeinformationen ein.

10. Geben Sie im Fenster **Benutzer, Computer, Dienstkonten oder Gruppen auswählen** den Namen der Gruppe aus [Erstellen einer Sicherheitsgruppe in Active Directory Domain Services](#create-a-security-group-in-active-directory-domain-services) ein.

11. Klicken Sie auf **OK**. Bestätigen Sie anschließend, dass die Gruppe über die Berechtigung zum **Lesen und Ausführen** verfügt. Wenn die Gruppe über Berechtigungen verfügt, sollte das Kontrollkästchen „Zulassen“ aktiviert sein, wie in der folgenden Abbildung gezeigt:

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Fenster „Sicherheit“ Unter der Liste „Berechtigungen“ ist die Berechtigung „Lesen und Ausführen“ mit einem grünen Häkchen in der Spalte „Zulassen“ gekennzeichnet.](media/read-and-execute.png)

12. Fügen Sie die Active Directory-Gruppe mit den Computerkonten mit Berechtigungen zum **Lesen und Ausführen** zur Sicherheitsgruppe hinzu.

13. Wählen Sie **Übernehmen**. Wenn eine Windows-Sicherheitsabfrage angezeigt wird, wählen Sie **Ja** aus, um Ihre Änderungen zu bestätigen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie nach der Einrichtung auf Probleme stoßen, lesen Sie unseren [Artikel zur Problembehandlung bei Azure Files](troubleshoot-authorization.md).