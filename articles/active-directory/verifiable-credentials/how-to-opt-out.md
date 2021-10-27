---
title: Deaktivieren der Azure Active Directory-Nachweise (Vorschau)
description: Hier erfahren Sie, wie Sie die Nachweisvorschau deaktivieren.
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: e46537a2bf0a9db08ec9baad810d5915450ee8ea
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006810"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>Deaktivieren der Nachweise (Vorschau)

In diesem Artikel:

- Mögliche Gründe für die Deaktivierung
- Erforderliche Schritte
- Informationen dazu, was mit Ihren Daten geschieht
- Auswirkung auf vorhandene Nachweise

> [!IMPORTANT]
> Azure Active Directory-Nachweise befinden sich derzeit in der Public Preview-Phase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

- Schließen Sie das Onboarding für Nachweise ab.

## <a name="potential-reasons-for-opting-out"></a>Mögliche Gründe für die Deaktivierung

Aktuell sind keine Änderungen an den Domäneninformationen möglich. Wenn Ihnen also ein Fehler unterläuft oder Sie eine Änderung vornehmen möchten, haben Sie nur die Möglichkeit, die Funktion zu deaktivieren und von vorn zu beginnen.

## <a name="the-steps-required"></a>Erforderliche Schritte

1. Suchen Sie im Azure-Portal nach Nachweisen.
2. Wählen Sie im linken Menü die Option **Einstellungen** aus.
3. Wählen Sie im Abschnitt **Reset your organization** (Organisation zurücksetzen) die Option **Delete all credentials, and opt out of preview** (Alle Anmeldeinformationen löschen und Vorschau deaktivieren) aus.

   ![Einstellungen: Zurücksetzen der Organisation](media/how-to-opt-out/settings-reset.png)

4. Lesen Sie die Warnmeldung, und wählen Sie zum Fortfahren die Option **Delete and opt out** (Löschen und deaktivieren) aus.

   ![Einstellungen: Löschen und deaktivieren](media/how-to-opt-out/delete-and-opt-out.png)

Sie haben nun die Nachweisvorschau deaktiviert. Im Anschluss erfahren Sie, was im Hintergrund passiert.

## <a name="what-happens-to-your-data"></a>Informationen dazu, was mit Ihren Daten geschieht

Nach der Deaktivierung des Diensts für Azure Active Directory-Nachweise passiert Folgendes:

- Die DID-Schlüssel in Key Vault werden [vorläufig gelöscht](../../key-vault/general/soft-delete-overview.md).
- Das Ausstellerobjekt wird aus unserer Datenbank gelöscht.
- Der Mandantenbezeichner wird aus unserer Datenbank gelöscht. 
- Alle Vertragsobjekte werden aus unserer Datenbank gelöscht.

Nach der Deaktivierung können Sie die DID nicht wiederherstellen und auch keine Vorgänge mehr für Ihre DID ausführen. Dieser Schritt ist endgültig, und Sie müssen die Funktion wieder aktivieren, woraufhin eine neue DID erstellt wird.  

## <a name="effect-on-existing-verifiable-credentials"></a>Auswirkung auf vorhandene Nachweise

Alle bereits ausgestellten Nachweise bleiben bestehen. Sie werden nicht kryptografisch ungültig, da Ihre DID weiterhin über ION aufgelöst werden kann.
Wenn vertrauende Seiten allerdings die Status-API aufzurufen, wird immer eine Fehlermeldung zurückgegeben.

## <a name="next-steps"></a>Nächste Schritte

- Einrichten von Nachweisen für Ihren [Azure-Mandanten](get-started-verifiable-credentials.md)