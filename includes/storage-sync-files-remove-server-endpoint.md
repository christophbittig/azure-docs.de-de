---
title: Datei einfügen
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d527af6607dfdaf2cf2b5de7b6adbfed40f74895
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760123"
---
Nein: Das Entfernen eines Serverendpunkts entspricht nicht dem Neustart eines Servers! Das Entfernen und erneute Erstellen des Serverendpunkts ist fast nie die optimale Lösung für das Beheben von Problemen mit der Synchronisierung, dem Cloudtiering oder anderen Aspekten der Azure-Dateisynchronisierung. Das Entfernen eines Serverendpunkts ist ein destruktiver Vorgang. Es kann zu Datenverlust führen, wenn mehrstufige Dateien außerhalb des Namespaces des Serverendpunkts vorhanden sind. Unter [Warum sind mehrstufige Dateien außerhalb des Serverendpunkt-Namespaces vorhanden?](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) finden Sie weitere Informationen. Oder es kann bei mehrstufigen Dateien innerhalb des Namespaces des Serverendpunkts zu nicht zugänglichen Dateien führen. Diese Probleme werden nicht dadurch gelöst, dass der Serverendpunkt neu erstellt wird. Auf Ihrem Serverendpunkt sind möglicherweise auch dann mehrstufige Dateien vorhanden, wenn Sie das Cloudtiering nie aktiviert haben. Aus diesem Grund wird empfohlen, den Serverendpunkt nur zu entfernen, wenn Sie die Azure-Dateisynchronisierung mit diesem spezifischen Ordner nicht mehr verwenden möchten oder von einem Microsoft-Supportmitarbeiter ausdrücklich dazu aufgefordert wurden. Weitere Informationen zum Entfernen von Serverendpunkten finden Sie unter [Entfernen eines Serverendpunkts](../articles/storage/file-sync/file-sync-server-endpoint-delete.md).    
