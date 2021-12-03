---
title: Problembehandlung bei Fehlern von Momentaufnahmerichtlinien für Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel werden Fehlermeldungen und Lösungen beschrieben, mit denen Sie Probleme bei der Verwaltung von Momentaufnahmen-Richtlinien für Azure NetApp Files beheben können.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: e6348e28b187b9d09f25eb50264b23aa3a521aae
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224047"
---
# <a name="troubleshoot-snapshot-policy-errors"></a>Behandeln von Fehlern bei Momentaufnahmerichtlinien

In diesem Artikel werden Fehlerszenarios beschrieben, die beim Verwalten von Momentaufnahmen-Richtlinien für Azure NetApp Files auftreten können. Außerdem werden Lösungen bereitstellt, die Ihnen helfen können, die Probleme zu beheben.

## <a name="error-conditions-and-resolutions"></a>Fehlerbedingungen und Auflösungen 

|     Fehlerzustand    |     Lösung    |
|-|-|
| Fehler beim Erstellen der Momentaufnahmen-Richtlinie wg. ungültigen Namens der Momentaufnahmen-Richtlinie. | Bei der Erstellung der Momentaufnahmen-Richtlinie tritt ein Fehler auf, wenn der Name der Momentaufnahmen-Richtlinie ungültig ist. Die folgenden Richtlinien gelten für Namen von Momentaufnahmen-Richtlinien:  <ul><li> Der Name der Momentaufnahmen-Richtlinie darf weder Nicht-ASCII-Zeichen noch Sonderzeichen enthalten. </li> <li> Der Name der Momentaufnahmen-Richtlinie muss mit einem Buchstaben oder einer Ziffer beginnen und darf nur Buchstaben, Ziffern, Unterstriche („_“) und Bindestriche („-“) enthalten. </li> <li> Der Name der Momentaufnahmen-Richtlinie muss zwischen 1 und 64 Zeichen lang sein.  </li></ul> Ändern Sie den Namen der Momentaufnahmen-Richtlinie gemäß den Richtlinien.  |
| Fehler beim Erstellen der Momentaufnahmen-Richtlinie wg. ungültiger Werte. | Azure NetApp Files kann keine Momentaufnahmen-Richtlinie erstellen, wenn Sie einen ungültigen Wert für ein Feld wie `Number of snapshots to keep` oder `Minute on the hour to take snapshot` eingeben. Die folgenden Werte sind gültig:  <ul><li>Bei dem Wert muss es sich um eine gültige Zahl handeln.</li> <li>Der Wert muss zwischen 0 und 59 liegen.</li></ul> Stellen Sie sicher, dass für die Felder ein gültiger Wert angegeben wird. | 
| Fehler `Total number of snapshots to keep exceeds 255` beim Erstellen der Momentaufnahmen-Richtlinie. | Jedes Volume kann [maximal 255 Momentaufnahmen](azure-netapp-files-resource-limits.md) besitzen. Das Maximum umfasst die Summe aller stündlichen, täglichen, wöchentlichen und monatlichen Momentaufnahmen. <br> Verringern Sie den `Snapshots to keep`-Wert, und versuchen Sie es erneut. |
| Beim Zuweisen einer Richtlinie zu einem Volume tritt der Fehler `Total snapshot policy is over the max '255'` auf. | Jedes Volume kann [maximal 255 Momentaufnahmen](azure-netapp-files-resource-limits.md) besitzen. Wenn die Summe aller On-Demand-, stündlichen, täglichen, wöchentlichen und monatlichen Momentaufnahmen den maximalen Wert überschreitet, tritt ein Fehler auf. <br> Verringern Sie den `snapshots to keep`-Wert, oder löschen Sie einige On-Demand-Momentaufnahmen, und versuchen Sie es erneut. | 

## <a name="next-steps"></a>Nächste Schritte  

* [Verwalten von Momentaufnahmenrichtlinien](snapshots-manage-policy.md)
