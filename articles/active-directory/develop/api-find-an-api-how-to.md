---
title: Suchen nach einer API für eine benutzerdefiniert entwickelte App | Azure
description: Konfigurieren der Berechtigungen, die für den Zugriff auf eine bestimmte API in einer benutzerdefinierten Azure AD-Anwendung benötigt werden
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 2d5c7f6e603b79b87297b0bbadb3d210ceaf0484
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154658"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Suchen einer bestimmten API, die für eine benutzerdefinierte Anwendung benötigt wird

Für den Zugriff auf APIs müssen Zugriffsbereiche und Rollen konfiguriert werden. Konfigurieren Sie Zugriffsbereiche und Rollen für die API, wenn Sie die Web-APIs Ihrer Ressourcenanwendung für Clientanwendungen verfügbar machen möchten. Konfigurieren Sie Zugriffsberechtigungen für die API in der App-Registrierung, wenn eine Clientanwendung auf eine Web-API zugreifen soll.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurieren einer Ressourcenanwendung zum Bereitstellen von Web-APIs

Wenn Sie Ihre Web-API verfügbar machen, wird die API in der Liste **Hiermit wählen Sie eine API aus.** angezeigt, wenn Berechtigungen zu einer App-Registrierung hinzugefügt werden. Befolgen Sie für das Hinzufügen von Zugriffsbereichen die Schritte in [Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs

Wenn Sie Ihrer App-Registrierung Berechtigungen hinzufügen, können Sie für verfügbar gemachte Web-APIs **API-Zugriff hinzufügen**. Befolgen Sie für den Zugriff auf Web-APIs die Schritte in [Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Nächste Schritte

- [Grundlegendes zum Azure Active Directory-Anwendungsmanifest](./reference-app-manifest.md)
