---
title: Konfigurieren von HTTPS für Ihre benutzerdefinierte Domäne in einer Azure Front Door Standard/Premium-SKU-Konfiguration
description: In diesem Artikel erfahren Sie, wie Sie das Onboarding für eine benutzerdefinierte Domäne in eine Azure Front Door Standard/Premium-SKU durchführen.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/26/2021
ms.author: amsriva
ms.openlocfilehash: 79674febe7f43f8ae57d8c0420d6c8500506506e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965673"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Konfigurieren von HTTPS für eine benutzerdefinierte Front Door Standard/Premium-SKU (Vorschau) über das Azure-Portal

> [!NOTE]
> Diese Dokumentation ist für Azure Front Door Standard/Premium (Vorschau) gedacht. Suchen Sie nach Informationen zu Azure Front Door? Informationen finden Sie [hier](../front-door-overview.md).

Azure Front Door Standard/Premium ermöglicht standardmäßig eine sichere TLS-Zustellung für Ihre Anwendungen, wenn eine benutzerdefinierte Domäne hinzugefügt wird. Durch die Verwendung des HTTPS-Protokolls für Ihre benutzerdefinierte Domäne stellen Sie sicher, dass Ihre vertraulichen Daten beim Senden über das Internet sicher mit TLS/SSL-Verschlüsselung übertragen werden. Wenn Ihr Webbrowser über HTTPS eine Verbindung mit einer Website herstellt, überprüft er das Sicherheitszertifikat der Website, um sicherzustellen, dass es von einer legitimen Zertifizierungsstelle stammt. Dieser Prozess sorgt für Sicherheit und schützt Ihre Webanwendungen vor Angriffen.

Azure Front Door Standard/Premium unterstützt sowohl von Azure verwaltete Zertifikate als auch kundenseitig verwaltete Zertifikate. Azure Front Door aktiviert standardmäßig automatisch HTTPS für alle Ihre benutzerdefinierten Domänen mithilfe von verwalteten Azure-Zertifikaten. Für das Abrufen eines verwalteten Azure-Zertifikats sind keine zusätzlichen Schritte erforderlich. Ein Zertifikat wird während der Domänenüberprüfung erstellt. Sie können auch Ihr eigenes Zertifikat verwenden, indem Sie Azure Front Door Standard/Premium in Ihren Key Vault integrieren.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Vorschau) befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie in den [**zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

* Bevor Sie HTTPS für Ihre benutzerdefinierte Domäne konfigurieren können, müssen Sie zunächst ein Azure Front Door Standard/Premium-Profil erstellen. Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Azure Front Door Standard/Premium-Profils](create-front-door-portal.md).

* Wenn Sie noch nicht über eine benutzerdefinierte Domäne verfügen, müssen Sie zunächst eine bei einem Domänenanbieter erwerben. Informationen hierzu finden Sie beispielsweise unter [Kaufen eines benutzerdefinierten Domänennamens für Azure-Web-Apps](../../app-service/manage-custom-dns-buy-domain.md).

* Wenn Sie Azure zum Hosten Ihrer [DNS-Domänen](../../dns/dns-overview.md) verwenden, müssen Sie das Domain Name System (DNS) des Domänenanbieters an eine Azure DNS-Instanz delegieren. Weitere Informationen finden Sie unter [Delegieren einer Domäne an Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). Wenn Sie einen Anbieter für die Verwaltung Ihrer DNS-Domäne verwenden, müssen Sie die Domäne andernfalls manuell überprüfen, indem Sie die angeforderten DNS-TXT-Einträge eingeben.

## <a name="azure-managed-certificates"></a>Verwaltete Azure-Zertifikate

1. Wählen Sie unter Einstellungen für Ihr Azure Front Door Standard/Premium-Profil die Option **Domänen** und dann **+ Hinzufügen** aus, um eine neue Domäne hinzuzufügen.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="Screenshot der Landing Page der Domänenkonfiguration":::

1. Wählen Sie auf der Seite **Domäne hinzufügen** für die *DNS-Verwaltung* die Option **Mit Azure verwaltetes DNS** aus. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Screenshot: Hinzufügen einer Domänenseite mit aktivierter Option „Mit Azure verwaltetes DNS“":::

1. Überprüfen Sie die benutzerdefinierte Domäne, und ordnen Sie sie einem Endpunkt zu, indem Sie die Schritte unter „Aktivieren der [benutzerdefinierten Domäne](how-to-add-custom-domain.md)“ befolgen.

1. Nachdem die benutzerdefinierte Domäne erfolgreich dem Endpunkt zugeordnet wurde, wird ein verwaltetes Azure-Zertifikat für Front Door bereitgestellt. Dieser Vorgang kann von einigen Minuten bis zu einer Stunde dauern.

## <a name="using-your-own-certificate"></a>Verwenden eines eigenen Zertifikats

Sie können auch Ihr eigenes TLS-Zertifikat verwenden.  Wenn Sie Ihr TLS/SSL-Zertifikat erstellen, müssen Sie eine vollständige Zertifikatkette mit einer zulässigen Zertifizierungsstelle erstellen, die in der [Microsoft-Liste der vertrauenswürdigen Zertifizierungsstellen](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT) enthalten ist. Bei Verwendung einer unzulässigen Zertifizierungsstelle wird Ihre Anforderung abgelehnt.  Das Zertifikat muss über eine vollständige Zertifikatkette mit Blatt- und Zwischenzertifikaten verfügen, und die Stammzertifizierungsstelle muss in der [Microsoft-Liste der vertrauenswürdigen Zertifizierungsstellen](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT) enthalten sein. Wenn ein Zertifikat ohne vollständige Kette präsentiert wird, funktionieren die in diesem Zertifikat enthaltenen Anforderungen nicht wie erwartet. Dieses Zertifikat muss in einen Azure Key Vault importiert werden, bevor Sie es mit Azure Front Door Standard/Premium verwenden können. Weitere Informationen finden Sie unter [Importieren eines Zertifikats](../../key-vault/certificates/tutorial-import-certificate.md) in Azure Key Vault.

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Vorbereiten Ihres Azure Key Vault-Kontos und Ihres Zertifikats
 
1. Sie benötigen ein aktives Azure Key Vault-Konto. Dieses muss zum gleichen Abonnement gehören wie die Azure Front Door Standard/Premium-Instanz, für die Sie benutzerdefiniertes HTTPS aktivieren möchten. Erstellen Sie bei Bedarf ein Azure Key Vault-Konto.

    > [!WARNING]
    > Azure Front Door unterstützt derzeit nur Key Vault-Konten im selben Abonnement wie die Front Door-Konfiguration. Das Auswählen einer Key Vault-Instanz in einem anderen Abonnement als Ihrer Azure Front Door Standard/Premium-Instanz führt zu einem Fehler.

1. Falls Sie bereits über ein Zertifikat verfügen, können Sie es direkt in Ihr Azure Key Vault-Konto hochladen. Andernfalls können Sie ein neues Zertifikat direkt über Azure Key Vault von einer der Partnerzertifizierungsstellen erstellen, in die Azure Key Vault integriert wird. Laden Sie Ihr Zertifikat nicht als **Geheimnis**, sondern als Objekt vom Typ **Zertifikat** hoch.

    > [!NOTE]
    > Bei eigenen TLS-/SSL-Zertifikaten werden von Front Door keine Zertifikate mit EC-Kryptographiealgorithmen unterstützt. Das Zertifikat muss über eine vollständige Zertifikatkette mit Blatt- und Zwischenzertifikaten verfügen, und die Stammzertifizierungsstelle muss in der [Microsoft-Liste der vertrauenswürdigen Zertifizierungsstellen](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT) enthalten sein. 

#### <a name="register-azure-front-door"></a>Registrieren von Azure Front Door

Registrieren Sie den Dienstprinzipal für Azure Front Door als App in Azure Active Directory mithilfe von PowerShell.

> [!NOTE]
> Diese Aktion erfordert globale Administratorberechtigungen und muss lediglich **einmal** pro Mandant ausgeführt werden.

1. Installieren Sie bei Bedarf auf dem lokalen Computer [Azure PowerShell](/powershell/azure/install-az-ps) in PowerShell.

1. Führen Sie in PowerShell den folgenden Befehl aus:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Gewähren von Zugriff auf Ihren Schlüsseltresor für Azure Front Door
 
Gewähren Sie Azure Front Door Berechtigungen für den Zugriff auf die Zertifikate in Ihrem Azure Key Vault-Konto.

1. Wählen Sie in Ihrem Key Vault-Konto unter „Einstellungen“ die Option **Zugriffsrichtlinien** aus. Wählen Sie dann **Neue hinzufügen** aus, um eine neue Richtlinie zu erstellen.

1. Suchen Sie unter **Prinzipal auswählen** nach **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**, und wählen Sie **Microsoft.AzureFrontDoor-Cdn** aus. Klicken Sie auf **Auswählen**.

1. Wählen Sie unter **Berechtigungen für Geheimnis** die Option **Abrufen** aus, um Front Door das Abrufen des Zertifikats zu ermöglichen.

1. Wählen Sie unter **Zertifikatberechtigungen** die Option **Abrufen** aus, um Front Door das Abrufen des Zertifikats zu ermöglichen.

1. Klicken Sie auf **OK**. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Auswählen des bereitzustellenden Zertifikats für Azure Front Door
 
1. Kehren Sie im Portal zu Ihrer Azure Front Door Standard/Premium-Instanz zurück.

1. Navigieren Sie unter *Einstellungen* zu **Geheimnisse**, und wählen Sie **Zertifikat hinzufügen** aus.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Screenshot der Landing Page für das Azure Front Door-Geheimnis":::

1. Aktivieren Sie auf der Seite **Zertifikat hinzufügen** das Kontrollkästchen für das Zertifikat, das Sie zu Azure Front Door Standard/Premium hinzufügen möchten. Belassen Sie als Versionsauswahl die Option „Neueste“, und wählen Sie **Hinzufügen** aus. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="Screenshot der Seite „Zertifikat hinzufügen“":::

1. Sobald das Zertifikat erfolgreich bereitgestellt wurde, können Sie es verwenden, wenn Sie eine neue benutzerdefinierte Domäne hinzufügen.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="Screenshot des erfolgreich zu den Geheimnissen hinzugefügten Zertifikats":::

1. Navigieren Sie unter *Einstellung* zu **Domänen**, und wählen Sie **+ Hinzufügen** aus, um eine neue benutzerdefinierte Domäne hinzuzufügen. Wählen Sie auf der Seite **Domäne hinzufügen** die Option „Bring Your Own Certificate (BYOC)“ für *HTTPS* aus. Wählen Sie für *Geheimnis* das zu verwendende Zertifikat aus der Dropdownliste aus. 

    > [!NOTE]
    > Das ausgewählte Zertifikat muss einen allgemeinen Namen (Common Name, CN) aufweisen, der der hinzugefügten benutzerdefinierten Domäne entspricht.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="Screenshot der Seite zum Hinzufügen einer benutzerdefinierten Domäne mit HTTPS":::

1. Befolgen Sie die Schritte auf dem Bildschirm, um das Zertifikat zu überprüfen. Ordnen Sie dann die neu erstellte benutzerdefinierte Domäne einem Endpunkt zu, wie im Leitfaden zum [Erstellen einer benutzerdefinierten Domäne](how-to-add-custom-domain.md) beschrieben.

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Wechsel von „Mit Azure verwaltet“ zu Bring Your Own Certificate (BYOC)

1. Sie können ein vorhandenes verwaltetes Azure-Zertifikat in ein benutzerseitig verwaltetes Zertifikat ändern, indem Sie den Zertifikatstatus auswählen, um die Seite mit den **Zertifikatdetails** zu öffnen.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="Screenshot des Zertifikatstatus auf der Landing Page für Domänen" lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. Auf der Seite **Zertifikatdetails** können Sie von „Mit Azure verwaltet“ zur Option „Bring Your Own Certificate (BYOC)“ wechseln. Führen Sie dann dieselben Schritte wie zuvor aus, um ein Zertifikat auszuwählen. Wählen Sie **Aktualisieren** aus, um das zugeordnete Zertifikat mit einer Domäne zu ändern.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="Screenshot der Seite mit den Zertifikatdetails":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum [Zwischenspeichern mit Azure Front Door Standard/Premium](concept-caching.md).
