<properties
	pageTitle="Comment configurer des connexions VPN dans Gestion des API Azure"
	description="Apprenez à configurer une connexion VPN dans Gestion des API Azure et à accéder à des services web par son intermédiaire."
	services="api-management"
	documentationCenter=""
	authors="antonba"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="antonba"/>

# Comment configurer des connexions VPN dans Gestion des API Azure

La prise en charge du VPN par Gestion des API vous permet de connecter votre proxy Gestion des API à un réseau virtuel Azure. Ceci permet aux clients Gestion des API de se connecter de façon sécurisée à leurs services web principaux, qu’ils soient locaux ou qu’il s’agisse de services inaccessibles via l’internet public.

## <a name="enable-vpn"> </a>Activer les connexions VPN

>La connexion VPN est disponible uniquement dans le niveau **Premium**. Pour passer sur cette connexion, ouvrez votre service Gestion des API dans le [portail de gestion][], puis ouvrez l’onglet **Mettre à l’échelle**. Dans la section **Général**, sélectionnez le niveau Premium et cliquez sur Enregistrer.

Pour activer la connectivité VPN, ouvrez votre service Gestion des API dans le [portail de gestion][] et passez à l’onglet **Configurer**.

Dans la section VPN, définissez la valeur de **Connexion VPN** sur **Activée**.

![Configurer l’onglet de l’instance Gestion des API][api-management-setup-vpn-configure]

Vous voyez maintenant une liste de toutes les régions où votre service Gestion des API est créé.

Sélectionnez un VPN et un sous-réseau pour chaque région. La liste des VPN contient les réseaux virtuels disponibles dans votre abonnement Azure et qui sont installés dans la région que vous configurez.

![Sélectionner le VPN][api-management-setup-vpn-select]

Cliquez sur **Enregistrer** au bas de la page. Vous ne pouvez pas effectuer d’autres opérations sur le service Gestion des API à partir du portail de gestion Azure pendant la mise à jour. Le proxy de service reste disponible et les appels d’exécution ne sont pas affectés.

Notez que l’adresse IP virtuelle du proxy change chaque fois que le VPN est activé ou désactivé.

## <a name="connect-vpn"> </a>Se connecter à un service web derrière un VPN

Une fois que votre service Gestion des API est connecté au VPN, l’accès aux services web dans le réseau virtuel est similaire à l’accès aux services publics. Tapez simplement l’adresse locale ou le nom de l’hôte \(si un serveur DNS a été configuré pour ce réseau virtuel Azure\) de votre service web dans le champ **URL du service Web** lors de la création ou de la modification d’une API.

![Ajouter des API à partir du VPN][api-management-setup-vpn-add-api]


## <a name="related-content"> </a>Contenu connexe


 * [Didacticiel : création d’un réseau virtuel entre différents locaux pour une connectivité de site à site][]
 * [Utilisation de l’inspecteur d’API pour le suivi des appels dans Gestion des API Azure][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[portail de gestion]: https://manage.windowsazure.com/

[Didacticiel : création d’un réseau virtuel entre différents locaux pour une connectivité de site à site]: ../virtual-networks-create-site-to-site-cross-premises-connectivity
[Utilisation de l’inspecteur d’API pour le suivi des appels dans Gestion des API Azure]: api-management-howto-api-inspector.md

<!--HONumber=54-->