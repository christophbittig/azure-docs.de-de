---
title: Einrichten eines Labs zum Kennenlernen von MATLAB mit Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Lab zum Kennenlernen von MATLAB mit Azure Lab Services einrichten.
author: emaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 8df8963f705fa8314ae3314af194f834deb69bdc
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176793"
---
# <a name="setup-a-lab-to-teach-matlab"></a>Einrichten eines Labs zum Kennenlernen von MATLAB

[MATLAB](https://www.mathworks.com/products/matlab.html) steht für „Matrix Laboratory“ und bezeichnet eine Programmierplattform von [MathWorks](https://www.mathworks.com/).  Sie kombiniert Rechenleistung und Visualisierung, was sie in den Bereichen Mathematik, Ingenieurwesen, Physik und Chemie zu einem beliebten Tool macht.

Wenn Sie eine [campusweite Lizenz](https://www.mathworks.com/academia/tah-support-program/administrators.html) verwenden, laden Sie die Dateien für das MATLAB-Installationsprogramm wie in der verlinkten [Anleitung für das Herunterladen der MATLAB-Installationsdateien](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine) beschrieben auf den Vorlagencomputer herunter.  

In diesem Artikel erfahren Sie, wie Sie eine Klasse einrichten, die MATLAB-Clientsoftware mit einem Lizenzserver verwendet.

## <a name="license-server"></a>Lizenzserver

Bevor Sie den Vorlagencomputer für Ihr Lab anpassen, müssen Sie den Server so einrichten, dass die [Network License Manager](https://www.mathworks.com/help/install/administer-network-licenses.html)-Software (Manager für Netzwerklizenzen) ausgeführt wird.  Diese Anleitung richtet sich ausschließlich an Einrichtungen, die sich für die Option zur Netzwerklizenzierung für MATLAB entscheiden, mit der Benutzer einen Pool von Lizenzschlüsseln gemeinsam nutzen können.  Außerdem müssen Sie die Lizenzdatei und den Dateiinstallationsschlüssel für später speichern.  Eine ausführliche Anleitung zum Herunterladen einer Lizenzdatei finden Sie im ersten Schritt im Artikel [Installieren von Network License Manager (Manager für Netzwerklizenzen) mit einer Internetverbindung](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html).

Detaillierte Anweisungen für das Installieren eines Lizenzservers finden Sie unter [Installieren von Network License Manager (Manager für Netzwerklizenzen) mit einer Internetverbindung](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html).  Im Artikel über das [Leihen von Lizenzen](https://www.mathworks.com/help/install/license/borrow-licenses.html) erfahren Sie, wie Sie dieses aktivieren.

Wenn sich der Lizenzserver in einem lokalen Netzwerk oder einem privaten Netzwerk in Azure befindet, vergessen Sie nicht, ein Peering für [das virtuelle Netzwerk](how-to-connect-peer-virtual-network.md) und Ihr [Labkonto](tutorial-setup-lab-account.md) durchzuführen.  Das Netzwerkpeering muss vor dem Erstellen des Labs erfolgen, damit die virtuellen Labcomputer auf den Lizenzserver zugreifen können.

## <a name="lab-configuration"></a>Labkonfiguration

Zum Einrichten dieses Labs benötigen Sie als Einstieg ein Azure-Abonnement.  Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie entweder ein neues Lab-Konto in Azure Lab Services erstellen oder ein vorhandenes Konto verwenden.  Informationen zum Erstellen eines neuen Labkontos finden Sie im Tutorial [Einrichten eines Labkontos mit Azure Lab Services](tutorial-setup-lab-account.md).

Wenn Sie ein neues Lab erstellen möchten, führen Sie die Schritte im Tutorial [Einrichten eines Classroom-Labs](tutorial-setup-classroom-lab.md) aus.  Wenden Sie die folgenden Einstellungen an:

| Größe des virtuellen Computers | Image |
| -------------------- | ----- |
| Medium | Windows 10 |

MATLAB wird auf weiteren Betriebssystemen unterstützt.  Weitere Informationen hierzu finden Sie bei den [Systemanforderungen für MATLAB](https://www.mathworks.com/support/requirements/matlab-system-requirements.html).

> [!WARNING]
> Vergessen Sie nicht, vor dem Erstellen des Labs ein [Peering für das virtuelle Netzwerk](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) für das Labkonto und das virtuelle Netzwerk für den Lizenzserver durchzuführen.

## <a name="template-machine"></a>Vorlagencomputer

Starten Sie den Vorlagencomputer sobald er erstellt wurde, und stellen Sie eine Verbindung mit ihm her, um die folgenden Hauptaufgaben auszuführen.

1. Laden Sie die Installationsdateien für die MATLAB-Clientsoftware herunter.
2. Installieren Sie MATLAB mit dem Dateiinstallationsschlüssel.

Die Installation von MATLAB besteht aus mehreren Teilen.  Im ersten Teil werden die Dateien für MATLAB und alle anderen Produkte heruntergeladen, die Sie installieren möchten.  Bei der Verwendung eines Dateiinstallationsschlüssels ist es erforderlich, dass alle Installationsdateien für die zu installierenden Produkte vorab heruntergeladen werden.  Im zweiten Teil wird die MATLAB-Software auf der Vorlagen-VM installiert und aktiviert.  Wenn die Vorlagen-VM für die Aktivierung mit dem Lizenzserver konfiguriert ist, gilt dies auch für die Kursteilnehmer-VMs.

### <a name="download-installation-files"></a>Herunterladen der Installationsdateien

Sie müssen Lizenzadministrator sein, um die Installationsdateien herunterzuladen und die Lizenzdatei sowie den Dateiinstallationsschlüssel abzurufen.  Die Schritte zum Herunterladen der Installationsdateien werden im Folgenden erläutert.

1. Melden Sie sich bei Ihrem Konto für [https://www.mathworks.com](https://www.mathworks.com) an.
2. Klicken Sie auf **My Account** (Mein Konto).
3. Klicken Sie auf der Kontoseite im Abschnitt **My Software** (Meine Software) auf die Lizenz, die mit dem Setup von Network License Manager (Manager für Netzwerklizenzen) für das Lab verknüpft ist.
4. Klicken Sie auf der Seite mit den Lizenzdetails auf **Download Products** (Produkte herunterladen).
5. Warten Sie, bis sich das Installationsprogramm selbst extrahiert hat.
6. Starten Sie das Installationsprogramm.  
7. Geben Sie auf der Seite **Sign in to your MathWorks Account** (Anmelden bei Ihrem MathWorks-Konto) die Anmeldeinformationen für Ihr MathWorks-Konto ein.
8. Akzeptieren Sie auf der Seite **MathWorks License Agreement** (MathWorks-Lizenzvertrag) die Bedingungen, und klicken Sie auf die Schaltfläche **Next** (Weiter).
9. Klicken Sie auf die Dropdownliste bei **Advanced Options** (Erweiterte Optionen), und wählen Sie **I want to download without installing** (Ich möchte ohne Installation herunterladen) aus.
10. Klicken Sie auf der Seite **Select destination folder** (Zielordner auswählen) auf **Next** (Weiter).
11. Wählen Sie **Windows** als Plattform des Computers aus, auf dem MATLAB installiert wird.
12. Vergewissern Sie sich auf der Seite **Select product** (Produkt auswählen), dass MATLAB und alle anderen MathWorks-Produkte, die Sie installieren möchten, ausgewählt sind.
13. Klicken Sie auf der Seite **Confirm Selections and Download** (Auswahl bestätigen und herunterladen) auf **Begin Download** (Download starten).  
14. Warten Sie, bis die ausgewählten Produkte heruntergeladen wurden.  Klicken Sie auf **Fertig stellen**.

Sie können auch ein ISO-Image von der MathWorks-Website herunterladen.

1. Melden Sie sich bei Ihrem Konto für [https://www.mathworks.com](https://www.mathworks.com) an.
2. Wechseln Sie zu [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads).
3. Wählen Sie das Release von MATLAB aus, das Sie installieren möchten.
4. Klicken Sie bei den zugehörigen Links auf den Link „Get {version}.iso image“ ({Version}.iso-Image abrufen), wobei bei „{version}“ etwas wie „R2020a“ steht.
5. Klicken Sie auf den blauen Link zum **Herunterladen des Release** für Windows.

### <a name="run-installer"></a>Ausführen des Installationsprogramms

Nach dem Herunterladen der Dateien besteht der zweite Schritt im Ausführen des Installationsprogramms. Auch um diesen Schritt ausführen zu können müssen Sie Lizenzadministrator sein.  Nur Lizenzadministratoren können MATLAB mit einem Dateiinstallationsschlüssel installieren.

1. Überprüfen Sie die heruntergeladene Lizenzdatei, und stellen Sie sicher, dass der Lizenzserver in der Zeile SERVER richtig angegeben ist.  Informationen dazu, wie die Lizenzdatei formatiert sein sollte, finden Sie in den Artikeln zum [Aktualisieren der Netzwerklizenz](https://www.mathworks.com/help/install/ug/network-license-files.html), [Leihen von Lizenzen](https://www.mathworks.com/help/install/license/borrow-licenses.html)und [Ermitteln der Host-ID](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license).
2. Starten Sie das MATLAB-Installationsprogramm.
3. Geben Sie auf der Seite **Sign in to your MathWorks Account** (Anmelden bei Ihrem MathWorks-Konto) die Anmeldeinformationen für Ihr MathWorks-Konto ein.
4. Akzeptieren Sie auf der Seite **MathWorks License Agreement** (MathWorks-Lizenzvertrag) die Bedingungen, und klicken Sie auf die Schaltfläche **Next** (Weiter).
5. Klicken Sie auf die Dropdownliste bei **Advanced Options** (Erweiterte Optionen), und wählen Sie **I have a File Installation Key** (Ich besitze einen Dateiinstallationsschlüssel) aus.
6. Geben Sie auf der Seite **Install using File Installation Key** (Installieren mit Dateiinstallationsschlüssel) den Dateiinstallationsschlüssel für den Lizenzserver ein.   Klicken Sie auf **Weiter**.
7. Navigieren Sie auf der Seite **Select License File** (Lizenzdatei auswählen) zu der zuvor beim Herunterladen der Installationsdateien gespeicherten Lizenzdatei.
8. Klicken Sie auf der Seite **Select Destination Folder** (Zielordner auswählen) auf **Next** (Weiter).
9. Klicken Sie auf der Seite **Select Products** (Produkte auswählen) auf **Next** (Weiter).
10. Klicken Sie auf der Seite **Select Options** (Optionen auswählen) auf **Next** (Weiter).
11. Klicken Sie auf der Seite **Confirm Selections and Install** (Auswahl bestätigen und installieren) auf **Begin Install** (Installation starten).
12. Vergewissern Sie sich, dass auf der Seite **Installation Complete** (Installation abgeschlossen) die Option **Activate MATLAB** (MATLAB aktivieren) aktiviert ist.  Klicken Sie auf **Fertig stellen**.

## <a name="cost-estimate"></a>Kostenschätzung

Betrachten wir eine mögliche Kostenschätzung für diesen Kurs.  Diese Schätzung umfasst nicht die Kosten für das Ausführen des Lizenzservers.  Wir setzen einen Kurs mit 25 Kursteilnehmern an.  Es ist eine Kursdauer von 20 Stunden geplant.  Zudem erhält jeder Kursteilnehmer ein Kontingent von 10 Stunden für Hausaufgaben und Aufgaben außerhalb der regulären Kurszeiten.  Als Größe des virtuellen Computers wurde „mittel“ ausgewählt, was 55 Lab-Einheiten entspricht.

Hier ist ein Beispiel für eine mögliche Kostenschätzung für diesen Kurs:

25 Kursteilnehmer \* (20 geplante Stunden + 10 Kontingentstunden) \* 55 Lab-Einheiten \* 0,01 USD pro Stunde = 412,50 USD

>[!IMPORTANT]
> Diese Kostenschätzung dient ausschließlich zu Beispielzwecken.  Weitere Informationen zu den aktuellen Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Nächste Schritte

Die nächsten Einrichtungsschritte sind für alle Labs gleich.

- [Erstellen und Verwalten einer Classroom-Vorlage in Azure Lab Services](how-to-create-manage-template.md)
- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)
