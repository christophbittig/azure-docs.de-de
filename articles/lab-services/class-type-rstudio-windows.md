---
title: Einrichten eines Labs mit R und RStudio unter Windows mit Azure Lab Services
description: Erfahren Sie, wie Sie Labs einrichten, um R mithilfe von RStudio unter Windows zu unterrichten.
author: emaher
ms.topic: article
ms.date: 08/26/2021
ms.author: enewman
ms.openlocfilehash: 6a50b1f0cb53fa064a426b2fe3d28ae7fb7be2aa
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115427"
---
# <a name="set-up-a-lab-to-teach-r-on-windows"></a>Einrichten eines Labs, um R unter Windows zu unterrichten

[R](https://www.r-project.org/about.html) ist eine Open-Source-Sprache, die für statistische Berechnungen und Grafiken verwendet wird.  Sie wird in der statistischen Analyse von Genetik bis hin zur Verarbeitung natürlicher Sprache und Analyse von Finanzdaten verwendet.  R bietet als Benutzeroberfläche eine [interaktive Befehlszeile](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line).  [RStudio](https://www.rstudio.com/products/rstudio/) ist eine interaktive Entwicklungsumgebung (IDE), die für die Sprache R zur Verfügung steht.  Die kostenlose Version bietet Codebearbeitungstools, eine integrierte Debugfunktionalität und Paketentwicklungstools.

Dieser Artikel befasst sich ausschließlich mit RStudio und R als Baustein für einen Kurs, in dem statistisches Computing verwendet werden soll.  Für die Kurstypen [Deep Learning](class-type-deep-learning-natural-language-processing.md) sowie [Python und Jupyter Notebooks](class-type-jupyter-notebook.md) wird RStudio unterschiedlich eingerichtet.  In jedem Artikel wird beschrieben, wie Sie das Marketplace-Image [Data Science Virtual Machine für Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-US/marketplace/apps/microsoft-dsvm.ubuntu-1804) verwenden. Darin sind viele [Data Science-bezogene Tools](/azure/machine-learning/data-science-virtual-machine/tools-included) vorinstalliert, einschließlich RStudio.  

## <a name="lab-account-configuration"></a>Konfiguration eines Lab-Kontos

Zum Einrichten dieses Labs benötigen Sie zunächst ein Azure-Abonnement und ein Lab-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie ein neues Lab-Konto in Azure Lab Services erstellen. Weitere Informationen zum Erstellen eines neuen Lab-Kontos finden Sie im Tutorial zum [Einrichten eines Lab-Kontos](./tutorial-setup-lab-account.md). Sie können auch ein vorhandenes Lab-Konto verwenden.

### <a name="lab-account-settings"></a>Lab-Kontoeinstellungen

Aktivieren Sie die Einstellungen Ihres Lab-Kontos wie in der folgenden Tabelle beschrieben.

| Lab-Kontoeinstellungen | Instructions |
| -------------------- | ----- |
| [Aktivieren des virtuellen Peernetzwerks](how-to-connect-peer-virtual-network.md) | In folgenden Fällen aktivieren:<ul><li>Der Kurs erfordert eine R Server-Freigabe.</li><li>Für den Kurs sind große Datendateien erforderlich, die Sie extern und nicht auf der Kursteilnehmer-VM speichern möchten.</li></ul> |

> [!IMPORTANT]
> Wenn Sie das Peer-VNet aktivieren möchten, muss dies vor der Erstellung des Labs erfolgen.

## <a name="lab-configuration"></a>Labkonfiguration

Anweisungen zum Erstellen eines neuen Labs und zum Anwenden der erforderlichen Einstellungen finden Sie unter [Tutorial: Einrichten eines Classroom-Labs](tutorial-setup-classroom-lab.md).  Wenden Sie beim Erstellen des Labs die folgenden Einstellungen an:

| Lab-Einrichtung | Wert und Beschreibung |
| ------------ | ------------------ |
| VM-Größe | Kleine GPU (Compute)|
| VM-Image | Windows 10 Pro. Version 2004 |

## <a name="external-resource-configuration"></a>Konfiguration externer Ressourcen

Für einige Kurse müssen Dateien, z. B. große Datendateien, extern gespeichert werden.  Optionen und Setupanweisungen finden Sie unter [Verwenden von externem Dateispeicher in Azure Lab Services](how-to-attach-external-storage.md).

Wenn Sie sich für eine RStudio Server-Freigabe für die Kursteilnehmer entscheiden, muss der Server eingerichtet werden, bevor das Lab erstellt wird.  Weitere Informationen zum Einrichten eines freigegebenen Servers finden Sie unter [Erstellen eines Labs mit einer freigegebenen Ressource in Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Anweisungen zum Erstellen eines RStudio-Servers finden Sie unter [Herunterladen von RStudio Server für Debian und Ubuntu](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/) und [Zugreifen auf RStudio Server (Open-Source)](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started).

## <a name="template-configuration"></a>Vorlagenkonfiguration

Nachdem Sie den Vorlagencomputer erstellt haben, starten Sie ihn, und stellen Sie eine Verbindung mit ihm her, um [R](https://docs.rstudio.com/resources/install-r/) und [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) zu installieren.  

### <a name="install-r"></a>Installieren von R

1. Laden Sie das [neueste Installationsprogramm für R für Windows](https://cran.r-project.org/bin/windows/base/release.html) herunter.  Eine vollständige Liste der verfügbaren Versionen finden Sie auf der [Downloadseite für R für Windows](https://cran.r-project.org/bin/windows/base/).
2. Führen Sie den Installer aus.
    1. Wählen Sie für die Eingabeaufforderung **Setupsprache auswählen** die gewünschte Sprache aus, und klicken Sie auf **OK**.
    2. Lesen Sie auf der Seite **Information** (Informationen) des Installationsprogramms den Lizenzvertrag.  Wählen Sie **Next** (Weiter) aus, um die Vereinbarung zu akzeptieren und fortzufahren.
    3. Übernehmen Sie auf der Seite **Select Destination Location** (Zielspeicherort auswählen) den Standardinstallationsspeicherort, und wählen Sie **Next** (Weiter) aus.
    4. Deaktivieren Sie auf der Seite **Select Components** (Komponenten auswählen) optional die Option **32-bit files** (32-Bit-Dateien).  Weitere Informationen zum Ausführen von 32-Bit- und 62-Bit-Versionen von R finden Sie unter der häufig gestellten Frage [Kann sowohl 32-Bit- als auch 64-Bit-R auf demselben Computer installiert werden?](https://cran.r-project.org/bin/windows/base/rw-FAQ.html#Can-both-32_002d-and-64_002dbit-R-be-installed-on-the-same-machine_003f).
    5. Belassen Sie auf der Seite **Startup options** (Startoptionen) die Startoptionen bei **No (accept defaults)** (Nein (Standardwerte übernehmen)).  Wenn Sie möchten, dass die grafische Benutzeroberfläche (GUI) von R separate Fenster (SDI) oder reine Texthilfe verwendet, wählen Sie das Optionsfeld **Yes (customize startup)** (Ja (Start anpassen)) aus und ändern die Startoptionen auf den folgenden Seiten des Assistenten.
    6. Klicken Sie auf der Seite **Startmenüordner auswählen** auf **Weiter**.
    7. Wählen Sie auf der Seite **Select Additional Tasks** (Zusätzliche Aufgaben auswählen) optional **Create a desktop shortcut** (Desktopverknüpfung erstellen) aus.  Wählen Sie **Weiter** aus.
    8. Warten Sie auf der Seite **Installing** (Installieren), bis die Installation abgeschlossen ist.
    9. Wählen Sie auf der Seite **Completing the R for Windows** (R für Windows abschließen) die Option **Finish** (Fertig stellen) aus.

Sie können die Installation von R auch mithilfe von PowerShell ausführen.  Das Codebeispiel zeigt, wie Sie R ohne die 32-Bit-Komponente installieren und ein Desktopsymbol für die neueste Version von R hinzufügen. Eine vollständige Liste der Befehlszeilenoptionen für das Installationsprogramm finden Sie unter [Befehlszeilenparameter für das Setup](https://jrsoftware.org/ishelp/index.php?topic=setupcmdline).

```powershell
#Avoid prompt to setup Internet Explorer if we must parse download page
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Internet Explorer\Main" -Name "DisableFirstRunCustomize" -Value 2

$outputfile = "R-win.exe"

$result = Invoke-WebRequest "https://cran.r-project.org/bin/windows/base/release.html" -OutFile $outputfile -PassThru

#Check if we need to parse the result ourselves, to find the latest version of R
if ($result.StatusCode -eq '200' -and $result.Headers["Content-Type"] -eq 'text/html')
{
    $metaTag = $result.ParsedHtml.Head.children | Where-Object {$_.nodeName -eq 'META'}
    if ($metaTag.content  -match "R-\d+\.\d+\.\d+-win.exe"){
        $outputfile = $Matches.0

        #Download latest version
        Invoke-WebRequest "https://cran.r-project.org/bin/windows/base/$outputfile" -OutFile $outputfile
    }else{
        Write-Error "Unable to find latest version of R installer.  Go to https://cran.r-project.org/bin/windows/base/release.html to download manually."
    }
}

#Install Silently
$installPath = Get-Item -Path $outputfile
Start-Process -FilePath $installPath.FullName -ArgumentList "/VERYSILENT /LOG=r-install.log /NORESTART /COMPONENTS=""main,x64,translations"" /MERGETASKS=""desktopicon"" /LANG=""en""" -NoNewWindow -Wait
```

### <a name="install-rstudio"></a>Installieren von RStudio

Nachdem Sie R lokal installiert haben, können Sie die RStudio-IDE installieren.  Wir installieren die kostenlose Version von RStudio Desktop.  Informationen zu allen verfügbaren Versionen finden Sie unter [RStudio-Downloads](https://www.rstudio.com/products/rstudio/download/).

1. Laden Sie das [Installationsprogramm für RStudio](https://www.rstudio.com/products/rstudio/download/#download) für Windows 10 herunter.  Die Installationsdatei weist das Format `rstudio-{version}.exe` auf.  
2. Führen Sie das Installationsprogramm von RStudio aus.
    1. Wählen Sie auf der Seite **Welcome to RStudio Setup** (Willkommen beim RStudio-Setup) des **RStudio-Setup**-Assistenten die Option **Next** (Weiter) aus.
    2. Wählen Sie auf der Seite **Choose Install Location** (Installationsspeicherort auswählen) die Option **Next** (Weiter) aus.
    3. Wählen Sie auf der Seite **Choose Start Menu Folder** (Ordner des Startmenüs auswählen) die Option **Install** (Installieren) aus.
    4. Warten Sie auf der Seite **Installing** (Installieren), bis die Installation abgeschlossen ist.
    5. Wählen Sie auf der Seite **Completing RStudio Setup** (RStudio-Setup abschließen) die Option **Finish** (Fertig stellen) aus.

Führen Sie die folgenden Befehle aus, um die RStudio-Installationsschritte mithilfe von PowerShell auszuführen.  Überprüfen Sie unter [RStudio-Downloads](https://www.rstudio.com/products/rstudio/download/), ob die RStudio-Version verfügbar ist, bevor Sie die Befehle ausführen.

```powershell
$rstudiover="1.4.1717"
$outputfile = "RStudio-$rstudiover.exe"

#Download installer executable
Invoke-WebRequest "https://download1.rstudio.org/desktop/windows/RStudio-$rstudiover.exe" -OutFile $outputfile

#Install RStudio silently
$installPath = Get-Item -Path $outputfile
Start-Process -FilePath $installPath.FullName -ArgumentList "/S" -NoNewWindow -Wait
```

### <a name="cran-packages"></a>CRAN-Pakete

Verwenden Sie den Befehl `install.packages(“package name”)` in einer interaktiven R-Sitzung, wie im Artikel [Schnellliste mit nützlichen R-Paketen](https://support.rstudio.com/hc/articles/201057987-Quick-list-of-useful-R-packages) gezeigt.  Verwenden Sie alternativ das Menüelement „Tools -> Install Packages“ (Extras > Pakete installieren) in RStudio.

Wenn Sie bei der Suche nach einem Paket Hilfe benötigen, sehen Sie sich eine [Liste von Paketen nach Aufgabe](https://cran.r-project.org/web/views/) oder eine [alphabetische Liste der Pakete](https://cloud.r-project.org/web/packages/available_packages_by_name.html) an.

## <a name="cost"></a>Cost

Sehen wir uns eine Beispielkostenschätzung für diesen Kurs an.  Angenommen, Sie haben einen Kurs mit 25 Kursteilnehmern. Für jeden Kursteilnehmer ist eine Kurszeit von 20 Stunden geplant.  Zudem erhält jeder Kursteilnehmer ein Kontingent von zehn zusätzlichen Stunden für Hausaufgaben und Arbeitsaufträge außerhalb der geplanten Kurszeit.  Als Größe des virtuellen Computers wurde **Kleine GPU (Compute)** ausgewählt, was 139 Lab-Einheiten entspricht.

25 Kursteilnehmer &times; (20 geplante Stunden + 10 Kontingentstunden) &times; 139 Lab-Einheiten &times; 0,01 USD pro Stunde = 1042,5 USD

> [!IMPORTANT]
> Diese Kostenschätzung dient ausschließlich zu Beispielzwecken.  Aktuelle Informationen zu Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Nächste Schritte

Das Vorlagenimage kann jetzt im Lab veröffentlicht werden. Weitere Anweisungen finden Sie unter [Veröffentlichen der Vorlage für virtuelle Computer](how-to-create-manage-template.md#publish-the-template-vm).

Lesen Sie folgende Artikel zum Einrichten des Labs:

- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Festlegen von Kontingenten](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)
