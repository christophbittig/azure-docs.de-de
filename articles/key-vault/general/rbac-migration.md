---
title: Migrieren zur rollenbasierten Zugriffssteuerung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie von Key Vault-Zugriffsrichtlinien zu Azure-Rollen migrieren.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 75a6064ba44f803837719e0cb1f574d4295f8ac4
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732888"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>Migrieren von Key Vault-Zugriffsrichtlinien zu einem Berechtigungsmodell für die rollenbasierte Zugriffssteuerung in Azure

Das Modell der Key Vault-Zugriffsrichtlinien ist ein auf Key Vault basierendes Autorisierungssystem zur Bereitstellung von Zugriff auf Schlüssel, Geheimnisse und Zertifikate. Der Zugriff kann durch Zuweisen von einzelnen Berechtigungen an Sicherheitsprinzipale (Benutzer, Gruppen, Dienstprinzipale, verwaltete Identitäten) auf der Key Vault-Ebene gesteuert werden. 

Die rollenbasierte Zugriffssteuerung (Azure Role-Based Access Control, Azure RBAC) ist ein Autorisierungssystem, das auf [Azure Resource Manager](../../azure-resource-manager/management/overview.md) basiert und eine präzise Verwaltung des Zugriffs auf Azure-Ressourcen ermöglicht. Mit Azure RBAC steuern Sie den Zugriff auf Ressourcen, indem Sie Rollenzuweisungen erstellen. Diese bestehen aus den folgenden drei Elementen: Sicherheitsprinzipal, Rollendefinition (mehrere vordefinierte Berechtigungen) und Bereich (Ressourcengruppe oder einzelne Ressource). Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md).

Bevor Sie mit der Migration zu Azure RBAC beginnen, sollten Sie sich mit dessen Vorteilen und Einschränkungen vertraut machen.

Azure RBAC bietet gegenüber Key Vault-Zugriffsrichtlinien die folgenden Hauptvorteile:
- Vereinheitlichtes Zugriffssteuerungsmodell für Azure-Ressourcen: dieselbe API für alle Azure-Dienste
- Zentralisierte Zugriffsverwaltung für Administratoren: Verwalten aller Azure-Ressourcen in einer Ansicht
- Integriert mit [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) für eine zeitbasierte Zugriffssteuerung
- Ablehnungszuweisungen: Möglichkeit zum Ausschluss von Sicherheitsprinzipalen in einem bestimmten Bereich. Weitere Informationen finden Sie unter [Verstehen von Ablehnungszuweisungen](../../role-based-access-control/deny-assignments.md).

Azure RBAC besitzt die folgenden Nachteile:
- Latenz bei Rollenzuweisungen: Es kann mehrere Minuten dauern, bis eine Rollenzuweisung angewendet wird. Key Vault-Zugriffsrichtlinien werden sofort zugewiesen.
- Begrenzte Anzahl von Rollenzuweisungen: 2000 Rollenzuweisungen pro Abonnement gegenüber 1024 Zugriffsrichtlinien pro Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Zugriffsrichtlinien für die Zuordnung von Azure-Rollen

Azure RBAC verfügt über mehrere integrierte Azure-Rollen, die Sie Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zuweisen können. Wenn die integrierten Rollen den Ansprüchen Ihrer Organisation nicht entsprechen, können Sie Ihre eigenen [benutzerdefinierten Azure-Rollen](../../role-based-access-control/custom-roles.md) erstellen.

Key Vault verfügt über die folgenden integrierten Rollen für die Zugriffsverwaltung von Schlüsseln, Zertifikaten und Geheimnissen:
- Key Vault-Administrator
- Key Vault-Leser
- Key Vault-Zertifikatbeauftragter
- Key Vault-Kryptografiebeauftragter
- Key Vault-Kryptografiebenutzer
- Key Vault Crypto Service Encryption-Benutzer
- Key Vault-Geheimnisbeauftragter
- Key Vault-Geheimnisbenutzer

Weitere Informationen zu verfügbaren integrierten Rollen finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).

Key Vault-Zugriffsrichtlinien können mit einzeln ausgewählten Berechtigungen oder über vordefinierte Berechtigungsvorlagen zugewiesen werden.

Die folgenden vordefinierten Berechtigungsvorlagen für Zugriffsrichtlinien sind verfügbar:
- Verwaltung von Schlüsseln, Geheimnissen und Zertifikaten
- Verwaltung von Schlüsseln und Geheimnissen
- Verwaltung von Geheimnissen und Zertifikaten
- Schlüsselverwaltung
- Verwaltung von Geheimnissen
- Zertifikatverwaltung
- SQL Server-Connector
- Azure Data Lake Storage oder Azure Storage
- Azure Backup
- Exchange Online-Kundenschlüssel
- SharePoint Online-Kundenschlüssel
- Azure Information-BYOK

### <a name="access-policy-templates-to-azure-roles-mapping"></a>Vorlagen für Zugriffsrichtlinien für die Zuordnung von Azure-Rollen
| Zugriffsrichtlinienvorlage | Operationen (Operations) | Azure-Rolle |
| --- | --- | --- |
| Verwaltung von Schlüsseln, Geheimnissen und Zertifikaten | Schlüssel: alle Vorgänge <br>Zertifikate: alle Vorgänge<br>Geheimnisse: alle Vorgänge | Key Vault-Administrator |
| Verwaltung von Schlüsseln und Geheimnissen | Schlüssel: alle Vorgänge <br>Geheimnisse: alle Vorgänge| Key Vault-Kryptografiebeauftragter <br> Key Vault-Geheimnisbeauftragter |
| Verwaltung von Geheimnissen und Zertifikaten | Zertifikate: alle Vorgänge <br>Geheimnisse: alle Vorgänge| Key Vault-Zertifikatbeauftragter <br> Key Vault-Geheimnisbeauftragter|
| Schlüsselverwaltung | Schlüssel: alle Vorgänge| Key Vault-Kryptografiebeauftragter|
| Verwaltung von Geheimnissen | Geheimnisse: alle Vorgänge| Key Vault-Geheimnisbeauftragter|
| Zertifikatverwaltung | Zertifikate: alle Vorgänge | Key Vault-Zertifikatbeauftragter|
| SQL Server-Connector | Schlüssel: GET-, LIST-, WRAP KEY-, UNWRAP KEY-Vorgänge | Key Vault Crypto Service Encryption-Benutzer|
| Azure Data Lake Storage oder Azure Storage | Schlüssel: GET-, LIST-, UNWRAP KEY-Vorgänge | –<br> Benutzerdefinierte Rolle erforderlich|
| Azure Backup | Schlüssel: GET-, LIST-, BACKUP-Vorgänge<br> Zertifikate: GET-, LIST-, BACKUP-Vorgänge | –<br> Benutzerdefinierte Rolle erforderlich|
| Exchange Online-Kundenschlüssel | Schlüssel: GET-, LIST-, WRAP KEY-, UNWRAP KEY-Vorgänge | Key Vault Crypto Service Encryption-Benutzer|
| Exchange Online-Kundenschlüssel | Schlüssel: GET-, LIST-, WRAP KEY-, UNWRAP KEY-Vorgänge | Key Vault Crypto Service Encryption-Benutzer|
| Azure Information-BYOK | Schlüssel: GET-, DECRYPT-, SIGN-Vorgänge | –<br>Benutzerdefinierte Rolle erforderlich|


## <a name="assignment-scopes-mapping"></a>Zuordnen von Zuweisungsbereichen  

Mit Azure RBAC für Key Vault können Sie Rollen auf den folgenden Ebenen zuweisen:
- Verwaltungsgruppe
- Subscription
- Resource group
- Key Vault-Ressource
- Einzelner Schlüssel, einzelnes Geheimnis, einzelnes Zertifikat

Mit dem Berechtigungsmodell für Key Vault-Zugriffsrichtlinien können Richtlinien nur auf der Key Vault-Ressourcenebene zugewiesen werden.

Es hat sich bewährt, ein Key Vault-Objekt pro Anwendung zu erstellen und den Zugriff auf der Key Vault-Ebene zu verwalten. In manchen Szenarios kann die Verwaltung des Zugriffs auf anderen Ebenen jedoch die Zugriffsverwaltung vereinfachen.

- ** Infrastruktur, Sicherheitsadministratoren und Operatoren: Für die Verwaltung einer Gruppe von Key Vaults auf Verwaltungsgruppen-, Abonnement- oder Ressourcengruppenebene mit Key Vault-Zugriffsrichtlinien sind Richtlinien für jedes einzelne Key Vault-Objekt erforderlich. Mit Azure RBAC können Sie eine Rollenzuweisung auf Verwaltungsgruppen-, Abonnement- oder Ressourcengruppenebene erstellen. Diese Zuweisung gilt für alle neuen Key Vault-Objekte, die auf der gleichen Ebene erstellt werden. In diesem Szenario wird empfohlen, anstatt eines permanenten Zugriffs Privileged Identity Management mit Just-in-Time-Zugriff zu verwenden.
 
- ** Anwendungen: In manchen Szenarios muss eine Anwendung ein Geheimnis für eine andere Anwendung freigeben. Bei Verwendung von Key Vault-Zugriffsrichtlinien müssen in diesem Fall getrennte Key Vault-Objekte erstellt werden, um zu verhindern, dass auf alle Geheimnisse zugegriffen werden kann. Mit Azure RBAC können Sie stattdessen eine Rolle für ein einzelnes Geheimnis zuweisen.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Schritte zur Migration von Key Vault-Zugriffsrichtlinien zu Azure RBAC
Zwischen den Berechtigungsmodellen von Azure RBAC und den Key Vault-Zugriffsrichtlinien bestehen viele Unterschiede. Führen Sie die folgenden Schritte aus, um Ausfälle während der Migration zu verhindern:
 
1. **Identifizieren und Zuweisen von Rollen**: Identifizieren Sie mithilfe der Zuordnungstabelle oben integrierte Rollen, und erstellen Sie bei Bedarf benutzerdefinierte Rollen. Weisen Sie Rollen Bereichen zu, und verwenden Sie dabei den Leitfaden für die Zuordnung von Bereichen. Weitere Informationen zum Zuweisen von Rollen zu Key Vault-Objekten finden Sie unter [Gewähren des Zugriffs auf Key Vault-Schlüssel, -Zertifikate und -Geheimnisse mit der rollenbasierten Zugriffssteuerung in Azure](rbac-guide.md).
1. **Überprüfen von Rollenzuweisungen**: Die Verteilung von Rollenzuweisungen in Azure RBAC kann mehrere Minuten dauern. Eine Anleitung zur Überprüfung von Rollenzuweisungen finden Sie unter [Auflisten von Rollenzuweisungen in einem Bereich](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope).
1. **Konfigurieren von Überwachung und Warnungen für Key Vault**: Sie sollten unbedingt die Protokollierung aktivieren und Warnungen für Ausnahmen vom Typ „Zugriff verweigert“ einrichten. Weitere Informationen finden Sie unter [Überwachung und Warnungen für Azure Key Vault](./alert.md).
1. **Festlegen des Berechtigungsmodells für die rollenbasierte Zugriffssteuerung in Azure auf Key Vault**: Wenn Sie das Azure RBAC-Berechtigungsmodell aktivieren, werden alle vorhandenen Zugriffsrichtlinien ungültig. Bei einem Fehler können Sie wieder von dem Berechtigungsmodell zu den vorhandenen Zugriffsrichtlinien wechseln, die unverändert bleiben.

> [!NOTE]
> Das Ändern des Berechtigungsmodells erfordert die Berechtigung „Microsoft.Authorization/roleAssignments/write“, die in den Rollen [Besitzer](../../role-based-access-control/built-in-roles.md#owner) und [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) enthalten ist. Klassische Administratorrollen für ein Abonnement wie „Dienstadministrator“ und „Co-Admin“ werden nicht unterstützt.

> [!NOTE]
> Ist das Azure RBAC-Berechtigungsmodell aktiviert, tritt für alle Skripts, die ein Update der Zugriffsrichtlinien durchführen möchten, ein Fehler auf. Diese Skripts müssen aktualisiert werden, damit Sie Azure RBAC verwenden können.

## <a name="troubleshooting"></a>Problembehandlung
-  Die Rollenzuweisung funktioniert auch nach einigen Minuten noch nicht: In manchen Situationen kann die Zuweisung von Rollen länger dauern. Daher sollte der Code Wiederholungslogik enthalten, um diese Fälle abzudecken.
- Die Rollenzuweisungen sind nach der (vorläufigen) Löschung und Wiederherstellung von Key Vault nicht mehr vorhanden: Für alle Azure-Dienste gilt zurzeit eine Einschränkung für das Feature „Vorläufiges Löschen“. Nach der Wiederherstellung müssen alle Rollenzuweisungen neu erstellt werden.    

## <a name="learn-more"></a>Weitere Informationen

- [RBAC in Azure – Übersicht](../../role-based-access-control/overview.md)
- [Tutorial zu benutzerdefinierten Rollen](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
