---
title: Datenzugriffsrichtlinienbereitstellung für Azure Storage
description: Schritt-für-Schritt-Anleitung zum Integrieren von Azure Storage in Azure Purview-Zugriffsrichtlinien und zum Erstellen von Zugriffsrichtlinien durch Datenbesitzer.
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 11/15/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: eab2c720aafe2cfd5a1ca46f2549b42d6f644b25
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555715"
---
# <a name="dataset-provisioning-by-data-owner-for-azure-storage"></a>Datensatz-Bereitstellung durch Datenbesitzer für Azure Storage

## <a name="supported-capabilities"></a>Unterstützte Funktionen
In diesem Leitfaden wird beschrieben, wie Sie Azure Storage zum Erzwingen von Datenzugriffsrichtlinien konfigurieren, die über Azure Purview erstellt und verwaltet werden. Die Azure Purview-Richtlinienerstellung unterstützt die folgenden Funktionen:
-   Datenzugriffsrichtlinien zum Steuern des Zugriffs auf Daten, die in Blob- oder ADLS Gen2-Dateien (Azure Data Lake Storage) gespeichert sind

> [!IMPORTANT]
> Diese Funktionen befinden sich derzeit in der Vorschau. Diese Vorschauversion wird ohne Servicelevelvereinbarung bereitgestellt und sollte nicht für Produktionsworkloads verwendet werden. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="important-limitations"></a>Wichtige Einschränkungen
1. Die Zugriffsrichtlinienfunktion ist nur für neue Azure Purview- und Azure Storage-Konten verfügbar.
2. Registrieren Sie alle Datenquellen für die Verwendung von Governance, und verwalten Sie alle zugehörigen Zugriffsrichtlinien in einem einzelnen Azure Purview-Konto.
3. Dieses Feature kann nur in den unten aufgeführten Regionen verwendet werden, in denen Verwaltungs- und Erzwingungsfunktionen für Zugriffsrichtlinie bereitgestellt werden.

### <a name="supported-regions"></a>Unterstützte Regionen

#### <a name="azure-purview-management-side"></a>Azure Purview (Verwaltungsseite)
-   Nordeuropa
-   Europa, Westen
-   UK, Süden
-   East US
-   USA (Ost 2)
-   USA Süd Mitte
-   USA, Westen 2
-   Asien, Südosten
-   Australien (Osten)
-   Kanada, Mitte
-   Frankreich, Mitte

#### <a name="azure-storage-enforcement-side"></a>Azure Storage (Erzwingungsseite)
-   Frankreich, Mitte
-   Kanada, Mitte

## <a name="prerequisites"></a>Voraussetzungen
> [!Important]
> Lesen Sie diesem Abschnitt sorgfältig. Es gibt mehrere Voraussetzungen, damit Zugriffsrichtlinien ordnungsgemäß funktionieren.

### <a name="select-an-isolated-test-subscription"></a>Auswählen eines isolierten Testabonnements
Erstellen oder verwenden Sie ein isoliertes Testabonnement und führen Sie die folgenden Schritte aus, um ein neues Azure Storage-Konto und ein neues Azure Purview-Konto in diesem Abonnement zu erstellen.

### <a name="create-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos
Erstellen Sie ein neues Azure Storage-Konto in den oben aufgeführten Regionen. Weitere Informationen finden Sie unter [Azure Storage – Erstellen eines Speicherkontos](../storage/common/storage-account-create.md).

### <a name="configure-azure-storage-to-enforce-access-policies-from-purview"></a>Konfigurieren Azure Storage zum Erzwingen von Zugriffsrichtlinien aus Purview

#### <a name="enable-access-policy-enforcement-in-the-subscription"></a>Aktivieren der Erzwingung von Zugriffsrichtlinie im Abonnement
Führen Sie die folgenden Befehle in PowerShell aus, um sich zu registrieren und zu bestätigen, dass die Zugriffsrichtlinie in dem Abonnement aktiviert ist, in dem sich das Azure Storage-Konto befindet:

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```
Wenn die Ausgabe des letzten Befehls den Wert „RegistrationState“ als „Registriert“ anzeigt, ist Ihr Abonnement für diese Funktionalität aktiviert.

#### <a name="check-access-permissions-in-azure-storage"></a>Überprüfen der Zugriffsberechtigungen in Azure Storage
Ein Benutzer muss die Rolle „Besitzer“ im Azure Storage-Konto haben, um diese Datenquelle später in Azure Purview für Zugriffsrichtlinien zu registrieren: Überprüfen des Zugriffs für einen Benutzer auf [Azure-Ressourcen](../role-based-access-control/check-access.md)

### <a name="create-azure-purview-account"></a>Azure Purview-Konto erstellen
Erstellen Sie ein neues Azure Purview-Konto in den Regionen, in denen die neue Funktionalität aktiviert ist, unter dem isolierten Testabonnement. Informationen zum Erstellen eines neuen Purview-Kontos finden Sie unter  [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal.](create-catalog-portal.md)

### <a name="configure-azure-purview-to-manage-access-policies"></a>Konfigurieren von Azure Purview zum Verwalten von Zugriffsrichtlinien
Führen Sie alle folgenden Schritte aus, um Azure Purview zum Verwalten von Zugriffsrichtlinien zu aktivieren. 

#### <a name="opt-in-to-participate-in-azure-purview-data-use-policy-preview"></a>Anmelden für die Teilnahme an der Vorschau der Azure Purview-Datennutzungsrichtlinie
Diese Funktion befindet sich derzeit in der Vorschauphase, sodass Sie sich für die [Teilnahme an der Vorschau der Purview-Datennutzungsrichtlinie](https://aka.ms/opt-in-data-use-policy) anmelden müssen.

#### <a name="register-purview-as-a-resource-provider-in-other-subscriptions"></a>Registrieren von Purview als Ressourcenanbieter in anderen Abonnements
Führen Sie diesen Schritt nur aus, wenn das Storage Konto, auf das Sie den Zugriff verwalten möchten, sich in einem anderen Abonnement als das Azure Purview-Konto befindet. Registrieren Sie Azure Purview als Ressourcenanbieter in diesen Abonnements, indem Sie diesem Leitfaden folgen:  
[Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md)

#### <a name="configure-permissions-for-policy-management-actions"></a>Konfigurieren von Berechtigungen für Richtlinienverwaltungsaktionen
- Der Benutzer muss sowohl Datenquellenbesitzer als auch Purview-Datenquellenadministrator sein, um eine Quelle für die Governance der Datennutzung zu registrieren. Jede dieser Rollen kann die Registrierung der Quelle für die Datennutzungsgovernance jedoch unabhängig voneinander wieder aufknüpfen.
- Der Benutzer muss die Rolle Autor von Purview-Richtlinien auf Stammsammlungsebene haben, um Richtlinienerstellungs-/Verwaltungsaktionen ausführen zu können.
- Der Benutzer muss die Rolle „Purview-Datenquellenadministrator“ auf Stammsammlungsebene haben, um die Richtlinie zu veröffentlichen.

Weitere Informationen finden Sie im Abschnitt zum Verwalten von Rollenzuweisungen in diesem Handbuch: [Erstellen und Verwalten von Sammlungen](how-to-create-and-manage-collections.md)

Weitere Informationen finden Sie im Abschnitt „Bekannte Probleme“ am Ende dieses Dokuments.

#### <a name="register-and-scan-data-sources-in-purview"></a>Registrieren und Scannen von Azure-Datenquellen
Registrieren und scannen Sie jede Datenquelle mit Purview, um später Zugriffsrichtlinien zu definieren. Befolgen Sie die Anleitungen für die Purview-Registrierung, um Ihr Speicherkonto zu registrieren:

-   [Registrieren und Scannen von Azure Storage-Blobs – Azure Purview](register-scan-azure-blob-storage-source.md)

-   [Registrieren und Scannen von Azure Data Lake Storage (ADLS) Gen2 - Azure Purview](register-scan-adls-gen2.md)

Aktivieren Sie während der Registrierung die Datenquelle für **Datennutzungsgovernance** wie in der Abbildung.

:::image type="content" source="./media/how-to-access-policies-storage/register-data-source-for-policy.png" alt-text="Die Abbildung zeigt, wie sie eine Datenquelle für die Richtlinie registrieren.":::

> [!NOTE]
> Das Verhalten des Umschalters erzwingt, dass alle Datenquellen in einem bestimmten Abonnement nur für die Datennutzungsgovernance in einem einzelnen Purview-Konto registriert werden können. Dieses Purview-Konto selbst kann in einem beliebigen Abonnement im Mandanten angemeldet werden.

## <a name="policy-authoring"></a>Richtlinienerstellung

In diesem Abschnitt werden die Schritte zum Erstellen, Aktualisieren und Veröffentlichen von Purview-Zugriffsrichtlinien beschrieben.

### <a name="create-a-new-policy"></a>Erstellen einer neuen Richtlinie

In diesem Abschnitt werden die Schritte zum Erstellen einer neuen Richtlinie in Azure Purview beschrieben.

1. Melden Sie sich beim Purview-Portal an.

1. Navigieren Sie im linken Bereich zur **Richtlinienverwaltungs**-App.

1. Wählen Sie auf der Richtlinienseite die Schaltfläche **Neue Richtlinie** aus.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-1.png" alt-text="Die Abbildung zeigt, wie ein Datenbesitzer auf die Richtlinienfunktionalität in Azure Purview zugreifen kann, wenn er Richtlinien erstellen möchte.":::

1. Die neue Richtlinienseite wird angezeigt. Geben Sie einen **Namen** und eine **Beschreibung** für die Richtlinie ein.

1. Um der neuen Richtlinie Richtlinienanweisungen hinzuzufügen, wählen Sie die Schaltfläche **Neue Richtlinienanweisungen** aus. Dadurch wird der Richtlinienansweisungs-Generator angezeigt.

    :::image type="content" source="./media/how-to-access-policies-storage/create-new-policy-storage.png" alt-text="Die Abbildung zeigt, wie ein Datenbesitzer eine neue Richtlinienanweisung erstellen kann.":::

1. Wählen Sie die Schaltfläche **Aktion** aus, und wählen Sie in der Dropdownliste Lesen oder Ändern aus.

1. Wählen Sie die Schaltfläche **Effekt** aus, und wählen Sie in der Dropdownliste Zulassen aus.

1. Wählen Sie die Schaltfläche **Datenressourcen** aus, um die Optionen zum Angeben des Datenressourcenpfads anzuzeigen

1. Geben Sie im Feld **Assets** den **Datenquellentyp** ein, und wählen Sie den **Namen** einer zuvor registrierten Datenquelle aus.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-source-type-storage.png" alt-text="Die Abbildung zeigt, wie ein Datenbesitzer beim Bearbeiten einer Richtlinienanweisung eine Datenressource auswählen kann.":::

1. Wählen Sie die Schaltfläche **Weiter** aus, und durchlaufen Sie die Hierarchie, um den Ordner oder die Datei auszuwählen. Wählen Sie dann die Schaltfläche **Hinzufügen** aus. Dadurch gelangen Sie zurück zum Richtlinien-Editor.

    :::image type="content" source="./media/how-to-access-policies-storage/select-asset-storage.png" alt-text="Die Abbildung zeigt, wie ein Datenbesitzer das Asset beim Erstellen oder Bearbeiten einer Richtlinienanweisung auswählen kann.":::

1. Wählen Sie die Schaltfläche **Betreff** aus, und geben Sie die Betreffidentität als Prinzipal, Gruppe oder MSI ein. Wählen Sie anschließend die Schaltfläche **OK** aus. Dadurch gelangen Sie zurück zum Richtlinien-Editor

    :::image type="content" source="./media/how-to-access-policies-storage/select-subject.png" alt-text="Die Abbildung zeigt, wie ein Datenbesitzer beim Erstellen oder Bearbeiten einer Richtlinienanweisung den Betreff auswählen kann.":::

1. Wiederholen Sie die Schritte 5 bis 11, um weitere Richtlinienanweisungen einzugeben.

1. Wählen Sie die Schaltfläche **Speichern** aus, um die Richtlinie zu speichern

### <a name="update-or-delete-a-policy"></a>Aktualisieren oder Löschen einer Richtlinie

Die Schritte zum Erstellen einer neuen Richtlinie in Purview lauten wie folgt.

1. Melden Sie sich beim Purview-Portal an.

1. Navigieren Sie im linken Bereich zur Purview-Richtlinien-App.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="Die Abbildung zeigt, wie ein Datenbesitzer auf die Richtlinienfunktionalität in Azure Purview zugreifen kann, wenn er eine Richtlinie aktualisieren möchte.":::

1. Das Richtlinienportal wird die Liste der in Purview vorhandenen Richtlinien anzeigen. Wählen Sie die Richtlinie aus, die aktualisiert werden muss.

1. Die Seite mit den Richtliniendetails wird angezeigt, einschließlich der Optionen Bearbeiten und Löschen. Wählen Sie die Schaltfläche **Bearbeiten** aus, um den Richtlinienanweisungs-Generator für die Anweisungen in dieser Richtlinie zu öffnen. Jetzt können alle Teile der Anweisungen in dieser Richtlinie aktualisiert werden. Wählen Sie die Schaltfläche **Löschen** aus, um die Richtlinie zu löschen.

    :::image type="content" source="./media/how-to-access-policies-storage/edit-policy-storage.png" alt-text="Die Abbildung zeigt, wie ein Datenbesitzer eine Richtlinienanweisung bearbeiten oder löschen kann.":::

### <a name="publish-the-policy"></a>Veröffentlichen der Richtlinie

Eine neu erstellte Richtlinie befindet sich im Entwurfszustand. Bei der Veröffentlichung wird die neue Richtlinie einer oder mehreren Datenquellen unter Governance zugeordnet.
Die Schritte zum Veröffentlichen einer Richtlinie lauten wie folgt

1. Melden Sie sich beim Purview-Portal an.

1. Navigieren Sie im linken Bereich zur Purview-Richtlinien-App.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="Die Abbildung zeigt, wie ein Datenbesitzer auf die Richtlinienfunktionalität in Azure Purview zugreifen kann, wenn er eine Richtlinie veröffentlichen möchte.":::

1. Das Richtlinienportal wird die Liste der in Purview vorhandenen Richtlinien anzeigen. Suchen Sie die Richtlinie, die veröffentlicht werden muss. Wählen Sie die Schaltfläche **Veröffentlichen** in der rechten oberen Ecke aus.

    :::image type="content" source="./media/how-to-access-policies-storage/publish-policy-storage.png" alt-text="Die Abbildung zeigt, wie ein Datenbesitzer eine Richtlinie veröffentlichen kann.":::

1. Eine Liste der Datenquellen wird angezeigt. Sie können einen Namen eingeben, um die Liste zu filtern. Wählen Sie dann jede Datenquelle aus, in der diese Richtlinie veröffentlicht werden soll, und wählen Sie dann die Schaltfläche **Veröffentlichen** aus.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-sources-publish-policy-storage.png" alt-text="Die Abbildung zeigt, wie ein Datenbesitzer die Datenquelle auswählen kann, in der die Richtlinie veröffentlicht wird.":::

>[!NOTE]
> Veröffentlichen ist ein Hintergrundvorgang. Es kann bis zu **2 Stunden** dauern, bis die Änderungen in der Datenquelle widergespiegelt werden.

## <a name="azure-purview-policy-action-to-azure-storage-action-mapping"></a>Azure Purview-Richtlinienaktion zur Azure Storage Aktionszuordnung

Dieser Abschnitt enthält eine Referenz dazu, wie Aktionen in Azure Purview-Datenrichtlinien bestimmten Aktionen in Azure Storage zugeordnet werden.

| **Purview-Richtlinien-Aktion** | **Datenquellenspezifische Aktionen**                                                        |
|---------------------------|-----------------------------------------------------------------------------------------|
|||
| *Lesen*                    |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/read                      |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                |
|||
| *Modify*                  |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write               |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action          |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action         |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete              |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/read                      |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/write                     |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/delete                    |
|||

## <a name="known-issues"></a>Bekannte Probleme
Hier werden bekannte Probleme in der aktuellen Version aufgeführt:
1. Zusätzlich zur Rolle „Richtlinienautor“ benötigt der Benutzer die Berechtigung „Verzeichnisleseberechtigter“ in Azure Active Directory (AAD), um eine Datenbesitzerrichtlinie zu erstellen.
1. Die Rolle „Richtlinienautor“ reicht nicht aus, um Richtlinien zu erstellen. Außerdem ist die Rolle „Purview Datenquellenadministrator“ erforderlich.

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie den Blog und sehen Sie sich die Demo im Zusammenhang mit den in dieser Anleitung erwähnten Funktionen an.

* [Neuerungen in Azure Purview auf der Microsoft Ignite 2021](https://techcommunity.microsoft.com/t5/azure-purview/what-s-new-in-azure-purview-at-microsoft-ignite-2021/ba-p/2915954)
* [Demo der Zugriffsrichtlinie für Azure Storage](https://www.youtube.com/watch?v=CFE8ltT19Ss)
