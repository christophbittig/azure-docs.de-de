---
title: Rollen und Ressourcenzugriffssteuerung
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie den Ressourcenzugriff mithilfe von Rollen steuern.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 672df647bb655210ab759278a69e63a38abc1cf4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035294"
---
# <a name="roles-and-resource-access-control"></a>Rollen und Ressourcenzugriffssteuerung

Bei der Planung Ihrer Zugriffssteuerungsstrategie ist es am besten, Benutzern die Rolle mit den geringsten Berechtigungen zuzuweisen, die für den Zugriff auf Ressourcen erforderlich ist. In der folgenden Tabelle werden die primären Ressourcen in Ihrem Azure AD B2C-Mandanten und die am besten geeigneten Administratorrollen für die Benutzer beschrieben, die sie verwalten.

|Resource  |BESCHREIBUNG  |Role  |
|---------|---------|---------|
|[Anwendungsregistrierungen](tutorial-register-applications.md) | Erstellen und verwalten Sie alle Aspekte Ihrer Web-, mobilen und nativen Anwendungsregistrierungen in Azure AD B2C.|[Anwendungsadministrator](../active-directory/roles/permissions-reference.md#application-administrator)|
|[Identitätsanbieter](add-identity-provider.md)| Konfigurieren Sie den [lokalen Identitätsanbieter](identity-provider-local.md) und externe soziale Netzwerke oder Unternehmensidentitätsanbieter. | [Externer Identitätsanbieteradministrator](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[API-Connectors](add-api-connector.md)| Integrieren Sie Ihre Benutzerabläufe mit Web-APIs, um das Benutzererlebnis anzupassen und mit externen Systemen zu integrieren.|[Administrator für Benutzerflows mit externer ID](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Unternehmensbranding](customize-ui.md#configure-company-branding)| Passen Sie Ihre Benutzerflow-Seiten an.| [Globaler Administrator](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Benutzerattribute](user-flow-custom-attributes.md)| Benutzer mit dieser Rolle können benutzerdefinierte Attribute, die für alle Benutzerflows im Mandanten verfügbar sind, hinzufügen und löschen.| [Administrator für Benutzerflowattribute mit externer ID](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Verwalten von Benutzern| Verwalten Sie [Consumer-Konten](manage-users-portal.md) und Administratorkonten wie in diesem Artikel beschrieben.| [Benutzeradministrator](../active-directory/roles/permissions-reference.md#user-administrator)|
|Rollen und Administratoren| Verwalten von Rollenzuweisungen im Azure AD B2C-Verzeichnis. Erstellen und Verwalten von Gruppen, die Azure AD B2C-Rollen zugewiesen werden können. |[Globaler Administrator](../active-directory/roles/permissions-reference.md#global-administrator), [Administrator für privilegierte Rollen](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Benutzerabläufe](user-flow-overview.md)|Mit Benutzerflows konfigurieren und aktivieren Sie schnell häufige Identitätsaufgaben wie Registrierung, Anmeldung und Bearbeitung des Profils.| [Administrator für Benutzerflows mit externer ID](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Benutzerdefinierte Richtlinien](user-flow-overview.md)| Erstellen, Lesen, Aktualisieren und Löschen aller benutzerdefinierten Richtlinien in Azure AD B2C.| [B2C-IEF-Richtlinienadministrator](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[Richtlinienschlüssel](policy-keys-overview.md)|Hinzufügen und Verwalten von Verschlüsselungsschlüsseln zum Signieren und Überprüfen von Token, geheimen Clientschlüsseln, Zertifikaten und Kennwörtern in benutzerdefinierten Richtlinien.|[B2C-IEF-Schlüsselsatzadministrator](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|