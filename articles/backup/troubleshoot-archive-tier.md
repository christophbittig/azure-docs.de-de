---
title: Problembehandlung für die Archivebene
description: Hier erfahren Sie mehr über die Problembehandlung von Archivebenenfehlern für Azure Backup.
ms.topic: troubleshooting
ms.date: 10/23/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 560c8900e5d0c06dc436ec1222583db58f7cdda3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094987"
---
# <a name="troubleshooting-recovery-point-archive-using-archive-tier"></a>Problembehandlung für das Archiv des Wiederherstellungspunkts mithilfe der Archivebene

Dieser Artikel enthält Details zur Problembehandlung für Fehlercodes, die angezeigt werden, wenn ein Wiederherstellungspunkt nicht in das Archiv verschoben werden kann.

## <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Fehlermeldung**: Der Wiederherstellungspunkttyp ist nicht für eine Verschiebung in das Archiv qualifiziert.

**Beschreibung**: Dieser Fehlercode wird angezeigt, wenn der ausgewählte Typ des Wiederherstellungspunkts nicht berechtigt ist, in das Archiv verschoben zu werden.

**Empfohlene Aktion**: Überprüfen Sie die Berechtigung des Wiederherstellungspunkts. Weitere Informationen finden Sie unter [Unterstützte Workloads](archive-tier-support.md#supported-workloads).

## <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Fehlermeldung**: Der Wiederherstellungspunkt umfasst aktive Abhängigkeiten für die Wiederherstellung und ist nicht für eine Verschiebung in das Archiv qualifiziert.

**Beschreibung**: Der ausgewählte Wiederherstellungspunkt enthält aktive Abhängigkeiten und kann daher nicht in das Archiv verschoben werden.

**Empfohlene Aktion**: Überprüfen Sie die Berechtigung des Wiederherstellungspunkts. Weitere Informationen finden Sie unter [Unterstützte Workloads](archive-tier-support.md#supported-workloads).

## <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Fehlermeldung**: Der Wiederherstellungspunkt ist nicht für eine Verschiebung in das Archiv qualifiziert, weil die bisherige Lebensdauer im Key Vault Standard-Tarif nicht dem empfohlenen Mindestwert entspricht.

**Beschreibung**: Der Wiederherstellungspunkt muss bei virtuellen Azure-Computern mindestens drei Monate und bei SQL Server mindestens 45 Tage auf der Standardzugriffsebene gespeichert gewesen sein.

**Empfohlene Aktion**: Überprüfen Sie die Berechtigung des Wiederherstellungspunkts. Weitere Informationen finden Sie unter [Unterstützte Workloads](archive-tier-support.md#supported-workloads).

## <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Fehlermeldung**: Die verbleibende Lebensdauer des Wiederherstellungspunkts liegt unter dem erforderlichen Mindestwert.

**Beschreibung**: Die erforderliche Mindestlebensdauer für einen Wiederherstellungspunkt, die zum Verschieben in das Archiv berechtigt, beträgt sechs Monate.

**Empfohlene Aktion**: Überprüfen Sie die Berechtigung des Wiederherstellungspunkts. Weitere Informationen finden Sie unter [Unterstützte Workloads](archive-tier-support.md#supported-workloads).

## <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Fehlermeldung**: Der Wiederherstellungspunkt ist nicht für eine Verschiebung in das Archiv qualifiziert, weil er bereits in die Archivebene verschoben wurde.

**Beschreibung**: Der ausgewählte Wiederherstellungspunkt befindet sich bereits im Archiv. Daher ist er nicht berechtigt, in das Archiv verschoben zu werden.

## <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Fehlermeldung**: Der Datenquellentyp ist für die Empfehlungs-API nicht geeignet.

**Beschreibung**: Die Empfehlungs-API ist nur für virtuelle Azure-Computer geeignet. Dies trifft nicht auf den ausgewählten Datenquellentyp zu.

## <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**Fehlermeldung**: Der Wiederherstellungspunkt wurde bereits aktiviert. Die Aktivierung ist für diesen Wiederherstellungspunkt nicht zulässig.

**Beschreibung**: Der ausgewählte Wiederherstellungspunkt wurde bereits aktiviert.

## <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Fehlermeldung**: Der Wiederherstellungspunkt ist nicht berechtigt, in das Archiv verschoben zu werden.

**Beschreibung**:Der ausgewählte Wiederherstellungspunkt ist nicht berechtigt, in das Archiv verschoben zu werden.

## <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**Fehlermeldung**: Der Archivwiederherstellungspunkt wird nicht aktiviert. Wiederholen Sie die Wiederherstellung, nachdem die Aktivierung des Wiederherstellungspunkts im Archiv abgeschlossen ist.

**Beschreibung**: Der Wiederherstellungspunkt ist nicht aktiviert. Versuchen Sie, die Wiederherstellung nach dem Aktivieren des Wiederherstellungspunkts auszuführen.

## <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**Fehlermeldung**: Aktivierung wird nur für Archivwiederherstellungspunkte unterstützt. Aktivierung wird nur für Archivwiederherstellungspunkte unterstützt.

**Beschreibung**: Für den ausgewählten Wiederherstellungspunkt ist keine Aktivierung zulässig.

## <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Fehlermeldung**: Die Aktivierung des Archivwiederherstellungspunkts wird bereits durchgeführt.

**Beschreibung**: Die Aktivierung des ausgewählten Wiederherstellungspunkts wird bereits durchgeführt.

## <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Fehlermeldung**: Der Wiederherstellungspunkt kann nicht auf die Archivebene verschoben werden, da die von der Richtlinie vorgegebene Aufbewahrungsdauer nicht ausreicht.

**Empfohlene Aktion**: Aktualisieren Sie die Richtlinie für das geschützte Element mit der entsprechenden Aufbewahrungseinstellung, und wiederholen Sie dann den Vorgang.

## <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**Fehlermeldung**: Es wird noch ermittelt, ob dieser Wiederherstellungspunkt verschoben werden kann.

**Beschreibung**: Es muss noch ermittelt werden, ob der Wiederherstellungspunkt verschoben werden kann.

**Empfohlene Aktion**: Warten Sie einen Moment, und wiederholen Sie dann den Vorgang.
