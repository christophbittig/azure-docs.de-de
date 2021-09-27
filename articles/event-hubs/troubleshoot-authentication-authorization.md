---
title: 'Behandeln von Authentifizierungs- und Autorisierungsproblemen: Azure Event Hubs'
description: Dieser Artikel bietet Informationen zum Beheben von Authentifizierungs- und Autorisierungsproblemen mit Azure Event Hubs.
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: 0561ab94f113bf849d5492b93171c9b14540de44
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656833"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>Behandeln von Authentifizierungs- und Autorisierungsproblemen: Azure Event Hubs
Dieser Artikel bietet Tipps zum [Beheben von Konnektivitätsproblemen](troubleshooting-guide.md) mit Azure Event Hubs. Dieser Artikel bietet Tipps und Empfehlungen zum Beheben von Authentifizierungs- und Autorisierungsproblemen mit Azure Event Hubs. 

## <a name="if-you-are-using-azure-active-directory"></a>Wenn Sie Azure Active Directory verwenden
Wenn Sie Azure Active Directory (Azure AD) für die Authentifizierung und Autorisierung mit Azure Event Hubs verwenden, bestätigen Sie, dass die Identität, die auf den Event Hub zugreift, Mitglied der richtigen **Azure-Rolle** im richtigen **Ressourcenbereich** ist (Consumergruppe, Event Hub, Namespace, Ressourcengruppe oder Abonnement).

### <a name="azure-roles"></a>Azure-Rollen
- [Azure Event Hubs-Datenbesitzer](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) für den vollständigen Zugriff auf Event Hubs-Ressourcen.
- [Azure Event Hubs-Datenabsender](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) für den Sendezugriff.
- [Azure Event Hubs-Datenempfänger](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) für den Empfangszugriff.

Informationen zu den integrierten Rollen für die Schemaregistrierung finden Sie unter [Schemaregistrierungsrollen](schema-registry-overview.md#azure-role-based-access-control).

### <a name="resource-scopes"></a>Ressourcenbereiche
- **Consumergruppe**: Bei diesem Umfang gilt die Rollenzuweisung nur für diese Entität. Derzeit unterstützt das Azure-Portal keine Zuweisung einer Azure-Rolle zu einem Sicherheitsprinzipal auf dieser Ebene. 
- **Event Hub**: Die Rollenzuweisung gilt für die Event Hub-Entität und die darunter vorhandene Consumergruppe.
- **Namespace**: Die Rollenzuweisung umfasst die gesamte Topologie von Event Hubs unter dem Namespace sowie die ihm zugeordnete Consumergruppe.
- **Ressourcengruppe**: Die Rollenzuweisung gilt für alle Event Hubs-Ressourcen unter der Ressourcengruppe.
- **Abonnement**: Die Rollenzuweisung gilt für alle Event Hubs-Ressourcen in allen Ressourcengruppen im Abonnement.

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Authentifizieren einer Anwendung mit Azure Active Directory, um auf Azure Event Hubs-Ressourcen zuzugreifen](authenticate-application.md)
- [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Azure Active Directory](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>Wenn SieShared Access Signatures (SAS) verwenden
Wenn Sie [SAS](authenticate-shared-access-signature.md) verwenden, führen Sie die folgenden Schritte aus: 

- Stellen Sie sicher, dass der von Ihnen verwendete SAS-Schlüssel richtig ist. Wenn dies nicht der Fall ist, verwenden Sie den richtigen SAS-Schlüssel.
- Vergewissern Sie sich, dass der Schlüssel über die richtigen Berechtigungen verfügt (Senden, Empfangen oder Verfügterwalten). Wenn dies nicht der Fall ist, verwenden Sie einen Schlüssel, der über die erforderliche Berechtigung verfügt. 
- Überprüfen Sie, ob der Schlüssel abgelaufen ist. Es wird empfohlen, den SAS-Schlüssel rechtzeitig vor dem Ablauf zu erneuern. Wenn es eine Zeitabweichung zwischen dem Client und den Dienstknoten der Event Hubs gibt, kann das Authentifizierungstoken ablaufen, bevor der Client dies bemerkt. Die aktuelle Implementierung berücksichtigt eine Zeitabweichung von bis zu 5 Minuten, d. h. der Client erneuert das Token 5 Minuten vor Ablauf. Wenn die Zeitabweichung größer als 5 Minuten ist, können daher zeitweilig Authentifizierungsfehler für den Client auftreten.
- Wenn die **SAS-Startzeit** auf **jetzt** festgelegt ist, kann es in den ersten Minuten zu zeitweiligen Fehlern aufgrund von Zeitabweichungen (Unterschiede bei der aktuellen Uhrzeit auf verschiedenen Computern) kommen. Legen Sie als Startzeit eine Uhrzeit fest, die mindestens 15 Minuten in der Vergangenheit liegt, oder legen Sie gar keine Startzeit fest. Dasselbe gilt auch für die Ablaufzeit. 

Weitere Informationen finden Sie in den folgenden Artikeln: 

- [Authentifizieren mit SAS (Shared Access Signatures)](authenticate-shared-access-signature.md). 
- [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Shared Access Signatures](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in folgenden Artikeln:

* [Behandeln von Konnektivitätsproblemen](troubleshooting-guide.md)
