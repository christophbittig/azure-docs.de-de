---
title: Einrichten eines Labs zum Vermitteln der Front-End-Entwicklung mit React unter Windows mithilfe von Azure Lab Services
description: Hier erfahren Sie, wie Sie Labs zum Vermitteln der Front-End-Entwicklung mit React einrichten.
author: emaher
ms.topic: how-to
ms.date: 05/16/2021
ms.author: enewman
ms.openlocfilehash: f7cd012e609b253269d1aa04137e8e7e94e0ffde
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176680"
---
# <a name="set-up-lab-for-react-on-windows"></a>Einrichten eines Labs für React unter Linux

[React](https://reactjs.org/) ist eine beliebte JavaScript-Bibliothek zum Erstellen von Benutzeroberflächen (UI). React ist eine deklarative Möglichkeit, wiederverwendbare Komponenten für Ihre Website zu erstellen.  Es gibt viele weitere beliebte Bibliotheken für die JavaScript-basierte Front-End-Entwicklung.  Beim Erstellen des Labs werden einige dieser Bibliotheken verwendet.  [Redux](https://redux.js.org/) ist eine Bibliothek, die einen vorhersagbaren Zustandscontainer für JavaScript-Apps bereitstellt und häufig als Ergänzung zu React verwendet wird. [JSX](https://reactjs.org/docs/introducing-jsx.html) ist eine Bibliothekssyntaxerweiterung für JavaScript, die häufig mit React verwendet wird, um zu beschreiben, wie die Benutzeroberfläche aussehen sollte.  [NodeJS](https://nodejs.org/) ist eine bequeme Möglichkeit, einen Webserver für Ihre React-Anwendung auszuführen.

In diesem Artikel wird erläutert, wie Sie [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) für Ihre Entwicklungsumgebung installieren und welche Tools und Bibliotheken für einen React-Webentwicklungskurs erforderlich sind.

## <a name="lab-configuration"></a>Labkonfiguration

Zum Einrichten dieses Labs benötigen Sie zunächst ein Azure-Abonnement und ein Lab-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie ein neues Lab-Konto in Azure Lab Services erstellen. Weitere Informationen zum Erstellen eines neuen Lab-Kontos finden Sie im Tutorial zum [Einrichten eines Lab-Kontos](./tutorial-setup-lab-account.md). Sie können auch ein vorhandenes Lab-Konto verwenden.

### <a name="lab-account-settings"></a>Lab-Kontoeinstellungen

Aktivieren Sie die Einstellungen Ihres Lab-Kontos wie in der folgenden Tabelle beschrieben. Weitere Informationen zum Aktivieren von Azure Marketplace-Images finden Sie unter [Angeben der für Lab-Ersteller verfügbaren Azure Marketplace-Images](./specify-marketplace-images.md).

| Lab-Kontoeinstellungen | Instructions |
| -------------------- | ----- |
| Marketplace-Image | Aktivieren Sie das Image „Visual Studio 2019 Community (aktuelles Release) unter Windows Server 2019 (x64)“ zur Verwendung in Ihrem Labkonto. |

### <a name="lab-settings"></a>Lab-Einstellungen

Die empfohlene VM-Größe hängt von den Arten der Workloads ab, die Ihre Kursteilnehmer verwenden sollen.  

| Lab-Einrichtung | Wert und Beschreibung |
| ------------ | ------------------ |
| VM-Größe | **Mittel** |

Es wird empfohlen, Ihre Workloads zu testen, um festzustellen, ob eine größere Dimensionierung erforderlich ist.  Weitere Informationen zu den einzelnen Größen finden Sie unter [Festlegen der VM-Größe](administrator-guide.md#vm-sizing).

## <a name="template-machine-configuration"></a>Konfiguration des Vorlagencomputers

Die Schritte in diesem Abschnitt zeigen, wie Sie Folgendes ausführen, um die Vorlagen-VM einzurichten:

1. Installieren von Entwicklungstools
1. Installieren von Debuggererweiterungen für Ihren Webbrowser
1. Aktualisieren der Firewalleinstellungen

### <a name="install-development-tools"></a>Installieren von Entwicklungstools

Im Zusammenhang mit dem Image „Visual Studio 2019 Community (neuestes Release) unter Windows Server 2019 (x64)“ ist die erforderliche [Workload **Node.js-Entwicklung**](/visualstudio/javascript/tutorial-nodejs-with-react-and-jsx?view=vs-2019&preserve-view=true#prerequisites) für [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) bereits installiert.

1. Installieren Sie Ihren bevorzugten Webbrowser.  Der Internet Explorer ist für das Image standardmäßig installiert.
1. Navigieren Sie zur Website [Node.js](https://nodejs.org), und klicken Sie auf die Schaltfläche **Herunterladen**.  Sie können die neueste LTS-Version (Long-Term Service), die aktuelle Version mit den neuesten Features oder ein früheres Release verwenden.  Wenn Sie NodeJS installieren, wird auch der [Node-Paket-Manager](https://www.npmjs.com/) installiert, der zum Installieren von React, Redux und JSX verwendet wird.
1. [Aktualisieren Sie Visual Studio 2019](/visualstudio/install/update-visual-studio?view=vs-2019&preserve-view=true) bei Bedarf auf das neueste Release.

Andere Komponenten, die für eine React-basierte Website benötigt werden, werden mithilfe von npm in einer bestimmten Anwendung installiert.  Weitere Informationen zum Hinzufügen von npm-Paketen finden Sie unter [Verwalten von NPM-Paketen in Visual Studio](/visualstudio/javascript/npm-package-management?view=vs-2019&preserve-view=true#add-npm-packages).  

Wenn Sie in einem Projekt beispielsweise das [interaktive Node.js-Fenster](/visualstudio/javascript/nodejs-interactive-repl?view=vs-2019&preserve-view=true) verwenden, geben Sie die folgenden Befehle ein, um die React-, Redux- und JSX-Bibliotheken zu installieren:

```bash
.npm install react
.npm install react-dom
.npm install react-redux
.npm install react-jsx
```

Informationen zum Erstellen Ihrer ersten Node.js- und React-App in Visual Studio finden Sie unter [Tutorial: Erstellen einer Node.js- und React-App in Visual Studio](/visualstudio/javascript/tutorial-nodejs-with-react-and-jsx?view=vs-2019&preserve-view=true).

### <a name="install-debugger-extensions"></a>Installieren von Debuggererweiterungen

Installieren Sie die Erweiterungen für React-Entwicklertools für Ihren Browser, damit Sie React-Komponenten untersuchen und Leistungsinformationen aufzeichnen können.  

- [Edge-Add-On „React Developer Tools“ (React-Entwicklertools)](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)
- [Chrome-Erweiterung „React Developer Tools“ (React-Entwicklertools)](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- [Firefox-Add-On „React Developer Tools“ (React-Entwicklertools)](https://addons.mozilla.org/firefox/addon/react-devtools/)

### <a name="update-firewall-settings"></a>Aktualisieren der Firewalleinstellungen

Eingehender Datenverkehr an Ihren Node.js-Server wird standardmäßig blockiert.  Wenn Sie während der Ausführung auf die Website eines Kursteilnehmers zugreifen möchten, fügen Sie eine Firewallregel für eingehenden Datenverkehr hinzu, um den Datenverkehr zuzulassen.  Sehen Sie sich die **Projekteigenschaft „Anwendungsport“** an, um herauszufinden, welcher Port während des Debuggens verwendet wird.  Im folgenden Beispiel wird das PowerShell-Cmdlet [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule?view=windowsserver2019-ps&preserve-view=true) verwendet, um den Zugriff auf Port 1337 zuzulassen.  

```powershell
New-NetFirewallRule -DisplayName "Allow access to Port 1337" -Direction Inbound -LocalPort 1337 -Protocol TCP -Action Allow
```

>[!IMPORTANT]
>Kursleiter müssen die Vorlagen-VM oder eine andere Lab-VM verwenden, um auf die Website eines Kursteilnehmers zuzugreifen.

## <a name="cost"></a>Cost

Sehen wir uns eine Beispielkostenschätzung für diesen Kurs an.  Angenommen, Sie haben einen Kurs mit 25 Kursteilnehmern. Für jeden Kursteilnehmer ist eine Kurszeit von 20 Stunden geplant.  Zudem erhält jeder Kursteilnehmer ein Kontingent von zehn zusätzlichen Stunden für Hausaufgaben und Arbeitsaufträge außerhalb der geplanten Kurszeit.  Als Größe des virtuellen Computers wurde **Mittel** ausgewählt, was 55 Lab-Einheiten entspricht.

- 25 Kursteilnehmer &times; (20 geplante Stunden + 10 Kontingentstunden) &times; 55 Lab-Einheiten &times; 0,01 USD pro Stunde = 412,50 USD

> [!IMPORTANT]
> Diese Kostenschätzung dient ausschließlich zu Beispielzwecken.  Aktuelle Informationen zu Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Nächste Schritte

Das Vorlagenimage kann jetzt im Lab veröffentlicht werden. Weitere Anweisungen finden Sie unter [Veröffentlichen der Vorlage für virtuelle Computer](how-to-create-manage-template.md#publish-the-template-vm).

Lesen Sie folgende Artikel zum Einrichten des Labs:

- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Festlegen von Kontingenten](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)
