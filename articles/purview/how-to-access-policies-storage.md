---
title: Datenzugriffsrichtlinienbereitstellung für Azure Storage
description: Schritt-für-Schritt-Anleitung zum Integrieren von Azure Storage in Azure Purview-Zugriffsrichtlinien und zum Erstellen von Zugriffsrichtlinien durch Datenbesitzer.
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: cbd6214687e23b90d0c289dd5a34d9410b7f092a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021358"
---
# <a name="dataset-provisioning-by-data-owner-for-azure-storage"></a>Datensatz-Bereitstellung durch Datenbesitzer für Azure Storage

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Purview-Richtlinienerstellung unterstützt die folgenden Funktionen:
-   Datenzugriffsrichtlinie für Azure Storage zum Steuern des Zugriffs auf Daten, die in Blob- oder ADLS Gen2-Dateien gespeichert sind

> [!IMPORTANT]
> Diese Funktionen befinden sich derzeit in der Vorschau. Diese Vorschauversion wird ohne Servicelevelvereinbarung bereitgestellt und sollte nicht für Produktionsworkloads verwendet werden. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

### <a name="provision-new-accounts-in-an-isolated-test-subscription"></a>Bereitstellen neuer Konten in einem isolierten Testabonnement
Führen Sie die folgenden Schritte aus, um ein neues Azure Purview-Konto und ein neues Azure Storage-Konto in einem isolierten Testabonnement zu erstellen. Aktivieren Sie dann die Zugriffsrichtlinienfunktionalität in diesen Konten.

## <a name="supported-regions"></a>Unterstützte Regionen

> [!IMPORTANT]
> 1. Die Zugriffsrichtlinienfunktion ist nur für neue Azure Purview- und Azure Storage-Konten verfügbar.
> 2. Dieses Feature kann nur in den unten aufgeführten Regionen verwendet werden, in denen die Zugriffsrichtlinienfunktionalität bereitgestellt wird.

### <a name="azure-purview"></a>Azure Purview 

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


### <a name="azure-storage"></a>Azure Storage

-   Frankreich, Mitte
-   Kanada, Mitte


### <a name="create-azure-purview-account"></a>Azure Purview-Konto erstellen

Erstellen Sie ein neues Azure Purview-Konto in den Regionen, in denen die neue Funktionalität aktiviert ist, unter dem Abonnement, das für die neue Funktionalität isoliert ist.

Informationen zum Erstellen eines neuen Purview-Kontos finden Sie unter  [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal.](create-catalog-portal.md)

### <a name="create-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

Informationen zum Erstellen eines neuen Azure Storage-Kontos finden Sie unter [Erstellen eines Speicherkontos - Azure Storage](../storage/common/storage-account-create.md)

### <a name="configure-azure-purview-and-storage-for-access-policies"></a>Konfigurieren von Azure Purview und Storage für Zugriffsrichtlinien

In diesem Abschnitt werden die Schritte zum Konfigurieren von Azure Purview und Storage zum Aktivieren von Zugriffsrichtlinien beschrieben.

#### <a name="register-the-access-policies-functionality-in-azure-storage"></a>Registrieren der Zugriffsrichtlinienfunktionen in Azure Storage

Führen Sie die folgenden Befehle in PowerShell aus, um sich zu registrieren und zu bestätigen, dass diese Funktionalität für Ihr Abonnement aktiviert ist

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```

Wenn die Ausgabe des letzten Befehls den Wert „RegistrationState“ als „Registriert“ anzeigt, ist Ihr Abonnement für diese Funktionalität aktiviert.

#### <a name="register-and-scan-data-sources-in-purview"></a>Registrieren und Scannen von Azure-Datenquellen

Die Datenquelle muss mit Purview registriert und überprüft werden, um Richtlinien zu definieren. Befolgen Sie die Anleitungen für die Purview-Registrierung, um Ihr Speicherkonto zu registrieren:

-   [Scannen von Azure Storage-Blobs - Azure Purview](register-scan-azure-blob-storage-source.md)

-   [Registrieren und Scannen von Azure Data Lake Storage (ADLS) Gen2 - Azure Purview](register-scan-adls-gen2.md)

Aktivieren Sie während der Registrierung die Datenquelle für „Data use governance“, wie in der Abbildung dargestellt

:::image type="content" source="./media/how-to-access-policies-storage/register-data-source-for-policy.png" alt-text="Die Abbildung zeigt, wie sie eine Datenquelle für die Richtlinie registrieren.":::

#### <a name="configure-permissions-for-policy-management-actions"></a>Konfigurieren von Berechtigungen für Richtlinienverwaltungsaktionen

-   Ein Benutzer muss Teil der Rolle „Datenkurator für Purview“ sein, um Richtlinienerstellungs-/Verwaltungsaktionen auszuführen.
-   Ein Benutzer muss Teil der Rolle „Datenquellenadministrator“ sein, um die Richtlinie zu veröffentlichen.

Weitere Informationen finden Sie im Abschnitt zum Verwalten von Rollenzuweisungen in diesem Handbuch: [Erstellen und Verwalten von Sammlungen](how-to-create-and-manage-collections.md)

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

1. Eine Liste der Datenquellen wird angezeigt. Sie können einen Namen eingeben, um die Liste zu filtern. Wählen Sie dann jede Datenquelle aus, in der diese Richtlinie veröffentlicht werden soll, und wählen Sie dann die Schaltfläche **Veröffentlichen** aus. Veröffentlichen ist ein Hintergrundvorgang. Es kann bis zu 2 Stunden dauern, bis die Änderungen in der Datenquelle widergespiegelt werden.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-sources-publish-policy-storage.png" alt-text="Die Abbildung zeigt, wie ein Datenbesitzer die Datenquelle auswählen kann, in der die Richtlinie veröffentlicht wird.":::

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie diese Artikel, um die Konzepte im Zusammenhang mit Zugriffsrichtlinien zu verstehen:

* [Konzepte für Azure Purview-Zugriffsrichtlinien](concept-data-policies.md)
* [Konzepte für Azure Purview Self-Service-Datenermittlung und -Zugriff](concept-self-service.md)
