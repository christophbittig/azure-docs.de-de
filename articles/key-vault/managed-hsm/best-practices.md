---
title: Bewährte Methoden bei der Verwendung verwalteter HSMs von Azure Key Vault
description: In diesem Dokument werden einige der bewährten Methoden zum Verwenden von Key Vault erläutert.
services: key-vault
author: mbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: mbaldwin
ms.openlocfilehash: 99a313f64e9f5bafd13dbbf45a6881e1c6e2f275
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459694"
---
# <a name="best-practices-when-using-managed-hsm"></a>Bewährte Methoden bei der Verwendung verwalteter HSMs

## <a name="control-access-to-your-managed-hsm"></a>Steuern des Zugriffs auf Ihr verwaltetes HSM

Verwaltetes HSM ist ein Clouddienst zum Schutz von Verschlüsselungsschlüsseln. Da diese Schlüssel vertraulich und unternehmenskritisch sind, stellen Sie sicher, dass der Zugriff auf Ihre verwalteten HSMs geschützt ist, indem Sie nur autorisierte Anwendungen und Benutzer zulassen. In diesem [Artikel](access-control.md) finden Sie eine Übersicht über das Zugriffsmodell. Es erläutert die Authentifizierung und Autorisierung sowie die rollenbasierte Zugriffssteuerung.
- Erstellen Sie eine [Azure Active Directory-Sicherheitsgruppe](../../active-directory/fundamentals/active-directory-manage-groups.md) für die HSM-Administratoren (anstatt einzelnen Personen die Administratorrolle zuzuweisen). Dadurch wird eine „Verwaltungssperre“ im Falle der Löschung einzelner Konten verhindert.
- Sperren Sie den Zugriff auf Ihre Verwaltungsgruppen, Abonnements, Ressourcengruppen und verwaltete HSMs – Verwenden Sie Azure RBAC, um den Zugriff auf Ihre Verwaltungsgruppen, Abonnements und Ressourcengruppen zu steuern.
- Erstellen Sie Rollenzuweisungen pro Schlüssel mit der [lokalen RBAC für verwaltete HSMs](access-control.md#data-plane-and-managed-hsm-local-rbac).
- Vermeiden Sie die Zuweisung mehrerer Rollen zu denselben Prinzipalen, um die Aufgabentrennung beizubehalten. 
- Verwenden Sie beim Zuweisen von Rollen das Prinzip der geringstmöglichen Zugriffsrechte.
- Erstellen Sie eine benutzerdefinierte Rollendefinition mit genauen Berechtigungen.

## <a name="choose-regions-that-support-availability-zones"></a>Auswählen von Regionen, die Verfügbarkeitszonen unterstützen

- Wählen Sie Azure-Regionen aus, in denen [Verfügbarkeitszonen](../../availability-zones/az-overview.md) unterstützt werden, um für die beste Hochverfügbarkeit und Zonenresilienz zu sorgen. Diese Regionen werden im Azure-Portal als „Empfohlene Regionen“ angezeigt.

## <a name="backup"></a>Backup

- Stellen Sie sicher, dass Sie regelmäßig Sicherungen Ihrer HSM durchführen. Sicherungen können auf der HSM-Ebene und für bestimmte Schlüssel durchgeführt werden. 

## <a name="turn-on-logging"></a>Aktivieren Sie die Protokollierung.

- [Aktivieren Sie die Protokollierung](logging.md) für Ihre HSM Richten sie auch Warnungen ein.

## <a name="turn-on-recovery-options"></a>Aktivieren von Wiederherstellungsoptionen

- Standardmäßig ist das [vorläufige Löschen](soft-delete-overview.md) aktiviert. Sie können einen Aufbewahrungszeitraum von 7 bis 90 Tagen auswählen.
- Aktivieren Sie den Löschschutz, um das sofortige dauerhafte Löschen von HSMs oder Schlüsseln zu verhindern. Ist der Löschschutz aktiviert, verbleiben das HSM oder die Schlüssel im gelöschten Zustand, bis der Aufbewahrungszeitraum abgelaufen sind.

## <a name="generate-and-import-keys-from-on-premise-hsm"></a>Generieren und Importieren von Schlüsseln aus einem lokalen HSM

> [!NOTE]
> Schlüssel, die in einem verwalteten HSM erstellt oder in ein HSM importiert wurden, können nicht exportiert werden.

- Generieren Sie Schlüssel in Ihrem lokalen HSM, und [importieren Sie diese in das verwaltete HSM](hsm-protected-keys-byok.md), um eine langfristige Portabilität und Schlüsseldauerhaftigkeit sicherzustellen. Sie verfügen über eine Kopie Ihrer Schlüssel, die für die zukünftige Verwendung sicher in Ihrem lokalen HSM gespeichert sind.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur vollständigen HSM-Sicherung/-Wiederherstellung finden Sie unter [Vollständige Sicherung/Wiederherstellung](backup-restore.md).
- Informationen zum Verwenden von Azure Monitor zum Konfigurieren der Protokollierung finden Sie unter [Protokollierung für verwaltete HSMs](logging.md).
- Informationen zur Schlüsselverwaltung finden Sie unter [Verwalten verwalteter HSM-Schlüssel](key-management.md).
- Informationen zum Verwalten von Rollenzuweisungen finden Sie unter [Rollenverwaltung für verwaltetes HSM](role-management.md).
- Informationen zu Wiederherstellungsoptionen finden Sie unter [Übersicht über vorläufiges Löschen von verwaltetem HSM](soft-delete-overview.md).
