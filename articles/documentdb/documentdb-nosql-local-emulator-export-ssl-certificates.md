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
ms.date: 11/29/2016
ms.author: tvoellm
translationtype: Human Translation
ms.sourcegitcommit: c47ff7045a7c69f2f4a0235fa591a6a8cc820192
ms.openlocfilehash: dc3d297f44fb99b6fad58810eb31f429fdb1bd25

---

# <a name="export-the-documentdb-emulator-certificates"></a>Exporter les certificats de l’émulateur DocumentDB

[**Téléchargement de l’émulateur**](https://aka.ms/documentdb-emulator)

L’ émulateur Azure DocumentDB fournit un environnement local qui émule le service Azure DocumentDB à des fins de développement, notamment pour l’utilisation des connexions SSL. Cet article vous explique comment exporter les certificats SSL pour une utilisation dans les langages et les runtimes qui ne s’intègrent pas avec le magasin de certificats Windows, comme Java, qui utilise son propre [magasin de certificats](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), et Python, qui utilise des [wrappers de socket](https://docs.python.org/2/library/ssl.html). Pour en savoir plus sur l’émulateur, consultez l’article [Use the Azure DocumentDB Emulator for development and testing](./documentdb-nosql-local-emulator.md) (Utilisation de l’émulateur Azure pour le développement et le test).

# <a name="certification-rotation"></a>Rotation de certification

Les certificats de l’émulateur local DocumentDB sont générés lors de la première exécution de l’émulateur. Il existe deux certificats. L’un des deux est utilisé pour connecter l’émulateur local, l’autre pour gérer les clés secrètes au sein de l’émulateur. Le certificat à exporter est le certificat de connexion présentant le nom convivial DocumentDBEmulatorCertificate.

Pour régénérer les certificats, cliquez sur Réinitialiser les données dans l’émulateur local DocumentDB exécuté dans la barre d’état Windows, tel que représenté ci-dessous. Si vous régénérez les certificats et les avez installés dans le magasin de certificats Java ou les utilisez ailleurs, vous devez les mettre à jour pour maintenir la connexion de votre application à l’émulateur local.

![Réinitialiser les données de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-reset-data.png)

# <a name="how-to-export-the-documentdb-ssl-certificate"></a>Comment exporter le certificat SSL DocumentDB

- Étape 1 : Démarrez le gestionnaire de certificats Windows en exécutant certlm.msc et en accédant au dossier Personnel > Certificats, puis en ouvrant le certificat portant le nom convivial DocumentDBEmulatorCertificate.

![Étape 1 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-1.png)

- Étape 2 : Cliquez sur Détails, puis sur OK.

![Étape 1 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-2.png)

- Étape 3 : Cliquez sur Copier dans un fichier

![Étape 1 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-3.png)

- Étape 4 : Cliquez sur Suivant

![Étape 1 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-4.png)

- Étape 5 : Cliquez sur l’option de refus d’exportation de la clé privée, puis sur Suivant.

![Étape 1 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-5.png)

- Étape 6 : Cliquez sur le fichier codé en base 64 X.509 (.CER), puis sur Suivant

![Étape 1 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-6.png)

- Étape 7 : Attribuez un nom au certificat Ici, cliquez sur documentdbemulatorcert, puis sur Suivant

![Étape 1 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-7.png)

- Étape 8 : Cliquez sur Terminer

![Étape 1 d’exportation de l’émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-8.png)

# <a name="how-to-use-the-certificate-in-java"></a>Comment utiliser le certificat en Java

Lors de l’exécution d’applications Java ou MongoDB qui utilisent le client Java, il est plus simple d’installer le certificat dans le magasin de certificat par défaut que de transmettre les indicateurs "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>". Par exemple, l’[application de démonstration Java](https://localhost:8081/_explorer/index.html) incluse dépend du magasin de certificats par défaut.

Suivez les instructions de la publication [Ajout d’un certificat au magasin de certificats d’autorité de certification Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store) pour importer le certificat X.509 dans le magasin de certificats Java par défaut. Gardez à l’esprit que vous travaillerez dans le répertoire %JAVA_HOME% durant l’exécution de keytool.

Une fois que le certificat SSL DocumentDBEmulatorCertificate est installé, votre application doit être en mesure de se connecter et d’utiliser l’émulateur local DocumentDB. Si les problèmes persistent, suivez les recommandations de l’article [Debugging SSL/TLS Connections](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) (Débogage des connexions SSL/TLS). Il est très probable que le certificat n’est pas installé dans le magasin %JAVA_HOME%/jre/lib/security/cacerts. Par exemple, si vous disposez de plusieurs versions de Java installées, votre application peut utiliser un magasin cacaerts différent de celui mis à jour.

# <a name="how-to-use-the-certificate-in-python"></a>Comment utiliser le certificat en python

Par défaut, le kit de développement logiciel (SDK) Python ne tentera pas d’utiliser le certificat SSL lors de la connexion à l’émulateur local. Si vous souhaitez utiliser la validation SSL, vous pouvez suivre les exemples de la documentation des [wrappers de socket Python](https://docs.python.org/2/library/ssl.html).



<!--HONumber=Nov16_HO5-->


