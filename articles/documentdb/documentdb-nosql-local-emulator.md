---
title: "Développement local avec l’émulateur DocumentDB | Microsoft Docs"
description: "L’émulateur DocumentDB vous permet de développer et de tester votre application localement, sans créer d’abonnement Azure et sans frais."
services: documentdb
documentationcenter: 
keywords: "Émulateur DocumentDB"
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 0d0264857f552b65f5934eea686329e8d36a1204
ms.openlocfilehash: e86b1990ea38f9f8af4e5401fe37eec321075e64


---
# <a name="use-the-azure-documentdb-emulator-for-development-and-testing"></a>Utilisation de l'émulateur Azure DocumentDB pour le développement et le test

[**Téléchargement de l'émulateur**](https://aka.ms/documentdb-emulator)

L' émulateur Azure DocumentDB fournit un environnement local qui émule le service Azure DocumentDB à des fins de développement. L’émulateur DocumentDB vous permet de développer et tester votre application localement, sans créer d’abonnement Azure et sans frais. Lorsque vous êtes satisfait du fonctionnement de votre application dans l’émulateur DocumentDB, vous pouvez commencer à utiliser un compte Azure DocumentDB dans le cloud.

Nous vous recommandons de commencer par visionner la vidéo suivante, où Kirill Gavrylyuk montre comment prendre en main l’émulateur DocumentDB.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="documentdb-emulator-system-requirements"></a>Configuration système requise pour l'émulateur DocumentDB
L’émulateur DocumentDB nécessite la configuration matérielle et logicielle suivante :

* Configuration logicielle requise
  * Windows Server 2012 R2, Windows Server 2016 ou Windows 10
*   Conditions matérielles minimales requises
  * 2 Go de RAM
  * 10 Go d’espace disque disponible

## <a name="installing-the-documentdb-emulator"></a>Installation de l'émulateur DocumentDB
Vous pouvez télécharger et installer l’émulateur DocumentDB à partir du [Centre de téléchargement Microsoft](https://aka.ms/documentdb-emulator). 

> [!NOTE]
> Pour installer, configurer et exécuter l'émulateur DocumentDB, vous devez disposer de privilèges d'administrateur sur l'ordinateur.

## <a name="checking-for-documentdb-emulator-updates"></a>Vérification des mises à jour de l’émulateur DocumentDB
L’émulateur DocumentDB inclut un Explorateur de données Azure DocumentDB intégré vous permettant de parcourir les données stockées dans DocumentDB, de créer des collections et de savoir quand une nouvelle mise à jour est disponible au téléchargement. 

> [!NOTE]
> Il n'est pas garanti que vous puissiez accéder aux données créées dans une version de l'émulateur DocumentDB à partir d'une autre version. Si vous devez rendre vos données persistantes à long terme, nous vous recommandons de stocker ces données dans un compte DocumentDB plutôt que dans l'émulateur DocumentDB. 

## <a name="how-the-documentdb-emulator-works"></a>Fonctionnement de l’émulateur DocumentDB
L’émulateur DocumentDB fournit une émulation haute fidélité du service DocumentDB. Il prend en charge les mêmes fonctionnalités identiques qu’Azure DocumentDB, notamment la prise en charge de la création et de l’interrogation des documents JSON, la configuration et la mise à l’échelle des collections, et l’exécution des procédures stockées et des déclencheurs. Vous pouvez développer et tester des applications à l’aide de l’émulateur DocumentDB et les déployer vers Azure à l’échelle mondiale en apportant une seule modification à la configuration du point de terminaison de connexion pour DocumentDB.

Nous avons créé une émulation locale haute fidélité du service DocumentDB réel, mais l’implémentation de l’émulateur DocumentDB est différente de celle du service. Par exemple, l’émulateur DocumentDB utilise les composants du système d’exploitation standard, notamment le système de fichiers local pour la persistance, et la pile de protocole HTTPS pour la connectivité. Cela signifie que certaines fonctionnalités qui s’appuient sur l’infrastructure Azure, comme la réplication globale, la latence en quelques millisecondes pour les lectures/écritures et les niveaux de cohérence ajustables ne sont pas disponibles via l’émulateur DocumentDB.


## <a name="authenticating-requests-against-the-documentdb-emulator"></a>Authentification des demandes auprès de l’émulateur DocumentDB
Tout comme avec Azure Document dans le cloud, chaque demande que vous effectuez auprès de l’émulateur DocumentDB doit être authentifiée. L’émulateur DocumentDB prend en charge uniquement un compte fixe et une clé d’authentification connue pour l’authentification de la clé principale. Ce compte et cette clé sont les seules informations d'identification autorisées pour une utilisation avec l'émulateur DocumentDB. Il s'agit de :

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> La clé principale prise en charge par l’émulateur DocumentDB est destinée à être utilisée uniquement avec l’émulateur. Vous ne pouvez pas utiliser votre compte DocumentDB de production avec l'émulateur DocumentDB. 

En outre, comme pour le service Azure DocumentDB, l’émulateur DocumentDB prend en charge uniquement une communication sécurisée via SSL.

## <a name="start-and-initialize-the-documentdb-emulator"></a>Démarrer et initialiser l’émulateur DocumentDB

Pour démarrer l’émulateur Azure DocumentDB, sélectionnez le bouton Démarrer ou appuyez sur la touche Windows. Commencez à taper **Émulateur DocumentDB**, puis sélectionnez l’émulateur dans la liste des applications. 

![Sélectionnez le bouton Démarrer ou appuyez sur la touche Windows, commencez à taper **Émulateur DocumentDB**, puis sélectionnez l’émulateur dans la liste des applications](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-start.png)

Lorsque l’émulateur est en cours d’exécution, une icône est affichée dans la zone de notification de la barre des tâches Windows. L’émulateur DocumentDB s’exécute par défaut sur l’ordinateur local (« localhost ») et écoute sur le port 8081.

![Notification dans la barre des tâches de l'émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-taskbar.png)

L’émulateur DocumentDB est installé par défaut dans le répertoire `C:\Program Files\DocumentDB Emulator`. Vous pouvez également démarrer et arrêter l’émulateur à partir de la ligne de commande. Pour plus d’informations, consultez la section [Référence de l’outil en ligne de commande](#command-line).

## <a name="start-the-local-emulator-data-explorer"></a>Démarrer l’émulateur local de l’Explorateur de données

Au démarrage de l’émulateur local, l’Explorateur de données DocumentDB s'ouvre automatiquement dans votre navigateur. L’adresse apparaît sous la forme [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Si vous fermez l’explorateur et que vous souhaitez le réouvrir ultérieurement, vous pouvez ouvrir l’URL dans votre navigateur ou la lancer à partir de l’émulateur DocumentDB dans l’icône de la barre d’état système Windows, comme indiqué ci-dessous.

![Lanceur de l'Explorateur de données de l'émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-data-explorer-launcher.png)

## <a name="developing-with-the-documentdb-emulator"></a>Développement avec l’émulateur DocumentDB
Une fois que l'émulateur DocumentDB est exécuté sur votre bureau, vous pouvez utiliser n'importe quel [SDK DocumentDB](documentdb-sdk-dotnet.md) pris en charge ou l'[API REST DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) pour interagir avec lui. L’émulateur DocumentDB inclut également un Explorateur de données intégré qui vous permet de créer des collections, d'afficher et de modifier des documents sans avoir à écrire de code. 

    // Connect to the DocumentDB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==",
        new ConnectionPolicy { EnableEndpointDiscovery = false });

> [!NOTE]
> Lors de la connexion à l’émulateur, vous devez définir EnableEndpointDiscovery = false dans la configuration de la connexion.

Si vous utilisez la [prise en charge du protocole DocumentDB pour MongoDB](documentdb-protocol-mongodb.md), utilisez la chaîne de connexion suivante :

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10250/admin?ssl=true&3t.sslSelfSignedCerts=true

Vous pouvez utiliser les outils existants comme [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) pour vous connecter à l’émulateur DocumentDB. Vous pouvez également migrer des données entre l’émulateur DocumentDB et le service Azure DocumentDB à l’aide de l'[outil de migration de données DocumentDB](https://github.com/azure/azure-documentdb-datamigrationtool).

## <a name="export-the-documentdb-emulator-ssl-certificate"></a>Exporter le certificat SSL de l’émulateur DocumentDB

Les langages .Net et le runtime utilisent le magasin de certificats Windows pour se connecter en toute sécurité à l’émulateur local DocumentDB. D’autres langages ont leur propre méthode de gestion et de l’utilisation des certificats. Java utilise son propre [magasin de certificats](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), tandis que Python utilise des [wrappers de socket](https://docs.python.org/2/library/ssl.html).

Pour obtenir un certificat à utiliser avec les langages et les runtimes qui ne s’intègrent pas au magasin de certificats Windows, vous devrez l'exporter à l’aide du Gestionnaire de certificats Windows. Vous pouvez le démarrer en exécutant le fichier certlm.msc ou suivre les instructions étape par étape de la publication « [ Exporter les certificats de l’émulateur DocumentDB](./documentdb-nosql-local-emulator-export-ssl-certificates.md) ». Une fois le certificat en cours d’exécution, ouvrez les certificats personnels comme indiqué ci-dessous et exportez le certificat avec le nom convivial "DocumentDBEmulatorCertificate" en tant que fichier X.509 (.cer) codé en Base 64.

![Certificat SSL de l'émulateur local DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-ssl_certificate.png)

Le certificat X.509 peut être importé dans le magasin de certificats Java en suivant les instructions de la publication « [Ajout d'un certificat au magasin de certificats d'autorité de certification Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store) ».  Une fois le certificat importé dans les magasin de certificats Java, les applications MongoDB pourront se connecter à l’émulateur local DocumentDB.

## <a name="a-idcommand-lineadocumentdb-emulator-command-line-tool-reference"></a><a id="command-line"></a>Référence de l’outil en ligne de commande de l’émulateur DocumentDB
À partir de l’emplacement d’installation, vous pouvez utiliser la ligne de commande pour démarrer et arrêter l’émulateur, configurer les options et effectuer d’autres opérations.

### <a name="command-line-syntax"></a>Syntaxe de ligne de commande

    DocumentDB.LocalEmulator.exe [/shutdown] [/datapath] [/port] [/mongoport] [/directports] [/key] [/?]

Pour afficher la liste des options, tapez `DocumentDB.LocalEmulator.exe /?` dans l’invite de commandes.

<table>
<tr>
  <td><strong>Option</strong></td>
  <td><strong>Description</strong></td>
  <td><strong>Commande</strong></td>
  <td><strong>Arguments</strong></td>
</tr>
<tr>
  <td>[aucun argument]</td>
  <td>Démarre l’émulateur DocumentDB avec des paramètres par défaut</td>
  <td>DocumentDB.LocalEmulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>Arrête l’émulateur DocumentDB</td>
  <td>DocumentDB.LocalEmulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>Aide</td>
  <td>Affiche la liste des arguments de ligne de commande</td>
  <td>DocumentDB.LocalEmulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Datapath</td>
  <td>Spécifie le chemin d’accès dans lequel stocker les fichiers de données</td>
  <td>DocumentDB.LocalEmulator.exe /datapath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt; : un chemin accessible</td>
</tr>
<tr>
  <td>Port</td>
  <td>Spécifie le numéro de port à utiliser pour l'émulateur.  La valeur par défaut est 8081</td>
  <td>DocumentDB.LocalEmulator.exe /port=&lt;port&gt;</td>
  <td>&lt;port&gt; : numéro de port unique</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Spécifie le numéro de port à utiliser pour l'API de compatibilité MongoDB. La valeur par défaut est 10250</td>
  <td>DocumentDB.LocalEmulator.exe /mongoport=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt; : numéro de port unique</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Spécifie les ports à utiliser pour une connectivité directe. Les valeurs par défaut sont 10251,10252,10253,10254</td>
  <td>DocumentDB.LocalEmulator.exe /directports:&lt;directports&gt;</td>
  <td>&lt;directports&gt; : liste séparée par des virgules de 4 ports</td>
</tr>
<tr>
  <td>Clé</td>
  <td>Clé d’autorisation pour l’émulateur. La clé doit être le codage en base 64 d’un vecteur de 64 octets</td>
  <td>DocumentDB.LocalEmulator.exe /key:&lt;key&gt;</td>
  <td>&lt;clé&gt; : la clé doit être le codage en base 64 d’un vecteur de 64 octets</td>
</tr>
<tr>
  <td>EnableThrottling</td>
  <td>Spécifie que le comportement de limitation des demandes est activé</td>
  <td>DocumentDB.LocalEmulator.exe /enablethrottling</td>
  <td></td>
</tr>
<tr>
  <td>DisableThrottling</td>
  <td>Spécifie que le comportement de limitation des demandes est désactivé</td>
  <td>DocumentDB.LocalEmulator.exe /disablethrottling</td>
  <td></td>
</tr>
</table>

## <a name="differences-between-the-documentdb-emulator-and-azure-documentdb"></a>Différences entre l’émulateur DocumentDB et Azure DocumentDB 
L’émulateur DocumentDB étant un environnement émulé exécuté sur une station de travail pour développeur locale, il existe des différences de fonctionnalités entre l’émulateur et un compte Azure DocumentDB dans le cloud :

* L'émulateur DocumentDB prend en charge uniquement un compte fixe et une clé principale connue.  La régénération de clé n’est pas possible dans l’émulateur DocumentDB.
* L’émulateur DocumentDB n’est pas un service de stockage évolutif et ne prend pas en charge un grand nombre de collections.
* L’émulateur DocumentDB ne simule pas différents [niveaux de cohérence DocumentDB](documentdb-consistency-levels.md).
* L’émulateur DocumentDB ne simule pas la [réplication dans plusieurs régions](documentdb-distribute-data-globally.md).
* L’émulateur DocumentDB ne prend pas en charge les substitutions de quota de service disponibles dans le service Azure DocumentDB (par exemple, les limites de taille de document ou l'augmentation du stockage d'une collection partitionnée).
* Votre copie de l’émulateur DocumentDB n'a peut-être pas été mise à jour avec les dernières modifications apportées au service Azure DocumentDB, veuillez consulter la section [Planificateur de capacité DocumentDB](https://www.documentdb.com/capacityplanner) pour évaluer avec précision les besoins de débit de production (RU) de votre application.

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur DocumentDB, consultez la rubrique [Présentation d’Azure DocumentDB](documentdb-introduction.md)
* Pour vous initier au développement de l’émulateur DocumentDB, téléchargez un des [Kits de développement logiciel (SDK) DocumentDB pris en charge](documentdb-sdk-dotnet.md).



<!--HONumber=Nov16_HO5-->


