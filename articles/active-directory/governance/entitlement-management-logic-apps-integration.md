---
title: Auslösen benutzerdefinierter Logic Apps mit der Azure-AD-Berechtigungsverwaltung (Entitlement Management)
description: Erfahren Sie, wie Sie benutzerdefinierte Logic Apps in der Azure Active Directory Berechtigungsverwaltung konfigurieren und verwenden.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: karenhoran
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 11/02/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdd706be5107558422971336cdb598c1148d6f83
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511378"
---
# <a name="trigger-custom-logic-apps-with-azure-ad-entitlement-management"></a>Auslösen benutzerdefinierter Logic Apps mit der Azure-AD-Berechtigungsverwaltung (Entitlement Management)


[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) können verwendet werden, um benutzerdefinierte Workflows zu automatisieren und Apps und Dienste an einem Ort zu verbinden. Benutzer können Logic Apps mit Berechtigungsverwaltung integrieren, um ihre Governance-Workflows über die grundlegenden Anwendungsfälle für die Berechtigungsverwaltung hinaus zu erweitern.

Diese Logic Apps können dann ausgelöst werden, damit sie in Übereinstimmung mit Anwendungsfällen der Berechtigungsverwaltung ausgeführt werden, z. B. wenn ein Zugriffspaket gewährt oder angefordert wird. Beispielsweise könnte ein Administrator eine benutzerdefinierte Logic App erstellen und mit der Berechtigungsverwaltung verknüpfen, sodass beim Anfordern eines Zugriffspakets durch einen Benutzer eine Logic App ausgelöst wird, die sicherstellt, dass dem Benutzer auch bestimmte Merkmale in einer SAAS-Drittanbieter-App (z. B. Salesforce) zugewiesen werden oder eine benutzerdefinierte E-Mail gesendet wird.

Anwendungsfälle für die Berechtigungsverwaltung, die in die Logic Apps integriert werden können:  

- wenn ein Zugriffspaket angefordert wird  

- wenn ein Zugriffspaket gewährt wird  

- wenn eine Zugriffspaketzuweisung abläuft  

Diese Trigger für Logic Apps werden auf einer neuen Registerkarte innerhalb der Zugriffspaketrichtlinien mit dem Namen **Regeln** gesteuert. Darüber hinaus werden auf der Registerkarte **Benutzerdefinierte Erweiterungen** auf der Seite Katalog alle hinzugefügten Logic Apps für einen bestimmten Katalog angezeigt. In diesem Abschnitt wird beschrieben, wie Sie Logic Apps erstellen und Katalogen hinzufügen sowie auf Pakete in der Berechtigungsverwaltung zugreifen.

## <a name="create-and-add-a-logic-app-to-a-catalog-for-use-in-entitlement-management"></a>Erstellen und Hinzufügen einer Logic App zu einem Katalog zur Verwendung in der Berechtigungsverwaltung 

**Erforderliche Rollen:** Globaler Administrator, Identity Governance Administrator, Katalogbesitzer oder Ressourcengruppenbesitzer (Resource Group Owner) 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wählen Sie im Azure-Portal die Option **Azure Active Directory** und dann **Identity Governance** aus. 

1. Klicken Sie im Menü auf der linken Seite auf **Kataloge**. 

1. Wählen Sie im linken Menü **Benutzerdefinierte Erweiterungen (Vorschau)** aus. 

1. Wählen Sie in der Kopf-Navigationsleiste **Benutzerdefinierte Erweiterung hinzufügen** aus.  

1. Geben Sie auf der Registerkarte **Grundlagen** den Namen der benutzerdefinierten Erweiterung (verknüpfte Logic App, die Sie hinzufügen) und eine Beschreibung des Workflows ein. Diese Felder werden in Zukunft auf der Registerkarte **Benutzerdefinierte Erweiterungen** des Katalogs angezeigt. 

    ![Fenster zum Erstellen einer benutzerdefinierten Erweiterung](./media/entitlement-management-logic-apps/create-custom-extension.png)


1. Navigieren Sie zur Registerkarte **Details**. 

1. Wählen Sie **Ja** im Feld „Neue Logic App erstellen“ aus. Wählen Sie andernfalls **Nein** aus und wechseln Sie zu Schritt 9, wenn Sie eine vorhandene Logic App verwenden möchten. Wenn Sie Ja ausgewählt haben, wählen Sie eine der folgenden Optionen aus und fahren Sie mit Schritt 9 fort: 

    1. Wählen Sie **Neue Azure AD Anwendung erstellen** aus, wenn Sie eine neue Anwendung als Grundlage für die neue Logic App verwenden möchten, oder
    
        ![Fensterbereich zum Auswählen einer neuen App für die Logic App](./media/entitlement-management-logic-apps/new-app-selection.png)

    1. Wählen Sie **Bestehende Azure AD Anwendung** aus, wenn Sie eine bestehende Anwendung als Grundlage für die neue Logic App verwenden möchten.
    
        ![Fensterbereich zum Auswählen einer vorhandenen App für die Logic App](./media/entitlement-management-logic-apps/existing-app-selection.png)

    > [!Note]    
    > Später können Sie im Logic-App-Designer bearbeiten, was Ihre Logic App machen soll. Wählen Sie dazu die Logic App aus, die Sie auf der Registerkarte **Benutzerdefinierte Erweiterungen** von **Kataloge** erstellt haben.  

1. Geben Sie als Nächstes die **Abonnement-ID**, **die Ressourcengruppe** und den **Namen der Logic App** ein. 

1. Wählen Sie dann **Überprüfen und erstellen** aus. 

1. Überprüfen Sie die Zusammenfassung Ihrer benutzerdefinierten Erweiterung und stellen Sie sicher, dass die Details für Ihre Logic-App-Aufrufe korrekt sind. Klicken Sie anschließend auf **Erstellen**.

    ![Beispiel für eine Zusammenfassung einer benutzerdefinierten Erweiterung](./media/entitlement-management-logic-apps/custom-extension-summary.png)

1. Diese benutzerdefinierte Erweiterung für die verknüpfte Logic App wird jetzt auf Ihrer Registerkarte Benutzerdefinierte Erweiterungen unter Kataloge angezeigt. Sie können dies in den Zugriffspaketrichtlinien aufrufen.


## <a name="edit-a-linked-logic-app"></a>Bearbeiten einer verknüpften Logic App 

**Erforderliche Rolle:** Globaler Administrator, Identity-Governance-Administrator oder Katalogbesitzer 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) I an. 

1. Wählen Sie im Azure-Portal die Option **Azure Active Directory** und dann **Identity Governance** aus. 

1. Klicken Sie im Menü auf der linken Seite auf **Kataloge**. 

1. Wählen Sie im linken Menü **Benutzerdefinierte Erweiterungen** aus. 

1. Hier können Sie alle benutzerdefinierten Erweiterungen (Logic Apps), die Sie diesem Katalog hinzugefügt haben, anzeigen lassen. Um einen Logic-App-Workflow zu bearbeiten oder einen Workflow für eine neu hinzugefügte Logic App zu erstellen, wählen Sie unter **Endpunkt** die benutzerdefinierte Logic-App-Erweiterung aus. Dadurch wird der Logic-App-Designer geöffnet und Sie können Ihren Workflow erstellen.  

 Weitere Informationen zum Erstellen von Logic-App-Workflows finden Sie unter [Erstellen eines automatisierten Workflows mit Azure Logic Apps im Azure-Portal](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow).

## <a name="add-custom-extension-to-access-package-policy"></a>Hinzufügen einer benutzerdefinierten Erweiterung zur Zugriffspaketrichtlinie 

**Erforderliche Rollen:** Globaler Administrator, Identity-Governance-Administrator, Katalogbesitzer oder Zugriffspaket-Manager 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wählen Sie im Azure-Portal die Option **Azure Active Directory** und dann **Identity Governance** aus. 

1. Wählen Sie im linken Menü **Zugriffspakete** aus. 

1. Wählen Sie **Neues Zugriffspaket** aus, wenn Sie einem neuen Zugriffspaket eine benutzerdefinierte Erweiterung (Logic App) hinzufügen möchten. Oder wählen Sie aus der Liste der bereits erstellten Zugriffspakete das Zugriffspaket aus, dem Sie eine benutzerdefinierte Erweiterung (Logic App) hinzufügen möchten.  

    > [!NOTE]  
    > Weitere Informationen zum Erstellen eines Zugriffspakets finden Sie unter [Erstellen eines neuen Zugriffspakets in der Berechtigungsverwaltung](entitlement-management-access-package-create.md).  Weitere Informationen zum Bearbeiten eines vorhandenen Zugriffspakets finden Sie unter [Ändern der Anforderungseinstellungen für ein Zugriffspaket in der Azure AD Berechtigungsverwaltung](entitlement-management-access-package-request-policy.md#open-and-edit-an-existing-policy-of-request-settings). 

1. Wechseln Sie in den Richtlinieneinstellungen des Zugriffspakets zur Registerkarte **Regeln (Vorschau)** . 

1. Wählen Sie im Menü unten **wann** das Zugriffspaketereignis aus, das Sie als Trigger für diese benutzerdefinierte Erweiterung (Logic-App) verwenden möchten. Wenn Sie beispielsweise nur den Workflow der benutzerdefinierten Erweiterung Logic App auslösen möchten, wenn ein Benutzer das Zugriffspaket anfordert, dann wählen Sie aus, **wenn eine Anforderung erstellt wird**. 

1. Wählen Sie im Menü unter **Ausführen (Do)** die benutzerdefinierte Erweiterung (Logic App) aus, die Sie dem Zugriffspaket hinzufügen möchten. Die von Ihnen ausgewählte Ausführen-(Do-)Aktion wird ausgeführt, wenn im Feld „Wann“ das ausgewählte Ereignis auftaucht.  

1. Wählen Sie **Erstellen** aus, wenn Sie die benutzerdefinierte Erweiterung einem neuen Zugriffspaket hinzufügen möchten. Wählen Sie **Aktualisieren** aus, wenn Sie es einem vorhandenen Zugriffspaket hinzufügen möchten.

    ![Hinzufügen einer Logik-App zu einem Zugriffspaket](./media/entitlement-management-logic-apps/add-logic-apps-access-package.png)

## <a name="troubleshooting-and-validation"></a>Problembehandlung und Überprüfung 

Um sicherzustellen, dass Ihre benutzerdefinierte Erweiterung die zugeordnete Logic App ordnungsgemäß ausgelöst hat, wenn sie von der Option **Ausführen (Do)** des Zugriffspakets aufgerufen wird, können Sie die Logic-App-Protokolle einsehen. 

Auf der Übersichtsseite für eine bestimmte Logic App werden Zeitstempel des Zeitpunkts der letzten Ausführung der Logic App angezeigt. Außerdem wird in der Übersicht „Ressourcengruppe“ für eine Ressourcengruppe für eine Ressourcengruppe mit einer verknüpften benutzerdefinierten Erweiterung der Name dieser benutzerdefinierten Erweiterung in der Übersicht angezeigt, wenn sie ordnungsgemäß konfiguriert wurde.  

## <a name="next-steps"></a>Nächste Schritte