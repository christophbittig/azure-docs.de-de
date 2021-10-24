---
title: 'Problembehandlung bei einer fehlerhaften Dienstanforderung: Fehler 404 (Unterdomäne nicht gefunden)'
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Statusantworten des Fehlers 404 (Unterdomäne nicht gefunden) aus Azure Digital Twins diagnostizieren und beheben.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 9/23/2021
ms.openlocfilehash: 91125140632d689eaab18a74b7da75fe3c69800b
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132244"
---
# <a name="troubleshooting-failed-service-request-error-404-sub-domain-not-found"></a>Problembehandlung bei einer fehlerhaften Dienstanforderung: Fehler 404 (Unterdomäne nicht gefunden)

In diesem Artikel werden die Ursachen sowie die Schritte zur Behebung im Falle eines 404-Fehlers bei Service Requests für Azure Digital Twins beschrieben. 

## <a name="symptoms"></a>Symptome

Dieser Fehler kann auftreten, wenn auf eine Azure Digital Twins-Instanz mit einem Dienstprinzipal oder Benutzerkonto zugegriffen wird, der bzw. das zu einem anderen [Azure AD-Mandanten (Azure Active Directory)](../active-directory/develop/quickstart-create-new-tenant.md) gehört. Der Identität scheinen die richtigen [Rollen](concepts-security.md) zugewiesen zu sein, aber bei API-Anforderungen tritt ein Fehler mit dem Status `404 Sub-Domain not found` auf.

## <a name="causes"></a>Ursachen

### <a name="cause-1"></a>Ursache 1

Azure Digital Twins erfordert, dass alle authentifizierenden Benutzer zum gleichen Azure AD-Mandanten gehören wie die Azure Digital Twins-Instanz.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>Lösungen

### <a name="solution-1"></a>Lösung 1

Sie können dieses Problem beheben, indem Sie für jede Verbundidentität eines anderen Mandanten ein **Token** vom Basismandanten der Azure Digital Twins-Instanz anfordern. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>Lösung 2

Wenn Sie in Ihrem Code die Klasse `DefaultAzureCredential` verwenden und dieses Problem nach dem Abrufen eines Tokens weiterhin auftritt, können Sie den Basismandanten in den Optionen vom Typ `DefaultAzureCredential` angeben, um den Mandanten zu identifizieren, auch wenn von der Authentifizierung standardmäßig ein anderer Typ verwendet wird.

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich weiter über die Sicherheit und Berechtigungen in Azure Digital Twins:
* [Sicherheit für Azure Digital Twins-Lösungen](concepts-security.md)
