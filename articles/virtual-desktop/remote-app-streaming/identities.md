---
title: Einrichten verwalteter Identitäten in Azure Virtual Desktop – Azure
description: Hier erfahren Sie, wie Sie verwaltete Identitäten für Ihre Kunden in Azure Virtual Desktop mit Azure AD, Azure AD DS oder AD DS einrichten.
author: Heidilohr
ms.topic: how-to
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 98dcd68e62f04eaf9ae439389cb31e281cdb2f69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345902"
---
# <a name="set-up-managed-identities"></a>Einrichten von verwalteten Identitäten

Da Azure Virtual Desktop derzeit keine externen Profile oder „Identitäten“ unterstützt, können Ihre Benutzer nicht mit ihren unternehmenseigenen Anmeldeinformationen auf die von Ihnen gehosteten Apps zugreifen. Stattdessen müssen Sie Identitäten für sie in der Active Directory-Domäne erstellen, die Sie für das Remotestreaming von Apps verwenden und Benutzerobjekte mit dem zugeordneten Azure Active Directory-Mandanten (Azure AD) synchronisieren.

In diesem Artikel wird erläutert, wie Sie Benutzeridentitäten verwalten können, um eine sichere Umgebung für Ihre Kunden bereitzustellen. Außerdem werden die verschiedenen Teile einer Identität besprochen.

## <a name="requirements"></a>Requirements (Anforderungen)

Die von Ihnen erstellten Identitäten müssen die folgenden Richtlinien befolgen:

- Bei den Identitäten muss es sich um [Hybrididentitäten](../../active-directory/hybrid/whatis-hybrid-identity.md) handeln, was bedeutet, dass sie sowohl in [Active Directory (AD)](/previous-versions/windows/it-pro/windows-server-2003/cc781408(v=ws.10)) als auch in [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) vorhanden sind. Sie können entweder [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/active-directory-domain-services) oder [Azure Active Directory Domain Services (Azure AD DS)](https://azure.microsoft.com/services/active-directory-ds) verwenden, um diese Identitäten zu erstellen. Weitere Informationen zu den einzelnen Methoden finden Sie unter [Vergleichen von Identitätslösungen](../../active-directory-domain-services/compare-identity-solutions.md).
- Sie sollten Benutzer aus verschiedenen Organisationen in separaten Azure AD-Mandanten aufnehmen, um Sicherheitsverletzungen zu vermeiden. Wir empfehlen, eine Active Directory-Domäne und einen Azure Active Directory-Mandanten pro Kunde zu erstellen. Dieser Mandant sollte über ein eigenes Azure AD DS- oder AD DS-Abonnement verfügen, das diesem Kunden zugeordnet ist.

In den folgenden beiden Abschnitten erfahren Sie, wie Sie Identitäten mit AD DS und Azure AD DS erstellen können. Um die [Sicherheitsrichtlinien für organisationsübergreifende Apps](security.md) zu befolgen, müssen Sie den Vorgang für jeden Kunden wiederholen.

## <a name="managing-users-with-active-directory-domain-services"></a>Verwalten von Benutzern mit Active Directory Domain Services

Bei dieser Methode werden Sie Hybrididentitäten mithilfe eines Active Directory-Domänencontrollers einrichten, um Benutzeridentitäten zu verwalten und sie mit Azure AD zu synchronisieren.

Diese Methode umfasst die Einrichtung von Active Directory-Domänencontrollern zur Verwaltung der Benutzeridentitäten und die Synchronisierung der Benutzer mit Azure AD, um Hybrididentitäten zu erstellen. Diese Identitäten können dann für den Zugriff auf gehostete Anwendungen in Azure Virtual Desktop verwendet werden. In dieser Konfiguration werden die Benutzer von Active Directory mit Azure AD synchronisiert und die virtuellen Sitzungshostcomputer werden mit der AD DS-Domäne verknüpft.

So richten Sie eine Identität in AD DS ein

1. [Erstellen Sie einen Azure AD-Mandanten](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) und ein Abonnement für Ihren Kunden.

2. [Installieren Sie Active Directory Domain Services](/windows-server/identity/ad-ds/deploy/install-active-directory-domain-services--level-100-) auf dem virtuellen Computer (VM) mit Windows Server, den Sie für den Kunden verwenden.

3. Installieren und konfigurieren Sie [Azure AD Connect](../../active-directory/hybrid/how-to-connect-install-roadmap.md) auf einer separaten VM, die in eine Domäne eingebunden ist, um die Benutzerkonten von Active Directory mit Azure Active Directory zu synchronisieren.

4. Wenn Sie planen, die VMs mithilfe von Intune zu verwalten, aktivieren Sie in [Azure AD Hybrid eingebundene Geräte](../../active-directory/devices/hybrid-azuread-join-plan.md) mit Azure AD Connect.

5. Sobald Sie die Umgebung konfiguriert haben, [erstellen Sie neue Benutzer](/previous-versions/windows/it-pro/windows-server-2003/cc755607(v=ws.10)) in Active Directory. Diese Benutzer sollten automatisch mit Azure AD synchronisiert werden.

6. Verwenden Sie bei der Bereitstellung von Sitzungshosts in Ihrem Hostpool den Active Directory-Domänennamen, um die VMs zu verknüpfen, und stellen Sie sicher, dass die Sitzungshosts über eine Sichtverbindung zum Domänencontroller verfügen.

Diese Konfiguration gibt Ihnen mehr Kontrolle über Ihre Umgebung, ist aber aufgrund ihrer Komplexität nicht immer einfach zu verwalten. Mit dieser Option können Sie Ihren Benutzern jedoch Azure AD-basierte Apps bereitstellen. Außerdem können Sie die VMs Ihrer Benutzer mit Intune verwalten.

## <a name="managing-users-with-azure-active-directory-domain-services"></a>Verwalten von Benutzern mit Azure Active Directory Domain Services

Azure AD DS-Identitäten werden in einem von Microsoft verwalteten Active Directory-PaaS (Platform as a Service) gespeichert, in dem Microsoft zwei AD-Domänencontroller verwaltet, mit denen Benutzer AD DS innerhalb ihrer Azure-Abonnements verwenden können. In dieser Konfiguration werden die Benutzer von Azure AD mit Azure AD DS synchronisiert, und die Sitzungshosts werden in die Azure AD DS-Domäne eingebunden. Azure AD DS-Identitäten sind einfacher zu verwalten, bieten aber keine so umfassende Kontrolle wie reguläre AD DS-Identitäten. Sie können die Azure Virtual Desktop-VMs nur in die Azure AD DS-Domäne einbinden und sie nicht mit Intune verwalten.

So erstellen Sie eine Identität mit Azure AD DS

1. [Erstellen Sie einen Azure AD-Mandanten](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) und ein -Abonnement für Ihren Kunden.

2. [Stellen Sie Azure AD Directory Services](../../active-directory-domain-services/tutorial-create-instance.md) im Abonnement des Benutzers bereit.

3. Nachdem Sie die Konfiguration der Umgebung abgeschlossen haben, [erstellen Sie neue Benutzer](../../active-directory/fundamentals/add-users-azure-active-directory.md) in Azure Active Directory. Diese Benutzerobjekte werden automatisch mit Azure AD DS synchronisiert.

4. Verwenden Sie beim Bereitstellen von Sitzungshosts in einem Hostpool den Azure AD DS-Domänennamen, um den VMs beizutreten.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mehr über Sicherheitsüberlegungen bei der Einrichtung von Identitäten und Mandanten erfahren möchten, lesen Sie die [Sicherheitsrichtlinien für organisationsübergreifende Apps](security.md).
