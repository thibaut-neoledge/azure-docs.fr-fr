<properties 
   pageTitle="Surveillance avec Traffic Manager"
   description="Cet article vous aide à comprendre et à configurer la surveillance de Traffic Manager"
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

# À propos de la surveillance avec Traffic Manager

Azure Traffic Manager surveille vos points de terminaison, y compris les services cloud et sites web, pour vérifier qu’ils sont disponibles. Afin que la surveillance fonctionne correctement, vous devez la configurer de la même façon pour chaque point de terminaison que vous spécifiez dans votre profil Traffic Manager. Dès lors que vous avez configuré la surveillance, Traffic Manager affiche l’état de vos points de terminaison et de votre profil dans le Portail de gestion. Vous pouvez configurer les paramètres de surveillance dans le Portail de gestion sur la page Configurer de votre profil Traffic Manager. Vous pouvez spécifier les paramètres suivants :

- **Protocole** : choisissez HTTP ou HTTPS. Notez que la surveillance HTTPS ne vérifie pas si votre certificat SSL est valide, mais uniquement qu’il est présent.

- **Port** : choisissez le port utilisé pour la requête. Les ports HTTP standard et HTTPS font partie des options.

- **Chemin d’accès relatif et nom de fichier** : indiquez le chemin d’accès et le nom du fichier auquel le système de surveillance tentera d’accéder. Notez que vous pouvez utiliser la barre oblique « / » pour le chemin d’accès relatif, pour indiquer que le fichier est dans le répertoire racine (par défaut).

## À propos de la surveillance de l’état d’intégrité

Azure Traffic Manager indique l’intégrité du service du point de terminaison et du profil dans le Portail de gestion. La colonne d’état affiche l’état d’analyse le plus récent pour le profil et le point de terminaison. Cet état vous permet de connaître l’intégrité de vos profils en fonction de vos paramètres de surveillance Traffic Manager. Lorsque votre profil est intègre, les requêtes DNS sont distribuées à vos services selon les paramètres de routage du trafic du profil (tourniquet (round robin), performances ou basculement). Lorsque le système de surveillance de Traffic Manager détecte une modification de l’état d’analyse, il met à jour l’entrée d’état dans le Portail de gestion. L’actualisation de l’état peut prendre jusqu’à cinq minutes.

### État d’analyse des points de terminaison

Dans le tableau ci-dessous, l’état d’analyse des points de terminaison découle d’une combinaison des résultats des sondes d’intégrité des points de terminaison et des configurations de vos profils et points de terminaison.

|État du profil|État du point de terminaison|État d’analyse des points de terminaison (API et portail)|Remarques|
|---|---|---|---|
|Désactivé|Activé|Inactif|Les profils désactivés ne sont pas surveillés. Toutefois, l’état des points de terminaison dans les profils désactivés peut toujours être géré.|
|&lt;tout&gt;|Désactivé|Désactivé|Les profils désactivés ne sont pas surveillés. Toutefois, l’état des points de terminaison dans les profils désactivés peut toujours être géré.|
|Activé|Activé|En ligne|Le point de terminaison est surveillé et sain.|
|Activé|Activé|Détérioré|Le point de terminaison est surveillé et défectueux.|
|Activé|Activé|CheckingEndpoint|Le point de terminaison est surveillé, mais les résultats de la première analyse n’ont pas encore été reçus. Cet état temporaire est indiqué lorsque vous venez d’ajouter un nouveau point de terminaison au profil ou que vous venez d’activer un point de terminaison ou un profil.|
|Activé|Activé|Arrêté|Le service cloud ou site web sous-jacent n’est pas en cours d’exécution.|

### État d’analyse des profils

Dans le tableau ci-dessous, l’état d’analyse des profils découle d’une combinaison de l’état d’analyse des points de terminaison et de l’état de vos profils configurés.

|État du profil (conformément à la configuration)|État d’analyse des points de terminaison|État d’analyse des profils (API et portail)|Remarques|
|---|---|---|---|
|Désactivé|&lt;tout&gt; ou profil sans points de terminaison définis.|Désactivé|Les points de terminaison ne sont pas surveillés.|
|Activé|Un ou plusieurs points de terminaison ont l’état Détérioré.|Détérioré|Indique qu’une action du client est requise.|
|Activé|Un ou plusieurs points de terminaison ont l’état En ligne. Aucun point de terminaison n’est Détérioré.|En ligne|Le service accepte le trafic et aucune action du client n’est requise.|
|Activé|Un ou plusieurs points de terminaison ont l’état CheckingEndpoint. Aucun point de terminaison n’est En ligne ou Détérioré.|CheckingEndpoints|État de transition. Cet état survient généralement lorsqu’un profil vient d’être activé et que l’intégrité du point de terminaison est en cours d’analyse.|
|Activé|Tous les points de terminaison définis dans le profil ont l’état Désactivé ou Arrêté, ou le profil n’inclut aucun point de terminaison défini.|Inactif|Aucun point de terminaison n’est actif, mais le profil est toujours activé.|

## Fonctionnement de la surveillance

Un exemple illustrant, de façon de chronologique, le processus de surveillance avec un seul service cloud est proposé ci-dessous. Le scénario est le suivant :

- Le service cloud est disponible et reçoit le trafic via le profil Traffic Manager uniquement.

- Le service cloud devient indisponible.

- Le temps d’indisponibilité du service cloud est beaucoup plus long que la durée de vie (TTL, Time-to-Live) du DNS.

- Le service cloud redevient disponible.

- Le service cloud reçoit de nouveau le trafic via ce profil Traffic Manager uniquement.

![Séquence de surveillance de Traffic Manager](./media/traffic-manager-monitoring/IC697947.jpg)

**Figure 1** : exemple de séquence de surveillance. Les chiffres de la figure correspondent aux explications ci-après :

1. **GET** : le système de surveillance de Traffic Manager exécute une commande GET sur le chemin d’accès et le fichier que vous avez spécifiés dans les paramètres de surveillance.
2. **200 OK** : le système de surveillance attend le renvoi d’un message HTTP 200 OK dans un délai de 10 secondes. Lorsqu’il reçoit cette réponse, il considère que le service cloud est disponible. 

>[AZURE.NOTE]Traffic Manager considère qu’un point de terminaison a l’état En ligne uniquement lorsqu’il reçoit un message 200 OK. S’il reçoit une autre réponse, il considère que le point de terminaison n’est pas disponible et que le contrôle a échoué. Pour plus d’informations sur le dépannage des vérifications en échec, consultez [Résolution des problèmes liés à l’état Détérioré d’Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

3. **30 secondes entre les vérifications** : cette vérification est effectuée toutes les 30 secondes.
4. **Service cloud indisponible** : le service cloud est indisponible. Traffic Manager ne sera pas averti avant le prochain contrôle de surveillance.
5. **Tentatives d’accès au fichier de surveillance (4 essais)** : le système de surveillance exécute une commande GET, mais ne reçoit pas de réponse dans un délai de 10 secondes. Il effectue ensuite trois essais supplémentaires par intervalles de 30 secondes. Cela signifie qu’il faut environ 1 minute et demie au système de surveillance (au maximum) pour détecter si un service devient indisponible. Lorsque l’un des essais aboutit, le nombre d’essais est réinitialisé. Si un ou plusieurs messages 200 OK sont renvoyés plus de 10 secondes après la commande GET, le système de surveillance considère toujours que le contrôle a échoué (ceci n’apparaît pas sur la figure).
6. **Marquage du service comme détérioré** : après le quatrième échec successif, le système de surveillance marque le service cloud indisponible comme étant détérioré. 

7. **Diminution du trafic vers le service cloud** : le trafic vers le service cloud indisponible peut être maintenu. Les clients seront confrontés à des défaillances, car le service n’est pas disponible. Les clients et les serveurs DNS secondaires ont mis en cache l’enregistrement DNS pour l’adresse IP du service cloud indisponible. Ils continuent à résoudre le nom DNS du domaine d’entreprise en adresse IP du service. En outre, les serveurs DNS secondaires peuvent encore distribuer les informations DNS du service indisponible. Comme les clients et les serveurs DNS secondaires sont mis à jour, le trafic vers l’adresse IP du service indisponible est ralenti. Le système de surveillance continue à effectuer des contrôles toutes les 30 secondes. Dans cet exemple, le service ne répond pas et reste indisponible.
8. **Arrêt du trafic vers le service cloud** : à ce stade, la plupart des serveurs DNS et des clients doivent être mis à jour, et le trafic vers le service indisponible s’arrête. Le délai maximal avant l’arrêt complet du trafic dépend de la TTL. Par défaut, la TTL du DNS est de 300 secondes (5 minutes). Les clients se basent sur cette valeur pour arrêter d’utiliser le service après 5 minutes. Le système de surveillance continue à effectuer des contrôles toutes les 30 secondes et le service cloud ne répond pas.
9. **Le service cloud est de nouveau en ligne et reçoit le trafic** : le service est disponible, mais Traffic Manager n’en est pas informé tant que le système de surveillance n’a pas effectué de vérifications.
10. **Le trafic vers le service est rétabli** : Traffic Manager envoie une commande GET et reçoit un message 200 OK dans un délai de 10 secondes. Il commence ensuite à distribuer le nom DNS du service cloud aux serveurs DNS, à mesure que ces derniers demandent des mises à jour. Par conséquent, le trafic vers le service est rétabli.

## État des points de terminaison parents et enfants pour les profils imbriqués

Le tableau suivant décrit le comportement de surveillance de Traffic Manager pour les profils parents et enfants d’un profil imbriqué et le paramètre minChildEndpoints. Pour plus d'informations, consultez la rubrique [Qu’est-ce que Traffic Manager ?](traffic-manager-overview.md).

|État d’analyse des profils enfants|État d’analyse des points de terminaison parents|Remarques|
|---|---|---|
|Désactivé : vous avez désactivé le profil.|Arrêté|L’état des points de terminaison parents est Arrêté, pas Désactivé. L’état Désactivé sert uniquement à indiquer que vous avez désactivé le point de terminaison dans le profil parent.|
|Détérioré : un point de terminaison enfant au moins a l’état Détérioré.|État En ligne, si le nombre de points de terminaison En ligne du profil enfant correspond au moins à la valeur de l’état minChildEndpoints.CheckingEndpoint, si le nombre de points de terminaison En ligne et CheckingEndpoint du profil enfant correspond au moins à la valeur de l’état minChildEndpoints. Dans le cas contraire, l’état est Détérioré.|Le trafic est acheminé vers un point de terminaison dont l’état est CheckingEndpoint. Si la valeur minChildEndpoints définie est trop élevée, le point de terminaison parent est toujours détérioré.|
|En ligne : au moins un enfant a l’état En ligne, et aucun n’a l’état Détérioré.|Identique à ce qui précède.||
|CheckingEndpoints : au moins un enfant a l’état CheckingEndpoint ; aucun n’a l’état En ligne ou Détérioré.|Identique à ce qui précède.||
|Inactif : tous les points de terminaison ont l’état Désactivé ou Arrêté, ou ce profil n’inclut aucun point de terminaison.|Arrêté||

## Configuration de la surveillance pour un chemin d’accès et un nom de fichier spécifiques

1. Créez un fichier portant le même nom sur chaque point de terminaison que vous envisagez d’inclure dans votre profil.
2. Pour chaque point de terminaison, utilisez un navigateur web pour tester l’accès au fichier. L’URL est constituée du nom de domaine du point de terminaison spécifique (service cloud ou site web), du chemin d’accès au fichier et du nom de fichier. 
3. Dans le portail de gestion, sous **Paramètres de surveillance**, indiquez le chemin d’accès et le nom du fichier dans le champ **Chemin d’accès relatif et nom de fichier**.
4. Après avoir modifié la configuration, cliquez sur **Enregistrer** en bas de la page.

## Voir aussi

[Qu’est-ce que Traffic Manager ?](traffic-manager-overview.md)

[À propos des méthodes de routage du trafic de Traffic Manager](traffic-manager-load-balancing-methods.md)

[Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Sites Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Résolution des problèmes liés à l’état détérioré d’Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md)
 

<!---HONumber=Oct15_HO3-->