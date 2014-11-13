<properties linkid="develop-python-cloud-services-with-ptvs" urlDisplayName="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" pageTitle="R&ocirc;les web et r&ocirc;les de travail Python avec Python Tools&nbsp;2.1 pour Visual Studio" metaKeywords="Azure python, web role, worker role, PTVS, cloud service" description="Pr&eacute;sentation des outils Python pour Visual Studio pour la cr&eacute;ation de services cloud Azure comprenant des r&ocirc;les web et de travail." metaCanonical="" services="" documentationCenter="Python" title="R&ocirc;les web et r&ocirc;les de travail Python avec Python Tools&nbsp;2.1 pour Visual Studio" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />

# Rôles web et rôles de travail Python avec Python Tools 2.1 pour Visual Studio

Ce guide fournit un aperçu de l'utilisation des rôles web et de travail Python avec [Python Tools pour Visual Studio][Python Tools pour Visual Studio].

-   [Configuration requise][Configuration requise]
-   [Présentation des rôles web et de travail Python][Présentation des rôles web et de travail Python]
-   [Création du projet][Création du projet]
-   [Exécution en local][Exécution en local]
-   [Publication dans Azure][Publication dans Azure]
-   [Étapes suivantes][Étapes suivantes]

## <a name="prerequisites"></a>Conditions préalables

-   Visual Studio 2012 ou 2013
-   [PTVS 2,1][PTVS 2,1]
-   [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013][Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013] ou [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012][Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012]
-   [Python 2.7 32 bits][Python 2.7 32 bits] ou [Python 3.4 32 bits][Python 3.4 32 bits]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Présentation des rôles web et de travail Python

Azure propose trois modèles de calcul pour l'exécution d'applications : [Sites web Azure][Sites web Azure], [Azure Virtual Machines][Azure Virtual Machines] et [Azure Cloud Services][Azure Cloud Services]. Ils prennent tous les trois en charge Python. Cloud Services, qui inclut les rôles web et de travail, fournit *PaaS (Platform as a Service)*. Au sein d'un service cloud, un rôle web fournit un serveur web IIS (Internet Information Services) dédié permettant d'héberger des applications web frontales, tandis qu'un rôle de travail peut exécuter des tâches asynchrones, de longue durée ou perpétuelles indépendamment des interactions ou saisies des utilisateurs.

Pour plus d'informations, consultez la page [Présentation d'un service cloud][Présentation d'un service cloud].

<div class="dev-callout"><strong>Vous voulez cr&eacute;er un simple site Web&nbsp;?</strong>
<p>Si votre sc&eacute;nario ne comporte qu'un simple composant frontal web, envisagez d'utiliser un site web Azure l&eacute;ger. Vous pouvez facilement passer &agrave; un service cloud en fonction de l'&eacute;volution de votre site et de vos besoins. Consultez le <a href="/fr-fr/develop/python/">Centre de d&eacute;veloppement Python</a> pour d&eacute;couvrir des articles abordant le d&eacute;veloppement pour les sites web Azure.</p>
</div>

## <a name="project-creation"></a>Création du projet

Dans Visual Studio, vous pouvez sélectionner **Service cloud Azure** dans la boîte de dialogue **Nouveau projet**, sous **Python**.

![Boîte de dialogue Nouveau projet][Boîte de dialogue Nouveau projet]

Dans l'Assistant Service Cloud Azure, vous pouvez sélectionner les nouveaux rôles web et de travail.

![Azure Cloud Service Dialog][Azure Cloud Service Dialog]

Le modèle de rôle de travail est fourni avec du code réutilisable permettant la connexion à un compte de stockage Azure ou à Service Bus.

![Cloud Service Solution][Cloud Service Solution]

Vous pouvez ajouter des rôles web ou des rôles de travail quand vous le souhaitez à un service cloud existant. Vous pouvez choisir d'ajouter des projets existants à votre solution ou bien d'en créer de nouveaux.

![Add Role Command][Add Role Command]

Votre service cloud peut contenir des rôles dans différents langages. Par exemple, vous pouvez avoir un rôle web Python implémenté à l'aide de Django, avec des rôles de travail Python C#. Vous pouvez assurer la communication entre les rôles grâce aux files d'attente du bus des services ou aux files d'attente de stockage.

## <a name="run-locally"></a>Exécution en local

Si vous définissez votre projet de service cloud comme projet de démarrage et que vous appuyez sur F5, le service cloud s'exécute dans l'émulateur Azure local.

Bien que PTVS puisse être lancé dans l'émulateur, le débogage (points d'arrêt, etc) ne fonctionne pas.

Pour déboguer vos rôles web et vos rôles de travail, vous pouvez définir le projet de rôle comme projet de démarrage, et déboguer ce projet. Vous pouvez aussi définir plusieurs projets de démarrage. Cliquez avec le bouton droit sur la solution, puis sélectionnez **Définir comme projet de démarrage**.

![Solution Startup Project Properties][Solution Startup Project Properties]

## <a name="publish-to-azure"></a>Publication dans Azure

Pour la publication, cliquez avec le bouton droit sur le projet de service cloud dans la solution, puis sélectionnez **Publier**.

![Microsoft Azure Publish Sign In][Microsoft Azure Publish Sign In]

Dans la page des paramètres, sélectionnez le service cloud sur lequel vous voulez publier.

![Microsoft Azure Publish Settings][Microsoft Azure Publish Settings]

Vous pouvez créer un service cloud si vous n'en avez pas déjà un.

![Create Cloud Service Dialog][Create Cloud Service Dialog]

Il est aussi utile d'activer les connexions Bureau à distance sur la ou les machines afin de pouvoir déboguer les problèmes.

![Remote Desktop Configuration Dialog][Remote Desktop Configuration Dialog]

Une fois que vous avez terminé la configuration des paramètres, cliquez sur **Publier**.

La progression s'affiche alors dans la fenêtre de résultat, puis vous verrez la fenêtre Journal des activités Microsoft Azure.

![Microsoft Azure Activity Log Window][Microsoft Azure Activity Log Window]

Le déploiement prend plusieurs minutes. Ensuite, vos rôles web et de travail sont exécutés sur Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur l'utilisation des rôles web et de travail dans les outils Python pour Visual Studio, consultez la documentation PTVS :

-   [Projets de service cloud][Projets de service cloud]

Pour plus de détails sur l'utilisation des services Azure à partir de vos rôles web et de travail, par exemple pour utiliser le stockage Azure ou Service Bus, consultez les guides suivants :

-   [Service Blob][Service Blob]
-   [Service de Table][Service de Table]
-   [Service de File d'attente][Service de File d'attente]
-   [Files d'attente de Service Bus][Files d'attente de Service Bus]
-   [Rubriques Service Bus][Rubriques Service Bus]

<!--Link references--> <!--External Link references-->

  [Python Tools pour Visual Studio]: http://pytools.codeplex.com
  [Configuration requise]: #prerequisites
  [Présentation des rôles web et de travail Python]: #what-are-python-web-and-worker-roles
  [Création du projet]: #project-creation
  [Exécution en local]: #run-locally
  [Publication dans Azure]: #publish-to-azure
  [Étapes suivantes]: #next-steps
  [PTVS 2,1]: http://go.microsoft.com/fwlink/?LinkId=517189
  [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
  [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
  [Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
  [Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
  [Sites web Azure]: /fr-fr/documentation/articles/fundamentals-application-models/#WebSites
  [Azure Virtual Machines]: /fr-fr/documentation/articles/fundamentals-application-models/#VMachine
  [Azure Cloud Services]: /fr-fr/documentation/articles/fundamentals-application-models/#CloudServices
  [Présentation d'un service cloud]: /fr-fr/manage/services/cloud-services/what-is-a-cloud-service/
  [Boîte de dialogue Nouveau projet]: ./media/cloud-services-python-ptvs/new-project-cloud-service.png
  [Azure Cloud Service Dialog]: ./media/cloud-services-python-ptvs/new-service-wizard.png
  [Cloud Service Solution]: ./media/cloud-services-python-ptvs/worker.png
  [Add Role Command]: ./media/cloud-services-python-ptvs/add-new-or-existing-role.png
  [Solution Startup Project Properties]: ./media/cloud-services-python-ptvs/startup.png
  [Microsoft Azure Publish Sign In]: ./media/cloud-services-python-ptvs/publish-sign-in.png
  [Microsoft Azure Publish Settings]: ./media/cloud-services-python-ptvs/publish-settings.png
  [Create Cloud Service Dialog]: ./media/cloud-services-python-ptvs/publish-create-cloud-service.png
  [Remote Desktop Configuration Dialog]: ./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png
  [Microsoft Azure Activity Log Window]: ./media/cloud-services-python-ptvs/publish-activity-log.png
  [Projets de service cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
  [Service Blob]: /fr-fr/documentation/articles/storage-python-how-to-use-blob-storage/
  [Service de Table]: /fr-fr/documentation/articles/storage-python-how-to-use-table-storage/
  [Service de File d'attente]: /fr-fr/documentation/articles/storage-python-how-to-use-queue-storage/
  [Files d'attente de Service Bus]: /fr-fr/documentation/articles/service-bus-python-how-to-use-queues/
  [Rubriques Service Bus]: /fr-fr/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/
