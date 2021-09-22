---
title: Erstellen eines Kontos über das Azure-Portal
description: Hier erfahren Sie, wie Sie ein Azure Batch-Konto im Azure-Portal erstellen, um umfangreiche parallele Workloads in der Cloud auszuführen.
ms.topic: how-to
ms.date: 08/31/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 037ada644f60eabf498c59047513f4ad8292f239
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123308693"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Erstellen eines Batch-Kontos mit dem Azure-Portal

In diesem Artikel erfahren Sie, wie Sie im [Azure-Portal](https://portal.azure.com) ein [Azure Batch-Konto](accounts.md) mit den geeigneten Kontoeigenschaften für Ihr Computeszenario erstellen. Außerdem erfahren Sie, wo Sie wichtige Kontoeigenschaften wie Zugriffsschlüssel und Konto-URLs finden.

Hintergrundinformationen zu Batch-Konten und -Szenarien finden Sie unter [Workflow und Ressourcen des Batch-Diensts](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Erstellen eines Batch-Kontos

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie auf der Startseite die Option **Ressource erstellen** aus.

1. Geben Sie **Batch-Dienst** in das Suchfeld ein. Wählen Sie **Batch-Dienst** aus den Ergebnissen und dann **Erstellen** aus.

1. Geben Sie die folgenden Details ein.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="Screenshot: Bildschirm des neuen Azure Batch-Kontos.":::

    a. **Abonnement**: Das Abonnement, in dem das Batch-Konto erstellt werden soll. Wenn Sie nur über ein Abonnement verfügen, ist es standardmäßig ausgewählt.

    b. **Ressourcengruppe**: Eine vorhandene Ressourcengruppe für Ihr neues Batch-Konto. Optional können Sie auch eine neue Ressourcengruppe erstellen.

    c. **Kontoname**: Der ausgewählte Name muss innerhalb der Azure-Region, in der das Konto erstellt wird, eindeutig sein (siehe **Standort** weiter unten). Der Kontoname darf nur Kleinbuchstaben und Zahlen enthalten und muss 3 bis 24 Zeichen lang sein.

    d. **Standort**: Die Azure-Region, in der das Batch-Konto erstellt werden soll. Nur die von Ihrem Abonnement und der Ressourcengruppe unterstützten Regionen werden als Optionen angezeigt.

    e. **Speicherkonto**: Ein optionales [Azure Storage-Konto](accounts.md#azure-storage-accounts), das Sie dem Azure Batch-Konto zuordnen. Sie können ein vorhandenes Speicherkonto auswählen oder ein neues erstellen. Die beste Leistung erzielen Sie mit einem Speicherkonto vom Typ „Allgemein v2“.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="Screenshot: Optionen beim Erstellen eines Speicherkontos.":::

1. Wählen Sie, falls gewünscht, **Erweitert** aus, um den **Identitätstyp**, den **öffentlichen Netzwerkzugriff** oder den **Poolzuordnungsmodus** festzulegen. Für die meisten Szenarien sind die Standardoptionen geeignet.

1. Wählen Sie **Überprüfen + Erstellen** und dann **Erstellen** aus, um das Konto zu erstellen.

## <a name="view-batch-account-properties"></a>Anzeigen der Eigenschaften des Batch-Kontos

Klicken Sie nach der Kontoerstellung auf das Konto, um auf dessen Einstellungen und Eigenschaften zuzugreifen. Über das linke Menü können Sie auf alle Kontoeinstellungen und -eigenschaften zugreifen.

> [!NOTE]
> Der Name des Batch-Kontos ist seine ID und kann nicht geändert werden. Wenn Sie den Namen eines Batch-Kontos ändern müssen, müssen Sie das Konto löschen und ein neues mit dem gewünschten Namen erstellen.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Screenshot: Seite „Azure Batch-Konto“ im Azure-Portal.":::

Wenn Sie eine Anwendung mit den [Batch-APIs](batch-apis-tools.md#azure-accounts-for-batch-development) entwickeln, benötigen Sie eine Konto-URL und einen Schlüssel für den Zugriff auf Ihre Batch-Ressourcen. (Batch unterstützt auch die Azure Active Directory-Authentifizierung.) Klicken Sie zum Anzeigen der Zugriffsinformationen für das Batch-Konto auf **Schlüssel**.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Screenshot: Azure Batch-Schlüssel im Azure-Portal.":::

Klicken Sie auf **Speicherkonto**, um den Namen und die Schlüssel des mit Ihrem Batch-Konto verknüpften Speicherkontos anzuzeigen.

Um die [Ressourcenkontingente](batch-quota-limit.md) anzuzeigen, die für das Azure Batch-Konto gelten, wählen Sie **Kontingente** aus.

## <a name="additional-configuration-for-user-subscription-mode"></a>Zusätzliche Konfiguration für den Benutzerabonnementmodus

Wenn Sie ein Batch-Konto im Benutzerabonnementmodus erstellen möchten, führen Sie vor der Erstellung des Kontos die folgenden zusätzlichen Schritte aus.

> [!IMPORTANT]
> Der Benutzer, der das Batch-Konto im Benutzerabonnementmodus erstellt, muss über die Rollenzuweisung „Mitwirkender“ oder „Besitzer“ für das Abonnement verfügen, in dem das Batch-Konto erstellt wird.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Gewähren des Zugriffs auf Ihr Abonnement für Azure Batch (einmaliger Vorgang)

Wenn Sie Ihr erstes Batch-Konto im Modus „Benutzerabonnement“ erstellen, müssen Sie Ihr Abonnement bei Batch registrieren. (Falls Sie diesen Schritt bereits ausgeführt haben, fahren Sie mit dem nächsten Abschnitt fort.)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie auf **Alle Dienste** > **Abonnements** und anschließend auf das Abonnement, das Sie für das Batch-Konto verwenden möchten.

1. Klicken Sie auf der Seite **Abonnement** auf **Ressourcenanbieter**, und suchen Sie nach **Microsoft.Batch**. Überprüfen Sie, ob der Ressourcenanbieter **Microsoft.Batch** im Abonnement registriert ist. Wenn dies nicht der Fall ist, wählen Sie den Link **Registrieren** am oberen Rand des Bildschirms aus.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Screenshot: Microsoft.Batch-Ressourcenanbieter.":::

1. Navigieren Sie zurück zur Seite **Abonnement**, und wählen Sie dann **Zugriffssteuerung (IAM)** aus.

1. Weisen Sie der Batch-API die Rolle **Mitwirkender** oder **Besitzer** zu. Sie finden dieses Konto, indem Sie nach **Microsoft Azure Batch** suchen. (Die Anwendungs-ID für dieses Konto lautet **ddbf3205-c6bd-46ae-8127-60eb93363864**.)

   Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Im Modus „Benutzerabonnement“ ist ein [Azure-Schlüsseltresor](../key-vault/general/overview.md) erforderlich. Der Schlüsseltresor muss sich im gleichen Abonnement und in der gleichen Region befinden wie das Batch-Konto, das erstellt werden soll.

1. Wählen Sie auf der Startseite des [Microsoft Azure-Portals](https://portal.azure.com) die Option **Ressource erstellen** aus.
1. Geben Sie **Key Vault** in das Suchfeld ein. Wählen Sie in den Ergebnissen **Schlüsseltresor**  und dann **Erstellen** aus.
1. Geben Sie auf der Seite **Schlüsseltresor erstellen** einen Namen für den Schlüsseltresor ein, und erstellen Sie eine neue Ressourcengruppe in derselben Region, in der sich das Azure Batch-Konto befinden soll. Behalten Sie bei den übrigen Einstellungen die Standardwerte bei, und klicken Sie auf **Erstellen**.

Geben Sie beim Erstellen des Batch-Kontos im Modus „Benutzerabonnement“ die Option **Benutzerabonnement** als Poolzuordnungsmodus an. Wählen Sie den Schlüsseltresor aus, den Sie erstellt haben, und aktivieren Sie das Kontrollkästchen, um Azure Batch Zugriff auf den Schlüsseltresor zu gewähren.

Wenn Sie den Zugriff auf den Schlüsseltresor lieber manuell gewähren möchten, wechseln Sie zum Abschnitt **Zugriffsrichtlinien** des Schlüsseltresors, und wählen Sie **Zugriffsrichtlinie hinzufügen** aus. Wählen Sie den Link neben **Prinzipal auswählen** aus, und suchen Sie nach **Microsoft Azure Batch** (Anwendungs-ID **ddbf3205-c6bd-46ae-8127-60eb93363864**). Wählen Sie den Prinzipal aus, und konfigurieren Sie dann die **Berechtigungen für Geheimnis** mithilfe des Dropdownmenüs. Azure Batch muss mindestens die Berechtigungen **Abrufen**, **Auflisten**, **Festlegen** und **Löschen** erhalten. Bei [Schlüsseltresoren mit aktiviertem vorläufigem Löschen](../key-vault/general/soft-delete-overview.md) muss Azure Batch außerdem die Berechtigung zum **Wiederherstellen** erhalten.

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Screenshot: Auswahloptionen für „Berechtigungen für Geheimnis“ für Azure Batch":::

Wählen Sie **Hinzufügen** aus. Stellen Sie dann sicher, dass für die verknüpfte **Key Vault**-Ressource die Kontrollkästchen **Azure Virtual Machines für Bereitstellung** und **Azure Resource Manager für Vorlagenbereitstellung** aktiviert sind. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="Screenshot: Bildschirm „Zugriffsrichtlinie“.":::

### <a name="configure-subscription-quotas"></a>Konfigurieren von Abonnementkontingenten

Für Batch-Konten im Modus „Benutzerabonnement“ müssen [Kernspeicherkontingente](batch-quota-limit.md) manuell festgelegt werden. Die Batch Standard-Kernkontingente gelten nicht für Konten im Modus „Benutzerabonnement“. Stattdessen werden die [Kontingente in Ihrem Abonnement](../azure-resource-manager/management/azure-subscription-service-limits.md) für regionale Computekerne, Computekerne pro Serie und andere Ressourcen angewandt und erzwungen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) Ihr Batch-Konto im Benutzerabonnementmodus aus, um die dazugehörigen Einstellungen und Eigenschaften anzuzeigen.
1. Wählen Sie im linken Menü die Option **Kontingente** aus, um die Kernkontingente für Ihr Batch-Konto anzuzeigen und zu konfigurieren.

## <a name="other-batch-account-management-options"></a>Weitere Optionen für die Verwaltung von Batch-Konten

Neben der Verwendung des Azure-Portals stehen Ihnen zum Erstellen und Verwalten von Batch-Konten die folgenden Tools zur Verfügung:

- [Batch-PowerShell-Cmdlets](batch-powershell-cmdlets-get-started.md)
- [Azure-Befehlszeilenschnittstelle](batch-cli-get-started.md)
- [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Workflow des Batch-Diensts und primäre Ressourcen](batch-service-workflow-features.md) wie Pools, Knoten, Aufträge und Aufgaben.
- Informieren Sie sich über die Grundlagen der Entwicklung einer Batch-fähigen Anwendung mit der [Batch-.NET-Clientbibliothek](quick-run-dotnet.md) oder mit [Python](quick-run-python.md). In diesen Schnellstarts werden Sie durch eine Beispielanwendung geführt, die den Azure Batch-Dienst zum Ausführen einer Workload auf mehreren Computeknoten verwendet und Azure Storage zum Bereitstellen und Abrufen von Workloaddateien nutzt.