---
title: Konfigurieren des Azure RBAC for FHIR-Diensts – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie Azure RBAC for FHIR konfigurieren.
author: SteveWohl
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 11/17/2021
ms.author: zxue
ms.openlocfilehash: b43127a483e9935fe2212f85ab730d344815db07
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814739"
---
# <a name="configure-azure-rbac-for-healthcare-apis"></a>Konfigurieren von Azure RBAC für Gesundheits-APIs

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie die rollenbasierte [Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/index.yml) verwenden, um zugriff auf die Datenebene der Gesundheits-APIs zu weisen. Azure RBAC ist die bevorzugte Methode zum Zuweisen des Zugriffs auf die Datenebene, wenn Benutzer der Datenebene in dem Azure Active Directory-Mandanten verwaltet werden, der Ihrem Azure-Abonnement zugeordnet ist.

Sie können Rollenzuweisungen über die Azure-Portal. Beachten Sie, dass der FHIR-Dienst und der DICOM-Dienst unterschiedliche Anwendungsrollen definiert haben. Fügen Sie eine oder mehrere Rollen hinzu, oder entfernen Sie sie, um Benutzerzugriffssteuerungen zu verwalten.

## <a name="assign-roles-for-the-fhir-service"></a>Zuweisen von Rollen für den FHIR-Dienst

Um Benutzern, Dienstprinzipals oder Gruppen Zugriff auf die FHIR-Datenebene zu gewähren, wählen Sie den FHIR-Dienst aus der Azure-Portal. Wählen **Sie Zugriffssteuerung (IAM)** und dann die Registerkarte **Rollenzuweisungen** aus. Wählen **Sie + Hinzufügen** und dann **Rollenzuweisung hinzufügen aus.**
 
Wenn die Option für die Rollenzuweisung ausgegraut ist, bitten Sie Ihren Azure-Abonnementadministrator, Ihnen die Berechtigungen für das Abonnement oder die Ressourcengruppe zu gewähren, z. B. "Benutzerzugriffsadministrator". Weitere Informationen zu den integrierten Azure-Rollen finden Sie unter [Integrierte Azure-Rollen.](../role-based-access-control/built-in-roles.md)

[![Rollenzuweisung für die Zugriffssteuerung. ](fhir/media/rbac/role-assignment.png) ](fhir/media/rbac/role-assignment.png#lightbox)

Suchen Sie in der Rollenauswahl nach einer der integrierten Rollen für die FHIR-Datenebene, z. B. "FHIR-Daten-Mitwirkender". Sie können unten weitere Rollen auswählen.

* **FHIR-Datenleser:** Kann FHIR-Daten lesen (und durchsuchen).
* **FHIR-Datenschreiber:** Kann FHIR-Daten lesen, schreiben und soft delete.
* **FHIR-Datenexporter:** Kann Daten lesen und exportieren ($export Operator).
* **FHIR-Daten-Mitwirkender:** Kann alle Vorgänge auf Datenebene ausführen.
* **FHIR-Datenkonverter:** Kann den Konverter zum Durchführen der Datenkonvertierung verwenden

Geben Sie **im Abschnitt** Auswählen den Namen der Clientanwendungsregistrierung ein. Wenn der Name gefunden wird, wird der Anwendungsname aufgeführt. Wählen Sie den Anwendungsnamen und dann Speichern **aus.** 

Wenn die Clientanwendung nicht gefunden wird, überprüfen Sie Ihre Anwendungsregistrierung, um sicherzustellen, dass der Name korrekt ist. Stellen Sie sicher, dass die Clientanwendung in demselben Mandanten erstellt wird, in dem der FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) bereitgestellt wird.


[![Wählen Sie Rollenzuweisung aus. ](fhir/media/rbac/select-role-assignment.png) ](fhir/media/rbac/select-role-assignment.png#lightbox)

Sie können die Rollenzuweisung überprüfen, indem Sie in der Menüoption Zugriffssteuerung **(IAM)** die Registerkarte Rollenzuweisungen auswählen. 
 
## <a name="assign-roles-for-the-dicom-service"></a>Zuweisen von Rollen für den DICOM-Dienst

Um Benutzern, Dienstprinzipals oder Gruppen Zugriff auf die DICOM-Datenebene zu gewähren, wählen Sie das Blatt **Zugriffssteuerung (IAM)** aus. Wählen Sie die **Registerkarte Rollenzuweisungen** und dann **+ Hinzufügen aus.**

[![dicom-Zugriffssteuerung. ](dicom/media/dicom-access-control.png) ](dicom/media/dicom-access-control.png#lightbox)

Suchen Sie **in** der Rollenauswahl nach einer der integrierten Rollen für die DICOM-Datenebene:

[![Fügen Sie die RBAC-Rollenzuweisung hinzu. ](dicom/media/rbac-add-role-assignment.png) ](dicom/media/rbac-add-role-assignment.png#lightbox)

Sie können zwischen folgenden Rollen auswählen:

* DICOM-Datenbesitzer: Vollzugriff auf DICOM-Daten.
* DICOM-Datenleser: Lesen und Durchsuchen von DICOM-Daten.

Wenn diese Rollen für Ihre Anforderungen nicht ausreichen, können Sie mitHilfe von PowerShell benutzerdefinierte Rollen erstellen.  Informationen zum Erstellen benutzerdefinierter Rollen finden Sie unter [Erstellen einer benutzerdefinierten Rolle mit Azure PowerShell](../role-based-access-control/custom-roles-powershell.md).

Suchen Sie **im** Feld Auswählen nach einem Benutzer, Dienstprinzipal oder einer Gruppe, dem bzw. der Sie die Rolle zuweisen möchten.

> [!NOTE]
> Wenn Sie in Ihrer Anwendung oder in anderen Tools nicht auf den FHIR- oder DICOM-Dienst zugreifen können, müssen Sie möglicherweise noch einige Minuten warten, bis die Rollenzuweisung im System übermittelt wurde.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Azure-Rollen für den FHIR-Dienst und den DICOM-Dienst zuweisen. Informationen zum Zugreifen auf die Gesundheits-APIs mit Postman finden Sie unter

- [Zugriff mitHilfe von Postman](use-postman.md)
- [Zugriff über den REST-Client](using-rest-client.md)
- [Zugriff mit cURL](using-curl.md)
