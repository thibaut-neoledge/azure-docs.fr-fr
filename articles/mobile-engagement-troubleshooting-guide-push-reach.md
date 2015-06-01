<properties 
   pageTitle="Guide de dépannage d’Azure Mobile Engagement - Push/Reach" 
   description="Résolution des problèmes des interactions et des notifications utilisateur dans Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>


# Guide de dépannage pour les problèmes liés à Push et Reach

Voici des problèmes potentiels liés à la façon dont Azure Mobile Engagement envoie des informations à vos utilisateurs.
 
## Échecs Push

### Problème
- Push ne fonctionne pas (dans l'application, hors de l'application, ou les deux).

### Causes
- Un échec push indique souvent qu'Azure Mobile Engagement, Reach ou une autre fonctionnalité avancée d'Azure Mobile Engagement n'est pas correctement intégrée ou qu'une mise à niveau est requise dans le Kit de développement logiciel (SDK) pour résoudre un problème connu avec une nouvelle plate-forme de système d'exploitation ou de périphérique.
- Testez simplement un push dans l'application et un push hors de l'application pour déterminer si le problème se situe dans l'application ou hors de l'application.
- Dans le cadre du dépannage, effectuez des tests à partir de l'interface utilisateur et à partir de l'API pour voir les informations d'erreur supplémentaires disponibles aux deux emplacements.
- Les push en dehors de l'application ne fonctionneront pas si Azure Mobile Engagement et Reach sont intégrés dans le Kit de développement logiciel (SDK).
- Les transmissions de type push en dehors de l’application ne fonctionneront pas si les certificats ne sont pas valides, ou s’ils utilisent PROD  au lieu de DEV (iOS uniquement). (** Remarque : ** les notifications Push « en dehors de l’application » ne sont pas remises à iOS, si les versions de développement (DEV) et de production (PROD) de votre application sont installées sur le même périphérique, dans la mesure où le jeton de sécurité associé à votre certificat peut être invalidé par Apple. Pour résoudre ce problème, désinstallez les versions PROD et DEV de votre application et ré-installez uniquement une version sur votre périphérique.)
- Les décomptes de push en dehors de l'application sont gérés différemment sur des plates-formes différentes (iOS affiche moins d'informations qu'Android si les push natifs sont désactivés sur un périphérique, l'API peut fournir davantage d'informations que l'interface utilisateur dans les statistiques de push).
- Les push en dehors de l'application peuvent être bloqués par les clients au niveau du système d'exploitation (iOS et Android).
- Les push en dehors de l'application apparaissent comme désactivés dans l'interface utilisateur d'Azure Mobile Engagement s'ils ne sont pas correctement intégrés, mais peuvent échouer en mode silencieux à partir de l'API.
- Les push dans l'application ne fonctionneront pas si Azure Mobile Engagement et Reach sont intégrés dans le Kit de développement logiciel (SDK).
- Les push GCM et ADM ne fonctionneront pas si Azure Mobile Engagement et le serveur spécifique sont intégrés dans le Kit de développement logiciel (Android uniquement).
- Les push dans l'application et en dehors de l'application doivent être testés séparément pour déterminer s'il s'agit d'un problème Push ou Reach.
- Les push dans l'application requièrent que l'application soit ouverte pour être reçue.
- Les push dans l'application sont souvent configurés pour être filtrés par une balise d'informations d'application d'inclusion ou d'exclusion.
- Si vous utilisez une catégorie personnalisée dans Reach pour afficher les notifications dans l'application, vous devez suivre le cycle de vie correct de la notification, sans quoi la notification pourrait ne pas être effacée lorsque l'utilisateur la fait disparaître.
- Si vous lancez une campagne sans date de fin et qu'un périphérique reçoit la notification dans l'application mais ne l'affiche pas encore, l'utilisateur continuera à recevoir la notification lors de sa prochaine connexion à l'application, même si vous arrêtez manuellement la campagne.
- Pour les problèmes avec l'API Push, confirmez que vous souhaitez vraiment utiliser l'API Push au lieu de l'API Reach (l'API Reach étant utilisée plus souvent) et que vous ne confondez pas les paramètres « charge utile » et « notificateur ».
- Testez votre campagne push avec à la fois un périphérique connecté via le Wi-Fi et 3G pour éliminer la connexion réseau comme source potentielle de problèmes.

### Voir aussi

- [Guide de dépannage - Kit de développement logiciel (SDK)][Link 25], [Guide de dépannage - Notifications Push][Link 23], [Documentation du Kit de développement logiciel (SDK) - iOS - Préparation de votre application Apple pour les notifications Push][Link 5]
 
## Test push

### Problème
- Les push peuvent être envoyés à un périphérique spécifique basé sur un ID de périphérique.

### Causes

- Appareils de test sont configurés différemment pour chaque plate-forme, mais à l'origine d'un événement dans votre application sur un périphérique de test et recherchez votre ID de périphérique dans le portail doivent fonctionner pour trouver votre ID de périphérique pour toutes les plateformes.
- Les périphériques de test fonctionnent différemment avec IDFA et IDFV (iOS uniquement).

### Voir aussi

- [Documentation de l’interface utilisateur - Reach][Link 17]
 
## Personnalisation push

### Problème
- L'élément de contenu push avancé ne fonctionne pas (badge, sonnerie, vibration, image, etc.).
- Les liens de push ne fonctionnent pas (hors de l'application, dans l'application, vers un site Web, à un emplacement dans l'application).
- Les statistiques de push indiquent qu'un push n'a pas été envoyé au nombre de personnes prévu (trop ou trop peu).
- Push dupliqués et reçus en double.
- Impossible d'inscrire le dispositif de test pour les push Azure Mobile Engagement (avec votre propre application de production ou développement).

### Causes

- Pour créer un lien vers un emplacement spécifique dans l'application requiert "categories" (Android uniquement).
- Des schémas de lien profond pour rediriger les utilisateurs vers un autre emplacement après avoir cliqué sur une notification Push doivent être créés dans votre application et le système d’exploitation du périphérique et gérés par ces derniers, et non par Mobile Engagement directement. (** Remarque : ** les notifications en dehors de l’application ne peuvent pas lier directement à des emplacements au sein de l’application avec iOS, contrairement à Android.)
- Les serveurs d'images externes doivent être en mesure d'utiliser HTTP « GET » et « HEAD » pour que les push de grandes images fonctionnent (Android uniquement).
- Dans votre code, vous pouvez désactiver l'agent Azure Mobile Engagement lorsque le clavier est ouvert et que votre code réactive l'agent Azure Mobile Engagement une fois que le clavier est fermé afin que le clavier n'affecte pas l'apparence de votre notification (iOS uniquement).
- Certains éléments ne fonctionnent pas dans les simulations de test, mais uniquement les campagnes réelles (badge, sonnerie, vibration, image, etc.).
- Aucune donnée du côté serveur n'est enregistrée lorsque vous utilisez le bouton pour « tester » les push. Les données sont enregistrées uniquement pour les campagnes push réelles.
- Pour aider à isoler le problème, résolvez les problèmes avec : tester, simuler et une campagne réelle puisque chacun d'eux fonctionne légèrement différemment.
- La durée pendant laquelle vos campagnes « dans l’application » et « à tout moment » sont planifiées pour s’exécuter affecte les numéros de livraison, dans la mesure où une campagne n’est remise qu’aux utilisateurs « dans l’application » pendant son exécution (et aux utilisateurs disposant de paramètres de périphérique définis pour recevoir des notifications « en dehors de l’application »).
- Les différences de gestion des notifications en dehors de l’application entre Android et iOS compliquent la comparaison directe des statistiques de transmission entre la version Android et iOS de votre application. Android fournit plus d’informations de notification au niveau du système d’exploitation qu’iOS. Android signale dans le centre de notification lorsqu’une notification native est reçue, supprimée ou qu’un utilisateur clique dessus, mais iOS ne signale pas ces informations, à moins que l’utilisateur clique sur la notification. 
- La principale raison pour laquelle les statistiques de « push » sont différentes de celles de « livraison » pour campagnes Reach est que les notifications « dans l’application » et « en dehors de l’application » sont comptées différemment. Les notifications « dans l’application » sont gérées par Mobile Engagement, tandis que les notifications « en dehors de l’application » sont gérées par le centre de notification du système d’exploitation de votre périphérique.

### Voir aussi

- [Procédures - Première notification Push][Link 27], [Guide de dépannage - Push][Link 23], [Informations sur le protocole HTTP](http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
- [Développeur Apple - Informations de l’interface utilisateur « Refuser »](http://support.apple.com/kb/HT3576), [Développeur Apple - Informations de développement « Refuser »](https://developer.apple.com/notifications/), [Développeur Apple - Dépannage « Refuser »](https://developer.apple.com/library/ios/technotes/tn2265/), [Développeur Apple - schéma d’URL](https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html) 
- [Développeur Android - Informations de l’interface utilisateur « Refuser »](http://developer.android.com/about/versions/jelly-bean.html), [Développeur Android - Modèles « Refuser »](http://developer.android.com/design/patterns/notifications.html), [Développeur Android - Notificateur « Refuser »](http://developer.android.com/guide/topics/ui/notifiers/notifications.html), [Développeur Android - Aperçu « Refuser »](https://developer.android.com/preview/notifications.html), [Développeur Android - Référence « Refuser »](http://developer.android.com/reference/android/app/Notification.html), [Développeur Android - Schéma du filtre d’intention](http://developer.android.com/guide/components/intents-filters.html#DataTest), [Développeur Android - Guide de référence du filtre d’intention](http://developer.android.com/reference/android/content/Intent.html#toUri)

## Ciblage push

### Problème
- La ciblage intégré ne fonctionne pas comme prévu.
- Le ciblage de balise d'informations d'application ne fonctionne pas comme prévu.
- Le ciblage par géolocalisation ne fonctionne pas comme prévu.
- Les options de langue ne fonctionnent pas comme prévu.

### Causes

- Assurez-vous que vous avez téléchargé les balises d'informations d'applications via l'interface utilisateur ou l'API Azure Mobile.
- La limitation de la vitesse des push ou le quota de push au niveau de l'application, ou la limitation de l'audience au niveau de la campagne peut empêcher une personne de recevoir un push spécifique, même si elle répond à vos critères de ciblage. 
- La définition d'une « langue » est différente du ciblage en fonction du pays ou des paramètres régionaux, qui lui-même est différent du ciblage en fonction de l'emplacement géographique basé sur l'emplacement téléphonique ou du GPS.
- Le message dans la « langue par défaut » est envoyé à tous les clients dont le périphérique n'est pas défini sur l'une des langues alternatives que vous spécifiez.

### Voir aussi

- [Documentation de l’interface utilisateur - Reach][Link 17], [Documentation de l’interface utilisateur - Paramètres][Link 20], [Documentation de l’API - Reach][Link 4], [Documentation de l’API - Push][Link 4], [Documentation de l’API - Périphérique][Link 4]
 
## Planification push

### Problème
- La planification push ne fonctionne pas comme prévu (envoi trop précoce ou retardé).

### Causes

- Fuseaux horaires peuvent problèmes grâce à la planification, surtout lorsque vous utilisez le fuseau horaire de l'utilisateur final.
- Les fonctionnalités push avancées peuvent retarder les push.
- Le ciblage en fonction des paramètres du téléphone (au lieu des balises d'informations de l'application) peut retarder les push car Azure Mobile Engagement peut avoir à demander les données du téléphone en temps réel avant d'envoyer un push.
- Les campagnes marketing créées sans date de fin stockent le push localement sur le périphérique et l'affichent lors de la prochaine ouverture de l'application, même si la campagne est terminée manuellement.
- Le démarrage simultané de plusieurs campagnes peut mettre plus de temps à analyser votre base d'utilisateurs (essayez de démarrer une seule campagne à la fois avec un maximum de quatre, et ciblez uniquement vos utilisateurs actifs afin que les anciens utilisateurs n'aient pas à être analysés).
- Si vous utilisez l'option « Ignorer l'audience, la notification Push sera envoyée aux utilisateurs via l'API Reach » dans la section « Campagne » de la campagne Reach, cette campagne ne sera pas automatiquement envoyée. Vous devrez l'envoyer manuellement via l'API Reach.
- Si vous utilisez une catégorie personnalisée dans Reach pour afficher les notifications dans l'application, vous devez suivre le cycle de vie correct d'une notification, sans quoi la notification pourrait ne pas être effacée lorsque l'utilisateur la fait disparaître.

### Voir aussi

- [Procédures Reach - Planification de campagne][Link 3], [Documentation de l’interface utilisateur - Nouvelle campagne Push Reach][Link 27]
 
<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

<!--HONumber=54-->