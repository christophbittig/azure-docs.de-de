---
title: Installation des Azure AD-ECMA-Connectorhosts
description: In diesem Artikel wird das Installieren des Azure AD-ECMA-Connectorhosts beschrieben.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dea990e1075e8a0714959f42fdef38286a120912
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449588"
---
# <a name="installation-of-the-azure-ad-ecma-connector-host"></a>Installation des Azure AD-ECMA-Connectorhosts

>[!IMPORTANT]
> Die Vorschauversion der lokalen Bereitstellung ist derzeit nur auf Einladung verfügbar. Um Zugriff auf die Funktion anzufordern, verwenden Sie das [Zugriffsanforderungsformular](https://aka.ms/onpremprovisioningpublicpreviewaccess). Wir öffnen die Vorschauversion im Rahmen der Vorbereitung auf die allgemeine Verfügbarkeit in den nächsten Monaten für weitere Kunden und Connectors.

Der Azure AD-ECMA-Connectorhost (Azure Active Directory) ist Bestandteil des Azure AD Connect-Bereitstellungs-Agent-Pakets. Der Bereitstellungs-Agent und Azure AD-ECMA-Connectorhost sind zwei separate Windows-Dienste. Sie werden mithilfe eines Installationsprogramms installiert, das auf demselben Computer bereitgestellt wird.

Dieser Flow führt Sie durch die Installation und Konfiguration des Azure AD ECMA-Connectorhosts.

 ![Diagramm des Installationsflows.](./media/on-premises-ecma-install/flow-1.png)

Weitere Installations- und Konfigurationsinformationen finden Sie unter:

   - [Voraussetzungen für den Azure AD-ECMA-Connectorhost](on-premises-ecma-prerequisites.md)
   - [Konfigurieren des Azure AD-ECMA-Connectorhosts und des Bereitstellungs-Agents](on-premises-ecma-configure.md)
   - [Konfigurieren eines generischen SQL-Connectors mit dem Azure AD-ECMA-Connectorhost](on-premises-sql-connector-configure.md)

## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>Herunterladen und Installieren des Azure AD Connect-Bereitstellungs-Agent-Pakets

 1. Melden Sie sich beim Azure-Portal an.
 1. Navigieren Sie zu **Unternehmensanwendungen** > **Neue Anwendung hinzufügen**.
 1. Suchen Sie nach der Anwendung **Lokale Bereitstellung**, und fügen Sie Ihrem Mandantenimage die Anwendung hinzu.
 1. Wechseln Sie zum Bereich **Bereitstellung**.
 1. Wählen Sie **Lokale Konnektivität** aus.
 1. Laden Sie das Agent-Installationsprogramm herunter.
 1. Führen Sie das Installationsprogramm für den Azure AD Connect-Bereitstellungs-Agent **AADConnectProvisioningAgentSetup.msi** aus.
 1. Akzeptieren Sie auf dem Bildschirm **Microsoft Azure AD Connect-Bereitstellungs-Agent-Paket** die Lizenzbedingungen, und wählen Sie **Installieren** aus.
 
    ![Bildschirm „Microsoft Azure AD Connect-Bereitstellungs-Agent-Paket“.](media/on-premises-ecma-install/install-1.png)</br>
 1. Nach Abschluss dieses Vorgangs wird der Konfigurations-Assistent gestartet. Klicken Sie auf **Weiter**.
 
    ![Screenshot des Begrüßungsbildschirms.](media/on-premises-ecma-install/install-2.png)</br>

 1. Wählen Sie auf dem Bildschirm **Erweiterung auswählen** die Option **Lokale Anwendungsbereitstellung (Azure AD in Anwendung)** aus. Klicken Sie auf **Weiter**. 
 
    ![Screenshot von „Erweiterung auswählen“.](media/on-premises-ecma-install/install-3.png)</br>
 1. Verwenden Sie Ihr globales Administratorkonto, um sich bei Azure AD anzumelden.
 
     ![Screenshot: Anmeldung bei Azure.](media/on-premises-ecma-install/install-4.png)</br>
 1. Wählen Sie auf dem Bildschirm **Agent-Konfiguration** die Option **Bestätigen** aus.
 
     ![Screenshot der Installationsbestätigung.](media/on-premises-ecma-install/install-5.png)</br>
 1. Nach Abschluss der Installation sollte im unteren Bereich des Assistenten eine Meldung angezeigt werden. Wählen Sie **Beenden** aus.
 
     ![Screenshot des Abschlusses.](media/on-premises-ecma-install/install-6.png)</br>
 

Nachdem das Agent-Paket erfolgreich installiert wurde, müssen Sie den Azure AD-ECMA-Connectorhost konfigurieren und Connectors erstellen oder importieren.
 
## <a name="next-steps"></a>Nächste Schritte

- [Voraussetzungen für den Azure AD-ECMA-Connectorhost](on-premises-ecma-prerequisites.md)
- [Konfiguration des Azure AD-ECMA-Connectorhosts](on-premises-ecma-configure.md)
- [Generischer SQL-Connector](on-premises-sql-connector-configure.md)
