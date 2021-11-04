---
title: 'Schnellstart: Erstellen eines Purview-Kontos im Azure-Portal'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie ein Azure Purview-Konto erstellen und Berechtigungen für dessen Verwendung konfigurieren.
author: nayenama
ms.author: nayenama
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: purview
ms.custom: mode-portal
ms.openlocfilehash: a84af112016ea4775f19234e89551f5f69b059bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037851"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal

In dieser Schnellstartanleitung werden die Schritte zum Erstellen eines Azure Purview-Kontos im Azure-Portal und zum Einstieg in das Klassifizieren, Sichern und Entdecken Ihrer Daten in Purview beschrieben.

Azure Purview ist ein Data Governance-Dienst, der Sie bei der Verwaltung Ihrer Datenlandschaft unterstützt. Durch Herstellen einer Verbindung zu Daten in Ihren lokalen Quellen, mehreren Clouds und SaaS-Quellen (Software-as-a-Service) erstellt Purview eine aktuelle Zuordnung Ihrer Informationen. Purview identifiziert und klassifiziert sensible Daten und gewährleistet eine End-to-End-Herkunft. Datenconsumer können Daten in Ihrer Organisation auffinden, und Datenadministratoren können Ihre Daten überwachen, schützen und die richtige Verwendung ihrer Daten sicherstellen.

Weitere Informationen zu Purview [finden Sie auf unserer Übersichtsseite](overview.md). Weitere Informationen zum Bereitstellen von Purview in Ihrer Organisation [finden Sie in unseren bewährten Methoden für die Bereitstellung](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

## <a name="create-an-azure-purview-account"></a>Erstellen eines Azure Purview-Kontos

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Seite **Purview accounts** (Purview-Konten).

    :::image type="content" source="media/create-catalog-portal/purview-accounts-page.png" alt-text="Screenshot der Seite „Purview accounts“ (Purview-Konten) im Azure-Portal":::

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
