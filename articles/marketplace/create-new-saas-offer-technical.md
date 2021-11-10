---
title: Hinzufügen technischer Details für ein SaaS-Angebot in Azure Marketplace
description: Geben Sie technische Details für ein SaaS-Angebot (Software-as-a-Service) in Azure Marketplace an.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 39e41febed0809486d2e6796284d86a0874c13f5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059674"
---
# <a name="add-technical-details-for-a-saas-offer"></a>Hinzufügen technischer Details für ein SaaS-Angebot

In diesem Artikel wird beschrieben, wie technische Details eingegeben werden, die es ermöglichen, eine Verbindung zwischen dem kommerziellen Microsoft-Marketplace und Ihrer Lösung herzustellen. Durch diese Verbindung können wir Ihr Angebot für den Kunden bereitstellen, wenn er es kaufen und verwalten möchte. Weitere Informationen zu diesen Einstellungen finden Sie unter [Technische Informationen](plan-saas-offer.md#technical-information).

> [!NOTE]
> Wenn Sie Transaktionen unabhängig verarbeiten möchten, wird diese Option nicht angezeigt. In diesem Fall können Sie mit [Verkaufen Ihres SaaS-Angebots](create-new-saas-offer-marketing.md) fortfahren.

## <a name="technical-configuration"></a>Technische Konfiguration

Auf der Registerkarte **Technische Konfiguration** definieren Sie die technischen Details, die vom kommerziellen Marketplace für die Kommunikation mit Ihrer SaaS-Anwendung oder -Lösung verwendet werden.

- **URL der Angebotsseite** (erforderlich): Definieren Sie die URL der SaaS-Website (z. B. `https://contoso.com/signup`), zu der Kunden gelangen, nachdem sie Ihr Angebot im kommerziellen Marketplace erworben und den Konfigurationsvorgang vom neu erstellten SaaS-Abonnement aus gestartet haben.

  > [!IMPORTANT]
  > Ihre Angebotsseite sollte rund um die Uhr aktiv sein. Dies ist die einzige Möglichkeit, Sie über neue Käufe Ihrer SaaS-Angebote im kommerziellen Marketplace oder über Konfigurationsanforderungen eines aktiven Abonnements für ein Angebot zu benachrichtigen. Fügen Sie das Pfund-Zeichen (#) nicht in die URL der Landing Page ein. Andernfalls können die Kunden nicht auf Ihre Landing Page zugreifen.

- **Verbindungswebhook** (erforderlich): Für alle asynchronen Ereignisse, die Microsoft an Sie senden muss (etwa, wenn das SaaS-Abonnement gekündigt wurde), muss eine [Verbindungswebhook-URL](./partner-center-portal/pc-saas-fulfillment-webhook.md) angegeben werden. Diese URL wird aufgerufen, um Sie über das Ereignis zu informieren.

  > [!IMPORTANT]
  > Ihr Webhook sollte rund um die Uhr in Betrieb sein, da Sie nur so über Aktualisierungen der SaaS-Abonnements Ihrer Kunden, welche über den kommerziellen Marktplatz erworben wurden, informiert werden.

- **Azure Active Directory-Mandanten-ID** (erforderlich): Um die Mandanten-ID für Ihre Azure Active Directory-App (Azure AD) zu ermitteln, wechseln Sie in Azure Active Directory zum Blatt [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade). Wählen Sie in der Spalte **Anzeigename** die App aus. Suchen Sie dann nach der aufgeführten Nummer für **Verzeichnis-ID (Mandant)** (z. B. `50c464d3-4930-494c-963c-1e951d15360e`).

- **Azure Active Directory-Anwendungs-ID** (erforderlich): Um die [Anwendungs-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) zu ermitteln, wechseln Sie in Azure Active Directory zum Blatt [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade). Wählen Sie in der Spalte **Anzeigename** die App aus. Suchen Sie dann nach der aufgeführten Nummer für die Anwendungs-ID (Client) wie z. B. `50c464d3-4930-494c-963c-1e951d15360e`.

Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren: Planübersicht

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen von Plänen für ein SaaS-Angebot](create-new-saas-offer-plans.md)
