---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 1c1ab85b6da016b966bab7c66cd2a22f257d27d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068391"
---
Wenn Sie dem Dataset neue Dateien hinzufügen möchten, verwenden Sie die inkrementelle Aktualisierung, um Ihrem Projekt diese neuen Dateien hinzuzufügen.   Wenn die **inkrementelle Aktualisierung** aktiviert ist, wird das Dataset auf der Grundlage der Bezeichnungsabschlussrate regelmäßig auf neue Dateien überprüft, die einem Projekt hinzugefügt werden sollen.   Die Überprüfung auf neue Daten wird beendet, wenn das Projekt die Obergrenze von 500.000 Dateien erreicht.

Wenn Sie Ihrem Projekt weitere Dateien hinzufügen möchten, verwenden Sie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/), um sie in den entsprechenden Ordner im Blobspeicher hochzuladen. 

Wählen Sie das Kontrollkästchen **Inkrementelle Aktualisierung aktivieren** aus, wenn das Projekt den Datenspeicher kontinuierlich auf neue Daten überwachen soll. Ist die Option aktiviert, werden einmal pro Tag Daten in Ihr Projekt gepullt. Nachdem Sie dem Datenspeicher neue Daten hinzugefügt haben, müssen Sie warten, bis sie in Ihrem Projekt angezeigt werden.  Im Abschnitt **Inkrementelle Aktualisierung** auf der Registerkarte **Details** können Sie für Ihr Projekt den Zeitstempel für die letzte Aktualisierung der Daten anzeigen.
Deaktivieren Sie diese Option, wenn neue Dateien im Datenspeicher dem Projekt nicht hinzugefügt werden sollen.