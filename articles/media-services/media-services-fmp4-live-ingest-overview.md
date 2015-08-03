<properties 
	pageTitle="Spécification d'ingestion en direct au format MP4 fragmenté Azure Media Services" 
	description="Cette spécification décrit le protocole et le format requis pour l'ingestion de diffusion en continu en direct basée sur le format MP4 fragmenté pour Microsoft Azure Media Services. Microsoft Azure Media Services fournit le service de diffusion en continu en direct qui permet aux clients de diffuser en continu des événements en direct et de diffuser du contenu en temps réel en utilisant Microsoft Azure en tant que plateforme cloud. Au moment de l'écriture, le format MP4 fragmenté pré-encodé est le seul mécanisme d'ingestion pour la diffusion en direct dans Microsoft Azure Media Services. Ce document explique également les pratiques recommandées en matière de création de mécanismes d'ingestion en direct extrêmement redondants et robustes." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/21/2015" 
	ms.author="juliako"/>

#Spécification d'ingestion en direct au format MP4 fragmenté Azure Media Services

Cette spécification décrit le protocole et le format requis pour l'ingestion de diffusion en continu en direct basée sur le format MP4 fragmenté pour Microsoft Azure Media Services. Microsoft Azure Media Services fournit le service de diffusion en continu en direct qui permet aux clients de diffuser en continu des événements en direct et de diffuser du contenu en temps réel en utilisant Microsoft Azure en tant que plateforme cloud. Au moment de l'écriture, le format MP4 fragmenté pré-encodé est le seul mécanisme d'ingestion pour la diffusion en direct dans Microsoft Azure Media Services. Ce document explique également les pratiques recommandées en matière de création de mécanismes d'ingestion en direct extrêmement redondants et robustes.

##Notation de conformité

Les mots clés « MUST », « MUST NOT », « REQUIRED », « SHALL », « SHALL NOT », « SHOULD », « SHOULD NOT », « RECOMMENDED », « MAY » et « OPTIONAL » figurant dans ce document doivent être interprétés comme indiqué dans le document RFC 2119.

##Diagramme du service 

Le diagramme ci-dessous illustre l'architecture de haut niveau du service de diffusion en continu en direct dans Microsoft Azure Media Services :

1.	L'encodeur en direct transmet les flux en direct dans des canaux créés et approvisionnés via le Kit de développement logiciel (SDK) Microsoft Azure Media Services.
2.	Les canaux, les programmes et le point de terminaison de diffusion en continu dans Microsoft Azure Media Services gèrent toutes les fonctionnalités de diffusion en continu en direct, notamment l'ingestion, le formatage, le DVR cloud, la sécurité, l'extensibilité et la redondance.
3.	Les clients peuvent éventuellement choisir de déployer une couche CDN entre le point de terminaison de diffusion en continu et les points de terminaison client.
4.	Les points de terminaison client diffusent à partir du point de terminaison de diffusion en continu à l'aide des protocoles de diffusion en continu adaptative HTTP (par exemple, Smooth Streaming, DASH, HDS ou HLS).

![image1][image1]


##Format de flux binaire – MP4 fragmenté ISO 14496-12

Le format câble utilisé pour l’ingestion de la diffusion en continu en direct décrite dans ce document repose sur la norme [ISO 14496-12]. Reportez-vous à [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) pour obtenir une explication détaillée du format MP4 fragmenté et des extensions de fichiers vidéo à la demande et d'ingestion de diffusion en continu en direct.

Ci-dessous figure la liste des définitions de formats spéciaux qui s'appliquent à l'ingestion en direct dans Microsoft Azure Media Services :

1. Les zones « ftyp », LiveServerManifestBox et « moov » DOIVENT être envoyées avec chaque requête (HTTP POST). Elles DOIVENT être envoyées au début du flux et chaque fois que l'encodeur doit se reconnecter pour reprendre l'ingestion du flux. Reportez-vous à la section 6 dans [1] pour plus d’informations.
2. La section 3.3.2 dans [1] définit une zone facultative appelée StreamManifestBox pour l’ingestion en direct. En raison de la logique de routage du programme d'équilibrage de la charge de Microsoft Azure, l'utilisation de cette zone est déconseillée et NE DOIT PAS être présente lors de l'ingestion dans Microsoft Azure Media Services. Si cette zone est présente, Azure Media Services l'ignore en mode silencieux.
3. La zone TrackFragmentExtendedHeaderBox définie à la section 3.2.3.2 dans [1] doit être présente pour chaque fragment.
4. La version 2 de la zone TrackFragmentExtendedHeaderBox DOIT être utilisée pour générer des segments de médias avec des URL identiques dans plusieurs centres de données. Le champ d'index de fragment est OBLIGATOIRE pour le basculement entre centres de données des formats de diffusion en continu basée sur les index comme Apple HTTP Live Streaming (HLS) et MPEG-DASH basée sur les index. Pour permettre un basculement entre centres de données, l'index de fragment DOIT être synchronisé entre plusieurs encodeurs et augmenté de 1 pour chaque fragment multimédia successif, même entre les redémarrages ou échecs de l'encodeur.
5. La section 3.3.6 dans [1] définit la zone appelée MovieFragmentRandomAccessBox (’mfra’) qui PEUT être envoyée à la fin de l’ingestion en direct pour indiquer la fin du flux (EOS, End-of-Stream) au canal. En raison de la logique d'ingestion d'Azure Media Services, l'utilisation de la fin du flux (EOS) est déconseillée et la zone 'mfra' pour l'ingestion en direct ne DOIT PAS être envoyée. Si elle l'est, Azure Media Services l'ignore en mode silencieux. Il est recommandé d'utiliser la [réinitialisation du canal](https://msdn.microsoft.com/library/azure/dn783458.aspx#reset_channels) pour réinitialiser l'état du point d'ingestion et également d'utiliser l'[arrêt du programme](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) pour mettre fin à une présentation et à un flux.
6. La durée du fragment MP4 DOIT être une constante, afin de réduire la taille des manifestes du client et d'améliorer les heuristiques de téléchargement client via l'utilisation de balises de répétition. La durée PEUT varier pour compenser les fréquences d'images non entières.
7. La durée du fragment MP4 DOIT être comprise entre environ 2 et 6 secondes.
8. Les horodatages et index du fragment MP4 (TrackFragmentExtendedHeaderBox fragment_absolute_time et fragment_index) DOIVENT normalement arriver dans l'ordre croissant. Bien qu'Azure Media Services ne duplique pas les fragments, il est capable, de façon très limitée, de réorganiser les fragments en fonction de la chronologie du média.

##Format de protocole – HTTP

L'ingestion en direct basée sur le format MP4 fragmenté ISO pour Microsoft Azure Media Services utilise une longue requête HTTP POST standard pour transmettre des données multimédias encodées empaquetées au format MP4 fragmenté au service. Chaque requête HTTP POST envoie un flux binaire (« Stream ») MP4 fragmenté en commençant par les zones d'en-tête (zone « ftyp », « Live Server Manifest Box » et « moov ») et en continuant avec une séquence de fragments (zones « moof » et « mdat »). Reportez-vous à la section 9.2 dans [1] pour connaître la syntaxe d’URL de la requête HTTP POST. Voici un exemple d'URL POST :

	http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

Voici les spécifications détaillées :

1. L'encodeur DOIT démarrer la diffusion en envoyant une requête HTTP POST avec un « corps » vide (longueur de contenu nulle) à l'aide de la même URL d'ingestion. Cela peut aider à détecter rapidement si le point de terminaison d'ingestion en direct est valide et s'il existe une authentification ou d'autres conditions requises. Conformément au protocole HTTP, le serveur n'est pas capable de renvoyer une réponse HTTP tant que la requête entière, y compris le corps POST, n'est pas reçue. Étant donné la longue durée d'un événement en direct, sans cette étape, l'encodeur risque de ne pas pouvoir détecter d'éventuelles erreurs tant qu'il n'a pas terminé d'envoyer toutes les données.
2. Le codeur DOIT gérer toutes les erreurs ou demandes d'authentification suite à (1). Si (1) réussit avec une réponse 200, continuez.
3. L'encodeur DOIT démarrer une nouvelle requête HTTP POST avec le flux MP4 fragmenté. La charge utile DOIT commencer par les zones d'en-tête suivies des fragments. Notez que la zone « ftyp », « Live Server Manifest Box » et « moov » (dans cet ordre) DOIT être envoyée avec chaque requête, même si l'encodeur doit se reconnecter, car la requête précédente a été interrompue avant la fin du flux. 
4. L'encodeur DOIT utiliser l'encodage de transfert en bloc pour le téléchargement, puisqu'il est impossible de prédire la longueur totale du contenu de l'événement en direct.
5. Quand l'événement est terminé, après l'envoi du dernier fragment, l'encodeur DOIT gracieusement mettre fin à la séquence de message de l'encodage de transfert en bloc (la plupart des piles de client HTTP gère cela automatiquement). L'encodeur DOIT attendre que le service retourne le code de réponse finale, puis mettre fin à la connexion. 
6. L’encodeur NE DOIT PAS utiliser le nom Events() comme décrit à la section 9.2 dans [1] pour l’ingestion en direct dans Microsoft Azure Media Services.
7. Si la requête HTTP POST se termine ou arrive à expiration avant la fin du flux avec une erreur TCP, l'encodeur DOIT émettre une nouvelle requête POST à l'aide d'une nouvelle connexion et suivre les spécifications ci-dessus en plus de respecter l'exigence supplémentaire stipulant que l'encodeur DOIT renvoyer les deux fragments MP4 précédents pour chaque piste dans le flux, puis reprendre sans introduire de discontinuités dans la chronologie du média. Le renvoi des deux derniers fragments MP4 pour chaque piste garantit l'absence de perte de données. En d'autres termes, si un flux contient à la fois une piste audio et vidéo, et si la requête POST en cours échoue, l'encodeur doit se reconnecter et renvoyer les deux derniers fragments de la piste audio, lesquels ont déjà été envoyés correctement, ainsi que les deux derniers fragments de la piste vidéo, lesquels ont déjà été envoyés correctement, pour garantir l'absence de perte de données. L'encodeur DOIT conserver un tampon « de transfert » des fragments multimédias, qu'il renvoie lors de la reconnexion.

##Échelle de temps 

[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) décrit l'utilisation de l'échelle de temps pour SmoothStreamingMedia (Section 2.2.2.1), StreamElement (Section 2.2.2.3), StreamFragmentElement (Section 2.2.2.6) et LiveSMIL (Section 2.2.7.3.1). Si la valeur de l'échelle de temps n'est pas présente, la valeur par défaut utilisée est 10 000 000 (10 MHz). Bien que la spécification du format Smooth Streaming ne bloque pas l'utilisation d'autres valeurs d'échelle de temps, la plupart des implémentations de l'encodeur utilise cette valeur par défaut (10 MHz) pour générer les données d'ingestion Smooth Streaming. En raison de la fonctionnalité [Azure Media Dynamic Packaging](media-services-dynamic-packaging-overview.md), il est recommandé d'utiliser l'échelle de temps de 90 kHz pour les flux vidéos et de 44,1 ou de 48,1 kHz pour les flux audio. Si des valeurs d'échelle de temps différentes sont utilisées pour des flux différents, l'échelle de temps au niveau du flux DOIT être envoyée. Reportez-vous à [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

##Définition de « Stream »  

« Stream » est l'unité de base de l'opération dans l'ingestion en direct pour rédiger la présentation en direct, gérer le basculement de la diffusion en continu et les scénarios de redondance. « Stream » se définit comme un seul flux binaire MP4 fragmenté pouvant contenir une piste unique ou plusieurs pistes. Une présentation en direct complète peut contenir un ou plusieurs flux selon la configuration des encodeurs en direct. Les exemples suivants illustrent les différentes options d'utilisation des flux pour rédiger une présentation en direct.

**Exemple :**

Le client veut créer une présentation de diffusion en continu en direct qui inclut les débits binaires audio/vidéo suivants :

Vidéo : 3 000 Kbits/s, 1 500 Kbits/s, 750 Kbits/s

Audio : 128 Kbits/s

###Option 1 : toutes les pistes dans un seul flux

Dans cette option, un seul encodeur génère toutes les pistes audio/vidéo et les regroupe dans un seul flux binaire MP4 fragmenté qui est ensuite envoyé via une connexion HTTP POST unique. Dans cet exemple, il existe un seul flux pour cette présentation en direct :

![image2][image2]

###Option 2 : chaque piste dans un flux distinct

Dans cette option, les encodeurs mettent une seule piste dans chaque flux binaire MP4 fragmenté et publient tous les flux sur plusieurs connexions HTTP distinctes. Cette option est réalisable avec un seul encodeur ou plusieurs. Du point de vue de l'ingestion en direct, cette présentation en direct se compose de quatre flux.

![image3][image3]

###Option 3 : regrouper la piste audio et la piste vidéo au débit binaire le plus bas dans un seul flux

Dans cette option, le client choisit de regrouper la piste audio avec la piste vidéo au débit binaire le plus bas dans un seul flux binaire MP4 fragmenté et de laisser les deux autres pistes vidéo dans son propre flux.


![image4][image4]


###Résumé

Ce qui précède ne constitue PAS la liste exhaustive de toutes les options d'ingestion possibles pour cet exemple. En fait, tous les regroupements de pistes dans des flux sont pris en charge par l'ingestion en direct. Les clients et fournisseurs d'encodeurs peuvent choisir leurs propres implémentations selon la complexité d'ingénierie, la capacité de l'encodeur et les questions de redondance et de basculement. Toutefois, il convient de noter que dans la plupart des cas, il existe uniquement une piste audio pour toute la présentation en direct. Donc il est important de garantir l'intégrité du flux d'ingestion qui contient la piste audio. Cette remarque entraîne souvent le placement de la piste audio dans son propre flux (comme dans l'option 2) ou son regroupement avec la piste vidéo au débit binaire le plus bas (comme dans l'option 3). De plus, pour une meilleure redondance et une meilleure tolérance de panne, l'envoi de la même piste audio dans deux flux différents (option 2 avec pistes audio redondantes) ou le regroupement de la piste audio avec au moins deux des pistes vidéo au débit binaire le plus faible (option 3 avec audio regroupé avec au moins deux flux vidéo) sont fortement recommandés pour l'ingestion en direct dans Microsoft Azure Media Services.

##Basculement du service 

Étant donné la nature de la diffusion en continu en direct, une bonne prise en charge du basculement s'avère essentielle pour garantir la disponibilité du service. Microsoft Azure Media Services est conçu pour gérer divers types de défaillances, notamment les erreurs de réseau, les erreurs de serveur, les problèmes de stockage, etc. Utilisé conjointement avec la logique de basculement appropriée du côté de l'encodeur en direct, le client peut obtenir un service de diffusion en continu en direct très fiable à partir du cloud.

Dans cette section, nous aborderons les scénarios de basculement du service. Le cas échéant, la défaillance se produit quelque part au sein du service et se traduit par une erreur de réseau. Voici quelques recommandations sur l'implémentation de l'encodeur pour gérer le basculement du service :


1. Utilisez un délai d'expiration de 10 secondes pour établir la connexion TCP. Si une tentative d'établissement de la connexion prend plus de 10 secondes, abandonnez l'opération et réessayez. 
2. Utilisez un court délai d'expiration pour l'envoi des fragments de message de la requête HTTP. Si la durée du fragment MP4 cible s'élève à N secondes, utilisez un délai d'expiration d'envoi compris entre N et 2N secondes. Par exemple, utilisez un délai d'expiration compris entre 6 et 12 secondes si la durée du fragment MP4 est de 6 secondes. En cas d'expiration, réinitialisez la connexion, ouvrez une nouvelle connexion et reprenez l'ingestion du flux sur la nouvelle connexion. 
3. Conservez un tampon de substitution contenant les deux derniers fragments, pour chaque piste, qui ont été correctement et complètement envoyés au service. Si la requête HTTP POST d'un flux est arrêtée ou arrive à expiration avant la fin du flux, ouvrez une nouvelle connexion et commencez une autre requête HTTP POST, renvoyez les en-têtes du flux, renvoyez les deux derniers fragments de chaque piste et reprenez le flux sans introduire de discontinuités dans la chronologie du média. Les risques de perte de données sont ainsi réduits.
4. Il est recommandé que l'encodeur ne limite PAS le nombre de nouvelles tentatives de connexion ou de reprise de la diffusion en continu suite à une erreur TCP.
5. Après une erreur TCP :
	1. La connexion en cours DOIT être fermée et une nouvelle connexion DOIT être créée pour une nouvelle requête HTTP POST.
	2. La nouvelle URL HTTP POST DOIT être identique à l'URL POST initiale.
	3. La nouvelle requête HTTP POST DOIT inclure des en-têtes de flux (zone « ftyp », « Live Server Manifest Box » et « moov ») identiques aux en-têtes de flux de la requête POST initiale.
	4. Les deux derniers fragments envoyés pour chaque piste DOIVENT être renvoyés et la diffusion en continu reprise sans introduire de discontinuités dans la chronologie du média. Les horodatages des fragments MP4 doivent s'incrémenter en permanence, même dans les requêtes HTTP POST.
6. L'encodeur DOIT mettre fin à la requête HTTP POST si les données ne sont pas envoyées à un débit proportionné par rapport à la durée du fragment MP4. Une requête HTTP POST qui n'envoie pas de données peut empêcher Azure Media Services de se déconnecter rapidement de l'encodeur en cas de mise à jour du service. C'est pourquoi la requête HTTP POST des pistes partiellement allouées (signal d'annonce) DOIT avoir une durée de vie courte et se terminer dès que le fragment partiellement alloué est envoyé.

##Basculement de l'encodeur

Le basculement de l'encodeur est le deuxième type de scénario de basculement qui doit être traité pour la diffusion en continu en direct de bout en bout. Dans ce scénario, la condition d'erreur s'est produite côté encodeur.

![image5][image5]


Voici les attentes du point de terminaison d'ingestion en direct en cas de basculement de l'encodeur :

1. Une nouvelle instance de l'encodeur DOIT être créée pour continuer la diffusion en continu, comme illustré dans le diagramme ci-dessus (flux pour une vidéo de 3 000 k avec une ligne en pointillés).
2. Le nouvel encodeur DOIT utiliser la même URL pour les requêtes HTTP POST que l'instance défaillante.
3. La requête POST du nouvel encodeur DOIT inclure les mêmes zones d'en-tête MP4 fragmenté que l'instance défaillante.
4. Le nouvel encodeur DOIT être correctement synchronisée avec tous les autres encodeurs en cours d'exécution pour la même présentation en direct pour générer des échantillons audio/vidéo synchronisés avec des limites de fragments alignées.
5. Le nouveau flux DOIT être sémantiquement équivalent au flux précédent et interchangeables au niveau de l'en-tête et du fragment.
6. Le nouvel encodeur DOIT tenter de minimiser les pertes de données. Les valeurs fragment_absolute_time et fragment_index des fragments multimédias DOIVENT augmenter à partir du point où l'encodeur s'est arrêté la dernière fois. Les valeurs fragment_absolute_time et fragment_index DOIVENT augmenter de façon continue, mais il est possible d'introduire une discontinuité si besoin. Azure Media Services ignore les fragments déjà reçus et traités. Une erreur du côté du renvoi des fragments est donc préférable à l'introduction de discontinuités dans la chronologie du média. 

##Redondance de l'encodeur 

Pour certains événements en direct critiques qui exigent une disponibilité et une qualité d'expérience encore plus élevées, il est recommandé d'utiliser des encodeurs redondants en mode actif-actif pour obtenir un basculement transparent sans perte de données.

![image6][image6]

Comme illustré dans le diagramme ci-dessus, il existe deux groupes d'encodeurs qui transmettent simultanément deux copies de chaque flux au service en direct. Cette configuration est prise en charge car Microsoft Azure Media Services a la possibilité de filtrer les fragments en double en fonction de l'ID du flux et de l'horodatage des fragments. Le flux en direct et l'archive obtenus consistent en une seule copie de tous les flux constituant l'agrégation la meilleure possible des deux sources. Par exemple, dans un cas extrême hypothétique, tant qu'il existe un encodeur (éventuellement différent) en cours d'exécution à un moment donné pour chaque flux, le flux en direct obtenu à partir du service est continu sans perte de données.

La configuration requise pour ce scénario est presque identique à celle requise en cas de basculement de l'encodeur, à l'exception près que le deuxième ensemble d'encodeurs s'exécute en même temps que les encodeurs principaux.

##Redondance du service  

Pour une distribution globale hautement redondante, il est parfois nécessaire de disposer d'une sauvegarde inter-régions pour traiter les urgences régionales. En développant la topologie « Redondance de l'encodeur », les clients peuvent choisir d'avoir un déploiement de services redondant dans une région différente qui est connectée au deuxième ensemble d'encodeurs. Les clients peuvent également avoir recours à un fournisseur CDN pour déployer un GTM (Global Traffic Manager) devant les deux déploiements de services afin d'acheminer le trafic du client en toute transparence. La configuration requise pour les encodeurs est la même que celle de l'exemple « Redondance de l'encodeur », à la seule exception près que le deuxième ensemble d'encodeurs a besoin de pointer vers un autre point de terminaison d'ingestion en direct. Le diagramme ci-dessous illustre cette configuration :

![image7][image7]

##Types spéciaux de formats d'ingestion 

Cette section présente les types spéciaux de formats d'ingestion en direct, conçus pour gérer certains scénarios spécifiques.

###Piste partiellement allouée

Pendant une présentation de diffusion en continu en direct avec une expérience client riche, il est souvent nécessaire de transmettre des événements synchronisés ou des signaux intrabande avec les principales données multimédias. L'insertion de publicités dynamiques en direct en est un exemple. Ce type de signalisation d'événement diffère de la diffusion audio/vidéo en continu standard en raison de sa nature partiellement allouée. Autrement dit, les données de signalisation ne se produisent généralement pas de manière continue et l'intervalle peut être difficile à prédire. Le concept de piste partiellement allouée a été spécialement conçu pour ingérer et diffuser des données de signalisation intrabande.

Voici une implémentation recommandée pour l'ingestion d'une piste partiellement allouée :

1. Créez un flux binaire MP4 fragmenté distinct qui contient juste des pistes partiellement allouées sans pistes audio/vidéo.
2. Dans la zone « Live Server Manifest Box » définie à la section 6 dans [1], utilisez le paramètre « parentTrackName » pour spécifier le nom de la piste parent. Reportez-vous à la section 4.2.1.2.1.2 dans [1] pour plus d’informations.
3. Dans la zone « Live Server Manifest Box », manifestOutput DOIT avoir la valeur « true ».
4. Étant donné la nature partiellement allouée de l'événement de signalisation, voici ce que nous recommandons :
	1. Au début de l'événement en direct, l'encodeur envoie les zones d'en-tête initiales au service, ce qui permet au service d'enregistrer la piste partiellement allouée dans le manifeste du client.
	2. L'encodeur DOIT mettre fin à la requête HTTP POST quand les données ne sont pas envoyées. Une requête HTTP POST de longue durée qui n'envoie pas de données peut empêcher Azure Media Services de se déconnecter rapidement de l'encodeur en cas de mise à jour du service ou de redémarrage du serveur, puisque le serveur multimédia est temporairement bloqué dans une opération de réception sur le socket. 
	3. Pendant que les données de signalisation ne sont pas disponibles, l'encodeur DOIT fermer la requête HTTP POST. Pendant que la requête POST est active, l'encodeur DOIT envoyer des données. 
	4. Lors de l'envoi de fragments partiellement alloués, l'encodeur peut définir un en-tête Content-Length explicite s'il est disponible.
	5. Lors de l'envoi d'un fragment partiellement alloué avec une nouvelle connexion, l'encodeur DOIT commencer par envoyer les zones d'en-tête, puis les nouveaux fragments. Cette recommandation permet de gérer le cas où le basculement a eu lieu entre temps et où la nouvelle connexion est établie avec un nouveau serveur qui n'a pas vu la piste partiellement allouée auparavant.
	6. Le fragment de piste partiellement allouée devient disponible pour le client quand le fragment de piste parent correspondant dont la valeur d'horodatage est égale ou supérieure est mis à la disposition du client. Par exemple, si le fragment partiellement alloué a un horodatage de t=1000, il est attendu après que le client visualise le fragment vidéo (en supposant que le nom de la piste parent est une vidéo) 1000 ou au-delà. Il peut télécharger le fragment partiellement alloué t=1000. Notez que le signal réel peut très bien servir à un autre emplacement dans la chronologie de la présentation pour sa fonction désignée. Dans l'exemple ci-dessus, le fragment partiellement alloué de t=1000 peut avoir une charge utile XML pour insérer une publicité à un emplacement situé quelques secondes plus tard.
	7. La charge utile du fragment de piste partiellement alloué peut être dans différents formats (par exemple, XML, texte ou binaire, etc.) en fonction de différents scénarios. 


###Piste audio redondante

Dans un scénario de diffusion en continu adaptative HTTP classique (par exemple, Smooth Streaming ou DASH), la présentation dans son ensemble ne contient souvent qu'une seule piste audio. Contrairement aux pistes vidéo qui ont plusieurs niveaux de qualité parmi lesquels le client peut choisir dans des conditions d'erreur, la piste audio peut correspondre à un seul point de défaillance si l'ingestion du flux qui contient la piste audio est rompue.

Pour résoudre ce problème, Microsoft Azure Media Services prend en charge l'ingestion en direct des pistes audio redondantes. L'idée est que la même piste audio peut être envoyée plusieurs fois dans différents flux. Alors que le service enregistre une seule fois la piste audio dans le manifeste du client, il est en mesure d'utiliser des pistes audio redondantes en tant que sauvegardes pour la récupération de fragments audio si la piste audio principale rencontre des problèmes. Pour ingérer des pistes audio redondantes, l'encodeur doit :

1. créer la même piste audio dans plusieurs flux binaires MP4 fragmentés. Les pistes audio redondantes DOIVENT être sémantiquement équivalentes aux mêmes horodatages de fragments et interchangeables au niveau de l'en-tête et du fragment.
2. Vérifiez que l’entrée « audio » dans la zone Live Server Manifest (section 6 dans [1]) est la même pour toutes les pistes audio redondantes.

Voici une implémentation recommandée pour les pistes audio redondantes :

1. Envoyez chaque piste audio unique dans un flux toute seule. Envoyez également un flux redondant pour chacun de ces flux de piste audio, où le deuxième flux diffère du premier uniquement par l'identificateur inclus dans l'URL HTTP POST : {protocole}://{adresse du serveur}/{chemin du point de publication}/Streams({identificateur}).
2. Utilisez des flux distincts pour envoyer les deux débits binaires vidéo les plus bas. Chacun de ces flux DOIT également contenir une copie de chaque piste audio unique. Par exemple, quand plusieurs langues sont prises en charge, ces flux DOIVENT contenir des pistes audio pour chaque langue.
3. Utilisez des instances de serveur (encodeur) distinctes pour encoder et envoyer les flux redondants mentionnés dans (1) et (2). 


[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png

 

<!---HONumber=July15_HO4-->