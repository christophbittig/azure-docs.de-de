---
title: Behandeln von Bereitstellungsproblemen bei Cloud Services (klassisch) | Microsoft-Dokumentation
description: Bei der Bereitstellung eines Clouddiensts in Azure können einige allgemeine Probleme auftreten. Dieser Artikel bietet Lösungen für einige dieser Probleme.
ms.topic: troubleshooting
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b8610917a365297d1e2f17f40f2096839eb39f89
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824893"
---
# <a name="troubleshoot-azure-cloud-services-classic-deployment-problems"></a>Behandeln von Bereitstellungsproblemen mit Azure Cloud Services (klassisch)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Wenn Sie ein Anwendungspaket für einen Clouddienst in Azure bereitstellen, können Sie im Bereich **Eigenschaften** des Azure-Portals Informationen zu der Bereitstellung abrufen. Mit den Informationen in diesem Bereich können Sie Probleme mit dem Clouddienst behandeln, und Sie können dem Azure-Support diese Informationen bereitstellen, wenn Sie eine neue Supportanfrage eröffnen.

Sie finden den Bereich **Eigenschaften** wie folgt:

* Im Azure-Portal: Klicken Sie auf die Bereitstellung Ihres Clouddiensts, klicken Sie auf **Alle Einstellungen**, und klicken Sie dann auf **Eigenschaften**.

> [!NOTE]
> Sie können den Inhalt des Bereichs **Eigenschaften** in die Zwischenablage kopieren, indem Sie auf das Symbol in der oberen rechten Ecke des Bereichs klicken.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problem: Ich kann nicht auf meine Website zugreifen, aber die Bereitstellung wurde gestartet, und alle Rolleninstanzen sind bereit.
Die im Portal angezeigte Website-URL enthält den Port nicht. Der Standardport für Websites lautet 80. Falls Ihre Anwendung so konfiguriert ist, dass sie einen anderen Port verwendet, müssen Sie der URL die richtige Portnummer hinzufügen, wenn Sie auf die Website zugreifen.

1. Klicken Sie im Azure-Portal auf die Bereitstellung Ihres Clouddiensts.
2. Überprüfen Sie im Bereich **Eigenschaften** des Azure-Portals die Ports für die Rolleninstanzen (unter **Eingabeendpunkte**).
3. Falls der Port nicht 80 lautet, fügen Sie der URL den richtigen Portwert hinzu, wenn Sie auf die Anwendung zugreifen. Um einen anderen Port als den Standardport anzugeben, geben Sie die URL gefolgt von einem Doppelpunkt (:) und der Portnummer ohne Leerzeichen ein.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problem: Meine Rolleninstanzen wurden wiederverwendet, ohne dass ich dies veranlasst habe.
Die Dienstreparatur wird automatisch ausgeführt, wenn Azure Problemknoten erkennt und Rolleninstanzen deshalb auf neue Knoten verschiebt. In diesem Fall werden die Rolleninstanzen möglicherweise automatisch wiederverwendet (recycelt). So finden Sie heraus, ob eine Dienstreparatur ausgeführt wurde:

1. Klicken Sie im Azure-Portal auf die Bereitstellung Ihres Clouddiensts.
2. Überprüfen Sie die Informationen im Bereich **Eigenschaften** des Azure-Portals, und stellen Sie fest, ob während der Zeitraums, in dem Sie die zyklische Ausführung von Rollen beobachtet haben, eine Dienstreparatur durchgeführt wurde.

Rollen werden darüber hinaus etwa einmal pro Monat im Rahmen von Upgrades der Host- und Gastbetriebssysteme wiederverwendet.  
Weitere Informationen finden Sie im Blogbeitrag [Role Instance Restarts Due to OS Upgrades](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problem: Ich kann keinen VIP-Austausch durchführen und erhalte einen Fehler.
Ein VIP-Austausch ist nicht zulässig, während ein Bereitstellungsupdate ausgeführt wird. In folgenden Fällen können Bereitstellungsupdates automatisch erfolgen:

* Es steht ein neues Gastbetriebssystem zur Verfügung, und Ihr System ist für automatische Updates konfiguriert
* Es findet eine Dienstreparatur statt

So finden Sie heraus, ob ein automatisches Update den VIP-Austausch verhindert

1. Klicken Sie im Azure-Portal auf die Bereitstellung Ihres Clouddiensts.
2. Sehen Sie sich im Bereich **Eigenschaften** des Azure-Portals den Wert von **Status** an. Wenn er **Bereit** lautet, überprüfen Sie unter **Letzter Vorgang**, ob vor Kurzem ein Vorgang ausgeführt wurde, der möglicherweise den VIP-Swap verhindert.
3. Wiederholen Sie die Schritte 1 und 2 für die Produktionsbereitstellung.
4. Wenn ein automatisches Update ausgeführt wird, warten Sie, bis der Vorgang beendet ist, bevor Sie erneut versuchen, den VIP-Austausch durchzuführen.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problem: Eine Rolleninstanz befindet sich in einer Schleife zwischen „Gestartet“, „Wird initialisiert“, „Ausgelastet“ und „Beendet“.
Diese Bedingung kann auf ein Problem mit dem Anwendungscode, dem Anwendungspaket oder der Konfigurationsdatei hinweisen. In diesem Fall sehen Sie, dass sich der Status alle paar Minuten ändert und im Azure-Portal Meldungen wie **Recycling**, **Ausgelastet** oder **Wird initialisiert** angezeigt werden. Dies weist darauf hin, dass ein Fehler in der Anwendung vorliegt, der verhindert, dass die Rolleninstanz ausgeführt wird.

Weitere Informationen zum Behandeln dieses Problems finden Sie im Blogbeitrag [Azure PaaS Compute Diagnostics Data](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data) (Azure-PaaS-Compute-Diagnosedaten) und unter [Allgemeine Probleme, durch die Rollen zyklisch ausgeführt werden](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problem: Meine Anwendung funktioniert nicht mehr.
1. Klicken Sie im Azure-Portal auf die Rolleninstanz.
2. Sehen Sie sich im Bereich **Eigenschaften** des Azure-Portals die folgenden Bedingungen an, die Ihnen bei der Problembehebung helfen können:
   * Wenn die Rolleninstanz vor Kurzem beendet wurde (Sie können den Wert unter **Anzahl abgebrochener Nachrichten** überprüfen), wird möglicherweise die Bereitstellung aktualisiert. Warten Sie, ob die Rolleninstanz ihre Funktionsweise selbstständig wieder aufnimmt.
   * Wenn die Rolleninstanz **ausgelastet** ist, überprüfen Sie den Anwendungscode, um festzustellen, ob das [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) -Ereignis verarbeitet wird. Möglicherweise müssen Sie Code hinzufügen oder korrigieren, der dieses Ereignis verarbeitet.
   * Weitere Informationen erhalten Sie in den Diagnosedaten und Problembehandlungsszenarios im Blogbeitrag [Azure PaaS Compute Diagnostics Data](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)(in englischer Sprache).

> [!WARNING]
> Wenn Sie Ihren Clouddienst wiederverwenden, setzen Sie die Eigenschaften für die Bereitstellung zurück und löschen so praktisch die Informationen für das ursprüngliche Problem.
>
>

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich weitere [Artikel zur Problembehandlung](./cloud-services-allocation-failures.md) für Clouddienste an.

Erfahren Sie in der [Blogreihe von Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data), wie Sie Probleme bei Clouddienstrollen mit den Compute-Diagnosedaten von Azure-PaaS beheben.