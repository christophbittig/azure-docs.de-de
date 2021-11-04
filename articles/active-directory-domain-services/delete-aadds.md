---
title: Löschen von Azure Active Directory Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine von Azure Active Directory Domain Services verwaltete Domäne im Azure-Portal deaktivieren oder löschen.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fa0cc8c0ca548dbf5021d7531198802729afaf2f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427815"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Löschen einer von Azure Active Directory Domain Services verwalteten Domäne im Azure-Portal

Wenn Sie eine verwaltete Azure Active Directory Domain Services (Azure AD DS)-Domäne nicht mehr benötigen, können Sie sie löschen. Es gibt keine Möglichkeit, eine verwaltete Azure AD DS-Domäne abzuschalten oder vorübergehend zu deaktivieren. Durch das Löschen der verwalteten Domäne wird der Azure AD-Mandant weder gelöscht noch anderweitig beeinträchtigt.

In diesem Artikel erfahren Sie, wie Sie im Azure-Portal eine verwaltete Domäne löschen.

> [!WARNING]
> **Die Löschung ist dauerhaft und kann nicht rückgängig gemacht werden.**
> 
> Beim Löschen einer verwalteten Domäne werden die folgenden Schritte ausgeführt:
>   * Die Bereitstellung der Domänencontroller für die verwaltete Domäne wird aufgehoben, und die Domänencontroller werden aus dem virtuellen Netzwerk entfernt.
>   * Die Daten in der verwalteten Domäne werden endgültig gelöscht. Dazu gehören u.a. benutzerdefinierte Organisationseinheiten, GPOs, benutzerdefinierte DNS-Einträge, Dienstprinzipale und gruppenverwaltete Dienstkonten (GMSAs), die Sie erstellt haben.
>   * Computer, die in die verwaltete Domäne eingebunden sind, verlieren ihre Vertrauensstellung mit der Domäne und müssen aus der Domäne entfernt werden.
>       * Bei diesen Computern können Sie sich nicht mit AD-Unternehmensanmeldeinformationen anmelden. Stattdessen müssen Sie die Anmeldeinformationen des lokalen Administrators für den Computer verwenden.

## <a name="delete-the-managed-domain"></a>Löschen der verwalteten Domäne

Führen Sie die folgenden Schritte aus, um eine verwaltete Domäne zu löschen:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Azure AD Domain Services**, und wählen Sie ihn aus.
1. Wählen Sie den Namen Ihrer verwalteten Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie auf der Seite **Übersicht** die Option **Löschen** aus. Geben Sie zum Bestätigen des Löschvorgangs erneut den Domänennamen der verwalteten Domäne ein, und wählen Sie dann **Löschen** aus.

Das Löschen der verwalteten Domäne kann zwischen 15 und 20 Minuten (oder länger) dauern.

## <a name="next-steps"></a>Nächste Schritte

Ziehen Sie [Feedback][feedback] in Betracht, um uns mitzuteilen, welche Funktionen Sie sich in Azure AD DS wünschen.

Wenn Sie erneut mit Azure AD DS beginnen möchten, lesen Sie [Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789?c=5d63b5b7-ae25-ec11-b6e6-000d3a4f0789
[create-instance]: tutorial-create-instance.md
