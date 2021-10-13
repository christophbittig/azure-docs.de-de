---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 2a1c7c65721483e851a7686c38736f0664b965ad
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368027"
---
Wenn Sie dem Dataset neue Dateien hinzufügen möchten, verwenden Sie die inkrementelle Aktualisierung, um Ihrem Projekt diese neuen Dateien hinzuzufügen.   Wenn die **inkrementelle Aktualisierung** aktiviert ist, wird das Dataset auf der Grundlage der Bezeichnungsabschlussrate regelmäßig auf neue Bilder überprüft, die einem Projekt hinzugefügt werden sollen.   Die Überprüfung auf neue Daten wird beendet, wenn das Projekt die Obergrenze von 500.000 Dateien erreicht.

Wenn Sie Ihrem Projekt weitere Dateien hinzufügen möchten, verwenden Sie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/), um sie in den entsprechenden Ordner im Blobspeicher hochzuladen. 

Wählen Sie das Kontrollkästchen **Inkrementelle Aktualisierung aktivieren** aus, wenn das Projekt den Datenspeicher kontinuierlich auf neue Daten überwachen soll. Ist die Option aktiviert, werden einmal pro Tag Daten in Ihr Projekt gepullt. Nachdem Sie dem Datenspeicher neue Daten hinzugefügt haben, müssen Sie warten, bis sie in Ihrem Projekt angezeigt werden.  Im Abschnitt **Inkrementelle Aktualisierung** auf der Registerkarte **Details** können Sie für Ihr Projekt den Zeitstempel für die letzte Aktualisierung der Daten anzeigen.
Deaktivieren Sie diese Option, wenn neue Dateien im Datenspeicher dem Projekt nicht hinzugefügt werden sollen.