<properties 
   pageTitle="Configurer une méthode de routage du trafic basé sur les performances | Microsoft Azure"
   description="Cet article vous aide à configurer la méthode de routage du trafic basé sur les performances dans Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />

# Configurer une méthode de routage du trafic basé sur les performances

Pour router le trafic des services cloud et des sites Web (points de terminaison) qui se trouvent dans différents centres de données du monde entier (également appelés régions), vous pouvez diriger le trafic entrant vers le point de terminaison qui présente la plus faible latence du point de vue du client à l'origine de la demande. En règle générale, le centre de données avec la latence la plus faible est celui qui se trouve géographiquement le plus proche. La méthode de routage du trafic basé sur les performances permet une répartition en fonction de la latence, mais ne tient pas compte des modifications apportées en temps réel à la charge ou à la configuration réseau. Pour plus d’informations sur les différentes méthodes de routage du trafic fournies par Azure Traffic Manager, consultez la rubrique [À propos des méthodes de routage du trafic de Traffic Manager](traffic-manager-load-balancing-methods.md).

## Router le trafic en fonction de la latence la plus faible sur un ensemble de points de terminaison :

1. Dans le volet gauche du portail de gestion, cliquez sur l’icône **Traffic Manager** pour ouvrir le volet Traffic Manager. Si vous n’avez pas encore créé votre profil Traffic Manager, consultez [Gérer les profils Traffic Manager](traffic-manager-manage-profiles.md) pour connaître les étapes de création d’un profil Traffic Manager de base.
2. Dans le portail de gestion, dans le volet Traffic Manager, recherchez le profil Traffic Manager qui contient les paramètres que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
3. Dans la page de votre profil, cliquez sur **Points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents. Pour savoir comment ajouter ou supprimer des points de terminaison dans votre profil, consultez [Gérer les points de terminaison dans Traffic Manager](traffic-manager-endpoints.md).
4. Dans la page de votre profil, cliquez sur **Configurer** en haut pour ouvrir la page de configuration.
5. Pour **Paramètres de méthode de routage de trafic**, vérifiez que la méthode de routage du trafic est **Performances*. Si ce n’est pas le cas, cliquez sur **Performances** dans la liste déroulante.
6. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. La surveillance permet de s'assurer qu'aucun trafic n'est dirigé vers les points de terminaison désactivés. Pour surveiller les points de terminaison, vous devez indiquer un chemin et un nom de fichier. Notez qu'une barre oblique « / » est une entrée valide pour le chemin d'accès relatif et qu'elle implique que le fichier se trouve dans le répertoire racine (par défaut). Pour plus d’informations sur la surveillance, consultez [À propos de la surveillance avec Traffic Manager](traffic-manager-monitoring.md).
7. Une fois que vous avez terminé de modifier la configuration, cliquez sur **Enregistrer** en bas de la page.
8. Testez les modifications dans votre configuration. Pour plus d’informations, consultez [Test des paramètres de Traffic Manager](traffic-manager-testing-settings.md).
9. Une fois le profil Traffic Manager configuré et opérationnel, modifiez l'enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre nom de domaine d'entreprise vers le nom de domaine Traffic Manager. Pour plus d’informations sur la marche à suivre, consultez [Redirection d’un domaine Internet d’entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md).

## Étapes suivantes

[À propos des méthodes de routage du trafic de Traffic Manager](traffic-manager-load-balancing-methods.md)

[Traffic Manager - Désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Traffic Manager - Désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

[Qu’est-ce que Traffic Manager ?](traffic-manager-overview.md)

[Cloud Services](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Opérations sur Traffic Manager (Référence sur l’API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769)

 

<!---HONumber=Oct15_HO3-->