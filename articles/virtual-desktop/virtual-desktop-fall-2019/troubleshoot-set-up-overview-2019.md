---
title: 'Problembehandlung von Azure Virtual Desktop (klassisch): Übersicht – Azure'
description: Eine Übersicht zur Problembehandlung bei der Einrichtung einer Azure Virtual Desktop (klassisch)-Mandantenumgebung.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5c55dac88810531af849cbacb129197eaddf1a08
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774593"
---
#  <a name="azure-virtual-desktop-classic-troubleshooting-overview-feedback-and-support"></a>Problembehandlung von Azure Virtual Desktop (klassisch): Übersicht, Feedback und Support

>[!IMPORTANT]
>Dieser Inhalt gilt für Azure Virtual Desktop (klassisch). Der Dienst unterstützt keine Azure Virtual Desktop-Objekte in Azure Resource Manager. Wenn Sie Azure Virtual Desktop-Objekte in Azure Resource Manager verwalten möchten, helfen Ihnen die Informationen in [diesem Artikel](../troubleshoot-set-up-overview.md) weiter.

Dieser Artikel gibt einen Überblick über die Probleme, die bei der Einrichtung einer Azure Virtual Desktop-Mandantenumgebung auftreten können, und beschreibt Möglichkeiten, diese Probleme zu lösen.

## <a name="provide-feedback"></a>Feedback geben

In der [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) können Sie sich mit dem Produktteam und aktiven Communitymitgliedern über den Azure Virtual Desktop-Dienst austauschen.

## <a name="escalation-tracks"></a>Eskalationspfade

Verwenden Sie die folgende Tabelle, um Probleme zu identifizieren und zu beheben, die beim Einrichten einer Mandantenumgebung mit dem Remotedesktopclient auftreten können. Nachdem Sie Ihren Mandanten eingerichtet haben, können Sie den neuen [Diagnosedienst](diagnostics-role-service-2019.md) verwenden, um Probleme für häufige Szenarien zu identifizieren.

>[!NOTE]
> Wir betreiben ein Tech Community-Forum, das Sie besuchen können, um Probleme mit dem Produktteam und aktiven Communitymitgliedern zu diskutieren. Besuchen Sie die [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), um eine Diskussion zu beginnen.

| **Problem**                                                            | **Vorgeschlagene Lösung**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Erstellen eines Azure Virtual Desktop-Mandanten                                                    | Bei einem Azure-Ausfall [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/). Anderenfalls [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/) und wählen **Azure Virtual Desktop** als Dienst und **Bereitstellung** als Problemtyp aus. Anschließend wählen Sie als Untertyp des Problems **Probleme beim Erstellen eines Azure Virtual Desktop-Mandanten** aus.|
| Zugreifen auf Marketplace-Vorlagen im Azure-Portal       | Bei einem Azure-Ausfall [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/). <br> <br> Azure Virtual Desktop-Vorlagen sind im Azure Marketplace kostenlos verfügbar.|
| Zugreifen auf Azure Resource Manager-Vorlagen aus GitHub                                  | Weitere Informationen finden Sie unter [Mandanten- und Hostpoolerstellung](troubleshoot-set-up-issues-2019.md) im Abschnitt [Erstellen von Azure Virtual Desktop-Sitzungshost-VMs](troubleshoot-set-up-issues-2019.md#creating-azure-virtual-desktop-session-host-vms). Wenn das Problem immer noch nicht gelöst ist, wenden Sie sich an das [GitHub-Supportteam](https://github.com/contact). <br> <br> Wenn der Fehler nach dem Zugriff auf die Vorlage in GitHub auftritt, wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/create-ticket/).|
| Einstellungen für den Sitzungshostpool von Azure Virtual Network (VNET) und Express Route               | [Öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/), und wählen Sie den entsprechenden Dienst (in der Kategorie „Netzwerk“) aus. |
| Sitzungshostpool-VM-Erstellung, wenn keine mit Azure Virtual Desktop bereitgestellten Azure Resource Manager-Vorlagen verwendet werden | [Öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/), und wählen Sie als Dienst **VM unter Windows** aus. <br> <br> Informationen zu Problemen mit den Vorlagen von Azure Resource Manager, die mit Azure Virtual Desktop bereitgestellt werden, finden Sie im Abschnitt „Erstellen eines Azure Virtual Desktop-Mandanten“ unter [Mandanten- und Hostpoolerstellung](troubleshoot-set-up-issues-2019.md). |
| Verwalten der Azure Virtual Desktop-Sitzungshostumgebung über das Azure-Portal    | [Öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/). <br> <br> Informationen zu Verwaltungsproblemen bei Verwendung von Remotedesktopdiensten/Azure Virtual Desktop PowerShell finden Sie unter [Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md). Alternativ [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/), und wählen Sie **Azure Virtual Desktop** als Dienst und **Konfiguration und Verwaltung** als Problemtyp aus. Wählen Sie anschließend **Probleme beim Konfigurieren eines Mandanten über PowerShell** als Untertyp des Problems aus. |
| Verwalten einer Azure Virtual Desktop-Konfiguration, die an Hostpools und Anwendungsgruppen (App-Gruppen) gebunden ist      | Weitere Informationen finden Sie unter [Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md). Alternativ [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/), und wählen Sie **Azure Virtual Desktop** als Dienst sowie den jeweiligen Problemtyp aus.|
| Bereitstellen und Verwalten von FSLogix-Profilcontainern | Weitere Informationen finden Sie unter [Leitfaden zur Problembehandlung für FSLogix-Produkte](/fslogix/fslogix-trouble-shooting-ht/). Wenn sich das Problem dadurch nicht beheben lässt, [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/), und wählen Sie **Azure Virtual Desktop** als Dienst und **FSLogix** als Problemtyp aus. Wählen Sie anschließend den entsprechenden Untertyp des Problems aus. |
| Fehlfunktion von Remotedesktopclients beim Start                                                 | Weitere Informationen finden Sie unter [Problembehandlung beim Remotedesktopclient](../troubleshoot-client.md). Wenn sich das Problem dadurch nicht beheben lässt, [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/), und wählen Sie **Azure Virtual Desktop** als Dienst und **Remotedesktopclients** als Problemtyp aus.  <br> <br> Wenn es sich um ein Netzwerkproblem handelt, müssen Ihre Benutzer sich an ihren Netzwerkadministrator wenden. |
| Verbunden, aber kein Feed                                                                 | Führen Sie die Problembehandlung anhand der im Artikel [Azure Virtual Desktop-Dienstverbindungen](troubleshoot-service-connection-2019.md) im Abschnitt [Der Benutzer stellt eine Verbindung her, aber es wird nichts angezeigt (kein Feed)](troubleshoot-service-connection-2019.md#user-connects-but-nothing-is-displayed-no-feed) beschriebenen Schritte aus. <br> <br> Wenn Ihre Benutzer einer App-Gruppe zugewiesen wurden, [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/) und wählen Sie **Azure Virtual Desktop** als Dienst und **Remotedesktopclients** als Problemtyp aus. |
| Feedermittlungsprobleme aufgrund des Netzwerks                                            | Ihre Benutzer müssen sich an ihren Netzwerkadministrator wenden. |
| Verbinden von Clients                                                                    | Weitere Informationen finden Sie unter [Azure Virtual Desktop-Dienstverbindungen](troubleshoot-service-connection-2019.md). Wenn das Ihr Problem nicht löst, finden Sie weitere Informationen unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration-2019.md). |
| Reaktionsfähigkeit von Remoteanwendungen oder Desktop                                      | Wenn Probleme mit einer bestimmten Anwendung oder einem Produkt verknüpft sind, wenden Sie sich an das Team, das für das betreffende Produkt zuständig ist. |
| Lizenzierungsmeldungen oder -fehler                                                          | Wenn Probleme mit einer bestimmten Anwendung oder einem Produkt verknüpft sind, wenden Sie sich an das Team, das für das betreffende Produkt zuständig ist. |
| Probleme bei der Verwendung von Azure Virtual Desktop-Tools auf GitHub (Azure Resource Manager-Vorlagen, Diagnosetool, Verwaltungstool) | Informationen zum Melden von Problemen finden Sie unter [Azure Resource Manager-Vorlagen für Remotedesktopdienste](https://github.com/Azure/RDS-Templates/blob/master/README.md). |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Problembehandlung beim Erstellen eines Mandanten- und Hostpools in einer Azure Virtual Desktop-Umgebung finden Sie unter [Mandanten- und Hostpoolerstellung](troubleshoot-set-up-issues-2019.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Azure Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration-2019.md).
- Informationen zur Behebung von Problemen bei Azure Virtual Desktop-Clientverbindungen finden Sie unter [Azure Virtual Desktop – Clientverbindungen](troubleshoot-service-connection-2019.md).
- Informationen zur Behebung von Problemen bei Remotedesktop-Clients finden Sie unter [Problembehandlung für den Remotedesktop-Client](../troubleshoot-client.md).
- Informationen zur Problembehandlung bei der Verwendung von PowerShell mit Azure Virtual Desktop finden Sie unter [Azure Virtual Desktop – PowerShell](troubleshoot-powershell-2019.md).
- Weitere Informationen zum Dienst finden Sie unter [Azure Virtual Desktop-Umgebung](environment-setup-2019.md).
- Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung von Bereitstellungen der Resource Manager-Vorlage](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Informationen zur Überwachung von Aktionen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](../../azure-monitor/essentials/activity-log.md).
- Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen](../../azure-resource-manager/templates/deployment-history.md).
