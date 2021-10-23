---
title: Einrichten eines Linux-Labs zur Shell-Skripterstellung mit Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Lab für Schulungen zu Shellskripts unter Linux einrichten.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0faf5c9a11ad4aed3bf1157bc18dbeb84f60030b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176546"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Einrichten eines Labs zur Schulung in Shellskripts unter Linux
Erfahren Sie, wie Sie unter Linux ein Lab für Schulungen zum Erstellen von Shellskripts einrichten. Die Skripterstellung empfiehlt sich bei der Systemverwaltung, um Administratoren sich wiederholende Aufgaben abzunehmen. In diesem beispielhaften Kurs geht es um klassische Bash-Skripts und erweiterte Skripts. Erweiterte Skripts sind Skripts, die Bash-Befehle und Ruby kombinieren. Dieser Ansatz ermöglicht Ruby das Weiterleiten von Daten, während Bash-Befehle mit der Shell interagieren können. 

Kursteilnehmer bekommen einen virtuellen Linux-Computer zugewiesen, um die Grundlagen von Linux zu erlernen und sich mit der Erstellung von Bash-Skripts vertraut zu machen. Bei dem virtuellen Linux-Computer ist der Remotedesktopzugriff aktiviert und die Text-Editoren [gedit](https://help.gnome.org/users/gedit/stable/) und [Visual Studio Code](https://code.visualstudio.com/) sind installiert.

## <a name="lab-configuration"></a>Labkonfiguration
Zum Einrichten dieses Labs benötigen Sie als Einstieg ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie über ein Azure-Abonnement verfügen, können Sie entweder ein neues Lab-Konto in Azure Lab Services erstellen oder ein vorhandenes Lab-Konto verwenden. Sehen Sie sich das folgende Tutorial zum Erstellen eines neuen Lab-Kontos an: [Tutorial zum Einrichten eines Lab-Kontos](tutorial-setup-lab-account.md).

Nachdem Sie das Lab-Konto erstellt haben, aktivieren Sie die folgenden Einstellungen im Lab-Konto: 

| Lab-Kontoeinstellungen | Instructions |
| ----------- | ------------ |  
| Marketplace-Bilder | Aktivieren Sie das Image „Ubuntu Server 18.04 LTS“ für die Verwendung in Ihrem Lab-Konto. Weitere Informationen finden Sie unter [Angeben von für Lab-Ersteller verfügbaren Marketplace-Images](specify-marketplace-images.md). | 

Folgen Sie [diesem Tutorial](tutorial-setup-classroom-lab.md), um ein neues Lab mit den folgenden Einstellungen zu erstellen:

| Lab-Einstellungen | Wert/Anweisungen | 
| ------------ | ------------------ |
| Größe des virtuellen Computers (VM) | Klein  |
| VM-Image | Ubuntu Server 18.04 LTS|
| Remotedesktopverbindung aktivieren | Enable (Aktivieren). <p>Wenn Sie diese Einstellung aktivieren, können Kursleiter und Kursteilnehmer mithilfe von Remotedesktop (RDP) eine Verbindung mit ihren VMs herstellen. Weitere Informationen finden Sie unter [Aktivieren von Remotedesktop für virtuelle Linux-Computer in einem Lab in Azure Lab Services.](how-to-enable-remote-desktop-linux.md) </p>|

## <a name="install-desktop-and-rdp"></a>Installieren von Desktop und RDP
Im Image „Ubuntu Server 18.04 LTS“ ist standardmäßig kein RDP-Remotedesktopserver installiert. Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Remotedesktop zum Herstellen einer Verbindung mit einem virtuellen Linux-Computer in Azure](../virtual-machines/linux/use-remote-desktop.md), um die auf dem Vorlagencomputer zum Herstellen einer Verbindung über das Remotedesktopprotokoll (RDP) erforderlichen Pakete zu installieren.

## <a name="install-ruby"></a>Installieren von Ruby
Ruby ist eine dynamische Open Source-Programmiersprache, die mit Bash-Skripts kombiniert werden kann. In diesem Abschnitt wird gezeigt, wie Sie `apt-get` verwenden, um die neueste Version von [Ruby](https://www.ruby-lang.org/) zu installieren.

1. Führen Sie zur Installation den folgenden Befehl aus:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  [Ruby](https://www.ruby-lang.org/) installieren.  Ruby ist eine dynamische Open Source-Programmiersprache, die mit Bash-Skripts kombiniert werden kann. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Installieren von Entwicklungstools
In diesem Abschnitt wird gezeigt, wie Sie einige Text-Editoren installieren. Gedit ist der standardmäßige Text-Editor für die GNOME-Desktopumgebung. Es ist als allgemeiner Text-Editor konzipiert. Visual Studio Code ist ein Text-Editor, der Debuggen und die Integration der Quellcodeverwaltung unterstützt.

> [!NOTE]
> Es sind mehrere verschiedene Text-Editoren verfügbar. Visual Studio Code und gedit sind nur zwei Beispiele.

1. Installieren Sie [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).  Visual Studio Code kann mithilfe des Snap Stores installiert werden.  Informationen zu alternativen Installationsoptionen finden Sie unter [Alternative Downloads für Visual Studio Code](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    Die Vorlage ist jetzt aktualisiert und verfügt über die für die Fertigstellung des Labs erforderliche Programmiersprache und Entwicklungstools. Das Vorlagenimage kann jetzt im Lab veröffentlicht werden. Wählen Sie auf der Vorlagenseite die Schaltfläche **Veröffentlichen** aus, um die Vorlage im Lab zu veröffentlichen.  

## <a name="cost"></a>Kosten 
Das folgende Beispiel dient der Einschätzung der Lab-Kosten:
 
Für einen Kurs mit 25 Teilnehmern, 20 planmäßigen Kursstunden und 10 Stunden Hausaufgaben bzw. Arbeitsaufträgen entstünden folgende Kosten für das Lab: 

25 Kursteilnehmer x (20 + 10) Stunden x 20 Lab-Einheiten x 0,01 USD pro Stunde = 150 USD

Weitere Informationen zu den Preisen finden Sie im folgenden Dokument: [Preise für Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Zusammenfassung
In diesem Artikel wurden die Schritte zum Erstellen eines Labs für Skript-Kurse erläutert. Während dieser Artikel schwerpunktmäßig das Einrichten von Ruby-Skripttools auf Linux-VMs behandelt, kann das gleiche Setup auch für andere Skript-Kurse, z. B. Python, unter Linux verwendet werden.

## <a name="next-steps"></a>Nächste Schritte
Die nächsten Schritte sind die gleichen für sämtliche Labs:

- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Registrierungslinks an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users). 





