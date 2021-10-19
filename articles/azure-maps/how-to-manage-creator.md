---
title: Verwalten von Microsoft Azure Maps Creator
description: In diesem Artikel erfahren Sie, wie Sie Microsoft Azure Maps Creator verwalten.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/08/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 09a70e55d796e0eb16d0806fb9a0726ff67ea812
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729765"
---
# <a name="manage-azure-maps-creator"></a>Verwalten von Azure Maps Creator

Sie können Azure Maps Creator verwenden, um private Gebäudeplandaten zu erstellen. Mit der Azure Maps-API und dem Modul für Gebäudepläne können Sie interaktive und dynamische Gebäudeplan-Webanwendungen entwickeln. Preisinformationen finden Sie unter [Auswählen des richtigen Tarifs in Azure Maps](choose-pricing-tier.md).

In diesem Artikel erfahren Sie Schritt für Schritt, wie Sie eine Creator-Ressource in einem Azure Maps-Konto erstellen und löschen.

## <a name="create-creator-resource"></a>Erstellen einer Creator-Ressource

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)

2. Navigieren Sie zum Menü des Azure-Portals. Wählen Sie **Alle Ressourcen** und dann Ihr Azure Maps-Konto aus.

      :::image type="content" border="true" source="./media/how-to-manage-creator/select-all-resources.png" alt-text="Auswählen des Azure Maps-Kontos":::

3. Wählen Sie im Navigationsbereich **Creator-Übersicht** und dann **Erstellen** aus.

    :::image type="content" border="true" source="./media/how-to-manage-creator/creator-blade-settings.png" alt-text="Azure Maps Creator-Erstellungsseite":::

4. Geben Sie den Namen, den Ort und die Kartenbereitstellungs-Speichereinheiten für Ihre Creator-Ressource ein, und wählen Sie dann **Überprüfen + erstellen** aus.

   :::image type="content" source="./media/how-to-manage-creator/creator-creation-dialog.png" alt-text="Seite zum Eingeben der Creator-Kontoinformationen":::

5. Überprüfen Sie Ihre Einstellungen, und wählen Sie anschließend **Erstellen** aus.

    :::image type="content" source="./media/how-to-manage-creator/creator-create-dialog.png" alt-text="Seite zum Bestätigen der Creator-Kontoeinstellungen":::

    Nach Abschluss der Bereitstellung wird eine Seite mit einer Erfolgs- oder Fehlermeldung angezeigt.

    :::image type="content" source="./media/how-to-manage-creator/creator-resource-created.png" alt-text="Statusseite für die Ressourcenbereitstellung":::

6. Wählen Sie **Zu Ressource wechseln** aus. Auf der Ansichtsseite Ihrer Creator-Ressource werden der Status Ihrer Creator-Ressource und die gewählte demografische Region angezeigt.
      :::image type="content" source="./media/how-to-manage-creator/creator-resource-view.png" alt-text="Creator-Statusseite":::

   >[!NOTE]
   >Um zum Azure Maps-Konto zurückzukehren, wählen Sie **Azure Maps-Konto** im Navigationsbereich aus.

## <a name="delete-creator-resource"></a>Löschen der Creator-Ressource

So löschen Sie die Creator-Ressource:

1. Wählen Sie in Ihrem Azure Maps-Konto unter **Creator** die Option **Übersicht** aus.

2. Wählen Sie **Löschen**.

    >[!WARNING]
    >Wenn Sie die Creator-Ressource Ihres Azure Maps-Kontos löschen, löschen Sie auch die mit Creator-Diensten erstellten Konvertierungen, Datasets, Kachelsets und Featurezustandssets.

     :::image type="content" source="./media/how-to-manage-creator/creator-delete.png" alt-text="Creator-Seite mit Schaltfläche „Löschen“":::

3. Sie werden aufgefordert, den Löschvorgang zu bestätigen, indem Sie den Namen Ihrer Creator-Ressource eingeben. Nachdem die Ressource gelöscht wurde, wird eine Bestätigungsseite angezeigt, die wie folgt aussieht:

     :::image type="content" source="./media/how-to-manage-creator/creator-confirm-delete.png" alt-text="Creator-Seite mit Löschbestätigung":::

## <a name="authentication"></a>Authentifizierung

Von Creator werden die Zugriffssteuerungseinstellungen (IAM) von Azure Maps geerbt. Alle API-Aufrufe für Datenzugriff müssen mit Authentifizierungs- und Autorisierungsregeln gesendet werden.

Creator-Nutzungsdaten fließen in Ihre Azure Maps-Nutzungsdiagramme und in das Aktivitätsprotokoll mit ein.  Weitere Informationen finden Sie unter [Verwalten der Authentifizierung in Azure Maps](./how-to-manage-authentication.md).

>[!Important]
>Es wird empfohlen, Folgendes zu verwenden:
>
> * Azure Active Directory (Azure AD) in allen Lösungen, die mit einem Azure Maps-Konto mit Creator-Diensten erstellt werden. Weitere Informationen zu Azure AD finden Sie unter [Azure AD-Authentifizierung](azure-maps-authentication.md#azure-ad-authentication).
>
>* Einstellungen für rollenbasierte Zugriffssteuerung (RBAC). Mithilfe dieser Einstellungen können Kartenersteller als Rolle „Azure Maps-Datenmitwirkender“ und Azure Maps-Datenbenutzer als Rolle „Azure Maps-Datenleser“ fungieren. Weitere Informationen finden Sie unter [Autorisierung mit rollenbasierter Zugriffssteuerung](azure-maps-authentication.md#authorization-with-role-based-access-control).

## <a name="access-to-creator-services"></a>Zugreifen auf Creator-Dienste

Die Creator-Dienste und Dienste, die in Creator gehostete Daten verwenden (z. B. Renderingdienst), sind über eine geografische URL zugänglich. Die geografische URL wird durch den Standort bestimmt, der bei der Erstellung ausgewählt wird. Wenn Creator also beispielsweise in einer Region an einem geografischen Standort in den USA erstellt wird, müssen alle für den Konvertierungsdienst bestimmten Aufrufe an `us.atlas.microsoft.com/conversions` übermittelt werden. Informationen zum Anzeigen von Zuordnungen der Region zum geografischen Standort finden Sie unter [Geografischer Bereich des Creator-Diensts](creator-geographic-scope.md).

Darüber hinaus sollten alle in Creator importierten Daten an den gleichen geografischen Standort hochgeladen werden, an dem sich auch die Creator-Ressource befindet. Wenn Creator also beispielsweise in den USA bereitgestellt wurde, sollten alle Rohdaten über `us.atlas.microsoft.com/mapData/upload` hochgeladen werden.

## <a name="next-steps"></a>Nächste Schritte

Einführung in Creator-Dienste für Gebäudepläne:

> [!div class="nextstepaction"]
> [Datenupload](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Datenkonvertierung](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Dataset](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Kachelset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Featurezustandsset](creator-indoor-maps.md#feature-statesets)

In den folgenden Artikeln erfahren Sie, wie Sie mithilfe der Creator-Dienste Gebäudepläne in Ihrer Anwendung rendern:

> [!div class="nextstepaction"]
> [Verwenden von Creator zum Erstellen von Gebäudeplänen](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Implementieren dynamischer Stile für Creator-Gebäudepläne](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Verwenden des Moduls „Gebäudepläne“ von Azure Maps](how-to-use-indoor-module.md)
