---
title: Azure Arc-fähige Datendienste – Releaseversionen
description: Ein Versionsprotokoll nach Veröffentlichungsdatum für Azure Arc-fähige Datendienste
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 08/06/2021
ms.topic: conceptual
ms.openlocfilehash: 3e636fe5ff6477f78f09d940b70099b8cbf41fb9
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349931"
---
# <a name="version-log"></a>Versionsprotokoll

In der folgenden Tabelle werden die verschiedenen Versionen im zeitlichen Verlauf beschrieben:

|Date|Dieser Parameter gibt den Releasenamen an.|Tag für Containerimages|CRD-Präfixe und -Versionen|ARM-API-Version|Version der `arcdata`-Azure CLI-Erweiterung|Arc-fähige Version der Kubernetes-Helm-Chart-Erweiterung|Arc-Datenerweiterung für Azure Data Studio|
|---|---|---|---|---|---|---|---|
|30. Juli 2021|SQL Managed Instance mit Azure Arc-Unterstützung (universell) und SQL Server mit Arc-Unterstützung (allgemein verfügbar)|v1.0.0_2021-07-30|`datacontrollers`: v1beta1, v1 <br/>`exporttasks.tasks`: v1beta1, v1 <br/>`monitors`: v1beta1, v1 <br/>`sqlmanagedinstances.sql`: v1beta1, v1 <br/>`postgresqls`: v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql`: v1beta1 <br/>`dags.sql`: v1beta1 <br/>|2021-08-01 (stabil)|1.0|1.0.16701001, Release Train: stabil|0.9.5|
|3\. August 2021|Update auf Azure Arc-Erweiterung für Azure Data Studio zum Abgleich mit der GA-Version vom 30. Juli|Keine Änderung|Keine Änderung|Keine Änderung|Keine Änderung|Keine Änderung|0.9.6|

Fügen Sie `.arcdata.microsoft.com` an das Präfix an, um eine vollständige CRD zu erhalten. 
