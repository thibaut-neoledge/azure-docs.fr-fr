---
title: Prise en main du serveur Azure Multi-Factor Authentication | Microsoft Docs
description: "Ceci est la page d'authentification multifacteur Azure qui explique la prise en main du serveur Azure MFA."
services: multi-factor-authentication
keywords: "authentification serveur, page d’activation d’application d’authentification Azure Multi Factor Authentication, téléchargement du serveur d’authentification"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: e94120e4-ed77-44b8-84e4-1c5f7e186a6b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/26/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 4235dfd0e17b9892787dd86d807b8f1f6e360675
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017

---

<a id="getting-started-with-the-azure-multi-factor-authentication-server" class="xliff"></a>

# Prise en main du serveur Azure Multi-Factor Authentication

<center>![MFA en local](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Maintenant que nous avons déterminé s’il fallait utiliser le serveur Multi-Factor Authentication local, nous pouvons commencer. Cette page vous indique comment installer le serveur et le configurer avec votre Active Directory local. Si le serveur MFA est déjà installé et que vous souhaitez effectuer une mise à niveau, voir [Mise à niveau vers le serveur Azure Multi-Factor Authentication le plus récent](multi-factor-authentication-server-upgrade.md). Pour plus d’informations sur l’installation du service web, voir [Déploiement du service web de l’application mobile pour le serveur Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md).
 
<a id="plan-your-deployment" class="xliff"></a>

## Planifier votre déploiement

Avant de télécharger le serveur Microsoft Azure Multi-Factor Authentication, pensez à vos exigences en termes de charge et de haute disponibilité. Prenez en compte ces informations pour décider de quelle manière et à quel endroit effectuer le déploiement. 

Le nombre d’utilisateurs que vous souhaitez authentifier, sur une base régulière, est une bonne ligne directrice pour avoir une idée de la quantité de mémoire nécessaire. 

| Users | RAM |
| ----- | --- |
| 1-10,000 | 4 Go |
| 10,001-50,000 | 8 Go |
| 50,001-100,000 | 12 Go |
| 100,000-200,001 | 16 Go |
| 200,001+ | 32 Go |

Vous devez configurer plusieurs serveurs pour l’équilibrage de charge ou la haute disponibilité ? Il existe plusieurs façons de définir cette configuration avec le serveur Azure MFA. Lorsque vous installez votre premier serveur Azure MFA, il devient le maître. Tous les serveurs supplémentaires deviennent ses subordonnés. Ils synchronisent automatiquement les utilisateurs ainsi que la configuration avec le serveur maître. Par la suite, vous pouvez configurer un serveur principal et conserver le reste des serveurs en tant que serveurs de sauvegarde. Vous pouvez aussi configurer l’équilibrage de charge parmi l’ensemble des serveurs. 

Lorsqu’un serveur Azure MFA maître est hors connexion, les serveurs subordonnés peuvent continuer à traiter les requêtes de vérification en deux étapes. Toutefois, vous ne pouvez pas ajouter de nouveaux utilisateurs. Quant aux utilisateurs existants, ils ne peuvent mettre à jour leurs paramètres que lorsqu’un serveur maître est à nouveau en ligne ou qu’un serveur subordonné est promu. 

<a id="prepare-your-environment" class="xliff"></a>

## Préparation de votre environnement

Assurez-vous que le serveur que vous utilisez pour l’authentification multifacteur Azure répond aux exigences suivantes :

| Configuration requise du serveur Azure Multi-Factor Authentication | Description |
|:--- |:--- |
| Matériel |<li>200 Mo d’espace disque</li><li>Processeur compatible x32 ou x64</li><li>1 Go de RAM ou plus</li> |
| Logiciel |<li>Windows Server 2008 ou version ultérieure si l’hôte est un système d’exploitation de serveur</li><li>Windows 7 ou version ultérieure si l’hôte est un système d’exploitation client</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 ou version ultérieure si vous installez le kit de développement logiciel du portail de l’utilisateur ou du service web</li> |

<a id="azure-multi-factor-authentication-server-firewall-requirements" class="xliff"></a>

### Configuration requise du serveur Azure Multi-Factor Authentication
Chaque serveur MFA doit pouvoir communiquer sur les éléments sortants du port 443, vers les adresses suivantes :

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Si les pare-feu sortants sont limités sur le port 443, ouvrez les plages d'adresses IP suivantes :

| Sous-réseau IP | Masque réseau | Plage d’adresses IP |
|:--- |:--- |:--- |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254 |

Si vous n’utilisez pas la fonctionnalité de confirmation d’événement et que les utilisateurs ne se servent pas d’applications mobiles pour effectuer des vérifications à partir des appareils sur le réseau de l’entreprise, vous avez uniquement besoin des plages suivantes :

| Sous-réseau IP | Masque réseau | Plage d’adresses IP |
|:--- |:--- |:--- |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79 |
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79 |
| 70.37.154.200/29 |255.255.255.248 |70.37.154.201 – 70.37.154.206 |

<a id="download-the-azure-multi-factor-authentication-server" class="xliff"></a>

## Téléchargement du serveur Azure Multi-Factor Authentication
Vous pouvez télécharger le serveur Azure Multi-Factor Authentication de deux manières différentes : Les deux sont exécutées via le portail Azure. La première consiste à gérer le fournisseur d'authentification multifacteur directement. La seconde utilise les paramètres du service. La seconde option requiert un fournisseur d’authentification multifacteur ou une licence Azure MFA, Azure AD Premium ou Enterprise Mobility Suite.

> [!Important]
> Ces deux options semblent similaires, mais il est important de savoir laquelle utiliser. Si vos utilisateurs disposent de licences avec MFA (Azure MFA, Azure AD Premium ou Enterprise Mobility + Security), ne créez pas de fournisseur d’authentification multifacteur pour le téléchargement du serveur. Au lieu de cela, utilisez l’option 2 pour télécharger le serveur depuis la page des paramètres du service. 

<a id="option-1-download-azure-multi-factor-authentication-server-from-the-azure-classic-portal" class="xliff"></a>

### Option 1 : télécharger le serveur Azure Multi-Factor Authentication depuis le portail Azure Classic

Utilisez cette option de téléchargement si vous disposez déjà d’un fournisseur d’authentification multifacteur, car vous payez pour MFA en fonction du nombre d’utilisateurs actifs ou d’authentifications. 

1. Connectez-vous en tant qu’administrateur au [portail Azure Classic](https://manage.windowsazure.com).
2. Sélectionnez **Active Directory**à gauche.
3. Sur la page Active Directory, cliquez sur **Fournisseurs d’authentification multifacteur** ![Fournisseurs d’authentification multifacteur](./media/multi-factor-authentication-get-started-server/authproviders.png)
4. Cliquez sur **Gérer**. Une nouvelle page s’ouvre.
5. Cliquez sur **Téléchargements**.
6. Cliquez sur le lien **Télécharger**.
   ![Télécharger](./media/multi-factor-authentication-get-started-server/download4.png)
7. Enregistrez le fichier téléchargé.

<a id="option-2-download-azure-multi-factor-authentication-server-from-the-service-settings" class="xliff"></a>

### Option 2 : télécharger le serveur Azure Multi-Factor Authentication à l’aide des paramètres du service

Utilisez cette option de téléchargement si vous disposez d’une licence Enterprise Mobility Suite, Azure AD Premium ou Enterprise Cloud Suite. 

1. Connectez-vous en tant qu’administrateur au [portail Azure Classic](https://manage.windowsazure.com).
2. Sélectionnez **Active Directory**à gauche.
3. Double-cliquez sur votre instance d’Azure AD.
4. En haut de la page, cliquez sur **Configurer**
5. Défilez vers la section **Authentification multifacteur**, puis sélectionnez **Gérer les paramètres du service**
6. Au bas de la page Paramètres du service, cliquez sur **Accéder au portail**. Une nouvelle page s’ouvre.
   ![Télécharger](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Cliquez sur **Téléchargements**
8. Cliquez sur le lien **Télécharger**.
    ![Télécharger](./media/multi-factor-authentication-get-started-server/download4.png)
9. Enregistrez le fichier téléchargé.

<a id="install-and-configure-the-azure-multi-factor-authentication-server" class="xliff"></a>

## Installation et configuration du serveur Azure Multi-Factor Authentication
Maintenant que vous avez téléchargé le serveur, vous pouvez l’installer et le configurer.  Assurez-vous que le serveur que vous installez répond aux exigences regroupées sur la liste dans la section de planification. 

Pour une installation rapide via l’Assistant de configuration, veuillez suivre ces étapes. Si vous ne voyez pas l’Assistant ou que vous souhaitez l’exécuter de nouveau, sélectionnez-le à partir du menu **Outils** sur le serveur.

1. Double-cliquez sur le fichier exécutable. 
2. Sur l'écran Sélectionner le dossier d’installation, assurez-vous que le dossier est correct, puis cliquez sur **Suivant**.
3. Une fois l'installation terminée, cliquez sur **Terminer**.  L'Assistant de configuration démarre.
4. Sur l’écran d’accueil de l’Assistant Configuration, cochez l’option **Ignorer l’Assistant Configuration de l’authentification**, puis cliquez sur **Suivant**.  La fermeture de l’Assistant intervient et le serveur démarre.
    ![Cloud](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Revenez à la page à partir de laquelle vous avez téléchargé le serveur, puis cliquez sur le bouton **Générer des informations d’identification d’activation** . Copiez ces informations dans les zones appropriées du serveur Azure MFA, puis cliquez sur **Activer**.

<a id="import-users-from-active-directory" class="xliff"></a>

## Importation des utilisateurs à partir d'Active Directory
Maintenant que le serveur est installé et configuré, vous pouvez importer très rapidement des utilisateurs sur le serveur Azure MFA.

1. Sur la gauche du serveur Azure MFA, sélectionnez **Utilisateurs**.
2. En bas de la page, sélectionnez **Importer à partir d’Active Directory**.
3. Vous pouvez désormais rechercher des utilisateurs individuels ou bien le répertoire Active des unités d'organisation, composé lui-même d’utilisateurs.  Dans ce cas, nous spécifions l'unité d'organisation des utilisateurs.
4. Mettez en surbrillance tous les utilisateurs mentionnés sur la droite, puis cliquez sur **Importer**.  Vous devriez recevoir un message vous indiquant que l’opération a été réalisée avec succès.  Fermez la fenêtre d'importation.
   ![Cloud](./media/multi-factor-authentication-get-started-server/import2.png)

<a id="send-users-an-email" class="xliff"></a>

## Envoi d’un e-mail aux utilisateurs
Maintenant que vous avez importé les utilisateurs sur le serveur MFA, envoyez un e-mail les informant qu’ils ont été inscrits pour la vérification en deux étapes.

L’e-mail que vous envoyez doit être déterminé par la façon dont vous avez configuré vos utilisateurs pour la vérification en deux étapes. Par exemple, si vous avez pu importer les numéros de téléphone à partir du répertoire de l’entreprise, l’e-mail doit inclure les numéros de téléphone par défaut afin que les utilisateurs sachent à quoi s’attendre. Si vous n’avez pas importé les numéros de téléphone ou si les utilisateurs doivent recourir à l’application mobile, envoyez-leur un e-mail leur demandant d’effectuer l’inscription de leur compte en fournissant un lien hypertexte permettant d’accéder au portail utilisateur Azure Multi-Factor Authentication.

Le contenu de l’e-mail varie en fonction de la méthode de vérification définie pour l'utilisateur (appel téléphonique, SMS ou application mobile).  Par exemple, si l'utilisateur doit saisir un code confidentiel pour s'authentifier, l’e-mail lui indique le code confidentiel initial qui a été défini.  Les utilisateurs doivent modifier leur code PIN lors de leur première vérification.


<a id="configure-email-and-email-templates" class="xliff"></a>

### Configuration du courrier électronique et des modèles de courrier électronique
Cliquez sur l'icône de courrier électronique sur la gauche pour configurer les paramètres d'envoi de ces e-mails. Cette page vous permet de saisir les informations SMTP de votre serveur de messagerie et d’envoyer des e-mails, en cochant la case **Envoyer des e-mails aux utilisateurs**.

![Paramètres de courrier électronique](./media/multi-factor-authentication-get-started-server/email1.png)

L'onglet Contenu des e-mails présente tous les modèles d’e-mail disponibles. Selon la façon dont les utilisateurs ont été configurés pour utiliser la vérification en deux étapes, vous pouvez choisir le modèle qui correspond le mieux à vos besoins.

![Email templates](./media/multi-factor-authentication-get-started-server/email2.png)

<a id="how-the-azure-multi-factor-authentication-server-handles-user-data" class="xliff"></a>

## Comment le serveur Azure Multi-Factor Authentication gère les données utilisateur
Lorsque vous utilisez le serveur Multi-Factor Authentication (MFA) sur site, les données utilisateur sont stockées sur les serveurs locaux. Aucune donnée utilisateur persistante n'est stockée dans le cloud. Lorsque l'utilisateur effectue une vérification en deux étapes, le serveur MFA envoie des données au service cloud Azure MFA pour effectuer la vérification. Lorsque ces demandes d'authentification sont envoyées au service cloud, les champs suivants sont envoyés dans la demande et les journaux afin qu'ils soient disponibles dans les rapports d'utilisation/d'authentification du client. Certains champs étant facultatifs, ils peuvent être activés ou désactivés sur le serveur Multi-Factor Authentication. La communication du serveur MFA au service cloud MFA utilise SSL/TLS sur le port 443 sortant. Ces champs sont les suivants :

* ID unique : nom d'utilisateur ou ID du serveur MFA interne
* Prénom et nom (facultatif)
* Adresse de messagerie (facultatif)
* Numéro de téléphone : en cas d'authentification par appel vocal ou SMS
* Jeton du périphérique : en cas d'authentification par application mobile
* mode d'authentification
* Résultat de l'authentification
* Nom du serveur MFA
* Adresse IP du serveur MFA
* Adresse IP du client (si disponible)

En plus des champs ci-dessus, le résultat de la vérification (réussite/échec) et le motif de refus sont également stockés avec les données d'authentification et disponibles dans les rapports d'utilisation/d'authentification.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes

- Installez et configurez le [portail utilisateur](multi-factor-authentication-get-started-portal.md) pour une utilisation en libre-service.

- Installez et configurez le serveur Azure MFA avec [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md), [l’authentification RADIUS](multi-factor-authentication-get-started-server-radius.md) ou [l’authentification LDAP](multi-factor-authentication-get-started-server-ldap.md).

- Installez et configurez la [passerelle des services Bureau à distance et Azure Multi-Factor Authentication Server avec RADIUS](multi-factor-authentication-get-started-server-rdg.md). 

- [Déployez le service web de l’application mobile du serveur Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md).

- [Scénarios avancés avec Azure Multi-Factor Authentication et des VPN tiers](multi-factor-authentication-advanced-vpn-configurations.md).

