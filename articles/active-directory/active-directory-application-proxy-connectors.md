<properties
	pageTitle="Utilisation de connecteurs de proxy d’application Azure AD | Microsoft Azure"
	description="Explique comment créer et gérer des groupes de connecteurs dans le proxy d’Application Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="kgremban"/>


# Publier des applications sur des réseaux et emplacements distincts à l’aide de groupes de connecteurs

> [AZURE.NOTE]Le Proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou Basic d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

Les groupes de connecteurs sont utiles, notamment dans les scénarios suivants :


- Sites avec plusieurs centres de données interconnectés dans lesquels vous souhaitez conserver autant de trafic que possible, car les liaisons entre les centres de données sont généralement coûteuses et ont une latence élevée. Vous pouvez déployer des connecteurs dans chaque centre de données pour servir uniquement les applications qui résident dans le centre de données dans le but d’économiser des liaisons précieuses entre centres de données et offrir une expérience entièrement transparente à vos utilisateurs.
- Gestion d’applications installées sur des réseaux isolés qui ne font pas partie du réseau d’entreprise principal. Vous pouvez utiliser des groupes de connecteurs pour installer des connecteurs dédiés pour des réseaux isolés afin d’activer également l’isolement des applications sur le réseau.
- Pour les applications installées sur IaaS pour l’accès au cloud, les groupes de connecteurs fournissent un service commun pour sécuriser l’accès à toutes ces applications sans créer de dépendance supplémentaire vis-à-vis de votre réseau d’entreprise, ni fragmenter l’expérience. Les connecteurs peuvent être installés sur chaque centre de données du cloud et servir uniquement les applications qui se trouvent sur ce réseau. Vous pouvez installer plusieurs connecteurs pour atteindre une haute disponibilité.
- Prise en charge des environnements à plusieurs forêts dans lesquels des connecteurs spécifiques peuvent être déployés par forêt et définis pour servir des applications spécifiques.
- Des groupes de connecteurs peuvent être utilisés dans les sites de récupération d’urgence pour détecter le basculement ou faire office de sauvegarde pour le site principal.
- Des groupes de connecteurs peuvent également être utilisés pour servir plusieurs sociétés à partir d’un client unique.

## Utilisation de groupes de connecteurs
Pour regrouper vos connecteurs, vous devez vous assurer que vous avez [installé plusieurs connecteurs](active-directory-application-proxy-enable.md), puis veiller à leur attribuer un nom et à les regrouper. Enfin, vous devez les affecter à des applications spécifiques.

## Étape 1 : créer des groupes de connecteurs
Vous pouvez créer autant de groupes de connecteurs que vous le souhaitez. La création de groupes de connecteurs s’effectue dans le portail Azure Classic. Sélectionnez votre annuaire, puis cliquez sur **Configurer**.

  ![Capture d’écran de la configuration du proxy d’application - cliquez sur Gérer les groupes de connecteurs](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

Ensuite, sous Proxy d’application, cliquez sur **Gérer les groupes de connecteurs** et créez un groupe de connecteurs en attribuant un nom au groupe.

  ![Capture d’écran de groupes de connecteurs du proxy d’application - nommez le nouveau groupe](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## Étape 2 : affecter des connecteurs à vos groupes
Une fois les groupes de connecteurs créés, déplacez les connecteurs vers le groupe approprié. Sous **Proxy d’application**, cliquez sur **Gérer les connecteurs**. Sous **Groupe**, sélectionnez le groupe souhaité pour chaque connecteur. Notez que l’activation des connecteurs dans le nouveau groupe peut prendre jusqu’à 10 minutes.

  ![Capture d’écran de connecteurs du proxy d’application - sélectionnez un groupe dans le menu déroulant](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## Étape 3 : affecter des applications à vos groupes de connecteurs
La dernière étape consiste à définir chaque application sur le groupe de connecteurs destiné à la servir. Dans le portail Azure Classic, dans votre annuaire, sélectionnez l’application que vous voulez affecter au groupe, puis cliquez sur **Configurer**. Sous **Groupe de connecteurs**, sélectionnez le groupe que l’application doit utiliser. Cette modification est appliquée immédiatement.

  ![Capture d’écran de groupe de connecteurs du proxy d’application - sélectionnez un groupe dans le menu déroulant](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)

Pour plus d’informations sur la publication d’applications, consultez [Publier des applications à l’aide de votre propre nom de domaine](active-directory-application-proxy-custom-domains.md).

## Voir aussi
Vous pouvez faire bien d’autres choses encore avec le Proxy d’application :

- [Activer le proxy d’application](active-directory-application-proxy-enable.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Utiliser des applications utilisant les revendications](active-directory-application-proxy-claims-aware-apps.md)
- [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)

## En savoir plus sur le Proxy d’application
- [Consultez notre aide en ligne](active-directory-application-proxy-enable.md)
- [Consultez le blog sur le Proxy d’application](http://blogs.technet.com/b/applicationproxyblog/)
- [Regardez nos vidéos sur Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Ressources supplémentaires

* [À propos de la délégation Kerberos contrainte](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=AcomDC_0114_2016-->