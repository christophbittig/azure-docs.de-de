---
title: Was ist die gruppenbasierte Lizenzierung? – Azure Active Directory | Microsoft-Dokumentation
description: Lernen Sie mehr über die gruppenbasierte Lizenzierung in Azure Active Directory, ihre Funktionsweise und die entsprechenden Best Practices.
services: active-directory
keywords: Azure AD-Lizenzierung
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8c8e3be984d50475724525e34a2aee682408be4
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369662"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Was ist die gruppenbasierte Lizenzierung in Azure Active Directory?

Für kostenpflichtige Microsoft-Clouddienste wie Microsoft 365, Enterprise Mobility + Security, Dynamics 365 und ähnliche Produkte werden Lizenzen benötigt. Diese Lizenzen werden jedem Benutzer zugewiesen, der Zugriff auf diese Dienste benötigt. Administratoren verwalten Lizenzen über eines der Verwaltungsportale (Office, Azure) und PowerShell-Cmdlets. Azure Active Directory (Azure AD) ist die zugrunde liegende Infrastruktur, die die Identitätsverwaltung aller Microsoft-Clouddienste unterstützt. Azure AD speichert Informationen zum Lizenzzuweisungsstatus für Benutzer.

Bislang konnten Lizenzen nur auf Ebene einzelner Benutzer zugewiesen werden, was die umfassende Verwaltung erschweren kann. Um Benutzerlizenzen basierend auf Organisationsänderungen zu vergeben oder zu entziehen, wenn beispielsweise Benutzer der Organisation oder Abteilung beitreten oder diese verlassen, muss ein Administrator häufig ein komplexes PowerShell-Skript schreiben. Dieses Skript richtet einzelne Aufrufe an den Clouddienst.

Um diese Probleme zu beheben, enthält Azure AD jetzt die gruppenbasierte Lizenzierung. Sie können einer Gruppe eine oder mehrere Produktlizenzen zuweisen. Azure AD stellt sicher, dass die Lizenzen allen Mitgliedern der Gruppe zugewiesen werden. Allen neuen Mitgliedern, die der Gruppe beitreten, werden die entsprechenden Lizenzen zugewiesen. Wenn sie die Gruppe verlassen, werden diese Lizenzen entfernt. Dadurch ist keine automatisierte Lizenzverwaltung über PowerShell mehr erforderlich, um Änderungen in der Organisations- und Abteilungsstruktur benutzerbezogen widerzuspiegeln.

## <a name="licensing-requirements"></a>Lizenzanforderungen
Sie benötigen **für jeden Benutzer**, der von der gruppenbasierten Lizenzierung profitiert, eine der folgenden Lizenzen:

- Kostenpflichtiges oder Testabonnement für Azure AD Premium P1 oder höher

- Kostenpflichtige Edition oder kostenlose Testversion von Microsoft 365 Business Premium oder Office 365 Enterprise E3 oder Office 365 A3 oder Office 365 GCC G3 oder Office 365 E3 für GCCH oder Office 365 E3 für DOD und höher

### <a name="required-number-of-licenses"></a>Erforderliche Anzahl von Lizenzen
Für alle Gruppen, denen eine Lizenz zugewiesen ist, müssen Sie auch über eine Lizenz für jedes einzelne Mitglied verfügen. Sie müssen zwar nicht jedem Mitglied der Gruppe eine Lizenz zuweisen, aber Sie müssen mindestens über genügend Lizenzen für alle Mitglieder verfügen. Bei 1.000 einzelnen Mitgliedern, die lizenzierten Gruppen in Ihrem Mandanten angehören, müssen Sie beispielsweise über mindestens 1.000 Lizenzen verfügen, um den Lizenzvertrag zu erfüllen.

## <a name="features"></a>Features

Hier die Hauptmerkmale der gruppenbasierten Lizenzierung:

- Lizenzen können beliebigen Sicherheitsgruppen in Azure AD zugewiesen werden. Sicherheitsgruppen können mithilfe von Azure AD Connect aus einer lokalen Umgebung synchronisiert werden. Sie können Sicherheitsgruppen auch direkt in Azure AD Connect (auch als reine Cloudgruppen bezeichnet) oder automatisch über das Azure AD-Feature „Dynamische Gruppe“ erstellen.

- Wenn eine Produktlizenz einer Gruppe zugewiesen wird, kann der Administrator einen oder mehrere Servicepläne im Produkt deaktivieren. In der Regel erfolgt diese Zuweisung, wenn die Organisation einen in einem Produkt enthaltenen Dienst noch nicht verwenden kann. Beispielsweise könnte der Administrator Microsoft 365 einer Abteilung zuweisen, aber den Yammer-Dienst vorübergehend deaktivieren.

- Alle Microsoft-Clouddienste, die eine Lizenzierung auf Benutzerebene erfordern, werden unterstützt. Dazu zählen alle Microsoft 365-Produkte, Enterprise Mobility + Security und Dynamics 365.

- Die gruppenbasierte Lizenzierung ist derzeit nur über das [Azure-Portal](https://portal.azure.com) verfügbar. Wenn Sie in erster Linie andere Verwaltungsportale für die Benutzer- und Gruppenverwaltung nutzen, z.B. das [Microsoft 365 Admin Center](https://admin.microsoft.com), können Sie dies weiterhin tun. Jedoch sollten Sie das Azure-Portal zum Verwalten von Lizenzen auf Gruppenebene verwenden.

- Azure AD verwaltet Lizenzänderungen, die sich aus Änderungen an der Gruppenmitgliedschaft ergeben, automatisch. In der Regel erfolgen Änderungen des Lizenzstatus binnen Minuten nach einer Mitgliedschaftsänderung.

- Ein Benutzer kann Mitglied mehrerer Gruppen mit angegebenen Lizenzrichtlinien sein. Ein Benutzer kann auch über Lizenzen verfügen, die außerhalb von Gruppen zugewiesen wurden. Der resultierende Benutzerstatus ist eine Kombination aller zugewiesenen Produkt- und Dienstlizenzen. Wenn einem Benutzer die gleiche Lizenz aus mehreren Quellen zugewiesen wurde, wird die Lizenz nur einmal genutzt.

- In manchen Fällen können Benutzern keine Lizenzen zugewiesen werden. Mögliche Gründe sind das Fehlen verfügbarer Lizenzen im Mandanten oder in Konflikt stehende Dienste, die gleichzeitig zugewiesen wurden. Administratoren haben Zugriff auf Informationen zu Benutzern, für die Azure AD Gruppenlizenzen nicht vollständig verarbeiten konnte. Sie können anhand dieser Informationen Korrekturmaßnahmen vornehmen.

## <a name="your-feedback-is-welcome"></a>Wir freuen uns auf Ihr Feedback!

Wenn Sie Feedback oder Vorschläge zu Features haben, verwenden Sie [das Azure AD-Administratorforum](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu anderen Szenarien für die gruppenbasierte Lizenzverwaltung finden Sie unter:

* [Zuweisen von Lizenzen zu einer Gruppe in Azure Active Directory](../enterprise-users/licensing-groups-assign.md)
* [Bestimmen und Beheben von Lizenzproblemen für eine Gruppe in Azure Active Directory](../enterprise-users/licensing-groups-resolve-problems.md)
* [Migrieren einzelner lizenzierter Benutzer zur gruppenbasierten Lizenzierung in Azure Active Directory](../enterprise-users/licensing-groups-migrate-users.md)
* [Sicheres Migrieren von Benutzern zwischen Produktlizenzen mithilfe von gruppenbasierter Lizenzierung in Azure Active Directory](../enterprise-users/licensing-groups-change-licenses.md)
* [Gruppenbasierte Azure Active Directory-Lizenzierung – zusätzliche Szenarien](../enterprise-users/licensing-group-advanced.md)
* [PowerShell-Beispiele für die gruppenbasierte Lizenzierung in Azure AD](../enterprise-users/licensing-ps-examples.md)
