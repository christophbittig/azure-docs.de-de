---
title: Einrichten eines Labs mit R und RStudio unter Linux mit Azure Lab Services
description: Erfahren Sie, wie Sie Labs einrichten, um R mithilfe von RStudio unter Linux zu unterrichten.
author: emaher
ms.topic: article
ms.date: 08/25/2021
ms.author: enewman
ms.openlocfilehash: 14e30572ab620b36730c725c7ac5a2e762947366
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755489"
---
# <a name="set-up-a-lab-to-teach-r-on-linux"></a>Einrichten eines Labs zum Unterrichten von R unter Linux

[R](https://www.r-project.org/about.html) ist eine Open-Source-Sprache, die für statistisches Computing und Grafiken verwendet wird.  Sie wird in der statistischen Analyse von Genetik bis hin zur Verarbeitung natürlicher Sprache und Analyse von Finanzdaten verwendet.  R bietet als Benutzeroberfläche eine [interaktive Befehlszeile](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line).  [RStudio](https://www.rstudio.com/products/rstudio/) ist eine interaktive Entwicklungsumgebung (IDE), die für die Sprache R zur Verfügung steht.  Die kostenlose Version bietet Codebearbeitungstools, eine integrierte Debugfunktionalität und Paketentwicklungstools.

Dieser Artikel befasst sich ausschließlich mit RStudio und R als Baustein für einen Kurs, in dem statistisches Computing verwendet werden soll.  Für die Kurstypen [Deep Learning](class-type-deep-learning-natural-language-processing.md) sowie [Python und Jupyter Notebooks](class-type-jupyter-notebook.md) wird RStudio unterschiedlich eingerichtet.  In jedem Artikel wird beschrieben, wie Sie das Marketplace-Image [Data Science Virtual Machine für Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-US/marketplace/apps/microsoft-dsvm.ubuntu-1804) verwenden. Darin sind viele [Data Science-bezogene Tools](../machine-learning/data-science-virtual-machine/tools-included.md) vorinstalliert, einschließlich RStudio.  

## <a name="lab-account-configuration"></a>Konfiguration eines Lab-Kontos

Zum Einrichten dieses Labs benötigen Sie zunächst ein Azure-Abonnement und ein Lab-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie ein neues Lab-Konto in Azure Lab Services erstellen. Weitere Informationen zum Erstellen eines neuen Lab-Kontos finden Sie im Tutorial zum [Einrichten eines Lab-Kontos](./tutorial-setup-lab-account.md). Sie können auch ein vorhandenes Lab-Konto verwenden.

### <a name="lab-account-settings"></a>Lab-Kontoeinstellungen

Aktivieren Sie die Einstellungen Ihres Lab-Kontos wie in der folgenden Tabelle beschrieben. Weitere Informationen zum Aktivieren von Azure Marketplace-Images finden Sie unter [Angeben der für Lab-Ersteller verfügbaren Azure Marketplace-Images](./specify-marketplace-images.md).

| Lab-Kontoeinstellungen | Instructions |
| -------------------- | ----- |
| Marketplace-Images | Ubuntu Server 18.04 LTS |
| [Virtuelles Peernetzwerk aktivieren](how-to-connect-peer-virtual-network.md) | In folgenden Fällen aktivieren:<ul><li>Der Kurs erfordert eine R Server-Freigabe.</li><li>Für den Kurs sind große Datendateien erforderlich, die Sie extern und nicht auf der Kursteilnehmer-VM speichern möchten.</li></ul> |

> [!IMPORTANT]
> Wenn Sie das Peer-VNet aktivieren möchten, muss dies vor der Erstellung des Labs erfolgen.

## <a name="lab-configuration"></a>Labkonfiguration

Anweisungen zum Erstellen eines neuen Labs und zum Anwenden der erforderlichen Einstellungen finden Sie unter [Tutorial: Einrichten eines Classroom-Labs](tutorial-setup-classroom-lab.md).  Wenden Sie beim Erstellen des Labs die folgenden Einstellungen an:

| Lab-Einrichtung | Wert und Beschreibung |
| ------------ | ------------------ |
| VM-Größe | Kleine GPU (Compute)|
| VM-Image | Ubuntu Server 18.04 LTS |
| Remotedesktopverbindung aktivieren | Diese Einstellung muss aktiviert werden, wenn Sie RDP verwenden möchten.  Diese Einstellung ist nicht erforderlich, wenn Sie [X2Go zum Herstellen einer Verbindung mit den Lab-Computern](how-to-use-remote-desktop-linux-student.md) auswählen.  Beim ersten Mal müssen Sie über SSH eine Verbindung mit der Linux-VM herstellen und die RDP-/X2Go- und GUI-Pakete installieren.  Weitere Informationen finden Sie unter [Aktivieren des grafischen Remotedesktops für Linux-VMs in Azure Lab Services](how-to-enable-remote-desktop-linux.md). |

## <a name="external-resource-configuration"></a>Konfiguration externer Ressourcen

Für einige Kurse müssen Dateien, z. B. große Datendateien, extern gespeichert werden.  Optionen und Setupanweisungen finden Sie unter [Verwenden von externem Dateispeicher in Azure Lab Services](how-to-attach-external-storage.md).

Wenn Sie sich für eine R Server-Freigabe für die Kursteilnehmer entscheiden, muss der Server eingerichtet werden, bevor das Lab erstellt wird.  Weitere Informationen zum Einrichten eines freigegebenen Servers finden Sie unter [Erstellen eines Labs mit einer freigegebenen Ressource in Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Anweisungen zum Erstellen eines RStudio-Servers finden Sie unter [Herunterladen von RStudio Server für Debian und Ubuntu](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/) und [Zugreifen auf RStudio Server (Open-Source)](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started).

## <a name="template-configuration"></a>Vorlagenkonfiguration

Nachdem Sie den Vorlagencomputer erstellt haben, starten Sie ihn, und stellen Sie eine Verbindung mit ihm her, um [R](https://docs.rstudio.com/resources/install-r/), [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) und optional [X2Go Server](https://wiki.x2go.org/doku.php/doc:installation:x2goserver) zu installieren.  

Zunächst aktualisieren wir apt und führen ein Upgrade für die vorhandenen Pakete auf dem Computer durch.

```bash
sudo apt update 
sudo apt upgrade
```

### <a name="install-x2go-server"></a>Installieren von X2Go Server

Wenn Sie sich für die Verwendung von X2Go entscheiden, [installieren Sie den Server](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop#install-x2go-server).  Als Erstes müssen Sie eine [Verbindung über ssh herstellen](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh), um die Serverkomponente zu installieren.  Wenn dieser Vorgang abgeschlossen ist, kann der Rest des Setups durchgeführt werden, sobald Sie eine [Verbindung über den X2Go-Client](how-to-use-remote-desktop-linux-student.md) hergestellt haben.

Die Standardinstallation von X2Go ist nicht mit RStudio kompatibel.  Um dieses Problem zu umgehen, aktualisieren Sie die x2goagent-Optionsdatei.

1. Bearbeiten Sie die Datei `/etc/x2go/x2goagent.options`.  Denken Sie daran, die Datei als sudo zu bearbeiten.
    1. Heben Sie die Auskommentierung der folgenden Zeile auf: `X2GO_NXAGENT_DEFAULT_OPTIONS+=" -extension GLX"`
    1. Kommentieren Sie die folgenden Zeile aus: `X2GO_NXAGENT_DEFAULT_OPTIONS+=" -extension GLX"`
2. Starten Sie X2Go Server neu, damit die neuen Optionen verwendet werden.

    ```bash
    sudo systemctl restart x2goserver
    ```

Alternativ dazu können Sie die erforderlichen Bibliotheken erstellen, indem Sie die Anweisungen unter [Glx Xlib-Problemumgehung für X2Go](https://wiki.x2go.org/doku.php/wiki:development:glx-xlib-workaround) befolgen.

### <a name="install-r"></a>Installieren von R

Für die Installation von R auf der VM gibt es verschiedene Möglichkeiten.  Wir installieren R über das CRAN-Repository (Comprehensive R Archive Network).  Dieses stellt die neuesten Versionen von R bereit. Sobald dieses Repository auf unserem Computer hinzugefügt wurde, können wir R und viele andere zugehörige Pakete installieren.

Fügen wir das CRAN-Repository hinzu. Die Befehle wurden anhand der Anweisungen geändert, die unter [Ubuntu-Pakete für R – Kurzanleitungen](https://cran.rstudio.com/bin/linux/ubuntu/) verfügbar sind.

```bash
#download helper packages
sudo apt install --no-install-recommends software-properties-common dirmngr
# download and add the signing key (by Michael Rutter) for these repos
sudo wget -q "https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc" -O /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
#add repository
sudo add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/"
```

Jetzt können wir das SDK mithilfe des folgenden Befehls installieren:

```bash
sudo apt install r-base
```

### <a name="install-rstudio"></a>Installieren von RStudio

Nachdem Sie R lokal installiert haben, können Sie die RStudio-IDE installieren.  Wir installieren die kostenlose Version von RStudio Desktop.  Alle verfügbaren Versionen finden Sie unter [RStudio-Downloads](https://www.rstudio.com/products/rstudio/download/).

1. [Importieren Sie den Codesignaturschlüssel](https://www.rstudio.com/code-signing/) für RStudio.

    ```bash
    sudo gpg --keyserver keyserver.ubuntu.com  --recv-keys 3F32EE77E331692F
    ```

2. Laden Sie die [Debian Linux-Paketdatei (DEB) für R Studio](https://www.rstudio.com/products/rstudio/download/#download) für Ubuntu herunter.  Die Datei liegt im Format `rstudio-{version}-amd64.deb` vor.  Beispiel:

    ```bash
    export rstudiover="1.4.1717"
    wget --quiet -O rstudio.deb https://download1.rstudio.org/desktop/bionic/amd64/rstudio-$rstudiover-amd64.deb
    ```

3. Verwenden Sie gdebi, um RStudio zu installieren.   Stellen Sie sicher, dass Sie den Dateipfad verwenden, um für apt die Installation einer lokalen Datei anzugeben.

    ```bash
    sudo apt install gdebi-core 
    echo "y" | gdebi rstudio.deb –quiet
    ```

### <a name="cran-packages"></a>CRAN-Pakete

Jetzt ist der Zeitpunkt für die Installation der gewünschten [CRAN-Pakete](https://cloud.r-project.org/web/packages/available_packages_by_name.html).  Fügen Sie zuerst das [aktuelle c2d4u-Repository für R 4.0 oder höher](https://cran.rstudio.com/bin/linux/ubuntu/#get-5000-cran-packages) hinzu.

```bash
sudo add-apt-repository ppa:c2d4u.team/c2d4u4.0+
```

Verwenden Sie den Befehl `install.packages(“package name”)` in einer interaktiven R-Sitzung, wie im Artikel [Schnellliste mit nützlichen R-Paketen](https://support.rstudio.com/hc/articles/201057987-Quick-list-of-useful-R-packages) gezeigt.  Verwenden Sie alternativ das Menüelement „Tools -> Install Packages“ (Extras > Pakete installieren) in RStudio.

Wenn Sie bei der Suche nach einem Paket Hilfe benötigen, sehen Sie sich eine [Liste von Paketen nach Aufgabe](https://cran.r-project.org/web/views/) oder eine [alphabetische Liste der Pakete](https://cloud.r-project.org/web/packages/available_packages_by_name.html) an.

## <a name="cost"></a>Cost

Sehen wir uns eine Beispielkostenschätzung für diesen Kurs an.  Angenommen, Sie haben einen Kurs mit 25 Kursteilnehmern. Für jeden Kursteilnehmer ist eine Kurszeit von 20 Stunden geplant.  Zudem erhält jeder Kursteilnehmer ein Kontingent von zehn zusätzlichen Stunden für Hausaufgaben und Arbeitsaufträge außerhalb der geplanten Kurszeit.  Als VM-Größe wurde **Kleine GPU (Compute)** ausgewählt, was 139 Lab-Einheiten entspricht.

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