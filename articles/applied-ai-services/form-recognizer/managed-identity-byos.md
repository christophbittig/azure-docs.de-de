---
title: Erstellen und Verwenden einer verwalteten Identität mit BYOS (Bring Your Own Storage)
titleSuffix: Azure Applied AI Services
description: Informationen zum Erstellen und Verwenden einer verwalteten Identität mit BYOS-Konten
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 83b8867ae750fe85bc848e339ddb0409cba85093
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400602"
---
# <a name="create-and-use-managed-identity-for-your-form-recognizer-resource"></a>Erstellen und Verwenden einer verwalteten Identität für Ihre Formularerkennungsressource

> [!IMPORTANT]
> Die Zuweisung der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) befindet sich derzeit in der Vorschauversion und wird für Produktionsworkloads nicht empfohlen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Azure RBAC-Zuweisungen werden verwendet, um Berechtigungen für die verwaltete Identität zu erteilen.

## <a name="what-is-managed-identity"></a>Was ist eine verwaltete Identität?

Eine verwaltete Azure-Identität ist ein Dienstprinzipal, der eine Azure Active Directory-Identität und bestimmte Berechtigungen für von Azure verwaltete Ressourcen erstellt. Sie können eine verwaltete Identität verwenden, um Zugriff auf eine beliebige Ressource zu gewähren, die die Azure AD-Authentifizierung unterstützt. Zum Gewähren des Zugriffs weisen Sie einer verwalteten Identität mithilfe der [rollenbasierten Zugriffssteuerung von Azure](../../role-based-access-control/overview.md) (Azure RBAC) eine Rolle zu.  Es entstehen keine zusätzlichen Kosten für die Verwendung der verwalteten Identität in Azure.

Die verwaltete Identität unterstützt sowohl private als auch öffentlich zugängliche Azure Blob Storage-Konten.  Für Speicherkonten mit öffentlichem Zugriff können Sie eine Shared Access Signature (SAS) verwenden, um eingeschränkten Zugriff zu gewähren.   In diesem Artikel erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität für Ihre Formularerkennungsinstanz aktivieren.

## <a name="private-storage-account-access"></a>Zugriff auf privates Speicherkonto
> [!NOTE]
>
> Azure-Formularerkennung unterstützt derzeit nur vom System zugewiesene verwaltete Identitäten. Vom Benutzer zugewiesene verwaltete Identitäten sind auf der Roadmap und werden in naher Zukunft aktiviert. 


 Der Zugriff auf das private Azure-Speicherkonto und die Authentifizierung werden von [verwalteten Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) unterstützt. Wenn Sie über ein Azure-Speicherkonto verfügen, das durch ein virtuelles Netzwerk (VNet) oder eine Firewall geschützt ist oder BYOS (Bring-Your-Own-Storage) aktiviert hat, kann die Formularerkennung nicht direkt auf Ihre Speicherkontodaten zugreifen. Sobald jedoch eine verwaltete Identität aktiviert ist, kann der Formularerkennungsdienst mithilfe der zugewiesenen Anmeldeinformationen für die verwaltete Identität auf Ihr Speicherkonto zugreifen.

> [!NOTE]
>
> * Wenn Sie Ihre Speicherdaten mit dem [**Tool für die Beschriftung von Beispielen für die Formularerkennung**](https://fott-2-1.azurewebsites.net/) analysieren möchten, müssen Sie das Tool hinter Ihrem VNet oder Ihrer Firewall bereitstellen.
>
> * Die APIs für die Analyse von [**Quittungen**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync), [**Visitenkarten**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync), [**Rechnungen**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291), [**Ausweisdokumenten**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707) und [**benutzerdefinierten Formularen**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) können Daten aus einem einzelnen Dokument extrahieren, indem sie Anforderungen in Form von unformatierten binären Inhalten übermitteln. In diesen Szenarien sind keine Anmeldeinformationen für die verwaltete Identität erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein aktives [**Azure-Konto**](https://azure.microsoft.com/free/cognitive-services/) – Sollten Sie über kein aktives Konto verfügen, können Sie ein [**kostenloses Konto erstellen**](https://azure.microsoft.com/free/).

* Eine [**Formularerkennungs**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)- oder [**Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)-Ressource im Azure-Portal. Ausführliche Schritte _finden_ Sie unter [Erstellen einer Cognitive Services-Ressource mithilfe des Azure-Portals](../../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows).

* Ein [**Azure Blob Storage-Konto**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) in derselben Region wie Ihre Formularerkennung-Ressource. Sie erstellen Container zum Speichern und Organisieren Ihrer Blobdaten unter Ihrem Speicherkonto. 

  * Wenn sich Ihr Speicherkonto hinter einer Firewall befindet, **müssen Sie die folgende Konfiguration aktivieren**: </br></br>

  * Klicken Sie auf der Seite Ihres Speicherkontos im linken Menü auf **Sicherheit + Netzwerkbetrieb** → **Netzwerk**.
    :::image type="content" source="media/managed-identities/security-and-networking-node.png" alt-text="Screenshot: Registerkarte „Sicherheit + Netzwerkbetrieb“":::

  * Klicken Sie im Hauptfenster auf **Allow access from selected networks** (Zugriff aus bestimmten Netzwerken zulassen).
  :::image type="content" source="media/managed-identities/firewalls-and-virtual-networks.png" alt-text="Screenshot: Aktiviertes Optionsfeld „Ausgewählte Netzwerke“":::

  * Navigieren Sie auf der Seite „Ausgewählte Netzwerke“ zur Kategorie **Ausnahmen**, und stellen Sie sicher, dass das Kontrollkästchen [**Azure-Diensten in der Liste der vertrauenswürdigen Dienste den Zugriff auf dieses Speicherkonto erlauben**](../../storage/common/storage-network-security.md?tabs=azure-portal#manage-exceptions) aktiviert ist.

    :::image type="content" source="media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="Screenshot: Kontrollkästchen für das Zulassen vertrauenswürdiger Dienste, Portalansicht":::
* Eine kurze Übersicht über die [**rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)**](../../role-based-access-control/role-assignments-portal.md) über das Azure-Portal.

## <a name="managed-identity-assignments"></a>Zuweisungen verwalteter Identitäten

Es gibt zwei Arten von verwalteten Identitäten: **systemseitig** und **benutzerseitig** zugewiesene Identitäten. Derzeit wird die Formularerkennung von der systemseitig zugewiesenen verwalteten Identität unterstützt. Eine systemseitig zugewiesene verwaltete Identität wird direkt für eine Dienstinstanz **aktiviert**. Sie ist nicht standardmäßig aktiviert. Sie müssen zu Ihrer Ressource wechseln und die Identitätseinstellung aktualisieren. Die systemseitig zugewiesene verwaltete Identität ist während des gesamten Lebenszyklus an Ihre Ressource gebunden. Wenn Sie Ihre Ressource löschen, wird auch die verwaltete Identität gelöscht.

In den folgenden Schritten aktivieren wir eine systemseitig zugewiesene verwaltete Identität und gewähren der Formularerkennung begrenzten Zugriff auf Ihr Azure Blob Storage-Konto.

## <a name="enable-a-system-assigned-managed-identity"></a>Aktivieren einer systemseitig zugewiesenen verwalteten Identität

>[!IMPORTANT]
>
> Um eine systemseitig zugewiesene verwaltete Identität zu aktivieren, benötigen Sie **Microsoft.Authorization/roleAssignments/write**-Berechtigungen, z. B. [**Besitzer**](../../role-based-access-control/built-in-roles.md#owner) oder [**Benutzerzugriffsadministrator**](../../role-based-access-control/built-in-roles.md#user-access-administrator). Sie können einen Bereich auf vier Ebenen angeben: Verwaltungsgruppe, Abonnement, Ressourcengruppe oder Ressource.

1. Melden Sie sich mit einem Konto, das Ihrem Azure-Abonnement zugeordnet ist, beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu Ihrer **Formularerkennung**-Ressourcenseite im Azure-Portal.

1. Wählen Sie in der linken Leiste die Option **Identität** aus der Liste **Ressourcenverwaltung** aus:

    :::image type="content" source="media/managed-identities/resource-management-identity-tab.png" alt-text="Screenshot: Registerkarte „Ressourcenverwaltungsidentität“ im Azure-Portal":::

1. Schalten Sie im Hauptfenster die Registerkarte **Vom System zugewiesener Status** auf **Ein**.

1. Wählen Sie unter **Berechtigungen** die Option **Azure-Rollenzuweisungen** aus:

    :::image type="content" source="media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="Screenshot: Aktivieren der systemseitig zugewiesenen verwalteten Identität im Azure-Portal":::

1. Eine Seite mit Azure-Rollenzuweisungen wird geöffnet. Wählen Sie Ihr Abonnement aus der Dropdownliste und dann die Option **&plus; Rollenzuweisung hinzufügen** aus.

    :::image type="content" source="media/managed-identities/azure-role-assignments-page-portal.png" alt-text="Screenshot: Seite „Azure-Rollenzuweisungen“ im Azure-Portal":::

    > [!NOTE]
    >
    > Wenn Sie im Azure-Portal keine Rolle zuweisen können, da die Option „Hinzufügen > Rollenzuweisung hinzufügen“ deaktiviert ist oder der Berechtigungsfehler „Sie verfügen nicht über die Berechtigung zum Hinzufügen einer Rollenzuweisung in diesem Bereich“ angezeigt wird, überprüfen Sie, ob Sie derzeit als Benutzer mit einer zugewiesenen Rolle angemeldet sind, die über Microsoft.Authorization/roleAssignments/write-Berechtigungen wie „Besitzer“ oder „Benutzerzugriffsadministrator“ im Speicherbereich für die Speicherressource verfügt.

 7. Als nächstes werden Sie Ihrer Dienstressource für die Formularerkennung eine **Leser von Speicherblobdaten**-Rolle zuweisen. Füllen Sie im Popupfenster **Rollenzuweisung hinzufügen** die Felder wie folgt aus, und wählen Sie **Speichern** aus:

    | Feld | Wert|
    |------|--------|
    |**Bereich**| **_Storage_**|
    |**Abonnement**| **_Das Ihrer Speicherressource zugeordnete Abonnement_**.|
    |**Ressource**| **_Der Name Ihrer Speicherressource_**|
    |**Rolle** | **_Leser von Speicherblobdaten_**: Ermöglicht den Lesezugriff auf Azure Storage-Blobcontainer und -Daten.|

     :::image type="content" source="media/managed-identities/add-role-assignment-window.png" alt-text="Screenshot: Seite „Azure-Rollenzuweisungen hinzufügen“ im Azure-Portal":::

1. Nachdem Sie die Bestätigungsmeldung für die _hinzugefügte Rollenzuweisung_ erhalten haben, aktualisieren Sie die Seite, um die hinzugefügte Rollenzuweisung anzuzeigen.

    :::image type="content" source="media/managed-identities/add-role-assignment-confirmation.png" alt-text="Screenshot: Popupmeldung zur Bestätigung der hinzugefügten Rollenzuweisung":::

1. Wenn die Änderung nicht sofort angezeigt wird, warten Sie und versuchen Sie, die Seite noch einmal zu aktualisieren. Beim Zuweisen oder Entfernen von Rollenzuweisungen kann es bis zu 30 Minuten dauern, bis Änderungen wirksam werden.

    :::image type="content" source="media/managed-identities/assigned-roles-window.png" alt-text="Screenshot: Fenster „Azure-Rollenzuweisungen“":::

 Das ist alles! Sie haben die Schritte zum Aktivieren einer systemseitig zugewiesenen verwalteten Identität abgeschlossen. Mit diesen Identitätsanmeldeinformationen können Sie der Formularerkennung bestimmte Zugriffsrechte für Dokumente und Dateien gewähren, die in Ihrem BYOS-Konto gespeichert sind.

## <a name="learn-more-about--managed-identity"></a>Weitere Informationen zur verwalteten Identität

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen zu verwalteten Identitäten für Azure-Ressourcen – Azure AD](../../active-directory/managed-identities-azure-resources/managed-identities-faq.md)
