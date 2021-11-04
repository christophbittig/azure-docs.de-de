---
title: 'Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Zusammenfassung'
description: In diesem Dokument wurden mehrere Themen im Zusammenhang mit der Migration einer Anwendung von einer lokalen MySQL-Instanz zu Azure Database for MySQL behandelt.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: ee67dc1701d8230250132b9a3be0bd5972294292
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422727"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-summary"></a>Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Zusammenfassung

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Voraussetzungen

[Security](13-security.md)

## <a name="overview"></a>Übersicht

In diesem Dokument wurden mehrere Themen im Zusammenhang mit der Migration einer Anwendung von einer lokalen MySQL-Instanz zu Azure Database for MySQL behandelt. Alle Phasen von Beginn und Bewertung des Projekts bis zur Anwendungsübernahme wurden behandelt.

Das Migrationsteam muss die Themen sorgfältig lesen, da die getroffene Auswahl Auswirkungen auf die Projektzeitachse haben kann. Die Gesamtkosten sind angesichts der vielen für den Einsatz im Unternehmen bereiten Features verlockend.

Der Ansatz des Migrationsprojekts ist wichtig. Das Team muss die Anwendungs- und Datenbankkomplexität bewerten, um die Dauer der Konvertierung zu bestimmen. Konvertierungstools erleichtern den Übergang, aber es ist immer ein gewisses Maß an manueller Überprüfung und Aktualisierung erforderlich. Skripterstellung für vor der Migration auszuführende Aufgaben und Tests nach der Migration sind wichtig.

Anwendungsarchitektur und -entwurf können aussagekräftige Indikatoren für den erforderlichen Aufwand liefern. Beispielsweise können Anwendungen, die ORM-Frameworks verwenden, insbesondere dann hervorragende Kandidaten sein, wenn die Geschäftslogik in der Anwendung und nicht in Datenbankobjekten enthalten ist.

Schließlich bietet der Marketplace verschiedene kostenlose oder kommerzielle Tools. In diesem Dokument wurden die Schritte behandelt, die erforderlich sind, wenn das Team eine Datenbankmigration mithilfe einer der gängigeren Open-Source-Tooloptionen plant. Unabhängig davon, welchen Pfad Sie wählen, verfügen Microsoft und die MySQL-Community über die Tools und das Fachwissen für erfolgreiche Datenbankmigrationen.

## <a name="questions-and-feedback"></a>Fragen und Feedback

Senden Sie Fragen oder Vorschläge zur Verwendung von Azure Database for MySQL per E-Mail an das Azure Database for MySQL-Team (AskAzureDBforMySQL@service.microsoft.com). 

> [!Important]
> Die Adresse des Azure Database for MySQL-Teams ist nur für allgemeine Fragen und nicht für Supporttickets bestimmt.

Weitere Kontaktmöglichkeiten:

  - Wenn Sie den Azure-Support kontaktieren oder ein Problem mit Ihrem Konto beheben lassen möchten, [erstellen Sie im Azure-Portal ein Ticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

  - Wenn Sie Feedback abgeben oder Vorschläge für neue Features einreichen möchten, erstellen Sie einen Eintrag über [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0).
fwill
## <a name="find-a-partner-to-help-migrate"></a>Finden Sie einen Partner zur Unterstützung bei der Migration

Vielleicht fühlen Sie sich ja von diesem Leitfaden ein wenig erschlagen, aber keine Sorge\! Viele Experten in der Community verfügen über fundierte Migrationserfahrung. [Suchen Sie sich einen Microsoft Partner](https://www.microsoft.com/solution-providers/home) oder [Microsoft MVP](https://mvp.microsoft.com/MvpSearch), der mit Ihnen die am besten geeignete Migrationsstrategie findet. Sie sind nicht allein\!

Sie können auch die technischen Foren und sozialen Gruppen durchsuchen, um ausführlichere Informationen aus der realen Welt zu erhalten:

  - [Microsoft Community Forum](/answers/topics/azure-database-mysql.html)

  - [Azure Database for MySQL Tech Community](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/bg-p/ADforMySQL)

  - [StackOverflow für Azure MySQL](https://stackoverflow.com/questions/tagged/azure-database-mysql)

  - [Azure-Facebook-Gruppe](https://www.facebook.com/groups/MsftAzure)

  - [LinkedIn-Azure-Gruppe](https://www.linkedin.com/groups/2733961/)

  - [LinkedIn-Azure-Entwickler-Gruppe](https://www.linkedin.com/groups/1731317/)

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Bereitstellen einer Beispielanwendung mit einem End-to-End-MySQL-Migrationsleitfaden und zum Überprüfen der verfügbaren Serverparameter finden Sie im [Anhang](15-appendix.md).
