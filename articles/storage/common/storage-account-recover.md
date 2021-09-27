---
title: Wiederherstellen eines gelöschten Speicherkontos
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie ein gelöschtes Speicherkonto im Azure-Portal wiederherstellen können.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 69d95ea67064ad45e60a6c38e37ab425bed27b76
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616876"
---
# <a name="recover-a-deleted-storage-account"></a>Wiederherstellen eines gelöschten Speicherkontos

In einigen Fällen kann ein gelöschtes Speicherkonto möglicherweise über das Azure-Portal wiederhergestellt werden. Zum Wiederherstellen eines Speicherkontos müssen die folgenden Bedingungen erfüllt sein:

- Das Speicherkonto wurde innerhalb der letzten 14 Tage gelöscht.
- Das Speicherkonto wurde mit dem Azure Resource Manager-Bereitstellungsmodell erstellt.
- Seit dem Löschen des ursprünglichen Kontos wurde kein neues Speicherkonto mit demselben Namen erstellt.
- Dem Benutzer, der das Speicherkonto wiederherstellt, muss eine Azure RBAC-Rolle zugewiesen werden, die die Berechtigung **Microsoft.Storage/storageAccounts/write** erteilt. Informationen zu integrierten Azure RBAC-Rollen, die diese Berechtigung erteilen, finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).

Bevor Sie ein gelöschtes Speicherkonto wiederherzustellen versuchen, vergewissern Sie sich, dass die Ressourcengruppe für dieses Konto vorhanden ist. Wenn die Ressourcengruppe gelöscht wurde, müssen Sie sie neu erstellen. Eine Ressourcengruppe kann nicht wiederhergestellt werden. Weitere Informationen finden Sie unter [Verwalten von Ressourcengruppen](../../azure-resource-manager/management/manage-resource-groups-portal.md).

Wenn das gelöschte Speicherkonto von Kunden verwaltete Schlüssel mit Azure Key Vault verwendet hat und der Schlüsseltresor ebenfalls gelöscht wurde, müssen Sie ihn zuerst wiederherstellen, bevor Sie das Speicherkonto wiederherstellen. Weitere Informationen finden Sie unter [Übersicht über die Azure Key Vault-Wiederherstellung](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> Die Wiederherstellung eines gelöschten Speicherkontos ist nicht sichergestellt. Die Wiederherstellung ist ein Versuch nach dem Prinzip „beste Leistung“. Microsoft empfiehlt das Sperren von Ressourcen, um eine versehentliche Löschung des Kontos zu verhindern. Weitere Informationen zu Ressourcensperren finden Sie unter [Sperren von Ressourcen, um Änderungen zu verhindern](../../azure-resource-manager/management/lock-resources.md).
>
> Eine weitere bewährte Vorgehensweise zum Vermeiden einer versehentlichen Kontolöschung besteht darin, die Anzahl von Benutzern zu begrenzen, die Berechtigungen zum Löschen eines Kontos über die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) haben. Weitere Informationen finden Sie unter [Bewährte Methoden für Azure RBAC](../../role-based-access-control/best-practices.md).

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>Wiederherstellen eines gelöschten Kontos über das Azure-Portal

Wenn Sie ein gelöschtes Speicherkonto aus einem anderen Speicherkonto heraus wiederherstellen möchten, führen Sie die folgenden Schritte aus:

1. Navigieren Sie im Azure-Portal zur Übersichtsseite für ein vorhandenes Speicherkonto.
1. Wählen Sie im Abschnitt **Support und Problembehandlung** die Option **Gelöschtes Konto wiederherstellen** aus.
1. Wählen Sie in der Dropdownliste das wiederherzustellende Konto aus, wie in der folgenden Abbildung gezeigt wird. Wenn das Speicherkonto, das Sie wiederherstellen möchten, in der Dropdownliste nicht angezeigt wird, kann es nicht wiederhergestellt werden.

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="Der Screenshot zeigt, wie ein Speicherkonto im Azure-Portal wiederhergestellt wird":::

1. Wählen Sie die Schaltfläche **Wiederherstellen** aus, um das Konto wiederherzustellen. Im Portal wird eine Benachrichtigung angezeigt, dass die Wiederherstellung gerade ausgeführt wird.

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>Wiederherstellen eines gelöschten Kontos über ein Supportticket

1. Navigieren Sie im Azure-Portal zu **Hilfe und Support**.
1. Wählen Sie **Neue Supportanfrage** aus.
1. Wählen Sie auf der Registerkarte **Grundlagen** im Feld **Problemtyp** die Option **Technisch** aus.
1. Wählen Sie im Feld **Abonnement** das Abonnement aus, in dem das gelöschte Speicherkonto enthalten war.
1. Wählen Sie im Feld **Dienst** die Option **Speicherkontoverwaltung** aus.
1. Wählen Sie im Feld **Ressource** eine beliebige Speicherkontoressource aus. Das gelöschte Speicherkonto wird in der Liste nicht angezeigt.
1. Fügen Sie eine kurze Zusammenfassung des Problems hinzu.
1. Wählen Sie im Feld **Problemtyp** die Option **Löschen und Wiederherstellen** aus.
1. Wählen Sie im Feld **Problemuntertyp** die Option **Gelöschtes Speicherkonto wiederherstellen** aus. Die folgende Abbildung zeigt ein Beispiel für die Registerkarte **Grundlagen**, die gerade ausgefüllt wird:

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="Der Screenshot zeigt, wie ein Speicherkonto über ein Supportticket wiederhergestellt wird – Registerkarte „Grundlagen“":::

1. Navigieren Sie als Nächstes zur Registerkarte **Lösungen**, und wählen Sie **Kundenseitig gesteuerte Wiederherstellung von Speicherkonten** aus, wie in der folgenden Abbildung gezeigt wird:

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="Der Screenshot zeigt, wie ein Speicherkonto über ein Supportticket wiederhergestellt wird – Registerkarte „Lösungen“":::

1. Wählen Sie in der Dropdownliste das wiederherzustellende Konto aus, wie in der folgenden Abbildung gezeigt wird. Wenn das Speicherkonto, das Sie wiederherstellen möchten, in der Dropdownliste nicht angezeigt wird, kann es nicht wiederhergestellt werden.

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="Der Screenshot zeigt, wie ein Speicherkonto über ein Supportticket wiederhergestellt wird":::

1. Wählen Sie die Schaltfläche **Wiederherstellen** aus, um das Konto wiederherzustellen. Im Portal wird eine Benachrichtigung angezeigt, dass die Wiederherstellung gerade ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

- [Speicherkontoübersicht](storage-account-overview.md)
- [Erstellen eines Speicherkontos](storage-account-create.md)
- [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“](storage-account-upgrade.md)
- [Verschieben eines Azure Storage-Kontos in eine andere Region](storage-account-move.md)
