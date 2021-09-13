---
title: 'ML Studio (Classic): Exportieren und Löschen Ihrer Daten (Azure)'
description: Produktinterne, von Machine Learning Studio (Classic) gespeicherte Daten können über das Azure-Portal sowie über authentifizierte REST-APIs exportiert und gelöscht werden. Auf Telemetriedaten können Sie über das Azure Privacy-Portal zugreifen. In diesem Artikel erfahren Sie, welche Schritte erforderlich sind.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 0f590f52224eed6fd32dbb2af0297669b99a3fe8
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688853"
---
# <a name="export-and-delete-in-product-user-data-from-machine-learning-studio-classic"></a>Exportieren und Löschen produktinterner Benutzerdaten aus Machine Learning Studio (Classic)

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

Produktinterne, von Machine Learning Studio (Classic) gespeicherte Daten können über das Azure-Portal, über die Benutzeroberfläche von Studio (Classic), per PowerShell sowie über authentifizierte REST-APIs gelöscht oder exportiert werden. In diesem Artikel erfahren Sie, wie das geht. 

Auf Telemetriedaten können Sie über das Azure Privacy-Portal zugreifen. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Welche Arten von Benutzerdaten erfasst Studio (klassisch)?

Bei diesem Dienst bestehen Benutzerdaten aus Informationen zu Benutzern mit autorisiertem Zugriff auf Arbeitsbereiche und Telemetriedatensätze der Benutzerinteraktionen mit dem Dienst.

In Machine Learning Studio (klassisch) gibt es zwei Arten von Benutzerdaten:
- **Persönliche Kontodaten:** Konto-IDs und E-Mail-Adressen, die mit einem Konto verknüpft sind.
- **Kundendaten:** Daten, die Sie zum Analysieren hochgeladen haben.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Studio (klassisch)-Kontotypen und die Speicherung von Daten

In Machine Learning Studio (klassisch) gibt es drei Arten von Konten. Die Art des Kontos, das Sie haben, gibt vor, wie Ihre Daten gespeichert werden und wie Sie diese löschen bzw. exportieren können.

- Ein **Gast-Arbeitsbereich** ist ein kostenloses, anonymes Konto. Sie melden sich ohne Angabe von Anmeldeinformationen (z. B. E-Mail-Adresse oder Kennwort) an.
    -  Die Daten werden nach Ablauf des Gast-Arbeitsbereichs endgültig gelöscht.
    - Gastbenutzer können Kundendaten über die Benutzeroberfläche, REST-APIs oder das PowerShell-Paket exportieren.
- Ein **kostenloser Arbeitsbereich** ist ein kostenloses Konto, bei dem Sie sich mit den Anmeldeinformationen (E-Mail-Adresse und Kennwort) eines Microsoft-Kontos anmelden.
    - Sie können persönliche und Kundendaten, die DSR-Anforderungen (Data Subject Rights) unterliegen, exportieren und löschen.
    - Kundendaten können Sie über die Benutzeroberfläche, REST-APIs oder das PowerShell-Paket exportieren.
    - Bei kostenlosen Arbeitsbereichen, für die keine Azure AD-Konten verwendet werden, können Telemetriedaten über das Azure Privacy-Portal exportiert werden.
    - Wenn Sie den Arbeitsbereich löschen, löschen Sie alle persönlichen Kundendaten.
- Ein **Standardarbeitsbereich** ist ein kostenpflichtiges Konto, auf das Sie mit Anmeldeinformationen zugreifen.
    - Sie können persönliche und Kundendaten, die DSR-Anforderungen unterliegen, exportieren und löschen.
    - Der Zugriff auf Daten kann über das Azure Privacy-Portal erfolgen.
    - Persönliche und Kundendaten können Sie über die Benutzeroberfläche, REST-APIs oder das PowerShell-Paket exportieren.
    - Sie können Ihre Daten im Azure-Portal löschen.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Löschen von Arbeitsbereichsdaten in Studio (klassisch) 

### <a name="delete-individual-assets"></a>Löschen einzelner Objekte

Benutzer können Objekte in einem Arbeitsbereich löschen, indem Sie zuerst die Objekte und dann die Schaltfläche „Löschen“ auswählen.

![Löschen von Objekten in Machine Learning Studio (klassisch)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Löschen eines gesamten Arbeitsbereichs

Benutzer können auch ihren gesamten Arbeitsbereich löschen:
- Kostenpflichtiger Arbeitsbereich: Löschen über das Azure-Portal.
- Kostenloser Arbeitsbereich: Verwenden Sie die Schaltfläche „Löschen“ im Bereich **Einstellungen**.

![Löschen eines kostenlosen Arbeitsbereichs in Machine Learning Studio (klassisch)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Exportieren von Studio (klassisch)-Daten mit PowerShell
Verwenden Sie PowerShell, um alle Informationen aus Machine Learning Studio (Classic) mithilfe von Befehlen in ein portables Format zu exportieren. Weitere Informationen finden Sie im Artikel [PowerShell-Module für Azure Machine Learning Studio (klassisch)](powershell-module.md).

## <a name="next-steps"></a>Nächste Schritte

Eine Dokumentation zur Abrechnung von Webdiensten und Vertragsplänen finden Sie in der [Referenz zu REST-APIs für die Verwaltung von Machine Learning Studio (Classic)](/rest/api/machinelearning/).