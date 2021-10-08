---
title: Bedingter Zugriff in Azure Synapse Analytics
description: In diesem Artikel wird der bedingte Zugriff in Azure Synapse Analytics erläutert.
author: meenalsri
ms.author: mesrivas
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/07/2021
ms.sub-service: security
ms.custom: template-concept
ms.openlocfilehash: 4461ac0874eef735b7d01a3fc9c2c4158ddcb62d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598467"
---
# <a name="conditional-access-in-azure-synapse-analytics"></a>Bedingter Zugriff in Azure Synapse Analytics

Sie können jetzt Richtlinien für den bedingten Zugriff für Azure Synapse-Arbeitsbereiche konfigurieren. Der bedingte Zugriff ist ein Tool, das von Azure Active Directory bereitgestellt wird, um mehrere Signale wie Gerätetyp und IP-Standort des Geräts zusammenzuführen, um Entscheidungen über die Gewährung des Zugriffs, die Sperrung des Zugriffs oder die Erzwingung der mehrstufigen Authentifizierung für eine Ressource zu treffen. Richtlinien für den bedingten Zugriff werden in Azure Active Directory konfiguriert. Erfahren Sie mehr über den [bedingten Zugriff](../../active-directory/conditional-access/overview.md).


## <a name="configure-conditional-access"></a>Konfigurieren des bedingten Zugriffs
Die folgenden Schritte zeigen, wie Sie eine Richtlinie für bedingten Zugriff für Azure Synapse-Arbeitsbereiche konfigurieren.

1. Melden Sie sich mit einem Konto mit *globalen Administratorberechtigungen* beim Azure-Portal an, wählen Sie **Azure Active Directory** und im Menü **Sicherheit** aus. 
2. Wählen Sie **Bedingter Zugriff** und dann **+ Neue Richtlinie** aus, und geben Sie einen Namen für die Richtlinie an.
3. Klicken Sie unter **Zuweisungen** auf **Benutzer und Gruppen**, aktivieren Sie die Option **Benutzer und Gruppen auswählen**, und wählen Sie dann einen Azure AD-Benutzer oder eine Gruppe für „Bedingter Zugriff“ aus. Klicken Sie auf „Auswählen“ und dann auf „Fertig“.
4. Wählen Sie **Cloud-Apps**, und klicken Sie auf **Apps auswählen**. Wählen Sie **Microsoft Azure Synapse Gateway** aus. Klicken Sie dann auf Auswählen und Fertig.
5. Wählen Sie unter **Zugriffskontrollen** die Option **Gewähren** aus. Aktivieren Sie dann die anzuwendende Richtlinie, und wählen Sie „Fertig“ aus.
6. Legen Sie die Umschaltfläche **Richtlinie aktivieren** auf **Ein** fest, und wählen Sie dann „Erstellen“ aus.


## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über Richtlinien für bedingten Zugriff und deren Komponenten.
- [Allgemeine Richtlinien für bedingten Zugriff](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)
- [Erstellen einer Richtlinie für bedingten Zugriff](../../active-directory/conditional-access/concept-conditional-access-policies.md)