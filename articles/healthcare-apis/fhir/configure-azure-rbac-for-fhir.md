---
title: Konfigurieren des Azure RBAC for FHIR-Diensts – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie Azure RBAC for FHIR konfigurieren.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 09/10/2021
ms.author: zxue
ms.openlocfilehash: ff2a488a7ed8a693f23b533242748eed400bd802
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782493"
---
# <a name="configure-azure-rbac-for-the-fhir-service"></a>Konfigurieren von Azure RBAC für den FHIR-Dienst

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie die rollenbasierte [Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/index.yml) verwenden, um zugriff auf die Datenebene der Gesundheits-APIs zu weisen. Azure RBAC ist die bevorzugte Methode zum Zuweisen des Zugriffs auf die Datenebene, wenn Benutzer der Datenebene in dem Azure Active Directory-Mandanten verwaltet werden, der Ihrem Azure-Abonnement zugeordnet ist. 

## <a name="assign-roles"></a>Zuweisen von Rollen

Um Benutzern, Dienstprinzipals oder Gruppen Zugriff auf die FHIR-Datenebene zu gewähren, wählen Sie den FHIR-Dienst aus der Azure-Portal. Wählen **Sie Zugriffssteuerung (IAM)** und dann die Registerkarte **Rollenzuweisungen** aus. Wählen **Sie + Hinzufügen** und dann **Rollenzuweisung hinzufügen aus.**
 
Wenn die Option für die Rollenzuweisung ausgegraut ist, bitten Sie Ihren Azure-Abonnementadministrator, Ihnen die Berechtigungen für das Abonnement oder die Ressourcengruppe zu gewähren, z. B. "Benutzerzugriffsadministrator". Weitere Informationen zu den integrierten Azure-Rollen finden Sie unter [Integrierte Azure-Rollen.](../../role-based-access-control/built-in-roles.md)

[![Rollenzuweisung für die Zugriffssteuerung. ](media/rbac/role-assignment.png) ](media/rbac/role-assignment.png#lightbox)

Suchen Sie in der Rollenauswahl nach einer der integrierten Rollen für die FHIR-Datenebene, z. B. "FHIR-Daten-Mitwirkender". Sie können unten andere Rollen auswählen.

* **FHIR-Datenleser:** Kann FHIR-Daten lesen (und durchsuchen).
* **FHIR-Datenschreiber:** Kann FHIR-Daten lesen, schreiben und soft delete.
* **FHIR-Datenexporter:** Kann Daten lesen und exportieren ($export Operator).
* **FHIR-Daten-Mitwirkender:** Kann alle Vorgänge auf Datenebene ausführen.
* **FHIR-Datenkonverter:** Kann den Konverter zum Durchführen der Datenkonvertierung verwenden

Geben Sie **im Abschnitt** Auswählen den Namen der Clientanwendungsregistrierung ein. Wenn der Name gefunden wird, wird der Anwendungsname aufgeführt. Wählen Sie den Anwendungsnamen und dann Speichern **aus.** 

Wenn die Clientanwendung nicht gefunden wird, überprüfen Sie Ihre Anwendungsregistrierung, um sicherzustellen, dass der Name korrekt ist. Stellen Sie sicher, dass die Clientanwendung in demselben Mandanten erstellt wird, in dem der FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) bereitgestellt wird.


[![Wählen Sie Rollenzuweisung aus. ](media/rbac/select-role-assignment.png) ](media/rbac/select-role-assignment.png#lightbox)

Sie können die Rollenzuweisung überprüfen, indem Sie in der Menüoption Zugriffssteuerung **(IAM)** die Registerkarte Rollenzuweisungen auswählen. 
 

> [!NOTE]
> Die Übertragung der Rollenzuweisung im System kann einige Minuten dauern. Wenn Sie in Ihrer Anwendung oder anderen Testtools nicht auf den FHIR-Dienst zugreifen können, warten Sie möglicherweise einige Minuten. Überprüfen Sie außerdem, ob Sie die Berechtigungen user_impersonation Azure Healthcare-APIs in Ihrer Anwendungsregistrierung erteilt haben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Azure-Rollen für die FHIR-Datenebene zuweisen. Informationen zum Zugreifen auf den FHIR-Dienst mit Postman finden Sie unter

>[!div class="nextstepaction"]
>[Zugreifen auf den FHIR-Dienst mit Postman](../use-postman.md)