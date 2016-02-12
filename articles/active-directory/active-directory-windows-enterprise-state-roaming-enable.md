<properties
    pageTitle="Activer Enterprise State Roaming dans Azure Active Directory | Microsoft Azure"
    description="Foire aux questions sur les paramètres Enterprise State Roaming sur les appareils Windows. Enterprise State Roaming fournit aux utilisateurs une expérience unifiée sur leurs appareils Windows et réduit le temps nécessaire à la configuration d’un nouveau périphérique."
    services="active-directory"
    keywords="enterprise state roaming, cloud windows, comment activer enterprise state roaming"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
    ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

 

# Activer Enterprise State Roaming dans Azure Active Directory

Enterprise State Roaming est disponible pour toute organisation ayant souscrit un abonnement premium à Azure Active Directory (Azure AD). Pour plus d’informations sur l’obtention d’un abonnement Azure AD, consultez la [page produit Azure AD](https://azure.microsoft.com/services/active-directory).

Lorsque vous activez Enterprise State Roaming, votre organisation reçoit automatiquement les licences pour un abonnement gratuit à Azure Rights Management. Cet abonnement gratuit est limité au chiffrage et au déchiffrage des données de paramètres d’entreprise uniquement ; pour utiliser toutes les fonctionnalités d’Azure Rights Management, vous devez souscrire un abonnement payant.

Après avoir obtenu un abonnement à Azure AD, procédez comme suit pour activer Enterprise State Roaming :
 
1. Connectez-vous au Portail Azure Classic. 
2. Sur la gauche, sélectionnez **ACTIVE DIRECTORY**, puis sélectionnez le répertoire pour lequel vous souhaitez activer Enterprise State Roaming. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)	 
3. Cliquez sur l’onglet **Configurer** en haut. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4.	Faites défiler la page vers le bas, sélectionnez **Les utilisateurs peuvent synchroniser les paramètres et les données d’application de l’entreprise**, puis cliquez sur **Enregistrer**. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Pour qu’un appareil Windows 10 prenne en charge l’itinérance avec le service Enterprise State Roaming, l’appareil doit s’authentifier à l’aide d’une identité Azure AD. Pour les appareils joints à Azure AD, la connexion principale de l’utilisateur est l’identité Azure AD. Ainsi, aucune configuration supplémentaire n’est requise. Pour les appareils qui utilisent un annuaire Active Directory local traditionnel, l’administrateur informatique doit connecter l’Active Directory local à Azure AD à l’aide d’[Azure AD Connect](active-directory-aadconnect.md), puis configurer la stratégie de groupe pour obliger les appareils clients à synchroniser automatiquement les données utilisateur avec Azure.

## Stockage des données de synchronisation
Les données Enterprise State Roaming sont hébergées dans une ou plusieurs [régions Azure](https://azure.microsoft.com/regions/) qui correspondent le plus à la valeur de pays définie dans l’instance Azure AD. Par exemple, les clients qui ont une valeur pays « France » sont hébergés dans une ou plusieurs des régions Azure situées en Europe, tandis que les données des utilisateurs ayant défini leur valeur de pays dans Azure AD sur « US » sont hébergées dans un ou plusieurs des régions Azure situées aux États-Unis. La valeur du pays est définie dans le cadre du processus de création de domaine Azure AD et ne peut pas être modifiée par la suite.

Pour plus d’informations sur l’emplacement de stockage des données, veuillez soumettre un ticket au [support technique Azure](https://azure.microsoft.com/support/options/).

## Gérer Enterprise State Roaming
Les administrateurs clients Azure AD peuvent activer et désactiver Enterprise State Roaming dans le portail Azure Classic. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

Les administrateurs clients peuvent également afficher un rapport d’état de synchronisation par utilisateur et limiter la synchronisation des paramètres à certains groupes de sécurité.

##Conservation des données
Les données synchronisées sur Azure via Enterprise State Roaming sont conservées indéfiniment, sauf si elles sont supprimées manuellement ou déterminées comme étant obsolètes.
 
- **Suppression manuelle de données** : si l’administrateur Azure AD souhaite supprimer manuellement des données de paramètres, il peut soumettre un ticket au [support technique Azure](https://azure.microsoft.com/support/options/).
 
 - **Suppression d’un utilisateur** : lorsqu’un utilisateur est supprimé dans Azure AD, son compte d’utilisateur est désactivé pendant 30 jours. Après 30 jours, le compte est supprimé ainsi que les données de paramètres associées.
 - **Suppression d’un client (répertoire)** : la suppression d’un répertoire entier dans Azure AD est une opération à effet immédiat. Toutes les données de paramètres associées à ce répertoire sont ensuite supprimées. 
 - **Suppression de paramètres** : si l’administrateur Azure AD souhaite supprimer immédiatement les données de paramètres d’un utilisateur spécifique, il peut soumettre un ticket au support technique Azure. 
- **Données obsolètes** : les données qui n’ont pas été utilisées depuis un an (« la période de rétention ») sont considérées comme obsolètes et peuvent être supprimées d’Azure. Il peut s’agir d’un ensemble spécifique de paramètres Windows/d’application ou de tous les paramètres d’un utilisateur. Par exemple : 
 - Si une collection de paramètres spécifique n’est utilisée par aucun appareil (par exemple, si une application est supprimée de l’appareil ou qu’un groupe de paramètres tels que « Thème » est désactivé pour tous les appareils d’un utilisateur), elle devient obsolète à la fin de la période de rétention et peut être supprimée. 
 - Si un utilisateur a désactivé la synchronisation des paramètres sur tous ses appareils, aucune donnée de paramètres n’est utilisée. De plus, toutes les données de paramètres pour cet utilisateur deviennent obsolètes et peuvent être supprimées à la fin de la période de rétention. 
 - Si l’administrateur du répertoire Azure AD désactive Enterprise State Roaming pour l’ensemble du répertoire, la synchronisation des paramètres cessera pour tous les utilisateurs du répertoire. De plus, toutes les données de paramètres de tous les utilisateurs deviennent obsolètes et peuvent être supprimées à la fin de la période de rétention. 

- **Récupération de données supprimées** : la stratégie de rétention de données n’est pas configurable. Une fois que les données ont été définitivement supprimées, elles ne sont pas récupérables. Toutefois, il est important de noter que les données de paramètres seront supprimées sur Azure uniquement, et non sur l’appareil de l’utilisateur final. Si un appareil se reconnecte au service Enterprise State Roaming, les paramètres sont alors à nouveau synchronisés et stockés dans Azure.

## Rubriques connexes
- [Présentation d’Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [FAQ sur l’itinérance des paramètres et des données](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Paramètres de stratégie de groupe et de MDM pour la synchronisation des paramètres](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Référence des paramètres d’itinérance Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->