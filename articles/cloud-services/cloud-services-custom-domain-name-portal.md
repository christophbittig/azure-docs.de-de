---
title: Konfigurieren eines benutzerdefinierten Domänennamens in Cloud Services (klassisch) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Ihre Azure-Anwendung oder -Daten durch das Konfigurieren von DNS-Einstellungen auf einer benutzerdefinierten Domäne im Internet verfügbar machen.  In diesen Beispielen wird das Azure-Portal verwendet.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 174b92e88a9b0da17d1fe152648e107d78d70c8f
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824002"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service-classic"></a>Konfigurieren eines benutzerdefinierten Domänennamens für einen Azure-Clouddienst (klassisch)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Wenn Sie einen Clouddienst erstellen, weist Azure diesen einer Unterdomäne von **cloudapp.net** zu. Wenn der Clouddienst beispielsweise den Namen „contoso“ hat, können Ihre Benutzer auf die Anwendung über eine URL wie `http://contoso.cloudapp.net` zugreifen. Darüber hinaus weist Azure eine virtuelle IP-Adresse zu.

Sie können Ihre Anwendung jedoch auch unter Ihrem eigenen Domänennamen zur Verfügung stellen, beispielsweise **contoso.com**. In diesem Artikel erfahren Sie, wie Sie einen benutzerdefinierten Domänennamen für Clouddienst-Webrollen reservieren oder konfigurieren.

Wissen Sie bereits, worum es sich bei CNAME-Einträgen und A-Datensätzen handelt? [Überspringen Sie die Erläuterung](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Die Verfahren in dieser Aufgabe gelten für Azure Cloud Services. Bei App Services lesen Sie die Informationen unter [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure-Web-Apps](../app-service/app-service-web-tutorial-custom-domain.md). Informationen zu Speicherkonten finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für Ihren Azure Blob Storage-Endpunkt](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Schneller ans Ziel kommen – mit der NEUEN [Komplettanleitung für Azure](https://support.microsoft.com/kb/2990804).  Mit dieser Anleitung wird das Zuordnen eines benutzerdefinierten Domänennamens UND das Absichern der Kommunikation (TLS) mit Azure-Clouddiensten oder Azure-Websites zum Kinderspiel.
> 
> 

## <a name="understand-cname-and-a-records"></a>Informationen zu CNAME- und A-Datensätzen
Mit CNAME- (oder Aliasdatensätze) und A-Datensätzen können Sie einen Domänennamen einem bestimmten Server (oder in diesem Fall Service) zuweisen, beide funktionieren jedoch unterschiedlich. Bei der Verwendung von A-Datensätzen mit Azure-Clouddiensten gibt es auch einige Besonderheiten, die Sie bei der Auswahl der Art des Datensatzes berücksichtigen sollten.

### <a name="cname-or-alias-record"></a>CNAME- oder Alias-Eintrag
Ein CNAME-Eintrag ordnet einem kanonischen Domänennamen eine *spezifische* Domäne wie beispielsweise **contoso.com** oder **www\..contoso.com** zu. In diesem Fall ist der kanonische Domänenname der Domänenname **[myapp].cloudapp.net** Ihrer von Azure gehosteten Anwendung. Nach der Erstellung erstellt der CNAME-Eintrag einen Alias für **[myapp].cloudapp.net**. Der CNAME-Eintrag wird automatisch zur IP-Adresse Ihres Diensts **[myapp].cloudapp.net** aufgelöst. Wenn sich also die IP-Adresse des Clouddiensts ändert, müssen Sie keine Maßnahmen ergreifen.

> [!NOTE]
> Bei einigen Domänenregistrierungen können Sie Unterdomänen nur mit einem CNAME-Eintrag wie „www\.contoso.com“ zuweisen und nicht mit einem Stammnamen wie „contoso.com“. Weitere Informationen zu CNAME-Datensätzen finden Sie in der durch Ihre Registrierung zur Verfügung gestellten Dokumentation, [dem Wikipedia-Eintrag "CNAME Resource Record"](https://en.wikipedia.org/wiki/CNAME_record) oder dem Dokument [IETF Domain Names - Implementation and Specification](https://tools.ietf.org/html/rfc1035) (IEFT-Domänennamen – Implementierung und Spezifizierung, in englischer Sprache).

### <a name="a-record"></a>A-Eintrag
Ein *A*-Eintrag weist einer IP-Adresse eine Domäne wie **contoso.com** oder **www\..contoso.com**, *oder eine Domäne mit Platzhalter* wie **\*.contoso.com** zu. Im Falle eines Azure Cloud Service ist dies die virtuelle IP des Service. Der Vorteil eines A-Eintrags gegenüber einem CNAME-Eintrag ist, dass Sie einen Eintrag mit einem Platzhalter verwenden können, beispielsweise \**_.contoso.com_*, der Anfragen für mehrere Unterdomänen verarbeiten kann, beispielsweise **mail.contoso.com**, **login.contoso.com** oder **www\..contoso.com**.

> [!NOTE]
> Da ein A-Datensatz einer statischen IP-Adresse zugeordnet ist, kann er Änderungen an der IP-Adresse des Clouddiensts nicht automatisch auflösen. Die von Ihrem Clouddienst verwendete IP-Adresse wird zum ersten Mal zugewiesen, wenn Sie sie an einem leeren Steckplatz bereitstellen (entweder Produktion oder Staging). Wenn Sie die Bereitstellung für den Steckplatz löschen, wird die IP-Adresse von Azure freigegeben und zukünftigen Bereitstellungen an dem Steckplatz wird eine neue IP-Adresse zugewiesen.
> 
> Die IP-Adresse eines bestimmten Bereitstellungs-Steckplatzes (Produktion oder Staging) wird beim Austausch von Staging- und Produktionsbereitstellung oder beim Durchführen eines direkten Upgrades einer vorhandenen Bereitstellung beibehalten. Weitere Informationen zum Durchführen dieser Aktionen finden Sie unter [Verwalten von Cloud Services](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Hinzufügen eines CNAME-Datensatzes zu Ihrer benutzerdefinierten Domäne
Sie müssen einen neuen Eintrag zu der DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen, um einen CNAME-Datensatz zu erstellen. Verwenden Sie hierzu die durch Ihre Registrierung bereitgestellten Tools. Die Methoden zum Festlegen eines CNAME-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden Sie sich auch in einigen Punkten. Die Konzepte sind allerdings gleich.

1. Verwenden Sie eine dieser Methoden, um den **.cloudapp.net** -Domänennamen zu ermitteln, der Ihrem Clouddienst zugewiesen ist.

   * Melden Sie sich beim [Azure portal] an, und wählen Sie Ihren Clouddienst aus. Suchen Sie im Abschnitt **Übersicht** nach dem Eintrag **Website-URL**.

       ![Die Schnelleinsicht zeigt die Site-URL.][csurl]

       **OR**
   * Installieren und konfigurieren Sie [Azure PowerShell](/powershell/azure/), und verwenden Sie dann den folgenden Befehl:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Speichern Sie die im Domänennamen verwendete URL, die von der jeweiligen Methode zurückgegeben wurde. Diesen Namen benötigen Sie zum Erstellen eines CNAME-Datensatzes.
2. Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung. Suchen Sie nach Links oder Bereichen der Website, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden.
3. Navigieren Sie nun zu dem Bereich, in dem Sie CNAMEs auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen. Suchen Sie nach den Wörtern **CNAME**, **Alias** oder **Unterdomänen**.
4. Sie müssen außerdem den Domänen- oder Unterdomänenalias für den CNAME angeben, beispielsweise **www**, wenn Sie einen Alias für **www\..customdomain.com** erstellen möchten. Wenn Sie einen Alias für die Stammdomäne erstellen möchten, wird dieser möglicherweise als " **\@** "-Zeichen in den DNS-Tools Ihrer Registrierung aufgeführt.
5. Dann müssen Sie einen kanonischen Hostnamen angeben. In diesem Fall ist das die Domäne **cloudapp.net** Ihrer Anwendung.

Der folgende CNAME-Eintrag leitet zum Beispiel den gesamten Verkehr von **www\..contoso.com** an **contoso.cloudapp.net** weiter. Das ist der benutzerdefinierte Domänenname Ihrer bereitgestellten Anwendung:

| Alias/Hostname/Unterdomäne | Kanonische Domäne |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Einem Besucher von **www\..contoso.com** wird niemals der wirkliche Host („contoso.cloudapp.net“) angezeigt. Die Weiterleitung ist also für den Endbenutzer nicht sichtbar.
> 
> Das oben genannte Beispiel gilt nur für Verkehr an der Unterdomäne **www**. Da Sie keine Platzhalter mit CNAME-Datensätzen verwenden können, müssen Sie einen CNAME für jede Domäne/Unterdomäne erstellen. Wenn Sie den Verkehr von Unterdomänen wie *.contoso.com an Ihre cloudapp.net-Adresse weiterleiten möchten, können Sie in Ihren DNS-Einstellungen einen Eintrag für **URL-Umleitung** oder **URL-Weiterleitung** erstellen. Alternativ können Sie einen A-Datensatz erstellen.

## <a name="add-an-a-record-for-your-custom-domain"></a>Hinzufügen eines A-Datensatzes für Ihre benutzerdefinierte Domäne
Sie müssen zunächst die virtuelle IP-Adresse Ihres Clouddiensts ermitteln, um einen A-Datensatz zu erstellen. Fügen Sie dann einen neuen Eintrag zu der DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzu. Verwenden Sie hierzu die durch Ihre Registrierung bereitgestellten Tools. Die Methoden zum Festlegen eines A-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden Sie sich auch in einigen Punkten. Die Konzepte sind allerdings gleich.

1. Verwenden Sie eine der folgenden Methoden, um die IP-Adresse Ihres Clouddiensts zu ermitteln.

   * Melden Sie sich beim [Azure portal] an, und wählen Sie Ihren Clouddienst aus. Suchen Sie im Abschnitt **Übersicht** nach dem Eintrag **Öffentliche IP-Adressen**.

       ![Die Schnelleinsicht zeigt die VIP.][vip]

       **OR**
   * Installieren und konfigurieren Sie [Azure PowerShell](/powershell/azure/), und verwenden Sie dann den folgenden Befehl:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Speichern Sie die IP-Adresse, da Sie sie zum Erstellen eines A-Datensatzes benötigen.
2. Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung. Suchen Sie nach Links oder Bereichen der Website, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden.
3. Navigieren Sie nun zu dem Bereich, in dem Sie A-Datensätze auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen.
4. Wählen Sie die Domäne oder Unterdomäne aus, die diesen A-Datensatz verwenden wird, oder geben Sie diese ein. Wählen Sie beispielsweise **www**, wenn Sie einen Alias für **www\..customdomain.com** erstellen möchten. Wenn Sie einen Platzhaltereintrag für alle Unterdomänen erstellen möchten, geben Sie „*****“ ein. Dieser Eintrag deckt alle Unterdomänen wie **mail.customdomain.com**, **login.customdomain.com** und **www\..customdomain.com** ab.

    Wenn Sie einen A-Datensatz für die Stammdomäne erstellen möchten, wird dieser möglicherweise als " **\@** "-Zeichen in den DNS-Tools Ihrer Registrierung aufgeführt.
5. Geben Sie die IP-Adresse Ihres Clouddiensts in das angegebene Feld ein. So wird der im A-Datensatz verwendete Domäneneintrag der IP-Adresse Ihrer Clouddienstbereitstellung zugewiesen.

Der folgende A-Datensatz leitet zum Beispiel den gesamten Verkehr von **www\.contoso.com** an **137.135.70.239** weiter. Das ist die IP-Adresse Ihrer bereitgestellten Anwendung:

| Hostname/Unterdomäne | IP-Adresse |
| --- | --- |
| \@ |137.135.70.239 |

Dieses Beispiel zeigt das Erstellen eines A-Datensatzes für die Stammdomäne. Wenn Sie einen Platzhaltereintrag erstellen möchten, der alle Unterdomänen abdeckt, würden Sie „*****“ als Unterdomäne eingeben.

> [!WARNING]
> IP-Adressen in Azure sind standardmäßig dynamische IP-Adressen. Es empfiehlt sich, eine [reservierte IP-Adresse](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) zu verwenden, um sicherzustellen, dass sich Ihre IP-Adresse nicht ändert.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
* [Verwalten von Clouddiensten](cloud-services-how-to-manage-portal.md)
* [Zuordnen von CDN-Inhalt (Content Delivery Network) zu einer benutzerdefinierten Domäne](../cdn/cdn-map-content-to-custom-domain.md)
* [Allgemeine Konfiguration Ihres Clouddiensts](cloud-services-how-to-configure-portal.md)
* Weitere Informationen zum [Bereitstellen eines Clouddiensts](cloud-services-how-to-create-deploy-portal.md)
* Konfigurieren von [TLS/SSL-Zertifikaten](cloud-services-configure-ssl-certificate-portal.md)

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png