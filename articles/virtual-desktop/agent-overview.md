---
title: Erste Schritte mit dem Azure Virtual Desktop-Agent
description: Hier erhalten Sie eine Übersicht über den Azure Virtual Desktop-Agent und dessen Updateprozesse.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: ae07694e72f910945fbb213a448bfd910fef9786
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132483850"
---
# <a name="get-started-with-the-azure-virtual-desktop-agent"></a>Erste Schritte mit dem Azure Virtual Desktop-Agent

Das Azure Virtual Desktop-Dienstframework enthält drei Hauptkomponenten: den Remotedesktopclient, den Dienst und die virtuellen Computer. Diese virtuellen Computer (Virtual Machines, VMs) befinden sich in dem Kundenabonnement, in dem der Azure Virtual Desktop-Agent und der Bootloader des Agents installiert sind. Der Agent dient zur Kommunikation zwischen dem Dienst und den VMs und ermöglicht so die Konnektivität. Wenn Probleme mit der Installation, dem Update oder der Konfiguration des Agents auftreten, können Ihre VMs daher keine Verbindung mit dem Dienst herstellen. Der Bootloader des Agents ist die ausführbare Datei, die den Agent lädt. 

In diesem Artikel erhalten Sie eine kurze Übersicht über die Installations- und Updateprozesse für den Agent.

>[!NOTE]
>Diese Dokumentation gilt nicht für den FSLogix-Agent oder den Remotedesktopclient-Agent.


## <a name="initial-installation-process"></a>Erstinstallation

Für die Erstinstallation des Azure Virtual Desktop-Agents gibt es zwei Möglichkeiten. Wenn Sie VMs (virtual Machines, virtuelle Computer) über das Azure-Portal oder den Azure Marketplace bereitstellen, werden der Agent und der zugehörige Bootloader automatisch installiert. Wenn Sie VMs mithilfe von PowerShell bereitstellen, müssen Sie die MSI-Dateien für den Agent und den Bootloader des Agents manuell herunterladen, wenn Sie [mithilfe von PowerShell einen Hostpool für Azure Virtual Desktop erstellen](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool). Sobald der Agent installiert ist, werden auch der parallele Stapel für Azure Virtual Desktop und der Überwachungs-Agent von Geneva installiert. Die Komponente für den parallelen Stapel ist erforderlich, damit Benutzer umgekehrte Verbindungen vom Server zum Client sicher einrichten können. Der Überwachungs-Agent von Geneva überwacht die Integrität des Agents. Alle drei Komponenten sind für die ordnungsgemäße Funktion der End-to-End-Benutzerkonnektivität von entscheidender Bedeutung.

>[!IMPORTANT]
>Für eine erfolgreiche Installation des Azure Virtual Desktop-Agents, des parallelen Stapels und des Überwachungs-Agents von Geneva müssen Sie die Blockierung aller URLs in der [Liste der erforderlichen URLs](safe-url-list.md#virtual-machines) aufheben. Das Aufheben der Blockierung dieser URLs ist eine Voraussetzung für die Verwendung des Azure Virtual Desktop-Diensts.

## <a name="agent-update-process"></a>Updateprozess für den Agent

Der Agent wird durch den Azure Virtual Desktop-Dienst aktualisiert, sobald ein Update verfügbar ist. Agent-Updates können neue Funktionen enthalten oder bestehende Probleme beheben. Es muss immer die neueste stabile Version des Agents installiert sein, damit die Konnektivität bzw. Sicherheit Ihrer VMs nicht verloren geht. Sobald die ursprüngliche Version des Azure Virtual Desktop-Agents installiert ist, fragt der Agent den Azure Virtual Desktop-Dienst regelmäßig nach neueren Versionen des Agents, des Stapels oder der Überwachungskomponente ab. Wenn bereits eine neuere Version einer der Komponenten bereitgestellt wurde, wird die aktualisierte Komponente automatisch vom Flight-System installiert.

Neue Agent-Versionen werden in regelmäßigen Abständen von fünf Tagen für alle Azure-Abonnements bereitgestellt. Diese Aktualisierungszeiträume werden als „Flights“ bezeichnet. Es dauert 24 Stunden, bis alle VMs in einer einzelnen Brokerregion das Agent-Update eines Flights erhalten haben. Daher erhalten bei einem Flight die VMs in Ihrem Hostpool das Agent-Update möglicherweise zu unterschiedlichen Zeiten. Auch wenn sich die VMs in unterschiedlichen Regionen befinden, werden sie möglicherweise an unterschiedlichen Tagen innerhalb des Zeitraums von fünf Tagen aktualisiert. Alle VM-Agents in allen Abonnements werden bis zum Ende des Bereitstellungszeitraums durch den Flight aktualisiert. Die Zuverlässigkeit des Dienst wird durch das Flight-System von Azure Virtual Desktop erhöht, da es die Stabilität und Qualität der Agent-Updates sicherstellt.


Weitere wichtige Punkte, die Sie beachten sollten:

- Das Agent-Update ist nicht mit den Buildupdates der Azure Virtual Desktop-Infrastruktur verbunden. Daher bedeutet eine Aktualisierung der Azure Virtual Desktop-Infrastruktur nicht, dass auch der Agent aktualisiert wurde.
- Da virtuelle Computer in Ihrem Hostpool ihre Agent-Updates zu unterschiedlichen Zeiten erhalten können, müssen Sie in der Lage sein, den Unterschied zwischen den Flightproblemen und den fehlgeschlagenen Agent-Updates zu erkennen. Wenn Sie die Ereignisprotokolle für Ihren virtuellen Computer unter **Ereignisanzeige** > **Windows-Protokoll** >  **-Anwendung** aufrufen und ein Ereignis mit der Bezeichnung „ID 3277“ anzeigen, funktioniert das Agent-Update nicht. Wenn dieses Ereignis nicht angezeigt wird, befindet sich der virtuelle Computer in einem anderen Flight und wird später aktualisiert.
- Nachdem der Überwachungs-Agent von Geneva auf die neueste Version aktualisiert wurde, wird zunächst der alte Task „GenevaTask“ ermittelt und deaktiviert und danach ein neuer Task für den neuen Überwachungs-Agent erstellt. Die frühere Version des Überwachungs-Agents wird nicht gelöscht, damit bei einem Fehler in der aktuellsten Version des Überwachungs-Agents eine Wiederherstellung der früheren Version durchgeführt werden kann. Wenn bei der neuesten Version ein Problem auftritt, wird der alte Überwachungs-Agent erneut aktiviert, damit weiterhin Überwachungsdaten bereitgestellt werden. Alle Versionen des Überwachungs-Agent, die älter sind als die letzte Version vor dem Update, werden von Ihrer VM gelöscht.
- Auf Ihrem VM werden drei Versionen des Agents und des nebeneinander ausgeführten Stapels gleichzeitig verwaltet. Dies ermöglicht eine schnelle Wiederherstellung, wenn beim Update Fehler auftreten. Die älteste Version des Agents oder Stapels wird immer dann vom virtuellen Computer entfernt, wenn der Agent oder der Stapel aktualisiert wird. Wenn Sie diese Komponenten vorzeitig löschen und der Agent oder Stapel einen Fehler auflistet, kann der Agent oder Stapel kein Rollback auf eine frühere Version ausführen, wodurch Ihr virtueller Computer in einen nicht verfügbaren Zustand gerät.

Das Agent-Update dauert auf einer neueren VM normalerweise 2-3 Minuten und sollte nicht dazu führen, dass die Verbindung der VM getrennt oder die VM selbst heruntergefahren wird. Dieser Updateprozess gilt sowohl für Azure Virtual Desktop (klassisch) als auch für die neueste Version von Azure Virtual Desktop mit Azure Resource Manager.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun den Azure Virtual Desktop-Agent kennengelernt haben, können Sie mit den folgenden Ressourcen fortfahren:

- Bei Problemen mit dem Agent oder der Konnektivität finden Sie hilfreiche Informationen unter [Behandeln häufiger Probleme im Zusammenhang mit dem Azure Virtual Desktop-Agent](troubleshoot-agent.md).
