<properties
   pageTitle="Aide relative au réseau de distribution de contenu (CDN) | Microsoft Azure"
   description="Instructions relatives au réseau de distribution contenu (CDN) pour la distribution de contenu d'une bande passante élevée hébergé dans Azure."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Aide relative au réseau de distribution de contenu (CDN)

![](media/best-practices-cdn/pnp-logo.png)

## Vue d'ensemble

Le Microsoft Azure Content Delivery Network (CDN) offre aux développeurs une solution globale pour fournir du contenu de large bande-passante hébergé dans Azure. Le CDN met en cache des objets disponibles au public, chargés à partir du stockage d’objet blob Azure ou d’un dossier d’application à des emplacements stratégiques pour offrir une bande passante maximum pour distribuer du contenu aux utilisateurs. Le CDN est généralement utilisé pour distribuer du contenu statique tel que des images, des feuilles de style, des documents, des fichiers, des scripts côté client et des pages HTML.

Les principaux avantages de l’utilisation du CDN sont une latence moindre et une distribution plus rapide du contenu aux utilisateurs, quel que soit leur situation géographique relativement au centre de données dans lequel est hébergée l’application, et une réduction de la charge de l’application étant donné qu’elle est libérée du traitement requis pour accéder et distribuer le contenu. Cette réduction de charge peut aider à augmenter la performance et l’extensibilité de l’application, tout en minimisant les coûts d’hébergement en réduisant le traitement des ressources nécessaires pour atteindre un certain niveau de performance et de disponibilité.

Vous avez peut-être la possibilité d’utiliser d’autres systèmes de réseau de distribution de contenu qui ne sont pas implémentés par Azure dans vos applications si le CDN d’Azure ne répond pas à vos besoins. Autrement, vous pouvez utiliser le CDN d’Azure pour des applications hébergées par d’autres fournisseurs en exposant le contenu statique dans le stockage Azure ou dans les instances de calcul Azure. ![](media/best-practices-cdn/CDN.png)

## Comment et pourquoi le CDN est-il utilisé ?

Le CDN est généralement utilisé dans les cas suivants :

- La distribution de ressources statiques pour des applications clientes, souvent depuis un site web. Il peut s'agir d'images, des feuilles de style, de documents, de fichiers, de scripts côté client, de pages HTML, de fragments HTML ou tout autre contenu que le serveur n’a pas besoin de modifier à chaque demande. L’application peut créer des éléments au cours de son exécution et les rendre disponibles au CDN (par exemple, en créant une liste de titres d’information actuels), mais elle ne le fait pas pour chaque demande.

- La distribution de contenu partagé et statique public sur des périphériques tels que des téléphones mobiles et des tablettes où l’application même est un service web qui offre une API aux clients. En plus des autres contenus que le serveur n’a pas besoin de modifier pour chaque demande, le CDN peut distribuer des jeux de données statiques que le client peut utiliser (peut-être pour générer l’interface utilisateur du client). Par exemple, ils peuvent être utilisés pour distribuer des documents JSON ou XML.

- La mise à disposition de sites web entiers qui se composent uniquement de contenu statique public pour les clients, sans nécessiter de ressources de calcul dédiées.

- La diffusion en continu de fichiers vidéo à la demande pour le client. La vidéo bénéficie d’une latence faible et d’une connectivité fiable disponible depuis les centres de données répartis dans le monde qui offrent des connexions au CDN.

- Le CDN améliore généralement l’expérience des utilisateurs, en particulier ceux qui se trouvent loin de l’emplacement du centre de données de l’application et qui,autrement, subiraient une faible latence. Une grande partie de la taille totale du contenu d’une application web est souvent statique et l’utilisation d’un CDN peut aider à maintenir la performance ainsi que l’expérience utilisateur générale tout en éliminant la contrainte du déploiement de l’application sur plusieurs centres de données.

- La copie avec une charge croissante sur des applications utilisées sur des périphériques mobiles et fixes et qui font partie de l’Internet des objets (IoT). Un grand nombre de ces appareils pourraient facilement surcharger l’application si le traitement des messages de bande passante et la gestion de la distribution de mise à jour de microprogrammes devaient être réalisés directement.

- La copie avec des fluctuations à la demande sans que l’application soit mise à l’échelle, évitant ainsi une augmentation des coûts d’exécution. Par exemple, lorsqu’une mise à jour est publiée sur un système d’exploitation, pour un périphérique matériel tel qu’un modèle de routeur spécifique, ou pour un appareil de consommation tel qu’une TV connectée, on observera une forte augmentation de la demande tandis qu’il est téléchargé par des millions d’utilisateurs et de périphériques sur un court délai.

- Le tableau suivant montre des exemples du temps médian du premier téléchargement dans différentes zones géographiques. Le rôle web cible est déployé sur Azure dans l’ouest des États-Unis. Il existe un lien étroit entre la forte stimulation due au CDN et la proximité d’un nœud du CDN. Pour obtenir la liste des emplacements de tous les nœuds CDN, consultez la page [Emplacements des nœuds du réseau de distribution de contenu (CDN) Azure](http://msdn.microsoft.com/library/azure/gg680302.aspx).

<table xmlns:xlink="http://www.w3.org/1999/xlink"><tr><th><a name="_MailEndCompose" href="#"><span /></a><br /></th><th><p>Heure du premier téléchargement (Origine)</p></th><th><p>Heure du premier téléchargement (CDN)</p></th><th><p>% de rapidité du CDN</p></th></tr><tr><td><p>* San José, CA</p></td><td><p>47.5</p></td><td><p>46.5</p></td><td><p>2 %</p></td></tr><tr><td><p>** Dulles, VA</p></td><td><p>109</p></td><td><p>40.5</p></td><td><p>169 %</p></td></tr><tr><td><p>Buenos Aires, AR</p></td><td><p>210</p></td><td><p>151</p></td><td><p>39 %</p></td></tr><tr><td><p>* Londres, UK</p></td><td><p>195</p></td><td><p>44</p></td><td><p>343 %</p></td></tr><tr><td><p>Shanghai, CN</p></td><td><p>242</p></td><td><p>206</p></td><td><p>17 %</p></td></tr><tr><td><p>* Singapore</p></td><td><p>214</p></td><td><p>74</p></td><td><p>189 %</p></td></tr><tr><td><p>* Tokyo, JP</p></td><td><p>163</p></td><td><p>48</p></td><td><p>240 %</p></td></tr><tr><td><p>Séoul, KR</p></td><td><p>190</p></td><td><p>190</p></td><td><p>0 %</p></td></tr></table>* Possède un nœud CDN Azure dans la même ville. * Possède un nœud CDN dans une ville voisine.


## Défis  

Il existe plusieurs problèmes à prendre en compte lorsque vous envisagez d'utiliser le CDN :

- **Déploiement** : vous devez décidez à partir de quel emplacement vous souhaitez charger le contenu du CDN (l’emplacement d’origine depuis lequel le CDN va rassembler le contenu), et si vous avez besoin de déployer le contenu dans plus d’un système de stockage (par exemple, sur le CDN et dans d’autres emplacements).

- Le mécanisme de déploiement de votre application doit prendre en compte le déploiement de contenu et de ressources statiques ainsi que le déploiement de fichiers d'application tels que des pages ASPX. Par exemple, elle peut nécessiter une étape distincte pour charger le contenu dans le stockage d’objets blob Azure.

- **Le contrôle de version et du cache** : vous devez envisager la façon dont vous allez mettre à jour du contenu statique et déployer de nouvelles versions. Actuellement, le CDN ne fournit pas de mécanisme permettant de vider le contenu afin de rendre les nouvelles versions disponibles. Il s'agit d'un problème similaire à la gestion de la mise en cache côté client, par exemple dans un navigateur web.

- **Test** : il peut être difficile de réaliser des tests locaux de vos paramètres du CDN lors du développement et du test d'une application localement ou de façon intermédiaire.

- **SEO** : contenu tel que les images et les documents utilisés à partir d'un autre domaine lorsque vous utilisez le CDN, ce qui aura un effet sur le SEO pour ce contenu.

- **Sécurité** : de nombreux services de CDN, tels que Azure CDN, ne proposent pas pour le moment différents types de contrôle d’accès au contenu.

- **Résilience** : le CDN peut être le point unique de défaillance d'une application. Il dispose d’une disponibilité SLA inférieure au stockage d’objets blob (qui peut être utilisé pour distribuer directement du contenu). Par conséquent, vous devrez peut-être envisager d'implémenter un mécanisme de secours pour le contenu important.

- Les clients peuvent se connecter à partir d'un environnement qui n'autorise pas l'accès aux ressources sur le CDN. Cela peut être un environnement soumis à des contraintes de sécurité qui limite l'accès uniquement à un ensemble de sources connues ou qui empêche le chargement de ressources à partir de tout autre emplacement que la page d’origine. Par conséquent, une implémentation de secours sera nécessaire.

- Vous devez implémenter un mécanisme permettant de surveiller la disponibilité de votre contenu via le CDN.

Les scénarios où le CDN peut s’avérer moins utile sont :

- Lorsque le contenu a un faible taux de réussite et peut donc être accessible certaines fois ou une fois seulement au cours de la période de validité de la durée de vie. La première fois qu'un élément est téléchargé, vous serez soumis à deux frais de transaction (du point d’origine au CDN, puis du CDN au client).

- Lorsque les données sont privées, comme pour les grandes entreprises ou les écosystèmes de chaîne d'approvisionnement.


## Instructions générales et meilleures pratiques

L’utilisation du CDN est une bonne façon de réduire la charge sur votre application et d’optimiser la disponibilité et les performances. Vous devez prendre cela en compte pour tout le contenu et les ressources appropriées utilisées par votre application. Lors de l’élaboration de votre stratégie, prenez en considération les points suivants pour utiliser le CDN :

- **Origine ** Le déploiement de contenu via le CDN nécessite simplement que vous indiquiez un point de terminaison HTTP (port 80) que le service CDN utilisera pour accéder et mettre en cache le contenu. + Le point de terminaison peut spécifier un conteneur de stockage d'objets blob Azure dans lequel se trouve le contenu statique que vous voulez distribuer via le CDN. Le conteneur doit être marqué comme étant public. Seuls les objets blob dans un conteneur public disposant d’un accès en lecture public seront disponibles sur le CDN. 
- Le point de terminaison peut spécifier un dossier nommé **cdn** dans la racine de l'une des couches de calcul de l'application (par exemple, un rôle web ou une machine virtuelle). Les résultats des demandes de ressources, y compris des ressources dynamiques telles que des pages ASPX, sont mis en cache sur le CDN. La période de mise en cache minimale est de 300 secondes. Une période plus courte empêchera le contenu d’être déployé sur le CDN (consultez la section « <a href=»#cachecontrol» xmlns:dt=»uuid:C2F41010-65B3-11d1-A29F-00AA00C14882» xmlns:xlink=»http://www.w3.org/1999/xlink» xmlns:MSHelp=»http://msdn.microsoft.com/mshelp»>Contrôle de cache</a> » pour plus d'informations).

- Si vous utilisez des Sites Web Azure, le point de terminaison est défini dans le dossier racine du site en sélectionnant le site lors de la création de l'instance du CDN. Tout le contenu du site est accessible via le CDN.

- Dans la plupart des cas, le pointage vers le point de terminaison CDN dans un dossier dans une des couches de calcul de votre application offre plus de flexibilité et de contrôle. Par exemple, il est plus facile de gérer les exigences de routage actuelles et futures et de générer de façon dynamique le contenu statique tel que des images miniatures.

- Vous pouvez utiliser des chaînes de requête pour différencier les objets dans le cache lorsque le contenu est distribué à partir de sources dynamiques telles que les pages ASPX. Toutefois, ce comportement peut être désactivé par un paramètre dans le portail de gestion lorsque vous spécifiez le point de terminaison CDN. Lors de la distribution du contenu depuis le stockage d’objets blob, les chaînes de requête sont traitées comme des littéraux de chaîne de manière à ce que deux éléments qui ont le même nom mais des chaînes de requête différentes soient stockés en tant qu'éléments distincts sur le CDN.

- Vous pouvez utiliser la réécriture d'URL pour les ressources telles que les scripts et autres contenus afin d'éviter de déplacer vos fichiers dans le dossier d'origine du CDN.

- Lorsque vous utilisez des objets blob de stockage Azure pour conserver le contenu du CDN, l'URL des ressources dans les objets blob est sensible à la casse pour le nom du conteneur et des objets blob.

- Lorsque vous utilisez les Sites Web Azure, vous spécifiez le chemin d'accès à l'instance CDN dans les liens vers les ressources. Par exemple, le code suivant spécifie un fichier image dans le dossier **Images** dossier du site qui sera distribué via le CDN :

  ```
  <img src="http://[your-cdn-instance].vo.msecnd.net/Images/image.jpg" />
  ```

- **Déploiement** : il vous faudra peut-être approvisionner et déployer le contenu statique indépendamment de l'application si vous ne l'incluez pas dans le package ou le processus de déploiement d'application. Pensez à la façon dont cela affectera l’approche du contrôle de version que vous utilisez pour gérer les composants d'application et le contenu de ressources statique.

- Pensez à la façon dont le regroupement (le rassemblement de plusieurs fichiers dans un seul fichier) et la minimisation (la suppression de caractères inutiles tels que des espaces blancs, les caractères de nouvelle ligne, les commentaires et autres caractères) des scripts et des fichiers CSS peuvent être gérés. Ces techniques couramment utilisées peuvent réduire les temps de chargement pour les clients et sont compatibles avec la distribution de contenu via le CDN. Pour plus d'informations, consultez [Regroupement et minimisation](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

- Si vous devez déployer le contenu à un emplacement supplémentaire, cela constitue une étape supplémentaire dans le processus de déploiement. Si l'application met à jour le contenu pour le CDN, peut-être à intervalles réguliers ou en réponse à un événement, elle doit stocker le contenu mis à jour dans tous les emplacements supplémentaires ainsi qu’au niveau du point de terminaison du CDN.

- Vous ne pouvez pas configurer un point de terminaison CDN pour une application déployée dans Azure intermédiaire ou dans l'émulateur Azure local dans Visual Studio. Cela affectera les tests des unités, les tests fonctionnels et le test avant le déploiement final. Vous devez autoriser cela en implémentant un autre mécanisme. Par exemple, vous pouvez prédéployer le contenu du CDN à l'aide d'un utilitaire ou d’une application personnalisée temporaire et effectuer des tests pendant la période où il est mis en cache. Autrement, utilisez des directives de compilation ou des constantes globales pour contrôler depuis quel emplacement l'application charge les ressources. Par exemple, lors de l'exécution en mode débogage, elle peut charger des ressources telles que les regroupements de script côté client et d’autres contenus à partir d'un dossier local et utiliser le CDN lors de l'exécution en mode version finale.

- Le CDN ne prend pas en charge toutes les fonctionnalités de compression natives. Toutefois, il distribue le contenu déjà compressé, tel que les fichiers zip et gzip. Lorsque vous utilisez un dossier d'application comme point de terminaison CDN, le serveur peut compresser du contenu par défaut de la même façon que lorsque vous le distribuez directement à un navigateur web ou un autre type de client. Cela dépend de la valeur **Accept-Encoding** envoyée à partir du client. Dans Azure, l’opération par défaut consiste à compresser automatiquement le contenu lorsque l'utilisation du processeur est inférieure à 50 %. La modification des paramètres peut nécessiter l'utilisation d'une tâche de démarrage pour activer la compression du résultat dynamique dans IIS si vous utilisez Cloud Services pour héberger l'application. Consultez [Activation de la compression gzip avec le CDN d’Azure via un rôle web](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx) pour plus d'informations.

- **Routage et contrôle de version** : vous devrez peut-être utiliser différentes instances de CDN. Par exemple, lorsque vous déployez une nouvelle version de l'application vous pouvez utiliser un autre CDN. Si vous utilisez le stockage d'objets blob Azure comme origine du contenu, vous pouvez simplement créer un compte de stockage ou un conteneur distinct et pointer le point de terminaison CDN vers celui-ci. Si vous utilisez le dossier racine **cdn** dans l'application en tant que point de terminaison CDN, vous pouvez utiliser des techniques de réécriture d'URL pour diriger les demandes vers un autre dossier.

- N'utilisez pas la chaîne de requête pour indiquer d’autres versions de l'application dans les liens vers les ressources sur le CDN car, lorsque le contenu est créé à partir du stockage d’objets blob Azure, la chaîne de requête fait partie du nom de la ressource (le nom de l'objet blob). Cela peut également affecter la façon dont le client met en cache les ressources.

- Le déploiement de nouvelles versions de contenu statique lorsque vous mettez à jour une application peut être problématique si les ressources précédentes sont mises en cache sur le CDN. Pour plus d’informations, consultez la section « <a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Contrôle de cache</a> ».

<a name="cachecontrol" href="#" xmlns:xlink="http://www.w3.org/1999/xlink"><span /></a>**Contrôle du cache**+ Prenez en compte la façon dont vous souhaitez gérer la mise en cache et au niveau de quelle couche de l’application. Par exemple, lors de l'utilisation d'un dossier en tant qu’origine du CDN, vous pouvez spécifier la capacité de mise en cache de pages qui génèrent du contenu et l'expiration de contenu pour toutes les ressources dans un dossier spécifique. Vous pouvez également spécifier des propriétés de cache pour le CDN et pour le client à l'aide d'en-têtes HTTP standard. Même si vous gérez probablement déjà la mise en cache sur le serveur et client, l’utilisation du CDN vous permettra de mieux voir la façon dont votre contenu est mis en cache à quel emplacement.

- Pour empêcher des objets d'être disponibles sur le CDN, vous pouvez les supprimer au point l'origine (conteneur d'objets blob ou dossier racine **cdn** de l’application), supprimer le point de terminaison CDN ou, dans le cas du stockage d'objets blob, rendre le conteneur ou l’objet blob privé. Toutefois, les éléments seront supprimés du CDN uniquement lorsque leur durée de vie (TTL) expire.

- Si aucune période d'expiration du cache n'est spécifiée (par exemple, lorsque le contenu est chargé à partir du stockage d'objets blob), ils seront mis en cache sur le CDN jusqu'à 72 heures.

- Dans une application web, vous pouvez définir la mise en cache et l’expiration pour tout le contenu à l'aide de l’élément **clientCache** dans la section **system.webServer/staticContent** d'un fichier web.config. Vous pouvez placer un fichier web.config dans n'importe quel dossier afin qu'il affecte les fichiers dans ce dossier et les fichiers dans tous les sous-dossiers.

- Si vous utilisez une technique dynamique telle que ASP.NET pour créer le contenu pour le CDN, assurez-vous de spécifier la propriété **Cache.SetExpires** sur chaque page. Le CDN ne met pas en cache le résultat des pages qui utilisent le paramètre public de capacité de mise en cache par défaut. Définissez la période d'expiration du cache à une valeur appropriée pour vous assurer que le contenu n'est pas ignoré et rechargé à partir de l'application à des intervalles très courts.

- **Sécurité** : le CDN peut distribuer du contenu via HTTPS (SSL) à l'aide du certificat fourni par le CDN, mais il sera également disponible sur HTTP. Vous ne pouvez pas bloquer l'accès HTTP aux éléments dans le CDN. Vous devrez peut-être utiliser HTTPS pour demander du contenu statique qui est affiché dans les pages chargées via HTTPS (par exemple, un Panier) afin d’éviter les avertissements de navigateur relatifs au contenu mixte.

- De nombreux services CDN, tels que le CDN Azure, ne proposent pas actuellement les installations de contrôle d'accès pour sécuriser l'accès au contenu. Vous ne pouvez pas utiliser des signatures d'accès partagé (SAS) avec le CDN.

- Si vous distribuez des scripts côté client à l'aide du CDN, vous pouvez rencontrer des problèmes si ces scripts utilisent un appel **XMLHttpRequest** pour effectuer des demandes HTTP pour d'autres ressources telles que des données, des images ou des polices dans un autre domaine. De nombreux navigateurs web empêchent le partage des ressources provenant de différentes origines (CORS) sauf si le serveur web est configuré pour définir les en-têtes de réponse appropriés. Pour plus d’informations au sujet du CORS, consultez la section « Prévention des risques » dans le guide <span class="highlight" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Considérations de sécurité des API</span>. Vous pouvez configurer le CDN pour prendre en charge le CORS : + si le stockage d’objets blob Azure est l’emplacement d’origine à partir duquel vous distribuez du contenu, vous pouvez ajouter un **CorsRule** aux propriétés de service. La règle peut spécifier les origines autorisées pour les demandes CORS, les méthodes autorisées telles que GET et la durée maximale en secondes de la règle (la période pendant laquelle le client doit demander les ressources liées après le chargement du contenu d'origine). Pour plus d’informations, consultez [Support du partage des ressources provenant de différentes origines (CORS) pour les services de stockage d’Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

- Si l'emplacement d’origine à partir duquel vous distribuez du contenu est un dossier dans l'application, tel que le dossier racine **cdn**, vous pouvez configurer des règles de trafic sortant dans le fichier de configuration de l'application pour définir un en-tête **Access-Control-Allow-Origin** sur toutes les réponses. Pour plus d'informations sur l'utilisation des règles de réécriture, consultez [Module de réécriture d'URL](http://www.iis.net/learn/extensions/url-rewrite-module). Notez que cette technique n'est pas possible si vous utilisez Sites Web Azure.

- **Les domaines personnalisés** + la plupart des CDN, y compris le CDN Azure, vous permettent de spécifier un nom de domaine personnalisé et de l'utiliser pour accéder aux ressources via le CDN. Vous pouvez également définir un nom de sous-domaine personnalisé à l'aide d’un enregistrement **CNAME** dans votre DNS. Cette approche peut fournir une couche supplémentaire d'abstraction et de contrôle.

- Si vous utilisez un enregistrement **CNAME**, vous ne pouvez pas (au moment de la rédaction de ce guide) utiliser également SSL car le CDN utilise son propre certificat SSL unique qui ne correspond pas à vos noms de domaine/sous-domaine personnalisés.

- **Secours CDN** : vous devez envisager la façon dont votre application peut faire face à une défaillance ou une indisponibilité temporaire du CDN. Les applications clientes peuvent être en mesure d'utiliser des copies des ressources qui ont été mises en cache localement (sur le client) lors de requêtes précédentes. Ils peuvent également utiliser le code qui détecte les défaillances et demander à la place des ressources à partir de l'emplacement d’origine (le dossier de l'application ou un conteneur d'objets blob Azure contenant les ressources) si le CDN n'est pas disponible.

- **SEO** : si le SEO est un aspect important de votre application, assurez-vous que vous : + d’inclure un en-tête canonique **Rel** dans chaque page ou ressource.

- Utilisez un enregistrement de sous-domaine **CNAME** et accédez aux ressources à l'aide de ce nom.

- Prenez en compte l'impact relatif au fait que l'adresse IP du CDN peut être un autre pays ou une autre région que celui/celle de l'application elle-même.

- Lorsque vous utilisez le stockage d'objets blob Azure comme origine, conservez la même structure du fichier pour les ressources sur le CDN que celle des dossiers de l'application.


- **Surveillance et journalisation** : ces opérations incluent le CDN dans le cadre de la stratégie de surveillance de votre application pour détecter et mesurer les défaillances ou l’étendue des occurrences de latence.

- Activez la journalisation pour le CDN et incluez-la dans vos opérations quotidiennes.

- **Coûts résultants** : vous êtes facturé pour les transferts de données sortantes depuis le CDN et pour les transactions de stockage lorsque le CDN charge des données à partir de votre application. Vous devez définir des délais d'expiration réalistes de cache du contenu pour garantir l’actualisation, sans que ces délais soient trop courts, ce qui entraînerait le rechargement répété du contenu à partir de l'application ou du stockage d’objets blob sur le CDN. Toutefois, des délais d’expiration très longs rendent plus difficile la suppression d’éléments sur le CDN, car vous devez attendre leur date d'expiration.

- Les éléments rarement téléchargés impliquent les deux frais de transaction sans fournir une réduction notable de la charge du serveur.



## Exemple de code
Cette section contient des exemples de code et des techniques permettant de travailler avec le CDN.


## Réécriture d'URL
À l’exception d’un fichier Web.config à la racine d’une application hébergée sur Cloud Services, les éléments suivants montrent comment effectuer la réécriture d'URL lors de l'utilisation du CDN. Les demandes du CDN pour le contenu qu'il mettra en cache sont redirigées vers des dossiers spécifiques dans la racine de l'application en fonction du type de la ressource (par exemple, les scripts et les images).

```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*).(.*)" ignoreCase="true" />
        <action type="Rewrite" url="{R:1}.{R:3}" appendQueryString="true" />
      </rule>
      <rule name="CdnImages" stopProcessing="true">
        <match url="cdn/Images/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Images/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnContent" stopProcessing="true">
        <match url="cdn/Content/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Content/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScript" stopProcessing="true">
        <match url="cdn/Scripts/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Scripts/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScriptBundles" stopProcessing="true">
        <match url="cdn/bundles/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/bundles/{R:1}" appendQueryString="true" />
      </rule>
    </rules>
  </rewrite>
  ...
</system.webServer>
```

L'ajout de règles de réécriture effectue les redirections suivantes :

- La première règle vous permet d'incorporer une version dans le nom de fichier d'une ressource, qui est alors ignorée. Par exemple, **Filename_v123.jpg **est réécrite sous la forme **Filename.jpg**. 
- Les quatre règles suivantes indiquent comment rediriger les demandes si vous ne souhaitez pas stocker les ressources dans un dossier nommé **cdn** à la racine du rôle web. Les règles mappent les URL **cdn/Images**, **cdn/Content**, **cdn/Scripts** et **cdn/bundles** vers leurs dossiers racines respectifs dans le rôle web. L’utilisation de la réécriture d'URL vous permet d’apporter quelques modifications pour le regroupement des ressources.


## Regroupement et minimisation ##

Le regroupement et la minimisation peuvent être gérés par ASP.NET. Dans un projet MVC, vous définissez vos regroupements dans **BundleConfig.cs**. Une référence au regroupement de script minimisé est créée en appelant la méthode **Script.Render**, généralement dans le code dans la classe d'affichage. Cette référence contient une chaîne de requête qui inclut un hachage basé sur le contenu du regroupement. Si le contenu du regroupement est modifié, le hachage généré est également modifié. Par défaut, les instances Azure CDN ont le paramètre **État de la chaîne de requête** désactivé. Afin que les regroupements de script mis à jour soient correctement gérés par le CDN, vous devez activer le paramètre **État de la chaîne de requête** pour l'instance CDN. Notez qu’il faut compter au minimum une heure avant la création du CDN et avant que les modifications ne soient prises en compte.


## Plus d'informations
+ [Azure CDN](http://azure.microsoft.com/services/cdn/)
+ [Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
+ [Distribution de contenu depuis Azure CDN dans votre application web](cdn-serve-content-from-cdn-in-your-web-application.md)
+ [Intégration d’un service cloud à Azure CDN](cdn-cloud-service-with-cdn.md)
+ [Meilleures pratiques pour le réseau de distribution de contenu Azure](http://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/)

<!---HONumber=July15_HO4-->