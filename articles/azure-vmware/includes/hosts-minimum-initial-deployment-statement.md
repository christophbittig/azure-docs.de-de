---
title: Minimale anfängliche Hostbereitstellung
description: Die Bereitstellung muss mindestens drei Hosts umfassen.
ms.topic: include
ms.date: 09/29/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 56a2f12ae7dddc6e0783d715554934d48dd227b1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300407"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-clouds-clusters.md -->

Für jede erstellte private Cloud gibt es standardmäßig ein vSAN-Cluster. Sie können Cluster hinzufügen, löschen und skalieren.  Die Mindestanzahl von Hosts pro Cluster und bei der anfänglichen Bereitstellung beträgt drei. 

Sie verwenden vSphere und NSX-T Manager, um die meisten weiteren Aspekte der Clusterkonfiguration oder des Clusterbetriebs zu verwalten. Der gesamte lokale Speicher jedes Hosts in einem Cluster befindet sich unter der Kontrolle von vSAN.

>[!TIP]
>Sie können den Cluster später jederzeit erweitern und zusätzliche Cluster hinzufügen, wenn Sie die anfängliche Bereitstellungsanzahl erhöhen möchten.
