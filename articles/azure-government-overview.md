<properties 
   pageTitle="Vue d'ensemble d'Azure Government" 
   description="Cet article fournit une vue d'ensemble des fonctionnalités Azure Government Cloud ainsi que de la conception et de la sécurité sans faille utilisées pour prendre en charge la conformité applicable aux organisations gouvernementales fédérales, d'état et locales et à leurs partenaires." 
   services="Azure-Government" 
   documentationCenter="" 
   authors="joharve2" 
   manager="chrisnie" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="azure-government" 
   ms.date="03/13/2015"
   ms.author="john.harvey@microsoft.com"/>

#  Vue d'ensemble de Microsoft Azure Government 

<p> Microsoft Azure Government est une instance isolée physiquement et isolée de tout réseau de Microsoft Azure. Ce guide du développeur fournit des détails sur les différences dont les développeurs d'applications et les administrateurs doivent tenir compte quand ils gèrent ces zones distinctes d'Azure.


## <a name="Overview"></a>Vue d’ensemble

Azure Government est un cloud communautaire gouvernemental (GCC, Government-Community Cloud) conçu pour prendre en charge les scénarios gouvernementaux stratégiques nécessitant débit, mise à l'échelle, sécurité, conformité et économie pour les organisations gouvernementales américaines. Il a été développé sur l'expérience confirmée de Microsoft dans l'offre de logiciels, de sécurité, de conformité et de contrôles dans les autres offres de cloud Microsoft comme Azure public, Office 365, O365 GCC, Microsoft CRM Online, etc.

En outre, Azure Government est conçu pour répondre aux besoins considérables de sécurité et de conformité pour les charges de travail sensibles et dédiées de l’administration publique américaine dans les réglementations telles que celles du programme FedRAMP (Federal Risk and Authorization Management Program), du Département de la défense ECSB (Enterprise Cloud Service Broker), de la politique sécuritaire des services CJIS (Criminal Justice Information Services) et de la loi HIPAA (Health Insurance Portability and Accountability Act).

Voici un récapitulatif de l'infrastructure, la structure et des services Azure Government Cloud disponibles pour aider les organisations gouvernementales à créer des solutions cloud hybrides pour atteindre leurs objectifs. Comme certains services ci-dessous sont disponibles uniquement en version préliminaire, consultez la [page des régions](http://azure.microsoft.com/regions/#services), car les services les plus récents généralement disponibles y sont répertoriés.

![][2]

Azure Government inclut les principaux composants d'IaaS (Infrastructure-as-a-Service) et PaaS (Platform-as-a-Service). Cela inclut l'infrastructure, le réseau, le stockage, la gestion des données, la gestion des identités et beaucoup d'autres services. Azure Government prend en charge les mêmes fonctionnalités exceptionnelles que celles exploitées par les clients Azure publics comme la réplication de données géosynchrone et la mise à l'échelle automatique. Microsoft a été identifié comme premier fournisseur d’offres <a href="https://www.gartner.com/doc/2575715/magic-quadrant-cloud-infrastructure-service" target="_new">IaaS</a> et <a href="https://www.gartner.com/doc/2645317/magic-quadrant-enterprise-application-platform" target="_new">PaaS<a/> par les principaux analystes du secteur.

En plus de fournir les services et fonctionnalités robustes d'Azure public, Azure Government fournit plusieurs fonctionnalités pour garantir la sécurisation de leurs données aux entités gouvernementales américaines en leur fournissant ce qui suit :

- **Instance physique et isolée du réseau** – L’environnement Azure Government est une instance complètement distincte de la version publique de Microsoft Azure. Il est uniquement utilisé par des organisations gouvernementales américaines et fournisseurs de solutions agréés.

- **Sécurité, confidentialité et conformité** - Microsoft a implémenté sa solide infrastructure de contrôles de sécurité, de confidentialité et de conformité, en plus de contrôles stricts complémentaires afin de répondre aux plus hauts niveaux d’exigences décrits dans les niveaux d’impact ECSB et dans les CJIS.

- **Stockage des données** – L’environnement Azure Government entretient deux centres de données distants de plus de 800 kilomètres. Toutes les données gérées par le client sont stockées dans les centres de données CONUS (Continental United States).

- **Personnel américain** – Tous les opérateurs et administrateurs Azure Government sont des citoyens américains passés au crible.

- **Gestion des identités** – La gestion des identités dans l’environnement Azure Government est une instance distincte d’Azure Active Directory.

- **Conformité** – Microsoft investit continuellement pour répondre aux besoins stricts de conformité fédérale, d’état et locale en constante évolution et pour maintenir cette conformité, notamment dans les solutions cloud du gouvernement américain comme FedRAMP, CJIS, ECSB et HIPAA.

- **Intégration cloud** – Azure Government fournit un environnement intégré dans O365 Governement permettant une authentification unique entre les services cloud et les services améliorés, tels que 1 To d’espace de stockage OneDrive.

Azure Government permet également aux organisations de gérer leurs investissements technologiques existants et de bénéficier des avantages des services cloud. Étant donné qu'Azure Government est une plateforme cloud interopérable, avec des produits et technologies, les organisations peuvent créer des applications plus ouvertes dès le départ. Les agences peuvent choisir les outils, les services, le système d'exploitation, l'architecture et les infrastructures, notamment Windows, Linux, Oracle, SharePoint, .NET, Java, PHP et Node.js pour leurs solutions cloud. La flexibilité de la plateforme Azure Government permet de nouvelles formes de collaboration entre les agences, de développement d'applications et d'intégration.

Les organisations gouvernementales américaines intéressées par les services cloud peuvent être sûres qu'Azure Government assure une considérable mise à l'échelle et des pratiques de sécurité rigoureuses pour répondre à leurs besoins en constante évolution.







## <a name="Features"></a> Fonctionnalités actuellement disponibles dans Microsoft Azure Government
Azure Government offre les services suivants dans les régions des états américains de l'Iowa et de la Virginie :

- Machines virtuelles
- Services cloud
- Stockage
- Active Directory
- Scheduler
- Réseau virtuel
- Base de données SQL
- Azure Files
- Media Services
- Traffic Manager
- Service Bus

D'autres services sont disponibles. Par ailleurs, des services seront ajoutés de façon continue. Pour obtenir la liste actualisée des services, consultez la [page des régions](http://azure.microsoft.com/regions/#services) qui indique les régions disponibles et leurs services.

Actuellement, les centres de données Azure Government se trouvent dans les états américains de l'Iowa et de la Virginie. Consultez la page des régions ci-dessus pour connaître les centres de données et les services disponibles actuellement.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>Étapes suivantes

Si vous souhaitez en savoir plus sur Azure Governement, consultez certains des liens ci-dessous.

- **<A href="http://azure.com/gov">Achat et utilisation d’Azure Government</a>**

- **<A href="/azure-government-developer-guide">Guide du développeur Azure Government</a>**

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines/virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=July15_HO3-->