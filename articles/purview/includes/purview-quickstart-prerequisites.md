---
title: Datei einfügen
description: include file
services: purview
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: include
ms.custom: include file
ms.date: 09/10/2021
ms.openlocfilehash: 8b2b6cf87380c0c89327a7cf0afd5331a140f836
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212481"
---
## <a name="prerequisites"></a>Voraussetzungen

* Sollten Sie über kein Azure-Abonnement verfügen, können Sie [ein kostenloses Abonnement erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

* Ihr Abonnement muss einem [Azure Active Directory-Mandanten](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) zugeordnet sein.

* Das Benutzerkonto, mit dem Sie sich bei Azure anmelden, muss ein Mitglied der Rolle *Mitwirkender* oder *Besitzer* oder ein *Administrator* des Azure-Abonnements sein. Wenn Sie die Berechtigungen anzeigen möchten, über die Sie im Abonnement verfügen, wechseln Sie zum [Azure-Portal](https://portal.azure.com), wählen Sie in der oberen rechten Ecke Ihren Benutzernamen aus, und wählen Sie **Weitere Optionen** (...) und dann **Meine Berechtigungen** aus. Wenn Sie Zugriff auf mehrere Abonnements besitzen, wählen Sie das entsprechende Abonnement aus.

* Keine [Azure-Richtlinien](../../governance/policy/overview.md), die das Erstellen von **Speicherkonten** oder **Event Hub-Namespaces** verhindern. Purview stellt bei seiner Erstellung ein verwaltetes Speicherkonto und einen Event Hub bereit. Wenn eine Blockierungsrichtlinie vorhanden ist und beibehalten werden muss, lesen Sie die Anleitung unter [Erstellen einer Azure-Richtlinie für Purview](../create-purview-portal-faq.md), und führen Sie die Schritte zum Erstellen einer Ausnahme für Purview-Konten aus.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.
