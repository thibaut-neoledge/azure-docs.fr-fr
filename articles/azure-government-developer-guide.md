<properties 
	pageTitle="Guide du développeur Azure Government" 
	description="This provides a comparision of features and guidance on developing applications for Azure Government" 
	services="" 
	documentationCenter="" 
	authors="Joharve2" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="01/21/2014" 
	ms.author="jharve"/>


#  Guide du développeur Microsoft Azure Government 

<p> Microsoft Azure Government est une instance isolée physiquement et isolée de tout réseau de Microsoft Azure. Ce guide du développeur fournit des détails sur les différences dont les développeurs d'applications et les administrateurs doivent tenir compte quand ils gèrent ces zones distinctes d'Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## Dans cette rubrique


+ [Vue d'ensemble](#Overview)
+ [Aide pour les développeurs](#Guidance)
+ [Fonctionnalités actuellement disponibles dans Microsoft Azure Government](#Features)
+ [Mappage de point de terminaison](#Endpoint)
+ [Étapes suivantes](#next)


## <a name="Overview"></a>Vue d’ensemble

Microsoft Azure Government est une instance distincte du service Microsoft Azure. Elle répond aux impératifs de sécurité et de conformité des agences fédérales américaines, des gouvernements des États et des administrations locales, ainsi que leurs fournisseurs de solutions. Azure Government offre un isolement physique et réseau par rapport aux déploiements qui ne sont pas ceux du gouvernement américain. Par ailleurs, il met à disposition un personnel américain sélectionné.

Microsoft fournit un certain nombre d'outils qui permettent de créer et déployer des applications cloud pour le service global Microsoft Azure (« service global ») et les services Microsoft Azure Government.

Durant la création et le déploiement d'applications pour les services Azure Government, par opposition au service global, les développeurs doivent connaître les principales différences qui existent entre ces deux services. Plus précisément, ils doivent savoir installer et configurer leur environnement de programmation, configurer les points de terminaison, écrire des applications et les déployer en tant que services dans Azure Government.

Les informations contenues dans ce document résument ces différences et complètent les informations disponibles sur le site [Azure Government](http://www.azure.com/gov "Azure Government") et sur le site de la [bibliothèque technique de Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") sur MSDN. Des informations officielles sont également disponibles à de nombreux autres emplacements, par exemple le [Centre de gestion de la confidentialité Microsoft Azure](http://azure.microsoft.com/support/trust-center/ "Centre de gestion de la confidentialité Microsoft Azure"), le [Centre de documentation Azure](http://azure.microsoft.com/documentation/) et les [blogs Azure](http://azure.microsoft.com/blog/ "Blogs Azure").

Ce contenu est destiné aux partenaires et développeurs qui effectuent des déploiements dans Microsoft Azure Government.



## <a name="Guidance"></a>Aide pour les développeurs
Dans la mesure où la plupart du contenu technique disponible actuellement suppose que les applications sont développées pour le service global plutôt que pour Microsoft Azure Government, vous devez vous assurer que les développeurs sont bien conscients des différences clés relatives aux applications développées pour être hébergées dans Azure Government.

- Tout d'abord, il existe des différences en matière de services et de fonctionnalités. Cela signifie que certaines fonctionnalités liées à des régions spécifiques du service global peuvent ne pas être disponibles dans Azure Government.

- Ensuite, pour les fonctionnalités offertes dans Azure Government, il existe des différences de configuration par rapport au service global. Ainsi, vous devez vérifier vos exemples de code, configurations et étapes de travail pour vous assurer que vous générez et exécutez des applications dans l'environnement des services cloud Azure Government.


## <a name="Features"></a> Fonctionnalités actuellement disponibles dans Microsoft Azure Government
Azure Government offre les services suivants dans les régions des états américains de l'Iowa et de la Virginie :

- Machines virtuelles
- Services cloud
- Stockage
- Active Directory
- Scheduler
- Réseau virtuel
- Base de données SQL

D'autres services sont disponibles. Par ailleurs, des services seront ajoutés de façon continue. Pour obtenir la liste actualisée des services, consultez la [page des régions](http://azure.microsoft.com/regions/#services) qui indique les régions disponibles et leurs services.

Actuellement, les centres de données Azure Government se trouvent dans les états américains de l'Iowa et de la Virginie. Consultez la page des régions ci-dessus pour connaître les centres de données et les services disponibles actuellement.

## <a name="Endpoint"></a>Mappage de point de terminaison

Aidez-vous du tableau suivant quand vous mappez des points de terminaison publics Microsoft Azure et SQL Database à des points de terminaison Azure Government spécifiques.


Type de service|Azure (public)|Azure Government
---|---|---
Accueil d'Azure Government|windowsazure.com|microsoftazure.us
Portail de gestion|manage.windowsazure.com|manage.windowsazure.us
Généralités|*.windows.net|*.usgovcloudapi.net
Principal|*.core.windows.net|*.core.usgovcloudapi.net
Calcul|*.cloudapp.net|*.usgovcloudapp.net
Stockage d’objets blob|*.blob.core.windows.net| *.blob.core.usgovcloudapi.net Queue Storage|*.queue.core.windows.net|*.queue.core.usgovcloudapi.net
Stockage de tables|*.table.core.windows.net|*.table.core.usgovcloudapi.net
Gestion des services|management.core.windows.net|management.core.usgovcloudapi.net
Base de données SQL|*.database.windows.net|*.database.usgovcloudapi.net

## <a name="next"></a>Étapes suivantes
Pour plus d’informations sur Azure Government et sur la façon dont votre organisation peut en bénéficier, accédez à <A href="http://azure.com/gov">http://www.azure.com/gov</a>.

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=July15_HO5-->