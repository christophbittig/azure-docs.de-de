---
title: 'Tutorial zu Zugriffs- und Anwendungssteuerungen: Microsoft Defender für Cloud'
description: Dieses Tutorial zeigt, wie Sie eine Richtlinie für den Just-in-Time-VM-Zugriff und eine Anwendungssteuerungsrichtlinie konfigurieren.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: e8656730d0edf3fe08a217b5acd5c94d886dadea
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475253"
---
# <a name="tutorial-protect-your-resources-with-microsoft-defender-for-cloud"></a>Tutorial: Schützen Ihrer Ressourcen mit Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender für Cloud verringert Ihre Gefährdung durch Bedrohungen, indem böswillige Aktivitäten mithilfe von Zugriffs- und Anwendungssteuerungen blockiert werden. Durch den JIT-Zugriff (Just-in-Time) auf einen virtuellen Computer wird die Anfälligkeit für Angriffe verringert, da Sie den dauerhaften Zugriff auf virtuelle Computer verweigern können. Stattdessen bieten Sie einen gesteuerten und überwachten Zugriff auf VMs nur bei Bedarf. Adaptive Anwendungssteuerungen helfen dabei, VMs gegen Schadsoftware abzusichern, indem sie steuern, welche Anwendungen auf Ihren VMs ausgeführt werden können. Defender für Cloud nutzt maschinelles Lernen, um die auf dem virtuellen Computer ausgeführten Prozesse zu analysieren, und unterstützt Sie beim Anwenden von Positivlistenregeln unter Verwendung dieser Daten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren einer Richtlinie für den Just-in-Time-VM-Zugriff
> * Konfigurieren einer Anwendungssteuerungsrichtlinie

## <a name="prerequisites"></a>Voraussetzungen
Um die in diesem Tutorial behandelten Features kennenzulernen, müssen Sie die erweiterten Sicherheitsfeatures von Defender für Cloud aktiviert haben. Eine kostenlose Testversion ist verfügbar. Informationen zum Upgrade finden Sie unter [Aktivieren des erhöhten Schutzes](enable-enhanced-security.md).

## <a name="manage-vm-access"></a>Verwalten des VM-Zugriffs
Mit JIT Zugriff auf einen virtuellen Computer kann eingehender Datenverkehr auf den virtuellen Azure-Computern gesperrt werden, um die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einen einfachen Zugriff auf Verbindungen mit virtuellen Computern bereitzustellen.

Verwaltungsports müssen nicht jederzeit geöffnet sein. Sie müssen nur geöffnet sein, während eine Verbindung mit dem virtuellen Computer besteht, z.B. zum Ausführen von Verwaltungs- oder Wartungsaufgaben. Wenn Just-In-Time aktiviert ist, verwendet Defender für Cloud NSG-Regeln (Netzwerksicherheitsgruppen), die den Zugriff auf Verwaltungsports beschränken, um sie vor Angriffen zu schützen.

Folgen Sie dem Leitfaden unter [Sichern Ihrer Verwaltungsports mit Just-in-Time-Zugriff (JIT)](just-in-time-access-usage.md).

## <a name="harden-vms-against-malware"></a>Absichern von VMs gegen Schadsoftware
Mit adaptiven Anwendungssteuerungen können Sie eine Gruppe von Anwendungen definieren, deren Ausführung in konfigurierten Ressourcengruppen zulässig ist. Dadurch können Sie Ihre VMs gegen Schadsoftware absichern. Defender für Cloud nutzt maschinelles Lernen, um die auf dem virtuellen Computer ausgeführten Prozesse zu analysieren, und unterstützt Sie beim Anwenden von Positivlistenregeln unter Verwendung dieser Daten.

Folgen Sie dem Leitfaden unter [Verwenden von adaptiven Anwendungssteuerungen zum Verringern der Angriffsfläche Ihres Computers](adaptive-application-controls.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie erfahren, wie Sie Ihre Angriffsfläche für Bedrohungen wie folgt verringern:

> [!div class="checklist"]
> * Konfigurieren einer Richtlinie für den Just-in-Time-VM-Zugriff, um einen gesteuerten und überwachten Zugriff auf VMs nur bei Bedarf zu ermöglichen
> * Konfigurieren einer adaptiven Anwendungssteuerung, um festzulegen, welche Anwendungen auf Ihren VMs ausgeführt werden können

Im nächsten Tutorial erfahren Sie, wie Sie auf Sicherheitsvorfälle reagieren.

> [!div class="nextstepaction"]
> [Tutorial: Reagieren auf Sicherheitsvorfälle](tutorial-security-incident.md)