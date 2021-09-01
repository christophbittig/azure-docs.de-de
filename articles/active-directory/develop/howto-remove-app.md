---
title: 'Vorgehensweise: Entfernen einer registrierten App aus der Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Anleitung erfahren Sie, wie Sie eine Anwendung entfernen, die bei der Microsoft Identity Platform registriert ist.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 0cea2fdd28c486dbd4b1ca3872cb4e5afaa0379a
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039680"
---
# <a name="remove-an-application-registered-with-the-microsoft-identity-platform"></a>Entfernen einer bei Microsoft Identity Platform registrierten Anwendung

Unternehmensentwickler und SaaS-Anbieter (Software-as-a-Service), die Anwendungen bei Microsoft Identity Platform registriert haben, müssen die Registrierung einer Anwendung unter Umständen entfernen.

In diesen Abschnitten wird Folgendes vermittelt:

* Entfernen einer Anwendung, die von Ihnen oder Ihrer Organisation erstellt wurde
* Entfernen einer Anwendung, die von einer anderen Organisation erstellt wurde

## <a name="prerequisites"></a>Voraussetzungen

* Eine [Anwendung, die in Ihrem Azure AD-Mandanten registriert ist](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Entfernen einer Anwendung, die von Ihnen oder Ihrer Organisation erstellt wurde

Anwendungen, die von Ihnen oder Ihrer Organisation registriert wurden, werden in Ihrem Mandanten durch ein Anwendungsobjekt und ein Dienstprinzipalobjekt dargestellt. Weitere Informationen finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte](./app-objects-and-service-principals.md).

> [!NOTE]
> Beim Löschen einer Anwendung wird auch das zugehörige Dienstprinzipalobjekt im Basisverzeichnis der Anwendung gelöscht. Bei mehrinstanzenfähigen Anwendungen werden Dienstprinzipalobjekte in anderen Verzeichnissen nicht gelöscht.

Zum Löschen einer Anwendung müssen Sie als Besitzer der Anwendung angegeben sein oder über Administratorberechtigungen verfügen.

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, unter dem die App registriert ist.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie die Option aus. 
1. Wählen Sie unter **Verwalten** die Option **App-Registrierungen** und dann die Anwendung aus, die Sie konfigurieren möchten. Nachdem Sie die App ausgewählt haben, wird die Seite **Übersicht** angezeigt.
1. Wählen Sie auf der Seite **Übersicht** die Option **Löschen**.
1. Lesen Sie die Informationen zu den Konsequenzen beim Löschen.  Aktivieren Sie das Kontrollkästchen, wenn es am unteren Rand des Bereichs angezeigt wird.
1. Wählen Sie **Löschen** aus, um zu bestätigen, dass Sie die App löschen möchten.

## <a name="remove-an-application-authored-by-another-organization"></a>Entfernen einer Anwendung, die von einer anderen Organisation erstellt wurde

Wenn Sie **App-Registrierungen** im Kontext eines Mandanten anzeigen, stammt ein Teil der Anwendungen, die auf der Registerkarte **Alle Apps** angegeben sind, von einem anderen Mandanten. Sie wurden während des Zustimmungsprozesses unter Ihrem Mandanten registriert. Genauer gesagt: Sie werden unter Ihrem Mandanten nur von einem Dienstprinzipalobjekt ohne entsprechendes Anwendungsobjekt repräsentiert. Weitere Informationen zu den Unterschieden zwischen Anwendungs- und Dienstprinzipalobjekten finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure AD](./app-objects-and-service-principals.md).

Der Administrator des Unternehmens muss seinen Dienstprinzipal entfernen, um für eine Anwendung den Zugriff auf Ihr Verzeichnis zu entfernen (nachdem die Zustimmung erteilt wurde). Der Administrator muss über Zugriffsberechtigungen vom Typ „Globaler Administrator“ verfügen und kann die Anwendung über das Azure-Portal entfernen oder die [Azure AD-PowerShell-Cmdlets](/previous-versions/azure/jj151815(v=azure.100)) zum Entfernen des Zugriffs verwenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über [Anwendungs- und Dienstprinzipalobjekte](app-objects-and-service-principals.md) in der Microsoft Identity Platform
