---
title: 'Azure CLI-Beispiele: Azure Database for MySQL Flexible Server'
description: Dieser Artikel führt die Azure CLI-Codebeispiele auf, die für die Interaktion mit Azure Database for MySQL – Flexible Server verfügbar sind.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 8ae33838b4dff052006f1124eea3dbec59cc7666
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844267"
---
# <a name="azure-cli-samples-for-azure-database-for-mysql---flexible-server"></a>Azure CLI-Beispiele für Azure Database for MySQL – Flexible Server

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Die folgende Tabelle enthält Links zu Azure CLI-Beispielskripts für Azure Database for MySQL – Flexible Server.

| Beispiellink | BESCHREIBUNG  |
|---|---|
|**Erstellen und Herstellen einer Verbindung mit einem Server**||
| [Erstellen eines Servers und Aktivieren der Konnektivität für den öffentlichen Zugriff](scripts/sample-cli-create-connect-public-access.md) | Erstellt eine Instanz von Azure Database for MySQL – Flexibler Server, konfiguriert eine Firewallregel auf Serverebene (Konnektivitätsmethode für den öffentlichen Zugriff) und stellt eine Verbindung mit dem Server her. |
| [Erstellen eines Servers und Aktivieren der Konnektivität für den privaten Zugriff (VNet-Integration)](scripts/sample-cli-create-connect-private-access.md) | Erstellt eine Instanz von Azure Database for MySQL – Flexibler Server in einem VNet (Konnektivitätsmethode für den privaten Zugriff) und stellt eine Verbindung mit dem Server über eine VM innerhalb des virtuellen Netzwerks her. |
|**Überwachen und Skalieren**||
| [Überwachen von Metriken und Skalieren eines Servers](scripts/sample-cli-monitor-and-scale.md) | Überwacht eine einzelne Instanz von Azure DB for MySQL – Flexible Server und skaliert sie hoch oder herunter, um wechselnden Leistungsanforderungen gerecht zu werden. |
|**Sichern und Wiederherstellen**||
| [Wiederherstellen eines Servers](scripts/sample-cli-restore-server.md) | Stellt eine einzelne Instanz von Azure DB for MySQL – Flexible Server zu einem früheren Zeitpunkt wieder her. |
|**Hochverfügbarkeit**||
| [Konfigurieren der zonenredundanten Hochverfügbarkeit](scripts/sample-cli-zone-redundant-ha.md) | Ermöglicht zonenredundante Hochverfügbarkeit beim Erstellen einer Instanz von Azure DB for MySQL – Flexible Server.|
| [Konfigurieren von Hochverfügbarkeit in gleicher Zone](scripts/sample-cli-same-zone-ha.md) | Ermöglicht Hochverfügbarkeit in gleicher Zone beim Erstellen einer Instanz von Azure DB for MySQL – Flexible Server.|
|**Verwalten des Servers**||
| [Erneutes Starten, Beenden, Starten eines Servers](scripts/sample-cli-restart-stop-start.md)| Führt Vorgänge zum erneuten Starten, Beenden und Starten für eine einzelne Instanz von Azure DB for MySQL – Flexible Server durch. |
| [Ändern von Serverparametern](scripts/sample-cli-change-server-parameters.md) | Ändert die Serverparameter einer einzelnen Instanz von Azure DB for MySQL – Flexible Server. |
|**Replikation**||
| [Erstellen von Lesereplikaten](scripts/sample-cli-read-replicas.md) | Erstellt und verwaltet Lesereplikate in einer einzelnen Instanz von Azure DB for MySQL – Flexible Server. |
|**Konfigurieren von Protokollen**||
| [Konfigurieren von Überwachungsprotokollen](scripts/sample-cli-audit-logs.md) | Konfiguriert Überwachungsprotokolle für eine einzelne Instanz von Azure DB for MySQL – Flexible Server. |
| [Konfigurieren des Protokolls für langsame Abfragen](scripts/sample-cli-slow-query-logs.md) | Konfiguriert Protokolle für langsame Abfragen für eine einzelne Instanz von Azure DB for MySQL – Flexible Server. |

