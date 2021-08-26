---
title: Tutorial zur Zugriffs- und Anwendungssteuerung in Azure Security Center
description: Dieses Tutorial zeigt, wie Sie eine Richtlinie für den Just-in-Time-VM-Zugriff und eine Anwendungssteuerungsrichtlinie konfigurieren.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 399fa371de57bbd8899a7c22686196a0a54be0ee
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726112"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Tutorial: Schützen Ihrer Ressourcen mit Azure Security Center
Security Center verringert Ihre Gefährdung durch Bedrohungen, indem mithilfe von Zugriffs- und Anwendungssteuerungen böswillige Aktivitäten blockiert werden. Durch den JIT-Zugriff (Just-in-Time) auf einen virtuellen Computer wird die Anfälligkeit für Angriffe verringert, da Sie den dauerhaften Zugriff auf virtuelle Computer verweigern können. Stattdessen bieten Sie einen gesteuerten und überwachten Zugriff auf VMs nur bei Bedarf. Adaptive Anwendungssteuerungen helfen dabei, VMs gegen Schadsoftware abzusichern, indem sie steuern, welche Anwendungen auf Ihren VMs ausgeführt werden können. Security Center nutzt maschinelles Lernen, um die auf dem virtuellen Computer ausgeführten Prozesse zu analysieren, und unterstützt Sie beim Anwenden von Positivlistenregeln unter Verwendung dieser Daten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren einer Richtlinie für den Just-in-Time-VM-Zugriff
> * Konfigurieren einer Anwendungssteuerungsrichtlinie

## <a name="prerequisites"></a>Voraussetzungen
Zum Durchlaufen der in diesem Tutorial behandelten Features muss Azure Defender aktiviert sein. Eine kostenlose Testversion ist verfügbar. Informationen zum Durchführen eines Upgrades finden Sie unter [Aktivieren von Azure Defender](enable-azure-defender.md).

## <a name="manage-vm-access"></a>Verwalten des VM-Zugriffs
Mit JIT Zugriff auf einen virtuellen Computer kann eingehender Datenverkehr auf den virtuellen Azure-Computern gesperrt werden, um die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einen einfachen Zugriff auf Verbindungen mit virtuellen Computern bereitzustellen.

Verwaltungsports müssen nicht jederzeit geöffnet sein. Sie müssen nur geöffnet sein, während eine Verbindung mit dem virtuellen Computer besteht, z.B. zum Ausführen von Verwaltungs- oder Wartungsaufgaben. Wenn Just-In-Time aktiviert ist, verwendet Security Center NSG-Regeln (Netzwerksicherheitsgruppen), die den Zugriff auf Verwaltungsports beschränken, um sie vor Angriffen zu schützen.

Folgen Sie dem Leitfaden unter [Sichern Ihrer Verwaltungsports mit Just-in-Time-Zugriff (JIT)](security-center-just-in-time.md).

## <a name="harden-vms-against-malware"></a>Absichern von VMs gegen Schadsoftware
Mit adaptiven Anwendungssteuerungen können Sie eine Gruppe von Anwendungen definieren, deren Ausführung in konfigurierten Ressourcengruppen zulässig ist. Dadurch können Sie Ihre VMs gegen Schadsoftware absichern. Security Center nutzt maschinelles Lernen, um die auf dem virtuellen Computer ausgeführten Prozesse zu analysieren, und unterstützt Sie beim Anwenden von Positivlistenregeln unter Verwendung dieser Daten.

Folgen Sie dem Leitfaden unter [Verwenden von adaptiven Anwendungssteuerungen zum Verringern der Angriffsfläche Ihres Computers](security-center-adaptive-application.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie erfahren, wie Sie Ihre Angriffsfläche für Bedrohungen wie folgt verringern:

> [!div class="checklist"]
> * Konfigurieren einer Richtlinie für den Just-in-Time-VM-Zugriff, um einen gesteuerten und überwachten Zugriff auf VMs nur bei Bedarf zu ermöglichen
> * Konfigurieren einer adaptiven Anwendungssteuerung, um festzulegen, welche Anwendungen auf Ihren VMs ausgeführt werden können

Im nächsten Tutorial erfahren Sie, wie Sie auf Sicherheitsvorfälle reagieren.

> [!div class="nextstepaction"]
> [Tutorial: Reagieren auf Sicherheitsvorfälle](tutorial-security-incident.md)