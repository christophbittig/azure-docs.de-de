---
title: Verwaltungseinheiten in Azure Active Directory | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Berechtigungen in Azure Active Directory mithilfe von Verwaltungseinheiten präziser delegieren.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26b91b216c6a813270cf3b30cde0d8450f964668
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734481"
---
# <a name="administrative-units-in-azure-active-directory"></a>Verwaltungseinheiten in Azure Active Directory

In diesem Artikel werden Verwaltungseinheiten in Azure Active Directory (Azure AD) beschrieben. Eine Verwaltungseinheit ist eine Azure AD Ressource, bei der es sich um einen Container für andere Azure AD Ressourcen handeln kann. Eine Verwaltungseinheit kann nur Benutzer und Gruppen enthalten.

Mit Verwaltungseinheiten können Sie die Berechtigungen einer Rolle auf einen beliebigen von Ihnen zu definierenden Bereich in Ihrer Organisation beschränken. Mit Verwaltungseinheiten könnten Sie beispielsweise die Rolle [Helpdesk-Administrator](permissions-reference.md#helpdesk-administrator) an regionale Supportspezialisten delegieren, damit diese nur in der von ihnen unterstützten Region Benutzer verwalten können.

## <a name="deployment-scenario"></a>Bereitstellungsszenario

Das Einschränken des Verwaltungsbereichs mithilfe von Verwaltungseinheiten kann in Organisationen hilfreich sein, die aus beliebigen unabhängigen Abteilungen bestehen. Stellen Sie sich beispielsweise eine große Universität vor, die sich aus vielen eigenständigen Fakultäten (Wirtschaftswissenschaften, Technik usw.) zusammensetzt. Die einzelnen Fakultäten verfügen jeweils über ein Team von IT-Administratoren, die den Zugriff steuern, Benutzer verwalten und Richtlinien für ihre Fakultät festlegen.

Ein zentraler Administrator könnte folgende Aktionen ausführen:

- Erstellen einer Verwaltungseinheit für die Fakultät „Wirtschaftswissenschaften“
- Auffüllen der Verwaltungseinheit nur mit Studenten und Mitarbeitern der Fakultät „Wirtschaftswissenschaften“
- Erstellen einer Rolle mit Administratorberechtigungen nur für Azure AD-Benutzer in der Verwaltungseinheit für die Fakultät „Wirtschaftswissenschaften“
- Hinzufügen des IT-Teams der Fakultät „Wirtschaftswissenschaften“ zu der Rolle, zusammen mit dem zugehörigen Bereich

## <a name="license-requirements"></a>Lizenzanforderungen

Bei der Verwendung von Verwaltungseinheiten ist für jeden Administrator einer Verwaltungseinheit eine Azure AD Premium P1-Lizenz erforderlich, und alle Mitglieder der Verwaltungseinheit benötigen Azure AD Free-Lizenzen. Um die richtige Lizenz für Ihre Anforderungen zu ermitteln, lesen Sie [Vergleich der allgemein verfügbaren Features der Editionen Free und Premium](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

## <a name="manage-administrative-units"></a>Verwalten von Verwaltungseinheiten

Sie können Verwaltungseinheiten im Azure-Portal, mithilfe von PowerShell-Cmdlets und -Skripts oder mit Microsoft Graph verwalten. Weitere Informationen finden Sie unter

- [Erstellen, Entfernen, Auffüllen und Hinzufügen von Rollen zu Verwaltungseinheiten](admin-units-manage.md): Enthält umfassende schrittweise Anleitungen.
- [Verwenden von Verwaltungseinheiten:](/powershell/azure/active-directory/working-with-administrative-units) Enthält Informationen zur Verwendung von Verwaltungseinheiten über PowerShell.
- [Ressourcentyp „administrativeUnit“:](/graph/api/resources/administrativeunit) Enthält eine ausführliche Dokumentation zu Microsoft Graph für Verwaltungseinheiten.

### <a name="plan-your-administrative-units"></a>Planen Ihrer Verwaltungseinheiten

Verwaltungseinheiten können zur logischen Gruppierung von Azure AD-Ressourcen verwendet werden. Eine Organisation mit einer global verteilten IT-Abteilung erstellt möglicherweise Verwaltungseinheiten, die relevante geografische Begrenzungen definieren. In einem anderen Szenario verfügt eine globale Organisation über verschiedene Unterorganisationen, die in ihrem Betrieb halbautonom sind. Hier können Verwaltungseinheiten die Unterorganisationen darstellen.

Die Kriterien für die Erstellung von Verwaltungseinheiten richten sich nach den besonderen Anforderungen eines Unternehmens. Verwaltungseinheiten sind eine gängige Methode, um die Struktur in Microsoft 365-Diensten zu definieren. Wir empfehlen, bei der Vorbereitung der Verwaltungseinheiten deren Verwendung über Microsoft 365-Dienste hinweg im Blick zu haben. Um Verwaltungseinheiten optimal zu nutzen, könnten Sie in Microsoft 365-Diensten gemeinsame Ressourcen in einer Verwaltungseinheit verknüpfen.

Sie können davon ausgehen, dass die Erstellung von Verwaltungseinheiten in der Organisation die folgenden Phasen durchläuft:

1. **Anfängliche Einführung:** Ihre Organisation beginnt anhand anfänglicher Kriterien mit der Erstellung von Verwaltungseinheiten. Mit zunehmender Verfeinerung der Kriterien nimmt dann auch die Anzahl von Verwaltungseinheiten zu.
1. **Bereinigung:** Nach der Definition der Kriterien werden nicht mehr benötigte Verwaltungseinheiten gelöscht.
1. **Stabilisierung:** Die Struktur Ihrer Organisation ist definiert, und die Anzahl von Verwaltungseinheiten wird sich kurzfristig nicht stark verändern.

## <a name="currently-supported-scenarios"></a>Derzeit unterstützte Szenarien

Globale Administratoren und Administratoren für privilegierte Rollen können auf das Azure-Portal zugreifen und folgende Aufgaben ausführen:

- Erstellen von Verwaltungseinheiten
- Hinzufügen von Benutzern und Gruppenmitgliedern für Verwaltungseinheiten
- Zuweisen von IT-Mitarbeitern zu Administratorrollen, die auf den Bereich der Verwaltungseinheit bezogen sind.

Administratoren, die auf den Bereich einer Verwaltungseinheit bezogen sind, können das Microsoft 365 Admin Center für die grundlegende Verwaltung von Benutzern in ihren Verwaltungseinheiten verwenden. Ein auf den Bereich einer Verwaltungseinheit bezogener Gruppenadministrator kann Gruppen mithilfe von PowerShell und Microsoft Graph oder über das Microsoft 365 Admin Center verwalten.

>[!Note]
>Nur die in diesem Abschnitt beschriebenen Features sind im Microsoft 365 Admin Center verfügbar. Für eine Azure AD-Rolle, die auf den Bereich einer Verwaltungseinheit bezogen ist, stehen keine Features auf Organisationsebene zur Verfügung.

In den folgenden Abschnitten wird die aktuelle Unterstützung von Szenarien mit Verwaltungseinheiten beschrieben.

### <a name="administrative-unit-management"></a>Verwalten von Verwaltungseinheiten

| Berechtigungen |   Graph/PowerShell   | Azure-Portal | Microsoft 365 Admin Center |
| --- | --- | --- | --- |
| Erstellen und Löschen von Verwaltungseinheiten   |    Unterstützt    |   Unterstützt   |    Nicht unterstützt |
| Hinzufügen und Entfernen einzelner Mitglieder zu Verwaltungseinheiten    |   Unterstützt    |   Unterstützt   |    Nicht unterstützt |
| Hinzufügen und Entfernen von Verwaltungseinheitsmitgliedern per Massenvorgang unter Verwendung von CSV-Dateien   |    Nicht unterstützt     |  Unterstützt   |    Keine Unterstützung geplant |
| Zuweisen von Administratoren, die auf den Bereich der Verwaltungseinheit bezogen sind  |     Unterstützt    |   Unterstützt    |   Nicht unterstützt |
| Dynamisches Hinzufügen und Entfernen von Verwaltungseinheitsmitgliedern auf der Grundlage von Attributen | Nicht unterstützt | Nicht unterstützt | Nicht unterstützt

### <a name="user-management"></a>Benutzerverwaltung

| Berechtigungen |   Graph/PowerShell   | Azure-Portal | Microsoft 365 Admin Center |
| --- | --- | --- | --- |
| Auf die Verwaltungseinheit ausgerichtete Verwaltung von Benutzereigenschaften, Kennwörtern und Lizenzen   |    Unterstützt     |  Unterstützt   |   Unterstützt |
| Auf die Verwaltungseinheit ausgerichtete Blockierung von Benutzeranmeldungen sowie Aufhebung der Blockierung    |   Unterstützt   |    Unterstützt   |    Unterstützt |
| Auf die Verwaltungseinheit ausgerichtete Verwaltung von Anmeldeinformationen für die mehrstufige Authentifizierung   |    Unterstützt   |   Unterstützt   |   Nicht unterstützt |

### <a name="group-management"></a>Gruppenverwaltung

| Berechtigungen |   Graph/PowerShell   | Azure-Portal | Microsoft 365 Admin Center |
| --- | --- | --- | --- |
| Auf die Verwaltungseinheit ausgerichtete Verwaltung von Gruppeneigenschaften und Gruppenmitgliedern     |  Unterstützt   |    Unterstützt    |  Nicht unterstützt |
| Auf die Verwaltungseinheit ausgerichtete Verwaltung der Gruppenlizenzierung   |    Unterstützt  |    Unterstützt   |   Nicht unterstützt |

Der mithilfe von Verwaltungseinheiten angewendete Bereich gilt nur für Verwaltungsberechtigungen. Verwaltungseinheiten verhindern nicht, dass Mitglieder oder Administratoren ihre [Standardbenutzerberechtigungen](../fundamentals/users-default-permissions.md) verwenden, um zu anderen Benutzern, Gruppen oder Ressourcen außerhalb der Verwaltungseinheit zu navigieren. Im Microsoft 365 Admin Center werden Benutzer außerhalb der Verwaltungseinheiten eines bereichsbezogenen Administrators herausgefiltert. Sie können aber zu anderen Benutzern im Azure-Portal, in PowerShell und in anderen Microsoft-Diensten navigieren.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Verwaltungseinheiten](admin-units-manage.md)
- [Hinzufügen und Verwalten von Benutzern in einer Verwaltungseinheit in Azure Active Directory](admin-units-add-manage-users.md)
- [Hinzufügen und Verwalten von Gruppen in Verwaltungseinheiten in Azure Active Directory](admin-units-add-manage-groups.md)
- [Zuweisen von bereichsbezogenen Rollen zu einer Verwaltungseinheit](admin-units-assign-roles.md)
