---
title: 'Tutorial: Erweitern von Windows-Dateiservern mit der Azure-Dateisynchronisierung | Microsoft-Dokumentation'
description: Es wird der gesamte Prozess beschrieben, mit dem Sie Windows-Dateiserver mit der Azure-Dateisynchronisierung erweitern.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 52a1931ec567d03d0beaaf9180532a91ff6bed07
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259463"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Tutorial: Erweitern von Windows-Dateiservern mit der Azure-Dateisynchronisierung

In diesem Artikel werden die grundlegenden Schritte zum Erweitern der Speicherkapazität einer Windows Server-Instanz mit der Azure-Dateisynchronisierung veranschaulicht. Im Tutorial wird zwar eine Windows Server-Instanz als virtueller Azure-Computer (VM) verwendet, aber normalerweise führen Sie diesen Prozess für Ihre lokalen Server durch. Eine Anleitung zum Bereitstellen der Azure-Dateisynchronisierung in Ihrer eigenen Umgebung finden Sie im Artikel [Bereitstellen der Azure-Dateisynchronisierung](file-sync-deployment-guide.md).

> [!div class="checklist"]
> * Bereitstellen des Speichersynchronisierungsdiensts
> * Vorbereiten von Windows Server für die Verwendung mit der Azure-Dateisynchronisierung
> * Installieren des Azure-Dateisynchronisierungs-Agents
> * Registrieren einer Windows Server-Instanz beim Speichersynchronisierungsdienst
> * Erstellen einer Synchronisierungsgruppe und eines Cloudendpunkts
> * Erstellen eines Serverendpunkts

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

Für dieses Tutorial müssen Sie die folgenden Schritte ausführen, bevor Sie die Azure-Dateisynchronisierung bereitstellen können:

- Erstellen eines Azure-Speicherkontos und einer Dateifreigabe
- Einrichten einer Windows Server 2016 Datacenter-VM
- Vorbereiten der Windows Server-VM für die Azure-Dateisynchronisierung

### <a name="create-a-folder-and-txt-file"></a>Erstellen eines Ordners und einer TXT-Datei

Erstellen Sie auf Ihrem lokalen Computer einen neuen Ordner mit dem Namen _FilesToSync_, und fügen Sie eine Textdatei mit dem Namen _mytestdoc.txt_ hinzu. Sie laden diese Datei später im Tutorial auf eine Dateifreigabe hoch.

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Erstellen einer Dateifreigabe

Nachdem Sie ein Azure-Speicherkonto bereitgestellt haben, erstellen Sie eine Dateifreigabe.

1. Wählen Sie im Azure-Portal die Option **Zu Ressource wechseln**.
1. Wählen Sie im Speicherkontobereich die Option **Dateien**.

    ![Auswählen von Dateien](./media/storage-sync-files-extend-servers/click-files.png)

1. Klicken Sie auf **+ Dateifreigabe**.

    ![Auswählen der Schaltfläche „Dateifreigabe hinzufügen“](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Geben Sie der neuen Dateifreigabe den Namen _afsfileshare_. Geben Sie unter **Kontingent** den Wert 5120 ein, und wählen Sie anschließend **Erstellen** aus. Das Kontingent kann auf maximal 100 TiB festgelegt werden, aber für dieses Tutorial benötigen Sie nur 5 TiB.

    ![Angeben eines Namens und Kontingents für die neue Dateifreigabe](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Wählen Sie die neue Dateifreigabe aus. Wählen Sie für den Dateifreigabeort die Option **Hochladen**.

    ![Hochladen einer Datei](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Navigieren Sie zum Ordner _FilesToSync_, in dem Sie Ihre TXT-Datei erstellt haben, wählen Sie _mytestdoc.txt_ aus, und wählen Sie dann die Option **Hochladen**.

    ![Durchsuchen der Dateifreigabe](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Sie haben jetzt ein Speicherkonto und eine Dateifreigabe mit einer Datei erstellt. Als Nächstes stellen Sie eine Azure-VM mit Windows Server 2016 Datacenter bereit, die in diesem Tutorial als lokaler Server dient.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Bereitstellen eines virtuellen Computers und Anfügen eines Datenträgers

1. Wechseln Sie zum Azure-Portal, und erweitern Sie das Menü auf der linken Seite. Wählen Sie oben links die Option **Ressource erstellen**.
1. Suchen Sie im Suchfeld oberhalb der Liste mit den **Azure Marketplace**-Ressourcen nach **Windows Server 2016 Datacenter**, und wählen Sie den Eintrag in den Ergebnissen aus. Wählen Sie **Erstellen**.
1. Navigieren Sie zur Registerkarte **Grundlagen**. Wählen Sie unter **Projektdetails** die Ressourcengruppe aus, die Sie für dieses Tutorial erstellt haben.

   ![Eingeben grundlegender Informationen zu Ihrem virtuellen Computer auf dem Portalblatt](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. Geben Sie unter **Instanzendetails** einen VM-Namen ein. Verwenden Sie beispielsweise _myVM_.
1. Lassen Sie die Standardeinstellungen für **Region**, **Verfügbarkeitsoptionen**, **Image** und **Größe** unverändert.
1. Geben Sie unter **Administratorkonto** einen **Benutzernamen** und ein **Kennwort** für den virtuellen Computer an.
1. Wählen Sie unter **Regeln für eingehende Ports** die Option **Ausgewählte Ports zulassen**, und wählen Sie im Dropdownmenü dann **RDP (3389)** und **HTTP**.

1. Vor der Erstellung der VM müssen Sie einen Datenträger für Daten erstellen.

   1. Wählen Sie **Weiter: Datenträger**.

      ![Hinzufügen von Datenträgern](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. Übernehmen Sie auf der Registerkarte **Datenträger** unter **Datenträgeroptionen** die Standardwerte.
   1. Wählen Sie unter **DATENTRÄGER** die Option **Create and attach a new disk** (Neuen Datenträger erstellen und anfügen).

   1. Verwenden Sie die Standardeinstellungen mit Ausnahme von **Größe (GiB)** . Den Wert dieser Option können Sie für dieses Tutorial in **1 GiB** ändern.

      ![Datenträgerdetails](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Klicken Sie auf **OK**.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

   Sie können das Symbol **Benachrichtigungen** wählen, um den **Bereitstellungsstatus** zu verfolgen. Das Erstellen eines neuen virtuellen Computers kann einige Minuten dauern.

1. Wählen Sie nach der Bereitstellung des virtuellen Computers die Option **Zu Ressource wechseln**.

   ![Zu Ressource wechseln](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Sie haben jetzt einen neuen virtuellen Computer erstellt und einen Datenträger angefügt. Als Nächstes stellen Sie eine Verbindung mit dem virtuellen Computer her.

### <a name="connect-to-your-vm"></a>Herstellen einer Verbindung mit Ihrer VM

1. Wählen Sie im Azure-Portal auf der Eigenschaftenseite des virtuellen Computers die Option **Verbinden**.

   ![Herstellen einer Verbindung mit einem virtuellen Azure-Computer über das Portal](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Übernehmen Sie auf der Seite zum **Herstellen der Verbindung mit dem virtuellen Computer** die Standardoptionen, um basierend auf der **IP-Adresse** eine Verbindung über den Port 3389 herzustellen. Wählen Sie **RDP-Datei herunterladen** aus.

   ![Herunterladen der RDP-Datei](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Öffnen Sie die heruntergeladene RDP-Datei, und klicken Sie auf **Verbinden**, wenn Sie dazu aufgefordert werden.
1. Wählen Sie im Fenster **Windows-Sicherheit** die Option **Weitere Optionen** und dann **Anderes Konto verwenden** aus. Geben Sie den Benutzernamen im Format *localhost\benutzername* sowie das Kennwort ein, das Sie für die VM erstellt haben, und wählen Sie anschließend **OK**.

   ![Weitere Optionen](./media/storage-sync-files-extend-servers/local-host2.png)

1. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wählen Sie **Ja** oder **Weiter**, um die Verbindung zu erstellen.

### <a name="prepare-the-windows-server"></a>Vorbereiten des Windows-Servers

Deaktivieren Sie für den Windows Server 2016 Datacenter-Server die Option „Verstärkte Sicherheitskonfiguration für Internet Explorer“. Dieser Schritt ist nur für die anfängliche Serverregistrierung erforderlich. Sie können sie nach dem Registrieren des Servers erneut aktivieren.

Auf der VM mit Windows Server 2016 Datacenter wird der Server-Manager automatisch geöffnet.  Sollte der Server-Manager nicht automatisch geöffnet werden, können Sie im Startmenü danach suchen.

1. Wählen Sie im **Server-Manager** die Option **Lokaler Server**.

   ![„Lokaler Server“ auf der linken Seite der Server-Manager-Benutzeroberfläche](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. Wählen Sie im Bereich **Eigenschaften** den Link für **Verstärkte Sicherheitskonfiguration für IE**.  

    ![Bereich „Verstärkte Sicherheitskonfiguration für Internet Explorer“ auf der Server-Manager-Benutzeroberfläche](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Wählen Sie im Dialogfeld **Verstärkte Sicherheitskonfiguration für Internet Explorer** für **Administratoren** und **Benutzer** die Option **Aus**:

    ![Popupfenster „Verstärkte Sicherheitskonfiguration für Internet Explorer“ mit Auswahl von „Aus“](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Sie können den Datenträger jetzt dem virtuellen Computer hinzufügen.

### <a name="add-the-data-disk"></a>Hinzufügen des Datenträgers

1. Wählen Sie auf der VM mit **Windows Server 2016 Datacenter** die Optionen **Dateien und Speicherdienste** > **Volumes** > **Datenträger**.

    ![Datenträger](media/storage-sync-files-extend-servers/your-disk.png)

1. Klicken Sie mit der rechten Maustaste auf den 1-GiB-Datenträger mit dem Namen **Msft Virtual Disk**, und wählen Sie **Neues Volume** aus.
1. Schließen Sie den Assistenten ab. Verwenden Sie die Standardeinstellungen, und notieren Sie sich den zugewiesenen Laufwerkbuchstaben.
1. Klicken Sie auf **Erstellen**.
1. Klicken Sie auf **Schließen**.

   Sie haben den Datenträger jetzt in den Onlinezustand versetzt und ein Volume erstellt. Öffnen Sie den Datei-Explorer auf der Windows Server-VM, um sich zu vergewissern, dass der zuletzt hinzugefügte Datenträger vorhanden ist.

1. Erweitern Sie im Datei-Explorer auf der VM die Option **Dieser PC**, und öffnen Sie das neue Laufwerk. In diesem Beispiel ist dies das Laufwerk „F:“.
1. Klicken Sie mit der rechten Maustaste, und wählen Sie **Neu** > **Ordner**. Geben Sie dem Ordner den Namen _FilesToSync_.
1. Öffnen Sie den Ordner **FilesToSync**.
1. Klicken Sie mit der rechten Maustaste, und wählen sie **Neu** > **Textdokument**. Geben Sie der Textdatei den Namen _MyTestFile_.

    ![Hinzufügen einer neuen Textdatei](media/storage-sync-files-extend-servers/new-file.png)

1. Schließen Sie den **Datei-Explorer** und **Server-Manager**.

### <a name="download-the-azure-powershell-module&quot;></a>Herunterladen des Azure PowerShell-Moduls

Installieren Sie als Nächstes auf dem virtuellen Computer mit Windows Server 2016 Datacenter das Azure PowerShell-Modul auf dem Server.

1. Öffnen Sie auf der VM ein PowerShell-Fenster mit erhöhten Rechten.
1. Führen Sie den folgenden Befehl aus:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Wenn Sie eine ältere NuGet-Version als 2.8.5.201 verwenden, werden Sie aufgefordert, die aktuelle NuGet-Version herunterzuladen und zu installieren.

   Standardmäßig ist der PowerShell-Katalog nicht als vertrauenswürdiges Repository für PowerShellGet konfiguriert. Bei der ersten Verwendung des PowerShell-Katalogs wird die folgende Meldung angezeigt:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is &quot;N"):
   ```

1. Wählen Sie **Ja** oder **Ja zu allen** als Antwort, um die Installation fortzusetzen.

Das Modul `Az` ist ein Rollupmodul für die Azure PowerShell-Cmdlets. Wenn Sie es installieren, werden alle verfügbaren Azure Resource Manager-Module heruntergeladen und die zugehörigen Cmdlets für die Nutzung zur Verfügung gestellt.

Sie haben Ihre Umgebung für das Tutorial jetzt eingerichtet. Alles ist für die Bereitstellung des Speichersynchronisierungsdiensts bereit.

## <a name="deploy-the-service"></a>Bereitstellen des Diensts

Für die Bereitstellung der Azure-Dateisynchronisierung platzieren Sie zuerst eine **Speichersynchronisierungsdienst**-Ressource in einer Ressourcengruppe für Ihr ausgewähltes Abonnement. Der Speichersynchronisierungsdienst erbt Zugriffsberechtigungen aus dem zugehörigen Abonnement und der Ressourcengruppe.

1. Wählen Sie im Azure-Portal die Option **Ressource erstellen**, und suchen Sie dann nach **Azure-Dateisynchronisierung**.
1. Wählen Sie in den Suchergebnissen die Option **Azure-Dateisynchronisierung**.
1. Wählen Sie **Erstellen**, um die Registerkarte **Speichersynchronisierung bereitstellen** zu öffnen.

   ![Speichersynchronisierung bereitstellen](media/storage-sync-files-extend-servers/afs-info.png)

   Geben Sie in dem neuen Bereich, der geöffnet wird, Folgendes ein:

   | Wert | BESCHREIBUNG |
   | ----- | ----- |
   | **Name** | Ein eindeutiger Name (pro Abonnement) für den Speichersynchronisierungsdienst.<br><br>Verwenden Sie für dieses Tutorial _afssyncservice02_. |
   | **Abonnement** | Das Azure-Abonnement, das Sie für dieses Tutorial verwenden. |
   | **Ressourcengruppe** | Die Ressourcengruppe, die den Speichersynchronisierungsdienst enthält.<br><br>Verwenden Sie für dieses Tutorial _afsresgroup101918_. |
   | **Location** | East US |

1. Wenn Sie fertig sind, können Sie **Erstellen** wählen, um den **Speichersynchronisierungsdienst** bereitzustellen.
1. Wählen Sie die Registerkarte **Benachrichtigungen** und dann **Zu Ressource wechseln**.

## <a name="install-the-agent"></a>Installieren des Agents

Der Azure-Dateisynchronisierungs-Agent ist ein herunterladbares Paket, mit dem ein Windows Server-Computer mit einer Azure-Dateifreigabe synchronisiert werden kann.

1. Öffnen Sie auf der VM mit **Windows Server 2016 Datacenter** den **Internet Explorer**.
1. Navigieren Sie zum [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Scrollen Sie nach unten zum Abschnitt **Azure-Dateisynchronisierungs-Agent**, und wählen Sie **Herunterladen**.

   ![Synchronisierungs-Agent – Download](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Aktivieren Sie das Kontrollkästchen für **StorageSyncAgent_V3_WS2016.EXE**, und wählen Sie **Weiter**.

   ![Agent-Auswahl](media/storage-sync-files-extend-servers/select-agent.png)

1. Wählen Sie **Einmal erlauben** > **Ausführen** > **Öffnen**.
1. Schließen Sie das PowerShell-Fenster, wenn Sie dies noch nicht getan haben.
1. Akzeptieren Sie die Standardwerte im **Setup-Assistenten für den Speichersynchronisierungs-Agent**.
1. Wählen Sie **Installieren** aus.
1. Wählen Sie **Fertig stellen** aus.

Sie haben den Azure-Synchronisierungsdienst bereitgestellt und den Agent auf der VM mit Windows Server 2016 Datacenter installiert. Als Nächstes müssen Sie die VM beim Speichersynchronisierungsdienst registrieren.

## <a name="register-windows-server"></a>Registrieren von Windows Server

Durch das Registrieren des Windows-Servers bei einem Speichersynchronisierungsdienst wird eine Vertrauensstellung zwischen dem Server (oder Cluster) und dem Speichersynchronisierungsdienst geschaffen. Ein Server kann jeweils nur für einen Speichersynchronisierungsdienst registriert sein. Er kann mit anderen Servern und Azure-Dateifreigaben synchronisiert werden, die diesem Speichersynchronisierungsdienst zugeordnet sind.

Die Benutzeroberfläche der Serverregistrierung sollte sich automatisch öffnen, nachdem Sie den Azure-Dateisynchronisierungs-Agent installiert haben. Wenn dies nicht der Fall ist, können Sie sie über den entsprechenden Dateispeicherort manuell öffnen: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`.

1. Wählen Sie **OK**, wenn die Benutzeroberfläche für die Serverregistrierung auf dem virtuellen Computer geöffnet wird.
1. Wählen Sie **Anmelden**, um zu beginnen.
1. Melden Sie sich mit Ihren Azure-Kontoanmeldeinformationen an, und wählen Sie **Anmelden**.
1. Geben Sie die folgenden Informationen ein:

   ![Screenshot der Serverregistrierungs-Benutzeroberfläche](media/storage-sync-files-extend-servers/signin.png)

   | Wert | BESCHREIBUNG |
   | ----- | ----- |
   | **Azure-Abonnement** | Das Abonnement, das den Speichersynchronisierungsdienst für dieses Tutorial enthält. |
   | **Ressourcengruppe** | Die Ressourcengruppe, die den Speichersynchronisierungsdienst enthält. Verwenden Sie für dieses Tutorial _afsresgroup101918_. |
   | **Speichersynchronisierungsdienst** | Der Name des Speichersynchronisierungsdiensts. Verwenden Sie für dieses Tutorial _afssyncservice02_. |

1. Wählen Sie **Registrieren**, um die Serverregistrierung durchzuführen.
1. Während des Registrierungsvorgangs werden Sie erneut aufgefordert, sich anzumelden. Melden Sie sich an, und wählen Sie **Weiter**.
1. Klicken Sie auf **OK**.

## <a name="create-a-sync-group"></a>Erstellen einer Synchronisierungsgruppe

Eine Synchronisierungsgruppe definiert die Synchronisierungstopologie für einen Satz von Dateien. Eine Synchronisierungsgruppe muss einen Cloudendpunkt enthalten, der eine Azure-Dateifreigabe darstellt. Außerdem muss eine Synchronisierungsgruppe mindestens einen Serverendpunkt enthalten. Ein Serverendpunkt stellt einen Pfad auf einem registrierten Server dar. Erstellen Sie wie folgt eine Synchronisierungsgruppe:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) im Speichersynchronisierungsdienst die Option **+ Synchronisierungsgruppe**. Verwenden Sie für dieses Tutorial *afssyncservice02*.

   ![Erstellen einer neuen Synchronisierungsgruppe im Azure-Portal](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Geben Sie die folgenden Informationen ein, um eine Synchronisierungsgruppe mit einem Cloudendpunkt zu erstellen:

   | Wert | BESCHREIBUNG |
   | ----- | ----- |
   | **Name der Synchronisierungsgruppe** | Dieser Name muss innerhalb des Speichersynchronisierungsdiensts eindeutig sein, es kann jedoch ein beliebiger Name sein, der für Sie Sinn ergibt. Verwenden Sie *afssyncgroup* für dieses Tutorial.|
   | **Abonnement** | Das Abonnement, unter dem Sie den Speichersynchronisierungsdienst für dieses Tutorial bereitgestellt haben. |
   | **Speicherkonto** | Wählen Sie **Speicherkonto auswählen**. Wählen Sie im angezeigten Bereich das Speicherkonto mit der Azure-Dateifreigabe aus, die Sie erstellt haben. Verwenden Sie *afsstoracct101918* für dieses Tutorial. |
   | **Azure-Dateifreigabe** | Der Name der Azure-Dateifreigabe, die Sie erstellt haben. Verwenden Sie *afsfileshare* für dieses Tutorial. |

1. Klicken Sie auf **Erstellen**.

Wenn Sie Ihre Synchronisierungsgruppe auswählen, sehen Sie, dass Sie jetzt über einen **Cloudendpunkt** verfügen.

## <a name="add-a-server-endpoint"></a>Hinzufügen eines Serverendpunkts

Ein Serverendpunkt stellt einen bestimmten Speicherort auf einem registrierten Server dar. Dies kann beispielsweise ein Ordner auf einem Servervolume sein. Fügen Sie wie folgt einen Serverendpunkt hinzu:

1. Wählen Sie die neu erstellte Synchronisierungsgruppe und dann die Option **Serverendpunkt hinzufügen** aus.

   ![Hinzufügen eines neuen Serverendpunkts im Bereich „Synchronisierungsgruppe“](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. Geben Sie im Bereich **Serverendpunkt hinzufügen** die folgenden Informationen ein, um einen Serverendpunkt zu erstellen:

   | Wert | BESCHREIBUNG |
   | ----- | ----- |
   | **Registrierter Server** | Der Name des Servers, den Sie erstellt haben. Verwenden Sie *afsvm101918* für dieses Tutorial. |
   | **Pfad** | Der Windows Server-Pfad zum Laufwerk, das Sie erstellt haben. Verwenden Sie *f:\filestosync* für dieses Tutorial. |
   | **Cloudtiering** | Lassen Sie diese Option für dieses Tutorial deaktiviert. |
   | **Freier Volumespeicherplatz** | Lassen Sie diese Option für dieses Tutorial leer. |

1. Klicken Sie auf **Erstellen**.

Ihre Dateien sind zwischen der Azure-Dateifreigabe und Windows Server jetzt synchron.

![Azure Storage erfolgreich synchronisiert](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Tutorial erstellten Ressourcen bereinigen möchten, entfernen Sie zuerst die Endpunkte aus dem Speichersynchronisierungsdienst. Heben Sie anschließend die Registrierung des Servers beim Speichersynchronisierungsdienst auf, entfernen Sie die Synchronisierungsgruppen, und löschen Sie den Synchronisierungsdienst.

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden die grundlegenden Schritte zum Erweitern der Speicherkapazität eines Windows-Servers mit der Azure-Dateisynchronisierung beschrieben. Ausführlichere Informationen zur Planung für eine Bereitstellung der Azure-Dateisynchronisierung finden Sie unter:

> [!div class="nextstepaction"]
> [Planung für die Bereitstellung der Azure-Dateisynchronisierung](file-sync-planning.md)
