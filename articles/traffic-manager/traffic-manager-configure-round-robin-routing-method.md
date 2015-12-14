<properties
   pageTitle="Configurer une méthode de routage du trafic en tourniquet de Traffic Manager | Microsoft Azure"
   description="Cet article vous aide à configurer l’équilibrage de charge de tourniquet pour vos points de terminaison Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/01/2015"
   ms.author="joaoma" />

# Configurer la méthode de routage du trafic en tourniquet (round robin)

Il est courant d'utiliser un ensemble de points de terminaison identiques, comprenant des services cloud et des sites web, et de répartir le trafic en tourniquet. Les étapes ci-dessous décrivent comment configurer Traffic Manager pour effectuer ce type de méthode de routage du trafic. Pour plus d’informations sur les différentes méthodes de routage du trafic, consultez la rubrique [À propos des méthodes de routage du trafic de Traffic Manager](traffic-manager-load-balancing-methods.md).

>[AZURE.NOTE]Azure Websites fournit déjà des fonctionnalités d'équilibrage de charge de tourniquet pour les sites web dans un centre de données (également appelé région). Traffic Manager vous permet de spécifier une méthode de routage du trafic en tourniquet (round robin) pour des sites web de différents centres de données.

## Router le trafic de manière équitable (tourniquet) sur un ensemble de points de terminaison :

1. Dans le volet gauche du portail Azure, cliquez sur l’icône **Traffic Manager** pour ouvrir le volet Traffic Manager. Si vous n’avez pas encore créé votre profil Traffic Manager, consultez [Gérer les profils Traffic Manager](traffic-manager-manage-profiles.md) pour connaître les étapes de création d’un profil Traffic Manager de base.
2. Dans le portail Azure, dans le volet Traffic Manager, recherchez le profil Traffic Manager qui contient les paramètres que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
3. Dans la page de votre profil, cliquez sur **Points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents. Pour savoir comment ajouter ou supprimer des points de terminaison, consultez [Gérer les points de terminaison dans Traffic Manager](traffic-manager-endpoints.md).
4. Dans la page de votre profil, cliquez sur **Configurer** en haut pour ouvrir la page de configuration.
5. Pour **Paramètres de méthode de routage de trafic**, vérifiez que la méthode de routage du trafic est **Tourniquet**. Si ce n’est pas le cas, cliquez sur **Tourniquet** dans la liste déroulante.
6. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. La surveillance permet de s'assurer qu'aucun trafic n'est dirigé vers les points de terminaison désactivés. Pour surveiller les points de terminaison, vous devez indiquer un chemin et un nom de fichier. Notez qu'une barre oblique « / » est une entrée valide pour le chemin d'accès relatif et qu'elle implique que le fichier se trouve dans le répertoire racine (par défaut). Pour plus d’informations sur la surveillance, consultez la rubrique [À propos de la surveillance avec Traffic Manager](traffic-manager-monitoring.md).
7. Une fois que vous avez terminé de modifier la configuration, cliquez sur **Enregistrer** en bas de la page.
8. Testez les modifications dans votre configuration. Pour plus d’informations, consultez [Test des paramètres de Traffic Manager](traffic-manager-testing-settings.md).
9. Une fois le profil Traffic Manager configuré et opérationnel, modifiez l'enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre nom de domaine d'entreprise vers le nom de domaine Traffic Manager. Pour plus d’informations sur la marche à suivre, consultez [Redirection d’un domaine Internet d’entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md).

## Étapes suivantes


[Rediriger un domaine Internet d’entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md)

[Méthodes de routage de Traffic Manager](traffic-manager-routing-methods.md)

[Configurer la méthode de routage par basculement](traffic-manager-configure-failover-routing-method.md)

[Configurer la méthode de routage basé sur les performances](traffic-manager-configure-performance-routing-method.md)

[Résolution des problèmes liés à l’état Détérioré de Traffic Manager](traffic-manager-troubleshooting-degraded.md)

[Traffic Manager - Désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Traffic Manager - Désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

 

<!---HONumber=AcomDC_1203_2015-->