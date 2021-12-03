---
title: 'Body Tracking in Azure Kinect: Gelenke'
description: Hier finden Sie Informationen zu Körperframes, Gelenken, Gelenkkoordinaten und der Gelenkhierarchie in Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, portieren, Körper, Body Tracking, Gelenk, Hierarchie, Knochen, Verbindung
ms.openlocfilehash: 2616573d0474addb14abe5f6e932181b097bed46
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223448"
---
# <a name="azure-kinect-body-tracking-joints"></a>Body Tracking in Azure Kinect: Gelenke

Die Body Tracking-Features in Azure Kinect können mehrere menschliche Körper gleichzeitig nachverfolgen. Jedem Körper ist eine ID zur temporalen Korrelation zwischen dem menschlichen und dem kinematischen Skelett zugeordnet. Die Anzahl von Körpern, die in jedem Frame erkannt werden, können mithilfe von `k4abt_frame_get_num_bodies()` abgerufen werden.

## <a name="joints"></a>Gelenke

Die Position und Orientierung von Gelenken werden relativ zum globalen Tiefensensor-Referenzframe geschätzt. Die Position wird in Millimetern angegeben. Die Orientierung wird als normalisierte Quaternion ausgedrückt.

## <a name="joint-coordinates"></a>Gelenkkoordinaten

Position und Orientierung jedes Gelenks bilden ein eigenes rechtshändiges Gelenkkoordinatensystem. Alle Gelenkkoordinatensysteme sind absolute Koordinatensysteme im 3D-Koordinatensystem der Tiefenkamera.

> [!NOTE]
> Die Auswahl der Ausrichtung der gespiegelten Achsen für die entsprechenden Gelenke auf den beiden Seiten des Körpers soll die Spiegelbewegung vereinfachen, z. B. beide Arme um +20° heben, was bei kommerziellen Avataren, Game-Engines und Renderingsoftware üblich ist.

![Gelenkkoordinaten](./media/concepts/joint-coordinates.png)

Legende: | x-Achse = rot | y-Achse = grün | z-Achse = blau |

## <a name="joint-hierarchy"></a>Gelenkhierarchie

Ein Skelett umfasst 32 Gelenke, deren Hierarchie von der Körpermitte zu den Extremitäten verläuft. Jede Verbindung (Knochen) verbindet das übergeordnete Gelenk mit einem untergeordneten Gelenk. Die Abbildung veranschaulicht die Positionen der Gelenke und deren Verbindung im menschlichen Körper.

![Gelenkhierarchie](./media/concepts/joint-hierarchy.png)

In der folgenden Tabelle sind die standardmäßigen Gelenkverbindungen aufgelistet.

|Index |Name des Gelenks     | Übergeordnetes Gelenk   |
|------|---------------|----------------|
| 0    |PELVIS (BECKEN)         | -              |
| 1    |SPINE_NAVEL (LENDENWIRBELSÄULE)    | PELVIS (BECKEN)         |
| 2    |SPINE_CHEST (BRUSTWIRBELSÄULE)    | SPINE_NAVEL (LENDENWIRBELSÄULE)    |
| 3    |NECK (HALS)           | SPINE_CHEST (BRUSTWIRBELSÄULE)    |
| 4    |CLAVICLE_LEFT (SCHLÜSSELBEIN_LINKS)  | SPINE_CHEST (BRUSTWIRBELSÄULE)    |
| 5    |SHOULDER_LEFT (SCHULTER_LINKS)  | CLAVICLE_LEFT (SCHLÜSSELBEIN_LINKS)  |
| 6    |ELBOW_LEFT (ELLENBOGEN_LINKS)     | SHOULDER_LEFT (SCHULTER_LINKS)  |
| 7    |WRIST_LEFT (HANDGELENK_LINKS)     | ELBOW_LEFT (ELLENBOGEN_LINKS)     |
| 8    |HAND_LEFT (HAND_LINKS)      | WRIST_LEFT (HANDGELENK_LINKS)     |
| 9    |HANDTIP_LEFT (FINGERSPITZE_LINKS)   | HAND_LEFT (HAND_LINKS)      |
| 10   |THUMB_LEFT (DAUMEN_LINKS)     | WRIST_LEFT (HANDGELENK_LINKS)     |
| 11   |CLAVICLE_RIGHT (SCHLÜSSELBEIN_RECHTS) | SPINE_CHEST (BRUSTWIRBELSÄULE)    |
| 12   |SHOULDER_RIGHT (SCHULTER_RECHTS) | CLAVICLE_RIGHT (SCHLÜSSELBEIN_RECHTS) |
| 13   |ELBOW_RIGHT (ELLENBOGEN_RECHTS)    | SHOULDER_RIGHT (SCHULTER_RECHTS) |
| 14   |WRIST_RIGHT (HANDGELENK_RECHTS)    | ELBOW_RIGHT (ELLENBOGEN_RECHTS)    |
| 15   |HAND_RIGHT (HAND_RECHTS)     | WRIST_RIGHT (HANDGELENK_RECHTS)    |
| 16   |HANDTIP_RIGHT (FINGERSPITZE_RECHTS)  | HAND_RIGHT (HAND_RECHTS)     |
| 17   |THUMB_RIGHT (DAUMEN_RECHTS)    | WRIST_RIGHT (HANDGELENK_RECHTS)    |
| 18   |HIP_LEFT (HÜFTE_LINKS)       | PELVIS (BECKEN)         |
| 19   |KNEE_LEFT (KNIE_LINKS)      | HIP_LEFT (HÜFTE_LINKS)       |
| 20   |ANKLE_LEFT (KNÖCHEL_LINKS)     | KNEE_LEFT (KNIE_LINKS)      |
| 21   |FOOT_LEFT (FUSS_LINKS)      | ANKLE_LEFT (KNÖCHEL_LINKS)     |
| 22   |HIP_RIGHT (HÜFTE_RECHTS)      | PELVIS (BECKEN)         |
| 23   |KNEE_RIGHT (KNIE_RECHTS)     | HIP_RIGHT (HÜFTE_RECHTS)      |
| 24   |ANKLE_RIGHT (KNÖCHEL_RECHTS)    | KNEE_RIGHT (KNIE_RECHTS)     |
| 25   |FOOT_RIGHT (FUSS_RECHTS)     | ANKLE_RIGHT (KNÖCHEL_RECHTS)    |
| 26   |HEAD           | NECK (HALS)           |
| 27   |NOSE (NASE)           | HEAD           |
| 28   |EYE_LEFT (AUGE_LINKS)       | HEAD           |
| 29   |EAR_LEFT (OHR_LINKS)       | HEAD           |
| 30   |EYE_RIGHT (AUGE_RECHTS)      | HEAD           |
| 31   |EAR_RIGHT (OHR_RECHTS)      | HEAD           |

## <a name="next-steps"></a>Nächste Schritte

[Body Tracking-Indexzuordnung](body-index-map.md)
