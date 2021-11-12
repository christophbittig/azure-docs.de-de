---
title: Lokale Azure AD-Anwendungsbereitstellung in SCIM-fähigen Apps
description: In diesem Artikel wird beschrieben, wie Sie mithilfe des Azure AD-Bereitstellungsdiensts Benutzer in einer lokalen, SCIM-fähigen App bereitstellen.
services: active-directory
author: billmath
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: 232735af49cd0f38ff169c224a48b76b0707c97d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261137"
---
# <a name="azure-ad-on-premises-application-provisioning-to-scim-enabled-apps"></a>Lokale Azure AD-Anwendungsbereitstellung in SCIM-fähigen Apps

>[!IMPORTANT]
> Die Vorschauversion zur lokalen Bereitstellung ist derzeit nur mit Einladung verfügbar. Um Zugriff auf die Funktion anzufordern, verwenden Sie das [Zugriffsanforderungsformular](https://aka.ms/onpremprovisioningpublicpreviewaccess). Wir öffnen die Vorschauversion im Rahmen der Vorbereitung auf die allgemeine Verfügbarkeit in den nächsten Monaten für weitere Kunden und Connectors.

Der Azure Active Directory-Bereitstellungsdienst (Azure AD) unterstützt einen [SCIM 2.0-Client](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010), mit dem Benutzer automatisch in Cloudanwendungen oder lokalen Anwendungen bereitgestellt werden können. In diesem Artikel wird beschrieben, wie Sie den Azure AD-Bereitstellungsdienst verwenden können, um Benutzer in einer lokalen, SCIM-fähigen Anwendung bereitzustellen. Wenn Sie Benutzer in lokalen Anwendungen ohne SCIM-Fähigkeit bereitstellen möchten, die SQL als Datenspeicher verwenden, lesen Sie das [Tutorial: Azure AD ECMA Connectorhost – generischer SQL-Connector](tutorial-ecma-sql-connector.md). Wenn Sie Benutzer in Cloud-Apps wie DropBox und Atlassian bereitstellen möchten, lesen Sie die [Tutorials](../../active-directory/saas-apps/tutorial-list.md) zu den jeweiligen Apps.

![Abbildung: SCIM-Architektur](./media/on-premises-scim-provisioning/scim-4.png)

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure AD-Mandant mit Azure AD Premium P1 oder Premium P2 (oder EMS E3 oder E5). [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
- Administratorrolle für die Installation des Agents Diese Aufgabe ist einmalig und sollte ein Azure-Konto sein, das entweder ein Hybridadministrator oder ein globaler Administrator ist. 
- Administratorrolle zum Konfigurieren der Anwendung in der Cloud (Anwendungsadministrator, Cloudanwendungsadministrator, globaler Administrator oder eine benutzerdefinierte Rolle mit Berechtigungen)

## <a name="on-premises-app-provisioning-to-scim-enabled-apps"></a>Lokale Anwendungsbereitstellung in SCIM-fähigen Apps
So stellen Sie Benutzer für SCIM-fähige Apps bereit:

 1. Fügen Sie die **lokale SCIM-App** aus dem [Katalog](../../active-directory/manage-apps/add-application-portal.md) bereit.
 1. Navigieren Sie zu Ihrer App, und wählen Sie **Bereitstellung** > **Bereitstellungs-Agent herunterladen** aus.
 1. Wählen Sie **lokale Verbindung** aus, und laden Sie den Bereitstellungs-Agent herunter.
 1. Kopieren Sie den Agent auf den virtuellen Computer oder Server, auf dem Ihr SCIM-Endpunkt gehostet wird.
 1. Starten Sie den Installer für den Bereitstellungs-Agent, akzeptieren Sie die Vertragsbedingungen, und wählen Sie **Installieren** aus.
 1. Öffnen Sie den Assistenten für den Bereitstellungs-Agent, und wählen Sie **Lokale Bereitstellung** aus, wenn Sie zur Eingabe der Erweiterung aufgefordert werden, die Sie aktivieren möchten.
 1. Geben Sie Anmeldeinformationen für einen Azure AD-Administrator an, wenn Sie zur Autorisierung aufgefordert werden. Hybridadministrator oder globaler Administrator ist erforderlich.
 1. Wählen Sie **Bestätigen** aus, um die erfolgreiche Installation zu bestätigen.
 1. Wechseln Sie zurück zu Ihrer Anwendung, und wählen Sie **Lokale Verbindung** aus.
 1. Wählen Sie in der Dropdownliste den installierten Agent und dann **Agent(en) zuweisen** aus.
 1. Warten Sie 10 Minuten, oder starten Sie den Azure AD Connect Bereitstellungs-Agent-Dienst auf Ihrem Server oder ihrer VM neu.
 1. Geben Sie die URL für Ihren SCIM-Endpunkt im Feld **Mandanten-URL** an. z. B. https://localhost:8585/scim.
 
     ![Screenshot: Zuweisung eines Agents](./media/on-premises-scim-provisioning/scim-2.png)
 1. Wählen Sie **Verbindung testen** aus, und speichern Sie die Anmeldeinformationen.
 1. Konfigurieren Sie alle [Attributzuordnungen](customize-application-attributes.md) oder [Bereichsregeln](define-conditional-rules-for-provisioning-user-accounts.md), die für Ihre Anwendung erforderlich sind.
 1. Fügen Sie Benutzer zum Bereich hinzu, indem Sie der Anwendung [Benutzer und Gruppen zuweisen](../../active-directory/manage-apps/add-application-portal-assign-users.md).
 1. Testen Sie die [bedarfsorientierte](provision-on-demand.md) Bereitstellung einiger Benutzer.
 1. Fügen Sie weitere Benutzer zum Bereich hinzu, indem Sie sie Ihrer Anwendung zuweisen.
 1. Wechseln Sie zum Bereich **Bereitstellung**, und klicken Sie auf **Bereitstellung beginnen**.
 1. Die Überwachung erfolgt mithilfe der [Bereitstellungsprotokolle](../../active-directory/reports-monitoring/concept-provisioning-logs.md).

## <a name="additional-requirements"></a>Zusätzliche Anforderungen
* Achten Sie darauf, dass Ihre [SCIM](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010)-Implementierung die [Azure AD SCIM-Anforderungen](use-scim-to-provision-users-and-groups.md) erfüllt.
  
  Azure AD bietet Open-Source-[Referenzcode](https://github.com/AzureAD/SCIMReferenceCode/wiki), den Entwickler zum Bootstrapping ihrer SCIM-Implementierung verwenden können. Der Code wird so bereitgestellt, wie er ist.
* Unterstützen Sie den /schemas-Endpunkt, um den im Azure-Portal erforderlichen Konfigurationsaufwand zu reduzieren. 

## <a name="next-steps"></a>Nächste Schritte

- [App-Bereitstellung](user-provisioning.md)
- [Generischer SQL-Connector](on-premises-sql-connector-configure.md)
- [Tutorial: Generischer SQL-Connector mit ECMA-Connectorhost](tutorial-ecma-sql-connector.md)
