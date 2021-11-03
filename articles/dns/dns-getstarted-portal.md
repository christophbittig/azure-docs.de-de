---
title: 'Schnellstart: Erstellen einer DNS-Zone und eines Eintrags: Azure-Portal'
titleSuffix: Azure DNS
description: Verwenden Sie diese Schritt-für-Schritt-Schnellstartanleitung, um zu erfahren, wie Sie eine Azure DNS-Zone und den zugehörigen Eintrag mit dem Azure-Portal erstellen.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 04/23/2021
ms.topic: quickstart
ms.service: dns
ms.custom: mode-portal
ms.openlocfilehash: f29b63398cf01320c6d46085139183edcf0e5f4b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033074"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Schnellstart: Erstellen einer Azure DNS-Zone und eines DNS-Eintrags mit dem Azure-Portal

Sie können Azure DNS zum Auflösen der Hostnamen in Ihrer öffentlichen Domäne konfigurieren. Wenn Sie beispielsweise den Domänennamen *contoso.xyz* von einer Domänennamen-Registrierungsstelle erworben haben, können Sie Azure DNS zum Hosten der Domäne *contoso.xyz* und zum Auflösen von *`www.contoso.xyz`* in die IP-Adresse Ihres Webservers oder Ihrer Web-App konfigurieren.

In dieser Schnellstartanleitung erstellen Sie eine Testdomäne und anschließend einen Adresseintrag zum Auflösen von *www* in die IP-Adresse *10.10.10.10*.

:::image type="content" source="media/dns-getstarted-portal/environment-diagram.png" alt-text="Diagramm: DNS-Bereitstellungsumgebung mit dem Azure-Portal" border="false":::

>[!IMPORTANT]
>Alle Namen und IP-Adressen in dieser Schnellstartanleitung sind Beispiele und stellen keine echten Szenarien dar.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Melden Sie sich für alle Portalschritte am [Azure-Portal](https://portal.azure.com) an.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-dns-zone"></a>Erstellen einer DNS-Zone

Eine DNS-Zone enthält die DNS-Einträge für eine Domäne. Wenn Sie eine Domäne in Azure DNS hosten möchten, erstellen Sie eine DNS-Zone für diesen Domänennamen. 

**Erstellen Sie die DNS-Zone wie folgt:**

1. Wählen Sie oben links die Option **Ressource erstellen** und dann **Netzwerk** und **DNS-Zone**.

1. Geben Sie auf der Seite **DNS-Zone erstellen** die folgenden Werte ein (bzw. wählen Sie sie aus):

   - **Name**: Geben Sie für dieses Schnellstartbeispiel *contoso.xyz* ein. Der DNS-Zonenname kann ein beliebiger Wert sein, der auf den Azure DNS-Servern nicht bereits konfiguriert ist. Ein realer Wert ist eine Domäne, die Sie von einer Domänennamen-Registrierungsstelle erworben haben.
   - **Ressourcengruppe**: Wählen Sie **Neue erstellen** aus, geben Sie *MyResourceGroup* ein, und wählen Sie **OK** aus. Der Name der Ressourcengruppe muss innerhalb des Azure-Abonnements eindeutig sein. 

1. Klicken Sie auf **Erstellen**.

   ![DNS-Zone](./media/dns-getstarted-portal/openzone650.png)

Die Erstellung der Zone kann einige Minuten dauern.

## <a name="create-a-dns-record"></a>Erstellen eines DNS-Eintrags

Sie erstellen DNS-Einträge für Ihre Domäne in der DNS-Zone. Erstellen Sie einen neuen Adresseintrag (A-Eintrag), um einen Hostnamen in eine IPv4-Adresse aufzulösen.

**Erstellen Sie einen A-Eintrag wie folgt:**

1. Öffnen Sie im Azure-Portal unter **Alle Ressourcen** die DNS-Zone **contoso.xyz** in der Ressourcengruppe **MyResourceGroup**. Sie können *contoso.xyz* im Feld **Nach Name filtern** eingeben, damit Sie es leichter finden.

1. Wählen Sie oben auf der Seite **DNS-Zone** die Option **+ Datensatzgruppe**.

1. Geben Sie auf der Seite **Datensatzgruppe hinzufügen** die folgenden Werte ein (bzw. wählen Sie sie aus):

   - **Name**: Geben Sie *www* ein. Der Eintragsname ist der Hostname, den Sie in die angegebene IP-Adresse auflösen möchten.
   - **Typ:** Wählen Sie **A** aus. A-Einträge sind am gängigsten. Es gibt aber noch weitere Eintragstypen für E-Mail-Server („MX“), IPv6-Adressen („AAAA“) usw. 
   - **TTL**: Geben Sie *1* ein. Mit der *Gültigkeitsdauer* der DNS-Anforderung wird angegeben, wie lange DNS-Server und -Clients eine Antwort zwischenspeichern können.
   - **TTL-Einheit**: Wählen Sie **Stunden** aus. Dies ist die Zeiteinheit für den **TTL**-Wert. 
   - **IP-Adresse**: Geben Sie für dieses Schnellstartbeispiel *10.10.10.10* ein. Dieser Wert ist die IP-Adresse, in die der Eintragsname aufgelöst wird. In einem echten Szenario geben Sie die öffentliche IP-Adresse für Ihren Webserver ein.

Da diese Schnellstartanleitung nur für schnelle Tests bestimmt ist, ist es nicht erforderlich, die Azure DNS-Namensserver bei einer Domänennamen-Registrierungsstelle zu konfigurieren. Bei einer echten Produktionsdomäne möchten Sie erreichen, dass alle Internetbenutzer den Hostnamen auflösen können, um eine Verbindung mit Ihrem Webserver oder Ihrer App herzustellen. Sie besuchen Ihre Domänennamen-Registrierungsstelle, um die Namenservereinträge durch die Azure DNS-Namenserver zu ersetzen. Weitere Informationen finden Sie im [Tutorial: Hosten Ihrer Domäne in Azure DNS](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>Testen der Namensauflösung

Sie besitzen nun eine DNS-Testzone mit einem A-Testeintrag und können die Namensauflösung mit dem Tool *nslookup* testen. 

**Testen Sie die DNS-Namensauflösung wie folgt:**

1. Öffnen Sie im Azure-Portal unter **Alle Ressourcen** die DNS-Zone **contoso.xyz** in der Ressourcengruppe **MyResourceGroup**. Sie können *contoso.xyz* im Feld **Nach Name filtern** eingeben, damit Sie es leichter finden.

1. Kopieren Sie einen der Namenservernamen aus der Liste mit den Namenservern auf der Seite **Übersicht**. 

   ![Zone](./media/dns-getstarted-portal/viewzonens500.png)

1. Öffnen Sie eine Eingabeaufforderung, und führen Sie den folgenden Befehl aus:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Beispiel:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Es sollte ein Bildschirm angezeigt werden, der in etwa wie folgt aussieht:

   ![Screenshot, der ein Eingabeaufforderungsfenster mit einem Namenserver-Suchbefehl und Werten für Server, Adresse, Name und Adresse zeigt.](media/dns-getstarted-portal/nslookup.PNG)

Der Hostname **www\.contoso.xyz** wird gemäß Ihrer Konfiguration zu **10.10.10.10** aufgelöst. Mit diesem Ergebnis wird bestätigt, dass die Namensauflösung richtig funktioniert. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die mit dieser Schnellstartanleitung erstellten Ressourcen nicht mehr benötigen, können Sie sie entfernen, indem Sie die Ressourcengruppe **MyResourceGroup** löschen. Öffnen Sie die Ressourcengruppe **MyResourceGroup**, und wählen Sie **Ressourcengruppe löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen von DNS-Einträgen für eine Web-App in einer benutzerdefinierten Domäne](./dns-web-sites-custom-domain.md)
