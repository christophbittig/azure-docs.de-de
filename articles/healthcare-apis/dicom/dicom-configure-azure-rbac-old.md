---
title: Konfigurieren von Azure RBAC für den DICOM-Dienst – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie Azure RBAC für den DICOM-Dienst konfigurieren.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2020
ms.author: aersoy
ms.openlocfilehash: 7b414fcd5c42ccfec48b3e17d8bfe1bad27ec952
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814738"
---
# <a name="configure-azure-rbac-for-the-dicom-service"></a>Konfigurieren von Azure RBAC für den DICOM-Dienst

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind. 

In diesem Artikel erfahren Sie, wie Sie die [rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/index.yml) verwenden, um den Zugriff auf den DICOM-Dienst zuzuweisen. 

## <a name="assign-roles"></a>Zuweisen von Rollen

Um Benutzern, Dienstprinzipalen oder Gruppen Zugriff auf die DICOM-Datenebene zu gewähren, wählen Sie das Blatt **Zugriffssteuerung (IAM)** aus. Wählen Sie die Registerkarte **Rollenzuweisungen** und **dann + Hinzufügen** aus.

[![dicom-Zugriffssteuerung. ](media/dicom-access-control.png) ](media/dicom-access-control.png#lightbox)


Suchen Sie in der **Auswahl Rolle** nach einer der integrierten Rollen für die DICOM-Datenebene:

[![Fügen Sie die RBAC-Rollenzuweisung hinzu. ](media/rbac-add-role-assignment.png) ](media/rbac-add-role-assignment.png#lightbox)

Sie können zwischen folgenden Rollen auswählen:

* DICOM-Datenbesitzer: Vollzugriff auf DICOM-Daten.
* DICOM-Datenleser: Lesen und Durchsuchen von DICOM-Daten.

Wenn diese Rollen nicht für Ihre Zwecke ausreichen, können Sie powerShell verwenden, um benutzerdefinierte Rollen zu erstellen.  Informationen zum Erstellen benutzerdefinierter Rollen finden Sie unter [Erstellen einer benutzerdefinierten Rolle mit Azure PowerShell](../../role-based-access-control/tutorial-custom-role-powershell.md).

Suchen **Sie** im Feld Auswählen nach einem Benutzer, Dienstprinzipal oder einer Gruppe, dem bzw. der Sie die Rolle zuweisen möchten.

## <a name="caching-behavior"></a>Verhalten beim Zwischenspeichern

Der DICOM-Dienst speichert Entscheidungen bis zu fünf Minuten lang zwischen. Wenn Sie einem Benutzer Zugriff auf den DICOM-Dienst gewähren, indem Sie ihn der Liste der zulässigen Objekt-IDs hinzufügen oder sie aus der Liste entfernen, sollten Sie davon ausgehen, dass es bis zu fünf Minuten dauert, bis Änderungen an den Berechtigungen verbreitet werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Azure-Rollen für die DICOM-Dienstdatenebene zuweisen. 
 
>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)
