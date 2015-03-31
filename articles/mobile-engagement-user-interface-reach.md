<properties 
   pageTitle="Interface utilisateur d'Azure Mobile Engagement - Reach" 
   description="Vue d'ensemble de l'interface utilisateur pour la section Reach d'Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="mattgre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="required" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement - Interface utilisateur

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">Introduction</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">Navigation</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">Accueil</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">Mon compte</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">Analyse</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">Moniteur</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">Reach</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segments</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Tableau de bord</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Paramètres</a>
</div>

# <a name="Reach"></a>Reach
 
La section Reach de l'interface utilisateur est l'outil de gestion des campagnes Push qui vous permet de créer, modifier, activer, terminer ou surveiller et obtenir des statistiques relatives aux campagnes de notifications Push et des fonctionnalités. Vous pouvez accéder à celles-ci via l'API Reach (ainsi que certains éléments de l'API Push de bas niveau). Que vous utilisiez ces API ou l'interface utilisateur, il vous faudra intégrer Azure Mobile Engagement et Reach à votre application pour chaque plateforme à l'aide du Kit de développement logiciel (SDK) avant de pouvoir utiliser les campagnes Reach.

**Voir aussi :** 

-  [Documentation sur les API - API Reach][Link 4], [Documentation sur les API - API Push][Link 4], [Guide de résolution des problèmes - Push/Reach][Link 2]
-  <a href="#ReachCampaign" title="Reach Campaign">Campagne Reach</a>, <a href="#ReachCriterion" title="Reach Criterion">Critère Reach</a>, <a href="#ReachContent" title="Reach Content">Contenu Reach</a>
 
## Quatre types de notifications Push :
1.    Annonces : elles vous permettent d'envoyer aux utilisateurs des messages publicitaires qui les redirigent vers un autre emplacement dans votre application ou les envoient vers une page Web ou un Store en dehors de votre application. 
2.    Sondages : ces notifications vous permettent de regrouper des informations sur les utilisateurs finaux en leur posant des questions.
3.    Push de données : ces notifications vous permettent d'envoyer un fichier de données binaire ou base 64. Les informations contenues dans un Push de données sont envoyées à votre application pour modifier l'expérience actuelle des utilisateurs de votre application. Votre application doit être en mesure de traiter les données dans un Push de données.
4.    Vignettes (Windows Phone uniquement) : ces notifications vous permettent d'utiliser les services de notifications Push Microsoft (MPNS) pour envoyer des notifications Push Windows natives contenant des données XML. (Prises en charge depuis la version 0.9.0 du Kit de développement logiciel (SDK). La charge utile finale des vignettes ne peut excéder 32 kilo-octets.)

**Voir aussi :** 

-  [Concepts - Glossaire][Link 6]

## Trois catégories de statistiques en temps réel sont indiquées pour chaque campagne : 
1.    Envoyées par notification Push : combien de notifications Push ont été envoyées selon les critères indiqués dans la campagne. 
2.    Répondues : combien d'utilisateurs ont répondu à la notification en l'ouvrant en dehors de l'application ou en la fermant dans l'application. 
3.    Activées : combien d'utilisateurs ont cliqué sur le lien dans la notification pour être redirigés vers un nouvel emplacement dans l'application, vers un Store ou un navigateur web. 

> Remarque : Consultez plus de statistiques de campagne détaillées disponibles dans les statistiques de l'API Reach

**Voir aussi :** 

-  [Concepts - Glossaire][Link 6], [Documentation sur les API - API Reach - Statistiques][Link 4]


## Informations relatives à la campagne :
Vous pouvez modifier, cloner, supprimer ou activer les campagnes qui n'ont pas encore été activées en plaçant le curseur sur leurs noms ou en cliquant pour les ouvrir. Vous pouvez cloner les campagnes qui ont déjà été activées en plaçant le curseur sur leurs noms ou en cliquant pour les ouvrir. Toutefois, vous ne pouvez pas modifier une campagne une fois celle-ci activée.
 
![Reach1][18]

## Commentaire Reach :
Vous pouvez passer de l'affichage des informations à l'affichage des statistiques d'une campagne ouverte qui a déjà été activée. Vous pouvez également passer d'un affichage simple à un affichage avancé des statistiques pour voir plus d'informations détaillées (selon vos autorisations). Vous pouvez aussi utiliser les informations du commentaire Reach d'une campagne précédente comme critères de ciblage d'une nouvelle campagne. Les statistiques du commentaire Reach peuvent également être regroupées à l'aide de " Statistiques " dans l'API Reach. Vous pouvez également personnaliser l'audience de vos campagnes Push en fonction des campagnes précédentes.


**Voir aussi :** 

-  <a href="#ReachCampaign">Documentation sur l'interface utilisateur (IU) - Reach - Nouvelle campagne Push</a>, [Documentation sur les API - API Reach - Statistiques][Link 4]

![Reach2][19]

## <a name="ReachCampaign"></a>Reach - Nouvelles campagne Push :
 
Vous pouvez utiliser la section Reach de l'interface utilisateur pour créer une nouvelle campagne Push à l'aide d'une formule complexe en indiquant toutes les informations dont vous avez besoin pour envoyer une notification Push. Les options d'une campagne Push varient quelque peu selon les quatre types de campagne : Annonces, Sondages, Push de données et Vignettes (Windows Phone uniquement).

**L'option s'applique à :**

- Langues :    Toutes (Annonces, Sondages, Push de données et Vignettes)
- Campagne :    Toutes (Annonces, Sondages, Push de données et Vignettes)
- Notification :     Annonces, Sondages
- Contenu :    Unique pour chaque type de campagne
- Audience :     Toutes (Annonces, Sondages, Push de données et Vignettes)
- Délai d'exécution :     Annonces, Sondages, Vignettes
- Test :    Toutes (Annonces, Sondages, Push de données et Vignettes)
 
![Reach-Campaign1][20]

## Langues :
Vous pouvez utiliser le menu déroulant Langues pour envoyer une version différente de votre notification Push vers des appareils configurés pour d'autres langues. Par défaut, tous les appareils reçoivent la même notification Push, quelle que soit la langue pour laquelle ils sont configurés. Les utilisateurs qui disposent d'un appareil configuré pour une autre langue recevront la version de la notification Push dans la langue par défaut. De nombreuses options de campagne Push vous permettent de spécifier un contenu différent pour chaque langue supplémentaire que vous sélectionnez. 
 
![Reach-Campaign2][21]

**Les différences linguistiques s'appliquent à :**

- Langues :    Des langues uniques peuvent être sélectionnées en complément de la langue par défaut
- Campagne :    Identique pour toutes les langues
- Notification :    Unique pour chaque langue ajoutée en complément de la langue par défaut
- Contenu :    Unique pour chaque langue ajoutée en complément de la langue par défaut
- Audience :     Peut être filtrée par un critère de langue distinct.
- Délai d'exécution :     Identique pour toutes les langues
- Test :    Peut être envoyé pour chaque langue en même temps
 
**Langues prises en charge :**

- Arabe (ar) 
- Bulgare (bg) 
- Catalan (ca) 
- Chinois (zh) 
- Croate (hr) 
- Tchèque (cs) 
- Danois (da) 
- Néerlandais (nl) 
- Anglais (en) 
- Finnois (fi) 
- Français (fr) 
- Allemand (de) 
- Grec (el) 
- Hébreu (he) 
- Hindi (hi) 
- Hongrois (hu) 
- Indonésien (id) 
- Italien (it) 
- Japonais (ja) 
- Coréen (ko) 
- Letton (lv) 
- Lituanien (lt) 
- Malais (macro-langue) (ms) 
- Norvégien Bokmål (nb) 
- Polonais (pl) 
- Portugais (pt) 
- Roumain (ro) 
- Russe (ru) 
- Serbe (sr) 
- Slovaque (sk) 
- Slovène (sl) 
- Espagnol (es) 
- Suédois (sv) 
- Tagalog (tl) 
- Thaï (th) 
- Turc (tr) 
- Ukrainien (uk) 
- Vietnamien (vi) 
 
## Campagne :
Vous pouvez utiliser la section Campagne pour définir le nom et la catégorie de votre campagne et si vous envisagez d'ignorer la section Audience d'une campagne Push et d'envoyer cette campagne via l'API Reach (et certains éléments de l'API Push de bas niveau). Des catégories peuvent être utilisées avec un modèle de notification personnalisé pour contrôler les notifications dans l'application suivant des paramètres prédéfinis. Vous pouvez obtenir une liste de vos " Catégories " existantes via l'API Reach.

> Avertissement : Si vous utilisez l'option " Ignorer l'audience, la notification Push sera envoyée aux utilisateurs via l'API Reach " dans la section " Campagne " de la campagne Reach, cette campagne ne sera pas automatiquement envoyée. Vous devrez l'envoyer manuellement via l'API Reach.
 
![Reach-Campaign3][22]
 
**L'option s'applique à :**

- Nom :    Tout
- Category:    Annonces, Sondages
- Ignorer l'audience, la notification Push sera envoyée aux utilisateurs via l'API Reach :    Tout
 
## Notification :
Utilisez la section Notification pour définir les paramètres de base pour votre notification Push, y compris : Le titre de la notification Push, le message, une image dans l'application ou si elle peut être révocable. De nombreux paramètres de notification sont spécifiques à la plateforme de votre appareil. Vous pouvez choisir d'envoyer votre notification Push depuis l'application ou en dehors de l'application, ou les deux. (N'oubliez pas que les utilisateurs peuvent accepter ou refuser les notifications Push envoyées en dehors de l'application au niveau du système d'exploitation de leurs appareils. Azure Mobile Engagement ne peut pas remplacer ce paramètre. Rappelez-vous également que l'API Reach gère les notifications Push dans et en dehors de l'application L'API Push peut également être utilisée pour gérer les notifications Push envoyées en dehors de l'application.) Les notifications Push peuvent être personnalisées avec des images ou du contenu HTML, notamment des liens ciblés menant hors de votre application ou vers un autre emplacement dans votre application (Kit de développement logiciel (SDK) Android 2.1.0 ou catégories d'intention ultérieures requises). Vous pouvez modifier l'icône ou le badge iOS et envoyer du texte ou du contenu Web (une fenêtre contextuelle avec du contenu HTML, un lien URL vers un autre emplacement dans ou en dehors de l'application). Vous pouvez aussi faire sonner ou vibrer les appareils Android avec la notification Push. (Rappelez-vous que vous aurez besoin des bonnes autorisations du Kit de développement logiciel (SDK) dans votre fichier manifeste Android pour faire sonner ou vibrer un appareil.) Il n'y a actuellement aucune norme industrielle pour les tailles " Grand format " puisque la taille des écrans est différente pour chaque appareil. Toutefois, les images 400 x 100 fonctionnent sur presque toutes les tailles d'écran.

## Types d'envoi :
-    En dehors de l'application uniquement : la notification sera envoyée lorsque l'utilisateur n'utilisera pas son application.
- Ce type de notification requiert un certificat d'Apple ou de Google (certificat APNs ou GCM).
- Dans l'application uniquement : La notification apparaît uniquement lorsque l'application est exécutée.
- Cette notification utilise le système de messagerie Capptain pour contacter l'utilisateur. Vous pouvez personnaliser entièrement l'affichage ou la disposition de votre notification Push.
- À tout moment : Cette option garantit l'envoi de votre notification, que l'application soit exécutée ou non.

 
![Reach-Campaign4][23]

**L'option s'applique à :**

- Notification :     Annonces, Sondages
 
## Contenu :
Vous pouvez utiliser la section Contenu pour modifier le contenu de vos annonces, de vos sondages, de vos Push de données et de vos vignettes (Windows Phone uniquement). Le paramètre Contenu de vos campagnes Push est spécifique à chaque type de campagne. 

**Voir aussi :**

- <a href="#ReachContent">Documentation sur l'interface utilisateur - Reach - Contenu Push</a>
 
![Reach-Campaign5][24]

## Audience :
Utilisez la section Audience pour définir une liste d'éléments standard pour limiter votre campagne ou pour définir les limites de votre campagne en fonction des critères personnalisés. L'ensemble des options standard pour limiter votre audience vous permet d'envoyer des notifications Push aux nouveaux ou aux anciens utilisateurs, ou aux utilisateurs des Push natifs uniquement. Vous pouvez également définir un quota pour limiter le nombre d'utilisateurs recevant la notification Push. Vous pouvez modifier manuellement l'expression pour définir la façon dont votre campagne est filtrée afin d'inclure un ou plusieurs critères pour les utilisateurs cibles. Vous pouvez taper manuellement une expression d'audience. Ce type d'expression doit définir explicitement la relation entre les critères. Un critère est décrit par un identificateur qui doit commencer par une majuscule et ne peut pas contenir d'espace. La relation entre les critères peut être décrite à l'aide des opérateurs  'and', 'or', 'not' (et, ou, pas) ainsi que '(', ')'. Exemple : " Critère1 ou (Critère1 sans le critère2) ".

> Remarque : Si l'audience incluse dans les campagnes est importante, l'analyse de ciblage côté serveur peut être lente, en particulier si vous essayez de démarrer plusieurs campagnes en même temps.

- Si possible, ne démarrez qu'une campagne à la fois.
- Tout au plus, ne démarrez que quatre campagnes en même temps.
- Envoyez des notifications Push à vos utilisateurs actifs uniquement (case à cocher " Solliciter seulement les utilisateurs qui peuvent être contactés via un Push natif " et " Solliciter seulement les utilisateurs actifs ") de façon à ce que seuls les utilisateurs qui ont installé votre application et l'utilisent toujours doivent être analysés.
Une fois votre audience définie, vous pouvez utiliser le bouton de simulation pour savoir combien d'utilisateurs recevront cette notification Push. Ce bouton calculera le nombre d'utilisateurs connus potentiellement ciblés par cette audience (il s'agit d'une estimation basée sur un échantillon aléatoire d'utilisateurs). Notez que les utilisateurs qui ont désinstallé l'application font également partie de l'audience mais ne peuvent pas être contactés.

**Voir aussi :**

- <a href="#ReachCriterion">Documentation sur l'interface utilisateur - Reach - Nouveau critère Push</a>

![Reach-Campaign6][25]

## Modifier l'expression
 
![Reach-Campaign7][26]
 
**L'option de limitation de votre audience s'applique à :**

- Solliciter uniquement un sous-ensemble d'utilisateurs :    Toutes (Annonces, Sondages, Push de données et Vignettes)
- Solliciter uniquement d'anciens utilisateurs :    Toutes (Annonces, Sondages, Push de données et Vignettes)
- Solliciter uniquement de nouveaux utilisateurs :    Toutes (Annonces, Sondages, Push de données et Vignettes)
- Solliciter uniquement des utilisateurs inactifs :    Annonces, Sondages, Vignettes
- Solliciter uniquement des utilisateurs actifs :    Toutes (Annonces, Sondages, Push de données et Vignettes)
- Solliciter uniquement des utilisateurs qui peuvent être contactés via un Push natif :     Annonces, Sondages
 
## Délai d'exécution :
Vous pouvez utiliser la section Délai d'exécution pour définir à quel moment la notification Push sera envoyée ou vous pouvez laisser le champ Délai d'exécution vide pour démarrer immédiatement la campagne. Notez que l'utilisation du fuseau horaire de l'utilisateur final peut faire démarrer la campagne un jour plus tôt que prévu pour vos utilisateurs en Asie et envoyer des petits lots de notifications Push à une certaine heure jusqu'à ce que tous les fuseaux horaires du monde correspondent au délai d'exécution défini pour votre campagne. L'utilisation du fuseau horaire des utilisateurs finaux peut également entraîner des retards dans les campagnes puisque cela nécessite de connaître l'heure du téléphone avant de démarrer l'envoi des notifications Push.

> Remarque : Les campagnes qui n'ont pas de date de fin peuvent mettre en cache des notifications Push localement et continuer de les afficher une fois que vous avez terminé manuellement ces campagnes. Pour éviter cela, indiquer une date de fin pour ces campagnes.

**Voir aussi :**

- [Procédures - Planification][Link 3] 
 
![Reach-Campaign8][27]

**Les paramètres s'appliquent à :**

- Délai d'exécution :     Annonces, Sondages, Vignettes
 
## Test :
Vous pouvez utiliser la section Test pour envoyer cette notification Push à votre propre appareil de test avant d'enregistrer la campagne. Si vous avez configuré des langues personnalisées pour cette campagne, vous pouvez tester la notification Push pour ces langues. Vous pouvez configurer un appareil de test depuis " Mon compte ".
> Remarque : Aucune donnée côté serveur n'est enregistrée lorsque vous utilisez le bouton pour " tester " les notifications Push. Les données sont enregistrées uniquement pour les vraies campagnes de notification Push.

**Voir aussi :**

- [Documentation sur l'interface utilisateur - Mon compte][Link 14]
 
![Reach-Campaign9][28]

## <a name="ReachCriterion"></a>Reach - Nouveau critère Push (pour une audience ciblée)

Le ciblage de votre audience suivant des critères spécifiques à l'aide du bouton " Nouveau critère " est l'un des concepts les plus performants d'Azure Mobile Engagement qui vous permet d'envoyer des notifications Push pertinentes auxquelles les clients répondront, au lieu d'envoyer des courriers indésirables à tout le monde. Vous pouvez limiter votre audience selon des critères standard et simuler des notifications Push pour déterminer combien de personnes recevront ces notifications.

**Voir aussi :**

- <a href="#ReachCampaign">Documentation sur l'interface utilisateur - Reach - Nouvelle campagne Push</a>

## Les critères d'audience peuvent inclure :

- **Des informations techniques :** Vous pouvez cibler votre audience en vous basant sur les mêmes informations techniques indiquées dans les sections Analyses et Moniteur. **Voir aussi :** [Documentation sur l'interface utilisateur - Analyses][Link 15], [Documentation sur l'interface utilisateur - Moniteur][Link 16]
- **Emplacement :** les applications qui utilisent un " Rapport de localisation en temps réel " avec le geofencing (ou géorepérage) peuvent utiliser la géolocalisation comme critère pour cibler une audience à partir d'une localisation GPS. Un " Rapport de localisation de zone différé " peut également être utilisé pour cibler une audience à partir de la localisation du téléphone cellulaire (Le " Rapport de localisation en temps réel " et le " Rapport de localisation de zone différé " doivent être activés à partir du Kit de développement logiciel (SDK)).**Consultez également :** [Documentation relative au Kit de développement logiciel (SDK) - iOS - Intégration][Link 5], [Documentation relative au Kit de développement logiciel (SDK) - Android - Intégration][Link 5]
- **Commentaire Reach :** vous pouvez cibler votre audience selon les commentaires des précédentes notifications Reach via les commentaires Reach dans les sections Annonces, Sondages et Push de données. Cela vous permet de mieux cibler votre audience après deux ou trois campagnes Reach, en comparaison à la première. Cela peut aussi être utilisé pour filtrer les utilisateurs qui ont déjà reçu une notification avec un contenu similaire en définissant une campagne à NE PAS envoyer aux utilisateurs ayant déjà reçu une campagne spécifique précédente. Vous pouvez même exclure les utilisateurs faisant partie d'une campagne spécifique toujours active **Consultez également :** la <a href="#ReachContent">Documentation sur l'interface utilisateur - Reach - Contenu Push</a>
- **Installation du suivi :** vous pouvez suivre des informations selon l'emplacement où les utilisateurs ont installé votre application. **Consultez également :** [Documentation sur l'interface utilisateur - Paramètres][Link 20]
- **Profil utilisateur :** vous pouvez cibler votre audience en fonction des informations d'utilisateur standard et des informations d'application personnalisées que vous avez créées. Cela inclut les utilisateurs qui sont actuellement connectés et les utilisateurs qui ont répondu à des questions spécifiques que vous leur avez demandé de définir dans l'application même, au lieu de leur demander comment ils ont répondu aux campagnes précédentes. Toutes les informations définies pour votre application sont indiquées dans cette liste.
- Segments : Vous pouvez également cibler votre audience en fonction des segments que vous avez créés suivant le comportement spécifique des utilisateurs et contenant des critères multiples. Tous les segments définis pour votre application sont indiqués dans cette liste. **Voir aussi :** [Documentation sur l'interface utilisateur - Segments][Link 18]
- **Informations relatives à l'application :** les balises d'informations personnalisées relatives à l'application peuvent être créées depuis la section " Paramètres " afin de suivre le comportement des utilisateurs. **Consultez également :** [Documentation sur l'interface utilisateur - Paramètres][Link 20]
## Exemple : 
Si vous souhaitez effectuer une transmission de type Push pour faire une annonce uniquement à un sous-ensemble de vos utilisateurs qui ont réalisé un achat dans l'application :

1. Rendez-vous sur la page des paramètres de l'application, sélectionnez le menu " Informations relatives à l'application " puis sélectionnez " Nouvelle information relative à l'application ".
2. Enregistrez une nouvelle information booléenne relative à l'application appelée " inAppPurchase ".
3. Cette information relative à l'application doit être définie sur " vrai " par votre application lorsque l'utilisateur réussit à réaliser un achat dans l'application (à l'aide de la fonction sendAppInfo (" inAppPurchase ", ...)).
4. Si vous ne souhaitez pas faire cela depuis votre application, vous pouvez le faire depuis le serveur principal à l'aide de l'API de l'appareil.
5. Vous n'aurez ensuite qu'à créer votre annonce avec un critère limitant votre audience aux utilisateurs ayant " inAppPurchase " défini sur " vrai ".
 
> Remarque : Le ciblage basé sur un autre critère que les balises d'informations relatives à l'application nécessite un regroupement d'informations par Azure Mobile Engagement à partir des appareils de vos utilisateurs avant que la notification Push ne soit envoyée et qu'elle n'entraîne un retard. Les options de configuration de notifications Push complexes (telles que la mise à jour des badges) peuvent également entraîner des retards. L'utilisation de la campagne unique depuis l'API Push est la méthode Push la plus rapide dans Azure Mobile Engagement. L'utilisation des balises d'informations relatives à l'application en tant que critères Push pour une campagne Reach (depuis l'API Reach ou l'IU) est la deuxième méthode la plus rapide puisque ces balises d'informations sont stockées côté serveur. L'utilisation d'autres critères de ciblage pour une campagne Push est la méthode la plus flexible mais la plus lente, car Azure Mobile Engagement doit interroger les appareils afin d'envoyer la campagne.
 
![Reach-Criterion1][29] 

**Les options de critère s'appliquent à :**

- **Informations techniques**     
- Nom du microprogramme :    Nom du microprogramme
- Version du microprogramme :    Version du microprogramme
- Modèle de l'appareil :    Modèle de l'appareil
- Fabricant de l'appareil :    Fabricant de l'appareil
- Version de l'application :    Version de l'application
- Nom de l'opérateur :    Nom de l'opérateur, non défini
- Pays de l'opérateur :    Pays de l'opérateur, non défini
- Type de réseau :    Type de réseau
- Paramètres régionaux :    Paramètres régionaux
- Taille de l'écran :    Taille de l'écran
- **Emplacement**      
- Dernière région connue :    Pays, région, localité
- Géofencing en temps réel :    Liste des POI (points d'intérêts) (nom, actions), POI circulaires (nom, latitude, longitude, rayon en mètres)
- **Commentaire Reach**     
- Commentaire de l'annonce :    Annonce, commentaire
- Commentaire du sondage :    Sondage, commentaire
- Commentaire de réponse du sondage :    Commentaire de réponse du sondage, question, choix
- Commentaire du Push de données :    Push de données, commentaire
- **Installation du suivi**     
- Store :    Store, non défini
- Source :    Source, non défini
- **Profil utilisateur**     
- Sexe :    masculin ou féminin, non défini
- Date de naissance :    opérateur, date, non défini
- Accepter :    vrai ou faux, non défini
- **Information de l'application**      
- Chaîne :    Chaîne, non défini
- Date :    opérateur, date, non défini
- Entier :    opérateur, nombre, non défini
- Booléen :    vrai ou faux, non défini
- **Segment**    
- Nom des segments (de la liste déroulante), exclusion (utilisateurs cibles qui ne font pas partie de ce segment).

## <a name="ReachContent"></a>Reach - Contenu Push (pour chaque type de campagne)
 
Vous pouvez utiliser la section Contenu d'une nouvelle campagne Reach pour modifier le contenu de vos annonces, sondages, Push de données et vignettes (Windows Phone uniquement). Le paramètre du contenu des campagnes Push est spécifique au type de campagne. 
 
## Types de contenu
- Annonces
- Sondages
- Push de données
- Vignettes (Windows Phone uniquement)
 
## Contenu des annonces
 
 ![Reach-Content1][30] 

## Choisissez le type de votre annonce :
-    Notification uniquement : Il s'agit d'une simple notification standard. Cela signifie que si un utilisateur clique dessus, aucun affichage supplémentaire n'apparaîtra. Seule l'action qui y est associée s'affichera.
-    Annonce de texte : Il s'agit d'une notification qui invite l'utilisateur à regarder l'affichage texte.
-    Annonce Web : Il s'agit d'une notification qui invite l'utilisateur à regarder l'affichage Web.

**Voir aussi :**

- [Procédures - Annonces][Link 3] 

**À propos des annonces d'affichage Web :**

Les occurrences du modèle "{deviceid}" dans le code HTML ou le code JavaScript que vous fournissez ici seront automatiquement remplacées par l'identificateur de l'appareil qui affiche l'annonce. Il s'agit d'un moyen simple pour récupérer les identificateurs d'appareil Azure Mobile Engagement dans un service Web externe hébergé dans votre arrière-guichet.
Si vous souhaitez créé un affichage Web plein écran (sans utiliser les boutons par défaut Action et Quitter que nous offrons), vous pouvez utiliser les fonctions suivantes depuis le code JavaScript de votre annonce d'affichage Web : 

-    effectuez l'action de l'annonce : ReachContent.actionContent()
-    quitter l'annonce : ReachContent.exitContent()
 
## Choisissez votre action :

**À propos des URL d'action :**

Toute URL qui peut être interprétée par le système d'exploitation d'un appareil cible peut être utilisée comme une URL d'action.
Toute URL dédiée pouvant être prise en charge par votre application (par exemple pour permettre à vos utilisateurs de passer à un écran spécifique) peut également être utilisée comme URL d'action.
Chaque occurrence du modèle {deviceid} est automatiquement remplacée par l'identificateur de l'appareil réalisant l'action. Cela peut être utilisé pour récupérer facilement des identificateurs d'appareil Azure Mobile Engagement via un service Web externe hébergé dans votre arrière-guichet.

- **Actions Android et iOS**
    - Ouvrir une page Web
    - http://[web-site-domain] 
    - Exemple:http://www.azure.com
    - Envoyer un courrier électronique
    - mailto :[destinataire-courrier-électronique]?subject=[sujet]&body=[message] 
    - Exemple :mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Envoyer un SMS
    - SMS :[numéro-téléphone] 
    - Exemple :sms:2125551212
    - Composer un numéro de téléphone
    - tél. :[numéro-téléphone] 
    - Exemple :tel:2125551212
- **Actions Android uniquement**
    - Télécharger une application du Play Store
    - marché ://details?id=[package d'application] 
    - Exemple :market://details?id=com.microsoft.office.word
    - Démarrer une recherche géolocalisée
    - geo:0,0?q=[requête de recherche] 
    - Exemple :geo:0,0?q=starbucks,paris
- **Actions iOS uniquement**
    - Télécharger une application depuis le Magasin d'applications
    - http://itunes.apple.com/[pays]/app/[nom_d'application]/id[id_application]?mt=8 
    - Exemple:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Actions Windows
    - Ouvrir une page Web
    - http://[web-site-domain] 
    - Exemple:http://www.azure.com
    - Envoyer un courrier électronique
    - mailto :[destinataire-courrier-électronique]?subject=[sujet]&body=[message] 
    - Exemple :mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Envoyer un SMS (application Skype du Store requise)
    - SMS :[numéro-téléphone] 
    - Exemple :sms:2125551212
    - Composer un numéro de téléphone (application Skype du Store requise)
    - tél. :[numéro-téléphone] 
    - Exemple :tel:2125551212
    - Télécharger une application du Play Store
    - ms-windows-store:PDP?PFN=[ID package d'application] 
    - Exemple :ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Démarrer une recherche Bing Cartes
    - Bing Cartes :?q=[requête de recherche] 
    - Exemple :bingmaps:?q=starbucks,paris
    - Utiliser un modèle personnalisé
    - [modèle personnalisé]://[paramètres du modèle personnalisé] 
    - Exemple :myCustomProtocol://myCustomParams
    - Utiliser un package de données (application du Store pour la lecture d'extension requise)
    - [dossier][données].[extension] 
    - Exemple :myfolderdata.txt
 
## Génération d'une URL de suivi :
-    Consultez la section " Paramètres " de la <Documentation sur l'interface utilisateur> pour les instructions relatives à la génération d'une URL de suivi qui permettra aux utilisateurs de télécharger une autre de vos applications.
 
## Définition du texte de votre annonce
Remplissez le titre, le contenu et le texte des boutons de votre annonce. 
Vous pouvez cibler une audience pour une future campagne suivant les commentaires Reach indiquant la façon dont les utilisateurs ont répondu à cette campagne. Le ciblage de l'audience peut se baser sur les commentaires déterminant si la campagne a été envoyée par notification Push, si elle a obtenu une réponse, si elle a été activée ou quittée.

**Voir aussi :**
- <a href="#ReachCriterion">Documentation sur l'interface utilisateur - Reach - Nouveau critère Push</a>

## Contenu des sondages
 
![Reach-Content2][31] 
Remplissez le titre, la description et le texte des boutons de votre annonce. 
Ajoutez ensuite des questions et des choix de réponse pour ces questions.
Vous pouvez cibler une audience pour une future campagne suivant les commentaires Reach indiquant la façon dont les utilisateurs ont répondu à cette campagne. Le ciblage de l'audience est déterminé selon que la campagne est issue d'une transmission de type push, qu'elle a obtenu une réponse, qu'elle a été activée ou quittée. Le ciblage de l'audience peut également se baser sur les commentaires de réponse du sondage pour lequel la question à choix multiple est utilisée comme critère.

**Voir aussi :**

- <a href="#ReachCriterion">Documentation sur l'interface utilisateur - Reach - Nouveau critère Push</a>
 
## Contenu des Push de données
 
![Reach-Content3][32] 

## Choisissez le type de vos données :
- Texte
- Données binaires
- Données Base64

## Définition du contenu de vos données
- Si vous sélectionnez une transmission de type push des données de texte, copiez et collez le texte dans la boîte " contenu ".
- Si vous sélectionnez une transmission de type push de données binaires ou base64, utilisez le bouton " Télécharger votre fichier " pour télécharger votre fichier.
- Vous pouvez cibler une audience pour une future campagne suivant les commentaires Reach indiquant la façon dont les utilisateurs ont répondu à cette campagne. Le ciblage de l'audience est déterminé selon que la campagne est issue d'une transmission de type push, qu'elle a obtenu une réponse, qu'elle a été activée ou quittée.

**Voir aussi :**

- <a href="#ReachCriterion">Documentation sur l'interface utilisateur - Reach - Nouveau critère Push</a>

## Contenu des vignettes (Windows Phone uniquement)

![Reach-Content4][33]

## Définition du contenu de vos vignettes
La charge utile de la vignette correspond au texte qui s'affichera dans la vignette de votre application sur les appareils Windows Phone.
Un Push de vignette est la version du service de notifications Push de Microsoft (MPNS) d'un Push natif pour Windows Phone. Ce type de Push de vignette est le seul type de push qui n'a pas de réponse. L'audience des futures campagnes ne peut donc pas être déterminée en fonction des résultats d'une campagne de Push de vignette. 

**Voir aussi :**

- [Documentation sur les API- API Reach - Push natif][Link 4]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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
