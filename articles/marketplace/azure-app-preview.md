---
title: Hinzufügen einer Vorschauzielgruppe für ein Azure-Anwendungsangebot
description: Fügen Sie für ein Azure-Anwendungsangebot in Partner Center eine Vorschauzielgruppe hinzu.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: cc0452ca7405803c47d40659e8e629892086e24a
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065386"
---
# <a name="add-a-preview-audience-for-an-azure-application-offer"></a>Hinzufügen einer Vorschauzielgruppe für ein Azure-Anwendungsangebot

In diesem Artikel wird beschrieben, wie Sie eine Vorschauzielgruppe für ein Azure-Anwendungsangebot im kommerziellen Marketplace konfigurieren. Sie müssen eine Vorschauzielgruppe definieren, die Ihren Angebotseintrag überprüfen kann, bevor es live geschaltet wird.

## <a name="define-a-preview-audience"></a>Definieren einer Vorschauzielgruppe

Auf der Seite **Vorschauzielgruppe** können Sie eine eingeschränkte Zielgruppe definieren, die Ihr Azure-Anwendungsangebot überprüfen kann, bevor Sie es live für die breitere Zielgruppe im Marketplace veröffentlichen. Sie legen Ihre Vorschauzielgruppe mithilfe von Azure-Abonnement-IDs fest, begleitet jeweils von einer optionalen Beschreibung. Keines dieser Felder ist für Kunden sichtbar. Sie finden Ihre Azure-Abonnement-ID auf der Seite **Abonnement** im Azure-Portal.

Fügen Sie mindestens eine und höchstens 10 Azure-Abonnement-IDs einzeln (bis zu 10) oder durch Hochladen einer CSV-Datei (bis zu 100) hinzu. So können Sie definieren, wer Ihr Angebot vorab testen kann, bevor es live veröffentlicht wird. Wenn Ihr Angebot bereits live geschaltet ist, können Sie dennoch eine Vorschauzielgruppe für das Testen von Änderungen oder Aktualisierungen Ihres Angebots definieren.

> [!NOTE]
> Eine Vorschauzielgruppe unterscheidet sich von einer privaten Zielgruppe. Eine Vorschauzielgruppe kann auf das Angebot zugreifen, bevor es in den Onlineshops live veröffentlicht wird. Sie kann alle Pläne sehen und überprüfen, einschließlich der Pläne, die nach der vollständigen Veröffentlichung Ihres Angebots im Marketplace nur für eine private Zielgruppe verfügbar sind. Sie können einen Plan nur für eine private Zielgruppe verfügbar machen. Eine private Zielgruppe (definiert auf der Registerkarte **Verfügbarkeit** des Plans) hat exklusiven Zugriff auf einen bestimmten Plan.

### <a name="add-subscription-ids-manually"></a>Manuelles Hinzufügen von Abonnement-IDs

1. Fügen Sie auf der Seite **Vorschauzielgruppe** eine einzelne Azure-Abonnement-ID und optional eine Beschreibung in den entsprechenden Feldern hinzu.
1. Um eine weitere ID hinzuzufügen, wählen Sie den Link **ID hinzufügen (max. 10)** aus.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren: „Technische Konfiguration“.
1. Fahren Sie mit [Nächste Schritte](#next-steps) fort.

### <a name="add-subscription-ids-with-a-csv-file"></a>Hinzufügen von Abonnement-IDs mit einer CSV-Datei

1. Wählen Sie auf der Seite **Preview Audience** (Vorschauzielgruppe) den Link **Zielgruppe exportieren (CSV)** aus.
1. Öffnen Sie die CSV-Datei in einer geeigneten Anwendung wie Microsoft Excel.
1. Geben Sie in der CSV-Datei in der Spalte **ID** die Azure-Abonnement-IDs ein, die Sie der Vorschauzielgruppe hinzufügen möchten.
1. In der Spalte **Description** (Beschreibung) können Sie optional eine Beschreibung für jede E-Mail-Adresse hinzufügen.
1. Geben Sie für jede Abonnement-ID, die Sie in Spalte B eingeben, in Spalte A den **Typ** „SubscriptionID“ ein.
1. Speichern Sie dies als CSV-Datei.
1. Wählen Sie auf der Seite **Vorschauzielgruppe Audience** den Link **Zielgruppe importieren (CSV)** aus.
1. Wählen Sie im Dialogfeld **Bestätigen** die Option **Ja** aus.
1. Wählen Sie die CSV-Datei und dann **Öffnen** aus.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren: „Technische Konfiguration“.

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen technischer Details zu diesem Angebot](azure-app-technical-configuration.md)
