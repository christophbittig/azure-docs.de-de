---
title: Hinzufügen eines benutzerdefinierten ZS-Zertifikats – Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure API Management ein benutzerdefiniertes ZS-Zertifikat hinzufügen. Sie können auch Anleitungen zum Löschen eines Zertifikats anzeigen.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 06/01/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e92d4a6c81376a9dbd004ac7fee978fa4d67e9a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628817"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Hinzufügen eines benutzerdefinierten ZS-Zertifikats in Azure API Management

Azure API Management ermöglicht die Installation von ZS-Zertifikaten (Zertifizierungsstellenzertifikaten) auf dem Computer im vertrauenswürdigen Stammzertifikatspeicher und den Zwischenzertifikatspeichern. Diese Funktion sollte verwendet werden, wenn Ihre Dienste ein benutzerdefiniertes ZS-Zertifikat erfordern.

Dieser Artikel erläutert die Verwaltung von ZS-Zertifikaten einer Azure API Management-Dienstinstanz im Azure-Portal. Wenn Sie beispielsweise selbstsignierte Clientzertifikate verwenden, können Sie benutzerdefinierte vertrauenswürdige Stammzertifikate in API Management hochladen. 

In API Management hochgeladene Zertifizierungsstellenzertifikate können nur für die Zertifikatüberprüfung durch das verwaltete API Management-Gateway verwendet werden. Wenn Sie das [selbstgehostete Gateway](self-hosted-gateway-overview.md) verwenden, erfahren Sie weiter unten in diesem Artikel, wie Sie [eine benutzerdefinierte Zertifizierungsstelle für das selbstgehostete Gateway erstellen](#create-custom-ca-for-self-hosted-gateway).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Hochladen eines ZS-Zertifikats

:::image type="content" source="media/api-management-howto-ca-certificates/00.png" alt-text="Zertifizierungsstellenzertifikate im Azure-Portal":::

Gehen Sie folgendermaßen vor, um ein neues ZS-Zertifikat hochzuladen. Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Tutorial [Erstellen einer API Management-Dienstinstanz](get-started-create-service-instance.md).

1. Navigieren Sie im Azure-Portal zu Ihrer Azure API Management-Dienstinstanz.

1. Wählen Sie in dem Menü unter **Sicherheit** die Option **Zertifikate > Zertifizierungsstellenzertifikate > + Hinzufügen** aus.

1. Suchen Sie nach dem CER-Zertifikatdatei, und wählen Sie den Zertifikatspeicher aus. Nur der öffentliche Schlüssel ist erforderlich, das Kennwort ist optional.

    :::image type="content" source="media/api-management-howto-ca-certificates/02.png" alt-text="Hinzufügen eines Zertifizierungsstellenzertifikats im Azure-Portal"::: 

1. Wählen Sie **Speichern** aus. Dieser Vorgang kann einige Minuten dauern.

> [!NOTE]
> Sie können ein Zertifizierungsstellenzertifikat auch mithilfe des PowerShell-Befehls `New-AzApiManagementSystemCertificate` hochladen.

## <a name="delete-a-ca-certificate"></a><a name="step1a"> </a>Löschen eines Zertifizierungsstellenzertifikats.

Wählen Sie das Zertifikat und dann im Kontextmenü ( **...** ) die Option **Löschen** aus.

## <a name="create-custom-ca-for-self-hosted-gateway"></a>Erstellen einer benutzerdefinierten Zertifizierungsstelle für ein selbstgehostetes Gateway 

Wenn Sie ein [selbstgehostetes Gateway](self-hosted-gateway-overview.md) verwenden, wird die Überprüfung von Server- und Clientzertifikaten mithilfe von Zertifizierungsstellen-Stammzertifikaten, die in den API Management-Dienst hochgeladen wurden, nicht unterstützt. Um eine Vertrauensstellung herzustellen, konfigurieren Sie ein spezifisches Clientzertifikat, damit es vom Gateway als benutzerdefinierte Zertifizierungsstelle als vertrauenswürdig eingestuft wird.

Verwenden Sie die REST-APIs der [Gatewayzertifizierungsstelle](/rest/api/apimanagement/2021-01-01-preview/gateway-certificate-authority), um benutzerdefinierte Zertifizierungsstellen für ein selbstgehostetes Gateway zu erstellen und zu verwalten. So erstellen Sie eine benutzerdefinierte Zertifizierungsstelle

1. Fügen Sie Ihrer API Management-Instanz [eine PFX-Zertifikatdatei hinzu](api-management-howto-mutual-certificates.md).
1. Verwenden Sie die REST-API [Gatewayzertifizierungsstelle – Erstellen oder Aktualisieren](/rest/api/apimanagement/2021-01-01-preview/gateway-certificate-authority/create-or-update), um das Zertifikat dem selbstverwalteten Gateway zuzuordnen.

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
