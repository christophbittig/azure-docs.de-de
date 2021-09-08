---
title: 'Azure Percept: Bekannte Probleme'
description: Enthält Informationen zu den bekannten Problemen für Azure Percept und den Problemumgehungen.
author: juniem
ms.author: amiyouss
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: 4d6bbc2396819b1eea7ac17f5c0da87055450927
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223952"
---
# <a name="azure-percept-known-issues"></a>Azure Percept: Bekannte Probleme

Dies sind Probleme mit Azure Percept DK, Azure Percept Audio oder Azure Percept Studio, die den Produktteams bekannt sind. Problemumgehungen und Problembehandlungsschritte werden nach Möglichkeit bereitgestellt. Wenn Sie von einem dieser Probleme betroffen sind, können Sie eine Frage an [Microsoft Q&A](/answers/topics/azure-percept.html) posten oder eine Kundensupportanfrage im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) stellen. 

|Bereich|Symptome|Beschreibung des Problems|Problemumgehung|
|-------|---------|---------|---------|
| Azure Percept DK | Die Beispiel- und Demomodelle können nicht in Azure Percept Studio bereitgestellt werden | Manchmal endet die Ausführung des Moduls „azureeyemodule“ oder „azureearspeechmodule“. edgeAgent-Protokolle zeigen den Fehler „zu viele Ebenen symbolischer Verknüpfungen“ an. | Setzen Sie Ihr Gerät durch [Aktualisieren über USB](./how-to-update-via-usb.md) zurück. |
| Lokalisierung | Nicht englischsprachigen Benutzern werden möglicherweise Teile des Azure Percept DK-Setups mit englischem Text angezeigt. | Die Benutzeroberfläche des Azure Percept DK-Setups ist nicht vollständig lokalisiert. | Die Korrektur ist für Juli 2021 geplant.  |
| Azure Percept DK | Auf einem Mac wird die Setup-Benutzeroberfläche plötzlich nach dem Herstellen einer WLAN-Verbindung geschlossen. | In der Setup-Benutzeroberfläche auf einem Mac wird anfänglich ein Fenster anstelle eines Webbrowsers geöffnet. Das Fenster wird nicht beibehalten, wenn die Verbindung vom Zugriffspunkt des Geräts zum WLAN wechselt. | Öffnen Sie einen Webbrowser, und wechseln Sie zu https://10.1.1.1, damit Sie die Einrichtungserfahrung abschließen können. |
| Azure Percept DK | Das Dev Kit führt ein benutzerdefiniertes Modell aus, und nach dem Neustart des Dev Kits wird das Standardbeispielmodell ausgeführt. | Der Modulzwillingscontainer für das benutzerdefinierte Modell bleibt bei Geräteneustarts nicht erhalten. Nach dem Neustart muss der Modulzwilling für das benutzerdefinierte Modul neu erstellt werden, was 5 Minuten oder länger dauern kann. Das Dev Kit führt das Standardmodell aus, bis dieser Prozess abgeschlossen ist. | Nach einem Neustart müssen Sie warten, bis der benutzerdefinierte Modulzwilling neu erstellt ist. |