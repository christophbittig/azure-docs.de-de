---
title: Ändern von Verzeichnismandanten mit Ihren individuellen VSS-Azure-Abonnements
description: Ändern von Verzeichnismandanten mit Ihren VSS-Azure-Abonnements.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: f2d1bb309a841beac4149e48ed48dcfa21f27c34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095560"
---
# <a name="change-directory-tenants-with-your-azure-subscriptions"></a>Ändern von Verzeichnismandanten mit Ihren VSS-Azure-Abonnements  

Organisationen verfügen möglicherweise über mehrere Azure-Gutschriftabonnements. Jedes Abonnement, das von einer Organisation eingerichtet wird, ist einer [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)-Instanz zugeordnet. (Azure AD)  

Azure AD ist der cloudbasierte Identitäts- und Zugriffsverwaltungsdienst von Microsoft, mit dem sich Ihre Mitarbeiter anmelden und auf interne und externe Ressourcen zugreifen können.  

Möglicherweise müssen Sie die Active Directory-Instanz ändern, in der Sie arbeiten, oder [Ihr Abonnement in eine andere Active Directory-Instanz übertragen](../../role-based-access-control/transfer-subscription.md).  

Wenn Sie Ihr Abonnement aktivieren, wird Ihre Identität basierend auf der von Ihnen verwendeten E-Mail-Adresse erstellt. Diese Identität ist entweder dem Active Directory-Mandanten Ihrer Organisation zugeordnet, oder es wird ein neuer Verzeichnismandant für diese Identität erstellt. Die Identität, die Sie verwenden, wird oben rechts im Azure-Portal angezeigt.  

![Screenshot des Azure-Portals mit hervorgehobener Identität in der oberen rechten Ecke.](media/how-to-change-directory-tenants-visual-studio-azure/identity.png "Die angemeldete Identität wird in der oberen rechten Ecke im Azure-Portal angezeigt.")  

Hier können Sie Identitäten oder Verzeichnisse wechseln. Möglicherweise müssen Sie Ihre Identität ändern, um auf bestimmte Verzeichnisse zuzugreifen.  

Wenn die Identität, mit der Sie angemeldet sind, mehreren Verzeichnissen zugeordnet ist, ändern Sie sie, indem Sie „Verzeichnis wechseln“ auswählen. Es werden die Verzeichnisse angezeigt, mit denen Ihre aktuelle Identität verknüpft ist.  

![Screenshot des Fensters „Verzeichnis und Abonnement“ im Azure-Portal.](media/how-to-change-directory-tenants-visual-studio-azure/switch-directory.png "Wechseln Sie die Verzeichnisse, indem Sie auf „Verzeichnis wechseln“ klicken. Wählen Sie das gewünschte Verzeichnis aus.")  

Ihre Umgebung im Portal hängt stark von dem Verzeichnis ab, das der von Ihnen verwendeten Identität zugeordnet ist. Um Verzeichnismandanten zu ändern, muss ein Administrator Ihre Identität als Benutzer innerhalb des Zielverzeichnisses hinzufügen.  

## <a name="importance-of-changing-your-azure-active-directory-tenant"></a>Wichtigkeit der Änderung Ihres Azure Active Directory-Mandanten  

Wenn Sie Ihr Azure-Guthabenabonnement über eine Visual Studio-Lizenz einrichten, können Sie eine E-Mail-Arbeitsadresse oder eine persönliche E-Mail-Adresse verwenden, um Ihre Identität zu erstellen.  

Wenn Sie Ihr Guthabenabonnement mit einem persönlichen Konto einrichten, sind Ihre Identität und Ihre E-Mail-Arbeitsadresse von der Active Directory-Instanz Ihrer Organisation isoliert. Angenommen, Sie arbeiten an einer App, die vor der Bereitstellung ein eigenes Abonnement zum Testen und Ausprobieren benötigt. Jetzt benötigen Sie Zugriff auf die Dokumente oder Referenzen Ihrer Organisation. Wenn Sie den Mandanten Ihres Verzeichnisses ändern, können Sie auf die Ressourcen Ihrer Organisation zugreifen und diese auf Ihre Ressourcen.  

Nachstehend finden Sie eine einfache Abbildung, die die grundlegenden Schritte bei einer Änderung oder Übertragung Ihres Abonnements zeigt.

![Abbildung, die die Änderung oder Übertragung Ihres Abonnements zeigt.](media/how-to-change-directory-tenants-visual-studio-azure/change-diagram.png "Abbildung, die zeigt, was geschieht, wenn Sie Ihr Abonnement ändern oder übertragen.")  

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

Wo und auf welche Weise Sie Zugriff besitzen, gibt basierend auf Ihren Anmeldeinformationen vor, was angezeigt wird. Dieser Zugriff kann auf verschiedenen Ebenen innerhalb der Hierarchie der Organisation gewährt werden. Sie können Zugriff auf Verzeichnisebene, Abonnementebene oder innerhalb von Ressourcengruppen erhalten.  

![Screenshot: Azure-Zugriffsebenen.](media/how-to-change-directory-tenants-visual-studio-azure/access-management.png "Die in Azure verfügbaren Zugriffsebenen.")  

Sie können Ihre Zugriffsebenen in der Zugriffssteuerung anzeigen und verwalten. Sie können auch den Zugriff anderer Benutzer auf das Abonnement abhängig von Ihren Zugriffsebenen verwalten.  

![Screenshot: Zugriffssteuerungsseite des Visual Studio-Abonnements.](media/how-to-change-directory-tenants-visual-studio-azure/access-control.png "Verwalten Sie den Zugriff auf Ihr Abonnement.")

## <a name="how-to-change-your-azure-directory-tenant"></a>Ändern Ihres Azure-Verzeichnismandanten

Für den Zugriff auf eine andere Active Directory-Instanz benötigen Sie ein aktives Konto mit den erforderlichen Berechtigungen und Zugriff zum Ändern von Verzeichnismandanten. Ein Administrator innerhalb des Verzeichnismandanten, auf den Sie zugreifen möchten, kann Sie in den folgenden Rollen hinzufügen:

* Benutzer
* Gast  

Nachdem Sie hinzugefügt wurden und die richtigen Berechtigungen erhalten haben, können Sie die Verzeichnisse in Ihrem Abonnement wechseln.  

1. Melden Sie sich an, und wählen Sie [auf der Seite „Abonnements“ im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ein Abonnement aus.  
2. Wählen Sie „Verzeichnis ändern“ aus.  

    ![Screenshot: Seite „Contoso Enterprise-Abonnement“ mit hervorgehobener Option „Verzeichnis ändern“.](media/how-to-change-directory-tenants-visual-studio-azure/change-directory.png "Wählen Sie „Verzeichnis ändern“ aus.") 
3. Ein Feld wird angezeigt, um das neue Verzeichnis auszuwählen.  
4. Wählen Sie „Ändern“ aus.  

    > [!NOTE]
    > Wenn Sie keinen Zugriff auf das Zielverzeichnis haben, wird es nicht angezeigt. Nachdem das Verzeichnis für das Abonnement geändert wurde, wird eine Erfolgsmeldung angezeigt.  

    ![Screenshot: Überprüfungsseite „Verzeichnis ändern“.](media/how-to-change-directory-tenants-visual-studio-azure/change-button.png "Wählen Sie das Verzeichnis aus der Dropdownliste aus, und klicken Sie auf die Schaltfläche „Ändern“.")
5. Wählen Sie auf der Abonnementseite „Verzeichnis wechseln“ aus, um auf das neue Verzeichnis zuzugreifen.  

  ![Screenshot: Seite „Abonnements“ mit hervorgehobener Option „Verzeichnisse wechseln“.](media/how-to-change-directory-tenants-visual-studio-azure/switch-directories-outlined.png "Klicken Sie auf „Verzeichnisse wechseln“, um auf das neue Verzeichnis zuzugreifen.")

Sie können auch auf ein Zielverzeichnis zugreifen und Ihren Mandanten ändern, indem Sie zum Zielverzeichnis navigieren und einen Administrator hinzufügen. Befolgen Sie [diese Anweisungen](/visualstudio/subscriptions/cloud-admin.md) zum Hinzufügen eines Administrators zu Ihrem Abonnement. Anschließend besitzt der Administrator Zugriff auf beide Verzeichnisse und kann das Mandantenverzeichnis für Sie ändern.  
