---
title: "Intégration de Azure MFA à l’aide d’extension de serveur NPS | Documents Microsoft"
description: "Cet article décrit l’intégration de votre infrastructure VPN avec Azure MFA à l’aide de l’extension de serveur NPS (Network Policy Server) pour Microsoft Azure."
services: active-directory
keywords: "Azure MFA, intégration VPN, Azure Active Directory, extension de serveur NPS"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: kgremban
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: b47e9b321b2fd0d0db9762003531b0fe9f045f07
ms.contentlocale: fr-fr
ms.lasthandoff: 09/21/2017

---

# <a name="integrate-your-vpn-infrastructure-with-azure-multi-factor-authentication-mfa-using-the-network-policy-server-nps-extension-for-azure"></a>Intégrer votre infrastructure VPN avec Azure d’authentification multifacteur (MFA) à l’aide de l’extension de serveur NPS (Network Policy Server) pour Azure

## <a name="overview"></a>Vue d'ensemble

L’extension du Service NPS (Network Policy Server) pour Azure permet aux entreprises de protéger l’authentification du client RADIUS (Remote Authentication Dial-In User Service) à l’aide de [l’authentification multifacteur (MFA) Azure](multi-factor-authentication-get-started-server-rdg.md) qui fournit une vérification en deux étapes.

Cet article fournit des instructions pour l’intégration de l’infrastructure de serveur NPS avec Azure MFA à l’aide de l’extension de serveur NPS pour Azure pour activer la vérification en deux étapes sécurisé pour les utilisateurs qui tentent de se connecter à votre réseau à l’aide d’un réseau privé virtuel (VPN). 

Les services de stratégie et d’accès réseau (NPS) permettent aux entreprises d’effectuer les opérations suivantes :

* spécifier des emplacements centraux pour la gestion et le contrôle des demandes du réseau pour spécifier qui peut se connecter, les heures de connexion autorisées pendant la journée, la durée des connexions et le niveau de sécurité que les clients doivent utiliser pour se connecter, et autres. Plutôt que de spécifier ces stratégies sur chaque serveur VPN ou de passerelle des services Bureau à distance (RD), ces stratégies peuvent être spécifiées une seule fois dans un emplacement central. Le protocole RADIUS est utilisé pour fournir l’authentification, l’autorisation et la gestion des comptes (AAA) centralisées. 
* Établissez et appliquez les stratégies de contrôle d’intégrité client de Protection d’accès réseau (NAP) qui déterminent si les périphériques sont accordés avec ou sans restrictions d’accès aux ressources réseau.
* Fournissez un moyen d’appliquer l’authentification et l’autorisation d’accès aux points d’accès sans fil et commutateurs compatibles à 802.1x.    

Pour plus d’informations, consultez [Serveur NPS (Network Policy Server)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Pour améliorer la sécurité et fournir un niveau élevé de conformité, les entreprises peuvent intégrer NPS avec Azure MFA pour vous assurer que les utilisateurs utilisent une vérification en deux étapes pour être en mesure de se connecter au port virtuel sur le serveur VPN. Pour que les utilisateurs puissent obtenir l’accès, ils doivent fournir leur combinaison de nom d’utilisateur/mot de passe ainsi que les informations dont dispose l’utilisateur. Ces informations doivent être approuvées et pas facilement dupliquées, comme un numéro de téléphone portable, numéro de téléphone fixe, une application sur un appareil mobile et autres.

Avant la disponibilité de l’extension de serveur NPS pour Azure, les clients qui souhaitaient mettre en œuvre la vérification en deux étapes pour les environnements de serveur NPS et l’authentification multifacteur Azure intégrées devaient configurer et gérer un serveur distinct de l’authentification multifacteur dans l’environnement local, comme décrit dans la rubrique Passerelle des services Bureau à distance et serveur Azure MFA utilisant RADIUS.

La disponibilité de l’extension de serveur NPS pour Azure permet désormais aux entreprises de choisir de déployer une solution d’authentification multifacteur locale ou une solution de l’authentification multifacteur basée sur le cloud pour l’authentification client RADIUS sécurisée.
 
## <a name="authentication-flow"></a>Flux d’authentification
Lorsqu’un utilisateur se connecte à un port virtuel sur un serveur VPN, il doit tout d’abord s’authentifier à l’aide d’une variété de protocoles, ce qui permet l’utilisation d’une combinaison de nom d’utilisateur/mot de passe et des méthodes d’authentification par certificat. 

En plus de l’authentification et de la vérification d’identité, les utilisateurs doivent disposer des autorisations d’accès appropriées. Dans des implémentations simples, ces autorisations d’accès à distance qui autorisent l’accès sont définies directement sur les objets d’utilisateur Active Directory. 

 ![Propriétés de l’utilisateur](./media/nps-extension-vpn/image1.png)

Pour des implémentations simples, chaque serveur VPN autorise ou refuse l’accès en fonction des stratégies définies sur chaque serveur VPN local.

Dans les mises en œuvre plus grandes et plus évolutives, les stratégies qui accordent ou refusent l’accès VPN sont centralisées sur les serveurs RADIUS. Dans ce cas, le serveur VPN agit comme un serveur d’accès (client RADIUS) qui transfère des demandes de connexion et des messages de compte à un serveur RADIUS. Pour se connecter au port virtuel sur le serveur VPN, les utilisateurs doivent être authentifiés et répondre aux conditions définies de manière centralisée sur les serveurs RADIUS. 

Lorsque l’extension de serveur NPS pour Azure est intégrée au serveur NPS, le flux d’authentification réussie est le suivant :

1. Le serveur VPN reçoit une demande d’authentification à partir d’un utilisateur VPN qui inclut le nom d’utilisateur et le mot de passe pour se connecter à une ressource, par exemple une session Bureau à distance. 
2. Agissant comme un client RADIUS, le serveur VPN convertit la demande en un message de demande d’accès RADIUS et envoie le message (le message est chiffré) au serveur RADIUS (NPS) où est installée l’extension du serveur NPS. 
3. La combinaison nom d’utilisateur et mot de passe est vérifiée dans Active Directory. Si le nom d’utilisateur / mot de passe est incorrect, le serveur RADIUS envoie un message de refus d’accès. 
4. Si toutes les conditions, comme spécifié dans la demande de connexion du serveur NPS et les stratégies de réseau, sont remplies (par exemple, les restrictions d’heure de la journée ou de groupe d’appartenance), l’extension NPS déclenche une demande d’authentification secondaire avec Azure MFA. 
5. Azure MFA communique avec Azure Active Directory pour récupérer les informations de l’utilisateur et procède à l’authentification secondaire à l’aide d’une méthode configurée par l’utilisateur (message texte, application mobile, etc.). 
6. En cas de réussite du défi MFA, Azure MFA communique le résultat sur l’extension du serveur NPS.
7. Après que la tentative de connexion soit à la fois authentifiée et autorisée, le serveur NPS où l’extension est installée envoie un message d’acceptation d’accès RADIUS au serveur VPN (client RADIUS).
8. L’utilisateur a accès au port virtuel sur le serveur VPN et établit un tunnel VPN chiffré.

## <a name="prerequisites"></a>Composants requis
Cette section détaille les conditions préalables nécessaires avant d’intégrer Azure MFA à la passerelle des services Bureau à distance. Avant de commencer, vous devez disposer des conditions requises en place suivantes.

* Infrastructure VPN
* Rôle des services de stratégie et d’accès réseau (NPS)
* Licence Azure MFA
* Logiciel Windows Server
* Bibliothèques
* Azure AD synchronisé avec AD local 
* ID du GUID Azure Active Directory

### <a name="vpn-infrastructure"></a>Infrastructure VPN
Cet article suppose que vous disposez d’une infrastructure VPN de travail avec Microsoft Windows Server 2016 en place et que le serveur VPN n’est actuellement pas configuré pour transférer les demandes de connexion directe à un serveur RADIUS. Configurez l’infrastructure VPN pour utiliser un serveur RADIUS central dans ce guide.

Si vous ne disposez pas d’une infrastructure de travail en place, vous pouvez rapidement créer cette infrastructure en suivant les instructions fournies dans les nombreux didacticiels du programme d’installation VPN que vous pouvez rechercher sur le Microsoft et des sites tiers. 

### <a name="network-policy-and-access-services-nps-role"></a>Rôle des services de stratégie et d’accès réseau (NPS)

Le service de rôle NPS fournit la fonctionnalité de client et le serveur RADIUS. Cet article suppose que vous avez installé le rôle NPS sur un serveur membre ou un contrôleur de domaine dans votre environnement. Configurez le RADIUS pour une configuration de VPN dans ce guide. Installez le rôle NPS sur un serveur _autre_ que votre serveur VPN.

Pour plus d’informations sur l’installation du service de rôle de serveur NPS Windows Server 2012 ou versions plus ancienne, consultez [Installer un serveur de stratégie de contrôle d’intégrité NAP](https://technet.microsoft.com/library/dd296890.aspx). La stratégie d’accès réseau (NAP) est déconseillée dans Windows Server 2016. Pour obtenir une description des meilleures pratiques pour le serveur NPS, y compris la recommandation pour installer le serveur NPS sur un contrôleur de domaine, consultez [Meilleures pratiques pour le serveur NPS](https://technet.microsoft.com/library/cc771746).

### <a name="licenses"></a>Licences

Une licence pour Azure MFA est obligatoire, disponible via un abonnement Azure AD Premium, Enterprise Mobility plus Security (EMS) ou MFA. Pour plus d’informations, consultez [Guide pratique pour obtenir l’authentification multifacteur Azure](multi-factor-authentication-versions-plans.md). À des fins de test, vous pouvez utiliser un abonnement d’évaluation.

### <a name="software"></a>Logiciel

L’extension de serveur NPS requiert Windows Server 2008 R2 SP1 ou version ultérieure avec le service de rôle NPS installé. Toutes les étapes de ce guide ont été effectuées à l’aide de Windows Server 2016.

### <a name="libraries"></a>Bibliothèques

Ces bibliothèques sont installées automatiquement avec l’extension.

-   [Visual C++ Redistributable Packages pour Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Module Microsoft Azure Active Directory pour Windows PowerShell version 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Le module Microsoft Azure Active Directory pour Windows PowerShell est installé, s’il n’est pas déjà présent, via un script de configuration que vous exécutez en tant que partie du processus de configuration. Il est inutile d’installer ce module en avance s’il n’est pas déjà installé.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory synchronisé avec Active Directory local 

Pour utiliser l’extension de serveur NPS, les utilisateurs locaux doivent être synchronisés avec Azure Active Directory et activés pour l’authentification multifacteur. Ce guide part du principe que les utilisateurs locaux sont synchronisés avec Azure Active Directory en utilisant AD Connect. Vous trouverez ci-dessous des instructions pour fournir aux utilisateurs l’authentification multifacteur.
Pour obtenir plus d’informations sur Azure AD Connect, consultez [Intégrer vos répertoires locaux avec Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>ID du GUID Azure Active Directory 
Pour installer le serveur NPS, vous devez connaître le GUID d’Azure Active Directory. Instructions pour rechercher le GUID d’Azure Active Directory sont fournies dans la section suivante.

## <a name="configure-radius-for-vpn-connections"></a>Configurer RADIUS pour les connexions VPN

Si vous avez installé le rôle de serveur NPS sur un serveur membre, vous devez le configurer pour authentifier et autoriser les clients VPN qui demande des connexions VPN. 

Cette section part du principe que vous avez installé le rôle de serveur de stratégie réseau mais que vous ne l'avez pas configuré pour une utilisation dans votre infrastructure.

>[!NOTE]
>Si vous disposez déjà d’un serveur VPN de travail qui utilise un serveur RADIUS centralisé pour l’authentification, vous pouvez ignorer cette section.
>

### <a name="register-server-in-active-directory"></a>Enregistrer le serveur dans Active Directory
Pour fonctionner correctement dans ce scénario, le serveur NPS doit être enregistré dans Active Directory.

1. Ouvrez le Gestionnaire de serveurs.
2. Dans Gestionnaire de serveurs, cliquez sur **Outils** puis cliquez sur **Serveur de stratégie réseau**. 
3. Dans la console NPS, cliquez avec le bouton droit sur **NPS (Local)**, puis cliquez sur **Enregistrer un serveur dans Active Directory**. Cliquez deux fois sur **OK**.

 ![Serveur de stratégie réseau](./media/nps-extension-vpn/image2.png)

4. Laissez la console ouverte pour la procédure suivante.

### <a name="use-wizard-to-configure-radius-server"></a>Utilisez l’Assistant pour configurer un serveur RADIUS
Vous pouvez utiliser un standard (basée sur l’Assistant) ou l’option de configuration avancée pour configurer le serveur RADIUS. Cette section suppose l’utilisation de l’option de configuration standard basée sur l’Assistant.

1. Dans la console NPS, cliquez sur **NPS (Local)**.
2. Sous Configuration standard, sélectionnez **Serveur RADIUS pour l’accès à distance ou les connexions VPN**, puis cliquez sur **Configurer une connexion VPN ou accès à distance**.

 ![Configurer VPN](./media/nps-extension-vpn/image3.png)

3. Dans la page Sélectionnez les accès à distance ou le type de connexions de réseau privé virtuel, sélectionnez **Connexions de réseau privé virtuel**, puis cliquez sur **Suivant**.

 ![Réseau privé virtuel](./media/nps-extension-vpn/image4.png)

4. Dans la page Spécifier l’accès à distance ou de serveur VPN, cliquez sur **Ajouter**.
5. Dans la boîte de dialogue **Nouveau client RADIUS**, fournissez un nom convivial, entrez le nom pouvant être résolu ou l’adresse IP du serveur VPN et entrez un mot de passe secret partagé. Créez un mot de passe secret partagé long et complexe. Enregistrez ce mot de passe, le cas échéant pour les étapes de la section suivante.

 ![Nouveau client RADIUS](./media/nps-extension-vpn/image5.png)

6. Cliquez sur **OK**, puis sur **Suivant**.
7. Sur la page **Configurer des méthodes d’authentification**, acceptez la sélection par défaut (Authentification chiffrée Microsoft version 2 (MS-CHAPv2) ou choisissez une autre option, puis cliquez sur **Suivant**.

  >[!NOTE]
  >Si vous configurez le protocole EAP (Extensible Authentication Protocol), vous devez utiliser MS CHAPv2 ou PEAP. Aucun autre EAP n’est pris en charge.
 
8. Dans la page Spécifier des groupes d’utilisateurs, cliquez sur **Ajouter** et sélectionnez un groupe approprié, le cas échéant. Sinon, laissez le champ vide pour accorder l’accès à tous les utilisateurs.

 ![Spécifier des groupes d’utilisateurs](./media/nps-extension-vpn/image7.png)

9. Cliquez sur **Suivant**.
10. Dans la page Spécifier des filtres IP, cliquez sur **Suivant**.
11. Dans la page Spécifier les paramètres de chiffrement, acceptez les paramètres par défaut, puis cliquez sur **Suivant**.

 ![Spécifier le chiffrement](./media/nps-extension-vpn/image8.png)

12. Dans la page Spécifier un nom de domaine, laissez le nom vide, acceptez le paramètre par défaut et cliquez sur **Suivant**.

 ![Spécifier un nom de domaine](./media/nps-extension-vpn/image9.png)

13. Dans la page Exécution un nouvel accès à distance ou Connexions réseau privé et clients RADIUS cliquez sur **Terminer**.

 ![Finaliser des connexions](./media/nps-extension-vpn/image10.png)

### <a name="verify-radius-configuration"></a>Vérifier la configuration RADIUS
Cette section décrit en détail la configuration que vous avez créé à l’aide de l’Assistant.

1. Sur le serveur NPS, dans la console NPS (Local), développez les clients RADIUS et sélectionnez **Clients RADIUS**.
2. Dans le volet de détails, cliquez sur le client RADIUS que vous avez créé à l’aide de l’Assistant, puis cliquez sur **Propriétés**. Les propriétés de votre client RADIUS (le serveur VPN) doivent être semblables à celles présentées ci-dessous.

 ![Properties VPN](./media/nps-extension-vpn/image11.png)

3. Cliquez sur **Annuler**.
4. Sur le serveur NPS, dans la console NPS (Local), développez **Stratégies**, puis sélectionnez **Stratégies de demande de connexion**. Vous devez voir la stratégie de Connexions VPN qui ressemble à l’image ci-dessous.

 ![Demandes de connexion](./media/nps-extension-vpn/image12.png)

5. Sous stratégies, sélectionnez **Stratégies réseau**. Vous devez voir une stratégie de Connexions VPN (réseau privé virtuel) qui ressemble à l’image ci-dessous.

 ![Propriétés du réseau](./media/nps-extension-vpn/image13.png)

## <a name="configure-vpn-server-to-use-radius-authentication"></a>Configurez le serveur VPN pour utiliser l’authentification RADIUS
Dans cette section, configurez le serveur VPN pour utiliser l’authentification RADIUS. Cette section part du principe que vous disposez d’une configuration de l’utilisation du serveur VPN mais que vous n’avez pas configuré le serveur VPN pour utiliser l’authentification RADIUS. Après avoir configuré le serveur VPN, confirmez que votre configuration fonctionne comme prévu.

>[!NOTE]
>Si vous disposez déjà d’une configuration de serveur VPN de travail qui utilise l’authentification RADIUS, vous pouvez ignorer cette section.
>

### <a name="configure-authentication-provider"></a>Configurer le fournisseur d’authentification
1. Sur le serveur VPN, ouvrez Gestionnaire de serveurs.
2. Dans le Gestionnaire de serveur, cliquez sur **Outils**, puis sur **Routage et accès à distance**.
3. Dans la console Routage et accès à distance, cliquez avec le bouton droit sur **\[Nom du serveur\] (local)**, puis cliquez sur **Propriétés**.

 ![Routage et d’accès à distance](./media/nps-extension-vpn/image14.png)
 
4. Dans la boîte de dialogue **[Server Name} (local) Propriétés**, cliquez sur l’onglet **Sécurité**. 
5. Sur l’onglet **Sécurité**, sous Fournisseur d’authentification, cliquez sur **Authentification RADIUS**, puis sur **Configurer**.

 ![Authentification RADIUS](./media/nps-extension-vpn/image15.png)
 
6. Dans la boîte de dialogue Authentification RADIUS, cliquez sur **Ajouter**.
7. Dans Ajouter un serveur RADIUS, dans Nom du serveur, ajoutez le nom ou l’adresse IP du serveur RADIUS que vous avez configuré dans la section précédente.
8. Dans Secret partagé, cliquez sur **Modifier** et ajoutez le mot de passe secret partagé créé et enregistré précédemment.
9. Dans Délai d’attente (secondes), modifiez la valeur pour une valeur comprise entre **30** et **60**. Il est nécessaire de prévoir un délai suffisant pour exécuter le second facteur d’authentification.
 
 ![Ajouter un serveur RADIUS](./media/nps-extension-vpn/image16.png)
 
10. Cliquez sur **OK** jusqu'à la fermeture de toutes les boîtes de dialogue.

### <a name="test-vpn-connectivity"></a>Connectivité VPN de test
Dans cette section, confirmez que le client VPN est authentifié et autorisé par le serveur RADIUS lorsque vous essayez de vous connecter à un port virtuel VPN. Cette section part du principe que vous utilisez Windows 10 comme un client VPN. 

>[!NOTE]
>Si vous avez déjà configuré un client VPN pour vous connecter au serveur VPN et avez enregistré les paramètres, vous pouvez ignorer les étapes relatives à la configuration et à l’enregistrement d’un objet de connexion VPN.
>

1. Sur votre ordinateur client VPN, cliquez sur **Démarrer**, puis sur**Paramètres** (icône d’engrenage).
2. Dans Paramètres de la fenêtre, cliquez sur **Réseau et Internet**.
3. Cliquez sur **VPN**.
4. Cliquez sur **Ajouter une connexion VPN**.
5. Dans Ajouter une connexion VPN, spécifiez Windows (intégré) en tant que fournisseur VPN, puis renseignez les champs restants, selon le cas et cliquez sur **Enregistrer**. 

 ![Ajouter une connexion VPN](./media/nps-extension-vpn/image17.png)
 
6. Ouvrez le **Centre réseau et partage** dans le panneau de configuration.
7. Cliquez sur **Modifier les paramètres de l’adaptateur**.

 ![Modifier les paramètres de l’adaptateur](./media/nps-extension-vpn/image18.png)

8. Cliquez avec le bouton droit sur la connexion réseau VPN, puis cliquez sur Propriétés. 

 ![Propriétés du réseau VPN](./media/nps-extension-vpn/image19.png)

9. Dans la boîte de dialogue des propriétés VPN, cliquez sur l’onglet **Sécurité**. 
10. Dans l’onglet Sécurité, assurez-vous que seul **Microsoft CHAP Version 2 (MS-CHAP v2)** est sélectionné, puis cliquez sur OK.

 ![Autoriser les protocoles](./media/nps-extension-vpn/image20.png)

11. Cliquez avec le bouton droit sur la connexion VPN, puis cliquez sur **Connecter**.
12. Dans la page Paramètres, cliquez sur **Connecter**.

Un message de connexion réussie s’affiche dans le journal de sécurité sur le serveur RADIUS en tant qu’ID d’événement 6272, comme illustré ci-dessous.

 ![Propriétés de l’événement](./media/nps-extension-vpn/image21.png)

## <a name="troubleshoot-guide"></a>Guide de résolution des problèmes
Supposons que votre configuration VPN fonctionnait avant que vous ayez configuré le serveur VPN pour utiliser un serveur RADIUS centralisé pour l’authentification et l’autorisation. Dans ce cas, il est probable que le problème puisse être provoqué par une configuration incorrecte du serveur RADIUS ou l’utilisation d’un nom d’utilisateur ou d’un mot de passe non valide. Par exemple, si vous utilisez l’autre suffixe UPN dans le nom d’utilisateur, la tentative de connexion peut échouer (vous devez utiliser le même nom de compte pour de meilleurs résultats). 

Pour résoudre ces problèmes, l’idéal est d’examiner les journaux des événements de sécurité sur le serveur RADIUS. Pour gagner du temps dans la recherche des événements, vous pouvez utiliser une vue personnalisée de stratégie et d’accès réseau basée sur les rôles dans l’Observateur d’événements, comme indiqué ci-dessous. L’ID d’événement 6273 indique les événements dans lesquels le serveur de stratégie réseau refuse l’accès à un utilisateur. 

 ![Observateur d’événements](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Configurer l’authentification multifacteur
Cette section fournit des instructions pour activer les utilisateurs pour l’authentification multifacteur et de configurer des comptes pour la vérification en deux étapes. 

### <a name="enable-multi-factor-authentication"></a>Activer l’authentification multifacteur
Dans cette section, activez des comptes Azure AD pour l’authentification multifacteur. Utilisez le **portail classique** pour activer les utilisateurs pour l’authentification multifacteur. 

1. Ouvrez un navigateur et accédez à [https://manage.windowsazure.com/](https://manage.windowsazure.com). 
2. Connectez-vous en tant qu’administrateur.
3. Dans le volet de navigation de gauche du portail, cliquez sur **ACTIVE DIRECTORY**.

 ![Répertoire par défaut](./media/nps-extension-vpn/image23.png)

4. Dans la colonne NOM, cliquez sur **Répertoire par défaut** (ou un autre répertoire, le cas échéant).
5. Dans la page Démarrage rapide, cliquez sur **Configurer**.

 ![Configuration par défaut](./media/nps-extension-vpn/image24.png)

6. Dans la section CONFIGURER, faites défiler le menu vers le bas et dans la section authentification multifacteur, cliquez sur **Gérer les paramètres du service**.

 ![Gérer les paramètres de l’authentification multifacteur](./media/nps-extension-vpn/image25.png)
 
7. Dans la page de l’authentification multifacteur, passez en revue les paramètres de service par défaut, puis cliquez sur **Utilisateurs**. 

 ![Utilisateurs MFA](./media/nps-extension-vpn/image26.png)
 
8. Dans la page Utilisateurs, sélectionnez les utilisateurs que vous souhaitez activer pour l’authentification multifacteur, puis cliquez sur **Activer**.

 ![Propriétés](./media/nps-extension-vpn/image27.png)
 
9. Lorsque vous y êtes invité, cliquez sur **Activer l’authentification multifacteur**.

 ![Activer la MFA](./media/nps-extension-vpn/image28.png)
 
10. Cliquez sur **Fermer**. 
11. Actualisez la page. L’état de l’authentification multifacteur est modifié pour Activé.

Pour plus d’informations sur comment activer les utilisateurs pour l’authentification multifacteur, consultez [Bien démarrer avec Azure Multi-Factor Authentication dans le cloud](multi-factor-authentication-get-started-cloud.md). 

### <a name="configure-accounts-for-two-step-verification"></a>Configurer des comptes pour la vérification en deux étapes
Une fois qu’un compte a été activé pour l’authentification multifacteur, vous ne pouvez pas vous connecter aux ressources régies par la stratégie d’authentification multifacteur jusqu'à ce que vous ayez correctement configuré un périphérique approuvé à utiliser pour que le second facteur d’authentification ait utilisé la vérification en deux étapes.

Dans cette section, configurez un périphérique approuvé pour une utilisation avec la vérification en deux étapes. Plusieurs options sont disponibles pour vous permettre de configurer ces options, notamment les suivantes :

* **Application mobile**. Installez l’application Microsoft Authenticator sur un appareil Windows Phone, Android ou iOS. Selon les stratégies de votre organisation, vous devez utiliser l’application dans un des deux modes : Recevoir des notifications de vérifications (une notification est envoyée à votre appareil) ou Utiliser le code de vérification (vous êtes invité à entrer un code de vérification mis à jour toutes les 30 secondes). 
* **Appel ou SMS sur téléphone mobile**. Vous pouvez recevoir un appel téléphonique automatisé ou un message texte. Avec l’option appel téléphonique, vous répondez à l’appel et appuyez sur le signe # pour vous authentifier. Avec l’option texte, vous pouvez répondre au SMS ou entrer le code de vérification fourni dans l’interface de connexion.
* **Appel téléphonique au bureau**. Ce processus est le même que celui décrit pour les appels téléphoniques automatisés ci-dessus.

Suivez ces instructions pour configurer un appareil pour utiliser l’application mobile et recevoir une notification Push pour vérification.

1. Connectez-vous sur [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou n’importe quel site, tel que [https://portal.azure.com](https://portal.azure.com), où vous devez vous authentifier à l’aide de vos informations d’identification de l’activation par authentification multifacteur. 
2. Lors de la connexion avec votre nom d’utilisateur et votre mot de passe, un écran vous invite à configurer le compte pour une vérification de sécurité supplémentaire.

 ![Sécurité supplémentaire](./media/nps-extension-vpn/image29.png)

3. Cliquez sur **Configurer maintenant**.
4. Dans la page Vérification de la sécurité supplémentaire, sélectionnez un type de contact (téléphone d’authentification, téléphone de bureau ou application mobile). Puis sélectionnez un pays ou une région et une méthode. La méthode varie en fonction du type de contact sélectionné. Par exemple, si vous choisissez Application mobile, vous pouvez choisir de recevoir des notifications pour la vérification ou utiliser un code de vérification. Les étapes suivantes supposent que vous choisissez **Application Mobile** en tant que type de contact.

 ![Authentification par téléphone](./media/nps-extension-vpn/image30.png)

5. Sélectionnez Application Mobile, cliquez sur **Recevoir des notifications pour la vérification**, puis sur **Configurer**. 

 ![Vérification par l’application mobile](./media/nps-extension-vpn/image31.png)
 
6. Si vous ne l’avez pas déjà fait, installez l’application mobile authentificateur sur votre appareil. 
7. Suivez les instructions de l’application mobile pour numériser le code-barres présenté ou entrer manuellement les informations, puis cliquez sur **Terminé**.

 ![Configurer l’application mobile](./media/nps-extension-vpn/image32.png)

8. Dans la page Vérification de la sécurité supplémentaire, cliquez sur **Contactez-moi** et répondez à la notification envoyée sur votre appareil.
9. Dans la page Vérification de la sécurité supplémentaire, entrez un numéro de contact au cas où vous perdriez l’accès à l’application mobile, puis cliquez sur **Suivant**.

 ![Numéro de téléphone mobile](./media/nps-extension-vpn/image33.png)
 
10. Dans la page Vérification de la sécurité supplémentaire, cliquez sur **Terminé**.

L’appareil est désormais configuré pour offrir une deuxième méthode de vérification. Pour plus d’informations sur la configuration des comptes pour la vérification en deux étapes, consultez [Configurer mon compte pour la vérification en deux étapes](./end-user/multi-factor-authentication-end-user-first-time.md).

## <a name="install-and-configure-nps-extension"></a>Installer et configurer l’extension NPS

Cette section fournit des instructions pour la configuration VPN pour utiliser Azure MFA pour l’authentification du client avec le serveur VPN.

Une fois l’extension de serveur NPS installée et configurée, toutes les authentifications du client RADIUS traitées par ce serveur doivent obligatoirement utiliser Azure MFA. Si tous vos utilisateurs VPN sont inscrits dans Azure MFA, vous pouvez configurer un autre serveur RADIUS pour authentifier les utilisateurs qui ne sont pas configurés pour utiliser l’authentification multifacteur. Ou bien, vous pouvez créer une entrée de registre qui permet aux utilisateurs testés de fournir un second facteur d’authentification, uniquement si ils sont inscrits à l’authentification multifacteur. 

Créez une nouvelle valeur de chaîne nommée _REQUIRE_USER_MATCH in HKLM\SOFTWARE\Microsoft\AzureMfa_ et définissez la valeur sur TRUE ou FALSE. 

 ![Exiger une correspondance d’utilisateur](./media/nps-extension-vpn/image34.png)
 
Si la valeur est définie sur TRUE ou pas définie, toutes les demandes d’authentification sont soumises à un défi d’authentification multifacteur. Si la valeur est définie sur FALSE, les défis de l’authentification multifacteur sont émis uniquement aux utilisateurs qui sont inscrits à l’authentification multifacteur. Utilisez le paramètre FALSE dans le test ou dans les environnements de production pendant une période d’intégration.

### <a name="acquire-azure-active-directory-guid-id"></a>Acquérir l’ID du GUID Azure Active Directory

Dans le cadre de la configuration de l’extension de serveur NPS, vous devez fournir les informations d’identification d’administrateur et l’ID Azure Active Directory pour votre client Azure AD. Les étapes suivantes vous expliquent comment obtenir l’ID client.

1. Connectez-vous au portail Azure sur le site [https://portal.azure.com](https://portal.azure.com) en tant qu’administrateur global du client Azure.
2. Dans la barre de navigation de gauche, sélectionnez l’icône **Azure Active Directory**.
3. Cliquez sur **Propriétés**.
4. Pour copier votre ID de répertoire dans le presse-papiers, sélectionnez l’icône **Copier**.
 
 ![ID du répertoire](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Installer l’extension NPS
L’extension de serveur NPS doit être installée sur un serveur doté du rôle des services de stratégie et d’accès réseau (NPS) installé et qui fonctionne comme serveur RADIUS dans votre conception. N’installez pas l’extension de serveur NPS sur votre serveur Bureau à distance.

1. Téléchargez l’extension de serveur NPS à partir de [https://aka.ms/npsmfa](https://aka.ms/npsmfa). 
2. Copiez le fichier exécutable du programme d’installation (NpsExtnForAzureMfaInstaller.exe) sur le serveur NPS.
3. Sur le serveur NPS, double-cliquez sur **NpsExtnForAzureMfaInstaller.exe**. À l’invite, cliquez sur **Exécuter**.
4. Dans l’extension de serveur NPS pour la boîte de dialogue d’Azure MFA, passez en revue les termes du contrat de licence de logiciel, cochez la case **J’accepte les conditions générales du contrat de licence**, puis cliquez sur **Installer**.

 ![Extension NPS](./media/nps-extension-vpn/image36.png)
 
5. Dans l’extension de serveur NPS pour la boîte de dialogue d’Azure MFA, cliquez sur **Fermer**.  

 ![Configuration réussie](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configurer des certificats pour une utilisation avec l’extension de serveur NPS à l’aide d’un script PowerShell
Pour garantir des communications et une assurance sécurisées, vous devez configurer des certificats à utiliser par l’extension NPS. Les composants de serveur NPS incluent un script Windows PowerShell qui configure un certificat auto-signé à utiliser avec le serveur NPS. 

Le script effectue les actions suivantes :

* crée un certificat auto-signé
* associe la clé publique du certificat au principal du service sur Azure AD
* stocke le certificat dans le Store de l’ordinateur local
* accorde l’accès à la clé privée du certificat à l’utilisateur réseau
* redémarre le service de serveur de stratégie réseau

Si vous souhaitez utiliser vos propres certificats, vous devez associer le public de votre certificat au principe de service sur Azure AD et ainsi de suite.
Pour utiliser le script, spécifiez l’extension avec vos informations d’identification administrateur Azure Active Directory et l’ID du client Azure Active Directory que vous avez copié précédemment. Exécutez le script sur chaque serveur NPS où vous avez installé l’extension du serveur NPS.

1. Ouvrez une invite administrative Windows PowerShell.
2. À l’invite de PowerShell, tapez _cd ‘c:\Program Files\Microsoft\AzureMfa\Config’_, puis appuyez sur **ENTRÉE**.
3. Tapez _.\AzureMfsNpsExtnConfigSetup.ps1_, puis appuyez sur **ENTRÉE**. 
 * Le script vérifie si le module Azure Active Directory PowerShell est installé. Si ce n’est pas le cas, le script installe le module pour vous.
 
 ![PowerShell](./media/nps-extension-vpn/image38.png)
 
4. Une fois la vérification de l’installation du module PowerShell par le script, il affiche la boîte de dialogue du module Azure Active Directory PowerShell. Dans la boîte de dialogue, entrez vos informations d’identification d’administrateur Azure AD et un mot de passe, puis cliquez sur **Connexion**. 
 
 ![Connexion PowerShell](./media/nps-extension-vpn/image39.png)
 
5. Lorsque vous y êtes invité, collez l’ID client que vous avez copié précédemment dans le presse-papiers, puis appuyez sur **ENTRÉE**. 

 ![ID client](./media/nps-extension-vpn/image40.png)

6. Le script crée un certificat auto-signé et effectue d’autres modifications de configuration. La sortie doit ressembler à l’image ci-dessous.

 ![Certificat auto-signé](./media/nps-extension-vpn/image41.png)

7. Redémarrez le serveur.
 
### <a name="verify-configuration"></a>Vérifier la configuration
Pour vérifier la configuration, vous devez établir une nouvelle connexion VPN avec le serveur VPN. Lors de l’entrée avec succès vos informations d’identification pour l’authentification principale, la connexion VPN attend que l’authentification secondaire réussisse avant que la connexion soit établie, comme indiqué ci-dessous. 

 ![Vérifier la configuration](./media/nps-extension-vpn/image42.png)

Si vous vous authentifiez correctement avec la méthode de vérification secondaire que vous avez configurée précédemment dans Azure MFA, vous êtes connecté à la ressource. Toutefois, si l’authentification secondaire ne réussit pas, l’accès à la ressource vous est refusé. 

Dans l’exemple ci-dessous, l’application d’authentification sur un téléphone Windows est utilisée pour fournir l’authentification secondaire.

 ![Vérifier le compte](./media/nps-extension-vpn/image43.png)

Une fois correctement authentifié à l’aide de la méthode secondaire, vous obtenez l’accès au port virtuel sur le serveur VPN. Toutefois, étant donné que vous devez utiliser une méthode d’authentification secondaire à l’aide d’une application mobile sur un appareil approuvé, le processus de connexion est plus sûr qu’avec la seule combinaison nom d’utilisateur / mot de passe.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Afficher les journaux de l’Observateur d’événements pour les événements de connexion réussie
Pour afficher les événements de connexion réussie dans les journaux de l’Observateur d’événements Windows, vous pouvez émettre la commande Windows PowerShell suivante pour interroger le journal de sécurité Windows sur le serveur NPS.

Pour interroger les événements de connexion réussie dans les journaux de l’observateur d’événements de sécurité, utilisez la commande suivante,
* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

 ![Observateur d’événements de sécurité](./media/nps-extension-vpn/image44.png)
 
Vous pouvez également afficher le journal de sécurité ou la vue personnalisée des services de stratégie et d’accès réseau, comme indiqué ci-dessous :

 ![Accès à la stratégie réseau](./media/nps-extension-vpn/image45.png)

Sur le serveur où vous avez installé l’extension de serveur NPS pour Azure MFA, vous pouvez rechercher les journaux d’application de l’Observateur d’événements spécifiques à l’extension sur **Journaux d’applications et services\Microsoft\AzureMfa**. 

* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

 ![Nombre d'événements](./media/nps-extension-vpn/image46.png)

## <a name="troubleshoot-guide"></a>Guide de résolution des problèmes
Si la configuration ne fonctionne pas comme prévu, la première chose à faire pour résoudre les problèmes consiste à vérifier que l’utilisateur est configuré pour utiliser Azure MFA. Inviter l’utilisateur à se connecter sur [https://portal.azure.com](https://portal.azure.com). S’ils sont invités à une vérification secondaire et peuvent correctement s’authentifier, vous pouvez éliminer une configuration incorrecte d’Azure MFA.

Si Azure MFA fonctionne pour le ou les utilisateurs, vous devez examiner les journaux des événements pertinents. Citons notamment les journaux des événements de sécurité, de la passerelle opérationnelle et Azure MFA qui sont décrits dans la section précédente. 

Voici un exemple de sortie du journal de sécurité montrant un échec d’événement de connexion (ID d’événement 6273) :

 ![Journal de sécurité](./media/nps-extension-vpn/image47.png)

Voici un événement associé aux journaux AzureMFA :

 ![Journaux Azure MFA](./media/nps-extension-vpn/image48.png)

Pour exécuter des options de résolution des problèmes avancée, consultez les fichiers journaux au format base de données de serveur NPS dans lesquels est installé le service NPS. Ces fichiers journaux sont créés dans le dossier _%SystemRoot%\System32\Logs_ comme fichiers texte délimité par des virgules. Pour obtenir une description de ces fichiers journaux, consultez [Interpréter des fichiers journaux au format base de données de serveur NPS](https://technet.microsoft.com/library/cc771748.aspx). 

Les entrées de ces fichiers journaux sont difficiles à interpréter sans les importer dans une feuille de calcul ou une base de données. Vous pouvez rechercher plusieurs analyseurs IAS en ligne pour vous aider à interpréter les fichiers journaux. Voici ci-dessous la sortie d’une de ces [applications de logiciel à contribution volontaire](http://www.deepsoftware.com/iasviewer) téléchargeable : 

 ![Application de logiciel à contribution volontaire](./media/nps-extension-vpn/image49.png)

Enfin, pour obtenir des options de résolution des problèmes supplémentaires, vous pouvez utiliser un analyseur de protocoles, tel que Wireshark ou [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). L’image suivante à partir de Wireshark affiche les messages RADIUS entre le serveur VPN et le serveur NPS.

 ![Microsoft Message Analyzer](./media/nps-extension-vpn/image50.png)

Pour plus d’informations, consultez [Intégrer votre infrastructure NPS existante à Azure Multi-Factor Authentication](multi-factor-authentication-nps-extension.md).  

## <a name="next-steps"></a>Étapes suivantes
[Comment obtenir Azure Multi-Factor Authentication ?](multi-factor-authentication-versions-plans.md)

[Passerelle des services Bureau à distance et serveur Multi-Factor Authentication avec RADIUS](multi-factor-authentication-get-started-server-rdg.md)

[Intégrer vos répertoires locaux à Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)


