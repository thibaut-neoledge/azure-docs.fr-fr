
# Mise en réseau Azure

La méthode la plus facile pour se connecter aux données et aux applications Azure est de passer par une connexion Internet classique. Mais la solution de facilité n'est pas toujours la meilleure démarche. Azure fournit également des technologies permettant de connecter des utilisateurs aux centres de données Azure. Ce sont justement ces technologies qui sont abordées dans cet article.

## Sommaire

* [Réseau virtuel Azure](#Vnet)
* [Azure Traffic Manager](#TrafficMngr)

<a name="Vnet"></a> 

## Réseau virtuel Azure

Azure vous permet de créer des machines virtuelles qui s'exécutent dans les centres de données Microsoft. Imaginez que votre organisation souhaite utiliser ces machines virtuelles pour exécuter des applications d'entreprise ou d'autres logiciels qui seront utilisés par les employés de votre entreprise. Par exemple, vous voudrez peut-être créer une batterie de serveurs SharePoint dans le cloud ou exécuter une application de gestion des stocks. Pour faciliter le travail de vos utilisateurs, vous aimeriez que ces applications soient accessibles tout comme si elles étaient exécutées dans votre propre centre de données.

Il existe une solution standard à ce type de problème : créer un réseau privé virtuel (VPN). Les organisations de toutes les tailles choisissent cette pratique aujourd'hui pour relier, par exemple, les ordinateurs du bureau d'une filiale au centre de données principal de l'entreprise. La même démarche peut fonctionner avec les machines virtuelles Azure, comme l'illustre la figure 1.

<a name="Fig1"></a>

![01_Miseenréseau](./media/azure-networking/Networking_01Networking.png)

**Figure 1 : Le réseau virtuel Azure permet la création dans le cloud d'un réseau virtuel connecté à votre centre de données local.**

Comme le montre l'illustration, le réseau virtuel Azure vous permet de créer une limite logique autour d'un groupe de machines virtuelles, appelé *réseau virtuel ou VNET*, dans un centre de données Azure. Vous pouvez alors établir une connexion IPsec entre ce VNET et votre réseau local. Les machines virtuelles d'un VNET peuvent être créées à l'aide des machines virtuelles Azure et/ou des services cloud Azure. Autrement dit, il peut s'agir de machines virtuelles créées à l'aide de la technologie Infrastructure as a Service (IaaS) d'Azure ou de sa technologie Platform as a Service (PaaS). Quel que soit votre choix, la création de la connexion IPsec requiert un périphérique de passerelle, matériel spécialisé fixé à votre réseau local, ainsi que les services de votre administrateur réseau. Une fois que cette connexion est en place, les machines virtuelles Azure s'exécutant dans votre VNET ressemblent à n'importe quelle autre partie du réseau de votre organisation.

Comme suggéré dans la [figure 1](#Fig1), vous allouez des adresses IP aux machines virtuelles Azure à partir du même espace d'adressage IP utilisé dans votre propre réseau. Dans la situation présentée ici, qui utilise des adresses IP privées, les machines virtuelles du cloud ne sont qu'un autre sous-réseau IP. Les logiciels s'exécutant sur votre réseau local voient ces machines virtuelles comme s'il s'agissait de machines virtuelles locales, tout comme pour les VPN ordinaires. Et il est important de remarquer cela, car cette connexion se produit au niveau de l'IP, les machines virtuelles et physiques des deux côtés peuvent exécuter n'importe quel système d'exploitation. Les machines virtuelles Azure exécutant Windows Server ou Linux peuvent interagir avec les machines locales exécutant Windows, Linux ou d'autres systèmes. Il est également possible d'utiliser des outils de gestion courants, comme System Center, pour gérer les machines virtuelles du cloud et les applications qu'elles contiennent.

L'utilisation d'un réseau virtuel Azure présente un intérêt dans de nombreuses situations. Comme cela a déjà été mentionné, cette approche facilite l'accès des utilisateurs de l'entreprise aux applications cloud. Un aspect important de cette simplicité d'utilisation est la possibilité pour les machines virtuelles Azure de faire partie intégrante d'un domaine Active Directory local pour donner aux utilisateurs une authentification unique sur les applications qu'elles exécutent. Si vous préférez, vous pouvez aussi créer un domaine Active Directory dans le cloud, puis connecter ce domaine à votre réseau local.

La création d'un VNET dans un centre de données Azure vous permet d'accéder efficacement à une grande réserve de ressources à la demande. Vous pouvez créer des machines virtuelles à la demande, régler leurs frais afférents pendant leur exécution, puis les retirer (et stopper la facturation) lorsque vous n'en avez plus besoin. Cela peut s'avérer utile dans les cas où un accès rapide à une machine préconfigurée est nécessaire, comme lors du développement de nouveaux logiciels par les équipes de développement. Plutôt que d'attendre qu'un administrateur local configure les ressources nécessaires, ces utilisateurs peuvent créer ces ressources par eux-mêmes dans le cloud public.

Et tout comme le réseau virtuel permet aux machines virtuelles Azure d'apparaître en local sur les ressources locales, l'inverse se vérifie également : un logiciel s'exécutant sur votre réseau local apparaît désormais en local pour les applications s'exécutant sur votre VNET Azure. Par exemple, imaginez que vous souhaitez déplacer une application locale existante vers Azure, car vous avez calculé qu'une exploitation dans le cloud serait moins coûteuse. Mais que se passerait-il si la loi exigeait que les données utilisées par cette application soient stockées localement ? Dans une situation de ce type, l'utilisation d'un réseau virtuel permet à l'application cloud de voir un système de base de données local comme s'il s'agissait d'un système local. Son accès est alors simplifié. Quel que soit le scénario que vous choisissez, le résultat est identique : Azure devient une extension de votre propre centre de données.

<a name="TrafficMngr"></a>

## Azure Traffic Manager

Imaginez que vous ayez développé une application Azure qui rencontre beaucoup de succès. Votre application est utilisée par de nombreuses personnes aux quatre coins du monde. Cela est une excellente chose, mais la réussite est souvent accompagnée de son lot de problèmes. Par exemple, votre application s'exécute probablement dans plusieurs centres de données Azure dans plusieurs endroits dans le monde. Comment pouvez-vous diriger de manière intelligente le trafic de requêtes utilisateur vers ces centres de données pour que vos utilisateurs en profitent toujours au maximum ?

Azure Traffic Manager sert justement à résoudre ce problème. La figure 2 l'illustre.

<a name="Fig3"></a>

![03_TrafficManager](./media/azure-networking/Networking_03TrafficManager.png)

**Figure 2 : Azure Traffic Manager redirige de façon intelligence les requêtes des utilisateurs entre les instances d'une application s'exécutant dans différents centres de données Azure.**

Dans cet exemple, votre application s'exécute sur des machines virtuelles réparties entre quatre centres de données : deux aux États-Unis, un en Europe et un en Asie. Imaginez qu'un utilisateur situé à Berlin souhaite accéder à l'application. Voici ce qu'il se produit avec l'utilisation de Traffic Manager.

Comme toujours, le système de l'utilisateur recherche le nom DNS de l'application (étape 1). Cette requête est dirigée vers le système DNS Azure (étape 2), qui recherche alors la stratégie Traffic Manager pour l'application. Chaque stratégie est créée par le propriétaire d'une application Azure spécifique, soit par le biais d'une interface graphique soit par une API REST. Indépendamment de son mode de création, la stratégie spécifie l'une des trois options d'équilibrage de charge :

* **Performance :** toutes les requêtes sont envoyées au centre données présentant la plus faible latence à partir du système de l'utilisateur.
* **Basculement :** toutes les requêtes sont envoyées au centre de données spécifié par le créateur de cette stratégie, sauf si ce centre de données est indisponible. Dans ce cas, les requêtes sont dirigées vers d'autres centres de données dans l'ordre de priorité défini par le créateur de cette stratégie.
* **Tourniquet (round robin) :** toutes les requêtes sont réparties équitablement entre tous les centres de données dans lesquels l'application s'exécute.

Une fois la bonne stratégie attribuée, Traffic Manager calcule le centre de données vers lequel cette requête devrait être acheminée en fonction de l'option spécifiée parmi les trois options disponibles (étape 3). Il renvoie ensuite l'emplacement du centre de données choisi à l'utilisateur (étape 4), qui accède à cette instance de l'application (étape 5).

Pour que cela fonctionne, Traffic Manager doit disposer d'une image actuelle des instances de l'application qui sont en cours d'exécution dans chaque centre de données. Pour ce faire, Traffic Manager effectue régulièrement un ping sur chaque copie de l'application via une commande HTTP GET, puis enregistre la réception d'une réponse ou non. Si une instance d'application ne répond plus, Traffic Manager arrête de diriger des utilisateurs vers cette instance jusqu'à ce qu'elle réponde à nouveau aux commandes Ping.

Les applications ne sont pas toutes assez volumineuses ou mondiales pour avoir besoin de Traffic Manager. Toutefois, ce service peut être très utile à celles qui en ont besoin.