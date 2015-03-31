<properties 
   pageTitle="Guide de dépannage d'Azure Mobile Engagement" 
   description="Guide de dépannage pour Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement - Guides de dépannage

## Introduction

Les guides de dépannage suivants couvrent les problèmes les plus courants rencontrés avec Azure Mobile Engagement, à l'exception des questions relatives à la [facturation][Link 11] et à l'inscription à l'assistance pour la [version préliminaire][Link 7]. Si vous n'êtes pas en mesure de résoudre votre problème avec ces guides de dépannage, vous pouvez soit ouvrir une demande de service Azure - si vous avez un [contrat de Support Azure](http://azure.microsoft.com/support/options/) - ou demander de l'aide sur le [Forum MSDN][Link 8]. Veillez à inclure toutes les informations de dépannage des <a href="#SR" title="SR">informations de la demande de service</a> lorsque vous ouvrez une demande de service pour aider le support technique à étudier votre problème.

## <a name="#ANALYTICS">Analyse, surveillance, segmentation et tableaux de bord</a>

Problèmes liés à la façon dont Azure Mobile Engagement rassemble des informations sur vos applications, périphériques et utilisateurs.

**Liste de symptômes :**

1. <a href="#ANALYTICS1">Informations manquantes/retardées</a>
2. <a href="#ANALYTICS2">Impossible de trouver des éléments dans l'interface utilisateur</a>
3. <a href="#ANALYTICS3">Dépannage d'incident</a>
 
<a name="#ANALYTICS1">**Symptôme :**
1.    Informations manquantes/retardées :</a>

- Les informations sont retardées dans l'analyse, la segmentation ou le tableau de bord.
- Il manque des informations de surveillance.
- Il manque des informations d'analyse, de segmentation ou du tableau de bord.
- Limites de segmentation atteinte.

**Causes :**

- Vous pouvez utiliser l'API Analytics, Monitor et Segments pour voir si des données manquantes dans l'IU sont visibles avec les API.
- Si le Kit de développement logiciel (SDK) Azure Mobile Engagement n'est pas correctement intégrée dans votre application, vous ne serez pas en mesure de voir les informations dans Analyse, Segmentation, Surveillance ou Tableaux de bord.
- Les segments ne peuvent pas être modifiée après avoir été créés, ils peuvent uniquement être " clonés " (copiés) ou " détruits " (supprimés).
Les segments peuvent uniquement contenir 10 critères.
- La meilleure façon de tester les informations manquantes de l'analyse (configurer un périphérique de test, désinstaller et/ou réinstaller l'application sur le périphérique de test).
- Les informations sont actualisées toutes les 24 heures pour l'analyse, la segmentation ou les tableaux de bord.
- Les informations figurant dans les nouveaux segments ne peuvent pas être affichées au cours de 24 heures qui suivent leur création (même si le segment est basé sur des informations antérieures).
- Le filtrage des données d'analyse dans l'interface utilisateur affiche tous les exemples de ce type, quelle que soit la version de votre application (par exemple, " Incidents " filtré par nom s'affichera dans les versions 1 et 2 de votre application).
- La période de l'analyse est basée sur la date des paramètres des périphériques des utilisateurs ; ainsi, un utilisateur dont le téléphone a une date incorrecte pourrait s'afficher dans une période incorrecte.
- Aucune donnée côté serveur n'est enregistrée lorsque vous utilisez le bouton pour " tester " les Push. Les données sont uniquement enregistrées pour les campagnes Push réelles.

**Voir aussi :**

- [Guide de dépannage - SDK][Link 2], [Documentation de l'API][Link 4], [Documentation de l'interface utilisateur - Segments][Link 1]

<a name="#ANALYTICS2">**Symptôme :**
2. Impossible de trouver des éléments dans l'interface utilisateur :</a>

- Impossible de créer des segments en fonction de certains critères de balise d'informations d'application intégrés ou personnalisés.
- Impossible de trouver certains critères de balise d'informations d'application intégrés ou personnalisés dans l'analyse, la surveillance ou les tableaux de bord.
- Impossible d'interpréter les données de l'analyse, de la surveillance, de la segmentation ou des tableaux de bord.

**Causes :**

- Certains éléments intégrés et certaines balises d'informations d'application sont uniquement disponibles en tant que critères push mais ne peuvent pas être ajoutés à un segment ou être visibles depuis l'analyse, la surveillance ou le tableau de bord. 
- Pour les éléments intégrés et les balises d'informations d'application qui ne peuvent pas être ajoutés à un segment, vous devrez configurer la liste des critères de ciblage dans chaque campagne pour effectuer la même fonction que le ciblage basé sur un segment.
- Consultez les menus contextuels dans les sections Analyse, Surveillance, Segmentation et Tableaux de bord de l'interface utilisateur d'Azure Mobile Engagement pour obtenir de l'aide de l'aide et consignes.

**Voir aussi :**

- [Documentation de l'interface utilisateur - Critère de nouveau Push Rush pour cibler un public][Link 1]
 
<a name="#ANALYTICS3">**Symptôme :**
3. Dépannage d'incident :</a>

- Incidents d'application apparaissant dans l'analyse, la surveillance ou le tableau de bord.

**Causes :**

- Pour résoudre les problèmes d'incident d'application dans Analyse, Surveillance ou Tableau de bord, veillez à consulter les notes de publication pour les problèmes connus avec les versions précédentes du Kit de développement logiciel (SDK).
- Pour mieux résoudre les incidents d'application, effectuez un événement à partir d'un périphérique de test avec votre application installée et recherchez l'ID de votre périphérique dans la section " Moniteur - Événements " de l'interface utilisateur d'Azure Mobile Engagement. Effectuez ensuite l'événement à l'origine de l'incident de votre application et cherchez des informations supplémentaires dans la section " Moniteur - Incident " de l'interface utilisateur d'Azure Mobile Engagement. 

**Voir aussi :**

- [Concepts - FAQ][Link 6], [Concepts - Glossaire][Link 6], [Documentation de l'interface utilisateur][Link 1], [Documentation du Kit de développement logiciel - Notes de publication][Link 5], [Documentation du Kit de développement logiciel - Guides de mise à niveau][Link 5], [Procédures - Inscription de l'ID d'un périphérique][Link 3]

## <a name="#APIS">API</a>

Problèmes d'interaction entre les administrateurs avec Azure Mobile Engagement via les API.

**Liste de symptômes :**

1. <a href="#APIS1">Problèmes de syntaxe</a>
2. <a href="#APIS2">Impossible d'utiliser l'API pour effectuer la même action disponible dans l'interface utilisateur d'Azure Mobile Engagement</a>
3. <a href="#APIS3">Messages d'erreur</a>
4. <a href="#APIS4">Échecs silencieux</a>
 
<a name="#APIS1">**Symptôme :**
1. Problèmes de syntaxe :</a>

- Erreurs de syntaxe lors de l'utilisation de l'API (ou comportement inattendu).

**Causes :**

- Problèmes de syntaxe :
    - Veillez à vérifier la syntaxe de l'API spécifique que vous utilisez pour vérifier que l'option est disponible.
    - Un problème courant avec l'utilisation de l'API est la confusion entre l'API Reach et l'API Push (la plupart des tâches doivent être effectuées avec l'API Reach et non l'API Push). 
    - Un autre problème courant avec l'intégration du SDK et l'utilisation de l'API est la confusion entre la clé du Kit de développement logiciel et la clé d'API.
    - Les scripts qui se connectent aux API doivent envoyer des données au moins 10 minutes, ou la connexion expire (fréquent surtout dans les scripts d'analyse API écoute pour les données). Pour éviter les délais d'attente, faites en sorte que votre script envoie une commande ping XMPP toutes les 10 minutes afin que la session avec le serveur reste active.

**Voir aussi :**
 
- [Concepts - Glossaire][Link 6], [Documentation de l'API][Link 4], [Informations sur le protocole XMPP]( http://xmpp.org/extensions/xep-0199.html)
 
<a name="#APIS2">**Symptôme :**
2. Impossible d'utiliser l'API pour effectuer la même action disponible dans l'interface utilisateur d'Azure Mobile Engagement :</a>

- Une action qui fonctionne à partir de l'interface utilisateur d'Azure Mobile Engagement ne fonctionne pas dans l'API Mobile Azure Engagement qui lui est associée.

**Causes :**

- Confirmer que vous pouvez effectuer la même action à partir de l'interface utilisateur de Azure Mobile Engagement indique que vous avez correctement intégré cette fonctionnalité d'Azure Mobile Engagement avec le Kit de développement.

**Voir aussi :**
 
- [Documentation de l'interface utilisateur][Link 1]
 
<a name="#APIS3">**Symptôme :**
3. Messages d'erreur :</a>

- Des codes d'erreur lors de l'utilisation de l'API s'affichent lors de l'exécution ou dans les journaux.

**Causes :**

- Voici une liste composite de numéros de codes d'état d'API courants pour référence et dépannage préliminaire :

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response's body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently "in progress", campaign must be "in progress" and the campaign's property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn't retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504         The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

**Voir aussi :**

- [Documentation des API - pour connaître le détail des erreurs de chaque API spécifique][Link 4]
 
<a name="#APIS4">**Symptôme :**
4. Échecs silencieux :</a>

- L'action de l'API échoue et aucun message d'erreur ne s'affiche lors de l'exécution ou dans les journaux.

**Causes :**

- Nombre d'éléments sera désactivé dans l'interface utilisateur de Azure Mobile Engagement si elles ne sont pas intégrées correctement, mais échouent silencieusement à partir de l'API, par conséquent, n'oubliez pas de tester la même fonctionnalité à partir de l'interface utilisateur pour voir si elle fonctionne.
- Azure Engagement Mobile et de nombreuses fonctionnalités avancées d'Azure Mobile Engagement que vous essayez d'utiliser doivent être intégrés individuellement dans votre application avec le Kit de développement logiciel, dans le cadre d'étapes distinctes, avant de pouvoir les utiliser.

**Voir aussi :**

- [Guide de dépannage - SDK][Link 2], [Documentation du SDK][Link 5]
 
## <a name="#PUSH">Push/Reach</a>

Problèmes avec la façon dont Azure Mobile Engagement envoie des informations à vos utilisateurs.
 
**Liste de symptômes :**

1. <a href="#PUSH1">Échecs de transmission</a>
2. <a href="#PUSH2">Problèmes de test push</a>
3. <a href="#PUSH3">Problèmes de personnalisation de push</a>
4. <a href="#PUSH4">Problèmes de ciblage de push</a>
5. <a href="#PUSH5">Planification push</a>
 
<a name="#PUSH1">**Symptôme :**
1. Échecs push :</a>

- Push ne fonctionne pas (dans l'application, hors de l'application, ou les deux).

**Causes :**

- Échecs push :
    - Un échec push indique souvent qu'Azure Mobile Engagement, Reach ou une autre fonctionnalité avancée d'Azure Mobile Engagement n'est pas correctement intégrée ou qu'une mise à niveau est requise dans le Kit de développement logiciel (SDK) pour résoudre un problème connu avec une nouvelle plate-forme de système d'exploitation ou de périphérique.
    - Testez simplement un push dans l'application et un push hors de l'application pour déterminer si le problème se situe dans l'application ou hors de l'application.
    - Dans le cadre du dépannage, effectuez des tests à partir de l'interface utilisateur et à partir de l'API pour voir les informations d'erreur supplémentaires disponibles aux deux emplacements.
    - Les push en dehors de l'application ne fonctionneront pas si Azure Mobile Engagement et Reach sont intégrés dans le Kit de développement logiciel (SDK).
    - Les push en dehors de l'application ne fonctionneront pas si les certificats ne sont pas valides, ou s'ils utilisent PROD ou DEV (iOS uniquement).
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
    - Pour les problèmes avec l'API Push, confirmez que vous souhaitez vraiment utiliser l'API Push au lieu de l'API Reach (l'API Reach étant utilisée plus souvent) et que vous ne confondez pas les paramètres " charge utile " et " notificateur ".
    - Testez votre campagne push avec à la fois un périphérique connecté via le Wi-Fi et 3G pour éliminer la connexion réseau comme source potentielle de problèmes.

**Voir aussi :**

- [Guide de dépannage - SDK][Link 2], [Guide de dépannage - Push][Link 2], [Documentation du Kit de développement logiciel - iOS - Préparation de votre Application pour les notifications Push Apple][Link 5]
 
<a name="#PUSH2">**Symptôme :**
2. Problèmes de test push :</a>

- Les push peuvent être envoyés à un périphérique spécifique basé sur un ID de périphérique.

**Causes :**

- Appareils de test sont configurés différemment pour chaque plate-forme, mais à l'origine d'un événement dans votre application sur un périphérique de test et recherchez votre ID de périphérique dans le portail doivent fonctionner pour trouver votre ID de périphérique pour toutes les plateformes.
- Les périphériques de test fonctionnent différemment avec IDFA et IDFV (iOS uniquement).

**Voir aussi :**

- [Documentation de l'interface utilisateur - Reach][Link 1]
 
<a name="#PUSH3">**Symptôme :**
3. Problèmes de personnalisation push :</a>

- L'élément de contenu push avancé ne fonctionne pas (badge, sonnerie, vibration, image, etc.).
- Les liens de push ne fonctionnent pas (hors de l'application, dans l'application, vers un site Web, à un emplacement dans l'application).
- Les statistiques de push indiquent qu'un push n'a pas été envoyé au nombre de personnes prévu (trop ou trop peu).
- Push dupliqués et reçus en double.
- Impossible d'inscrire le dispositif de test pour les push Azure Mobile Engagement (avec votre propre application de production ou développement).

**Causes :**

- Pour créer un lien vers un emplacement spécifique dans l'application requiert "categories" (Android uniquement).
- Les serveurs d'images externes doivent être en mesure d'utiliser HTTP " GET " et " HEAD " pour que les push de grandes images fonctionnent (Android uniquement).
- Dans votre code, vous pouvez désactiver l'agent Azure Mobile Engagement lorsque le clavier est ouvert et que votre code réactive l'agent Azure Mobile Engagement une fois que le clavier est fermé afin que le clavier n'affecte pas l'apparence de votre notification (iOS uniquement).
- Certains éléments ne fonctionnent pas dans les simulations de test, mais uniquement les campagnes réelles (badge, sonnerie, vibration, image, etc.).
- Aucune donnée du côté serveur n'est enregistrée lorsque vous utilisez le bouton pour " tester " les push. Les données sont enregistrées uniquement pour les campagnes push réelles.
- Pour aider à isoler le problème, résolvez les problèmes avec : tester, simuler et une campagne réelle puisque chacun d'eux fonctionne légèrement différemment.

**Voir aussi :**

- [Procédures - Push][Link 3], [Guide de dépannage - Push][Link 2], [Informations sur le protocole HTTP]( http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
 
<a name="#PUSH4">**Symptôme :**
4. Problèmes de ciblage push :</a>

- La ciblage intégré ne fonctionne pas comme prévu.
- Le ciblage de balise d'informations d'application ne fonctionne pas comme prévu.
- Le ciblage par géolocalisation ne fonctionne pas comme prévu.
- Les options de langue ne fonctionnent pas comme prévu.

**Causes :**

- Ciblage d'envoi :
    - Assurez-vous que vous avez téléchargé les balises d'informations d'applications via l'interface utilisateur ou l'API Azure Mobile.
    - La limitation de la vitesse des push ou le quota de push au niveau de l'application, ou la limitation de l'audience au niveau de la campagne peut empêcher une personne de recevoir un push spécifique, même si elle répond à vos critères de ciblage. 
    - La définition d'une " langue " est différente du ciblage en fonction du pays ou des paramètres régionaux, qui lui-même est différent du ciblage en fonction de l'emplacement géographique basé sur l'emplacement téléphonique ou du GPS.
    - Le message dans la " langue par défaut " est envoyé à tous les clients dont le périphérique n'est pas défini sur l'une des langues alternatives que vous spécifiez.

**Voir aussi :**

- [Documentation de l'interface utilisateur - Reach][Link 1], [Documentation de l'interface utilisateur - Paramètres][Link 1], [Documentation de l'API - Reach][Link 4], [Documentation de l'API - Push][Link 4], [Documentation de l'API - Périphérique][Link 4]
 
<a name="#PUSH5">**Symptôme :**
5. Planification push :</a>

- La planification push ne fonctionne pas comme prévu (envoi trop précoce ou retardé).

**Causes :**

- Fuseaux horaires peuvent problèmes grâce à la planification, surtout lorsque vous utilisez le fuseau horaire de l'utilisateur final.
- Les fonctionnalités push avancées peuvent retarder les push.
- Le ciblage en fonction des paramètres du téléphone (au lieu des balises d'informations de l'application) peut retarder les push car Azure Mobile Engagement peut avoir à demander les données du téléphone en temps réel avant d'envoyer un push.
- Les campagnes marketing créées sans date de fin stockent le push localement sur le périphérique et l'affichent lors de la prochaine ouverture de l'application, même si la campagne est terminée manuellement.
- Le démarrage simultané de plusieurs campagnes peut mettre plus de temps à analyser votre base d'utilisateurs (essayez de démarrer une seule campagne à la fois avec un maximum de quatre, et ciblez uniquement vos utilisateurs actifs afin que les anciens utilisateurs n'aient pas à être analysés).
- Si vous utilisez l'option " Ignorer l'audience, push sera envoyé aux utilisateurs via l'API " dans la section " Campagne " d'une campagne Reach, la campagne N'effectue PAS d'envoi automatique, vous devrez effectuer l'envoi manuellement via l'API Reach.
- Si vous utilisez une catégorie personnalisée dans Reach pour afficher les notifications dans l'application, vous devez suivre le cycle de vie correct d'une notification, sans quoi la notification pourrait ne pas être effacée lorsque l'utilisateur la fait disparaître.

**Voir aussi :**

- [Procédures - Planification][Link 3], [Documentation de l'interface utilisateur - nouvelle campagne Push de portée][Link 1]
 
## <a name="#SERVICE">Service</a>

Problèmes d'exécution d'Azure Mobile Engagement.

**Liste de symptômes :**

1. <a href="#SERVICE1">Interruptions de service</a>
2. <a href="#SERVICE2">Problèmes de connectivité et informations incorrectes</a>
3. <a href="#SERVICE3">Demandes de fonctionnalités</a>
 
<a name="#SERVICE1">**Symptôme :**
1. Interruptions de service :</a>

**Causes :**

- Problèmes qui semblent provenir d'Azure Mobile Engagement des interruptions de Service peuvent être dû à plusieurs problèmes différents :
    - Problèmes isolés systémiques à l'origine sur l'ensemble d'Azure Mobile Engagement
    - Problèmes connus provoqués par des pannes de serveur (ne s'affichent pas toujours dans l'état du serveur) :
retards de planification o, erreurs de ciblage, les problèmes de mise à jour de Badge, arrêt statistiques collecte, Push cesse de fonctionner, cessent de fonctionner, les nouvelles applications ou les utilisateurs de l'API ne peut pas être créés, des erreurs DNS et les erreurs de délai d'attente dans l'interface utilisateur, les API ou les applications sur un périphérique.
1.    Indisponibilités de dépendance de cloud
<Azure Service Status><Amazon Web Services (AWS) Status>
2.    Interruptions de dépendance des Services de notification push (PNS)
<Google - Service><Apple - Service><Android - Google GCM><Android - Amazon ADM><Apple - APN><Windows Phone - WNS><Windows Phone - MPNS><Windows - WNS>
3.    Interruptions App Store
<GooglePlay><iTunes><Windows Phone Store><Windows Store>

    - Indisponibilités de dépendance de cloud
[État du Service Azure]( http://azure.microsoft.com/status/), [État d'Amazon Web Services (AWS)]( http://status.aws.amazon.com/) 
    - Interruptions de dépendance des Services de notification push (PNS)
[Google - Service](http://www.google.com/appsstatus#hl=en&v=status), [Apple - Service]( http://www.apple.com/support/systemstatus/), [Android - Google GCM]( http://developer.android.com/google/gcm/index.html), [Android - Amazon ADM]( https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple - APN]( https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)[Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone - MPNS](http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx), [Windows - WNS](https://developer.windows.com/)
    - Interruptions App Store
[GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Windows Phone Store](http://www.windowsphone.com/), [Windows Store](http://windows.microsoft.com/)

*Pour savoir si le problème est systémique, vous pouvez tester la même fonction à partir d'une autre :*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*Pour savoir si le problème affecte uniquement l'interface utilisateur ou les API :*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[Documentation de l'API][Link 4], [Documentation de l'interface utilisateur][Link 1]
	
*Pour tester si le problème provient du réseau de votre téléphone cellulaire :*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*Pour tester si le problème provient de votre périphérique :*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[Documentation de l'interface utilisateur - paramètres][Link 1]

*Pour tester si le problème provient de votre application :*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

Pour tester si le problème avec les mises à niveau du système d'exploitation à l'utilisateur final les périphériques qui nécessitent une mise à niveau du Kit de développement logiciel pour résoudre :

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[Guide de dépannage - SDK][Link 2]
 
<a name="#SERVICE2">**Symptôme :**
2. Problèmes de connectivité et informations incorrectes :</a>

- Problèmes de connexion à l'interface utilisateur d'Azure Mobile Engagement.
- Erreurs de connexion avec l'API d'Azure Mobile Engagement.
- Problèmes lors du chargement des balises d'informations d'application via l'API de l'appareil.
- Problèmes lors du téléchargement des journaux ou des données exportées à partir d'Azure Mobile Engagement.
- Informations incorrectes indiquées dans l'interface utilisateur de Azure Mobile Engagement.
- Informations incorrectes indiquées dans les journaux Azure Mobile Engagement.

**Causes :**

- Pour les problèmes de connectivité avec Azure Mobile Engagement :
    - Confirmez que votre compte d'utilisateur dispose des autorisations nécessaires pour effectuer la tâche.
    - Vérifiez que le problème n'est pas isolé sur un seul ordinateur ou votre réseau local.
    - Vérifiez que que le service Azure Mobile Engagement n'a pas signalé d'interruptions.
    - Vérifiez que vos fichiers de balise d'informations d'application respectent toutes ces règles :
        - Utilisez uniquement le jeu de caractères UTF8 (le jeu de caractères ANSI n'est pas pris en charge).
        - Utilisez une virgule " , " comme caractère de séparation (vous pouvez ouvrir une demande de service pour modifier le caractère de séparation .csv afin que ce ne soit plus une virgule (" , ") mais un autre caractère, par exemple un point-virgule " ; ").
        - Utilisez des majuscules pour les valeurs booléennes " TRUE " et " FALSE ".
        - Utilisez un fichier d'une taille inférieure à la taille maximale de 35 Mo.

**Voir aussi :**

[Documentation de l'API][Link 4], [Documentation de l'interface utilisateur - Accueil][Link 1]
 
<a name="#SERVICE3">**Symptôme :**
3. Demandes de fonctionnalités :</a>

- La fonctionnalité que vous souhaitez utiliser n'apparaît pas encore dans Azure Mobile Engagement.

**Causes :**

Pour suggérer une nouvelle fonctionnalité Azure Mobile Engagement qui n'existe pas encore :
    - Ouvrez une demande de service Azure Mobile Engagement avec autant de détails que possible sur la nouvelle fonctionnalité que vous souhaiteriez voir dans Azure Mobile Engagement.
 
## <a name="#SDK">KIT DE DÉVELOPPEMENT LOGICIEL (SDK)</a>

Problèmes d'intégration d'Azure Mobile Engagement dans votre application.

**Liste de symptômes :**

1. <a href="#SDK1">Problèmes avec le Kit de développement logiciel (SDK) Azure Mobile Engagement découverts par un échec dans une autre partie de votre application</a>
2. <a href="#SDK2">Problèmes de codage avancé</a>
3. <a href="#SDK3">Problèmes de blocage d'application</a>
4. <a href="#SDK4">Échecs de téléchargement vers l'App Store</a>
 
<a name="#SDK1">**Symptôme :**
1. Problèmes avec le Kit de développement logiciel (SDK) Mobile Azure Engagement découverts par un échec dans une autre partie de votre application :</a>

- Échec de la collecte de données de l'interface utilisateur (dans Analyse, Surveillance, Segmentation ou Tableaux de bord).
- Échec push (push ne fonctionne pas dans l'application, en dehors de l'application, ou les deux).
- Échecs de composants avancés (le suivi, la géolocalisation ou les push spécifiques à une plateforme ne fonctionnent pas).
- Échecs d'API (les API échouent souvent en mode silencieux sans afficher de message d'erreur).
- Défaillances du service (aucun élément d'Azure Mobile Engagement ne fonctionne avec votre application).

**Causes :**

- La plupart des problèmes qui doivent être résolus avec le Kit de développement logiciel Azure Mobile Engagement sera découvert par un échec dans votre application (par exemple, un échec de collecte des données de l'interface utilisateur, Échec de transmission, Échec de la fonctionnalité avancée, Échec de l'API, Application tombe en panne ou interruption de service apparente).  
- Si une fonctionnalité particulière d'Azure Mobile Engagement n'a jamais fonctionné dans votre application auparavant, vous devez terminer l'intégration. 
- Si une fonctionnalité particulière de l'Engagement Mobile Azure fonctionnait et arrêté, vous devrez peut-être mettre à niveau vers la dernière version avec l'Engagement de Mobile Azure SDK. N'oubliez pas qu'il existe une version différente du Kit de développement logiciel (SDK)d'Azure Mobile Engagement pour chaque plateforme prise en charge par Azure Mobile Engagement (Android, iOS, Web, Windows et Windows Phone).

*Intégration du Kit de développement logiciel :* 

- Azure Mobile Engagement n'est pas correctement intégré dans le Kit de développement logiciel (Analyse).
- Reach n'est pas correctement intégré dans le Kit de développement logiciel (push dans l'application et en dehors de l'application).
- Certificat expiré ou PROD et DEV incorrects (iOS uniquement).
- GCM ou ADM n'est pas correctement intégré dans le Kit de développement logiciel (Android uniquement - Push spécifique au service).
- Le suivi n'est pas correctement intégré dans le Kit de développement logiciel (installer le suivi de magasin).
- Intégration incorrecte de l'emplacement tardif ou de l'emplacement GPS dans le Kit de développement logiciel (ciblage par emplacement géographique).
[Documentation du Kit de développement logiciel - Guide d'intégration][Link 5], [Guide de dépannage - Push][Link 2]

*Mise à niveau du Kit de développement logiciel :*

- Vous devez mettre à niveau le Kit de développement logiciel pour résoudre des problèmes des versions antérieures du Kit de développement logiciel (souvent liés à des versions plus récentes du système d'exploitation du périphérique).
- Désinstallez toutes les versions précédentes de votre application de votre appareil et réinstallez la version la plus récente de votre application, réenregistrez votre ID de périphérique à partir de l'interface utilisateur d'Azure Mobile Engagement pour confirmer que votre appareil utilise la version la plus récente de votre application.
[Documentation du Kit de développement logiciel - Notes de publication](http://go.microsoft.com/fwlink/?LinkId= 525554), [Documentation du Kit de développement logiciel - Guides de mise à niveau](http://go.microsoft.com/fwlink/?LinkId= 525554), [Documentation de l'interface utilisateur - Paramètres][Link 1]

*Kit de développement logiciel autre :*

- Des erreurs dans les sections de code liées à Azure Mobile Engagement peuvent empêcher Azure Mobile Engagement de fonctionner.
- Des erreurs dans le manifeste d'application " AndroidManifest.xml " peuvent empêcher Azure Mobile Engagement de fonctionner (Android uniquement).
- Un problème courant avec l'intégration du Kit de développement logiciel et 
- l'utilisation de l'API est la confusion entre la clé du Kit de développement logiciel et la clé d'API.
[Concepts - Glossaire][Link 6]
 
<a name="#SDK2">**Symptôme :**
2. Problèmes de codage avancé :</a>

-  Le code spécifique de plate-forme pas directement lié à Azure Mobile Engagement peut provoquer des problèmes sur iOS, Android et Windows Phone.

**Causes :**

- Beaucoup avancée des problèmes de codage avec Azure Mobile Engagement sont provoquées par le code spécifique de plate-forme mal écrit pas directement lié à l'Engagement de Mobile Azure. Outre la documentation d'Azure Mobile Engagement, vous devez consulter la documentation spécifique à la plateforme sur laquelle vous développez (Android, iOS, Web, Windows et Windows Phone).
- Ne pas configurer correctement " categories " empêche de lier une notification à un autre emplacement à l'intérieur ou en dehors de l'application (Android uniquement). 
- Ne pas définir " UIKit.framework " sur " facultatif " dans votre code iOS affiche un " Symbole erreur introuvable " et/ou des blocages sur les anciens périphériques iOS (iOS uniquement).
- Les certificats expirés ou qui ne sont pas correctement configurés à l'aide de la version de développement ou de production Prod du certificat, provoquent des problèmes push (iOS uniquement).
- Il existe certaines limitations inhérentes à une plateforme qu'Azure Mobile Engagement n'est pas en mesure de contrôler (par exemple le fonctionnement de system center pour les push en dehors de l'application dans Android et iOS).
- Azure Mobile Engagement publie une liste complète des packages internes utilisé par Azure Mobile Engagement pour iOS et Android à des fins de référence. N'oubliez pas que certaines fonctionnalités d'Azure Mobile Engagement sont spécifiques à la plate-forme (Android, iOS, Web, Windows et Windows Phone).
- Les Kits de développement logiciel pour chaque plateforme sont écrits dans les langages de programmation suivants :
    -     Kit de développement logiciel (SDK) Android écrit en Java
    -     Kit de développement logiciel (SDK) iOS écrit en objective C
    -     Kit de développement logiciel (SDK) Web écrit en JavaScript
    -     Kit de développement logiciel (SDK) Windows écrit en c# et en JavaScript
    -     Kit de développement logiciel (SDK) Windows Phone écrit en c# et en JavaScript

**Voir aussi :**

 - [Guide de dépannage - Push][Link 2], [Documentation du Kit de développement logiciel - Notes de publication][Link 5], [Documentation du Kit de développement logiciel - Guides de mise à niveau][Link 5], [Documentation du Kit de développement logiciel - Android - Vue d'ensemble de la documentation technique d'Azure Mobile Engagement][Link 5], [Documentation Kit de développement logiciel - iOS - Vue d'ensemble de la documentation technique Azure Mobile Engagement][Link 5], [Documentation du Kit de développement logiciel - iOS - Préparation de votre Application pour les notifications Push Apple][Link 5], [Développeur Android](https://developer.android.com/), [Développeur iOS](https://developer.apple.com/), [Développeur Windows](https://developer.windows.com/) 
 
<a name="#SDK3">**Symptôme :**
3.    Problèmes de blocage d'application</a>

- Votre application se bloque sur un périphérique de l'utilisateur final.

**Causes :**

- Les informations de blocage peuvent être consultées dans l'interface utilisateur de l'analyse ou de l'API d'analyse.
- Vous pouvez trouver l'ID de périphérique de votre périphérique de test et exécuter l'action à l'origine du blocage de votre application chez un utilisateur final afin d'identifier la cause de l'incident.
- Les problèmes connus avec le Kit de développement logiciel Mobile Azure Engagement qui provoquent le blocage d'applications sont parfois résolus par la mise à niveau vers la dernière version du Kit de développement logiciel. Par conséquent, veillez à vérifier les notes de publication de votre plateforme lorsque vous recherchez la cause d'un blocage.

**Voir aussi :**

- [Concepts - FAQ][Link 6], [Concepts - Glossaire][Link 6], [Documentation de l'API - Analyse de l'API - Pannes][Link 4], [Documentation de l'interface utilisateur - Analyse - Pannes][Link 1], [Documentation de l'interface utilisateur - Paramètres][Link 1], [Documentation du Kit de développement logiciel - Notes de publication][Link 5], [Documentation du Kit de développement logiciel - Guides de mise à niveau][Link 5]

<a name="#SDK4">**Symptôme :**
4. Échecs de téléchargement App Store</a>

- Stockent des erreurs liées à télécharger la dernière version de votre application dans iTunes, GooglePlay, Windows ou Windows Phone.

**Causes :**

- Application stocke parfois empêche les applications avec certaines fonctionnalités activées (l'iTunes store empêche l'utilisation de IDFV dans les applications dans le magasin et le magasin GooglePlay empêche le partage d'informations entre les applications de l'application). 
- Veillez à consulter les notes de publication sur votre plateforme et la version actuelle du SDK si vous avez des difficultés à télécharger une application dans le magasin.

**Voir aussi :**

- [Documentation du Kit de développement logiciel - Notes de publication][Link 5], [Documentation du Kit de développement logiciel - Guides de mise à niveau][Link 5] 

## <a name="#SR">Informations de dépannage SR</a>

Lorsque vous ouvrez une demande de service Azure Mobile Engagement, fournissez les informations suivantes :
 
**ID : Veuillez fournir tous les identificateurs applicables liées à votre problème :**

    - App ID
    - Campaign ID
    - Device ID
    - User ID
    - Username
    - App Info Tag
 
**Erreurs : Veuillez fournir toutes les informations sur l'erreur applicables liées à votre problème :**

    - The name of the API or UI section where the issue occurs
    - The text of any error message you receive
    - The results of any tests you have performed from the troubleshooting guides
 
-    [Guides de dépannage](http://go.microsoft.com/fwlink/?LinkId=524382)


**Code : Veuillez fournir les informations de codage applicables liées à votre problème :**

    - The SDK version and platform of your app (Android SDK 2.4.1, iOS 1.16.2, etc.)
    - The download location of your production app (or the APK/TGZ files of your development app)
    - The "AndroidManifest.xml" and/or any code snippet from your app related to Azure Mobile Engagement (for advanced troubleshooting)

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
