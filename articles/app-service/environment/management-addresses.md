---
title: Adresses de gestion Azure App Service Environment
description: "Répertorie les adresses de gestion utilisées pour la commande d’un environnement App Service"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 949b445793768f593af205321bf3ab5a7aa150c0
ms.contentlocale: fr-fr
ms.lasthandoff: 07/14/2017

---
# <a name="app-service-environment-management-addresses"></a>Adresses de gestion App Service Environment

L’environnement App Service Environment (ASE) est un déploiement du service Azure App Service dans un sous-réseau de votre réseau virtuel Azure.  L’ASE doit être accessible à partir du service Azure App Service pour pouvoir être géré.  Ce trafic de gestion ASE parcourt le réseau géré par l’utilisateur.  Il provient des serveurs d’administration Azure App Service et arrive à l’adresse IP virtuelle publique associée à l’ASE.  Pour plus d’informations sur les dépendances de mise en réseau d’un ASE, consultez la page [Considérations relatives à la mise en réseau pour un environnement App Service Environment][networking].  Pour des informations générales sur l’ASE, vous pouvez commencer par consulter la page [Présentation de l’environnement App Service Environment][intro].

Ce document répertorie les adresses IP sources pour le trafic de gestion vers l’ASE. Vous pouvez utiliser ces adresses afin de créer des groupes de sécurité réseau pour verrouiller le trafic entrant ou les utiliser dans les tables de routage en fonction des besoins.  Voici ce dont vous aurez besoin pour utiliser ces informations :

* les adresses IP répertoriées pour toutes les régions ;
* les adresses IP qui correspondent à la région dans laquelle votre ASE est déployé.

Le trafic de gestion entrant arrive de ces adresses IP sur les ports 454 et 455.

| Région | Adresses |
|--------|-----------|
| Toutes les régions | 70.37.57.58, 157.55.208.185, 52.174.22.21,13.94.149.179,13.94.143.126,13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141 |
| Centre Sud et Centre Nord des États-Unis | 23.102.188.65, 191.236.154.88 |
| Sud-Est de l’Australie et Est de l’Australie | 23.101.234.41, 104.210.90.65 |
| Ouest des États-Unis et Est des États-Unis | 104.45.227.37, 191.236.60.72 |
| Europe de l’Ouest et Europe du Nord | 191.233.94.45, 191.237.222.191 |
| Centre Ouest des États-Unis et Ouest des États-Unis 2 | 13.78.148.75, 13.66.225.188 |
| Centre des États-Unis et Est des États-Unis 2 | 104.43.165.73, 104.46.108.135 |
| Asie orientale et Asie du Sud-Est | 23.99.115.5, 104.215.158.33 |
| Est du Japon et Ouest du Japon | 104.41.185.116, 191.239.104.48 |
| Centre du Canada et Est du Canada | 40.85.230.101, 40.86.229.100 |
| Ouest du Royaume-Uni et Sud du Royaume-Uni | 51.141.8.34, 51.140.185.75 |
| Corée du Sud et Centre de la Corée | 52.231.200.177, 52.231.32.117 |
| Sud du Brésil et Centre Sud des États-Unis| 104.41.46.178, 23.102.188.65 |
| Inde centrale et Inde du Sud | 104.211.98.24, 104.211.225.66 |
| Inde-Ouest et Inde-Sud | 104.211.160.229, 104.211.225.66 |


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md

