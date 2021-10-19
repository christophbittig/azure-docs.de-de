---
title: Konfigurieren von Exporteinstellungen im FHIR-Dienst
description: In diesem Artikel wird beschrieben, wie Exporteinstellungen im FHIR-Dienst konfiguriert werden.
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/11/2021
ms.author: cavoeg
ms.openlocfilehash: 327a36714b822669cbc41fc8b8fb195bed4872ed
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346698"
---
# <a name="configure-export-settings-and-set-up-a-storage-account"></a>Konfigurieren von Exporteinstellungen und Einrichten eines Speicherkontos

Der FHIR-Dienst unterstützt $export Befehl, mit dem Sie die Daten aus dem FHIR-Dienstkonto in ein Speicherkonto exportieren können.

Beim Konfigurieren des Exports im FHIR-Dienst sind drei Schritte erforderlich:

1. Aktivieren Sie die verwaltete Identität im FHIR-Dienstdienst.
2. Erstellen eines Azure-Speicherkontos (sofern noch nicht geschehen) und Zuweisen der Berechtigung für den FHIR-Dienst zum Speicherkonto.
3. Auswählen des Speicherkontos im FHIR-Dienst als Exportspeicherkonto.

## <a name="enabling-managed-identity-on-fhir-service"></a>Aktivieren der verwalteten Identität im FHIR-Dienst

Der erste Schritt beim Konfigurieren des FHIR-Diensts für den Export besteht im Aktivieren der systemweiten verwalteten Identität für den Dienst. Weitere Informationen zu verwalteten Identitäten in Azure finden Sie unter [Informationen zu verwalteten Identitäten für Azure-Ressourcen.](../../active-directory/managed-identities-azure-resources/overview.md)

Wechseln Sie dazu zum FHIR-Dienst, und wählen Sie **Identität aus.** Wenn Sie den Status in **Ein ändern,** wird die verwaltete Identität im FHIR-Dienstdienst aktiviert.

![Aktivieren der verwalteten Identität](media/export-data/fhir-mi-enabled.png)

Nun können Sie zum nächsten Schritt wechseln, indem Sie ein Speicherkonto erstellen und dem Dienst Berechtigungen zuweisen.

## <a name="adding-permission-to-storage-account"></a>Hinzufügen der Berechtigung für das Speicherkonto

Der nächste Schritt beim Exportieren von Daten besteht im Zuweisen der Berechtigung für den FHIR-Dienst zum Schreiben in das Speicherkonto.

Nachdem Sie ein Speicherkonto erstellt haben, wechseln Sie zum Access Control **(IAM)** im Speicherkonto, und wählen Sie dann **Rollenzuweisung hinzufügen aus.** 

Weitere Informationen zum Zuweisen von Rollen im Azure-Portal finden Sie unter [Integrierte Azure-Rollen.](../../role-based-access-control/role-assignments-portal.md)

Hier fügen Sie dem Dienstnamen die Rolle [Storage Blobdaten-Mitwirkender](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) hinzu, und wählen Sie dann **Speichern aus.**

![Seite „Rollenzuweisung hinzufügen“](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

Nun können Sie das Speicherkonto im FHIR-Dienst als Standardspeicherkonto für die $export.

## <a name="selecting-the-storage-account-for-export"></a>Auswählen des Speicherkontos für „$export“

Der letzte Schritt besteht im Zuweisen des Azure-Speicherkontos, in das der FHIR-Dienst die Daten exportiert. Wechseln Sie hierzu zu **Integration** in den FHIR-Dienst, und wählen Sie das Speicherkonto aus.

![FHIR-Exportspeicher](media/export-data/fhir-export-storage.png)

Nachdem Sie diesen letzten Schritt abgeschlossen haben, können Sie die Daten nun mithilfe des befehls $export exportieren.

> [!Note]
> Nur Speicherkonten, die sich im gleichen Abonnement wie der FHIR-Dienst befinden, können als Ziel für $export registriert werden.