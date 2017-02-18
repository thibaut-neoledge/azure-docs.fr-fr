---
title: "Exporter les certificats de l’émulateur DocumentDB | Microsoft Docs"
description: "Lors du développement des langages et des runtimes qui n’utilisent pas le magasin de certificats Windows, vous devrez exporter et gérer les certificats SSL. Cet article vous fournit des instructions pas à pas."
services: documentdb
documentationcenter: 
keywords: "Émulateur DocumentDB"
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2017
ms.author: tvoellm
translationtype: Human Translation
ms.sourcegitcommit: 224897c09073fe36c7d4f9f8c965212c25297271
ms.openlocfilehash: 3f89e192052bb633190610a4dffbefb930dbc01c


---

# <a name="export-the-documentdb-emulator-certificates"></a>Exporter les certificats de l’émulateur DocumentDB

[**Téléchargement de l’émulateur**](https://aka.ms/documentdb-emulator)

L’ émulateur Azure DocumentDB fournit un environnement local qui émule le service Azure DocumentDB à des fins de développement, notamment pour l’utilisation des connexions SSL. Cet article vous explique comment exporter les certificats SSL pour une utilisation dans les langages et les runtimes qui ne s’intègrent pas avec le magasin de certificats Windows, comme Java, qui utilise son propre [magasin de certificats](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Python, qui utilise des [wrappers de socket](https://docs.python.org/2/library/ssl.html) et Node.js qui utilise [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Pour en savoir plus sur l’émulateur, consultez l’article [Utilisation de l'émulateur Azure DocumentDB pour le développement et le test](./documentdb-nosql-local-emulator.md).

## <a name="certification-rotation"></a>Rotation de certification

Les certificats de l’émulateur local DocumentDB sont générés lors de la première exécution de l’émulateur. Il existe deux certificats. L’un des deux est utilisé pour connecter l’émulateur local, l’autre pour gérer les clés secrètes au sein de l’émulateur. Le certificat à exporter est le certificat de connexion présentant le nom convivial DocumentDBEmulatorCertificate.

Pour régénérer les certificats, cliquez sur **Réinitialiser les données** dans l’émulateur DocumentDB exécuté dans la barre Windows, tel que représenté ci-dessous. Si vous régénérez les certificats et les avez installés dans le magasin de certificats Java ou les utilisez ailleurs, vous devez les mettre à jour pour maintenir la connexion de votre application à l’émulateur local.

![Réinitialiser les données de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-reset-data.png)

## <a name="how-to-export-the-documentdb-ssl-certificate"></a>Comment exporter le certificat SSL DocumentDB

1. Démarrez le gestionnaire de certificats Windows en exécutant certlm.msc et en accédant au dossier Personnel > Certificats, puis en ouvrant le certificat portant le nom convivial **DocumentDBEmulatorCertificate**.

    ![Étape 1 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-1.png)

2. Cliquez sur **Détails**, puis sur **OK**.

    ![Étape 2 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-2.png)

3. Cliquez sur **Copier dans un fichier...**.

    ![Étape 3 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-3.png)

4. Cliquez sur **Next**.

    ![Étape 4 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-4.png)

5. Cliquez sur l’option de **refus d’exportation de la clé privée**, puis sur **Suivant**.

    ![Étape 5 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-5.png)

6. Cliquez sur **X.509 encodé en base&64; (.cer)**, puis sur **Suivant**.

    ![Étape 6 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-6.png)

7. Attribuez un nom au certificat. Ici, cliquez sur **documentdbemulatorcert**, puis sur **Suivant**.

    ![Étape 7 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-7.png)

8. Cliquez sur **Terminer**.

    ![Étape 8 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Comment utiliser le certificat en Java

Lors de l’exécution d’applications Java ou MongoDB qui utilisent le client Java, il est plus simple d’installer le certificat dans le magasin de certificat par défaut que de transmettre les indicateurs "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>". Par exemple, l’[application de démonstration Java](https://localhost:8081/_explorer/index.html) incluse dépend du magasin de certificats par défaut.

Suivez les instructions figurant dans [Ajout d'un certificat au magasin de certificats d'autorité de certification Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store) pour importer le certificat X.509 dans le magasin de certificats Java par défaut. Gardez à l’esprit que vous travaillerez dans le répertoire %JAVA_HOME% durant l’exécution de keytool.

Une fois que le certificat SSL DocumentDBEmulatorCertificate est installé, votre application doit être en mesure de se connecter et d’utiliser l’émulateur local DocumentDB. Si les problèmes persistent, suivez les recommandations de l’article [Debugging SSL/TLS Connections](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) (Débogage des connexions SSL/TLS). Il est très probable que le certificat n’est pas installé dans le magasin %JAVA_HOME%/jre/lib/security/cacerts. Par exemple, si vous disposez de plusieurs versions de Java installées, votre application peut utiliser un magasin cacaerts différent de celui mis à jour.

## <a name="how-to-use-the-certificate-in-python"></a>Comment utiliser le certificat en python

Par défaut, le [Kit de développement logiciel (SDK) Python (version 2.0.0 ou supérieure)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-python) ne tentera pas d’utiliser le certificat SSL lors de la connexion à l’émulateur local. Si vous souhaitez utiliser la validation SSL, vous pouvez suivre les exemples de la documentation des [wrappers de socket Python](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Comment utiliser le certificat en Node.js

Par défaut, le [Kit de développement logiciel (SDK) Node.js (version 1.10.1 ou supérieure)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-node) ne tentera pas d’utiliser le certificat SSL lors de la connexion à l’émulateur local. Si vous souhaitez utiliser la validation SSL, vous pouvez suivre les exemples de la [documentation Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur DocumentDB, consultez la rubrique [Présentation d’Azure DocumentDB](documentdb-introduction.md)
* Pour vous initier au développement de l’émulateur DocumentDB, téléchargez un des [Kits de développement logiciel (SDK) DocumentDB pris en charge](documentdb-sdk-dotnet.md).



<!--HONumber=Jan17_HO4-->


