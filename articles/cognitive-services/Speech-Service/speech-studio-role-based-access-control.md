---
title: Rollenbasierte Zugriffskontrolle in Speech Studio – Speech Dienst
titleSuffix: Azure Cognitive Services
description: Lernen Sie, wie Sie dem Speech Dienst über Speech Studio Zugriffsrollen zuweisen können.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: eur
ms.openlocfilehash: 4b84d73b242aba0b46674005211dcb6de77d69e4
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509390"
---
# <a name="azure-role-based-access-control-in-speech-studio"></a>Azure rollenbasierte Zugriffskontrolle in Speech Studio 

Speech Studio unterstützt die rollenbasierte Zugriffskontrolle von Azure (Azure RBAC), ein Autorisierungssystem zum Verwalten des individuellen Zugriffs auf Azure-Ressourcen. Mit Azure RBAC können Sie verschiedenen Teammitgliedern unterschiedliche Berechtigungsstufen für Ihre Speech Studio-Vorgänge zuweisen. Weitere Information zur Azure RBAC finden Sie in der [Azure RBAC-Dokumentation](../../role-based-access-control/overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Sie müssen mit Ihrem Azure-Konto und Ihrer Speech-Ressource bei Speech Studio angemeldet sein. Siehe die [Speech Studio Übersicht](speech-studio-overview.md).

## <a name="manage-role-assignments-for-speech-resources"></a>Verwalten von Rollenzuweisungen für Speech-Ressourcen

Um Zugriff auf eine Azure-Sprachressource zu gewähren, fügen Sie eine Rollenzuweisung über das Azure RBAC-Tool im Azure-Portal hinzu. 

Innerhalb weniger Minuten wird dem Ziel die ausgewählte Rolle für den ausgewählten Bereich zugewiesen. Hilfe zu diesen Schritten finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md?tabs=current).

## <a name="supported-built-in-roles-in-speech-studio"></a>Unterstützte integrierte Rollen in Speech Studio

Eine Rollendefinition ist eine Sammlung von Berechtigungen. Verwenden Sie die folgenden empfohlenen integrierten Rollen, wenn Sie keine eindeutigen benutzerdefinierten Anforderungen für Berechtigungen haben:

| **Integrierte Rolle** | **Berechtigung zum Auflisten von Ressourcenschlüsseln** | **Berechtigung für Custom Speech-Vorgänge** | **Berechtigung für Custom Voice-Vorgänge**| **Berechtigung für andere Funktionen** |
| ---| ---| ---| ---| --|
|**Besitzer** |Ja |Vollzugriff auf die Projekte, einschließlich der Berechtigung zum Erstellen, Bearbeiten oder Löschen von Projekten/Daten/Modellen/Endpunkten |Vollzugriff auf die Projekte, einschließlich der Berechtigung zum Erstellen, Bearbeiten oder Löschen von Projekten/Daten/Modellen/Endpunkten |Vollzugriff |
|**Mitwirkender** |Ja |Vollzugriff auf die Projekte, einschließlich der Berechtigung zum Erstellen, Bearbeiten oder Löschen von Projekten/Daten/Modellen/Endpunkten |Vollzugriff auf die Projekte, einschließlich der Berechtigung zum Erstellen, Bearbeiten oder Löschen von Projekten/Daten/Modellen/Endpunkten |Vollzugriff |
|**Cognitive Service-Mitwirkende** |Ja |Vollzugriff auf die Projekte, einschließlich der Berechtigung zum Erstellen, Bearbeiten oder Löschen von Projekten/Daten/Modellen/Endpunkten |Vollzugriff auf die Projekte, einschließlich der Berechtigung zum Erstellen, Bearbeiten oder Löschen von Projekten/Daten/Modellen/Endpunkten |Vollzugriff |
|**Cognitive Service: Benutzer** |Ja |Vollzugriff auf die Projekte, einschließlich der Berechtigung zum Erstellen, Bearbeiten oder Löschen von Projekten/Daten/Modellen/Endpunkten |Vollzugriff auf die Projekte, einschließlich der Berechtigung zum Erstellen, Bearbeiten oder Löschen von Projekten/Daten/Modellen/Endpunkten |Vollzugriff |
|**Cognitive Service Speech: Mitwirkender** |Nein |Vollzugriff auf die Projekte, einschließlich der Berechtigung zum Erstellen, Bearbeiten oder Löschen von Projekten/Daten/Modellen/Endpunkten |Vollzugriff auf die Projekte, einschließlich der Berechtigung zum Erstellen, Bearbeiten oder Löschen von Projekten/Daten/Modellen/Endpunkten |Vollzugriff |
|**Cognitive Service Speech: Benutzer** |Nein |Kann die Projekte/DataSets/Modelle/Endpunkte anzeigen; kann nicht erstellen, bearbeiten, löschen |Kann die Projekte/DataSets/Modelle/Endpunkte anzeigen; kann nicht erstellen, bearbeiten, löschen |Vollzugriff |
|**Cognitive Services: Datenleser (Vorschau)** |Nein |Kann die Projekte/DataSets/Modelle/Endpunkte anzeigen; kann nicht erstellen, bearbeiten, löschen |Kann die Projekte/DataSets/Modelle/Endpunkte anzeigen; kann nicht erstellen, bearbeiten, löschen |Vollzugriff |

Alternativ können Sie auch [Ihre eigenen benutzerdefinierten Rollen erstellen](../../role-based-access-control/custom-roles.md). Ein Beispiel: Sie könnten eine benutzerdefinierte Rolle mit der Berechtigung zum Hochladen benutzerdefinierter Sprachdatensätze erstellen, jedoch ohne die Möglichkeit, ein benutzerdefiniertes Sprachmodell an einem Endpunkt bereitzustellen.

> [!NOTE]
> Speech Studio unterstützt die schlüsselbasierte Authentifizierung. Rollen, die die Berechtigung haben, Ressourcenschlüssel (`Microsoft.CognitiveServices/accounts/listKeys/action`) aufzulisten, werden zunächst mit einem Ressourcenschlüssel authentifiziert und haben dann vollen Zugriff auf die Vorgänge in Speech Studio, solange die Schlüsselauthentifizierung im Azure-Portal aktiviert ist. Wenn die Schlüsselauthentifizierung durch den Dienst-Administrator deaktiviert wird, verlieren diese Rollen jeglichen Zugriff auf das Studio.

> [!NOTE]
> Eine Ressource kann mehreren Rollen zugewiesen oder vererbt werden: Die endgültige Zugriffsebene auf diese Ressource ist eine Kombination aus den Berechtigungen aller Ihrer Rollen auf der Betriebsebene.

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie mehr über die [Speech Dienst-Verschlüsselung von ruhenden Daten](./speech-encryption-of-data-at-rest.md).
