---
title: 'Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie ein Azure Purview-Konto erstellen und Berechtigungen für dessen Verwendung konfigurieren.
author: nayenama
ms.author: nayenama
ms.date: 08/18/2021
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-portal
ms.openlocfilehash: 4f0ef5010a0862b1fa5514d83f6570eefa2c4e10
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102531"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal.

Azure Purview ist ein einheitliches Data Governance-Tool, das Ihnen bei der Verwaltung Ihrer Datenlandschaft hilft. In dieser Schnellstartanleitung werden die Schritte zum Erstellen eines Azure Purview-Kontos im Azure-Portal und zum Einstieg in das Klassifizieren, Sichern und Entdecken Ihrer Daten in Purview beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Das Benutzerkonto, mit dem Sie sich bei Azure anmelden, muss „Mitwirkender“, „Besitzer“ oder ein „Administrator“ des Azure-Abonnements sein.

* Sie verfügen über einen eigenen [Azure Active Directory-Mandanten](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Keine [Azure-Richtlinien](../governance/policy/overview.md), die das Erstellen von **Speicherkonten** oder **Event Hub-Namespaces** verhindern. Purview stellt bei seiner Erstellung ein verwaltetes Speicherkonto und einen Event Hub bereit. Wenn eine blockierende Richtlinie vorhanden ist und bestehen bleiben muss, befolgen Sie unsere [Anleitung zum Purview-Ausnahmetag](create-purview-portal-faq.md), um Ihre Umgebung vorzubereiten.

## <a name="create-an-azure-purview-account"></a>Erstellen eines Azure Purview-Kontos

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Seite **Purview accounts** (Purview-Konten).

    :::image type="content" source="media/create-catalog-portal/purview-accounts-page.png" alt-text="Screenshot der Seite „Purview accounts“ (Purview-Konten) im Azure-Portal.":::

1. Wählen Sie **Erstellen** aus, um ein neues Azure Purview-Konto zu erstellen.

   :::image type="content" source="media/create-catalog-portal/select-create.png" alt-text="Screenshot mit hervorgehobener Schaltfläche „Erstellen“ für ein Azure Purview-Konto im Azure-Portal.":::
  
      Alternativ können Sie auch zur Marketplace-Suche für **Azure Purview** navigieren und **Erstellen** auswählen.

     :::image type="content" source="media/create-catalog-portal/search-marketplace.png" alt-text="Screenshot von Azure Purview im Azure Marketplace mit hervorgehobener Schaltfläche „Erstellen“.":::

1. Wählen Sie auf der neuen Seite „Purview-Konto erstellen“ auf der Registerkarte **Grundlagen** das Azure-Abonnement aus, in dem Sie Ihr Purview-Konto erstellen möchten.

1. Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue, die Ihr Purview-Konto aufnehmen soll.

    Weitere Informationen zu Ressourcengruppen finden Sie in unserem Artikel [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

1. Geben Sie einen **Purview-Kontonamen** ein. Leerzeichen und Symbole sind nicht zulässig.
    Der Name des Purview-Kontos muss global eindeutig sein. Wenn folgender Fehler angezeigt wird, ändern Sie den Namen des Purview-Kontos, wiederholen Sie den Erstellungsvorgang.

    :::image type="content" source="media/create-catalog-portal/name-error.png" alt-text="Screenshot des Bildschirms „Purview-Konto erstellen“ mit einem bereits verwendeten Kontonamen und hervorgehobener Fehlermeldung.":::

1. Wählen Sie einen **Standort** aus.
    In der Liste werden nur Standorte angezeigt, die Purview unterstützen. Der Speicherort, den Sie auswählen, ist die Region, in der Ihr Purview-Konto und die Metadaten gespeichert werden. Quellen können sich in anderen Regionen befinden.

      > [!Note]
      > Azure Purview unterstützt kein regionsübergreifendes Verschieben von Konten. Stellen Sie also sicher, dass Sie in der richtigen Region bereitstellen. Weitere Informationen hierzu finden Sie unter [Unterstützung des Verschiebevorgangs für Ressourcen](../azure-resource-manager/management/move-support-resources.md).

1. Wählen Sie **Überprüfen und erstellen** und anschließend **Erstellen** aus. Die Erstellung dauert einige Minuten. Die neu erstellte Azure Purview-Kontoinstanz wird in der Liste auf der Seite **Purview accounts** (Purview-Konten) angezeigt.

    :::image type="content" source="media/create-catalog-portal/create-resource.png" alt-text="Screenshot des Bildschirms „Purview-Konto erstellen“ mit hervorgehobener Schaltfläche „Überprüfen + erstellen“.":::

## <a name="open-purview-studio"></a>Öffnen von Purview Studio

Nachdem Ihr Azure Purview-Konto erstellt wurde, verwenden Sie Purview Studio, um darauf zuzugreifen und es zu verwalten. Es gibt zwei Möglichkeiten, um Purview Studio zu öffnen:

* Öffnen Sie Ihr Purview-Konto im [Azure-Portal](https://portal.azure.com). Wählen Sie auf der Übersichtsseite die Kachel „Purview Studio öffnen“ aus.
    :::image type="content" source="media/create-catalog-portal/open-purview-studio.png" alt-text="Screenshot der Übersichtsseite des Purview-Kontos mit hervorgehobener Kachel „Purview Studio“.":::

* Alternativ können Sie zu [https://web.purview.azure.com](https://web.purview.azure.com) navigieren, Ihr Purview-Konto auswählen und sich bei Ihrem Arbeitsbereich anmelden.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie ein Azure Purview-Konto erstellen und über Purview Studio darauf zugreifen.

Lesen Sie diese nächsten Artikel, um zu erfahren, wie Sie in Purview Studio navigieren, eine Sammlung erstellen und Zugriff auf Purview gewähren.

* [Verwenden von Purview Studio](use-purview-studio.md)
* [Erstellen einer Sammlung](quickstart-create-collection.md)
* [Rollenbasierte Zugriffssteuerung auf der Datenebene von Azure Purview](catalog-permissions.md)
