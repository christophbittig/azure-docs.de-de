---
title: 'Schnellstart: Aktivieren verwalteter Identitäten für Ihr Automation-Konto mithilfe des Azure-Portals'
description: Diese Schnellstartanleitung unterstützt Sie beim Aktivieren verwalteter Identitäten für Ihr Automation-Konto mithilfe des Azure-Portals.
services: automation
ms.date: 09/07/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.openlocfilehash: 13b314e5f73a0c970e28dfdd87551d713d60175b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462119"
---
# <a name="quickstart-enable-managed-identities-for-your-automation-account-using-the-azure-portal"></a>Schnellstart: Aktivieren verwalteter Identitäten für Ihr Automation-Konto mithilfe des Azure-Portals

In dieser Schnellstartanleitung erfahren Sie, wie Sie verwaltete Identitäten für ein Azure Automation-Konto aktivieren. Weitere Informationen zur Funktionsweise verwalteter Identitäten mit Azure Automation finden Sie unter [Verwaltete Identitäten](../automation-security-overview.md#managed-identities).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Ein Azure Automation-Konto. Eine Anleitung hierzu finden Sie unter [Schnellstart: Erstellen eines Automation-Kontos mithilfe des Azure-Portals](create-account-portal.md).

- Eine vom Benutzer zugewiesene verwaltete Identität. Anleitungen finden Sie unter [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Die benutzerseitig zugewiesene verwaltete Identität und die Azure-Zielressourcen, die Ihr Runbook mit dieser Identität verwaltet, müssen sich im selben Azure-Abonnement befinden.

## <a name="enable-system-assigned-managed-identity"></a>Aktivieren einer systemseitig zugewiesenen verwalteten Identität

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem Automation-Konto.

1. Wählen Sie unter **Kontoeinstellungen** die Option **Identität (Vorschau)** aus.

   :::image type="content" source="media/enable-managed-identity/managed-identity-portal.png" alt-text="Navigieren zu „Identität“ im Portal":::

1. Legen Sie unter „Systemseitig zugewiesen“ die Option **Status** auf **Ein** fest, und klicken Sie auf **Speichern**. Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Ja**.

   In Ihrem Automation-Konto kann nun die systemseitig zugewiesene Identität verwendet werden, die bei Azure Active Directory (Azure ID) registriert ist und durch eine Objekt-ID angegeben wird.

   :::image type="content" source="media/enable-managed-identity/system-assigned-object-id.png" alt-text="Objekt-ID der verwalteten Identität.":::

## <a name="add-user-assigned-managed-identity"></a>Hinzufügen von benutzerseitig zugewiesenen verwalteten Identitäten

Dieser Abschnitt knüpft an das Ende des letzten Abschnitts an.

1. Wählen Sie die Registerkarte **Benutzerseitig zugewiesen** und anschließend **+ Hinzufügen** oder **Benutzerseitig zugewiesene verwaltete Identität hinzufügen** aus, um die Seite **Benutzerseitig zugewiesene verwaltete Identität h...** zu öffnen.

   :::image type="content" source="media/enable-managed-identity/user-assigned-portal.png" alt-text="Registerkarte „Benutzerseitig zugewiesen“ im Portal":::

1. Wählen Sie in der Dropdownliste **Abonnement** das Abonnement für Ihre benutzerseitig zugewiesene verwaltete Identität aus.

   :::image type="content" source="media/enable-managed-identity/add-user-assigned.png" alt-text="Seite „Benutzerseitig zugewiesene verwaltete Identität hinzufügen“ im Portal":::

1. Wählen Sie unter **Vom Benutzer zugewiesene verwaltete Identitäten** Ihre vorhandene benutzerseitig zugewiesene verwaltete Identität und dann **Hinzufügen** aus. Daraufhin werden Sie zur Registerkarte **Benutzerseitig zugewiesen** zurückgeleitet.

   :::image type="content" source="media/enable-managed-identity/added-user-identity-portal.png" alt-text="Benutzerseitig zugewiesene Identität im Portal hinzugefügt":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die an Ihr Automation-Konto angefügte benutzerseitig zugewiesene verwaltete Identität nicht mehr benötigen, führen Sie die folgenden Schritte aus:

1. Wählen Sie auf der Registerkarte **Benutzerseitig zugewiesen** Ihre benutzerseitig zugewiesene verwaltete Identität aus.

1. Wählen Sie im oberen Menü die Option **Entfernen** aus. Wenn Sie zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

Wenn Sie die für Ihr Automation-Konto aktivierte systemseitig zugewiesene verwaltete Identität nicht mehr benötigen, führen Sie die folgenden Schritte aus:

1. Wählen Sie auf der Registerkarte **Systemseitig zugewiesen** unter **Status** die Option **Aus** aus.

1. Wählen Sie im oberen Menü die Option **Speichern** aus. Wenn Sie zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie verwaltete Identitäten für ein Azure Automation-Konto aktiviert. Informationen zum Verwenden Ihres Automation-Kontos mit verwalteten Identitäten zum Ausführen eines Runbooks finden Sie unter folgendem Link:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen eines Automation PowerShell-Runbooks mithilfe einer verwalteten Identität](../learn/powershell-runbook-managed-identity.md)