---
title: Erstellen eines Dienstprinzipals in Azure
description: In diesem Artikel wird beschrieben, wie Sie in Azure einen Dienstprinzipal erstellen können.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d2be616d19ec862a7bf27d55f0a35ed1737fbf86
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095903"
---
# <a name="creating-a-service-principal"></a>Erstellen eines Dienstprinzipals

Sie können einen neuen Dienstprinzipal erstellen oder einen in Ihrem Azure Active Directory-Mandanten vorhandenen Dienstprinzipal verwenden.

## <a name="app-registration"></a>App-Registrierung

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2. Wählen Sie im Menü auf der linken Seite die Option **Azure Active Directory** aus.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-aad.png" alt-text="Screenshot des Links zu Azure Active Directory":::

3. Wählen Sie **App-Registrierungen** und **+ Neue Registrierung** aus.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-reg.png" alt-text="Screenshot des Links zu „Neue Registrierung“":::

4. Geben Sie einen Namen für die **Anwendung** ein (Dienstprinzipalname).

5. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis** aus.

6. Wählen Sie als **Umleitungs-URI** die Option **Web** aus, und geben Sie die gewünschte URL ein. Hierbei muss es sich nicht um eine reale oder funktionsfähige URL handeln.

7. Klicken Sie anschließend auf **Registrieren**.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-register.png" alt-text="Screenshot der Details der neuen App-Registrierung":::
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-app.png" alt-text="Screenshot der neu erstellten Anwendung":::

## <a name="adding-a-secret-to-the-client-credentials"></a>Hinzufügen eines Geheimnisses zu den Clientanmeldeinformationen

1. Wählen Sie in **App-Registrierungen** die App aus.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-app-select.png" alt-text="Screenshot, der die zu registrierende App zeigt":::

2. Klicken Sie auf die Schaltfläche **Zertifikat oder Geheimnis hinzufügen**.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-add-secret.png" alt-text="Screenshot, der die App zeigt":::

3. Klicken Sie unter **Geheime Clientschlüssel** auf **+ Neuer geheimer Clientschlüssel**.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-client-secret.png" alt-text="Screenshot des Menüs „Geheimer Clientschlüssel“":::

4. Geben Sie eine **Beschreibung** ein, und legen Sie den Wert **Ablauf** für das Geheimnis fest. :::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-secret-desc.png" alt-text="Screenshot mit den Details des geheimen Clientschlüssels":::
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-client-secret.png" alt-text="Screenshot des geheimen Clientschlüssels":::

5. Kopieren Sie den Wert von **Clientanmeldeinformationen** aus **Übersicht**.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-client-cred.png" alt-text="Screenshot mit der App-Übersicht":::

## <a name="adding-the-secret-to-the-key-vault"></a>Hinzufügen des Geheimnisses zum Schlüsseltresor

1. Navigieren Sie zu Ihrem **Schlüsseltresor**.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-key-vault.png" alt-text="Screenshot, der den Schlüsseltresor zeigt":::

2. Wählen Sie **Einstellungen** --> **Geheimnisse** -->  **+ Generieren/Importieren** aus.  
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-generate-secret.png" alt-text="Screenshot der Optionen im Schlüsseltresor":::

3. Geben Sie den gewünschten **Namen** und **Wert** unter **Geheimer Clientschlüssel** für Ihren Dienstprinzipal ein.  
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-sp-secret.png" alt-text="Screenshot: Schlüsseltresor zum Erstellen eines Geheimnisses":::

4. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
