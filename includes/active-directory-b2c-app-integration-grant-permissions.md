---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: kengaderdus
ms.openlocfilehash: eba3d57bab13cf573cd6e4478c0df672e3b34458
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007582"
---
Führen Sie die folgenden Schritte aus, um Ihrer App (**App-ID: 1**) Berechtigungen zu erteilen: 

1. Wählen Sie **App-Registrierungen** und dann die von Ihnen erstellte App (**App-ID: 1**) aus.
1. Wählen Sie unter **Verwalten** die Option **API-Berechtigungen**.
1. Wählen Sie unter **Konfigurierte Berechtigungen** die Option **Berechtigung hinzufügen** aus.
1. Wählen Sie die Registerkarte **Meine APIs** aus.
1. Wählen Sie die API aus, für die der Webanwendung Zugriff gewährt werden soll (**App-ID: 2**). Geben Sie z. B. **my-api1** ein.
1. Erweitern Sie unter **Berechtigung** den Eintrag **Aufgaben**, und wählen Sie die zuvor definierten Bereiche aus (Beispiel: **tasks.read** und **tasks.write**).
1. Wählen Sie **Berechtigungen hinzufügen** aus.
1. Wählen Sie **Administratoreinwilligung erteilen für\<*your tenant name*>** aus.
1. Wählen Sie **Ja**.
1. Wählen Sie **Aktualisieren** aus, und vergewissern Sie sich, dass für beide Bereiche unter **Status** der Status **Gewährt für...** angezeigt wird.
1. Wählen Sie in der Liste **Konfigurierte Berechtigungen** Ihren Bereich aus, und kopieren Sie dann den vollständigen Namen des Bereichs. 

    ![Screenshot: Bereich „Konfigurierte Berechtigungen“ mit erteilten Lesezugriffsberechtigungen.](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  