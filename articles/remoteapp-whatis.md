<properties title="What is RemoteApp?" pageTitle="What is RemoteApp?" description="Learn about RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# Présentation d'Azure RemoteApp

RemoteApp vous permet de faire en sorte que les programmes accessibles à distance par le biais d'Azure semblent s'exécuter sur l'ordinateur local de l'utilisateur final. Ces programmes sont connus sous le nom de programmes RemoteApp. Au lieu d'être présenté à l'utilisateur dans le bureau du serveur Hôte de session Bureau à distance, le programme RemoteApp est intégré au bureau du client. Le programme RemoteApp s'exécute dans sa propre fenêtre redimensionnable, qui peut être déplacée d'un moniteur à un autre, et dispose de sa propre entrée dans la barre des tâches. Si un utilisateur exécute plusieurs programmes RemoteApp sur le même serveur Hôte de session Bureau à distance, le programme RemoteApp partagera la même session Services Bureau à distance.

RemoteApp permet de réduire la complexité et la surcharge administrative dans de nombreuses situations, y compris les suivantes :

-   Les filiales se caractérisant par un support informatique limité sur site et une bande passante limitée pour le réseau.
-   Les situations où les utilisateurs doivent pouvoir accéder aux programmes à distance.
-   Le déploiement de programmes métier, en particulier les programmes métier personnalisés.
-   Les environnements, tels que les postes de travail ou les espaces de travail partagés, où les utilisateurs n'ont pas leur propre ordinateur.
-   Le déploiement de plusieurs versions d'un programme, en particulier si l'installation de plusieurs versions en local est susceptible de créer des conflits.

Contrairement aux Services Bureau à distance traditionnels, Azure RemoteApp s'exécute dans le portail de gestion Azure. Vos utilisateurs accèdent aux programmes par le biais du portail, tandis que vous effectuez l'ensemble de la gestion des programmes et des utilisateurs par le biais du portail d'administration.

Il existe deux types de déploiement RemoteApp :

-   Un déploiement sur le cloud est hébergé et stocke toutes les données des programmes dans le cloud Azure.
-   Un déploiement hybride est hébergé dans le cloud Azure mais permet aux utilisateurs d'accéder aux données stockées sur votre réseau local.

Quel que soit le type de déploiement choisi, après avoir créé le service, vous publiez les programmes que vous voulez partager avec vos utilisateurs sur le flux des utilisateurs finaux. Il s'agit de la liste des programmes disponibles auxquels vos utilisateurs ont accès par le biais du portail Azure. Sachez que le flux présente tous les programmes de tous les services associés à votre abonnement.

