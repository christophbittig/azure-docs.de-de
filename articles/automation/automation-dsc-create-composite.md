---
title: Konvertieren von Konfigurationen in zusammengesetzte Ressourcen für Azure Automation State Configuration
description: In diesem Artikel erfahren Sie, wie Sie Konfigurationen in zusammengesetzte Ressourcen für Azure Automation State Configuration konvertieren.
keywords: DSC,PowerShell,Konfiguration,Setup,Einrichtung
services: automation
ms.subservice: dsc
ms.date: 08/08/2019
ms.topic: conceptual
ms.openlocfilehash: 5c3fc5ece7eac812bdf60a310e7777ea411ab6b6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493455"
---
# <a name="convert-configurations-to-composite-resources"></a>Konvertieren von Konfigurationen in zusammengesetzte Ressourcen

> Gilt für: Windows PowerShell 5.1

Nachdem Sie mit dem Erstellen von Konfigurationen begonnen haben, können Sie schnell „Szenarien“ erstellen, mit denen Gruppen von Einstellungen verwaltet werden.
Beispiele wären:

- Erstellen eines Webservers
- Erstellen eines DNS-Servers
- Erstellen eines Servers, auf dem SharePoint ausgeführt wird
- Konfigurieren eines SQL-Clusters
- Verwalten von Firewalleinstellungen
- Verwalten von Kennworteinstellungen

Wenn Sie daran interessiert sind, diese Arbeit mit anderen Personen zu teilen, besteht die beste Möglichkeit darin, die Konfiguration als [zusammengesetzte Ressource](/powershell/scripting/dsc/resources/authoringresourcecomposite)zu packen.
Das erstmalige Erstellen von zusammengesetzten Ressourcen kann überfordernd sein.

> [!NOTE]
> Dieser Artikel bezieht sich auf eine Lösung, die von der Open Source-Community verwaltet wird.
> Support ist nur in Form der GitHub-Zusammenarbeit erhältlich, nicht von Microsoft.

## <a name="community-project-compositeresource"></a>Communityprojekt: CompositeResource

Eine von der Community verwaltete Lösung namens [CompositeResource](https://github.com/microsoft/compositeresource) wurde erstellt, um diese Herausforderung zu bewältigen.

CompositeResource automatisiert den Prozess der Erstellung eines neuen Moduls aus Ihrer Konfiguration.
Sie beginnen damit, dass Sie das Konfigurationsskript auf Ihrer Arbeitsstation (oder dem Buildserver) [dot-sourcen](https://devblogs.microsoft.com/scripting/how-to-reuse-windows-powershell-functions-in-scripts/), damit es in den Arbeitsspeicher geladen wird.
Anstatt die Konfiguration zum Generieren einer MOF-Datei auszuführen, verwenden Sie als Nächstes die vom CompositeResource-Modul bereitgestellte Funktion zum Automatisieren einer Konvertierung.
Das Cmdlet lädt den Inhalt Ihrer Konfiguration, ruft die Liste der Parameter ab und generiert ein neues Modul mit allem benötigten.

Nachdem Sie ein Modul generiert haben, können Sie jedes Mal, wenn Sie Änderungen vornehmen und es in Ihrem eigenen [PowerShellGet-Repository](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo) veröffentlichen, die Version hochzählen und Versionshinweise hinzufügen.

Nachdem Sie ein Modul für zusammengesetzte Ressourcen erstellt haben, das Ihre Konfiguration (oder mehrere Konfigurationen) enthält, können Sie es in der [Funktionalität für das Zusammenstellen einer Konfiguration](./compose-configurationwithcompositeresources.md) in Azure verwenden oder [DSC-Konfigurationsskripts](/powershell/scripting/dsc/configurations/configurations) hinzufügen, um MOF-Dateien zu generieren, und diese [MOF-Dateien in Azure Automation hochladen](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Danach registrieren Sie Ihre Server entweder von [lokal](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) oder [in Azure](./automation-dsc-onboarding.md#enable-azure-vms) um Konfigurationen abzurufen.
Das neueste Update für das Projekt verfügt auch über veröffentlichte [Runbooks](https://www.powershellgallery.com/packages?q=DscGallerySamples) für Azure Automation, um den Prozess des Importierens von Konfigurationen aus dem PowerShell-Katalog zu automatisieren.

Um die Automatisierung der Erstellung von zusammengesetzten Ressourcen zu testen, besuchen Sie den [PowerShell-Katalog](https://www.powershellgallery.com/packages/compositeresource/), und laden Sie die Lösung herunter, oder klicken Sie auf „Projektwebsite“, um die [Dokumentation](https://github.com/microsoft/compositeresource) anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

- Eine Einführung in PowerShell DSC finden Sie unter [Windows PowerShell DSC – Übersicht](/powershell/scripting/dsc/overview/overview).
- Erfahren Sie mehr über PowerShell DSC-Ressourcen in [DSC-Ressourcen](/powershell/scripting/dsc/resources/resources).
- Weitere Informationen zur Konfiguration des lokalen Konfigurations-Managers finden Sie unter [Konfigurieren des lokalen Konfigurations-Managers](/powershell/scripting/dsc/managing-nodes/metaconfig).
