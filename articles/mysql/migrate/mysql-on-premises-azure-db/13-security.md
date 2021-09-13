---
title: 'Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Sicherheit'
description: Die Migration zu einem cloudbasierten Dienst bedeutet nicht, dass das gesamte Internet ständig Zugriff darauf hat.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: f0d0613dca258050cec75d544438b781c1f9fe96
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285223"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-security"></a>Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Sicherheit

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Voraussetzungen

[Business Continuity & Disaster Recovery](12-business-continuity-and-disaster-recovery.md)

## <a name="overview"></a>Übersicht

Die Migration zu einem cloudbasierten Dienst bedeutet nicht, dass das gesamte Internet ständig Zugriff darauf hat. Azure bietet erstklassige Sicherheitsfunktionen, die sicherstellen, dass Datenworkloads kontinuierlich vor böswilligen Benutzern und Programmen geschützt werden.

## <a name="authentication"></a>Authentifizierung

Azure Database for MySQL unterstützt sowohl die Standardauthentifizierungsmechanismen für MySQL-Benutzerkonnektivität als auch die [Integration in Azure Active Directory](../../concepts-azure-ad-authentication.md). Bei dieser Sicherheitsintegration werden Token ausgestellt, die bei der MySQL-Anmeldung wie Kennwörter fungieren. Die [Konfiguration der Active Directory-Integration](../../howto-configure-sign-in-azure-ad-authentication.md) ist äußerst einfach und unterstützt nicht nur Benutzer, sondern auch AAD-Gruppen.

Durch diese enge Integration können Administratoren und Anwendungen die erweiterten Sicherheitsfeatures von [Azure Identity Protection](../../../active-directory/identity-protection/overview-identity-protection.md) nutzen, um Identitätsprobleme zu beseitigen.

> [!NOTE] 
> Dieses Sicherheitsfeature wird ab MySQL 5.7 unterstützt. Die meisten [Anwendungstreiber](../../howto-configure-sign-in-azure-ad-authentication.md) werden unterstützt, solange die Option `clear-text` angegeben wird.

## <a name="threat-protection"></a>Bedrohungsschutz

Wenn Benutzer- oder Anwendungsanmeldeinformationen kompromittiert wurden, enthalten Protokolle wahrscheinlich keine fehlgeschlagenen Anmeldeversuche. Kompromittierte Anmeldeinformationen können es böswilligen Benutzern ermöglichen, auf die Daten zuzugreifen und diese herunterzuladen. Von [Azure Threat Protection](../../concepts-security.md#threat-protection) können Anomalien bei Anmeldungen (z. B. ungewöhnliche Standorte, seltene Benutzer oder Brute-Force-Angriffe) und andere verdächtige Aktivitäten erkannt werden. Administratoren können benachrichtigt werden, wenn etwas verdächtig aussieht.

## <a name="audit-logging"></a>Überwachungsprotokollierung

MySQL verfügt über ein stabiles integriertes Überwachungsprotokollfeature. In Azure Database for MySQL ist dieses [Überwachungsprotokollfeature standardmäßig deaktiviert](../../concepts-audit-logs.md). Die Protokollierung auf Serverebene kann durch Ändern des Serverparameters `audit\_log\_enabled` aktiviert werden. Nach der Aktivierung können Sie über [Azure Monitor](../../../azure-monitor/overview.md) und [Log Analytics](../../../azure-monitor/logs/design-logs-deployment.md) auf Protokolle zugreifen, indem Sie die [Diagnoseprotokollierung](../../howto-configure-audit-logs-portal.md#set-up-diagnostic-logs) aktivieren.

Führen Sie die folgende KQL-Abfrage aus, um Ereignisse im Zusammenhang mit Benutzerverbindungen abzufragen:

```
AzureDiagnostics  
| where ResourceProvider =="MICROSOFT.DBFORMYSQL" 
| where Category == 'MySqlAuditLogs' and event\_class\_s == "connection\_log"  
| project TimeGenerated, LogicalServerName\_s, event\_class\_s, event\_subclass\_s  
, event\_time\_t, user\_s , ip\_s , sql\_text\_s  
| order by TimeGenerated asc
```

## <a name="encryption"></a>Verschlüsselung

Ruhende Daten in der MySQL-Instanz werden standardmäßig verschlüsselt. Alle automatisierten Sicherungen werden ebenfalls verschlüsselt, um mögliche Datenlecks zu verhindern. Diese Verschlüsselung erfolgt in der Regel mit einem Schlüssel, der beim Erstellen der Instanz erstellt wird. Zusätzlich zu diesem Standardverschlüsselungsschlüssel können Administratoren [eigene Schlüssel verwenden (BYOK, Bring Your Own Key)](../../concepts-data-encryption-mysql.md).

Bei Verwendung einer Strategie mit kundenseitig verwalteten Schlüsseln müssen Sie mit den Aufgaben rund um die Schlüssellebenszyklusverwaltung vertraut sein. Kundenschlüssel werden in einer Instanz von [Azure Key Vault](../../../key-vault/general/basic-concepts.md) gespeichert, und der Zugriff erfolgt über Richtlinien. Es ist wichtig, alle Empfehlungen für die Schlüsselverwaltung zu befolgen. Der Verlust des Verschlüsselungsschlüssels bedeutet den Verlust des Datenzugriffs.

Verwenden Sie zusätzlich zu kundenseitig verwalteten Schlüsseln Schlüssel auf Dienstebene, um eine [doppelte Verschlüsselung](../../concepts-infrastructure-double-encryption.md) zu implementieren. Durch die Implementierung dieses Features werden ruhende Daten hochgradig verschlüsselt. Dies geht allerdings zulasten der Verschlüsselungsleistung. Es sollten entsprechende Tests durchgeführt werden.

Daten können bei der Übertragung mit SSL/TLS verschlüsselt werden. Wie bereits erwähnt, kann es erforderlich sein, Ihre [Anwendungen zu ändern](../../howto-configure-ssl.md), damit diese Änderung unterstützt wird, und auch die entsprechenden TLS-Überprüfungseinstellungen zu konfigurieren.

## <a name="firewall"></a>Firewall

Sobald Benutzer eingerichtet sind und ruhende Daten verschlüsselt werden, sollte das Migrationsteam die Datenflüsse im Netzwerk überprüfen. Azure Database for MySQL bietet mehrere Mechanismen zum Schutz der Netzwerkebenen, indem der Zugriff nur auf autorisierte Benutzer, Anwendungen und Geräte beschränkt wird.

Die erste Verteidigungslinie zum Schutz der MySQL-Instanz besteht in der Implementierung von [Firewallregeln](../../concepts-firewall-rules.md). IP-Adressen können beim Zugriff auf die Instanz über interne oder externe IP-Adressen auf gültige Standorte beschränkt werden. Wenn die MySQL-Instanz nur für interne Anwendungen verwendet wird, sollten Sie den [öffentlichen Zugriff einschränken](../../howto-deny-public-network-access.md).

Wenn Sie eine Anwendung zusammen mit der MySQL-Workload zu Azure migrieren, ist es wahrscheinlich, dass mehrere virtuelle Netzwerke in einem Hub-and-Spoke-Muster eingerichtet werden, für das das [Peering virtueller Netzwerke](../../../virtual-network/virtual-network-peering-overview.md) konfiguriert werden muss.

## <a name="private-link"></a>Private Link

Aktivieren Sie [Private Link](../../concepts-data-access-security-private-link.md), um den Zugriff auf Azure Database for MySQL auf interne Azure-Ressourcen zu beschränken. Private Link stellt sicher, dass der MySQL-Instanz keine öffentliche, sondern eine private IP-Adresse zugewiesen wird.

> [!NOTE]
> Es gibt viele weitere [grundlegende Überlegungen zu Azure-Netzwerken](../../concepts-data-access-and-security-vnet.md), die berücksichtigt werden müssen, aber in diesem Leitfaden nicht behandelt werden.

Informieren Sie sich über die möglichen Aufgaben zum Erstellen einer [Sicherheitsbaseline](/azure/mysql/security-baseline), die für alle Azure-Ressourcen implementiert werden können. Nicht alle unter dem Referenzlink beschriebenen Elemente gelten für alle Datenworkloads oder Azure-Ressourcen.

## <a name="security-checklist"></a>Checkliste für die Sicherheit

  - Verwenden Sie nach Möglichkeit die Azure AD-Authentifizierung.

  - Aktivieren Sie Advanced Threat Protection.

  - Aktivieren Sie alle Überwachungsfeatures.

  - Erwägen Sie eine BYOK-Strategie (Bring Your Own Key).

  - Implementieren Sie Firewallregeln.

  - Nutzen Sie private Endpunkte für Workloads, die nicht über das Internet übertragen werden.  


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Zusammenfassung](./14-summary.md)