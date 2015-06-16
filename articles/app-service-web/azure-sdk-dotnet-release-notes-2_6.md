<properties 
   pageTitle="Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.6" 
   description="Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.6" 
   services="app-service/web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/29/2015"
   ms.author="juliako"/>


# Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.6

Ce document contient les notes de publication du Kit de développement logiciel (SDK) Azure pour .NET version 2.6.

Grâce au Kit de développement logiciel (SDK) Azure pour .NET 2.6, vous pouvez développer des applications de service cloud (PaaS) ciblant .NET 4.5.2 ou .NET 4.6 si vous installez manuellement le .NET Framework cible sur le rôle de service cloud. Consultez [Installer .NET sur un rôle de service cloud](http://go.microsoft.com/fwlink/?LinkID=309796).


##Mises à jour Service Bus

- Hubs d'événements : 

	- Permettent désormais un contrôle d'accès ciblé lors de l'envoi d'événements avec l'exposition d'un autre point de terminaison de serveur de publication pour les hubs d'événements.
	- Amélioration des fonctionnalités des hubs d'événements, notamment une meilleure stabilité.
	- Ajout de la prise en charge du protocole Amqp sur WebSocket pour la messagerie et les hubs d'événements.

##Mises à jour des outils HDInsight pour Visual Studio

- **Amélioration d'IntelliSense** : suggestion de métadonnées distantes

	Les outils HDInsight pour Visual Studio prennent en charge l'obtention de métadonnées distantes pendant la modification d'un script Hive. Par exemple, vous pouvez taper **SELECT * FROM** pour afficher tous les noms de tables. Si vous spécifiez une table, les noms des colonnes s'afficheront également.

- **Prise en charge de l'émulateur HDInsight**

	Les outils HDInsight pour Visual Studio prennent à présent en charge la connexion à l'émulateur HDInsight. Vous pouvez donc développer vos scripts Hive localement sans coûts supplémentaires, puis exécuter ces scripts sur vos clusters HDInsight.

	Pour plus d'informations, reportez-vous à [ce manuel](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

- **Prise en charge des clusters Hadoop génériques par les outils HDInsight pour Visual Studio** (version préliminaire)

	Les outils HDInsight pour Visual Studio prennent en charge les clusters Hadoop génériques. Les outils HDInsight pour Visual Studio vous permettront donc d'effectuer les opérations suivantes :

	- Connexion au cluster 
	- Écriture d'une requête Hive avec une prise en charge améliorée d'IntelliSense et de la saisie semi-automatique 
	- Affichage de tous les travaux de votre cluster avec une interface utilisateur intuitive 

	Pour plus d'informations, reportez-vous à [ce manuel](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

##Mises à jour d'In-Role Cache

- **In-Role Cache** a été mis à jour pour utiliser la version 4.3 du **Kit de développement logiciel (SDK) Microsoft Azure Storage**. Jusqu'à présent, **In-Role Cache** la version 1.7 du SDK.

	Les clients qui utilisent la version 2.5 du SDK Azure ou une version antérieure doivent effectuer une mise à jour vers la version 2.6 et passer à la nouvelle version du SDK Azure Storage. Notez que la version 2011-08-18 d'Azure Storage sera supprimée le 9 décembre 2015. Pour plus d'informations, lisez [cette annonce](http://azure.microsoft.com/blog/2014/08/04/microsoft-azure-storage-service-version-removal/).

	Pour plus d'informations, consultez [In-Role Cache pour Azure Cache](https://msdn.microsoft.com/library/azure/dn386103.aspx).

##Outils Azure App Service

Les éléments suivants ont été mis à jour avec la version 2.6 du Kit de développement logiciel (SDK) Azure.

- La publication Azure a été améliorée pour inclure Azure API Apps comme une cible de déploiement.
- La fonctionnalité d'approvisionnement d'API Apps permet désormais aux utilisateurs de créer et d'approvisionner des applications API.
- L'Explorateur de serveurs a été modifié pour refléter le nouveau nœud Service d'application. Les applications web, mobiles et API sont maintenant regroupées par groupe de ressources.
- La fonctionnalité Ajouter un client d'application API Azure a été ajoutée à la plupart des projets C# qui permettront la génération automatique des applications API avec prise en charge Swagger exécutées au sein d'un abonnement Azure.
- Les outils API Apps et les nœuds App Service de l'Explorateur de serveurs sont uniquement disponibles dans Visual Studio 2013. 

##Mises à jour des outils du gestionnaire des ressources Azure

Les outils du gestionnaire des ressources Azure ont été mis à jour et incluent désormais des modèles pour les machines virtuelles, le réseau et le stockage. L'expérience de modification du code JSON a été mise à jour et inclut désormais une nouvelle vue hiérarchique pour les modèles, ainsi que la possibilité de modifier les modèles à l'aide d'extraits de code JSON. Les modèles déployés à partir de Visual Studio utilisent un script PowerShell fourni avec le projet. Les modifications apportées au script seront donc utilisées par Visual Studio.

##Amélioration des diagnostics pour Cloud Services

Le Kit de développement logiciel (SDK) Azure version 2.6 prend en charge la collecte des journaux de diagnostic dans l'émulateur de calcul Azure, ainsi que le transfert de ces journaux vers le stockage de développement. Les journaux de diagnostic (y compris les journaux de suivi d'application, les journaux ETW, les compteurs de performances, les journaux d'infrastructure et les journaux d'événements Windows) générés pendant l'exécution de l'application dans l'émulateur peuvent être transférés vers le stockage de développement pour vérifier que la journalisation des diagnostics fonctionne sur votre ordinateur local.

Le compte de stockage Diagnostics peut désormais être spécifié dans le fichier de configuration de service (.cscfg), ce qui facilite l'utilisation de différents comptes de stockage de diagnostics dans différents environnements. Des différences notables existent entre le SDK Azure version 2.4 et version 2.6 au niveau du fonctionnement de la chaîne de connexion. Pour plus d'informations sur l'utilisation de la chaîne de connexion du compte de stockage Diagnostics et son impact sur les projets, consultez [Configuration des diagnostics pour Azure Cloud Services et Azure Virtual Machines](http://go.microsoft.com/fwlink/?LinkID=532784).

##Dernières modifications

###Outils du gestionnaire de ressources Azure 

- Le type de projet **Projets de déploiement cloud** disponible dans le SDK Azure version 2.5 s'appelle désormais **Groupe de ressources Azure**.
- Les projets de type **Projets de déploiement cloud** créés dans le SDK Azure version 2.5 peuvent être utilisés dans la version 2.6, mais le déploiement du modèle depuis Visual Studio échouera. Toutefois, le déploiement à l'aide du script PowerShell fonctionne toujours de la même façon. Pour plus d'informations sur l'utilisation des **projets de déploiement cloud** dans le SDK Azure version 2.6, lisez ce [blog](http://go.microsoft.com/fwlink/?LinkID=534086).
 
##Problèmes connus

La collecte des journaux de diagnostic dans l'émulateur nécessite un système d'exploitation 64 bits. Les journaux de diagnostic ne seront pas collectés sur les systèmes d'exploitation 32 bits. Cela n'affecte pas les autres fonctionnalités de l'émulateur.

<!--HONumber=52-->
 