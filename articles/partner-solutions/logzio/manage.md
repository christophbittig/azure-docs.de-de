---
title: Verwalten der Integration von Logz.io in Azure – Azure-Partnerlösungen
description: Hier erfahren Sie, wie Sie die Integration von Logz.io in Azure verwalten.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.openlocfilehash: 223dba26ab32659b710edde8f5bbbca7a67ba1af
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076583"
---
# <a name="manage-the-logzio-integration-in-azure"></a>Verwalten der Logz.io-Integration in Azure

In diesem Artikel wird beschrieben, wie Sie die Einstellungen für die Integration von Logz.io in Azure verwalten.

## <a name="resource-overview"></a>Ressourcenübersicht

Der Bildschirm **Übersicht** für Logz.io zeigt Details zur Ressource, einschließlich der folgenden Elemente:

- **Ressourcengruppe**
- **Location**
- **Abonnement**
- **Tags**
- **Logz.io-SSO**
- **Abrechnungszeitraum**

Je nachdem, ob Sie das einmalige Anmelden (Single Sign-On, SSO) aktiviert oder deaktiviert haben, sieht der Link für **Logz.io SSO** anders aus:

- Wenn Sie SSO aktiviert haben:

    :::image type="content" source="./media/manage/sso-opt-in.png" alt-text="Einmaliges Anmelden aktiviert":::

- Wenn Sie SSO deaktiviert haben:

    :::image type="content" source="./media/manage/sso-opt-out.png" alt-text="Einmaliges Anmelden deaktiviert":::

## <a name="reconfigure-rules-for-logs-and-metrics"></a>Erneutes Konfigurieren von Regeln für Protokolle und Metriken

Zum Ändern der Konfigurationsregeln wählen Sie unter **Logz-Konfiguration** die Option **Protokolle und Metriken** aus. Weitere Informationen finden Sie unter [Konfigurieren von Protokollen](./create.md#configure-logs).

:::image type="content" source="./media/manage/logs-metrics.png" alt-text="Konfiguration von Protokollen und Metriken":::

## <a name="configure-diagnostic-settings"></a>Konfigurieren von Diagnoseeinstellungen

Um Diagnoseeinstellungen zu konfigurieren, wählen Sie die Ressource aus. Wählen Sie dann im linken Bereich **Diagnoseeinstellungen** aus.

Aktivieren Sie in der Spalte **Zieldetails** die Option **An Partnerlösungen senden**, um Logz.io als Ziel auszuwählen. Diese Option ist erst verfügbar, nachdem eine Logz-Ressource erstellt wurde.

:::image type="content" source="./media/manage/configure-diagnostics.png" alt-text="Konfigurieren von Diagnoseeinstellungen":::

## <a name="view-monitored-resources"></a>Anzeigen der überwachten Ressourcen

Um die Liste der Ressourcen anzuzeigen, die Protokolle an Logz.io senden (gemäß den konfigurierten Geschäftsregeln), wechseln Sie zu **Logz-Konfiguration**, und wählen Sie **Überwachte Ressourcen** aus.

:::image type="content" source="./media/manage/monitored-resources.png" alt-text="Konfiguration von überwachten Ressourcen":::

Sie können die Liste nach Ressourcentyp, Namen der Ressourcengruppe, Speicherort und danach filtern, ob die Ressource Protokolle sendet.

Im Folgenden finden Sie die Beschreibungen der auf dem Bildschirm **Überwachte Ressourcen** gezeigten Spalten:

- **Ressourcenname**: Name der Azure-Ressource.
- **Ressourcentyp**: Typ der Azure-Ressource.
- **Ressourcengruppe**: Name der Ressourcengruppe für die Azure-Ressource.
- **Region**: Region oder Standort der Azure-Ressource.
- **Protokolle an Logz**: Gibt an, ob die Ressource Protokolle an Logz.io sendet. Wenn die Ressource keine Protokolle an Logz.io sendet, gibt dieses Feld den Grund dafür an. Mögliche Gründe:
  - Die Ressource unterstützt das Senden von Protokollen nicht: Nur Azure-Ressourcenprotokolle für alle Ressourcentypen und die [hier definierten Protokollkategorien](../../azure-monitor/essentials/resource-logs-categories.md) können für das Senden von Protokollen an Logz.io konfiguriert werden.
  - Limit von fünf Diagnoseeinstellungen erreicht: Jede Azure-Ressource kann über maximal fünf Diagnoseeinstellungen verfügen. Weitere Informationen finden Sie unter [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](../../azure-monitor/essentials/diagnostic-settings.md).
  - Fehler: Ein Fehler blockiert das Senden von Protokollen an Logz.io.
  - Protokolle nicht konfiguriert: Nur Ressourcen mit den geeigneten Ressourcentags können Protokolle an Logz.io senden. Siehe [Konfigurieren von Protokollen](./create.md#configure-logs).
  - Region nicht unterstützt: Die Azure-Ressource befindet sich in einer Region, die derzeit keine Protokolle an Logz.io sendet.

Um die Tagliste der Azure-Ressource zu bearbeiten, wählen Sie **Tags bearbeiten** aus. Sie können neue Tags hinzufügen oder vorhandene Tags löschen.

## <a name="monitor-vm-using-logzio-agent"></a>Überwachen einer VM mit dem Logz.io-Agent

Sie können Logz.io-Agents als Erweiterung auf virtuellen Computern installieren. Wechseln Sie zu **Logz-Konfiguration**, und wählen Sie **Agent für virtuelle Computer** aus. Dieser Bildschirm zeigt die Liste aller VMs im Abonnement.

Für jede VM werden die folgenden Daten angezeigt:

- **Ressourcenname**: Name des virtuellen Computers.
- **Ressourcentyp**: Typ der Azure-Ressource.
- **Ressourcengruppe**: Name der Ressourcengruppe für die Azure-Ressource.
- **Region**: Region oder Standort der Azure-Ressource.
- **Agent-Version**: Versionsnummer des Logz.io-Agents.
- **Erweiterungsstatus**: Gibt an, ob der Logz.io-Agent auf der VM **installiert** oder **nicht installiert** ist.
- **Protokolle an Logz**: Gibt an, ob der Logz.io-Agent Protokolle an Logz.io sendet.

:::image type="content" source="./media/manage/vm-agent.png" alt-text="Agent für virtuelle Computer":::

### <a name="install-vm-agent"></a>Installieren des VM-Agents

Um den VM-Agent zu installieren, führen Sie die folgenden Schritte aus.

1. Wählen Sie die VM aus, auf der Sie den Logz.io-Agent installieren möchten, und wählen Sie dann **Agent installieren** aus.

   Sie werden im Portal aufgefordert, die Installation des Agents mit der Standardauthentifizierung zu bestätigen.

1. Wählen Sie **OK** aus, um die Installation zu starten.

   Azure zeigt als Status **Installieren** an, bis der Agent installiert und bereitgestellt wurde.

   Nachdem der Logz.io-Agent installiert wurde, ändert sich der Status zu **Installiert**.

1. Um zu überprüfen, ob der Logz.io-Agent installiert wurde, wählen Sie die VM aus, und wechseln Sie zum Fenster **Erweiterungen**.

> [!NOTE]
> Wenn die VM nach der Installation des VM-Agents angehalten wird, zeigt die Spalte **Protokolle an Logz** für diese VM **Sendet nicht** an.

### <a name="uninstall-vm-agent"></a>Deinstallieren des VM-Agents

Sie können Logz.io-Agents auf einer VM über den **Agent für virtuelle Computer** deinstallieren. Wählen Sie die VM und dann **Agent deinstallieren** aus.

## <a name="create-logzio-sub-account"></a>Erstellen eines Logz.io-Unterkontos

Nur der Administrator des Logz.io-Kontos kann ein Unterkonto erstellen. Standardmäßig ist der Ersteller des Logz.io-Kontos als Administrator festgelegt. Melden Sie sich beim Logz.io-Konto an, um anderen Benutzer Administratorberechtigungen zu erteilen.

Weitere Informationen finden Sie auf der Logz.io-Website unter [Logz.io Sub Accounts](https://logz.io/blog/diving-deeper-logz-io-sub-accounts/) (Logz.io-Unterkonten).

Sie können ein Logz.io-Unterkonto mithilfe der Schaltfläche **Übersicht** > **Unterkonto hinzufügen** erstellen.

:::image type="content" source="./media/manage/create-sub-account-overview.png" alt-text="Erstellen eines Logz.io-Unterkontos in der Übersicht":::

Eine andere Methode besteht darin, zu **Logz-Konfiguration** zu wechseln und **Unterkonto hinzufügen** auszuwählen. Befolgen Sie die Eingabeaufforderungen, um das Unterkonto einzurichten, und aktiveren Sie die Übermittlung von Protokollen über das Unterkonto.

:::image type="content" source="./media/manage/create-sub-account-logz.png" alt-text="Erstellen eines Logz.io-Unterkontos über die Logz-Konfiguration":::

## <a name="delete-logzio-sub-account"></a>Löschen eines Logz.io-Unterkontos

1. Wählen Sie für die Logz.io-Ressource die **Logz-Konfiguration** und dann **Logz-Unterkonten** aus.
1. Wählen Sie die Ressource aus, die Sie löschen möchten.
1. Klicken Sie auf **Löschen**.

    :::image type="content" source="./media/manage/delete-sub-account-1.png" alt-text="Löschen eines Logz.io-Unterkontos über die Logz-Konfiguration":::

1. Geben Sie _Ja_ ein, um zu bestätigen, dass Sie die Logz.io-Ressource löschen möchten.
1. Klicken Sie auf **Löschen**.

    :::image type="content" source="./media/manage/delete-sub-account-2.png" alt-text="Bestätigen der Löschung eines Logz.io-Unterkontos":::

Es werden keine Protokolle mehr an Logz.io gesendet, und die Abrechnung für Logz.io wird beendet.

## <a name="delete-logzio-account"></a>Löschen eines Logz.io-Kontos

1. Wählen Sie in der Logz.io-Ressource **Übersicht** und dann **Löschen** aus.
1. Bestätigen Sie, dass Sie die Logz.io-Ressource löschen möchten.
1. Klicken Sie auf **Löschen**.

    :::image type="content" source="./media/manage/delete-resource-1.png" alt-text="Löschen einer Logz.io-Ressource in der Übersicht":::

1. Geben Sie _Ja_ ein, um zu bestätigen, dass Sie die Logz.io-Ressource löschen möchten.
1. Klicken Sie auf **Löschen**.

    :::image type="content" source="./media/manage/delete-resource-2.png" alt-text="Bestätigung der Löschung einer Logz.io-Ressource":::

> [!NOTE]
> Die Option **Löschen** im Hauptkonto wird nur aktiviert, wenn alle dem Hauptkonto zugeordneten Unterkonten bereits gelöscht wurde. Weitere Informationen zum Löschen von Unterkonten finden Sie unter [Löschen von Logz.io-Unterkonten](#delete-logzio-sub-account).

Nach dem Löschen der Ressource werden keine Protokolle mehr an Logz.io gesendet, und die Abrechnung für Logz.io über den Azure Marketplace wird beendet.

## <a name="get-support"></a>Support

Wenn Sie sich in Bezug auf die Integration von Logz.io in Azure an den Support wenden möchten, finden Sie hier die erforderlichen Informationen: **Support und Problembehandlung** und **Neue Supportanfrage**. Durch Auswahl dieser Option werden Sie zum [Logz.io-Portal](https://app.logz.io/) weitergeleitet. Nutzen Sie den Chatsupport, oder senden Sie eine E-Mail an [help@logz.io](mailto:help@logz.io).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Beheben von Problemen mit der Integration finden Sie unter [Problembehandlung](troubleshoot.md).
