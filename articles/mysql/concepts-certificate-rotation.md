---
title: Zertifikatrotation für Azure Database for MySQL
description: Hier erfahren Sie mehr über die bevorstehenden Änderungen an Stammzertifikaten im Zusammenhang mit Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 468244b786d418117f12b445d86b67580db913da
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639771"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql-single-server"></a>Grundlegendes zu den Änderungen im Zusammenhang mit der Stammzertifizierungsstelle für Azure Database for MySQL Single Server

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Die Änderung des Stammzertifikats am **15. Februar 2021 (15.02.2021)** wurde für Azure Database for MySQL Single Server im Rahmen der standardmäßigen Wartung und bewährten Sicherheitsmethoden erfolgreich abgeschlossen. In diesem Artikel erhalten Sie ausführlichere Informationen zu den Änderungen, den betroffenen Ressourcen sowie den Schritten, die erforderlich sind, um sicherzustellen, dass die Konnektivität Ihrer Anwendung mit dem Datenbankserver bestehen bleibt.

> [!NOTE]
> Dieser Artikel betrifft NUR [Azure Database for MySQL Single Server](single-server-overview.md). Bei [Azure Database for MySQL Flexible Server](flexible-server/overview.md) ist [DigiCert Global Root CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) das für die Kommunikation über SSL erforderliche Zertifikat.
>
> Dieser Artikel enthält Verweise auf den Begriff _Slave_, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.
>

#### <a name="why-is-a-root-certificate-update-required"></a>Warum ist eine Aktualisierung des Stammzertifikats erforderlich?

Benutzer von Azure Database for MySQL können eine Verbindung mit ihrem MySQL-Server nur mithilfe des vordefinierten Zertifikats herstellen, das sich [hier](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) befindet. Im [ZA-/Browserforum](https://cabforum.org/) wurden jedoch kürzlich Berichte veröffentlicht, in denen mehrere von Zertifizierungsstellenanbietern ausgestellte Zertifikate aufgeführt sind, die nicht konform sind.

Entsprechend den Konformitätsanforderungen der Branche haben Zertifizierungsstellenanbieter damit begonnen, Zertifizierungsstellenzertifikate für nicht konforme Zertifizierungsstellen zu widerrufen. Deshalb müssen Server Zertifikate verwenden, die von konformen Zertifizierungsstellen ausgestellt und durch Zertifizierungsstellenzertifikate von diesen Zertifizierungsstellen signiert wurden. Da Azure Database for MySQL eines dieser nicht konformen Zertifikate verwendete, musste das Zertifikat zur konformen Version rotiert werden, um die potenzielle Bedrohung für Ihre MySQL-Server zu minimieren.

Der Rollout des neuen Zertifikats hat am 15. Februar 2021 (15.02.2021) begonnen, und es ist ab diesem Datum gültig.

#### <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Welche Änderung wurde am 15. Februar 2021 (15.02.2021) durchgeführt?

Am 15. Februar 2021 wurde das [BaltimoreCyberTrustRoot-Stammzertifikat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) durch eine **konforme Version** desselben [BaltimoreCyberTrustRoot-Stammzertifikats](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) ersetzt, um sicherzustellen, dass Bestandskunden keinerlei Änderungen vornehmen müssen und es keine Auswirkungen auf deren Verbindungen mit dem Server gibt. Während dieser Änderung wurde das [BaltimoreCyberTrustRoot-Stammzertifikat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **nicht** durch [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ersetzt. Diese Änderung wird zurückgestellt, um den Kunden mehr Zeit für die Umstellung zu geben.

#### <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Muss ich Änderungen an meinem Client vornehmen, um die Konnektivität aufrechtzuerhalten?

Auf Clientseite ist keine Änderung erforderlich. Wenn Sie unsere frühere Empfehlung unten befolgt haben, können Sie weiterhin eine Verbindung herstellen, solange das **BaltimoreCyberTrustRoot-Zertifikat aus dem kombinierten Zertifizierungsstellenzertifikat nicht entfernt wird**. **Zur Aufrechterhaltung der Konnektivität empfehlen wir, dass Sie das BaltimoreCyberTrustRoot-Zertifikat bis auf Weiteres nicht aus Ihrem kombinierten Zertifizierungsstellenzertifikat entfernen.**

###### <a name="previous-recommendation"></a>Frühere Empfehlung

Wenn Sie verhindern möchten, dass die Verfügbarkeit Ihrer Anwendung infolge von unerwartet widerrufenen Zertifikaten unterbrochen wird, oder wenn Sie ein widerrufenes Zertifikat aktualisieren möchten, führen Sie die folgenden Schritte aus. Dadurch soll eine neue *PEM*-Datei erstellt werden, die das aktuelle Zertifikat und das neue Zertifikat kombiniert. Während der SSL-Zertifikatüberprüfung wird einer der zulässigen Werte verwendet. Sehen Sie sich die folgenden Schritte an:

1. Laden Sie die Stammzertifizierungsstellen BaltimoreCyberTrustRoot und DigiCertGlobalRootG2 über die folgenden Links herunter:

    * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
    * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

2. Generieren Sie einen kombinierten Zertifizierungsstellen-Zertifikatspeicher, in dem sowohl **BaltimoreCyberTrustRoot** als auch **DigiCertGlobalRootG2** enthalten sind.

    * Führen Sie für Java-Benutzer (MySQL-Connector/J) Folgendes aus:

      ```console
      keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
      ```

      ```console
      keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
      ```

      Ersetzen Sie dann die ursprüngliche Keystore-Datei durch die neu generierte Datei:

      * System.setProperty("javax.net.ssl.trustStore","Pfad_zur_Truststore-Datei");
      * System.setProperty("javax.net.ssl.trustStorePassword","Kennwort");

    * Stellen Sie bei .NET-Benutzern (MySQL-Connector/NET, MySQLConnector) sicher, dass **BaltimoreCyberTrustRoot** und **DigiCertGlobalRootG2** im Windows-Zertifikatspeicher unter vertrauenswürdigen Stammzertifizierungsstellen enthalten sind. Ist eines der Zertifikate nicht vorhanden, importieren Sie das fehlende Zertifikat.

      :::image type="content" source="media/overview/netconnecter-cert.png" alt-text="Azure Database for MySQL – Diagramm der Zertifikate für .NET-Benutzer":::

    * Stellen Sie bei .NET-Benutzern unter Linux, die SSL_CERT_DIR verwenden, sicher, dass **BaltimoreCyberTrustRoot** und **DigiCertGlobalRootG2** in dem durch SSL_CERT_DIR angegebenen Verzeichnis enthalten sind. Ist eines der Zertifikate nicht vorhanden, erstellen Sie die fehlende Zertifikatsdatei.

    * Bei anderen Benutzern (MySQL-Client/MySQL Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) können Sie zwei ZS-Zertifikatsdateien im folgenden Format zusammenführen:

      ```
      -----BEGIN CERTIFICATE-----
      (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      (Root CA2: DigiCertGlobalRootG2.crt.pem)
      -----END CERTIFICATE-----
      ```

3. Ersetzen Sie die ursprüngliche PEM-Datei der Stammzertifizierungsstelle durch die kombinierte Datei der Stammzertifizierungsstelle, und starten Sie die Anwendung bzw. den Client neu.

   Nachdem das neue Zertifikat auf der Serverseite bereitgestellt wurde, können Sie in Zukunft die PEM-Datei der Zertifizierungsstelle in „DigiCertGlobalRootG2.crt.pem“ ändern.

> [!NOTE]
> Bitte löschen oder ändern Sie das **Baltimore-Zertifikat** erst, nachdem die Zertifikatänderung vorgenommen wurde. Nach Abschluss der Änderung werden wir Ihnen eine Mitteilung senden. Dann können Sie das **Baltimore-Zertifikat** sicher löschen.

#### <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Warum wurde das BaltimoreCyberTrustRoo-Zertifikat während dieser Änderung am 15. Februar 2021 nicht durch DigiCertGlobalRootG2 ersetzt?

Wir haben die Bereitschaft der Kunden für diese Änderung ausgewertet und erkannt, dass viele Kunden noch zusätzliche Vorlaufzeit zur Abwicklung der Änderung benötigen. Um Kunden eine längere Vorlaufzeit zur Vorbereitung zu bieten, haben wir beschlossen, die Zertifikatänderung in „DigiCertGlobalRootG2“ um mindestens ein Jahr zu verschieben, damit Kunden und Endbenutzer genügend Vorlaufzeit haben.

Unsere Empfehlungen für Benutzer lauten: Führen Sie die oben genannten Schritte zum Erstellen eines kombinierten Zertifikats und Herstellen einer Verbindung mit Ihrem Server aus, aber entfernen Sie das BaltimoreCyberTrustRoot-Zertifikat erst, nachdem wir Ihnen eine entsprechende Mitteilung gesendet haben.

#### <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Was geschieht, wenn das BaltimoreCyberTrustRoot-Zertifikat entfernt wurde?

Während Sie eine Verbindung mit Ihrem Azure Database for MySQL-Server herstellen, treten Konnektivitätsfehler auf. Sie müssen SSL mit dem [BaltimoreCyberTrustRoot-Zertifikat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) erneut [konfigurieren](howto-configure-ssl.md), um die Konnektivität aufrechtzuerhalten.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

#### <a name="if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Muss ich die Stammzertifizierungsstelle auch aktualisieren, wenn ich SSL/TLS nicht verwende?

  Wenn Sie SSL/TLS nicht verwenden, sind keine Aktionen erforderlich.

#### <a name="if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>Muss ich bei Verwendung von SSL/TLS meinen Datenbankserver neu starten, um die Stammzertifizierungsstelle zu aktualisieren?

Nein, der Datenbankserver muss nicht neu gestartet werden, damit das neue Zertifikat verwendet werden kann. Dieses Stammzertifikat ist eine clientseitige Änderung, und die eingehenden Clientverbindungen müssen das neue Zertifikat verwenden, um sicherzustellen, dass sie eine Verbindung mit dem Datenbankserver herstellen können.

#### <a name="how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>Wie kann ich feststellen, ob ich SSL/TLS mit Stammzertifikatüberprüfung verwende?

Durch Überprüfen der Verbindungszeichenfolge können Sie ermitteln, ob Ihre Verbindungen das Stammzertifikat überprüfen.

* Wenn Ihre Verbindungszeichenfolge `sslmode=verify-ca` oder `sslmode=verify-identity` enthält, müssen Sie das Zertifikat aktualisieren.
* Wenn Ihre Verbindungszeichenfolge `sslmode=disable`, `sslmode=allow`, `sslmode=prefer` oder `sslmode=require` enthält, müssen Sie keine Zertifikate aktualisieren.
* Wenn in Ihrer Verbindungszeichenfolge „sslmode“ nicht angegeben ist, müssen Sie keine Zertifikat aktualisieren.

Wenn Sie einen Client verwenden, der die Verbindungszeichenfolge abstrahiert, lesen Sie die Dokumentation des Clients, um zu ermitteln, ob Zertifikate überprüft werden.

#### <a name="what-is-the-impact-of-using-app-service-with-azure-database-for-mysql"></a>Welche Auswirkungen hat es, wenn App Service bei Azure Database for MySQL verwendet wird?

Für Azure App Service-Instanzen, die eine Verbindung mit Azure Database for MySQL herstellen, gibt es zwei mögliche Szenarien – abhängig davon, wie Sie SSL bei Ihrer Anwendung verwenden.

* Dieses neue Zertifikat wurde App Service auf Plattformebene hinzugefügt. Wenn Sie die SSL-Zertifikate verwenden, die in der App Service-Plattform in Ihrer Anwendung enthalten sind, ist keine Aktion erforderlich. Dies ist das häufigste Szenario.
* Wenn Sie den Pfad zur SSL-Zertifikatsdatei explizit in den Code einschließen, müssen Sie das neue Zertifikat herunterladen und wie oben beschrieben ein kombiniertes Zertifikat erstellen und die Zertifikatsdatei verwenden. Ein gutes Beispiel für dieses Szenario ist die Verwendung von benutzerdefinierten Containern in App Service, wie es in der [App Service-Dokumentation](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress) beschrieben ist. Dies ist ein ungewöhnliches Szenario, doch haben wir festgestellt, dass es von einigen Benutzern verwendet wird.

#### <a name="what-is-the-impact-of-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>Welche Auswirkungen hat es, wenn Azure Kubernetes Service (AKS) bei Azure Database for MySQL verwendet wird?

Wenn Sie versuchen, mithilfe von Azure Kubernetes Service (AKS) eine Verbindung mit Azure Database for MySQL herzustellen, ähnelt dies dem Zugriff über eine dedizierte Kundenhostumgebung. Die entsprechenden Schritte finden Sie [hier](../aks/ingress-own-tls.md).

#### <a name="what-is-the-impact-of-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>Welche Auswirkungen hat es, wenn Azure Data Factory zum Herstellen einer Verbindung mit Azure Database for MySQL verwendet wird?

Ein Connector, der Azure Integration Runtime verwendet, nutzt Zertifikate im Windows-Zertifikatspeicher in der von Azure gehosteten Umgebung. Weil diese Zertifikate mit den neu angewendeten Zertifikaten bereits kompatibel sind, ist keine Aktion erforderlich.

Bei einem Connector, der die selbstgehostete Integration Runtime verwendet, für die Sie den Pfad zur SSL-Zertifikatsdatei explizit in die Verbindungszeichenfolge einschließen, müssen Sie das [neue Zertifikat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) herunterladen und die Verbindungszeichenfolge aktualisieren, damit es verwendet werden kann.

#### <a name="do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>Muss ich für diese Änderung eine Wartungsdowntime für den Datenbankserver planen?

Nein. Da die Änderung nur auf der Clientseite geschieht, um eine Verbindung mit dem Datenbankserver herzustellen, ist dafür keine Wartungsdowntime beim Datenbankserver erforderlich.

#### <a name="if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>Bin ich betroffen, wenn ich nach dem 15. Februar 2021 (15.02.2021) einen neuen Server erstelle?

Bei Servern, die nach dem 15. Februar 2021 (15.02.2021) erstellt werden, verwenden Sie weiterhin [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) für Ihre Anwendungen, um eine Verbindung mithilfe von SSL herzustellen.

#### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Wie oft aktualisiert Microsoft seine Zertifikate, bzw. welche Ablaufrichtlinie gilt?

Diese von Azure Database for MySQL verwendeten Zertifikate werden von vertrauenswürdigen Zertifizierungsstellen (ZS) bereitgestellt. Die Unterstützung dieser Zertifikate ist also an die Unterstützung dieser Zertifikate durch die ZS gebunden. Das [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)-Zertifikat läuft im Jahr 2025 ab, sodass Microsoft vor dem Ablauf eine Zertifikatänderung durchführen muss. Sollten in diesen vordefinierten Zertifikaten unvorhergesehene Fehler auftreten, muss Microsoft die Zertifikatrotation frühestens wie die Änderung am 15. Februar 2021 durchführen, um zu gewährleisten, dass der Dienst jederzeit sicher und konform ist.

#### <a name="if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>Muss ich bei Verwendung von Lesereplikaten dieses Update nur auf dem Quellserver oder für die gelesenen Replikate ausführen?

Da dieses Update eine clientseitige Änderung ist, müssen Sie die Änderungen auch für diese Clients anwenden, wenn der Client Daten vom Replikatserver gelesen hat.

#### <a name="if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>Muss ich bei Verwendung der Datenreplikation eine Aktion ausführen?

Wenn Sie die [Datenreplikation](concepts-data-in-replication.md) verwenden, um eine Verbindung mit Azure Database for MySQL herzustellen, müssen Sie zwei Punkte berücksichtigen:

* Wenn die Datenreplikation von einem virtuellen Computer (lokal oder Azure-VM) zu Azure Database for MySQL erfolgt, müssen Sie überprüfen, ob zum Erstellen des Replikats SSL verwendet wird. Führen Sie **SHOW SLAVE STATUS** aus, und überprüfen Sie die folgende Einstellung.  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    Wenn Sie sehen, dass das Zertifikat für „CA_file“, „SSL_Cert“ und „SSL_Key“ bereitgestellt wird, müssen Sie die Datei aktualisieren, indem Sie das [neue Zertifikat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) hinzufügen und eine kombinierte Zertifikatsdatei erstellen.

* Wenn die Datenreplikation zwischen zwei Azure Database for MySQL-Servern erfolgt, müssen Sie das Replikat zurücksetzen, indem Sie **CALL mysql.az_replication_change_master** ausführen und das neue duale Stammzertifikat als letzten Parameter ([master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)) bereitstellen.

#### <a name="is-there-a-server-side-query-to-determine-whether-ssl-is-being-used"></a>Gibt es eine serverseitige Abfrage zur Ermittlung, ob SSL verwendet wird?

Um zu überprüfen, ob Sie eine SSL-Verbindung zum Herstellen einer Verbindung mit dem Server verwenden, lesen Sie die Informationen unter [Überprüfen der SSL-Verbindung](howto-configure-ssl.md#step-4-verify-the-ssl-connection).

#### <a name="is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>Ist eine Aktion erforderlich, wenn sich DigiCertGlobalRootG2 bereits in meiner Zertifikatsdatei befindet?

Nein Wenn in Ihrer Zertifikatsdatei **DigiCertGlobalRootG2** bereits enthalten ist, ist keine Aktion erforderlich.

#### <a name="what-if-i-have-further-questions"></a>Wie gehe ich vor, wenn ich weitere Fragen habe?

Bei Fragen können Sie Antworten von Communityexperten unter [Microsoft Q&A](mailto:AzureDatabaseforMySQL@service.microsoft.com) erhalten. Wenn Sie über einen Supportplan verfügen und technische Hilfe benötigen, [kontaktieren Sie uns](mailto:AzureDatabaseforMySQL@service.microsoft.com).
