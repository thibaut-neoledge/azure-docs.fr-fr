<properties 
	pageTitle="Rôles Web et rôles de travail Python avec Python Tools 2.1 pour Visual Studio" 
	description="Présentation des outils Python pour Visual Studio pour la création de services cloud Azure comprenant des rôles Web et de travail." 
	services="" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huvalo"/>




# Rôles Web et rôles de travail Python avec Python Tools 2.1 pour Visual Studio

Ce guide fournit un aperçu de l'utilisation des rôles Web et de travail Python avec [Python Tools pour Visual Studio][].

## Configuration requise

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 pour Visual Studio][]
 - [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013][] ou [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012][]
 - [Python 2.7 32 bits][] ou [Python 3.4 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Présentation des rôles Web et de travail Python

Azure propose trois modèles de calcul pour l'exécution d'applications : [Azure Websites][sites Web de modèle d'exécution], [Azure Virtual Machines][machines virtuelles de modèle d'exécution] et [Azure Cloud Services][services cloud de modèle d'exécution]. Ils prennent tous les trois en charge Python. Cloud Services, qui contient des rôles Web et de travail, fournit *Platform as a Service (PaaS)*. Au sein d'un service cloud, un rôle Web fournit un serveur Web IIS (Internet Information Services) dédié permettant d'héberger des applications Web frontales, tandis qu'un rôle de travail peut exécuter des tâches asynchrones, de longue durée ou perpétuelles indépendamment des interactions ou saisies des utilisateurs.

Pour plus d'informations, consultez la page [Présentation d'un service cloud].

> [AZURE.NOTE] **Vous voulez créer un simple site Web ?**
Si votre scénario ne comporte qu'un simple composant frontal Web, envisagez d'utiliser un site Web Azure léger. Vous pouvez facilement passer à un service cloud en fonction de l'évolution de votre site et de vos besoins. Consultez le <a href="/fr-fr/develop/python/">Centre de développement Python</a> pour découvrir des articles abordant le développement pour les sites Web Azure.
<br />


## Création du projet

Dans Visual Studio, vous pouvez sélectionner **Service cloud Azure** dans la boîte de dialogue **Nouveau projet**, sous **Python**. 

![New Project Dialog](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Dans l'Assistant Service Cloud Azure, vous pouvez sélectionner les nouveaux rôles Web et de travail.

![Azure Cloud Service Dialog](./media/cloud-services-python-ptvs/new-service-wizard.png)

Le modèle de rôle de travail est fourni avec du code réutilisable permettant la connexion à un compte de stockage Azure ou à Service Bus.

![Cloud Service Solution](./media/cloud-services-python-ptvs/worker.png)

Vous pouvez ajouter des rôles Web ou des rôles de travail quand vous le souhaitez à un service cloud existant.  Vous pouvez choisir d'ajouter des projets existants à votre solution ou bien d'en créer de nouveaux. 

![Add Role Command](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Votre service cloud peut contenir des rôles dans différents langages.  Par exemple, vous pouvez avoir un rôle Web Python implémenté à l'aide de Django, avec des rôles de travail Python C#.  Vous pouvez assurer la communication entre les rôles grâce aux files d'attente du bus des services ou aux files d'attente de stockage.

## Exécution en local

Si vous définissez votre projet de service cloud comme projet de démarrage et que vous appuyez sur F5, le service cloud s'exécute dans l'émulateur Azure local.

Bien que PTVS puisse être lancé dans l'émulateur, le débogage (points d'arrêt, etc.) ne fonctionne pas.

Pour déboguer vos rôles Web et vos rôles de travail, vous pouvez définir le projet de rôle comme projet de démarrage, et déboguer ce projet.  Vous pouvez aussi définir plusieurs projets de démarrage.  Cliquez avec le bouton droit sur la solution, puis sélectionnez **Définir les projets de démarrage**.

![Solution Startup Project Properties](./media/cloud-services-python-ptvs/startup.png)

## Publication dans Azure

Pour la publication, cliquez avec le bouton droit sur le projet de service cloud dans la solution, puis sélectionnez **Publier**.

![Microsoft Azure Publish Sign In](./media/cloud-services-python-ptvs/publish-sign-in.png)

Dans la page des paramètres, sélectionnez le service cloud sur lequel vous voulez publier.

![Microsoft Azure Publish Settings](./media/cloud-services-python-ptvs/publish-settings.png)

Vous pouvez créer un service cloud si vous n'en avez pas déjà un.

![Create Cloud Service Dialog](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

Il est aussi utile d'activer les connexions Bureau à distance sur la ou les machines afin de pouvoir déboguer les problèmes.

![Remote Desktop Configuration Dialog](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

Une fois que vous avez terminé la configuration des paramètres, cliquez sur **Publier**.

La progression s'affiche alors dans la fenêtre de résultat, puis vous verrez la fenêtre Journal des activités Microsoft Azure.

![Microsoft Azure Activity Log Window](./media/cloud-services-python-ptvs/publish-activity-log.png)

Le déploiement prend plusieurs minutes. Ensuite, vos rôles Web et de travail sont exécutés sur Azure.

## Étapes suivantes

Pour plus d'informations sur l'utilisation des rôles Web et de travail dans les outils Python pour Visual Studio, consultez la documentation PTVS :

- [Projets de Service cloud][]

Pour plus de détails sur l'utilisation des services Azure à partir de vos rôles Web et de travail, par exemple pour utiliser le stockage Azure ou Service Bus, consultez les guides suivants :
 
- [Service BLOB][]
- [Service de table][]
- [Service de file d'attente][]
- [Files d'attente Service Bus][]
- [Rubriques Service Bus][]


<!--Link references-->

[Présentation d'un service cloud]: /fr-fr/manage/services/cloud-services/what-is-a-cloud-service/
[sites Web de modèle d'exécution]: ../fundamentals-application-models/#WebSites
[machines virtuelles de modèle d'exécution]: ../fundamentals-application-models/#VMachine
[services cloud de modèle d'exécution]: ../fundamentals-application-models/#CloudServices
[Centre de développement Python]: /fr-fr/develop/python/

[Service BLOB]: ../storage-python-how-to-use-blob-storage/
[Service de file d'attente]: ../storage-python-how-to-use-queue-storage/
[Service de table]: ../storage-python-how-to-use-table-storage/
[Files d'attente Service Bus]: ../service-bus-python-how-to-use-queues/
[Rubriques Service Bus]: ../service-bus-python-how-to-use-topics-subscriptions/


<!--External Link references-->

[Python Tools pour Visual Studio]: http://aka.ms/ptvs
[Documentation relative à Python Tools pour Visual Studio]: http://pytools.codeplex.com/documentation 
[Projets de Service cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project

[Python Tools 2.1 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191

<!--HONumber=45--> 
