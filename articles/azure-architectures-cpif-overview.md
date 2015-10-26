<properties 
   pageTitle="Framework d'intégration de plateforme dans le cloud | Microsoft Azure" 
   description="Le Framework d'intégration de plateforme dans le cloud (CPIF) fournit des conseils d'intégration de charge de travail pour les applications d'accueil dans une solution Microsoft Cloud composée de modèles architecturaux pour Microsoft Azure." 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>


# Framework d'intégration de plateforme dans le cloud (modèles d'architecture Azure)

Le Framework d'intégration de plateforme dans le cloud (CPIF) fournit des conseils d'intégration de charge de travail pour les applications d'accueil dans une solution Microsoft Cloud.

CPIF décrit la manière dont les organisations, les clients et les partenaires doivent concevoir et déployer des charges de travail ciblées dans le cloud en utilisant les fonctionnalités hybrides de gestion et de plateforme dans le cloud d'Azure, de System Center et de Windows Server. Les domaines CPIF ont été répartis dans les fonctionnalités suivantes :

![Balises des panneaux de ressources et de groupe de ressources](./media/azure-architecture-cpif-overview/overview.png)

##  Architecture CPIF

Les environnements publics et privés de cloud computing fournissent des éléments courants permettant de prendre en charge l'exécution de charges de travail complexes. Bien que ces architectures soient relativement bien comprises dans des environnements physiques et virtuels locaux traditionnels, les constructions disponibles dans Microsoft Azure nécessitent une planification supplémentaire pour rationaliser les fonctionnalités de plateforme et d'infrastructure présentes dans les environnements publics de cloud computing. Pour prendre en charge le développement d'une application ou d'un service hébergé dans Azure, plusieurs modèles, qui vous indiquent les composants nécessaires pour composer une solution de charge de travail donnée spécifique, vous sont requis.

Ces modèles architecturaux appartiennent aux catégories suivantes :

- **Infrastructure** : Microsoft Azure est une solution IaaS (infrastructure as a service) et PaaS (platform as a service) constituée de plusieurs services et fonctionnalités sous-jacents. Ces services peuvent être décomposés en grande partie en services de calcul, de stockage et de réseau, mais il se peut que plusieurs fonctionnalités ne correspondent pas à ces définitions. Les modèles d'infrastructure présentent une zone fonctionnelle spécifique de Microsoft Azure, qui est requise pour fournir un service spécifique à une ou plusieurs solutions hébergées dans un abonnement Azure. 
- **Fondation** : lors de la composition d’un service ou d’une application multiniveau dans Azure, plusieurs éléments doivent être utilisés conjointement pour fournir un environnement d’hébergement approprié. Les modèles de Fondation composent un ou plusieurs services de Microsoft Azure permettant de prendre en charge une couche de fonctionnalités au sein d'une application. Cela peut nécessiter l'utilisation d'un ou plusieurs composants décrits dans les modèles d'infrastructure mentionnés ci-dessus. Par exemple, la couche de présentation d'une application multi-niveaux nécessite des compétences en termes de calcul, de réseau et de stockage dans Azure pour devenir fonctionnelle. Les modèles de Fondation sont censés être associés à d'autres modèles dans le cadre d'une solution spécifique.
- **Solution** : les modèles de solution sont composés de modèles d’infrastructure et/ou de fondation pour représenter une application finale ou un service en cours de développement. Des solutions complexes ne devraient pas être développées indépendamment des autres modèles. Au contraire, elles doivent utiliser des interfaces et des composants définis dans chacune des catégories de modèles indiquées ci-dessus.    

## Concepts de modèle architectural Azure

Pour prendre en charge le développement d'architectures de solution dans Azure, plusieurs guides de modèle vous sont proposés pour les scénarios courants. Bien que ces guides de scénario soient conçus progressivement, les modèles envisagés comprennent :

- Niveau global du web à charge équilibrée 
- Niveau de données à charge équilibrée
- Niveau de traitement par lots
- Mise en réseau hybride
- Azure Search 

##  Ressources supplémentaires
[Architecture CPIF (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) (en anglais)

## Voir aussi
[Niveau global du web à charge équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) (en anglais)

[Niveau de données à charge équilibrée](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41) (en anglais)

[Niveau de traitement par lots](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1) (en anglais)

[Mise en réseau Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38) (en anglais)

[Azure Search](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=Oct15_HO3-->