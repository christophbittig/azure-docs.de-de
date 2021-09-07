---
title: Verwenden von VNet-Dienstendpunkten mit dem Speech-Dienst
titleSuffix: Azure Cognitive Services
description: In diesem Artikel wird beschrieben, wie Sie den Speech-Dienst mit einem Azure-VNet-Dienstendpunkt verwenden.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 903ce0888aa04e7005468ca4f8aec9cf977ead5d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114490214"
---
# <a name="use-speech-service-through-a-virtual-network-service-endpoint"></a>Verwenden des Speech-Diensts über einen VNET-Dienstendpunkt

[Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)-[Dienstendpunkte](../../virtual-network/virtual-network-service-endpoints-overview.md) (VNet) bieten über eine optimierte Route im Azure-Backbonenetzwerk eine sichere und direkte Verbindung mit Azure-Diensten. Endpunkte ermöglichen es Ihnen, Ihre kritischen Azure-Dienstressourcen auf Ihre virtuellen Netzwerke zu beschränken und so zu schützen. Dienstendpunkte aktivieren private IP-Adressen im virtuellen Netzwerk, um den Endpunkt eines Azure-Diensts zu erreichen, ohne dass eine öffentliche IP-Adresse im virtuellen Netzwerk benötigt wird.

In diesem Artikel wird erläutert, wie Sie VNet-Dienstendpunkte mit dem Speech-Dienst in Azure Cognitive Services einrichten und verwenden.

> [!NOTE]
> Bevor Sie fortfahren, informieren Sie sich über die [Verwendung von virtuellen Netzwerken mit Cognitive Services](../cognitive-services-virtual-networks.md).

In diesem Artikel wird außerdem erläutert, wie Sie [VNet-Dienstendpunkte später entfernen, aber weiterhin die Speech-Ressource verwenden](#use-a-speech-resource-that-has-a-custom-domain-name-but-that-doesnt-have-allowed-virtual-networks).

Sie müssen folgende Aufgaben ausführen, um eine Speech-Ressource für Szenarien mit VNet-Dienstendpunkten einzurichten:
1. [Erstellen Sie einen benutzerdefinierten Domänennamen für die Speech-Ressource.](#create-a-custom-domain-name)
1. [Konfigurieren Sie virtuelle Netzwerke und Netzwerkeinstellungen für die Speech-Ressource.](#configure-virtual-networks-and-the-speech-resource-networking-settings)
1. [Passen Sie vorhandene Anwendungen und Lösungen an.](#adjust-existing-applications-and-solutions)

> [!NOTE]
> Das Einrichten und Verwenden von VNet-Dienstendpunkten für den Speech-Dienst ähnelt dem Einrichten und Verwenden privater Endpunkte. Im vorliegenden Dokument beziehen wir uns auf die entsprechenden Abschnitte des [Artikels zur Verwendung privater Endpunkte](speech-services-private-link.md), wenn die Verfahren identisch sind.

[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints.md)]

In diesem Artikel wird beschrieben, wie Sie den Speech-Dienst mit VNet-Dienstendpunkten verwenden. Informationen zu privaten Endpunkten finden Sie unter [Verwenden des Speech-Diensts über einen privaten Endpunkt](speech-services-private-link.md).

## <a name="create-a-custom-domain-name"></a>Erstellen eines benutzerdefinierten Domänennamens

Für VNet-Dienstendpunkte wird ein [benutzerdefinierter Unterdomänenname für Cognitive Services](../cognitive-services-custom-subdomains.md) benötigt. Erstellen Sie eine benutzerdefinierte Domäne, indem Sie den [Anweisungen](speech-services-private-link.md#create-a-custom-domain-name) im Artikel zu privaten Endpunkten folgen. Alle Warnungen im Abschnitt gelten auch für VNet-Dienstendpunkte.

## <a name="configure-virtual-networks-and-the-speech-resource-networking-settings"></a>Konfigurieren virtueller Netzwerke und der Netzwerkeinstellungen für die Speech-Ressource

Sie müssen den Netzwerkeigenschaften der Speech-Ressource alle virtuellen Netzwerke hinzufügen, denen der Zugriff über den Dienstendpunkt gewährt wird.

> [!NOTE]
> Um über den VNet-Dienstendpunkt auf eine Speech-Ressource zuzugreifen, müssen Sie den Dienstendpunkttyp `Microsoft.CognitiveServices` für die erforderlichen Subnetze Ihres virtuellen Netzwerks aktivieren. Dadurch wird der gesamte Subnetzdatenverkehr im Zusammenhang mit Cognitive Services über das private Backbonenetzwerk geroutet. Wenn Sie aus demselben Subnetz auf andere Cognitive Services-Ressourcen zugreifen möchten, stellen Sie sicher, dass diese Ressourcen so konfiguriert sind, dass Sie Ihr virtuelles Netzwerk zulassen. 
>
> Wenn ein virtuelles Netzwerk in den Netzwerkeigenschaften der Speech-Ressource nicht als *zulässig* hinzugefügt wird, kann es nicht über den Dienstendpunkt auf die Speech-Ressource zugreifen – selbst dann nicht, wenn der Dienstendpunkt `Microsoft.CognitiveServices` für das virtuelle Netzwerk aktiviert ist. Wenn der Dienstendpunkt aktiviert wurde, aber das virtuelle Netzwerk nicht zulässig ist, ist die Speech-Ressource für das virtuelle Netzwerk nicht über eine öffentliche IP-Adresse zugänglich. Dies gilt unabhängig davon, welche weiteren Netzwerksicherheitseinstellungen für die Speech-Ressource gelten. Dies liegt daran, dass nach dem Aktivieren des Endpunkts `Microsoft.CognitiveServices` der gesamte Datenverkehr im Zusammenhang mit Cognitive Services über das private Backbonenetzwerk geleitet wird, und in diesem Fall sollte dem virtuellen Netzwerk explizit Zugriff auf die Ressource gestattet werden. Dies gilt für alle Cognitive Services-Ressourcen, nicht nur für Speech-Ressourcen.  
  
1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/), und melden Sie sich bei Ihrem Azure-Konto an.
1. Wählen Sie die Speech-Ressource aus.
1. Wählen Sie in der Gruppe **Ressourcenverwaltung** im linken Bereich die Option **Netzwerk** aus.
1. Wählen Sie auf der Registerkarte **Firewalls und virtuelle Netzwerke** die Option **Ausgewählte Netzwerke und private Endpunkte** aus. 

   > [!NOTE]
   > Um VNet-Dienstendpunkte zu verwenden, müssen Sie die Netzwerksicherheitsoption **Ausgewählte Netzwerke und private Endpunkte** auswählen. Es werden keine weiteren Optionen unterstützt. Wenn für Ihr Szenario die Option **Alle Netzwerke** benötigt wird, sollten Sie die Verwendung [privater Endpunkte](speech-services-private-link.md) in Betracht ziehen, die alle drei Netzwerksicherheitsoptionen unterstützen.

5. Wählen Sie **Vorhandenes virtuelles Netzwerk hinzufügen** oder **Neues virtuelles Netzwerk hinzufügen** aus, und geben Sie die erforderlichen Parameter an. Wählen Sie für ein vorhandenes virtuelles Netzwerk **Hinzufügen** oder für ein neues Netzwerk **Erstellen** aus. Wenn Sie ein vorhandenes virtuelles Netzwerk hinzufügen, wird der Dienstendpunkt `Microsoft.CognitiveServices` automatisch für die ausgewählten Subnetze aktiviert. Dieser Vorgang kann bis zu 15 Minuten in Anspruch nehmen. Beachten Sie auch den Hinweis am Anfang dieses Abschnitts.

### <a name="enabling-service-endpoint-for-an-existing-virtual-network"></a>Aktivieren eines Dienstendpunkts für ein vorhandenes virtuelles Netzwerk 

Wie im vorherigen Abschnitt beschrieben, wird der Dienstendpunkt *automatisch aktiviert, wenn Sie ein virtuelles Netzwerk als* zulässig`Microsoft.CognitiveServices` für die Speech-Ressource konfigurieren. Wenn Sie es später deaktivieren, müssen Sie das virtuelle Netzwerk manuell erneut aktivieren, um den Dienstendpunktzugriff auf die Speech-Ressource (und andere Cognitive Services-Ressourcen) wiederherzustellen:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/), und melden Sie sich bei Ihrem Azure-Konto an.
1. Wählen Sie das virtuelle Netzwerk aus.
1. Wählen Sie im linken Bereich in der Gruppe **Einstellungen** die Option **Subnetze** aus.
1. Wählen Sie das erforderliche Subnetz aus.
1. Auf der rechten Fensterseite wird ein neuer Bereich geöffnet. Wählen Sie in diesem Bereich im Abschnitt **Dienstendpunkte** in der Liste `Microsoft.CognitiveServices`Dienste **den Eintrag** aus.
1. Wählen Sie **Speichern** aus.

## <a name="adjust-existing-applications-and-solutions"></a>Anpassen vorhandener Anwendungen und Lösungen

Eine Speech-Ressource mit aktivierter benutzerdefinierter Domäne interagiert in anderer Weise mit dem Speech-Dienst. Dies gilt unabhängig davon, ob für die Speech-Ressource mit aktivierter benutzerdefinierter Domäne Dienstendpunkte konfiguriert sind. Die Informationen in diesem Abschnitt beziehen sich auf beide Szenarios.

### <a name="use-a-speech-resource-that-has-a-custom-domain-name-and-allowed-virtual-networks"></a>Verwenden einer Speech-Ressource mit einem benutzerdefinierten Domänennamen und zulässigen virtuellen Netzwerken 

In diesem Szenario ist die Option **Ausgewählte Netzwerke und private Endpunkte** in den Netzwerkeinstellungen der Speech-Ressource ausgewählt, und mindestens ein virtuelles Netzwerk ist zulässig. Dieses Szenario entspricht dem [Verwenden von Speech-Ressourcen mit benutzerdefiniertem Domänennamen und privatem Endpunkt](speech-services-private-link.md#adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint).


### <a name="use-a-speech-resource-that-has-a-custom-domain-name-but-that-doesnt-have-allowed-virtual-networks"></a>Verwenden einer Speech-Ressource mit einem benutzerdefinierten Domänennamen und ohne zulässige virtuelle Netzwerke

In diesem Szenario sind keine privaten Endpunkte aktiviert, und eine der folgenden Aussagen trifft zu:

- In den Netzwerkeinstellungen der Speech-Ressource ist die Option **Ausgewählte Netzwerke und private Endpunkte** ausgewählt, aber es sind keine zulässigen virtuellen Netzwerke konfiguriert.
- In den Netzwerkeinstellungen der Speech-Ressource ist die Option **Alle Netzwerke** ausgewählt.

Dieses Szenario entspricht dem [Verwenden von Speech-Ressourcen mit benutzerdefiniertem Domänennamen und ohne private Endpunkte](speech-services-private-link.md#adjust-an-application-to-use-a-speech-resource-without-private-endpoints).


[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints-simultaneously.md)]


## <a name="learn-more"></a>Erfahren Sie mehr

* [Verwenden des Speech-Diensts mithilfe eines privaten Endpunkts](speech-services-private-link.md)
* [Azure-VNet-Dienstendpunkte](../../virtual-network/virtual-network-service-endpoints-overview.md)
* [Azure Private Link](../../private-link/private-link-overview.md)
* [Speech SDK](speech-sdk.md)
* [Spracherkennungs-REST-API](rest-speech-to-text.md)
* [Text-to-Speech-REST-API](rest-text-to-speech.md)
