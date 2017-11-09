---
title: "Recommandations de sécurité pour les images Place de marché Microsoft Azure | Microsoft Docs"
description: "Cet article fournit des recommandations pour les images incluses sur la Place de marché"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: barclayn
ms.openlocfilehash: 4ae36f87c29975c82bb99f713893a9dc78a249e6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2017
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Recommandations de sécurité pour les images Place de marché Microsoft Azure

Nous recommandons que chaque solution respecte les recommandations de configuration de sécurité suivantes. Cela permet de maintenir un niveau élevé de sécurité pour les images de solutions partenaires dans la Place de marché.

Ces recommandations peuvent également être utiles pour les organisations qui n’ont pas d’images dans la Place de marché. Vous voudrez comparer les configurations des images Windows et Linux de votre entreprise aux lignes directrices des tableaux ci-dessous.

## <a name="open-source-based-images"></a>Images open source

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Catégorie**                                                 | **Vérification**                                                                                                                                                                                                                                                                              |
| Sécurité                                                     | Tous les derniers correctifs de sécurité pour la distribution de Linux sont installés.                                                                                                                                                                                                              |
| Sécurité                                                     | Vous avez suivi les bonnes pratiques du secteur pour sécuriser l’image de machine virtuelle pour votre distribution de Linux spécifique.                                                                                                                                                                                     |
| Sécurité                                                     | Limitez la surface d’attaque en conservant un encombrement minimal avec seulement les rôles Windows Server, fonctionnalités, services et ports réseau strictement nécessaires.                                                                                                                                               |
| Sécurité                                                     | Analysez le code source et l’image de machine virtuelle qui en résulte à la recherche de programmes malveillants.                                                                                                                                                                                                                                   |
| Sécurité                                                     | L’image de disque dur virtuel inclut uniquement les comptes verrouillés nécessaires, qui n’ont pas de mots de passe par défaut qui permettent une connexion interactive ; pas de failles.                                                                                                                                           |
| Sécurité                                                     | Les règles de pare-feu sont désactivées, sauf si l’application compte fonctionnellement sur elles, par exemple dans le cas d’un appareil de pare-feu.                                                                                                                                                                             |
| Sécurité                                                     | Toutes les informations sensibles ont été supprimées de l’image de disque dur virtuel, comme les clés SSH de test, le fichier d’hôtes connus, les fichiers journaux et les certificats inutiles.                                                                                                                                       |
| Sécurité                                                     | Nous recommandons de ne pas utiliser LVM.                                                                                                                                                                                                                                            |
| Sécurité                                                     | Les versions les plus récentes des bibliothèques requises doivent être incluses : </br> - OpenSSL v1.0 ou version ultérieure </br> - Python 2.5 ou version ultérieure (Python 2.6+ est vivement recommandé) </br> - Package Python pyasn1, le cas échéant </br> - d.OpenSSL v 1.0 ou version ultérieure                                                                |
| Sécurité                                                     | Les entrées d’historique de commandes Bash/Shell doivent être désactivées                                                                                                                                                                                                                                             |
| Mise en réseau                                                   | Le serveur SSH doit être inclus par défaut. Réglez l'option de maintien de la connexion au fichier de configuration du serveur SSH avec l'option suivante : ClientAliveInterval 180                                                                                                                                                        |
| Mise en réseau                                                   | L’image ne doit contenir aucune configuration de réseau personnalisée. Supprimez resolv.conf :`rm /etc/resolv.conf`                                                                                                                                                                                |
| Déploiement                                                   | Le dernier agent Azure Linux doit être installé </br> - L’agent doit être installé à l’aide du package RPM ou Deb.  </br> - Vous pouvez également utiliser le processus d’installation manuelle, mais les packages d’installation sont recommandés et préférés. </br> - Si vous installez l’agent manuellement à partir du référentiel github, vous devez tout d’abord copier le fichier `waagent` sur `/usr/sbin` et exécuter (en tant que root) : </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Le fichier de configuration de l’agent sera placé dans `/etc/waagent.conf`.    |
| Déploiement                                                   | Garantit que le Support Azure peut fournir à nos partenaires la sortie de console série lorsque cela est nécessaire et indiquer un délai d’expiration adapté au montage du disque de système d’exploitation à partir du stockage cloud. L’image doit avoir ajouté les paramètres suivants à la ligne de démarrage du noyau :`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Déploiement                                                   | Aucune partition d’échange n’est présente sur le disque du système d’exploitation. L’échange peut être demandé pour la création sur le disque de la ressource locale par l’agent Linux.         |
| Déploiement                                                   | Il est recommandé qu’une seule partition racine unique soit créée pour le disque du système d’exploitation.      |
| Déploiement                                                   | Système d’exploitation 64 bits uniquement.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Images Windows Server

|||
|-------------| -------------------------|
| **Catégorie**                                                     | **Vérification**                                                                                                                                                                |
| Sécurité                                                         | Utilisez une image de base de système d’exploitation sécurisée. Le disque dur virtuel utilisé pour la source de n’importe quelle image basée sur Windows Server doit venir d’images de système d’exploitation Windows Server obtenues via Microsoft Azure. |
| Sécurité                                                         | Installez toutes les dernières mises à jour de sécurité.                                                                                                                                     |
| Sécurité                                                         | Les applications ne doivent pas avoir une dépendance sur les noms d’utilisateur limités, comme administrator, root et admin.                                                                |
| Sécurité                                                         | Le chiffrement de lecteur BitLocker n’est pas pris en charge sur le disque du système d’exploitation. BitLocker peut être utilisé sur les disques de données.                                                            |
| Sécurité                                                         | Limitez la surface d’attaque en conservant un encombrement minimal avec seulement les rôles Windows Server, fonctionnalités, services et ports réseau strictement nécessaires activés.                         |
| Sécurité                                                         | Analysez le code source et l’image de machine virtuelle qui en résulte à la recherche de programmes malveillants.                                                                                                                     |
| Sécurité                                                         | Réglez les mises à jour de sécurité pour les images Windows Server pour qu’elles se fassent automatiquement.                                                                                                                |
| Sécurité                                                         | L’image de disque dur virtuel inclut uniquement les comptes verrouillés nécessaires, qui n’ont pas de mots de passe par défaut qui permettent une connexion interactive ; pas de failles.                             |
| Sécurité                                                         | Les règles de pare-feu sont désactivées, sauf si l’application compte fonctionnellement sur elles, par exemple dans le cas d’un appareil de pare-feu.                                                               |
| Sécurité                                                         | Toutes les informations sensibles ont été supprimées de l’image de disque dur virtuel. Par exemple, le fichier HOSTS, les fichiers journaux et les certificats inutiles doivent être supprimés.                                              |
| Déploiement                                                       | Système d’exploitation 64 bits uniquement.                            |
