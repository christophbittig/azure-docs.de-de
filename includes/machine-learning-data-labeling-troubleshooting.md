---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 8122a9c001ce56edefaa0215fc20fe183d2cd00c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368030"
---
Verwenden Sie diese Tipps, wenn Sie eines der folgenden Probleme feststellen.

|Problem  |Lösung  |
|---------|---------|
|Es können nur Datasets verwendet werden, die für Blobdatenspeicher erstellt wurden.     |  Dies ist eine bekannte Einschränkung beim aktuellen Release.       |
|Nach der Erstellung wird für das Projekt lange der Status „Wird initialisiert“ angezeigt.     | Aktualisieren Sie die Seite manuell. Die Initialisierung sollte mit ungefähr 20 Datenpunkten pro Sekunde abgeschlossen werden. Die fehlende automatische Aktualisierung ist ein bekanntes Problem.         |
|Neu bezeichnete Elemente, die in der Datenüberprüfung nicht sichtbar sind.     |   Wählen Sie die Schaltfläche **Erste** aus, um alle bezeichneten Elemente zu laden. Mit der Schaltfläche **Erste** gelangen Sie zurück an den Anfang der Liste, aber es werden alle bezeichneten Daten geladen.      |
|Das Zuweisen einer Gruppe von Aufgaben zu einem bestimmten Beschriftungsersteller ist nicht möglich.     |   Dies ist eine bekannte Einschränkung beim aktuellen Release.  |