---
title: Fehlerhafte Links auf der Seite für eine Azure Active Directory-Anwendungsproxyanwendung
description: Beheben von Problemen mit fehlerhaften Links in Anwendungsproxyanwendungen, die Sie in Azure Active Directory integriert haben
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 905f25d449c8de5046a83c4f66139fdd23227b2b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988979"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Links auf der Seite funktionieren nicht für eine Anwendungsproxyanwendung

In diesem Artikel finden Sie Hilfe zur Problembehandlung bei nicht ordnungsgemäß funktionierenden Links in der Azure Active Directory-Anwendungsproxyanwendung.

## <a name="overview"></a>Übersicht 
Nach dem Veröffentlichen einer Anwendungsproxy-App funktionieren standardmäßig nur Links in der Anwendung, die auf Ziele verweisen, die in der veröffentlichten Stamm-URL enthalten sind. Links innerhalb der Anwendungen funktionieren nicht, da die interne URL für die Anwendung möglicherweise nicht alle Ziele der Links in der Anwendung enthält.

**Was ist dafür die Ursache?** Wenn Sie auf einen Link in einer Anwendung klicken, versucht der Anwendungsproxy, die URL entweder als interne URL innerhalb derselben Anwendung oder als extern verfügbare URL aufzulösen. Wenn der Link auf eine interne URL verweist, die sich nicht innerhalb derselben Anwendung befindet, gehört sie keinem der Buckets an. Dies führt zu einem Fehler, dass das Ziel nicht gefunden wurde.

## <a name="ways-you-can-resolve-broken-links"></a>Methoden zum Beheben fehlerhafter Links

Es gibt drei Möglichkeiten, dieses Problem zu beheben. Die im Folgenden aufgeführten Optionen sind nach zunehmender Komplexität aufgeführt.

1.  Stellen Sie sicher, dass die interne URL eine Stamm-URL ist, die alle relevanten Links für die Anwendung enthält. Dadurch werden alle Links als innerhalb derselben Anwendung veröffentlichter Inhalt aufgelöst.

    Wenn Sie die interne URL ändern, die Zielseite für Benutzer jedoch nicht geändert werden soll, ändern Sie die URL der Startseite in die zuvor veröffentlichte interne URL. Wechseln Sie dazu zu „Azure Active Directory -&gt; App-Registrierungen -&gt; Anwendung auswählen -&gt; Branding“. Im Abschnitt „Branding“ befindet sich das Feld „URL der Startseite“, in dem Sie die gewünschte Startseite eingeben können. Wenn Sie noch die Legacybenutzeroberfläche für App-Registrierungen verwenden, werden auf der Registerkarte „Eigenschaften“ die Details der „URL der Startseite“ angezeigt. 
    
    > [!IMPORTANT]
    > Um die oben genannten Änderungen vornehmen zu können, benötigen Sie Rechte zum Ändern von Anwendungsobjekten in Azure AD. Dem Benutzer muss die Rolle [Anwendungsadministrator](../roles/delegate-app-roles.md#assign-built-in-application-admin-roles) zugewiesen werden, die dem Benutzer Rechte zum Ändern von Anwendungen in Azure AD erteilt.
    >

2.  Wenn Ihre Anwendung vollqualifizierte Domänennamen (FQDNs) verwendet, verwenden Sie [Benutzerdefinierte Domänen](application-proxy-configure-custom-domain.md), um Ihre Anwendungen zu veröffentlichen. Durch dieses Feature kann dieselbe URL sowohl intern als auch extern verwendet werden.

    Mit dieser Option wird sichergestellt, dass die Links in der Anwendung extern über den Anwendungsproxy zugänglich sind, da die Links mit internen URLs in der Anwendung auch extern erkannt werden. Alle Links müssen dennoch zu einer veröffentlichten Anwendung gehören. Mit dieser Option müssen die Links jedoch nicht zur gleichen Anwendung gehören und können mehreren Anwendungen angehören.

3.  Wenn keine dieser Optionen möglich ist, stehen mehrere Optionen zum Aktivieren der Inlinelinkübersetzung zur Verfügung. Dazu gehören die Verwendung des Intune Managed Browser, der Erweiterung „Meine Apps“ oder der Einstellung zur Linkübersetzung in Ihrer Anwendung. Weitere Informationen zu diesen Optionen und ihrer Aktivierung finden Sie unter [Umleiten von hartcodierten Links für Apps, die mit Azure AD-Anwendungsproxy veröffentlicht wurden](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Nächste Schritte
[Verwenden von vorhandenen lokalen Proxyservern](application-proxy-configure-connectors-with-proxy-servers.md)

