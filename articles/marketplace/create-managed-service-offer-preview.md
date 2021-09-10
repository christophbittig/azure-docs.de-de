---
title: Hinzufügen einer Vorschauzielgruppe für ein Angebot für verwaltete Dienste
description: Hinzufügen einer Vorschauzielgruppe für ein Angebot für verwaltete Dienste im Azure Marketplace
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 7/16/2021
ms.openlocfilehash: 38fbf04f9e8f961cca99bd3e0b23e05e664f4848
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113649715"
---
# <a name="add-a-preview-audience-for-a-managed-service-offer"></a>Hinzufügen einer Vorschauzielgruppe für ein Angebot für verwaltete Dienste

In diesem Artikel wird beschrieben, wie Sie mit Partner Center eine Vorschauzielgruppe eines Angebots für verwaltete Dienste im kommerziellen Marketplace konfigurieren. Die Vorschauzielgruppe kann Ihr Angebot überprüfen, bevor es online geschaltet wird.

## <a name="define-a-preview-audience"></a>Definieren einer Vorschauzielgruppe

Auf der Seite **Vorschauzielgruppe** können Sie eine eingeschränkte Zielgruppe definieren, die Ihr Angebot für verwaltete Dienste überprüfen kann, bevor Sie es live für die breitere Zielgruppe im Marketplace veröffentlichen. Sie legen Ihre Vorschauzielgruppe mithilfe von Azure-Abonnement-IDs fest, begleitet jeweils von einer optionalen Beschreibung. Keines dieser Felder ist für Kunden sichtbar. Sie finden Ihre Azure-Abonnement-ID auf der Seite **Abonnements** im Azure-Portal.

Fügen Sie mindestens eine Azure-Abonnement-ID hinzu, entweder einzeln (bis zu 10) oder durch Hochladen einer CSV-Datei (bis zu 100). So können Sie definieren, wer Ihr Angebot vorab testen kann, bevor es live veröffentlicht wird. Wenn Ihr Angebot bereits live geschaltet ist, können Sie dennoch eine Vorschauzielgruppe für das Testen von Aktualisierungen Ihres Angebots definieren.

> [!NOTE]
> Eine *Vorschauzielgruppe* unterscheidet sich von einer *privaten Zielgruppe*. Eine Vorschauzielgruppe kann auf das Angebot zugreifen, bevor es in den Onlineshops live veröffentlicht wird. Sie kann alle Pläne sehen und überprüfen, einschließlich der Pläne, die nach der vollständigen Veröffentlichung Ihres Angebots im Marketplace nur für eine private Zielgruppe verfügbar sind. Sie können einen Plan nur für eine private Zielgruppe verfügbar machen. Eine private Zielgruppe (definiert auf der Registerkarte Verfügbarkeit des Plans) hat exklusiven Zugriff auf einen bestimmten Plan.

## <a name="add-email-addresses-manually"></a>Manuelles Hinzufügen von E-Mail-Adressen

1. Fügen Sie auf der Seite **Vorschauzielgruppe** eine einzelne Azure-Abonnement-ID und optional eine Beschreibung in den entsprechenden Feldern hinzu.
2. Um eine weitere E-Mail-Adresse hinzuzufügen, wählen Sie den Link **ID hinzufügen (max. 10)** aus.
3. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren.

## <a name="add-email-addresses-using-a-csv-file"></a>Hinzufügen von E-Mail-Adressen über eine CSV-Datei

1. Wählen Sie auf der Seite **Vorschauzielgruppe** den Link **Zielgruppe exportieren (CSV)** aus.
2. Öffnen Sie die CSV-Datei. Geben Sie in der Spalte **Id** die Azure-Abonnement-IDs ein, die Sie der Vorschauzielgruppe hinzufügen möchten.
3. In der Spalte **Description** können Sie optional eine Beschreibung für jeden Eintrag hinzufügen.
4. Fügen Sie in der Spalte **Type** jeder Zeile mit einer ID die Zeichenfolge **SubscriptionID** hinzu.
5. Speichern Sie die Datei als CSV-Datei.
6. Wählen Sie auf der Seite **Vorschauzielgruppe Audience** den Link **Zielgruppe importieren (CSV)** aus.
7. Wählen Sie im Dialogfeld **Bestätigen** die Option **Ja** aus, und laden Sie dann die CSV-Datei hoch.

Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte, **Planübersicht**, fortfahren.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Plänen](create-managed-service-offer-plans.md)
