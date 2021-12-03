---
title: 'Schnellstart: Erstellen eines Azure Automation-Kontos über das Portal'
description: Dieser Schnellstart unterstützt Sie bei den ersten Schritten zum Erstellen eines Azure Automation-Kontos über das Portal.
services: automation
ms.date: 10/26/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7497362ccd71e6efabc873015796855bd989d33c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470673"
---
# <a name="quickstart-create-an-automation-account-using-the-azure-portal"></a>Schnellstart: Erstellen eines Automation-Kontos über das Azure-Portal

Ein [Azure Automation-Konto](../automation-security-overview.md) kann mithilfe des Azure-Portals erstellt werden. Hierbei handelt es sich um eine browserbasierte Benutzeroberfläche für den Zugriff auf eine Reihe von Ressourcen. Von einem Automation-Konto können Ressourcen in allen Regionen und Abonnements für einen bestimmten Mandanten verwaltet werden. In dieser Schnellstartanleitung erfahren Sie, wie Sie ein Automation-Konto erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-automation-account"></a>Erstellen eines Automation-Kontos

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie im oberen Menü auf **+ Ressource erstellen**.

1. Wählen Sie unter **Kategorien** die Option **IT- und Verwaltungstools** und dann **Automation** aus.

   :::image type="content" source="./media/create-account-portal/automation-account-portal.png" alt-text="Navigieren zu den Automation-Konten im Portal":::

Die Optionen für Ihr neues Automation-Konto sind auf der Seite **Automation-Konto erstellen** auf Registerkarten organisiert. In den folgenden Abschnitten werden die einzelnen Registerkarten und ihre Optionen beschrieben.

### <a name="basics"></a>Grundlagen

Geben Sie auf der Registerkarte **Grundlagen** die grundlegenden Informationen für Ihr Automation-Konto an. Nachdem Sie die Registerkarte **Grundlagen** ausgefüllt haben, können Sie Ihr neues Automation-Konto anhand der Optionen auf den übrigen Registerkarten weiter anpassen, oder Sie können **Überprüfen + erstellen** auswählen, um die Standardoptionen zu übernehmen und mit der Überprüfung und Erstellung des Kontos fortzufahren.

> [!NOTE]
> Standardmäßig wird eine systemseitig zugewiesene verwaltete Identität für das Automation-Konto aktiviert.

In der folgenden Tabelle werden die Felder der Registerkarte **Grundlagen** beschrieben.

| **Feld** | **Erforderlich**<br> **or**<br> **optional** |**Beschreibung** |
|---|---|---|
|Subscription|Erforderlich |Wählen Sie in der Dropdownliste das Azure-Abonnement für das Konto aus.|
|Ressourcengruppe|Erforderlich |Wählen Sie in der Dropdownliste Ihre vorhandene Ressourcengruppe oder die Option **Neu erstellen** aus.|
|Name des Automation-Kontos|Erforderlich |Geben Sie einen eindeutigen Namen für den Standort und die Ressourcengruppe ein. Namen von gelöschten Automation-Konten sind möglicherweise nicht sofort verfügbar. Nach der Eingabe auf der Benutzeroberfläche kann der Kontoname nicht mehr geändert werden. |
|Region|Erforderlich |Wählen Sie in der Dropdownliste eine Region für das Konto aus. Eine aktualisierte Liste mit Standorten, an denen Sie ein Automation-Konto bereitstellen können, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).|

Die folgende Abbildung zeigt eine Standardkonfiguration für ein neues Automation-Konto.

:::image type="content" source="./media/create-account-portal/create-account-basics.png" alt-text="Erforderliche Felder zum Erstellen des Automation-Kontos auf der Registerkarte „Grundlagen“":::

### <a name="advanced"></a>Fortgeschrittene

Auf der Registerkarte **Erweitert** können Sie die Option für die verwaltete Identität für Ihr neues Automation-Konto konfigurieren. Die benutzerseitig zugewiesene verwaltete Identität kann auch konfiguriert werden, nachdem das Automation-Konto erstellt wurde.

Anweisungen zum Erstellen einer benutzerseitig zugewiesenen verwalteten Identität finden Sie unter [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

In der folgenden Tabelle werden die Felder der Registerkarte **Erweitert** beschrieben.

| **Feld** | **Erforderlich**<br> **or**<br> **optional** |**Beschreibung** |
|---|---|---|
|Systemseitig zugewiesen |Optional |Eine Azure Active Directory-Identität, die an den Lebenszyklus des Automation-Kontos gebunden ist. |
|Benutzerseitig zugewiesen |Optional |Eine verwaltete Identität, die als eigenständige Azure-Ressource dargestellt und getrennt von den Ressourcen verwaltet wird, die sie verwenden.|

Sie können verwaltete Identitäten später aktivieren, und das Automation-Konto wird ohne eine solche Identität erstellt. Informationen zum Aktivieren einer verwalteten Identität nach dem Erstellen des Kontos finden Sie unter [Aktivieren einer verwalteten Identität](enable-managed-identity.md). Wenn Sie beide Optionen aktivieren, wählen Sie für die benutzerseitig zugewiesene Identität die Option **Benutzerseitig zugewiesene Identitäten hinzufügen** aus. Wählen Sie auf der Seite **Benutzerseitig zugewiesene verwaltete Identität auswählen** ein Abonnement aus, und fügen Sie mindestens eine in diesem Abonnement erstellte benutzerseitig zugewiesene Identität hinzu, um sie dem Automation-Konto zuzuweisen.

Die folgende Abbildung zeigt eine Standardkonfiguration für ein neues Automation-Konto.

:::image type="content" source="./media/create-account-portal/create-account-advanced.png" alt-text="Erforderliche Felder zum Erstellen des Automation-Kontos auf der Registerkarte „Erweitert“":::

### <a name="tags-tab"></a>Registerkarte „Tags“

Auf der Registerkarte **Tags** können Sie Resource Manager-Tags angeben, um Ihre Azure-Ressourcen zu organisieren. Weitere Informationen finden Sie unter [Markieren von Ressourcen, Ressourcengruppen und Abonnements für die logische Organisation](../../azure-resource-manager/management/tag-resources.md).

### <a name="review--create-tab"></a>Registerkarte „Überprüfen und erstellen“

Wenn Sie zur Registerkarte **Überprüfen + erstellen** wechseln, führt Azure die Überprüfung der ausgewählten Automation-Kontoeinstellungen aus. Wenn die Überprüfung erfolgreich ist, können Sie mit dem Erstellen des Automation-Kontos fortfahren.

Wenn die Überprüfung fehlschlägt, gibt das Portal an, welche Einstellungen geändert werden müssen.

Überprüfen Sie Ihr neues Automation-Konto.

:::image type="content" source="./media/create-account-portal/automation-account-overview.png" alt-text="Übersichtsseite für das Automation-Konto":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Automation-Konto nicht mehr verwenden möchten, wählen Sie auf der Seite **Übersicht** die Option **Löschen** und dann bei entsprechender Aufforderung **Ja** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein Automation-Konto erstellt. Fahren Sie mit der nächsten Schnellstartanleitung fort, um verwaltete Identitäten mit Ihrem Automation-Konto zu verwenden:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen eines Automation PowerShell-Runbooks mithilfe einer verwalteten Identität](../learn/powershell-runbook-managed-identity.md)