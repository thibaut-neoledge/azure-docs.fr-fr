# Compute

Azure vous permet de déployer et de surveiller le code de votre application
lors de son exécution dans un centre de données Microsoft. Quand vous créez une application
et que vous l'exécutez sur Azure, l'ensemble formé par le code et la configuration est
appelé un « service hébergé Azure ». Les services hébergés sont faciles à
gérer, faire évoluer, reconfigurer et mettre à jour avec les nouvelles versions du code de
votre application. Cet article décrit le modèle d'application de service hébergé
Azure.<span id="compare"></span></a>

## Sommaire<span id="_GoBack"></span></a>

-   [Avantages du modèle d’application Azure][]
-   [Principaux concepts des services hébergés][]
-   [Considérations relatives à la conception des services hébergés][]
-   [Conception de votre application pour l’extensibilité][]
-   [Définition et configuration des services hébergés][]
-   [Fichier de définition de service][]
-   [Fichier de configuration de service][]
-   [Création et déploiement d’un service hébergé][]
-   [Références][]

## <span id="benefits"></span> </a>Avantages du modèle d’application Azure

Quand vous déployez votre application en tant que service hébergé, Azure
crée une ou plusieurs machines virtuelles qui contiennent le code de votre
application, puis démarre les machines virtuelles sur des machines physiques qui se trouvent dans un
des centres de données Azure. Quand les demandes d'un client adressées à votre application
hébergée parviennent au centre de données, un programme d'équilibrage de la charge les
distribue de manière équitable entre les machines virtuelles. Si votre application est hébergée dans
Azure, elle profite de trois avantages essentiels :

-   **Haute disponibilité :** cela signifie qu'Azure s'assure
    que votre application est exécutée autant que possible et est capable
    de répondre aux demandes des clients. Si votre application s'arrête (par exemple
    à cause d'une exception non gérée), Azure le détecte
    et la redémarre automatiquement. Si la
    machine sur laquelle votre application s'exécute tombe en panne
    suite à un problème matériel, Azure le détecte également et
    crée automatiquement une nouvelle machine virtuelle sur une autre machine physique en fonctionnement
    afin d'y exécuter votre code. REMARQUE : pour que votre application obtienne
    la disponibilité à 99,95 % du contrat de niveau de service de Microsoft, vous
    devez avoir au moins deux machines virtuelles exécutant le code de votre application. Ceci permet à une machine
    virtuelle de traiter les demandes des clients pendant qu'Azure déplace votre code d'une
    machine virtuelle en panne vers une autre qui fonctionne correctement.

-   **Extensibilité :** Azure vous permet de modifier facilement et
    dynamiquement le nombre de machines virtuelles exécutant le code de votre application, pour améliorer
    la gestion de la charge de travail traitée par votre application. Vous pouvez ainsi
    adapter votre application à la charge de travail générée
    par vos clients, tandis que seules vous sont facturées les machines virtuelles
    dont vous avez besoin au moment où vous en avez besoin. Quand vous voulez changer le nombre de machines virtuelles, Azure
    répond en quelques minutes, ce qui vous permet de changer dynamiquement le
    nombre de machines virtuelles en cours d'exécution, aussi souvent que nécessaire.

-   **Capacité de gestion :** comme Azure est une offre PaaS
    (Platform as a Service), il gère l'infrastructure (le matériel lui-même,
    la consommation électrique et le réseau) requise pour conserver ces machines
    en fonctionnement. Azure gère également la plateforme, garantissant
    la mise à jour du système d'exploitation à l'aide des mises à jour de sécurité
    et des correctifs adéquats, ainsi que des mises à jour des composants, comme le
    .NET Framework et Internet Information Server. Comme toutes les machines virtuelles
    exécutent Windows Server 2008, Azure fournit des fonctionnalités
    supplémentaires, comme la surveillance des diagnostics, la prise en charge du Bureau à distance,
    les pare-feu et la configuration de magasins de certificats. Toutes ces fonctionnalités
    sont fournies sans coûts supplémentaires. En fait, quand vous exécutez votre
    application dans Azure, la licence du système d'exploitation
    Windows Server 2008 est incluse. Comme toutes les machines virtuelles
    exécutent Windows Server 2008, le code exécuté sous Windows Server 2008 fonctionne
    parfaitement sous Azure.

## <span id="concepts"></span> </a>Principaux concepts des services hébergés

Quand votre application est déployée comme service hébergé dans Azure,
elle est exécutée sous la forme d'un ou plusieurs *rôles*. Un *rôle* fait simplement référence à des
fichiers et à une configuration d'application. Vous pouvez définir un ou plusieurs rôles
pour votre application, chacun ayant son propre ensemble
de fichiers d'application et sa propre configuration. Pour chaque rôle de votre application, vous pouvez spécifier le
nombre de machines virtuelles, ou *instances de rôle*, à exécuter. La figure suivante montre deux
exemples simples d'une application modélisée en tant que service hébergé avec
des rôles et des instances de rôle.

##### Figure 1 : rôle unique avec trois instances (machines virtuelles) exécutées dans un centre de données Azure

![image][]

##### Figure 2 : deux rôles, contenant chacun deux instances (machines virtuelles) exécutées dans un centre de données Azure

![image][1]

Les instances de rôle traitent généralement les demandes des clients Internet
entrant dans le centre de données via ce qui est appelé un *point de terminaison d'entrée*. Un même rôle
peut avoir zéro ou plusieurs points de terminaison d'entrée. Chaque point de terminaison indique un protocole
(HTTP, HTTPS ou TCP) et un port. Un rôle est généralement configuré avec
deux points de terminaison d'entrée : une écoute HTTP sur le port 80 et une écoute HTTPS
sur le port 443. La figure ci-dessous montre un exemple de deux rôles différents
avec des points de terminaison d'entrée différents dirigeant les demandes du client vers ces rôles.

![image][2]

Quand vous créez un service hébergé dans Azure, une adresse IP adressable
publiquement lui est affectée, que les clients peuvent utiliser pour y accéder. Une fois
le service hébergé créé, vous devez également sélectionner un préfixe
d'URL mappé à cette adresse IP. Comme vous réservez
l'URL *préfixe*.cloudapp.net à votre utilisation exclusive, ce préfixe
doit être unique. Les clients communiquent avec vos instances de rôle en
utilisant l'URL. Vous ne distribuez ni ne publiez généralement pas l’URL
*préfixe*.cloudapp.net Azure. Vous allez plutôt acheter un nom DNS
auprès du bureau d'enregistrement DNS de votre choix, puis configurer votre nom DNS pour rediriger
les demandes du client vers l'URL Azure. Pour plus d’informations, consultez la page
[Configuration d’un nom de domaine personnalisé dans Azure][].

## <span id="considerations"></span> </a>Considérations relatives à la conception des services hébergés

Quand vous concevez une application pour un environnement cloud, vous devez prendre en compte
plusieurs éléments comme la latence, la
haute disponibilité et l'extensibilité.

Par exemple, le choix de l'emplacement du code de votre application est
important quand vous exécutez un service hébergé dans Azure. Il est courant
de déployer votre application vers les centres de données qui sont les plus proches de
vos clients, de façon à réduire la latence et obtenir les meilleures performances possibles.
Cependant, vous pouvez être amené à choisir un emplacement plus proche de votre entreprise ou de
vos données pour des raisons légales ou réglementaires concernant
vos données et leur emplacement. Six centres de données à travers le monde
peuvent héberger le code de votre application. Le tableau ci-dessous indique
les emplacements disponibles :

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tr><td style="width: 100px;">**Pays/région**</td><td style="width: 200px;">**Sous-régions**</td></tr>
<tr><td>États-Unis         </td><td>Centre Sud et Centre Nord</td></tr>
<tr><td>Europe             </td><td>Nord et Ouest            </td></tr>
<tr><td>Asie               </td><td>Sud-Est et Est           </td></tr>
</table>

Lors de la création d'un service hébergé, vous sélectionnez une sous-région indiquant
l'emplacement où vous voulez que votre code s'exécute.

Pour obtenir des capacités de haute disponibilité et d'extensibilité,
il est extrêmement important que les données de votre application soient conservées dans un référentiel centralisé, accessible
par plusieurs instances de rôle. C'est pour cette raison qu'Azure
propose différentes options de stockage, comme des objets blob, des tables et la base de données SQL. Pour plus
d'informations sur ces technologies de stockage,consultez l'article
[Offres de stockagede données dans Azure][]. La figure ci-dessous montre comment
le programme d'équilibrage de la charge du centre de données Azure
distribue les demandes des clients vers différentes instances de rôle
ayant accès au même stockage de données.

![image][3]

Généralement, vous voulez placer le code de votre application et vos données dans le
même centre de données, car cela permet de bénéficier d'une latence faible (et donc de meilleures performances)
quand le code de votre application accède aux données. De plus, vous n'êtes pas facturé
pour la bande passante quand les données sont déplacées au sein d'un même centre
de données.

## <span id="scale"></span> </a>Conception de votre application pour l’extensibilité

Vous pouvez parfois avoir besoin d'héberger une application simple (comme un site
web simple) dans Azure. Mais la plupart du temps, votre
application peut se composer de différents rôles qui travaillent tous ensemble. Par
exemple, dans la figure ci-dessous, vous pouvez voir deux instances du rôle
Site web, trois instances du rôle Traitement des commandes et une instance du
rôle Générateur de rapports. Ces rôles travaillent tous ensemble et leurs
codes respectifs peuvent être packagés et déployés comme une seule unité vers Azure.

![image][4]

La raison principale pour diviser une application en plusieurs rôles
exécutant chacun son propre ensemble d'instances de rôle (c'est-à-dire des machines virtuelles) est de pouvoir mettre à l'échelle
les rôles indépendamment. Par exemple, pendant les vacances, de nombreux
clients peuvent acheter des produits de votre société, vous allez donc
augmenter le nombre d'instances de rôle exécutant votre rôle Site web,
ainsi que le nombre d'instances exécutant votre rôle
Traitement des commandes. Après les vacances, vous pouvez avoir beaucoup de retours de produits.
Il vous faudra donc beaucoup d'instances Site web, mais moins d'instances
Traitement des commandes. Pendant le reste de l'année, il est possible que vous n'ayez besoin que de
quelques instances Site web et Traitement des commandes. Et pour tout ceci, il se peut que vous
ayez besoin d'une seule instance Générateur de rapports. La flexibilité des
déploiements basés sur des rôles dans Azure vous permet d'adapter facilement votre
application selon vos besoins.

Il est courant que les instances de rôle de votre service hébergé
communiquent les unes avec les autres. Par exemple, un rôle Site web peut accepter
la commande d'un client avant d'en déléguer le traitement aux instances de rôle
Traitement des commandes. La meilleure méthode de transmission des tâches
d'un ensemble d’instances de rôle à un autre est d'utiliser la technologie
de mise en file d'attente fournie par Azure, que ce soit le service de file d'attente ou les files
d'attente Service Bus. L'utilisation d'une file d'attente est une partie critique
de ce scénario. Grâce aux files d'attente, le service hébergé peut mettre à l'échelle ses rôles
de façon indépendante, ce qui vous permet d'équilibrer la charge de travail par rapport aux coûts. Si le
nombre de messages de la file d'attente augmente au fil du temps, vous pouvez
augmenter le nombre d'instances de rôle Traitement des commandes. Si le nombre de
messages de la file d'attente diminue au fil du temps, vous pouvez diminuer le
nombre d'instances de rôle Traitement des commandes. Ainsi, vous ne payez que pour
les instances requises pour gérer la charge de travail réelle.

Les files d’attente améliorent également la fiabilité. Quand vous réduisez le nombre d'instances de rôle
Traitement des commandes, Azure décide des instances
à arrêter. Il peut décider d'arrêter une instance qui est en train de
traiter un message en file d'attente. Cependant, comme le traitement du
message ne se termine pas correctement, ce dernier redevient visible pour
une autre instance de rôle Traitement des commandes qui le sélectionne
et le traite. Grâce à la visibilité des messages en file d'attente, vous
pouvez être sûr que les messages finiront toujours par être traités. La file d'attente fait aussi office
d'équilibreur de charge en distribuant efficacement ses messages aux instances
de rôle qui leur demandent des messages.

Dans le cas des instances de rôle Site web, vous pouvez surveiller le trafic
entrant et décider de les augmenter ou de les diminuer. La file
d'attente vous permet de mettre à l'échelle le nombre d'instances de rôle Site web
indépendamment des instances de rôle Traitement des commandes. Ceci est extrêmement
puissant et accroît considérablement votre flexibilité. Bien sûr, si votre
application comporte des rôles supplémentaires, vous pouvez ajouter d'autres
files d''attente pour les faire communiquer entre eux et profiter ainsi des
mêmes avantages en termes d'extensibilité et de coûts.

## <span id="defandcfg"></span> </a>Définition et configuration des services hébergés

Le déploiement d'un service hébergé sur Azure requiert un fichier de
définition de service et un fichier de configuration de service. Ces deux
fichiers sont au format XML et vous permettent de spécifier de manière déclarative des options
de déploiement pour votre service hébergé. Le fichier de définition de service
décrit tous les rôles constituant votre service hébergé et la façon
dont ils communiquent. Le fichier de configuration de service décrit le nombre
d'instances pour chaque rôle et les paramètres utilisés pour configurer chaque instance
de rôle.

## <span id="def"></span> </a>Fichier de définition de service

Comme mentionné plus haut, le fichier de définition de service (CSDEF) est un fichier XML
décrivant les différents rôles constituant votre
application. Le schéma complet pour le fichier XML peut être trouvé ici :
[<http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758711.aspx>][].
Le fichier CSDEF contient un élément WebRole ou WorkerRole pour chaque rôle
que vous voulez dans votre application. Déployer un rôle en tant que rôle web (à l'aide de l'élément
WebRole) signifie que le code s'exécutera sur une instance de rôle
contenant Windows Server 2008 et Internet Information Server (IIS).
Déployer un rôle en tant que rôle de travail (à l'aide de l'élément WorkerRole) signifie
que Windows Server 2008 sera installé sur l'instance de rôle (IIS ne sera
pas installé).

Vous pouvez tout à fait créer et déployer un rôle de travail qui utilise d'autres mécanismes
pour écouter les demandes web entrantes (par exemple, votre
code pourrait créer et utiliser un élément HttpListener .NET). Comme les instances de rôle
exécutent toutes Windows Server 2008, votre code peut effectuer toutes
les opérations normalement disponibles pour une application exécutée sur Windows Server.

1.  

Pour chaque rôle, indiquez la taille de la machine virtuelle souhaitée que
les instances de ce rôle doivent utiliser. Le tableau ci-dessous répertorie les différentes tailles de machines virtuelles disponibles
actuellement, ainsi que les attributs de chacune d'elles :

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tr><td>**Taille de la machine virtuelle**</td><td>**UC** </td><td>**RAM**</td><td>**Disque**</td><td>**Pic réseau en E/S**</td></tr>
<tr><td>**Tres petite**                   </td><td>1 x 1,0 GHz</td><td>768 Mo     </td><td>20 Go         </td><td>\~5 Mbits/s             </td></tr>
<tr><td>**Petite**                        </td><td>1 x 1.6 GHz</td><td>1,75 Go    </td><td>225 Go        </td><td>\~100 Mbits/s           </td></tr>
<tr><td>**Moyenne**                       </td><td>2 x 1.6 GHz</td><td>3,5 Go     </td><td>490 Go        </td><td>\~200 Mbits/s           </td></tr>
<tr><td>**Grande**                        </td><td>4 x 1.6 GHz</td><td>7 Go       </td><td>1 To          </td><td>\~400 Mbits/s           </td></tr>
<tr><td>**Tres grande**                   </td><td>8 x 1.6 GHz</td><td>14 Go      </td><td>2 To          </td><td>\~800 Mbits/s           </td></tr>
</table>

Chaque machine virtuelle que vous utilisez comme instance de rôle vous est facturée à l'heure.
Les données que vos instances de rôle envoient hors du centre de données
vous sont également facturées. Vous ne devez pas payer pour les données entrant dans le centre de données. Pour
plus d'informations, consultez [Tarifs Azure][]. En général, il est
conseillé d'utiliser de nombreuses petites instances de rôle plutôt que quelques grandes
instances, de façon à ce que votre application soit plus résiliente face aux défaillances. En effet,
moins vous aurez d'instances de rôle, plus la défaillance de l'une
d'entre elles sera lourde de conséquences pour votre application globale. De même, comme mentionné plus haut,
vous devez déployer au moins deux instances pour chaque rôle,
afin d''obtenir la disponibilité de 99,95 % du contrat de niveau de service de Microsoft.

Le fichier de définition de service (CSDEF) permet également de spécifier de nombreux
attributs de chaque rôle de votre application. Voici certains des éléments
les plus utiles pour vous :

-   **Certificats** : vous pouvez utiliser des certificats pour chiffrer des données ou si
    votre service web prend en charge SSL. Chaque certificat doit être téléchargé
    sur Azure. Pour plus d'informations, consultez [Gestion des certificats
    dans Azure][]. Ce paramètre XML installe les certificats précédemment
    téléchargés dans le magasin de certificats de l'instance de rôle pour
    permettre au code de votre application de les utiliser.

-   **Nom des paramètres de configuration** : pour les valeurs qui doivent être lues par votre ou vos
    applications lors de leur exécution sur une instance de rôle. La valeur réelle
    des paramètres de configuration est définie dans le fichier de
    configuration de service (CSCFG), qui peut être mis à jour à tout moment sans
    qu'il soit nécessaire de redéployer votre code. En fait, vous pouvez coder vos
    applications pour qu'elles détectent les modifications des
    valeurs de configuration sans impliquer de temps d'indisponibilité.

-   **Points de terminaison d’entrée** : vous pouvez indiquer ici les points de terminaison HTTP, HTTPS ou TCP
    (avec les ports) que vous voulez exposer au monde extérieur
    via votre URL *préfixe*.cloadapp.net. Quand Azure déploie votre
    rôle, il configure automatiquement le pare-feu sur l'instance
    de rôle.

-   **Points de terminaison internes** : vous pouvez spécifier ici les points de terminaison HTTP ou TCP
    que vous voulez exposer aux autres instances de rôle déployées
    dans le cadre de votre application. Les points de terminaison internes permettent
    à toutes les instances de rôle de votre application de communiquer entre elles, mais ils ne sont pas accessibles
    aux instances de rôle situées en dehors de l'application.

-   **Modules d’importation** : si vous le souhaitez, ils installent des composants utiles
    sur vos instances de rôle. Des composants existent pour la surveillance des diagnostics,
    le Bureau à distance et Azure Connect (qui permet à votre instance
    de rôle d'accéder à des ressources locales via un canal sécurisé).

-   **Stockage local** : ceci alloue un sous-répertoire sur l'instance
    de rôle utilisable par votre application. Pour plus d'informations sur cette
    option, consultez l'article [Offres de stockage de données dans Azure][Offres de stockagede données dans Azure].

-   **Tâches de démarrage** : elles vous permettent d'installer des composants
    prérequis sur une instance de rôle quand elle démarre. Ces tâches
    peuvent être exécutées avec élévation de privilèges en tant qu'administrateur.

## <span id="cfg"></span> </a>Fichier de configuration de service

Le fichier de configuration de service (CSCFG) est un fichier XML qui décrit
les paramètres que vous pouvez modifier sans redéployer votre application. Le schéma
complet pour le fichier XML peut être trouvé ici :
[][]<http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758710.aspx></a>.
Le fichier CSDEF contient un élément Role pour chaque rôle
de votre application. Voici certains des éléments que vous pouvez spécifier dans le fichier
CSCFG :

-   **Version du système d’exploitation** : cet attribut vous permet de sélectionner la version du
    système d'exploitation à utiliser pour toutes les instances de rôle
    exécutant le code de votre application. Ce système d'exploitation est appelé *système d'exploitation invité* et chaque nouvelle
    version inclut les correctifs et mises à jour de sécurité les plus récents,
    disponibles au moment de la publication du système d'exploitation invité. Si vous définissez la valeur de l'attribut
    osVersion à « \* », Azure met automatiquement
    à jour le système d'exploitation invité sur chacune de vos instances de rôle,
    dès que de nouvelles versions du système d'exploitation invité sont disponibles. Cependant, vous pouvez désactiver les mises
    à jour automatiques en sélectionnant une version spécifique du système d'exploitation invité. Par exemple,
    en configurant l'attribut osVersion avec la valeur
    « WA-GUEST-OS-2.8\_201109-01 », vos instances de rôle obtiennent ce qui
    est décrit sur cette page web :
    [][5]<http://msdn.microsoft.com/fr-fr/library/hh560567.aspx></a>. Pour plus
    d'informations sur les versions des systèmes d'exploitation invité, consultez [Gestion des mises à niveau vers les systèmes
    d'exploitation invités d'Azure][].

-   **Instances** : la valeur de cet élément indique le nombre d'instances
    de rôle que vous voulez configurer en exécutant le code pour un rôle
    particulier. Comme vous pouvez télécharger un nouveau fichier CSCFG vers Azure
    (sans redéployer votre application), il est très simple de
    modifier la valeur de cet élément et de télécharger un nouveau fichier CSCFG pour augmenter ou diminuer dynamiquement
    le nombre d'instances de rôle exécutant le
    code de votre application. Ceci vous permet de faire évoluer
    rapidement votre application pour répondre aux demandes des charges de travail réelles tout
    en contrôlant le coût de l'exécution des instances de rôle.

-   **Valeur des paramètres de configuration** : cet élément indique les valeurs
    des paramètres (comme défini dans le fichier CSDEF). Votre rôle peut lire ces
    valeurs durant son exécution. Ces valeurs de paramètres de configuration sont
    généralement utilisées pour les chaînes de connexion à une base de données SQL ou au stockage
    Azure, mais elles peuvent également être utilisées à d'autres fins, selon vos besoins.

## <span id="hostedservices"></span> </a>Création et déploiement d’un service hébergé

Pour créer un service hébergé, vous devez d'abord accéder au [portail de gestion Azure][] et configurer un service hébergé en indiquant un
préfixe DNS et le centre de données sur lequel vous voulez exécuter
votre code. Ensuite, dans votre environnement de développement, vous créez votre fichier de définition de
service, vous générez le code et le package de votre application, et vous placez tous ces fichiers
dans un fichier de package de services (CSPKG). Vous devez également
préparer votre fichier de configuration de service (CSCFG). Pour déployer votre rôle,
vous téléchargez les fichiers CSPKG et CSCFG avec l'API
de gestion des services Azure. Une fois le déploiement effectué, Azure configure les instances
de rôle dans le centre de données (en fonction des données de configuration),
extrait le code de votre application du package, le copie dans
les instances de rôle, puis démarre ces instances. ﻿Votre code est désormais opérationnel.

La figure ci-dessous montre les fichiers CSPKG et CSCFG créés sur votre
ordinateur de développement. Le fichier CSPKG contient le fichier CSDEF et le code
pour deux rôles. Une fois les fichiers CSPKG et CSCFG téléchargés avec
l'API de gestion des services Azure, Azure crée les instances de rôle
dans le centre de données. Dans cet exemple, le fichier CSCFG indique
qu'Azure doit créer trois instances du rôle \#1 et deux instances
du rôle \#2.

![image][6]

Pour plus d'informations sur le déploiement, la mise à niveau et la reconfiguration de vos
rôles, consultez l'article [Déploiement et mise à niveau des applications Azure][]
<span id="Ref"></span></a>.

## <span id="references"></span> </a>Références

-   [Création d’un service hébergé pour Azure][]

-   [Gestion des services hébergés dans Azure][]

-   [Migration des applications vers Azure][]

-   [Configuration d’une application Azure][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>&Eacute;crit par Jeffrey Richter (Wintellect)</p>

</div>

  [Avantages du modèle d’application Azure]: #benefits
  [Principaux concepts des services hébergés]: #concepts
  [Considérations relatives à la conception des services hébergés]: #considerations
  [Conception de votre application pour l’extensibilité]: #scale
  [Définition et configuration des services hébergés]: #defandcfg
  [Fichier de définition de service]: #def
  [Fichier de configuration de service]: #cfg
  [Création et déploiement d’un service hébergé]: #hostedservices
  [Références]: #references
  [image]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuration d’un nom de domaine personnalisé dans Azure]: http://www.windowsazure.com/fr-fr/develop/net/common-tasks/custom-dns/
  [Offres de stockagede données dans Azure]: http://www.windowsazure.com/fr-fr/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  [Tarifs Azure]: http://www.windowsazure.com/fr-fr/pricing/calculator/
  [Gestion des certificats dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg981929.aspx
  []: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758710.aspx
  [5]: http://msdn.microsoft.com/fr-fr/library/hh560567.aspx
  [Gestion des mises à niveau vers les systèmes d'exploitation invités d'Azure]: http://msdn.microsoft.com/fr-fr/library/ee924680.aspx
  [portail de gestion Azure]: http://manage.windowsazure.com/
  [6]: ./media/application-model/application-model-8.jpg
  [Déploiement et mise à niveau des applications Azure]: http://www.windowsazure.com/fr-fr/develop/net/fundamentals/deploying-applications/
  [Création d’un service hébergé pour Azure]: http://msdn.microsoft.com/fr-fr/library/gg432967.aspx
  [Gestion des services hébergés dans Azure]: http://msdn.microsoft.com/fr-fr/library/gg433038.aspx
  [Migration des applications vers Azure]: http://msdn.microsoft.com/fr-fr/library/gg186051.aspx
  [Configuration d’une application Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee405486.aspx
