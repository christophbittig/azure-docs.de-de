---
title: 'Verwalten der Aufzeichnungsrichtlinie mit Live Video Analytics: Azure'
description: In diesem Thema wird erklärt, wie Sie die Aufzeichnungsrichtlinie mit Live Video Analytics verwalten.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: a8301b97e571370d498fba9a8d46cf3fc545ff29
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740363"
---
# <a name="manage-recording-policy-with-live-video-analytics"></a>Verwalten der Aufzeichnungsrichtlinie mit Live Video Analytics

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

Sie können Live Video Analytics in IoT Edge für die [fortlaufende Videoaufzeichnung](continuous-video-recording-concept.md) verwenden. Die Videoaufzeichnungen können Sie für Wochen oder Monate in der Cloud speichern. Sie können die Dauer (in Tagen) dieses Cloudarchivs mithilfe der in Azure Storage integrierten [Tools zur Lebenszyklusverwaltung](../../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal) verwalten.  

Ihr Media Service-Konto ist mit einem Azure Storage-Konto verknüpft. Wenn Sie Videos in der Cloud aufzeichnen, wird der Inhalt in ein [Medienobjekt](../latest/assets-concept.md) in Media Services geschrieben. Jedes Medienobjekt wird einem Container im Speicherkonto zugeordnet. Mithilfe der Lebenszyklusverwaltung können Sie eine [Richtlinie](../../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal) für ein Speicherkonto definieren, in der Sie eine [Regel](../../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal#lifecycle-management-rule-definition) wie die folgende angeben können.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

Für die obige Regel gilt Folgendes:

* Sie gilt für alle Blockblobs im Speicherkonto.
* Sie gibt an, dass die Blobs, die älter als 30 Tage sind, von der [heißen in die kalte Speicherebene](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal) verschoben werden.
* Wenn Blobs älter als 90 Tage sind, müssen sie gelöscht werden.

Wenn Sie Live Video Analytics für die Aufzeichnung in einem Medienobjekt verwenden, geben Sie eine `segmentLength`-Eigenschaft an, die das Modul anweist, eine Mindestdauer des Videos (in Sekunden) zu aggregieren, ehe es in die Cloud geschrieben wird. Ihr Medienobjekt enthält eine Reihe von Segmenten, die jeweils einen Erstellungszeitstempel haben, der um `segmentLength` neuer ist als der vorherige. Wenn die Richtlinie für die Lebenszyklusverwaltung greift, werden Segmente gelöscht, die älter als der angegebene Schwellenwert sind. Sie können jedoch weiterhin über Media Service-APIs auf die restlichen Segmente zugreifen und diese wiedergeben. Weitere Informationen finden Sie unter [Wiedergeben von Aufzeichnungen](playback-recordings-how-to.md). 

## <a name="limitations"></a>Einschränkungen

Im Folgenden finden Sie einige bekannte Einschränkungen bei der Lebenszyklusverwaltung:

* In der Richtlinie können maximal 100 Regeln festgelegt werden, und in jeder Regel können bis zu 10 Container angegeben werden. Wenn Sie also unterschiedliche Aufzeichnungsrichtlinien benötigen (z. B. ein Archiv von 3 Tagen für die Kamera auf dem Parkplatz, eines von 30 Tagen für die Kamera im Ladebereich und eines von 180 Tagen für die Kamera hinter der Kasse), können Sie mit einem Media Services-Konto die Regeln für maximal 1.000 Kameras anpassen.
* Updates für die Richtlinie zur Lebenszyklusverwaltung werden nicht sofort angewandt. Weitere Einzelheiten finden Sie in [diesem Abschnitt der häufig gestellten Fragen (FAQ)](../../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal#faq).
* Wenn Sie eine Richtlinie anwenden, mit der Blobs auf die kalte Speicherebene verschoben werden, hat dies möglicherweise Einfluss auf die Wiedergabe dieses Teils des Archivs. Es können zusätzliche Wartezeiten oder sporadische Fehler auftreten. Die Wiedergabe von Inhalten auf Archivspeicherebene wird von Media Services nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

[Wiedergeben von Aufzeichnungen](playback-recordings-how-to.md)