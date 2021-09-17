---
title: Erstellen von Plänen für Dynamics 365 for Customer Engagement und Power Apps in Microsoft AppSource (Azure Marketplace)
description: Konfigurieren Sie Angebotspläne für Dynamics 365 for Customer Engagement und Power Apps, wenn Sie für Ihr Angebot die Drittanbieter-App-Verwaltung aktiviert haben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/30/2021
ms.openlocfilehash: 086457e916f3eeef41023efe56cc011e714ab343
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866328"
---
# <a name="create-dynamics-365-for-customer-engagement--power-apps-plans"></a>Erstellen von Plänen für Dynamics 365 for Customer Engagement und Power Apps

Wenn Sie die App-Lizenzverwaltung für Ihr Angebot aktiviert haben, sieht die Registerkarte **Planübersicht** wie im folgenden Screenshot aus. Navigieren Sie andernfalls zu [Einrichtung von Dynamics 365 for Customer Engagement & PowerApps bietet technische Konfiguration](dynamics-365-customer-engage-technical-configuration.md).

:::image type="content" source="./media/third-party-license/plan-tab.png" alt-text="Screenshot: Registerkarte „Planübersicht“ eines Angebots für Dynamics 365 for Customer Engagement und Power Apps, für das die Drittanbieter-App-Lizenzierung aktiviert wurde":::

Sie müssen mindestens einen Plan definieren, wenn für Ihr Angebot die App-Lizenzverwaltung aktiviert ist. Sie können eine Vielzahl von Plänen mit unterschiedlichen Optionen für das gleiche Angebot erstellen. Diese Pläne (auch SKUs genannt) können sich in Bezug auf Monetarisierung und Dienstebenen unterscheiden. Die Dienst-IDs dieser Pläne werden später in Ihrem Lösungspaket zugeordnet, um anhand dieser Pläne zur Laufzeit eine Lizenzprüfung durch die Dynamics-Plattform zu ermöglichen. Die Dienst-ID jedes Plans wird in Ihrem Lösungspaket zugeordnet. Dadurch kann von der Dynamics-Plattform eine Lizenzprüfung anhand dieser Pläne durchgeführt werden.

## <a name="create-a-plan"></a>Erstellen von Plänen

1. Wählen Sie oben auf der Seite **Planübersicht** die Option **+ Neuen Plan erstellen** aus.
1. Geben Sie im angezeigten Dialogfeld im Feld **Plan-ID** eine eindeutige Plan-ID ein. Verwenden Sie bis zu 50 Kleinbuchstaben, Ziffern, Bindestriche und Unterstriche. Die Plan-ID kann nicht mehr geändert werden, nachdem Sie **Erstellen** ausgewählt haben.
1. Geben Sie im Feld **Planname** einen eindeutigen Namen für diesen Plan ein. Es sind maximal 50 Zeichen zulässig.
1. Klicken Sie auf **Erstellen**.

## <a name="define-the-plan-listing"></a>Definieren des Planlistings

Auf der Registerkarte **Planlisting** können Sie den Plannamen und die Beschreibung so definieren, wie sie im kommerziellen Marketplace angezeigt werden sollen. Diese Informationen werden auf der Microsoft AppSource-Angebotsseite angezeigt.

1. Im Feld **Planname** wird der Name angezeigt, den Sie zuvor für diesen Plan angegeben haben. Sie können diesen jederzeit ändern. Dieser Name wird als Titel des Softwareplans für Ihr Angebot im kommerziellen Marketplace angezeigt.
1. Erläutern Sie im Feld **Planbeschreibung**, was diesen Softwareplan einzigartig macht und von anderen Softwareplänen in Ihrem Angebot unterscheidet. Diese Beschreibung kann bis zu 500 Zeichen umfassen.
1. Wählen Sie **Entwurf speichern** und dann oben links die Option **Planübersicht** aus.

    :::image type="content" source="./media/third-party-license/bronze-plan.png" alt-text="Screenshot: Link „Planübersicht“ auf der Seite „Planlisting“ eines Angebots in Partner Center":::

1. Wählen Sie im oberen Bereich der Seite **Planübersicht** die Option **+ Neuen Plan erstellen** aus, um einen weiteren Plan für dieses Angebot zu erstellen. Wiederholen Sie dann die Schritte im Abschnitt [Erstellen von Plänen](#create-a-plan). Wenn Sie keine weiteren Pläne erstellen möchten, fahren Sie mit dem nächsten Abschnitt fort.

## <a name="copy-the-service-ids"></a>Kopieren der Dienst-IDs

Sie müssen die Dienst-ID jedes erstellten Plans kopieren, um sie im nächsten Schritt Ihrem Lösungspaket zuordnen zu können.

- Kopieren Sie für jeden erstellten Plan die Dienst-ID, und speichern Sie sie an einem sicheren Ort. Die IDs werden im nächsten Schritt Ihrem Lösungspaket hinzugefügt. Die Dienst-ID ist auf der Seite **Planübersicht** im Format `ISV name.offer name.plan ID` angegeben. Beispiel: Fabrikam.F365.bronze.

    :::image type="content" source="./media/third-party-license/service-id.png" alt-text="Screenshot: Seite „Planübersicht“ mit hervorgehobener Dienst-ID für den Plan":::

## <a name="add-service-ids-to-your-solution-package"></a>Hinzufügen von Dienst-IDs zu Ihrem Lösungspaket

1. Fügen Sie Ihrem Lösungspaket die Dienst-IDs hinzu, die Sie im vorherigen Schritt kopiert haben. Eine entsprechende Anleitung finden Sie unter [Anhang: Hinzufügen von Lizenzinformationen zu Ihrer Lösung](/powerapps/developer/data-platform/appendix-add-license-information-to-your-solution) sowie unter [Schritt 3: Erstellen eines AppSource-Pakets für die App](/powerapps/developer/data-platform/create-package-app-appsource).
1. Nachdem Sie die ZIP-Datei des CRM-Pakets erstellt haben, laden Sie sie in Azure Blob Storage hoch. Sie müssen die SAS-URL des Azure Blob Storage-Kontos angeben, das die hochgeladene ZIP-Datei des CRM-Pakets enthält.

## <a name="next-steps"></a>Nächste Schritte

- Unter [Einrichtung von Dynamics 365 for Customer Engagement & PowerApps bietet technische Konfiguration](dynamics-365-customer-engage-technical-configuration.md) erfahren Sie, wie Sie das Lösungspaket in Ihr Angebot hochladen.
