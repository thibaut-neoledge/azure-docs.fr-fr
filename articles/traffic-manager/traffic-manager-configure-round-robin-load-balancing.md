<properties
   pageTitle="Configurer l’équilibrage de charge de tourniquet Traffic Manager"
   description="Cet article vous aide à configurer l’équilibrage de charge de tourniquet pour vos points de terminaison Traffic Manager."
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
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# Configurer l'équilibrage de charge de tourniquet

Il est courant d'utiliser un ensemble de points de terminaison identiques, comprenant des services cloud et des sites web, et de répartir le trafic en tourniquet. Les étapes ci-dessous décrivent comment configurer Traffic Manager pour effectuer ce type d'équilibrage de charge. Pour plus d’informations sur les différentes méthodes d’équilibrage de charge, consultez [À propos des méthodes d’équilibrage de charge de Traffic Manager](traffic-manager-load-balancing-methods.md).

>[AZURE.NOTE]Azure Websites fournit déjà des fonctionnalités d'équilibrage de charge de tourniquet pour les sites web dans un centre de données (également appelé région). Traffic Manager vous permet de spécifier l'équilibrage de charge de tourniquet pour des sites web dans différents centres de données.

## Équilibrer le trafic de manière équitable (tourniquet) sur un ensemble de points de terminaison :

1. Dans le volet gauche du portail de gestion, cliquez sur l’icône **Traffic Manager** pour ouvrir le volet Traffic Manager. Si vous n’avez pas encore créé votre profil Traffic Manager, consultez [Gérer les profils Traffic Manager](traffic-manager-manage-profiles.md) pour connaître les étapes de création d’un profil Traffic Manager de base.
2. Dans le portail de gestion, dans le volet Traffic Manager, recherchez le profil Traffic Manager qui contient les paramètres que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
3. Dans la page de votre profil, cliquez sur **Points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents. Pour savoir comment ajouter ou supprimer des points de terminaison, consultez [Gérer les points de terminaison dans Traffic Manager](traffic-manager-endpoints.md).
4. Dans la page de votre profil, cliquez sur **Configurer** en haut pour ouvrir la page de configuration.
5. Pour **Paramètres de méthode d’équilibrage de charge**, vérifiez que la méthode d’équilibrage de charge est **Tourniquet**. Si ce n’est pas le cas, cliquez sur **Tourniquet** dans la liste déroulante.
6. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. La surveillance permet de s'assurer qu'aucun trafic n'est dirigé vers les points de terminaison désactivés. Pour surveiller les points de terminaison, vous devez indiquer un chemin et un nom de fichier. Notez qu'une barre oblique « / » est une entrée valide pour le chemin d'accès relatif et qu'elle implique que le fichier se trouve dans le répertoire racine (par défaut). Pour plus d’informations sur la surveillance, consultez [À propos de la surveillance avec Traffic Manager](../traffic-manager-about-monitoring.md).
7. Une fois que vous avez terminé de modifier la configuration, cliquez sur **Enregistrer** en bas de la page.
8. Testez les modifications dans votre configuration. Pour plus d’informations, consultez [Test des paramètres de Traffic Manager](traffic-manager-testing-settings.md).
9. Une fois le profil Traffic Manager configuré et opérationnel, modifiez l'enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre nom de domaine d'entreprise vers le nom de domaine Traffic Manager. Pour plus d’informations sur la marche à suivre, consultez [Redirection d’un domaine Internet d’entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md).

## Voir aussi

[À propos des méthodes d’équilibrage de charge de Traffic Manager](traffic-manager-load-balancing-methods.md)

[Tâches de configuration de Traffic Manager](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Vue d’ensemble de Traffic Manager](../traffic-manmager-overview.md)

[Services Cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Opérations sur Traffic Manager (Référence sur l’API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=July15_HO4-->