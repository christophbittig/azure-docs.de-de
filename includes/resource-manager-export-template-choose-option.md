---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/03/2021
ms.author: tomfitz
ms.openlocfilehash: 6a7c9b230ffa42a539dd41cc9301915d7dbe3a34
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479152"
---
## <a name="choose-the-right-export-option"></a>Auswählen der richtigen Exportoption

Eine Vorlage kann auf zwei Arten exportiert werden:

* **Export aus Ressourcengruppe oder Ressource**. Diese Option generiert eine neue Vorlage aus vorhandenen Ressourcen. Die exportierte Vorlage ist eine „Momentaufnahme“ des aktuellen Zustands der Ressourcengruppe. Sie können eine gesamte Ressourcengruppe oder bestimmte Ressourcen innerhalb dieser Ressourcengruppe exportieren.

* **Aus dem Verlauf speichern**. Diese Option ruft eine exakte Kopie einer für die Bereitstellung verwendeten Vorlage ab. Sie geben die Bereitstellung aus dem Bereitstellungsverlauf an. 

Je nach gewählter Option haben die exportierten Vorlagen unterschiedliche Qualitäten.

| Aus Ressourcengruppe oder Ressource | Aus Verlauf |
| --------------------- | ----------------- |
| Die Vorlage wird automatisch generiert. Sie sollten den Code vor der Bereitstellung verbessern. | Die Vorlage ist eine genaue Kopie der vom Vorlagenautor erstellten Kopie. Sie kann ohne Änderungen erneut bereitgestellt werden. |
| Die Vorlage ist eine Momentaufnahme des aktuellen Zustands der Ressourcen. Sie umfasst alle manuellen Änderungen, die Sie nach der Bereitstellung vorgenommen haben. | Die Vorlage zeigt nur den Zustand der Ressourcen zum Zeitpunkt der Bereitstellung an. Alle manuellen Änderungen, die Sie nach der Bereitstellung vorgenommen haben, sind nicht enthalten. |
| Sie können auswählen, welche Ressourcen aus einer Ressourcengruppe exportiert werden sollen. | Alle Ressourcen für eine bestimmte Bereitstellung sind enthalten. Sie können keine Teilmenge dieser Ressourcen auswählen oder Ressourcen hinzufügen, die zu einem anderen Zeitpunkt hinzugefügt wurden. |
| Die Vorlage enthält alle Eigenschaften für die Ressourcen, einschließlich einiger Eigenschaften, die Sie normalerweise während der Bereitstellung nicht festlegen würden. Möglicherweise möchten Sie diese Eigenschaften entfernen oder bereinigen, bevor Sie die Vorlage wiederverwenden. | Die Vorlage enthält nur die für die Bereitstellung erforderlichen Eigenschaften. Die Vorlage ist von geringerer Dichte und einfacher zu lesen. |
| Die Vorlage enthält möglicherweise nicht alle für die Wiederverwendung erforderlichen Parameter. Die meisten Eigenschaftswerte sind in der Vorlage hartcodiert. Um die Vorlage in anderen Umgebungen neu bereitzustellen, müssen Sie Parameter hinzufügen, die die Möglichkeit zur Konfiguration der Ressourcen erhöhen.  Sie können die Auswahl von **Parameter einschließen** auch aufheben, sodass Sie Ihre eigenen Parameter erstellen können. | Die Vorlage enthält Parameter, die es einfach gestalten, sie in verschiedenen Umgebungen neu bereitzustellen. |

Exportieren Sie die Vorlage aus einer Ressourcengruppe oder Ressource, wenn Folgendes zutrifft:

* Sie müssen Änderungen an den Ressourcen erfassen, die nach der ursprünglichen Bereitstellung vorgenommen wurden.
* Sie möchten die zu exportierenden Ressourcen auswählen.
* Die Ressourcen wurden nicht mit einer Vorlage erstellt.

Exportieren Sie die Vorlage für folgende Fälle aus dem Verlauf:

* Sie benötigen eine einfach wiederzuverwendende Vorlage.
* Sie müssen keine Änderungen einbeziehen, die Sie nach der ursprünglichen Bereitstellung vorgenommen haben.
