---
title: Intelligente Optimierung – Azure Database for PostgreSQL – Flexibler Server
description: In diesem Artikel werden die Funktionen zur intelligenten Optimierung in Azure Database for PostgreSQL – Flexibler Server beschrieben.
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: d16a1842c4dfed044a8ac865a3dcdcd6cbe05bf3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339963"
---
# <a name="perform-intelligent-tuning-on-your-postgresql-flex-server"></a>Ausführen der intelligenten Optimierung bei PostgreSQL – Flexibler Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

> [!IMPORTANT]
> Die intelligente Optimierung befindet sich in der Vorschauphase.

**Anwendungsbereich:** Azure Database for PostgreSQL – Flex Server-Versionen 11 und höher

Die Funktionen zur intelligenten Optimierung in Azure Database for PostgreSQL – Flexibler Server bieten eine Möglichkeit, die Leistung Ihrer Datenbanken automatisch zu verbessern. Mit unserem Dienst passt die intelligente Optimierung Ihre Werte für Checkpoint_completion_target, min_wal_size und max_wal_size automatisch basierend auf Verwendungsmustern und -werten an. Der Dienst fragt alle 30 Minuten Statistiken für Ihre Datenbank ab und nimmt fortlaufend Anpassungen vor, um die Leistung ohne Interaktion von Benutzern zu optimieren.

## <a name="enabling-intelligent-tuning"></a>Aktivieren der intelligenten Optimierung

Die intelligente Optimierung ist ein optionales Feature. Daher ist es auf einem Server nicht standardmäßig aktiviert. Die Optimierung ist für einzelne Datenbanken verfügbar und nicht global. Wenn Sie sie also für eine Datenbank aktivieren, wird sie nicht für alle verbundenen Datenbanken aktiviert.

### <a name="enable-intelligent-tuning-using-the-azure-portal"></a>Aktivieren der intelligenten Optimierung über das Azure-Portal

1. Melden Sie sich beim Azure-Portal an, und wählen Sie Ihren Azure Database for PostgreSQL-Server aus.
2. Wählen Sie im Bereich Einstellungen im Menü die Option Serverparameter.
3. Suchen Sie nach dem Parameter für die intelligente Optimierung.
4. Legen Sie für den Wert auf TRUE oder FALSE fest, und speichern Sie ihn.

Es kann bis zu 35 Minuten dauern, bis der erste Datenbatch in der azure_sys-Datenbank gespeichert ist.

## <a name="information-about-intelligent-tuning"></a>Informationen zur intelligenten Optimierung

Die intelligente Optimierung basiert auf drei Hauptfunktionen für den angegebenen Zeitpunkt.

* Checkpoint_completion_target
* Min_wal_size
* Max_wal_size

Diese drei Variablen wirken sich hauptsächlich auf Folgendes aus:

* Dauer von Prüfpunkten
* Häufigkeit von Prüfpunkten
* Dauer von Synchronisierungen

Die intelligente Optimierung funktioniert in beide Richtungen. Sie versucht, die Dauer bei hohen Workloads zu verringern und während des Leerlaufs zu erhöhen. Mit diesen Regeln wird versucht, diese Features zu optimieren, damit Benutzer in Spitzenzeiten ohne manuelle Updates personalisierte Ergebnisse erzielen können.

## <a name="limitations-and-known-issues"></a>Einschränkungen und bekannte Probleme

* Optimierungen werden nur in bestimmten Bereichen ausgegeben. Es besteht auch die Möglichkeit, dass keine Änderungen vorgenommen werden.
* Die intelligente Optimierung kann durch gelöschte Datenbanken in der Abfrage verzögert werden. Dies kann zu geringfügigen Verzögerungen bei der Ausführung von Verbesserungen führen.
  
Optimierungen werden derzeit nur in den Speicherbereichen vorgenommen. Das Erweitern auf andere Bereiche ist in Arbeit.
