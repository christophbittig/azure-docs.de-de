---
title: Multimediaumleitung in Azure Virtual Desktop – Azure
description: Verwenden der Multimediaumleitung für Azure Virtual Desktop (Vorschau)
author: Heidilohr
ms.topic: how-to
ms.date: 08/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 574a10f6ef79ff3d40f5d62e49db9ebf198d2a79
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356539"
---
# <a name="multimedia-redirection-for-azure-virtual-desktop-preview"></a>Multimediaumleitung für Azure Virtual Desktop (Vorschau)

> [!IMPORTANT]
> Die Multimediaumleitung für Azure Virtual Desktop befindet sich derzeit in der Vorschau.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

>[!NOTE]
>Azure Virtual Desktop unterstützt derzeit keine Multimediaumleitung in Azure Virtual Desktop für Microsoft 365 Government (GCC), GCC-High-Umgebungen und Microsoft 365 DoD.
>
>Die Multimediaumleitung in Azure Virtual Desktop ist nur für den Windows-Desktopclient unter Windows 10 verfügbar. Die Multimediaumleitung erfordert mindestens Version 1.2.2222 des Windows-Desktopclients.

Die Multimediaumleitung (Multimedia Redirection, MMR) ermöglicht eine ruckelfreie Videowiedergabe in Ihrem Azure Virtual Desktop-Browser. Mit der Multimediaumleitung wird das Medienelement remote zum Browser auf dem lokalen Computer platziert, um die Verarbeitung und das Rendering zu beschleunigen. Sowohl Microsoft Edge als auch Google Chrome unterstützen die Multimediaumleitungsfunktion. Bei der öffentlichen Vorschauversion der Multimediaumleitung für Azure Virtual Desktop ist die Wiedergabe jedoch auf YouTube beschränkt. Um YouTube innerhalb der Bereitstellung Ihrer Organisation zu testen, müssen Sie [eine Erweiterung aktivieren](#managing-group-policies-for-the-multimedia-redirection-browser-extension).

## <a name="requirements"></a>Requirements (Anforderungen)

Bevor Sie die Multimediaumleitung in Azure Virtual Desktop verwenden können, müssen Sie Folgendes ausführen:

1. [Installieren Sie den Windows-Desktopclient](./user-documentation/connect-windows-7-10.md#install-the-windows-desktop-client) auf einem Windows 10- oder Windows 10 IoT Enterprise-Gerät, das die [Hardwareanforderungen für Teams auf einem Windows-PC](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/) erfüllt. Wenn Sie Version 1.2.2222 oder höher des Clients installieren, wird auch das Multimediaumleitungs-Plug-In (MsMmrDVCPlugin.dll) auf dem Clientgerät installiert. Weitere Informationen zu Updates und neuen Versionen finden Sie unter [Neuigkeiten im Windows-Desktopclient](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew).

2. [Konfigurieren Sie den Clientcomputer für die Insider-Gruppe.](create-host-pools-azure-marketplace.md)

3. Installieren Sie den [Multimediaumleitungsdienst](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWIzIk) und alle erforderlichen Browsererweiterungen auf der VM.

4. Konfigurieren Sie den Clientcomputer so, dass Ihre Benutzer auf das Insider-Programm zugreifen können. Um den Client für die Gruppe „Insider“ zu konfigurieren, legst du die folgenden Registrierungsinformationen fest:

   - **Schlüssel:** HKLM\\Software\\Microsoft\\MSRDC\\Policies
   - **Typ**: REG_SZ
   - **Name:** ReleaseRing
   - **Daten:** insider

   Weitere Informationen zum Insider-Programm finden Sie unter [Windows-Desktopclient für Administratoren](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin#configure-user-groups).

5. Verwenden Sie das [MSI-Installationsprogramm (MsMmrHostMri)](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWIzIk), um die Erweiterung für die Multimediaumleitung für Ihren Internetbrowser auf Ihrer Azure-VM zu installieren. Die Multimediaumleitung für Azure Virtual Desktop unterstützt derzeit nur Microsoft Edge und Google Chrome.

## <a name="managing-group-policies-for-the-multimedia-redirection-browser-extension"></a>Verwalten von Gruppenrichtlinien für die Browsererweiterung für die Multimediaumleitung

Mit dem MSI-Paket für die Multimediaumleitung werden auch die Browsererweiterungen installiert. Da sich dieser Dienst jedoch noch in der öffentlichen Vorschau befindet, kann die Benutzeroberfläche abweichen. Weitere Informationen zu bekannten Problemen finden Sie unter [Bekannte Probleme](#known-issues-and-limitations).

In einigen Fällen können Sie die Gruppenrichtlinie ändern, um die Browsererweiterungen zu verwalten und die Benutzerfreundlichkeit zu verbessern. Zum Beispiel:

- Sie können die Erweiterung ohne Benutzerinteraktion installieren.
- Sie können einschränken, für welche Websites die Multimediaumleitung verwendet werden soll.
- Sie können das Erweiterungssymbol in Google Chrome standardmäßig anheften. Das Erweiterungssymbol ist in Microsoft Edge bereits standardmäßig angeheftet, sodass Sie diese Einstellung nur in Chrome ändern müssen.

### <a name="configure-microsoft-edge-group-policies-for-multimedia-redirection"></a>Konfigurieren von Microsoft Edge-Gruppenrichtlinien für die Multimediaumleitung

Zum Konfigurieren der Gruppenrichtlinien müssen Sie die administrative Vorlage für Microsoft Edge bearbeiten. Die Konfigurationsoptionen für die Erweiterung sollten unter den **administrativen Vorlagen Microsoft Edge-Erweiterungen** > **Einstellungen für die Erweiterungsverwaltung konfigurieren** angezeigt werden.

Der folgende Code ist ein Beispiel für eine Microsoft Edge-Gruppenrichtlinie, mit der der Browser die Erweiterung für die Multimediaumleitung installiert und das Laden der Multimediaumleitung nur bei YouTube zulässt:

```cmd
{ "joeclbldhdmoijbaagobkhlpfjglcihd": { "installation_mode": "force_installed", "runtime_allowed_hosts": [ "*://*.youtube.com" ], "runtime_blocked_hosts": [ "*://*" ], "update_url": "https://edge.microsoft.com/extensionwebstorebase/v1/crx" } }
```

Weitere Informationen zum Konfigurieren von Gruppenrichtlinien finden Sie unter [Microsoft Edge-Gruppenrichtlinien](/DeployEdge/configure-microsoft-edge).

### <a name="configure-google-chrome-group-policies-for-multimedia-redirection"></a>Konfigurieren von Google Chrome-Gruppenrichtlinien für die Multimediaumleitung

Zum Konfigurieren von Google Chrome-Gruppenrichtlinien müssen Sie die administrative Vorlage für Google Chrome bearbeiten. Die Konfigurationsoptionen für die Erweiterung sollten unter den **Administrativen Vorlagen** > **Google** > **Google Chrome-Erweiterungen** > **Einstellungen für die Erweiterungsverwaltung** angezeigt werden.

Das folgende Beispiel ähnelt dem Codebeispiel in [Konfigurieren von Microsoft Edge-Gruppenrichtlinien für die Multimediaumleitung](#configure-microsoft-edge-group-policies-for-multimedia-redirection). Diese Richtlinie erzwingt die Installation der Erweiterung für die Multimediaumleitung und heftet das Symbol im Menü rechts oben an. Sie lässt das Laden der Multimediaumleitung nur bei YouTube zu.

```cmd
{ "lfmemoeeciijgkjkgbgikoonlkabmlno": { "installation_mode": "force_installed", "runtime_allowed_hosts": [ "*://*.youtube.com" ], "runtime_blocked_hosts": [ "*://*" ], "toolbar_pin": "force_pinned", "update_url": "https://clients2.google.com/service/update2/crx" } }
```

Weitere Informationen zum Konfigurieren von [Google Chrome-Gruppenrichtlinien](https://support.google.com/chrome/a/answer/187202#zippy=%2Cwindows).

## <a name="run-the-multimedia-redirection-extension-manually-on-a-browser"></a>Manuelles Ausführen der Erweiterung für die Multimediaumleitung in einem Browser

Die MMR verwendet Remote-Apps und den Sitzungsdesktop für die Browser Microsoft Edge und Google Chrome. Wenn die [Anforderungen](#requirements) erfüllt sind, öffnen Sie Ihren unterstützten Browser. Wenn Sie die Browser oder die Erweiterung nicht mit einer Gruppenrichtlinie installiert haben, müssen Benutzer die Erweiterung manuell ausführen. In diesem Abschnitt erfahren Sie, wie Sie die Erweiterung manuell in einem der derzeit unterstützten Browser ausführen.

### <a name="microsoft-edge"></a>Microsoft Edge

Um die Erweiterung manuell in Microsoft Edge auszuführen, suchen Sie im Überlaufmenü nach dem gelben Ausrufezeichen. Es sollte eine Aufforderung zum Aktivieren der Azure Virtual Desktop-Erweiterung für die Multimediaumleitung angezeigt werden. Wählen Sie **Erweiterung aktivieren** aus.

### <a name="google-chrome"></a>Google Chrome

Um die Erweiterung manuell in Google Chrome auszuführen, suchen Sie wie im folgenden Screenshot gezeigt nach der Benachrichtigung, dass die neue Erweiterung installiert wurde. 

![Screenshot der Google Chrome-Taskleiste Es gibt eine Benachrichtigungsregisterkarte mit der Meldung „Neue Erweiterung hinzugefügt“.](media/chrome-notification.png)

Wählen Sie die Benachrichtigung aus, um Benutzern das Aktivieren der Erweiterung zu erlauben. Benutzer sollten die Erweiterung außerdem anheften, damit sie über das Symbol erkennen können, ob die Multimediaumleitung verbunden ist.

### <a name="the-multimedia-redirection-status-icon"></a>Statussymbol der Multimediaumleitung

Um schnell zu erkennen, ob die Multimediaumleitung in Ihrem Browser aktiv ist, wurden die folgenden Symbolzustände hinzugefügt:

| Symbolzustand  | Definition  |
|-----------------|-----------------|
| [Das Standardprogrammsymbol von Azure Virtual Desktop ohne angewandten Status](/media/icon-default.png) | Die Darstellung des Standardsymbols ohne angewandten Status |
| [Das Programmsymbol von Azure Virtual Desktop mit einem roten Quadrat und einem „X“, das angibt, dass die Multimediaumleitung nicht funktioniert](/media/icon-disconnect.png) | Das rote Quadrat mit einem „X“ darin bedeutet, dass der Client keine Verbindung mit der Multimediaumleitung herstellen konnte. |
| [Das Programmsymbol von Azure Virtual Desktop mit einem grünen Quadrat und einem Häkchen, das angibt, dass die Multimediaumleitung funktioniert](/media/icon-connect.png) | Das grüne Quadrat mit einem Häkchen darin bedeutet, dass der Client erfolgreich eine Verbindung mit der Multimediaumleitung hergestellt hat. |

Wenn Sie das Symbol auswählen, wird ein Popupmenü mit einem Kontrollkästchen angezeigt, über das Sie die Multimediaumleitung auf allen Websites aktivieren oder deaktivieren können. Außerdem werden die Versionsnummern aller Komponenten des Diensts aufgeführt.

## <a name="send-feedback-during-public-preview"></a>Übermitteln von Feedback während der öffentlichen Vorschau

Wenn bei Ihnen Probleme auftreten, können Sie uns dies im Feedback-Hub auf dem Client und dem VM-Host mitteilen.

So übermitteln Sie Feedback

1. Öffnen Sie den **Feedback-Hub** sowohl auf dem Client als auch auf dem Server.

2. Wählen Sie **Problem melden** aus.

3. Verwenden Sie in beiden Problemberichten den gleichen Titel, geben Sie aber an, von wo aus Sie den Bericht übermitteln, indem Sie am Anfang entweder „[Client]“ oder „[Host]“ einfügen.

    Wenn Sie beispielsweise ein Problem vom Client aus übermitteln, verwenden Sie folgendes Format:

    >[Client] Titel des Berichts

    Wenn Sie ein Problem vom Host aus übermitteln, verwenden Sie folgendes Format:

    >[Host] Titel des Berichts

4. Beschreiben Sie das Problem, das bei Ihnen auftritt, im Feld **Detaillierter erläutern**. Es wird empfohlen, auch die URL des Videos einzufügen, das Sie wiedergegeben haben, als das Problem auftrat.

5. Wenn Sie fertig sind, wählen Sie **Weiter** aus.

6. Wählen Sie die Blase **Problem** und anschließend in den beiden Dropdownmenüs **Apps** und **Remotedesktop** aus, wie im folgenden Screenshot gezeigt.

    ![Screenshot des Fensters „2. Wählen Sie eine Kategorie aus“. Der Benutzer hat die Blase „Problem“ und dann in den Dropdownmenüs darunter „Apps“ und „Remotedesktop“ ausgewählt.](media/problem-category.png)

7. Klicken Sie auf **Weiter**.

8. Überprüfen Sie, ob in der Liste ein ähnliches Problem enthalten ist wie das, das Sie übermitteln möchten.
   
   - Wenn eine Blase angezeigt wird, die mit einem aktiven Fehler verknüpft ist, überprüfen Sie, ob die Beschreibung des Fehlers dem Problem entspricht, das Sie melden möchten. Wenn dies der Fall ist, wählen Sie die Blase und dann **Link to bug** (Mit Fehler verknüpfen) aus, wie im folgenden Screenshot gezeigt.

    ![Screenshot des Fensters „3. Find similar feedback“ (3. Ähnliches Feedback finden). Der Benutzer hat die Blase für die Option „Link to bug number 32350300 Active“ (Mit aktivem Bug 32350300 verknüpfen) ausgewählt.](media/link-to-bug.png)

    - Wenn Sie kein ähnliches Problem finden, wählen Sie **Make new bug** (Neuen Fehler erstellen) aus.

    ![Screenshot des Fensters „3. Find similar feedback“ (3. Ähnliches Feedback finden). Dieses Mal fehlt die Option „Link to bug“ (Mit Fehler verknüpfen), und der Benutzer hat stattdessen „Make new bug“ (Neuen Fehler erstellen) ausgewählt.](media/make-new-bug.png)

9. Klicken Sie auf **Weiter**.

10. Wählen Sie im Fenster **Weitere Details hinzufügen** die Option **Include data about Remote Desktop (Default)** (Daten zu Remotedesktop einschließen (Standard)) aus, und beantworten Sie dann alle Fragen so detailliert wie möglich.

    Wenn Sie eine Videoaufzeichnung des Problems hinzufügen möchten, wählen Sie **Include data about Remote Desktop (Default)** (Daten zu Remotedesktop einschließen (Standard)) und dann die Schaltfläche **Aufzeichnung starten** aus. Öffnen Sie während der Aufzeichnung Remotedesktop, und führen Sie den Vorgang aus, der zum Problem geführt hat. Wenn Sie fertig sind, wechseln Sie zum Browser zurück, und testen Sie das Video, um sicherzustellen, dass es ordnungsgemäß aufgezeichnet wurde.

    Stimmen Sie anschließend dem Senden der angefügten Dateien und Diagnosedaten an Microsoft zu, und wählen Sie dann **Übermitteln** aus.

### <a name="known-issues-and-limitations"></a>Einschränkungen und bekannte Probleme

Die folgenden Probleme sind bereits bekannt und müssen daher nicht mehr gemeldet werden:

- Die Multimediaumleitung funktioniert nur mit dem Windows-Desktopclient, nicht mit dem Webclient.

- Die Multimediaumleitung unterstützt derzeit keine geschützten Inhalte, sodass Videos von Pluralsight und Netflix nicht funktionieren.

- Während der öffentlichen Vorschau ist die Multimediaumleitung auf allen Websites mit Ausnahme von YouTube deaktiviert. Wenn Sie jedoch über die Erweiterung verfügen, können Sie die Multimediaumleitung für alle Websites aktivieren. Die Erweiterung wurde hinzugefügt, damit Organisationen das Feature auf ihren Unternehmenswebsites testen können.

- Es besteht eine geringe Wahrscheinlichkeit, dass das MSI-Installationsprogramm die Erweiterung während interner Tests nicht installieren kann. Wenn dieses Problem bei Ihnen auftritt, müssen Sie die Erweiterung für die Multimediaumleitung aus dem Microsoft Edge-Store oder Google Chrome-Store installieren.

    - [Browsererweiterung für die Multimediaumleitung (Microsoft Edge)](https://microsoftedge.microsoft.com/addons/detail/wvd-multimedia-redirectio/joeclbldhdmoijbaagobkhlpfjglcihd)
    - [Browsererweiterung für die Multimediaumleitung (Google Chrome)](https://chrome.google.com/webstore/detail/wvd-multimedia-redirectio/lfmemoeeciijgkjkgbgikoonlkabmlno)

- Wenn Sie die Erweiterung mit dem MSI-Installationsprogramm auf Hostcomputern installieren, werden entweder die Benutzer aufgefordert, die Erweiterung beim ersten Öffnen des Browsers zu akzeptieren, oder es wird eine Warnung oder Fehlermeldung angezeigt. Wenn Benutzer diese Aufforderung verweigern, kann dies dazu führen, dass die Erweiterung nicht geladen wird. Um dieses Problem zu vermeiden, installieren Sie die Erweiterungen, indem Sie die [Gruppenrichtlinien bearbeiten](#managing-group-policies-for-the-multimedia-redirection-browser-extension).

- Wenn Sie die Größe des Videofensters ändern, wird die Größe des Fensters schneller angepasst als das Video selbst. Dieses Problem tritt auch beim Minimieren und Maximieren des Fensters auf.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie am Videostreaming in anderen Bereichen von Azure Virtual Desktop interessiert sind, informieren Sie sich über [Teams für Azure Virtual Desktop](teams-on-avd.md).
