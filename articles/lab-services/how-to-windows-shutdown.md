---
title: Leitfaden zum Steuern des Verhaltens beim Herunterfahren von Windows in Azure Lab Services | Microsoft-Dokumentation
description: Schritte zum automatischen Herunterfahren eines virtuellen Windows-Computers, der sich im Leerlauf befindet, und zum Entfernen des Windows-Befehls zum Herunterfahren.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: b76855e80d01091686911ab3d55b7b05eaf92bb5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179904"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Leitfaden zum Steuern des Verhaltens beim Herunterfahren von Windows

Azure Lab Services bietet mehrere Kostenkontrollen, um zu verhindern, dass virtuelle Windows-Computer (VMs) unerwartet ausgeführt werden:
 - [Zeitplan festlegen](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
 - [Festlegen von Kontingenten für Benutzer](./how-to-configure-student-usage.md#set-quotas-for-users)
 - [Aktivieren von automatischem Herunterfahren beim Trennen der Verbindung](./how-to-enable-shutdown-disconnect.md)

Selbst bei Verwendung dieser Kostenkontrollen können Situationen eintreten, in denen eine Windows-VM unerwartet weiter ausgeführt und folglich vom Kontingent des Studenten abgezogen wird:

- **Das RDP-Fenster bleibt geöffnet.**
  
    Wenn ein Kursteilnehmer per RDP eine Verbindung mit dem virtuellen Computer herstellt, lässt er möglicherweise das RDP-Fenster versehentlich geöffnet.  Solange das RDP-Fenster geöffnet bleibt, wird die Einstellung zum **automatischen Herunterfahren beim Trennen der Verbindung** nicht angewendet, da sie erst ausgelöst wird, nachdem die RDP-Sitzung getrennt wurde.

- **Die VM wird mit dem Windows-Befehl zum Herunterfahren ausgeschaltet.**
  
    Ein Kursteilnehmer kann zum Ausschalten der VM den Windows-Befehl zum Herunterfahren oder andere in Windows bereitgestellte Mechanismen zum Herunterfahren statt der [Stopp-Schaltfläche in Azure Lab Services](./how-to-use-classroom-lab.md#start-or-stop-the-vm) verwenden.  In diesem Fall ist aus Sicht von Azure Lab Services die VM noch in Verwendung.
    
Um diese Situationen zu verhindern, werden im Leitfaden die Schritte zum automatischen Herunterfahren einer Windows-VM im Leerlauf und zum Entfernen des Windows-Befehls zum Herunterfahren aus dem **Startmenü** beschrieben.  

> [!NOTE]
> Eine VM kann auch unerwartet vom Kontingent abgezogen werden, wenn der Kursteilnehmer die VM startet, aber nie eine RDP-Verbindung mit ihr herstellt.  Dieses Szenario wird im Leitfaden derzeit *nicht* behandelt.  Stattdessen sollten die Kursteilnehmer aufgefordert werden, sofort nach dem Starten der VM eine RDP-Verbindung mit ihr herzustellen, oder die VM zu beenden.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Entfernen des Windows-Befehls zum Herunterfahren aus dem Startmenü

Sie können den Befehl zum Herunterfahren auch mit den Windows-Einstellungen für **Lokale Gruppenrichtlinien** aus dem **Startmenü** entfernen.

Um den Befehl zum Herunterfahren zu entfernen, können Sie eine Verbindung mit der Vorlagen-VM herstellen und das folgende PowerShell-Skript ausführen.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Oder Sie können mithilfe der Vorlagen-VM die folgenden manuellen Schritte ausführen:

1. Drücken Sie die Windows-Taste, geben Sie **gpedit** ein, und wählen Sie dann **Gruppenrichtlinie bearbeiten (Systemsteuerung)** aus.

1. Wechseln Sie zu **Computerkonfiguration > Administrative Vorlagen > Startmenü und Taskleiste**.  

    ![Editor für lokale Gruppenrichtlinien](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Klicken Sie mit der rechten Maustaste auf **Befehle „Herunterfahren“, „Neu starten“, „Energie sparen“ und „Ruhezustand“ entfernen und Zugriff darauf verweigern**, und klicken Sie auf **Bearbeiten**.

1. Aktivieren Sie die Einstellung **Aktiviert**, und klicken Sie dann auf **OK**:
 
   ![Einstellung für Herunterfahren](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Beachten Sie, dass der Befehl zum Herunterfahren nicht mehr im **Startmenü** von Windows angezeigt wird. Es wird nur noch der Befehl **Trennen** angezeigt.

    ![Befehl „Herunterfahren“](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie den Artikel zum Erstellen einer Windows-Vorlagen-VM: [Leitfaden zum Einrichten eines Windows-Vorlagencomputers in Azure Lab Services](how-to-prepare-windows-template.md)