---
title: Registrieren Azure Active Directory für den DICOM-Dienst – Azure Healthcare-APIs
description: In diesem Artikel wird die Registrierung Azure Active Directory Anwendung für den DICOM-Dienst beschrieben.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 1db5ba11d4a3bd8380561ae493c69b809edf5135
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339661"
---
# <a name="register-azure-active-directory-applications-for-the-dicom-service"></a>Registrieren Azure Active Directory Anwendungen für den DICOM-Dienst

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Beim Einrichten des DICOM-Diensts oder des FHIR-Servers für Azure (OSS) stehen Ihnen mehrere Optionen zur Auswahl. Für Open Source müssen Sie Ihre eigene Ressourcenanwendungsregistrierung erstellen. Für Azure API for FHIR wird diese Ressourcenanwendung automatisch erstellt.

## <a name="application-registrations"></a>Anwendungsregistrierungen

Damit eine Anwendung mit Azure AD interagieren kann, muss sie registriert werden. Im Kontext des DICOM-Diensts müssen drei Arten von Clientanwendungsregistrierungen diskutiert werden.

## <a name="client-applications"></a>Clientanwendungen

Bei Clientanwendungen handelt es sich um Registrierungen der Clients, von denen Token angefordert werden. In OAuth 2.0 unterscheiden wir häufig zwischen mindestens drei verschiedenen Anwendungstypen.

### <a name="confidential-clients"></a>Vertrauliche Clients

Vertrauliche Clients werden auch als Web-Apps in der Azure AD. Vertrauliche Clients sind Anwendungen, von denen der [Autorisierungscodeflow](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) verwendet wird, um im Namen eines angemeldeten Benutzers mit gültigen Anmeldeinformationen ein Token zu beziehen. Sie werden als vertrauliche Clients bezeichnet, da sie ein Geheimnis enthalten können, und stellen dieses Geheimnis Azure AD, wenn der Authentifizierungscode gegen ein Token ausgetauscht wird. Da vertrauliche Clients sich mit dem geheimen Clientgeheimnis authentifizieren können, sind sie mehr vertrauenswürdig als öffentliche Clients, können über langlebige Token verfügen und erhalten ein Aktualisierungstoken. Weitere Informationen finden Sie unter [Registrieren einer vertraulichen Clientanwendung in Azure Active Directory](dicom-register-confidential-client-application.md). Es ist wichtig, die Antwort-URL zu registrieren, unter der der Client den Autorisierungscode empfängt.

### <a name="public-clients"></a>Öffentliche Clients

Diese Clients eignen sich nicht zum Speichern von Geheimnissen. In der Regel ist dies eine Mobile Device-Anwendung oder eine Single-Page-JavaScript-Anwendung, bei der ein Geheimnis im Client von einem Benutzer gefunden werden kann. Von öffentlichen Clients wird zwar ebenfalls der Autorisierungscodeflow verwendet, sie dürfen beim Tokenbezug jedoch kein Geheimnis angeben und verfügen ggf. über kurzlebigere Token und über kein Aktualisierungstoken. Lesen Sie die Details zum Registrieren [einer öffentlichen Clientanwendung in Azure Active Directory](dicom-register-public-application.md).

### <a name="service-clients"></a>Dienstclients

Diese Clients beziehen Token mithilfe des [Clientanmeldeinformations-Flows](.././../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) für sich selbst (nicht im Namen eines Benutzers). Sie stellen in der Regel Anwendungen dar, die nicht interaktiv auf den DICOM-Dienst zugreifen. Ein Beispiel wäre etwa ein Erfassungsprozess. Wenn Sie einen Dienstclient verwenden, ist es nicht erforderlich, den Prozess zum Abrufen eines Tokens mit einem Aufruf des Endpunkts /authorize zu starten. Ein Dienstclient kann direkt zum Endpunkt /token wechseln und die Client-ID und den geheimen Clientgeheimnis zum Abrufen eines Tokens präsentieren. Weitere Informationen finden Sie unter [Registrieren einer Dienstclientanwendung in Azure Active Directory](dicom-register-service-client-application.md).

## <a name="next-steps"></a>Nächste Schritte

Dieser Übersichtsartikel führte Sie durch den Anwendungsregistrierungsprozess für Ressourcen- und Clientanwendungen, um mit dem DICOM-Dienst zu arbeiten. Nachdem Sie Ihre Anwendungen registriert haben, können Sie den DICOM-Dienst bereitstellen.

>[!div class="nextstepaction"]
>[Registrieren einer vertraulichen Clientanwendung in Azure Active Directory](dicom-register-confidential-client-application.md)

>[!div class="nextstepaction"]
>[Registrieren einer öffentlichen Clientanwendung in Azure Active Directory](dicom-register-public-application.md)

>[!div class="nextstepaction"]
>[Registrieren einer Dienstclientanwendun in Azure Active Directory](dicom-register-service-client-application.md)