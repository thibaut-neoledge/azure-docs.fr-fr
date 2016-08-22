<properties
	pageTitle="Mise en cache personnalisée dans Azure API Management"
	description="Découvrez comment mettre en cache des éléments par clé dans le service Azure API Management"
	services="api-management"
	documentationCenter=""
	authors="darrelmiller"
	manager=""
	editor=""/>

<tags
	ms.service="api-management"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/09/2016"
	ms.author="darrmi"/>

# Mise en cache personnalisée dans Azure API Management
Le service Azure API Management prend en charge la [mise en cache de réponses HTTP](api-management-howto-cache.md) en utilisant l’URL de la ressource comme clé. La clé peut être modifiée par les en-têtes de requête à l’aide des propriétés `vary-by`. Si cette approche permet de mettre en cache l’ensemble des réponses HTTP (également appelées représentations), elle peut être aussi parfois utile pour la mise en cache d’une simple partie d’une représentation. Les nouvelles stratégies [cache-lookup-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) et [cache-store-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) permettent de stocker et récupérer des éléments de données arbitraires à partir des définitions de stratégie. Cette fonctionnalité apporte une valeur supplémentaire à la stratégie [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) présentée précédemment, puisqu’elle vous permet de mettre en cache les réponses à partir de services externes.

## Architecture  
Le service API Management utilise un cache de données partagé par client, afin que vous puissiez continuer à accéder aux mêmes données mises en cache lorsque vous déployez plusieurs unités. Lorsque vous gérez un déploiement dans plusieurs régions, chacune des régions comporte cependant des caches indépendants. C’est pourquoi il est important de ne pas traiter le cache sous la forme d’un magasin de données où il représente la seule source de certains éléments d’informations. Si vous procédez ainsi, et que vous décidez ensuite de tirer parti du déploiement dans plusieurs régions, les clients ayant des utilisateurs nomades risquent de perdre l’accès à ces données mises en cache.

## Mise en cache de fragments
Il peut arriver que, dans les réponses renvoyées, une partie des données soit difficile à déterminer bien qu’elles restent à jour pendant un laps de temps raisonnable. Pensez, par exemple, à un service généré par une compagnie aérienne qui fournit des informations concernant les réservations de vol, l’état du vol, etc. Si l’utilisateur est membre du programme de points de fidélité de la compagnie aérienne, il obtiendrait également des informations relatives à l’état actuel de son adhésion et au nombre de miles cumulé. Ces informations relatives à l’utilisateur peuvent être stockées dans un autre système, mais il peut être souhaitable de les inclure dans les réponses renvoyées concernant les réservations et l’état du vol. Cette opération peut être effectuée à l’aide d’un processus appelé « mise en cache de fragments ». La représentation primaire peut être renvoyée par le serveur d’origine à l’aide d’un type de jeton pour indiquer l’emplacement où les informations relatives à l’utilisateur doivent être insérées.

Observez la réponse JSON suivante renvoyée par une API du serveur principal.


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

Voici la ressource secondaire disponible à l’emplacement `/userprofile/{userid}` :

     { "username" : "Bob Smith", "Status" : "Gold" }

Afin de déterminer les informations utilisateur qu’il convient d’inclure, nous devons identifier l’utilisateur final. Ce mécanisme dépend de l’implémentation. Par exemple, j’utilise la revendication `Subject` d’un jeton `JWT`.

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

Nous stockons cette valeur `enduserid` dans une variable de contexte en vue d’une utilisation ultérieure. L’étape suivante consiste à déterminer si une demande précédente a déjà permis d’extraire les informations de l’utilisateur et de les stocker dans le cache. Pour cela, nous utilisons la stratégie `cache-lookup-value`.

	  <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

Si aucune entrée du cache ne correspond à la valeur de clé, aucune variable de contexte `userprofile` ne sera créée. Pour vérifier si la recherche a abouti, nous utilisons la stratégie de flux de contrôle `choose`.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


Si la variable de contexte `userprofile` n’existe pas, nous allons devoir exécuter une requête HTTP pour la récupérer.

	<send-request
      mode="new"
      response-variable-name="userprofileresponse"
      timeout="10"
      ignore-error="true">

      <!-- Build a URL that points to the profile for the current end-user -->
      <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
          (string)context.Variables["enduserid"]).AbsoluteUri)
      </set-url>
      <set-method>GET</set-method>
	</send-request>

Nous utilisons le paramètre `enduserid` pour construire l’URL sur la ressource de profil utilisateur. Une fois la réponse obtenue, nous pouvons extraire le texte du corps de la réponse et le stocker dans une variable de contexte.

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

Pour nous éviter d’avoir à réexécuter cette requête HTTP, lorsque l’utilisateur effectue une autre requête, nous pouvons stocker le profil utilisateur dans le cache.

	<cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

Nous stockons la valeur dans le cache en utilisant la même clé que celle que nous avons initialement tenté de récupérer. La durée du stockage de la valeur doit dépendre de la fréquence à laquelle les informations sont modifiées et du degré de tolérance des utilisateurs face à des informations obsolètes.

Il est important de comprendre que la récupération de données du cache est une requête réseau hors processus et qu’elle peut potentiellement ajouter des dizaines de millisecondes à la requête. On peut y voir des avantages lorsqu’il faut plus de temps que prévu pour déterminer les informations de profil utilisateur si l’on doit exécuter des requêtes de base de données ou agréger des informations à partir de plusieurs serveurs principaux.

La dernière étape du processus consiste à mettre à jour la réponse renvoyée avec nos informations de profil utilisateur.

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

J’ai choisi d’inclure les guillemets dans le jeton de sorte que même si l’opération de remplacement ne se produit pas, la réponse renvoyée prenne toujours la forme d’un script JSON valide. L’idée était ici principalement de faciliter le débogage.

Une fois que l’on combine toutes ces étapes, on obtient une stratégie semblable à ce qui suit.

     <policies>
    	<inbound>
    		<!-- How you determine user identity is application dependent -->
    		<set-variable
              name="enduserid"
              value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

    		<!--Look for userprofile for this user in the cache -->
    		<cache-lookup-value
              key="@("userprofile-" + context.Variables["enduserid"])"
              variable-name="userprofile" />

    		<!-- If we don’t find it in the cache, make a request for it and store it -->
    		<choose>
    			<when condition="@(!context.Variables.ContainsKey("userprofile"))">
    				<!—Make HTTP request to get user profile -->
    				<send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
    					<set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
    					<set-method>GET</set-method>
    				</send-request>

    				<!—Store response body in context variable -->
    				<set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

    				<!—Store result in cache -->
    				<cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
    			</when>
    		</choose>
    		<base />
    	</inbound>
    	<outbound>
    		<!—Update response body with user profile-->
    		<find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
    		<base />
    	</outbound>
     </policies>

Cette approche de mise en cache est principalement utilisée dans les sites web où le script HTML est composé côté serveur afin de pouvoir être restitué sur une seule page. Elle peut toutefois être également utile dans les API où les clients ne peuvent pas effectuer de mise en cache HTTP côté client, ou lorsqu’il est préférable de ne pas confier cette responsabilité au client.

Ce même type de mise en cache de fragments peut également être effectué sur les serveurs web principaux qui utilisent un serveur de mise en cache Redis ; il est cependant utile de s’appuyer sur le service API Management pour exécuter cette opération lorsque les fragments mis en cache proviennent de serveurs principaux différents de ceux des réponses principales.

## Contrôle de version transparent
Il n’est pas rare que plusieurs versions différentes d’une implémentation d’une API soient prises en charge simultanément. Cette prise en charge simultanée permet, par exemple, de supporter des environnements différents (environnements de développement, de test, de production, etc.) ou bien de prendre en charge les versions antérieures de l’API pour laisser aux consommateurs de l’API le temps de migrer vers des versions plus récentes.

Au lieu de demander aux développeurs client de modifier les URL de `/v1/customers` à `/v2/customers`, il est envisageable de stocker dans les données de profil du consommateur la version de l’API qu’il souhaite actuellement utiliser et d’appeler l’URL du serveur principal concerné. Afin de déterminer l’URL de serveur principal qu’il convient d’appeler pour un client particulier, il est nécessaire d’interroger des données de configuration. En mettant en cache ces données de configuration, nous pouvons limiter la perte de performances associée à ce processus de recherche.

La première étape consiste à déterminer l’identificateur utilisé pour configurer la version souhaitée. Dans cet exemple, j’ai choisi d’associer la version à la clé d’abonnement du produit.

		<set-variable name="clientid" value="@(context.Subscription.Key)" />

Nous devons ensuite effectuer une recherche dans le cache pour vérifier si nous avons déjà récupéré la version client appropriée.

		<cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

Puis nous vérifions si cette version a ou non été trouvée dans le cache.

	<choose>
		<when condition="@(!context.Variables.ContainsKey("clientversion"))">

Dans le cas contraire, il est alors possible de l’extraire.

	<send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
        		<set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
        		<set-method>GET</set-method>
	</send-request>

Extrayez le texte du corps de réponse à partir de la réponse.

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

Stockez-le de nouveau dans le cache pour une utilisation ultérieure.

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

Pour finir, mettez à jour l’URL du serveur principal pour sélectionner la version du service souhaitée par le client.

	<set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

La stratégie complète se présente comme suit.

	 <inbound>
		<base />
		<set-variable name="clientid" value="@(context.Subscription.Key)" />
		<cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

		<!-- If we don’t find it in the cache, make a request for it and store it -->
		<choose>
			<when condition="@(!context.Variables.ContainsKey("clientversion"))">
				<send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
					<set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
					<set-method>GET</set-method>
				</send-request>
				<!-- Store response body in context variable -->
				<set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
				<!-- Store result in cache -->
				<cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
			</when>
		</choose>
		<set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
	</inbound>


En permettant aux consommateurs d’API de contrôler en toute transparence la version du serveur principal utilisée par les clients sans avoir à mettre à jour et redéployer les clients, nous proposons une solution pratique qui résout de nombreux problèmes liés au contrôle de version des API.

## Isolation des locataires

Dans les déploiements mutualisés plus volumineux, certaines entreprises créent des groupes de locataires spécifiques sur des déploiements distincts de matériel du serveur principal. Cette approche contribue à réduire au minimum le nombre de clients affectés par un problème matériel sur le serveur principal. Elle permet également de déployer de nouvelles versions de logiciels de manière progressive. Dans l’idéal, cette architecture de serveur principal doit être transparente pour les consommateurs d’API. Cet objectif est réalisable en adoptant une approche similaire à celle du contrôle de version transparent, puisqu’il repose sur la même technique de manipulation de l’URL du serveur principal qui utilise un état de configuration par clé d’API.

Au lieu de renvoyer une version par défaut de l’API pour chaque clé d’abonnement, vous devez retourner un identificateur qui associe un locataire au groupe matériel attribué. Cet identificateur peut être utilisé pour construire l’URL du serveur principal approprié.

## Résumé
L’utilisation du cache du service Azure API Management pour le stockage de n’importe quel type de données permet d’accéder efficacement aux données de configuration susceptibles d’affecter le mode de traitement d’une demande entrante. Cette mise en cache peut également être utilisée pour stocker des fragments de données pouvant augmenter les réponses retournées à partir d’une API du serveur principal.

## Étapes suivantes
Faites-nous part de vos commentaires dans le thread Disqus de cette rubrique si l’utilisation des stratégies décrites s’est révélée adaptée à d’autres scénarios, ou s’il existe des scénarios que vous souhaiteriez mettre en œuvre mais qu’il vous semble impossible de déployer actuellement.

<!---HONumber=AcomDC_0810_2016-->