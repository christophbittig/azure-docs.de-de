---
title: Gleichzeitiges Arbeiten mit Microsoft Sentinel-Vorfällen in vielen Arbeitsbereichen | Microsoft-Dokumentation
description: Informationen zum gleichzeitigen Anzeigen von Vorfällen in mehreren Arbeitsbereichen in Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 05f0fcf96b9553648830b084d6fa5ec7f00a634d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520463"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>Arbeiten mit Vorfällen in vielen Arbeitsbereichen gleichzeitig 

 [!INCLUDE [Banner for top of topics](./includes/banner.md)]

Um die Funktionen von Microsoft Sentinel in vollem Umfang nutzen zu können, empfiehlt Microsoft die Verwendung einer Umgebung mit einem einzelnen Arbeitsbereich. Es gibt jedoch einige Anwendungsfälle, bei denen in einigen Fällen – (z. B. bei einem [Managed Security Service Provider (MSSP)](./multiple-tenants-service-providers.md) und seinen Kunden – mehrere Arbeitsbereiche mit mehreren Mandanten benötigt werden. Die **Ansicht mit mehreren Arbeitsbereichen** ermöglicht es Ihnen, Sicherheitsvorfälle in mehreren Arbeitsbereichen gleichzeitig anzuzeigen und mit ihnen zu arbeiten, sogar mandantenübergreifend, sodass Sie die volle Transparenz und Kontrolle über die Sicherheitsreaktionsfähigkeit Ihres Unternehmens behalten.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="entering-multiple-workspace-view"></a>Wechseln in die Ansicht mit mehreren Arbeitsbereichen

Wenn Sie Microsoft Sentinel öffnen, sehen Sie eine Liste aller Arbeitsbereiche, für die Sie über Zugriffsrechte verfügen, und zwar über alle ausgewählten Mandanten und Abonnements hinweg. Auf der linken Seite jedes Arbeitsbereichsnamens befindet sich ein Kontrollkästchen. Durch Klicken auf den Namen eines einzelnen Arbeitsbereichs gelangen Sie in diesen Arbeitsbereich. Wenn Sie mehrere Arbeitsbereiche auswählen möchten, aktivieren Sie die entsprechenden Kontrollkästchen, und klicken Sie dann oben auf der Seite auf **Ansicht mit mehreren Arbeitsbereichen**.

> [!IMPORTANT]
> In der Ansicht mit mehreren Arbeitsbereichen werden derzeit maximal 10 gleichzeitig angezeigte Arbeitsbereiche unterstützt. 
> 
> Wenn Sie mehr als 10 Arbeitsbereiche aktivieren, wird eine Warnmeldung angezeigt.

Beachten Sie, dass in der Liste der Arbeitsbereiche das Verzeichnis, das Abonnement, der Standort und die Ressourcengruppe jedes einzelnen Arbeitsbereichs angezeigt werden. Das Verzeichnis entspricht dem Mandanten.

   ![Auswählen mehrerer Arbeitsbereiche](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>Arbeiten mit Vorfällen

In der **Ansicht mit mehreren Arbeitsbereichen** ist zurzeit nur der Bildschirm **Vorfälle** verfügbar. Aussehen und Funktionsweise entsprechen im Großen und Ganzen denen des regulären Bildschirms **Vorfälle**. Es gibt jedoch einige wichtige Unterschiede:

   ![Anzeigen von Vorfällen in mehreren Arbeitsbereichen](./media/multiple-workspace-view/incidents.png)

- Die Indikatoren am oberen Rand der Seite (*Offene Vorfälle*, *Neue Vorfälle*, *In Bearbeitung* usw.) zeigen Sie die Summen für alle ausgewählten Arbeitsbereiche an.

- Es werden Vorfälle aus allen ausgewählten Arbeitsbereichen und Verzeichnissen (Mandanten) in einer einzigen vereinheitlichten Liste angezeigt. Zusätzlich zu den Filtern im regulären **Vorfälle**-Bildschirm können Sie die Liste nach Arbeitsbereich und Verzeichnis filtern.

- Sie müssen über Lese- und Schreibberechtigungen für alle Arbeitsbereiche verfügen, von denen Sie Vorfälle ausgewählt haben. Wenn Sie für einige Arbeitsbereiche nur über Leseberechtigungen verfügen, werden bei der Auswahl von Vorfällen in diesen Arbeitsbereichen Warnmeldungen angezeigt. Sie können diese oder andere gemeinsam ausgewählte Vorfälle nicht ändern (selbst wenn Sie für die anderen Vorfälle über Berechtigungen verfügen).

- Wenn Sie einen einzelnen Vorfall auswählen und auf **Vollständige Details anzeigen** oder **Aktionen** > **Untersuchen** klicken, gelangen Sie in den Datenkontext des Arbeitsbereichs dieses speziellen Vorfalls.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Vorfälle in mehreren Microsoft Sentinel-Arbeitsbereichen gleichzeitig anzeigen und mit diesen arbeiten. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](detect-threats-built-in.md).
