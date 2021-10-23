---
title: Intelligente Optimierung – Azure Database for PostgreSQL – Flexibler Server
description: In diesem Artikel wird die Funktion zur intelligenten Optimierung in Azure Database for PostgreSQL – Flexibler Server beschrieben.
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: b354394a393907f47b5c29adf668c8d42e059122
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044893"
---
# <a name="perform-intelligent-tuning-in-azure-database-for-postgresql---flexible-server"></a>Durchführen der intelligenten Optimierung in Azure Database for PostgreSQL – Flexibler Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexibler Server und die intelligente Optimierung sind als Vorschauversion verfügbar.

**Anwendungsbereich:** Azure Database for PostgreSQL – Flexibler Server Versionen 11 und höher.

Die Funktion zur intelligenten Optimierung in Azure Database for PostgreSQL – Flexibler Server bietet eine Möglichkeit, die Leistung Ihrer Datenbanken automatisch zu verbessern. Die intelligente Optimierung passt automatisch Ihre Parameter `checkpoint_completion_target`, `min_wal_size` und `bgwriter_delay` an, basierend auf Nutzungsmustern und -werten. Sie fragt alle 30 Minuten Statistiken für Ihre Datenbank ab und nimmt fortlaufend Anpassungen vor, um die Leistung ohne Interaktion zu optimieren.

Die intelligente Optimierung ist ein optionales Feature. Daher ist sie auf einem Server nicht standardmäßig aktiviert. Sie ist für einzelne Datenbanken verfügbar und nicht global. Wenn sie für eine Datenbank aktiviert wird, wird sie nicht für alle verbundenen Datenbanken aktiviert.

## <a name="enable-intelligent-tuning-by-using-the-azure-portal"></a>Aktivieren der intelligenten Optimierung über das Azure-Portal

1. Melden Sie sich beim Azure-Portal an, und wählen Sie Ihren Azure Database for PostgreSQL-Server aus.
2. Wählen Sie im Menü unter **Einstellungen** die Option **Serverparameter**.
3. Suchen Sie nach dem Parameter für die intelligente Optimierung.
4. Legen Sie als Wert **True** fest, und wählen Sie dann **Speichern** aus.

Es kann bis zu 35 Minuten dauern, bis der erste Datenbatch in der *azure_sys*-Datenbank gespeichert ist.

## <a name="information-about-intelligent-tuning"></a>Informationen zur intelligenten Optimierung

Die intelligente Optimierung basiert auf drei Hauptparametern für den angegebenen Zeitpunkt: `checkpoint_completion_target`, `min_wal_size` und `bgwriter_delay`.

Diese drei Parameter wirken sich hauptsächlich auf Folgendes aus: 

* Dauer von Prüfpunkten.
* Häufigkeit von Prüfpunkten.
* Dauer von Synchronisierungen.

Die intelligente Optimierung funktioniert in beide Richtungen. Sie versucht, die Dauer bei hohen Workloads zu verringern und während des Leerlaufs zu erhöhen. Auf diese Weise erzielen Sie in Spitzenzeiten ohne manuelle Updates personalisierte Ergebnisse.

## <a name="limitations-and-known-issues"></a>Einschränkungen und bekannte Probleme

* Die intelligente Optimierung nimmt Optimierungen nur in bestimmten Bereichen vor. Es ist möglich, dass die Funktion nichts ändert.
* Gelöschte Datenbanken in der Abfrage können zu leichten Verzögerungen bei Änderungen und Verbesserungen führen.
* Aktuell führt die Funktion Optimieren nur in den Speicherbereichen durch.
