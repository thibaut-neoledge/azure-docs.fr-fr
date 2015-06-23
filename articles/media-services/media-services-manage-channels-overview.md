<properties 
	pageTitle="Utilisation d'encodeurs locaux pour envoyer un flux dynamique à débit binaire multiple vers un canal" 
	description="Cette rubrique explique comment configurer un canal qui reçoit un flux dynamique à débit binaire multiple en provenance d'un encodeur local. Le flux de données peut ensuite être distribué aux applications de lecture clientes via un ou plusieurs points de terminaison de diffusion en continu à l'aide d'un des protocoles de diffusion en continu adaptative suivants : HLS, Smooth Streaming, MPEG DASH, HDS." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="juliako"/>

# Utilisation d'encodeurs locaux pour envoyer un flux dynamique à débit binaire multiple vers un canal

## Vue d'ensemble

Dans Azure Media Services (AMS), un **canal** représente un pipeline de traitement du contenu vidéo en flux continu. Un **programme** vous permet de contrôler la publication et le stockage des segments dans un flux dynamique. Les canaux gèrent des programmes. La relation entre canal et programme est très similaire au contenu multimédia traditionnel où un canal présente un flux de contenu constant et un programme est limité à un événement minuté sur ce canal. 

Lorsque vous utilisez la vidéo en flux continu, un des composants impliqués dans le flux de travail est un encodeur vidéo dynamique convertissant les signaux de la caméra en flux de données qui sont envoyés à un service de diffusion vidéo en flux continu. Un **canal** peut recevoir des flux d'entrée dynamiques en provenance d'un encodeur dynamique local qui produit un flux à débit binaire multiple au format MP4 fragmenté (Smooth Streaming) ou RTMP. Les encodeurs dynamiques suivants peuvent être utilisés pour produire un flux au format Smooth Streaming : Elemental, Envivio, Cisco.  Les encodeurs suivants produisent un flux au format RTMP : Adobe Flash Live, Telestream Wirecast et transcodeurs Tricaster. Les flux reçus transitent par les **canaux** sans traitement supplémentaire. L'encodeur dynamique peut également recevoir un flux à débit binaire unique, mais comme le flux de données n'est pas traité, les applications clientes recevront également un flux à débit binaire unique (cette option n'est pas recommandée). Une fois le contenu reçu publié, il peut être transmis en continu aux applications de lecture clientes via un ou plusieurs **points de terminaison de diffusion en continu**. Les protocoles de diffusion adaptative suivants peuvent être utilisés pour lire le flux de données : HLS, MPEG DASH, Smooth Streaming, HDS. 

Un **point de terminaison de diffusion en continu** représente un service de diffusion en continu qui peut fournir du contenu directement à une application de lecteur cliente ou à un réseau de distribution de contenu (CDN) pour être redistribué. Le flux sortant d'un service de point de terminaison de diffusion en continu peut être un flux dynamique ou une ressource de vidéo à la demande dans votre compte Media Services. En outre, vous pouvez contrôler la capacité du service de point de terminaison de diffusion en continu afin de gérer les besoins croissants en matière de bande passante en ajustant les unités réservées de diffusion en continu. Vous devez allouer au moins une unité réservée pour les applications au sein d'un environnement de production. Pour plus d'informations, consultez [Mise à l'échelle d'un service de média](media-services-manage-origins.md#scale_streaming_endpoints).

Le diagramme suivant illustre un workflow de vidéo en flux continu utilisant un encodeur dynamique local pour produire des flux à débit binaire multiple au format MP4 fragmenté (Smooth Streaming) ou RMTP. 

![Live workflow][live-overview]

Cette rubrique donne une vue d'ensemble d'un canal qui reçoit un flux dynamique à débit binaire multiple en provenance d'un encodeur local. Elle présente également les composants liés aux canaux.

## <a id="scenarios"></a>Scénario courant de vidéo en flux continu

Les étapes suivantes décrivent les tâches impliquées dans la création d'applications courantes de vidéo en flux continu.

1. Connectez une caméra vidéo à un ordinateur. Lancez et configurez un encodeur dynamique local qui produit un flux à débit binaire multiple au format MP4 fragmenté (Smooth Streaming) ou RTMP. Pour plus d'informations, consultez la page [Prise en charge RTMP et encodeurs dynamiques dans Azure Media Services](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Cette étape peut également être effectuée après la création du canal.

1. Créez et démarrez un canal.
1. Récupérez l'URL de réception du canal. 

	L'URL de réception est utilisée par l'encodeur dynamique pour envoyer le flux au canal.
1. Récupérez l'URL d'aperçu du canal. 

	Utilisez cette URL pour vérifier que votre canal reçoit correctement le flux dynamique.

2. Créez une ressource.
3. Si vous souhaitez que la ressource soit chiffrée dynamiquement pendant la lecture, procédez comme suit : 	
	
	1. 	Créez une clé de contenu. 
	1. 	Configurez la stratégie d'autorisation de la clé de contenu.
1. Configurez la stratégie de remise de ressources (utilisée par l'empaquetage dynamique et le chiffrement dynamique).
3. Créez un programme et spécifiez l'utilisation de la ressource créée.
1. Publiez la ressource associée au programme en créant un localisateur OnDemand.  

	Assurez-vous que vous disposez d'au moins une unité réservée de diffusion en continu sur le point de terminaison de diffusion en continu à partir duquel vous souhaitez diffuser le contenu.
1. Démarrez le programme dès que vous êtes prêt à lancer la diffusion en continu et l'archivage.
1. Arrêtez le programme chaque fois que vous voulez arrêter la diffusion et archiver l'événement.
1. Supprimez le programme (et éventuellement la ressource).   

La section [Tâches de diffusion vidéo en flux continu](media-services-manage-channels-overview.md#tasks) offre des liens vers des rubriques expliquant comment effectuer les tâches décrites ci-dessus.


## <a id="channel_input"></a>Configurations de l'entrée (réception) des canaux

### <a id="ingest_protocols"></a>Protocoles de diffusion en continu de réception

Media Services prend en charge la réception des flux dynamiques à l'aide des protocoles de diffusion en continu suivants : 

- **MP4 fragmenté à débit binaire multiple**
 
- **RTMP à débit binaire multiple** 

	Lorsque le protocole de diffusion en continu de réception **RTMP** est sélectionné, deux points de terminaison de réception (entrée) sont créés pour le canal : 
	
	**URL principale** : Spécifie l'URL complète du point de terminaison de réception RTMP principal du canal.

	**URL secondaire**(facultatif) : Spécifie l'URL complète du point de terminaison de réception RTMP secondaire du canal. 

	Utilisez l'URL secondaire pour améliorer la durabilité et la tolérance de panne de votre flux de réception, ainsi que le basculement et la tolérance de panne de l'encodeur. 
	
	- Pour améliorer la durabilité et la tolérance de panne de réception :
		
		Utilisez un encodeur pour envoyer les mêmes données aux URL de réception principale et secondaire. La plupart des encodeurs RTMP (par exemple, Flash Media Encoder ou Wirecast) permettent d'utiliser des URL principale et secondaire.

	- Pour gérer le basculement et la tolérance de panne de l'encodeur :
		
		Utilisez plusieurs encodeurs pour générer les mêmes données et les envoyer aux URL de réception principale et secondaire. Cette approche améliore à la fois la durabilité de réception et la haute disponibilité de l'encodeur. REMARQUE : l'encodeur doit prendre en charge les scénarios de haute disponibilité et son heure interne doit être synchronisée (pour plus d'informations, reportez-vous au manuel de votre encodeur).
	
 	L'utilisation d'une URL de réception secondaire nécessite de la bande passante supplémentaire. 

Notez que vous pouvez recevoir un flux à débit binaire unique dans votre canal, mais comme le flux de données n'est pas traité par le canal, les applications clientes recevront également un flux à débit binaire unique (cette option n'est pas recommandée).

Pour plus d'informations sur les encodeurs dynamiques RTMP, consultez la page [Prise en charge RTMP et encodeurs dynamiques dans Azure Media Services](http://go.microsoft.com/fwlink/?LinkId=532824).

Les considérations suivantes s'appliquent :

- Assurez-vous que vous disposez d'une connectivité Internet libre suffisante pour envoyer des données aux points de réception. 
- Le flux à débit binaire multiple entrant peut présenter un maximum de 10 niveaux de qualité vidéo (également appelés couches) et un maximum de 5 pistes audio.
- Le débit binaire moyen le plus élevé pour les niveaux ou couches de qualité vidéo doit être inférieur à 10 Mbit/s.
- La somme des débits binaires moyens de tous les flux vidéo et audio doit être inférieure à 25 Mbit/s.
- Vous ne pouvez pas modifier le protocole d'entrée pendant l'exécution du canal ou de ses programmes associés. Si vous avez besoin d'autres protocoles, vous devez créer des canaux distincts pour chaque protocole d'entrée. 


### URL (points de terminaison) de réception 

Un canal fournit un point de terminaison d'entrée (URL de réception) que vous spécifiez dans l'encodeur dynamique pour que ce dernier puisse transmettre les flux à vos canaux.   

Vous pouvez obtenir les URL de réception lors de la création du canal. Pour obtenir ces URL, il n'est pas obligatoire que le canal soit à l'état démarré. Lorsque vous êtes prêt à commencer l'envoi de données dans le canal, ce dernier doit être démarré. Une fois que le canal commence à recevoir les données, vous pouvez prévisualiser votre flux via l'URL d'aperçu.

Vous avez la possibilité de recevoir un flux dynamique au format MP4 fragmenté (Smooth Streaming) via une connexion SSL. Pour assurer la réception via SSL, veillez à mettre à jour l'URL de réception pour HTTPS. Il n'est pas possible de recevoir un flux RTMP via SSL pour le moment. 

### <a id="keyframe_interval"></a>Intervalle d'image clé

Lorsque vous utilisez un encodeur dynamique local pour générer un flux à débit binaire multiple, l'intervalle d'image clé spécifie la durée du GOP (tel qu'il est utilisé par cet encodeur externe). Une fois ce flux entrant reçu par le canal, vous pouvez distribuer votre flux dynamique aux applications de lecture clientes dans un des formats suivants : Smooth Streaming, DASH et HLS. Lors de la diffusion en continu, TLS est toujours empaquetée de façon dynamique. Par défaut, Media Services calcule automatiquement le coefficient d'empaquetage de segment HLS (fragments par segment) en fonction de l'intervalle d'image clé, également appelé groupe d'images (GOP), reçu de l'encodeur dynamique. 

Le tableau suivant montre le mode de calcul de la durée du segment :

<table border="1">
<tr><th>Intervalle d'image clé</th><th>Coefficient d'empaquetage de segment HLS (FragmentsPerSegment)</th><th>Exemple</th></tr>
<tr><td>Inférieur ou égal à 3 secondes</td><td>3</td><td>Si la valeur de KeyFrameInterval (ou groupe d'images) est de 2 secondes, le coefficient d'empaquetage de segment HLS est égal à 3, ce qui a pour effet de créer un segment HLS de 6 secondes.</td></tr>
<tr><td>3 à 5 secondes</td><td>2</td><td>Si la valeur de KeyFrameInterval (ou groupe d'images) est de 4 secondes, le coefficient d'empaquetage de segment HLS est égal à 2, ce qui a pour effet de créer un segment HLS de 8 secondes.</td></tr>
<tr><td>Supérieur à 5 secondes</td><td>1</td><td>Si la valeur de KeyFrameInterval (ou groupe d'images) est de 6 secondes, le coefficient d'empaquetage de segment HLS est égal à 1, ce qui a pour effet de créer un segment HLS de 6 secondes.</td></tr>
</table>

Vous pouvez modifier le coefficient de fragments par segment en configurant la sortie du canal et le paramètre FragmentsPerSegment sur ChannelOutputHls. 

Vous pouvez également modifier la valeur d'intervalle d'image clé en définissant la propriété KeyFrameInterval sur ChannelInput. 

Si vous définissez explicitement la valeur KeyFrameInterval, le coefficient d'empaquetage de segment HLS FragmentsPerSegment est calculé à l'aide des règles décrites ci-dessus.  

Si vous définissez explicitement les valeurs KeyFrameInterval et FragmentsPerSegment, Media Services utilise les valeurs que vous avez définies. 


### Adresses IP autorisées

Vous pouvez définir les adresses IP autorisées à publier du contenu vidéo sur ce canal. Les adresses IP autorisées peuvent être définies sous forme d'adresse IP unique (ex. : 10.0.0.1), de plage IP constituée d'une adresse IP et d'un masque de sous-réseau CIDR (ex. : 10.0.0.1/22) ou de plage IP constituée d'une adresse IP et d'un masque de sous-réseau au format décimal séparé par des points (ex. : 10.0.0.1(255.255.252.0)). 

Si aucune adresse IP n'est spécifiée et qu'il n'existe pas de définition de règle, alors aucune adresse IP ne sera autorisée. Pour autoriser toutes les adresses IP, créez une règle et définissez la valeur 0.0.0.0/0.

## Aperçu du canal 

### URL d'aperçu

Les canaux fournissent un point de terminaison d'aperçu (URL d'aperçu) permettant de prévisualiser et de valider le flux avant de lui appliquer un traitement supplémentaire et de le distribuer.

Vous pouvez obtenir l'URL d'aperçu lors de la création du canal. Pour obtenir l'URL, il n'est pas obligatoire que le canal soit à l'état démarré. 

Une fois que le canal commence à recevoir les données, vous pouvez prévisualiser votre flux.

Notez que pour le moment, le flux d'aperçu ne peut être distribué qu'au format MP4 fragmenté (Smooth Streaming), quel que soit le type d'entrée spécifié. Vous pouvez utiliser le lecteur [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) pour tester la diffusion au format Smooth Streaming. Vous pouvez également utiliser un lecteur hébergé dans le portail de gestion Azure pour afficher votre flux.


### Adresses IP autorisées

Vous pouvez définir les adresses IP autorisées à se connecter au point de terminaison d'aperçu. Si aucune adresse IP n'est spécifiée, alors toutes les adresses IP seront autorisées. Les adresses IP autorisées peuvent être définies sous forme d'adresse IP unique (ex. : 10.0.0.1), de plage IP constituée d'une adresse IP et d'un masque de sous-réseau CIDR (ex. : 10.0.0.1/22) ou de plage IP constituée d'une adresse IP et d'un masque de sous-réseau au format décimal séparé par des points (ex. : 10.0.0.1(255.255.252.0)).

## Sortie du canal

Pour plus d'informations, consultez la section [Configuration de l'intervalle d'image clé](#keyframe_interval).


## Programmes du canal

Un canal est associé à des programmes vous permettant de contrôler la publication et le stockage des segments dans un flux dynamique. Les canaux gèrent des programmes. La relation entre canal et programme est très similaire au contenu multimédia traditionnel où un canal a un flux de contenu constant et un programme est limité à un événement minuté sur ce canal.

Vous pouvez spécifier le nombre d'heures pendant lesquelles vous souhaitez conserver le contenu enregistré pour le programme en définissant la durée de la **fenêtre d'archivage**. Cette valeur peut être comprise entre 5 minutes et 25 heures. La durée de la fenêtre d'archivage détermine également la plage maximale de temps dans laquelle les clients peuvent effectuer des recherches en arrière à partir de la position dynamique actuelle. Les programmes peuvent durer davantage que le laps de temps spécifié, mais le contenu qui se situe en dehors de la longueur de fenêtre est ignoré en permanence. La valeur de cette propriété détermine également la longueur maximale que les manifestes de client peuvent atteindre.

Chaque programme est associé à un élément multimédia. Pour publier le programme, vous devez créer un localisateur OnDemand pour l'élément multimédia associé. Le fait de posséder ce localisateur vous permettra de générer une URL de diffusion en continu que vous pourrez fournir à vos clients.

Un canal prend en charge jusqu'à trois programmes exécutés simultanément, ce qui rend possible la création de plusieurs archives du même flux entrant. Cela vous permet de publier et d'archiver différentes parties d'un événement en fonction des besoins. Par exemple, imaginez que vous devez archiver 6 heures d'un programme, mais diffuser uniquement les 10 dernières minutes. Pour ce faire, vous devez créer deux programmes exécutés simultanément. Un programme est configuré pour archiver 6 heures de l'événement, mais il n'est pas publié. L'autre programme est configuré pour archiver pendant 10 minutes et il est publié.

Vous ne devez pas réutiliser de programmes existants pour de nouveaux événements. Au lieu de cela, créez et démarrez un nouveau programme pour chaque événement, tel que décrit dans la section Programmation d'applications de vidéo en flux continu.

Démarrez le programme dès que vous êtes prêt à lancer la diffusion en continu et l'archivage. Arrêtez le programme chaque fois que vous voulez arrêter la diffusion et archiver l'événement. 

Pour supprimer du contenu archivé, arrêtez et supprimez le programme, puis supprimez l'élément multimédia associé. Un élément multimédia ne peut pas être supprimé s'il est utilisé par un programme ; le programme doit d'abord être supprimé. 

Même après l'arrêt et la suppression du programme, les utilisateurs pourront lire votre contenu archivé en tant que vidéo à la demande tant que vous n'aurez pas supprimé l'élément multimédia.

Si vous souhaitez conserver le contenu archivé sans qu'il soit disponible pour la diffusion, supprimez le localisateur de diffusion en continu.

## État du canal

État actuel du canal. Les valeurs possibles incluent :

- Arrêté. C'est l'état initial du canal après sa création. Dans cet état, les propriétés du canal peuvent être mises à jour, mais la diffusion en continu n'est pas autorisée.
- Démarrage en cours. Le canal est en cours de démarrage. Aucune mise à jour ou diffusion en continu n'est autorisée durant cet état. Si une erreur se produit, le canal retourne à l'état Arrêté.
- En cours d'exécution. Le canal est capable de traiter des flux dynamiques.
- En cours d'arrêt. Le canal est en cours d'arrêt. Aucune mise à jour ou diffusion en continu n'est autorisée durant cet état.
- Suppression en cours. Le canal est en cours de suppression. Aucune mise à jour ou diffusion en continu n'est autorisée durant cet état. 

## Sous-titrage et insertion de publicités 

Le tableau suivant présente les normes de sous-titrage et d'insertion de publicités prises en charge.

<table border="1">
<tr><th>Standard</th><th>Remarques</th></tr>
<tr><td>CEA-708 et EIA-608 (708/608)</td><td>CEA-708 et EIA-608 sont des normes de sous-titrage pour les États-Unis et le Canada.<br/>Actuellement, le sous-titrage est uniquement pris en charge s'il est inclus dans le flux d'entrée encodé. Vous devez utiliser un encodeur multimédia dynamique capable d'insérer des sous-titres 608 ou 708 dans le flux encodé qui est envoyé à Media Services. Media Services distribuera le contenu avec les sous-titres insérés à vos utilisateurs.</td></tr>
<tr><td>TTML dans ismt (pistes textuelles Smooth Streaming)</td><td>L'empaquetage dynamique de Media Services permet à vos clients de diffuser en continu du contenu dans un des formats suivants : MPEG DASH, HLS ou Smooth Streaming. Toutefois, si votre flux est au format MP4 fragmenté (Smooth Streaming) avec des sous-titres dans un fichier .ismt (pistes textuelles Smooth Streaming), vous serez seulement en mesure de distribuer le flux aux clients Smooth Streaming.</td></tr>
<tr><td>SCTE-35</td><td>Système de signalisation numérique utilisé pour signaler l'insertion de publicités. Les récepteurs en aval utilisent le signal pour ajouter les publicités au flux pendant le temps alloué. La signalisation SCTE-35 doit être envoyée sous forme de piste fragmentée dans le flux d'entrée.<br/>Notez qu'actuellement, le seul format de flux d'entrée pris en charge transmettant les signaux publicitaires est le format MP4 fragmenté (Smooth Streaming), qui est aussi le seul format de sortie compatible.</td></tr>
</table>

## Considérations

Lorsque vous utilisez un encodeur dynamique local pour envoyer un flux à débit binaire multiple dans un canal, les contraintes suivantes s'appliquent :

- Assurez-vous que vous disposez d'une connectivité Internet libre suffisante pour envoyer des données aux points de réception. 
- Le flux à débit binaire multiple entrant peut présenter un maximum de 10 niveaux de qualité vidéo (10 couches) et un maximum de 5 pistes audio.
- Le débit binaire moyen le plus élevé pour les niveaux ou couches de qualité vidéo doit être inférieur à 10 Mbit/s.
- La somme des débits binaires moyens de tous les flux vidéo et audio doit être inférieure à 25 Mbit/s.
- Vous ne pouvez pas modifier le protocole d'entrée pendant l'exécution du canal ou de ses programmes associés. Si vous avez besoin d'autres protocoles, vous devez créer des canaux distincts pour chaque protocole d'entrée. 


Autres considérations liées à l'utilisation des canaux et des composants associés :


- Vous êtes facturé uniquement lorsque votre canal est en cours d'exécution.
- Chaque fois que vous reconfigurez l'encodeur dynamique, appelez la méthode de réinitialisation **Reset** sur le canal. Avant de réinitialiser le canal, vous devez arrêter le programme. Une fois le canal réinitialisé, redémarrez le programme. 
- Un canal peut être arrêté uniquement lorsqu'il est en cours d'exécution et que tous les programmes du canal ont été arrêtés.
- Par défaut, vous pouvez seulement ajouter 5 canaux à votre compte Media Services. Pour plus d'informations, consultez la rubrique [Quotas et limitations](media-services-quotas-and-limitations.md).
- Vous ne pouvez pas modifier le protocole d'entrée pendant l'exécution du canal ou de ses programmes associés. Si vous avez besoin d'autres protocoles, vous devez créer des canaux distincts pour chaque protocole d'entrée. 

## <a id="tasks"></a>Tâches liées à la diffusion vidéo en flux continu

### Configuration de votre ordinateur

Pour plus d'informations sur la configuration de votre ordinateur, consultez la section [Configurer votre ordinateur](media-services-set-up-computer.md).

### Configuration de points de terminaison de diffusion en continu

Pour plus d'informations sur les points de terminaison de diffusion en continu et leur gestion, consultez la rubrique [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-manage-origins.md)

### Utilisation d'encodeurs dynamiques locaux pour envoyer un flux à débit binaire multiple vers un canal

Pour plus d'informations, consultez [Utilisation d'encodeurs tiers en temps réel avec Azure Media Services](https://msdn.microsoft.com/library/azure/dn783464.aspx).

### Gestion des canaux, des programmes, des ressources
Pour une présentation détaillée, consultez la page [Vue d'ensemble de la gestion des canaux et des programmes](media-services-manage-channels-overview.md).

Choisissez **Portail**, **.NET**, **API REST** pour voir des exemples.

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]

### Configuration de la stratégie de remise de ressources

Configurez la stratégie de remise de ressources à l'aide de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

### Création d'une clé de contenu

Créez une clé de contenu avec laquelle chiffrer votre ressource à l'aide de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

### Configuration de la stratégie d'autorisation de clé de contenu 

Configurez la stratégie de protection de contenu et d'autorisation de clé à l'aide de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


### Publication de ressources

Publiez des ressources (en créant des localisateurs) à l'aide du **portail de gestion Azure** ou de **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


### Activation du CDN Azure

Media Services prend en charge l'intégration au CDN Azure. Pour plus d'informations sur l'activation du CDN Azure, consultez la rubrique [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-manage-origins.md#enable_cdn).

### Mise à l'échelle d'un compte Media Services

Vous pouvez mettre à l'échelle **Media Services** en spécifiant le nombre d'**Unités réservées de diffusion en continu** avec lesquelles vous voulez que votre compte soit approvisionné. 

Pour plus d'informations sur la mise à l'échelle des unités de diffusion en continu, consultez : [Mise à l'échelle des unités de diffusion en continu](media-services-manage-origins.md#scale_streaming_endpoints.md).


[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png


<!--HONumber=52--> 