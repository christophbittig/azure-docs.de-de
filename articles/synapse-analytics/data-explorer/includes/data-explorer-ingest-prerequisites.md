---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 153f5c71925e3f4fe1078f014684a12c60a054b1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478787"
---
- Ein Azure-Abonnement. Erstellen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/).

- Erstellen eines Data Explorer-Pools über [Synapse Studio](../data-explorer-create-pool-studio.md) oder [im Azure-Portal](../data-explorer-create-pool-portal.md)
- Erstellen Sie eine Data Explorer-Datenbank.
    1. Wählen Sie in Synapse Studio im linken Bereich **Daten** aus.
    1. Wählen Sie **&plus;** (Neue Ressource hinzufügen) > **Data Explorer-Pool** aus, und verwenden Sie die folgenden Informationen:

        | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
        |--|--|--|
        | Poolname | *contosodataexplorer* | Name des zu verwendende Data Explorer-Pools |
        | Name | *TestDatabase* | Der Datenbankname muss innerhalb des Clusters eindeutig sein. |
        | Standardaufbewahrungszeitraum | *365* | Die Zeitspanne (in Tagen), für die garantiert wird, dass die Daten für Abfragen verfügbar bleiben. Die Zeitspanne wird ab dem Zeitpunkt gemessen, zu dem die Daten erfasst werden. |
        | Standardcachezeitraum | *31* | Die Zeitspanne (in Tagen), wie lange häufig abgefragte Daten im SSD-Speicher oder RAM (und nicht im längerfristigen Speicher) verfügbar bleiben. |

    1. Wählen Sie **Erstellen**, um die Datenbank zu erstellen. Die Erstellung dauert in der Regel weniger als eine Minute.
