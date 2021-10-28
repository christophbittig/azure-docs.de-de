---
title: 'Problembehandlung von Azure Virtual Desktop: Übersicht – Azure'
description: Eine Übersicht zur Problembehandlung bei der Einrichtung einer Azure Virtual Desktop-Umgebung.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 435f26a7fba7939ffc7ac370c648f6aae0d7a67f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234035"
---
# <a name="troubleshooting-overview-feedback-and-support-for-azure-virtual-desktop"></a>Problembehandlung von Übersicht, Feedback und Support für Azure Virtual Desktop

>[!IMPORTANT]
>Dieser Inhalt gilt für Azure Virtual Desktop mit Azure Virtual Desktop-Objekten für Azure Resource Manager. Wenn Sie Azure Virtual Desktop (klassisch) ohne Azure Resource Manager-Objekte verwenden, finden Sie weitere Informationen in [diesem Artikel](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).

Dieser Artikel gibt einen Überblick über die Probleme, die bei der Einrichtung einer Azure Virtual Desktop-Umgebung auftreten können, und beschreibt Möglichkeiten, diese Probleme zu lösen.

## <a name="troubleshoot-deployment-and-connection-issues"></a>Behandeln von Bereitstellungs- und Verbindungsproblemen

[Azure Monitor für Windows Virtual Desktop](azure-monitor.md) ist ein Dashboard, das auf Azure Monitor-Arbeitsmappen basiert und Probleme in Ihrer Windows Virtual Desktop-Umgebung für Sie schnell beheben und identifizieren kann. Wenn Sie lieber mit Kusto-Abfragen arbeiten, empfiehlt es sich, stattdessen das integrierte Diagnosefeature [Log Analytics](diagnostics-log-analytics.md) zu verwenden.

## <a name="report-issues"></a>Melden von Problemen

Besuchen Sie die [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum), um Probleme zu melden oder Features für Azure Virtual Desktop mit Azure Resource Manager-Integration vorzuschlagen. Außerdem können Sie in der Tech Community bewährte Methoden diskutieren oder neue Features vorschlagen sowie für diese abstimmen.

Wenn Sie einen Beitrag posten, in dem Sie um Hilfe bitten oder ein neues Feature vorschlagen, sollten Sie Ihr Anliegen so detailliert wie möglich beschreiben. Dies hilft anderen Benutzern dabei, Ihre Fragen zu beantworten oder sich ein genaueres Bild von dem vorgeschlagenen Feature zu machen.

## <a name="escalation-tracks"></a>Eskalationspfade

Als Erstes sollten Sie die [Azure-Statusseite](https://status.azure.com/status) und [Azure Service Health](https://azure.microsoft.com/features/service-health/) prüfen, um sich zu vergewissern, dass Ihr Azure-Dienst ordnungsgemäß funktioniert.

Orientieren Sie sich an der folgenden Tabelle, um Probleme zu identifizieren und zu beheben, die beim Einrichten einer Umgebung mit dem Remotedesktopclient auftreten können. Nachdem Sie Ihre Umgebung eingerichtet haben, können Sie den neuen [Diagnosedienst]() verwenden, um Probleme in häufigen Szenarien zu identifizieren.

| **Problem**                                                            | **Vorgeschlagene Lösung**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Einstellungen für den Sitzungshostpool von Azure Virtual Network (VNET) und Express Route               | [Öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/), und wählen Sie den entsprechenden Dienst (in der Kategorie „Netzwerk“) aus. |
| Sitzungshostpool-VM-Erstellung, wenn keine mit Azure Virtual Desktop bereitgestellten Azure Resource Manager-Vorlagen verwendet werden | [Erstellen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/), und wählen Sie als Dienst **Azure Virtual Desktop** aus. <br> <br> Informationen zu Problemen mit den Vorlagen von Azure Resource Manager, die mit Azure Virtual Desktop bereitgestellt werden, finden Sie im Abschnitt „Fehler mit der Azure Resource Manager-Vorlage“ der Seite [Hostpoolerstellung](troubleshoot-set-up-issues.md). |
| Verwalten der Azure Virtual Desktop-Sitzungshostumgebung über das Azure-Portal    | [Öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/). <br> <br> Informationen zu Verwaltungsproblemen bei Verwendung von Remotedesktopdiensten/Azure Virtual Desktop PowerShell finden Sie unter [Azure Virtual Desktop PowerShell](troubleshoot-powershell.md). Alternativ [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/), und wählen Sie **Azure Virtual Desktop** als Dienst und **Konfiguration und Verwaltung** als Problemtyp aus. Wählen Sie anschließend **Probleme beim Konfigurieren einer Umgebung über PowerShell** als Untertyp des Problems aus. |
| Verwalten einer Azure Virtual Desktop-Konfiguration, die an Hostpools und Anwendungsgruppen (App-Gruppen) gebunden ist      | Weitere Informationen finden Sie unter [Azure Virtual Desktop PowerShell](troubleshoot-powershell.md). Alternativ [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/), und wählen Sie **Azure Virtual Desktop** als Dienst sowie den jeweiligen Problemtyp aus.|
| Bereitstellen und Verwalten von FSLogix-Profilcontainern | Weitere Informationen finden Sie unter [Leitfaden zur Problembehandlung für FSLogix-Produkte](/fslogix/fslogix-trouble-shooting-ht/). Wenn sich das Problem dadurch nicht beheben lässt, [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/), und wählen Sie **Azure Virtual Desktop** als Dienst und **FSLogix** als Problemtyp aus. Wählen Sie anschließend den entsprechenden Untertyp des Problems aus. |
| Fehlfunktion von Remotedesktopclients beim Start                                                 | Weitere Informationen finden Sie unter [Problembehandlung beim Remotedesktopclient](troubleshoot-client.md). Wenn sich das Problem dadurch nicht beheben lässt, [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/), und wählen Sie **Azure Virtual Desktop** als Dienst und **Remotedesktopclients** als Problemtyp aus.  <br> <br> Wenn es sich um ein Netzwerkproblem handelt, müssen Ihre Benutzer sich an ihren Netzwerkadministrator wenden. |
| Verbunden, aber kein Feed                                                                 | Führen Sie die Problembehandlung anhand der im Artikel [Azure Virtual Desktop-Dienstverbindungen](troubleshoot-service-connection.md) im Abschnitt [Der Benutzer stellt eine Verbindung her, aber es wird nichts angezeigt (kein Feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) beschriebenen Schritte aus. <br> <br> Wenn Ihre Benutzer einer App-Gruppe zugewiesen wurden, [öffnen Sie eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/) und wählen Sie **Azure Virtual Desktop** als Dienst und **Remotedesktopclients** als Problemtyp aus. |
| Feedermittlungsprobleme aufgrund des Netzwerks                                            | Ihre Benutzer müssen sich an ihren Netzwerkadministrator wenden. |
| Verbinden von Clients                                                                    | Weitere Informationen finden Sie unter [Azure Virtual Desktop-Dienstverbindungen](troubleshoot-service-connection.md). Wenn das Ihr Problem nicht löst, finden Sie weitere Informationen unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md). |
| Reaktionsfähigkeit von Remoteanwendungen oder Desktop                                      | Wenn Probleme mit einer bestimmten Anwendung oder einem Produkt verknüpft sind, wenden Sie sich an das Team, das für das betreffende Produkt zuständig ist. |
| Lizenzierungsmeldungen oder -fehler                                                          | Wenn Probleme mit einer bestimmten Anwendung oder einem Produkt verknüpft sind, wenden Sie sich an das Team, das für das betreffende Produkt zuständig ist. |
| Probleme mit Authentifizierungsmethoden oder -tools von Drittanbietern | Überprüfen Sie, ob Ihr Drittanbieter Azure Virtual Desktop-Szenarios unterstützt, und kontaktieren Sie ihn bezüglich bekannter Probleme. |
| Probleme beim Verwenden von Log Analytics für Azure Virtual Desktop | Erstellen Sie bei Problemen mit dem Diagnoseschema [eine Azure-Supportanfrage](https://azure.microsoft.com/support/create-ticket/).<br><br>Wählen Sie für Abfragen, Visualisierungen oder andere Probleme in Log Analytics unter „Log Analytics“ den entsprechenden Problemtyp aus. |
| Probleme bei Microsoft 365-Apps | Wenden Sie sich an das Microsoft 365 Admin Center mit einer der [Hilfeoptionen des Microsoft 365 Admin Centers](/microsoft-365/admin/contact-support-for-business-products/). |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Problembehandlung beim Erstellen eines Hostpools in einer Azure Virtual Desktop-Umgebung finden Sie unter [Hostpoolerstellung](troubleshoot-set-up-issues.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Azure Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md).
- Informationen zur Problembehandlung beim Azure Virtual Desktop-Agent oder der Sitzungskonnektivität finden Sie unter [Beheben häufiger Probleme mit dem Azure Virtual Desktop-Agent](troubleshoot-agent.md).
- Informationen zur Behebung von Problemen bei Azure Virtual Desktop-Clientverbindungen finden Sie unter [Azure Virtual Desktop – Clientverbindungen](troubleshoot-service-connection.md).
- Informationen zur Behebung von Problemen bei Remotedesktop-Clients finden Sie unter [Problembehandlung für den Remotedesktop-Client](troubleshoot-client.md).
- Informationen zur Problembehandlung bei der Verwendung von PowerShell mit Azure Virtual Desktop finden Sie unter [Azure Virtual Desktop – PowerShell](troubleshoot-powershell.md).
- Weitere Informationen zum Dienst finden Sie unter [Azure Virtual Desktop-Umgebung](environment-setup.md).
- Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung von Bereitstellungen der Resource Manager-Vorlage](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Informationen zur Überwachung von Aktionen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](../azure-monitor/essentials/activity-log.md).
- Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen](../azure-resource-manager/templates/deployment-history.md).