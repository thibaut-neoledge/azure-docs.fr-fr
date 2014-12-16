<properties urlDisplayName="" pageTitle="Résolution des problèmes de contrôle d'accès basé sur un rôle" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Role based access control troubleshooting" authors="Stephen Siciliano"  solutions="" writer="" manager="terrylan" editor=""  />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="11/03/2014" ms.author="Stephen Siciliano" />

# Résolution des problèmes de contrôle d'accès basé sur un rôle

## Introduction

Le [contrôle d'accès basé sur un rôle](http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-configure/) est une fonctionnalité puissante qui vous permet de déléguer l'accès affiné aux ressources dans Azure. Cela signifie que vous pouvez en toute sécurité accorder à la personne de votre choix le droit de faire ce qu'elle a besoin de faire. Toutefois, le modèle de ressources pour les ressources Azure peut parfois être complexe, et il peut s'avérer difficile de comprendre avec précision pourquoi accorder certaines autorisations.

Ce document vous permet de savoir ce à quoi vous pouvez vous attendre lors de l'utilisation des nouveaux rôles dans la version préliminaire.  Cette version inclut trois rôles intégrés :
* Propriétaire
* Collaborateur
* Lecteur

Les propriétaires et collaborateurs disposent d'un accès complet à toutes les opérations de gestion, mais un collaborateur ne peut pas accorder d'accès à d'autres utilisateurs ou groupes. La situation du rôle de lecteur est un peu plus intéressante, et nous allons nous y attarder. [Consultez cet article](http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-configure/) pour découvrir la procédure détaillée vous permettant d'accorder l'accès.

## Charges de travail des sites Web

### Accès en lecture uniquement 

Si vous accordez à un utilisateur ou disposez vous-même de l'accès en lecture à un site Web, il se peut que certaines fonctionnalités inattendues ne soient pas accessibles. Les fonctionnalités de gestion suivantes exigent un accès en **écriture** au site Web (Collaborateur ou Propriétaire) et ne sont pas accessibles en lecture seule. 
 
1. Commandes (par ex., start, stop, etc.)
2. Modification de paramètres tels que la configuration générale, les paramètres de mise à l'échelle, les paramètres de sauvegarde et les paramètres d'analyse.
3. Accès aux informations d'identification de publication et autres informations secrètes, telles que les paramètres d'application et les chaînes de connexion.
4. Diffusion de journaux
5. Configuration des journaux de diagnostic
6. Console de sites Web (invite de commandes)
7. Déploiements actifs et récents (pour le déploiement continu Git local)
8. Estimation de dépense
9. Tests Web
10. Réseau virtuel (accessible à un lecteur uniquement si un réseau virtuel a été précédemment configuré par un utilisateur avec accès en écriture).
 
Si vous n'accédez à aucune de ces fonctionnalités, vous devez obtenir le rôle Collaborateur pour accéder à votre site Web. 

### Gestion des ressources connexes
 
Les sites Web sont compliqués par la présence de quelques ressources différentes qui interagissent. Voici un groupe de ressources classique avec deux sites Web :

![Website resource group](./media/role-based-access-control-troubleshooting/Website-resource-model.png)

En conséquence, si vous accordez à un utilisateur le seul accès au site Web, la plupart des fonctionnalités du volet Site Web seront désactivées. 
 
1. Les éléments suivants requièrent l'accès au **plan d'hébergement web** qui correspond à votre site web :  
    * Affichage du niveau de tarification du site Web (par ex., Gratuit, Partagé, De base ou Standard).
    * Configuration de mise à l'échelle (c'est-à-dire le nombre d'instances, la taille de la machine virtuelle, les paramètres de mise à l'échelle automatique).
    * Quotas (par ex., stockage, bande passante, UC).
2. Les éléments suivants requièrent l'accès à l'ensemble du **Groupe de ressources** qui contient le site web :  
    * Certificats et liaisons SSL (car les certificats SSL peuvent être partagés entre différents sites dans le même groupe de ressources et emplacement).
    * Règles d'alerte
    * Paramètres de mise à l'échelle automatique
    * Composants Application Insights
    * Tests Web

## Charges de travail des machines virtuelles

Comme pour les sites Web, certaines fonctionnalités du volet Machine virtuelle exigent un accès en écriture à la machine virtuelle ou à d'autres ressources du groupe de ressources.

Les ressources suivantes sont associées aux machines virtuelles :
* Noms de domaine
* Réseaux virtuels
* Comptes de stockage
* Règles d'alerte

1. Les éléments suivants requièrent l'accès en **écriture** à la machine virtuelle :  
    * Points de terminaison
    * Adresses IP
    * Disques
    * Extensions
2. Les éléments suivants requièrent l'accès à la machine virtuelle, ainsi qu'au **Groupe de ressources** (avec le nom de domaine) auquel ils appartiennent :  
    * Groupe à haute disponibilité
    * Jeu d'équilibrage de la charge
    * Règles d'alerte
    
Si vous n'accédez à aucune de ces fonctionnalités, vous devez obtenir auprès de votre administrateur le rôle Collaborateur pour accéder au groupe de ressources.
