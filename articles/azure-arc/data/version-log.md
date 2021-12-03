---
title: Azure Arc-fähige Datendienste – Releaseversionen
description: Ein Versionsprotokoll nach Veröffentlichungsdatum für Azure Arc-fähige Datendienste
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/04/2021
ms.topic: conceptual
ms.openlocfilehash: ca1b31abcfb9985dadda1ece8fced0a9f70c3b04
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893916"
---
# <a name="version-log"></a>Versionsprotokoll

In diesem Artikel werden die in jedem Release von Datendiensten mit Azure Arc-Unterstützung enthaltenen Komponentenversionen beschrieben.

## <a name="november-2-2021"></a>2\. November 2021

In der folgenden Tabelle werden die in diesem Release enthaltenen Komponenten beschrieben.

|Komponente  |Wert  |
|--------------------------------------------------------|---------|
|Tag für Containerimages                                    | v1.1.0_2021-11-02 |
|CRD-Namen und -Versionen                                  | `datacontrollers.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`monitors.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`postgresqls.arcdata.microsoft.com`: v1beta1, v2beta2 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1, v1 <br/>`dags.sql.arcdata.microsoft.com`: v1beta1, v2beta2 |
|ARM-API-Version                                         | 2021-11-01 |
|Version der `arcdata`-Azure CLI-Erweiterung                   | 1.1.0, (Nov 3),</br>1.1.1 (Nov4) |
|Arc-fähige Version der Kubernetes-Helm-Chart-Erweiterung     | 1.0.17551005 – Erforderlich bei einem Upgrade von der GA-Version (allgemeine Verfügbarkeit) <br/><br/> 1.1.17561007 – Ga+1/November-Releasediagramm |
|Arc-Datenerweiterung für Azure Data Studio                | 0.9.7 |

## <a name="august-3-2021"></a>3\. August 2021

Dieses Release aktualisiert die Azure Arc-Erweiterung für Azure Data Studio so, dass sie der GA-Version vom 30. Juli entspricht. In der folgenden Tabelle werden die von diesem Release aktualisierten Komponenten beschrieben. 

|Komponente  |Wert  |
|--------------------------------------------------------|---------|
|Arc-Datenerweiterung für Azure Data Studio                | 0.9.6 |

Alle anderen Komponenten sind mit den zuvor veröffentlichten Komponenten identisch.

## <a name="july-30-2021"></a>30. Juli 2021

Dieses Release führt die allgemeine Verfügbarkeit für universelle SQL Managed Instance mit Azure Arc-Unterstützung und SQL Server mit Azure Arc-Unterstützung ein. In der folgenden Tabelle werden die in diesem Release enthaltenen Komponenten beschrieben.

|Komponente  |Wert  |
|--------------------------------------------------------|---------|
|Tag für Containerimages                                    | v1.0.0_2021-07-30 |
|CRD-Namen und -Versionen                                  |`datacontrollers.arcdata.microsoft.com`: v1beta1, v1 <br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1, v1 <br/>`monitors.arcdata.microsoft.com`: v1beta1, v1 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1, v1 <br/>`postgresqls.arcdata.microsoft.com`: v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1 <br/>`dags.sql.arcdata.microsoft.com`: v1beta1 <br/> |
|ARM-API-Version                                         | 2021-08-01 (stabil) |
|Version der `arcdata`-Azure CLI-Erweiterung                   | 1.0 |
|Arc-fähige Version der Kubernetes-Helm-Chart-Erweiterung     | 1.0.16701001, Release Train: stabil |
|Arc-Datenerweiterung für Azure Data Studio                | 0.9.5 |
