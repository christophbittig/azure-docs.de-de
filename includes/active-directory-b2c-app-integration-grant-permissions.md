---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: b356480175c4b3da537b1e3e49fcdca676b2d6d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338911"
---
Führen Sie die folgenden Schritte aus, um Ihrer App (App-ID: 1) Berechtigungen zu erteilen: 

1. Wählen Sie **App-Registrierungen** und dann die von Ihnen erstellte App (App-ID: 1) aus.
1. Wählen Sie unter **Verwalten** die Option **API-Berechtigungen**.
1. Wählen Sie unter **Konfigurierte Berechtigungen** die Option **Berechtigung hinzufügen** aus.
1. Wählen Sie die Registerkarte **Meine APIs** aus.
1. Wählen Sie die API aus, für die der Webanwendung Zugriff gewährt werden soll (App-ID: 2). Beispiel: *my-api1*.
1. Erweitern Sie unter **Berechtigung** den Eintrag **Aufgaben**, und wählen Sie die zuvor definierten Bereiche aus (Beispiel: *tasks.read* und *tasks.write*).
1. Wählen Sie **Berechtigungen hinzufügen** aus.
1. Wählen Sie **Administratoreinwilligung erteilen für\<*your tenant name*>** aus.
1. Wählen Sie **Ja** aus.
1. Wählen Sie **Aktualisieren** aus, und vergewissern Sie sich, dass für beide Bereiche unter **Status** der Status „Gewährt für...“ angezeigt wird.
1. Wählen Sie in der Liste **Konfigurierte Berechtigungen** Ihren Bereich aus, und kopieren Sie dann den vollständigen Namen des Bereichs. 

    ![Screenshot: Bereich „Konfigurierte Berechtigungen“ mit erteilten Lesezugriffsberechtigungen.](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  