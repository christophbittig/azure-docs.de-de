---
title: Upgradeinformationen zum Azure Purview-Konto
description: Azure Purview-Konten, die vor dem 18. August 2021 erstellt wurden, werden automatisch auf die neueste Version von Purview geupgradet. In diesem Artikel werden die Änderungen und die nächsten erforderlichen Schritte beschrieben.
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: template-concept
ms.openlocfilehash: b528e4a4b96e0dbe5f8b2eca748420a23f3c2950
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208894"
---
# <a name="azure-purview-account-upgrade-information"></a>Upgradeinformationen zum Azure Purview-Konto

Azure Purview hat am 18. August 2021 einige neue Features veröffentlicht, darunter der elastische Data Map-Dienst, aktualisierte Sammlungen und eine aktualisierte Zugriffssteuerung. Azure Purview-Konten, die **nach** dem 18. August 2021 erstellt wurden, werden mit all diesen neuen Features, die bereits verfügbar sind, erstellt.

Für Konten, die **vor** dem 18. August 2021 (Legacykonten) erstellt wurden, wird automatisch ein Upgrade durchgeführt, um diese Features einzuschließen.

Wenn Sie ein Purview-Konto besitzen, das **vor** dem 18. August 2021 erstellt wurde, erhalten Sie eine E-Mail-Benachrichtigung, sobald für Ihr Purview-Konto ein Upgrade durchgeführt wurde.

In diesem Dokument werden die Änderungen beschrieben, die Ihr geupgradetes Konto aufweist, sowie alle Schritte, die Sie ausführen müssen, um die neuen Features zu verwenden.

## <a name="elastic-data-map"></a>Elastische Data Map

Geupgradete Azure Purview-Konten verwenden ein neues Data Map-System, das dynamisch auf Nutzungsbasis skaliert: der elastische Data Map-Dienst. Elastische Data Map-Systeme beginnen bei einer Kapazitätseinheit und fügen Kapazitätseinheiten nach Bedarf hinzu und reduzieren sie.
Dieses neue Feature beeinflusst nicht, wie Sie mit Purview interagieren, es wirkt sich jedoch auf die Abrechnung aus. Es skaliert auf die Größe, die Sie benötigen, um Ihre Datenlandschaft zu verwalten und Ihnen wird nur in Rechnung gestellt, was Sie verwenden.

Weitere Informationen zum neuen elastischen Data Map-Dienst und dessen Abrechnung finden Sie auf unserer [Seite zum elastischen Data Map-Dienst](concept-elastic-data-map.md).

Abrechnungsdetails für alle Purview-Anwendungen finden Sie im [Preisrechner](https://azure.microsoft.com/pricing/details/azure-purview/).

Wenn für Ihr Konto ein Upgrade durchgeführt wird, müssen Sie keine Änderungen vornehmen, um den elastischen Data Map-Dienst zu verwenden. Sie wird automatisch aktiviert.

## <a name="collections"></a>Auflistungen

Sammlungen sind in Purview-Legacykonten vorhanden, verfügen jedoch über neue Funktionen und werden in geupgradeten Purview-Konten auf andere Weise verwaltet.

[Legacysammlungen](how-to-create-and-manage-collections.md#legacy-collection-guide) waren eine Möglichkeit, Datenquellen und Artefakte in Ihrem Purview-Konto zu organisieren. Sammlungen werden immer noch verwendet, um Ihre Purview-Data Map-Anwendung an Ihre Unternehmenslandschaft anzupassen, jedoch enthalten sie jetzt auch die Zugriffssteuerung. Anstatt den Zugriff allgemein außerhalb Ihrer Data Map-Anwendung zu steuern, stimmt Ihre Access Management-Benutzeroberfläche durch Sammlungen mit Ihrer Data Map-Anwendung überein.

[Sammlungen](how-to-create-and-manage-collections.md) bieten Ihnen die differenzierte Kontrolle über Ihre Datenquellen, aber auch über die Auffindbarkeit. Ihnen werden nur Ressourcen in Sammlungen angezeigt, zu denen Sie Zugriff haben. Daher werden Ihnen nur die benötigten Informationen angezeigt.

Wenn für Ihr Purview-Konto ein Upgrade durchgeführt wird, werden auch Ihre Sammlungen geupgradet. Alle Ihre aktuellen Ressourcen werden in diese Sammlungen migriert. In den folgenden Abschnitten wird beschrieben, wo Sie Ihre Sammlungen und Ihre vorhandenen Ressourcen finden können.

### <a name="locate-and-manage-collections"></a>Suchen und Verwalten von Sammlungen

Um Ihre neuen Sammlungen zu finden, beginnen wir im [Purview Studio](https://web.purview.azure.com/resource/). Sie gelangen zu Purview Studio, indem Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Purview-Ressource wechseln und die Kachel **Purview Studio öffnen** in der Übersicht auswählen.

Wählen Sie im linken Bereich „Data Map“ > „Sammlungen“ aus, um die Seite für die Sammlungsverwaltung zu öffnen.

:::image type="content" source="./media/concept-account-upgrade/find-collections.png" alt-text="Screenshot: Purview Studio mit geöffneter Option „Data Map“ und ausgewählter Registerkarte „Sammlungen“" border="true":::

Dort finden Sie die Stammsammlung sowie alle Ihre vorhandenen Sammlungen. Die Stammsammlung ist die oberste Sammlung auf Ihrer Sammlungsliste und hat denselben Namen wie Ihre Purview-Ressource. In diesem Beispiel lautet der Name der Stammsammlung „Contoso Purview“.

:::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="Screenshot: Purview Studio mit geöffneter Option „Data Map“ und hervorgehobener Stammsammlung" border="true":::

Alle Ihre vorhandenen Sammlungen wurden zu dieser Stammsammlung hinzugefügt und können auf dieser Seite verwaltet werden.
Wählen Sie **+ Sammlung hinzufügen** aus, um eine neue Sammlung anzulegen.

:::image type="content" source="./media/concept-account-upgrade/select-add-a-collection.png" alt-text="Screenshot: Purview Studio mit dem Fenster für neue Sammlungen und hervorgehobenen Schaltflächen zum Hinzufügen einer Sammlung" border="true":::

Wählen Sie auf der Seite der Sammlungsdetails oder dem Dropdownmenü der Sammlung **Bearbeiten** aus, um eine Sammlung zu bearbeiten.

:::image type="content" source="./media/concept-account-upgrade/edit-collection.png" alt-text="Screenshot: Purview Studio mit geöffnetem Sammlungsfenster, mit hervorgehobener Schaltfläche „Bearbeiten“ im Fenster der ausgewählten Sammlung und unterhalb der Schaltfläche mit den Auslassungspunkten neben dem Namen der Sammlung" border="true":::

Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um alle Rollen in einer Sammlung anzuzeigen und die Rollenzuweisungen zu bearbeiten. Nur ein Sammlungsadministrator kann Rollenzuweisungen verwalten. Weitere Informationen zu Berechtigungen in geupgradeten Konten finden Sie [Abschnitt unten](#permissions).

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit hervorgehobener Registerkarte „Rollenzuweisungen“" border="true":::

Weitere Informationen über Sammlungen in geupradeten Konten finden Sie in unserem [Leitfaden zum Erstellen und Verwalten von Sammlungen](how-to-create-and-manage-collections.md).

### <a name="what-happens-to-your-collections-during-upgrade"></a>Was passiert während eines Upgrades mit Ihren Sammlungen?

1. Eine Stammsammlung wird erstellt. Diese Stammsammlung ist die oberste Sammlung auf Ihrer Sammlungsliste und hat denselben Namen wie Ihre Purview-Ressource. In diesem Beispiel lautet der Name der Stammsammlung „Contoso Purview“.

    :::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="Screenshot: Purview Studio mit geöffneter Option „Data Map“ und hervorgehobener Stammsammlung" border="true":::

1. Ihre bereits vorhandenen Sammlungen werden mit der Stammsammlung verbunden. Sie werden unterhalb der Stammsammlung angezeigt. Dort können Sie mit ihnen interagieren.

### <a name="what-happens-to-your-sources-during-upgrade"></a>Was passiert während eines Upgrades mit Ihren Datenquellen?

1. Alle Quellen, die keiner Sammlung zugeordnet waren, werden automatisch zur Stammsammlung hinzugefügt.

1. Alle Quellen oder Überprüfungen, die zuvor einer Sammlung zugeordnet waren, werden den entsprechenden Sammlungen in Ihrem geupgradeten Konto hinzugefügt.

Im geupgradeten Konto sind Ressourcen ebenfalls Sammlungen zugeordnet, jedoch werden sie möglicherweise nicht sofort in Ihrer geupgradeten Sammlung angezeigt. Dies kann zwei Gründe haben:

* Die Ressource wird durch eine geplante Überprüfung überprüft und die nächste Überprüfung wurde noch nicht ausgeführt.
* Die Ressource wurde von einer einmaligen Überprüfung überprüft.

> [!NOTE]
> Ressourcen werden einer Sammlung während des Überprüfungsprozesses hinzugefügt, sodass eine weitere Überprüfung Ihrer Ressourcen ausgeführt werden muss, um sie diesen neuen Sammlungen hinzuzufügen.

Bei Ihren geplanten Überprüfungen müssen Sie lediglich auf die nächste Ausführung dieser Überprüfungen warten. Die Ressourcen werden Ihrer Sammlung hinzugefügt.

Sie müssen einmalige Überprüfungen manuell erneut ausführen, um die Ressourcen in Ihrer Sammlung aufzufüllen.

1. Öffnen Sie den Data Map-Dienst im [Purview Studio](https://web.purview.azure.com/resource/), und wählen Sie **Quellen** aus. Wählen Sie die Quelle aus, die Sie überprüfen möchten.

    :::image type="content" source="./media/concept-account-upgrade/select-sources.png" alt-text="Screenshot des Purview Studio-Fensters, in dem der Data Map-Dienst geöffnet ist und die Quellen hervorgehoben sind" border="true":::

1. Wählen Sie die Registerkarte **Überprüfungen** und dann die Überprüfung aus, die Sie erneut ausführen möchten.

    :::image type="content" source="./media/concept-account-upgrade/select-scan.png" alt-text="Screenshot des Purview Studio-Fensters, in dem eine Quelle geöffnet ist und die Überprüfungen hervorgehoben sind" border="true":::

1. Wählen Sie **Überprüfung ausführen** aus, um die Überprüfung erneut auszuführen.

    :::image type="content" source="./media/concept-account-upgrade/run-scan-now.png" alt-text="Screenshot des Purview Studio-Fensters, in dem eine Überprüfung geöffnet und „Überprüfung ausführen“ hervorgehoben ist" border="true":::

### <a name="what-happens-when-your-upgraded-account-doesnt-have-a-collection-admin"></a>Was passiert, wenn Ihr geupgradetes Konto keine*n Sammlungsadministrator*in hat?

Ihr geupgradetes Purview-Konto hat standardmäßige Sammlungsadministrator*innen, wenn der Prozess mindestens eine*n Benutzer*in oder eine Gruppe in der folgenden Reihenfolge identifizieren kann: 

1. Benutzer*in (explizit zugewiesen)

1. Benutzerzugriffsadministrator*in (explizit zugewiesen)

1. Datenquellenadministrator*in und Datenkurator*in

Wenn Ihr Konto keine*n Benutzer*in oder Gruppen hat, die mit den oben genannten Kriterien übereinstimmen, hat das geupgradete Purview-Konto keine*n Sammlungsadministrator*in. 

Sie können mit der Verwaltungs-API weiterhin manuell eine*n Sammlungsadministrator*in hinzufügen. Beim Aufrufen dieser API benötigen Sie die Berechtigung „Besitzer“ oder „Benutzerzugriffsadministrator“ für das Purview-Konto, um eine Schreibaktion auszuführen. Sie müssen die `objectId`des neuen Sammlungsadministrators bzw. der neuen Sammlungsadministratorin kennen, die über die API übermittelt werden soll.

#### <a name="request"></a>Anforderung

   ```
    POST https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Purview/accounts/<accountName>/addRootCollectionAdmin?api-version=2021-07-01
   ```    
    
#### <a name="request-body"></a>Anforderungstext

   ```json
    {
        "objectId": "<objectId>"
    }
   ```    

`objectId` ist die Objekt-ID des neuen Sammlungsadministrators bzw. der neuen Sammlungsadministratorin.

#### <a name="response-body"></a>Antworttext

Bei Erfolg erhalten Sie eine Antwort mit leerem Text mit dem Code `200`.

Bei einem Fehler ist der Antworttext der folgende:

   ```json
    {
        "error": {
            "code": "19000",
            "message": "The caller does not have Microsoft.Authorization/roleAssignments/write permission on resource: [/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>].",
            "target": null,
            "details": []
        }
    }
   ```

## <a name="permissions"></a>Berechtigungen

In geupgradeten Purview-Konten werden Berechtigungen durch Sammlungen verwaltet.

> [!NOTE]
> Berechtigungen in geupgradeten Konten werden nicht mehr über Access Control (IAM) verwaltet. Wenn für ein Legacykonto ein Upgrade durchgeführt wird, werden bereits vorhandene IAM-Berechtigungen weiterhin unter Access Control IAM angezeigt, jedoch wirken sie sich nicht mehr auf den Zugriff in Purview aus.

Wenn für ein Legacykonto ein Upgrade durchgeführt wurde, werden alle in Access Control (IAM) zugewiesenen Berechtigungen der Stammsammlung einer der folgenden Rollen zugewiesen.

* **Sammlungsadministratoren** können eine Sammlung und ihre Details bearbeiten, den Zugriff in der Sammlung verwalten und untergeordnete Sammlungen hinzufügen.
* **Datenquellenadministratoren** können Datenquellen und Datenüberprüfungen verwalten.
* **Datenkuratoren** können Aktionen für Katalogdatenobjekte erstellen, lesen, ändern und löschen.
* **Datenleser** können auf Katalogdatenobjekte zugreifen, diese aber nicht ändern.

Um Ihren Benutzer*innen Zugriff auf Quellen und Artefakte in Purview zu gewähren, sollten Sie ihnen den Zugriff auf Sammlungen und untergeordnete Sammlungen ermöglichen, für die sie möglicherweise Zugriff auf die Daten benötigen.

> [!NOTE]
> Nur Sammlungsadministrator*innen haben die Berechtigung, den Zugriff auf Sammlungen zu verwalten.

Wählen Sie die Registerkarte **Rollenzuweisungen** in Ihrer Sammlung aus, um die Rollenzuweisungen anzuzeigen oder zu bearbeiten.

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit hervorgehobener Registerkarte „Rollenzuweisungen“" border="true":::

Weitere Informationen zum Verwalten von Berechtigungen in einem geupgradeten Purview-Konto finden Sie in unseren [Leitfaden zu Purview-Berechtigungen](catalog-permissions.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den oben genannten Konzepten oder zum Sichern Ihrer Purview-Ressource finden Sie unter den folgenden Links.

* [Private Endpunkt mit Azure Purview](catalog-private-link.md)
* [Elastische Data Map](concept-elastic-data-map.md)
* [Leitfaden zu Berechtigungen in Purview](catalog-permissions.md)
