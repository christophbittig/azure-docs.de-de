---
title: Gewähren von Berechtigungen für die verwaltete Identität in einem Synapse-Arbeitsbereich
description: In diesem Artikel wird erläutert, wie Berechtigungen für die verwaltete Identität im Azure Synapse-Arbeitsbereich konfiguriert werden.
author: meenalsri
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.custom: subject-rbac-steps
ms.openlocfilehash: 009f611e1c80f4c4e0e7bafaa80036d809b3393d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853071"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>Erteilen von Berechtigungen für die verwaltete Identität eines Arbeitsbereichs (Vorschau)

In diesem Artikel erfahren Sie, wie Sie der verwalteten Identität in einem Azure Synapse-Arbeitsbereich Berechtigungen gewähren. Berechtigungen ermöglichen wiederum den Zugriff auf dedizierte SQL-Pools im Arbeitsbereich und im ADLS Gen2-Speicherkonto über das Azure-Portal.

>[!NOTE]
>Diese verwaltete Identität für den Arbeitsbereich wird im Rest dieses Dokuments als „verwaltete Identität“ bezeichnet.

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Gewähren von Berechtigungen für die verwaltete Identität für das ADLS Gen2-Speicherkonto

Ein ADLS Gen2-Speicherkonto ist erforderlich, um einen Azure Synapse-Arbeitsbereich zu erstellen. Um Spark-Pools erfolgreich im Azure Synapse-Arbeitsbereich zu starten, benötigt die verwaltete Azure Synapse-Identität für dieses Speicherkonto die Rolle *Mitwirkender an Storage-Blobdaten*. Die Pipelineorchestrierung in Azure Synapse profitiert ebenfalls von dieser Rolle.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Gewähren von Berechtigungen für die verwaltete Identität während der Erstellung eines Arbeitsbereichs

Azure Synapse versucht, der verwalteten Identität die Rolle „Mitwirkender an Storage-Blobdaten“ zu gewähren, nachdem Sie den Azure Synapse-Arbeitsbereich über das Azure-Portal erstellt haben. Stellen Sie auf der Registerkarte **Grundlagen** die ADLS Gen2-Speicherkontodetails bereit.

![Registerkarte „Grundlagen“ im Erstellungsfluss des Arbeitsbereichs](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Wählen Sie unter **Kontoname** und **File system name** (Dateisystemname) das ADLS Gen2-Speicherkonto bzw. -Dateisystem aus.

![Bereitstellen der Details eines ADLS Gen2-Speicherkontos](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Wenn der Ersteller des Arbeitsbereichs auch **Besitzer** des ADLS Gen2-Speicherkontos ist, weist Azure Synapse der verwalteten Identität die Rolle *Mitwirkender an Storage-Blobdaten* zu. Unterhalb der von Ihnen eingegebenen Speicherkontodetails wird die folgende Meldung angezeigt.

![Erfolgreiche Zuweisung von „Mitwirkender an Storage-Blobdaten“.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Wenn der Ersteller des Arbeitsbereichs nicht der Besitzer des ADLS Gen2-Speicherkontos ist, weist Azure Synapse der verwalteten Identität nicht die Rolle *Mitwirkender an Storage-Blobdaten* zu. Die Meldung, die unter den Speicherkontodetails angezeigt wird, informiert den Ersteller des Arbeitsbereichs, dass er keine ausreichenden Berechtigungen besitzt, um der verwalteten Identität die Rolle *Mitwirkender an Storage-Blobdaten* zu gewähren.

![Nicht erfolgreiche Zuweisung von „Mitwirkender an Storage-Blobdaten“.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Wie die Meldung besagt, können Sie Spark-Pools nur erstellen, wenn der verwalteten Identität *Mitwirkender an Storage-Blobdaten* zugewiesen wurde.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Gewähren von Berechtigungen für die verwaltete Identität nach der Erstellung eines Arbeitsbereichs

Wenn Sie bei der Erstellung eines Arbeitsbereichs der verwalteten Identität nicht die Rolle *Mitwirkender an Storage-Blobdaten* zuweisen, weist der **Besitzer** des ADLS Gen2-Speicherkontos der Identität diese Rolle manuell zu. Die folgenden Schritten helfen Ihnen bei der manuellen Zuweisung.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Schritt 1: Navigieren zum ADLS Gen2-Speicherkonto im Azure-Portal

Öffnen Sie im Azure-Portal das ADLS Gen2-Speicherkonto, und klicken Sie im linken Navigationsbereich auf **Übersicht**. Sie müssen die Rolle *Mitwirkender an Storage-Blobdaten* nur auf Container- oder Dateisystemebene zuweisen. Wählen Sie **Container** aus.  
![ADLS Gen2-Speicherkonto – Übersicht](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Schritt 2: Auswählen des Containers

Die verwaltete Identität sollte über Datenzugriff auf den Container (das Dateisystem) verfügen, der (das) beim Erstellen des Arbeitsbereichs bereitgestellt wurde. Sie finden diesen Container oder das Dateisystem in Azure-Portal. Öffnen Sie den Azure Synapse-Arbeitsbereich im Azure-Portal, und wählen Sie im linken Navigationsbereich die Registerkarte **Übersicht** aus.
![ADLS Gen2-Speicherkontocontainer](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Wählen Sie denselben Container oder dasselbe Dateisystem aus, um der verwalteten Identität die Rolle *Mitwirkender an Storage-Blobdaten* zu gewähren.
![Screenshot, der den Container oder das Dateisystem zeigt, den/das Sie auswählen sollten.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-open-access-control-and-add-role-assignment"></a>Schritt 3: Öffnen der Zugriffssteuerung und Hinzufügen der Rollenzuweisung

1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

1. Weisen Sie die folgende Rolle zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).
    
    | Einstellung | Wert |
    | --- | --- |
    | Role | Mitwirkender an Storage-Blob |
    | Zugriff zuweisen zu | MANAGEDIDENTITY |
    | Member | Name der verwalteten Identität  |

    > [!NOTE]
    > Der Name der verwalteten Identität ist auch der Name des Arbeitsbereichs.

    ![Seite „Rollenzuweisung hinzufügen“ im Azure-Portal](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

1. Klicken Sie auf **Speichern**, um die Rollenzuweisung hinzuzufügen.

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Schritt 9: Überprüfen, ob die Rolle „Mitwirkender an Storage-Blobdaten“ der verwalteten Identität für das Speicherkonto zugewiesen ist

Wählen Sie **Zugriffssteuerung (IAM)** und anschließend **Rollenzuweisungen** aus.

![Überprüfen der Rollenzuweisung](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Ihre verwaltete Identität sollte im Abschnitt **Mitwirkender an Storage-Blobdaten** mit der zugewiesenen Rolle *Mitwirkender an Storage-Blobdaten* angezeigt werden. 
![Auswahl des ADLS Gen2-Speicherkontocontainers](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur [Verwalteten Identität des Arbeitsbereichs](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics).
