
<properties 
   pageTitle="Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.7" 
   description="Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.7" 
   services="app-service\web" 
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
   ms.date="08/09/2015"
   ms.author="juliako"/>


# Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.7

Ce document contient les notes de publication du Kit de développement logiciel (SDK) Azure pour .NET version 2.7.

Le SDK Azure 2.7 est uniquement pris en charge dans Visual Studio 2015 et Visual Studio 2013. Le [SDK Azure 2.6](http://azure.microsoft.com/downloads/) est le dernier Kit de développement logiciel pris en charge pour Visual Studio 2012.

Pour plus d’informations sur cette version, voir le [billet d’annonce du Kit de développement logiciel (SDK) Azure 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/).

##Améliorations en matière de connexion pour Visual Studio 2015

Le Kit de développement logiciel Azure 2.7 pour Visual Studio 2015 prend en charge les nouvelles fonctionnalités de gestion des identités de Visual Studio 2015. Cela inclut la prise en charge des comptes qui accèdent à Azure par le biais du contrôle d’accès en fonction du rôle (RBAC), de fournisseurs de solutions Cloud, de DreamSpark, ainsi que d’autres types de compte et d’abonnement.

Ces améliorations de connexion sont uniquement disponibles dans Visual Studio 2015. Leur prise en charge par Visual Studio 2013 sera incluse dans une mise à jour ultérieure.


##Kit de développement logiciel (SDK) Mobile

Les modèles **Mobile Apps** ont été mis à jour afin de refléter le dernier [package NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) et processus d’installation.

##Service Bus 

Résolutions de bogue et améliorations générales. Pour plus d’informations sur les mises à jour et sur les fonctionnalités, voir les notes de publication du dernier [package NuGet Service Bus](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

##Outils HDInsight 

Cette version intègre les mises à jour ci-dessous. Ces mises à jour sont disponibles en version préliminaire. Pour plus d’informations, voir [ce blog](http://go.microsoft.com/fwlink/?LinkId=619108).

- Graphiques Hive pour les travaux Hive sur Tez
- Prise en charge complète de la fonctionnalité IntelliSense pour les instructions DML Hive
- Prise en charge des modèles Pig
- Modèles Storm pour les services Azure

###Dernières modifications

- En cas d’utilisation de cette version des outils, l’ancien projet **Storm** doit être mis à niveau. Pour plus d’informations, voir [ce blog](http://go.microsoft.com/fwlink/?LinkId=619108).
- Visual Studio Web Express n’est plus pris en charge. Pour plus d’informations, voir [ce blog](http://go.microsoft.com/fwlink/?LinkId=619108).

##Outils Azure App Service

Dans cette version, les mises à jour ci-après ont été apportées à Web Tools Extensions. Pour plus d’informations, voir [ce blog](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/).

- Nouvelle prise en charge des comptes DreamSpark
- Modification complète d’Azure Tools pour prendre en charge les nouvelles API de gestion des ressources Azure
- Nouvelle prise en charge d’Azure App Service par [Cloud Explorer](azure-sdk-dot-net-release-notes-2_7.md#cloud_explorer)

###Problèmes connus

Les nœuds d’emplacement de déploiement de Web App n’apparaissent pas sous le nœud Emplacements de l’Explorateur de serveurs, et les nœuds enfants d’emplacement de déploiement de Web App ne se chargent pas sous Cloud Explorer. Ce problème a été résolu et préparé pour la prochaine version du Kit de développement logiciel (SDK).


##<a id="cloud_explorer"></a>Cloud Explorer pour Visual Studio 2015

Le Kit de développement logiciel (SDK) Azure 2.7 intègre Cloud Explorer pour Visual Studio 2015 qui vous permet de visualiser vos ressources Azure, d’inspecter leurs propriétés et d’exécuter des actions de développeur essentielles à partir de Visual Studio.

Cloud Explorer prend en charge les fonctionnalités suivantes :

- Vues Groupe de ressources et Type de ressource de vos ressources Azure 
- Recherche de ressources par nom (disponible dans la vue Type de ressource)
- Prise en charge des abonnements et des ressources auxquels s’applique le contrôle d’accès en fonction du rôle (RBAC) 
- Panneau Action intégrée affichant les actions pour développeurs qui sont propres aux ressources sélectionnées (par exemple, Attacher le débogueur distant pour les machines virtuelles créées à l’aide de la pile du Gestionnaire des ressources, Afficher les données de diagnostic pour les machines virtuelles, etc.)
- Panneau Propriétés intégrées affichant les propriétés pour développeurs généralement nécessaires lors des phases de développement ou de test 
- Changement rapide du compte à utiliser lors de l’énumération des ressources (à l’aide de la commande Paramètres de la barre d’outils) 
- Filtrage des abonnements à utiliser lors de l’énumération des ressources (à l’aide de la commande Paramètres de la barre d’outils) 
- Liens ciblés vers le portail Azure en version préliminaire pour la gestion des ressources et des groupes de ressources 
 
 
##Outils du gestionnaire de ressources Azure 

Les outils d’Azure Resource Manager ont été mis à jour de façon à fonctionner avec le contrôle d’accès en fonction du rôle (RBAC) et les nouveaux types d’abonnement. Ces modifications intègrent la possibilité d’utiliser les nouveaux comptes de stockage, en plus du stockage classique, pour stocker les artefacts lors du déploiement.

Si vous utilisez un projet de groupe de ressources Azure issu d’une version précédente du Kit de développement logiciel (SDK) avec le SDK 2.7, un nouveau script de déploiement est nécessaire pour effectuer le déploiement à l’aide d’un nouveau compte de stockage au lieu du stockage classique. Le système vous demandera confirmation avant de modifier votre projet pour y ajouter le nouveau script. L’ancien script sera renommé et vous devrez modifier le nouveau script manuellement.
 
 
##Outils de l’Explorateur de stockage 

- Prise en charge de l’affichage des types d’objet blob « Append Blob ». Pour plus d’informations, voir [ce billet de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx) (en anglais). 
- Prise en charge de l’affichage des comptes de stockage Premium par le biais de l’Explorateur de serveurs. Pour les comptes de stockage Premium, l’Explorateur de serveurs affiche uniquement les objets blob de pages, car il s’agit du seul type pris en charge pour ces comptes.

##Outils d’Azure Data Factory pour Visual Studio 

Introduction des **outils d’Azure Data Factory** pour Visual Studio. La liste ci-dessous répertorie les fonctionnalités activées. Pour plus d’informations, voir [ce blog](http://go.microsoft.com/fwlink/?LinkId=617530).

- **Création basée sur un modèle** : sélectionnez des modèles reposant sur des cas d’utilisation, des modèles de déplacement des données ou des modèles de traitement des données pour déployer une solution d’intégration de données de bout en bout et commencer à utiliser rapidement Data Factory. 
- **Intégration à l’Explorateur de solutions pour la création et le déploiement d’entités Data Factory** : créez et déployez des pipelines et des entités associées sous la forme de projets Visual Studio. 
- **Intégration à la vue Diagramme à des fins d’interaction visuelle pendant la création** : créez visuellement des pipelines et des jeux de données à l’aide de la vue Diagramme. 
- **Intégration à l’Explorateur de serveurs à des fins de navigation et d’interaction avec les entités déjà déployées** : tirez parti de l’Explorateur de serveurs pour parcourir les fabriques de données déjà déployées et les entités correspondantes. Importez une fabrique de données déployée ou n’importe quelle entité (pipeline, service lié, jeux de données) dans votre projet. 
- **Modification du code JSON avec validation de schéma et fonctionnalité IntelliSense enrichie** : configurez et modifiez efficacement les documents JSON des entités Data Factory grâce à la puissante fonctionnalité IntelliSense et à la validation de schéma. 
- **Publication multi-environnement** : publiez des pipelines créés dans un environnement de développement, de test ou de production en créant des fichiers de configuration distincts pour chaque environnement.
- **Prise en charge du traitement de données reposant sur Pig, Hive et .NET** : prenez en charge les scripts Pig et Hive dans vos projets Data Factory. Prenez également en charge le référencement de projets C# pour la gestion de .Net Activity.

##Voir aussi

[Billet d’annonce du Kit de développement logiciel (SDK) Azure 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Informations relatives à la prise en charge et au retrait pour le Kit de développement logiciel Azure SDK pour .NET et les API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

<!---HONumber=August15_HO7-->