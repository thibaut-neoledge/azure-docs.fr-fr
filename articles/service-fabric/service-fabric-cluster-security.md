<properties
   pageTitle="Sécuriser un cluster Service Fabric | Microsoft Azure"
   description="Décrit les scénarios de sécurité d’un cluster Service Fabric et les différentes technologies utilisées pour implémenter ces scénarios."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="chackdan"/>

# Scénarios de sécurité d’un cluster Service Fabric

Un cluster Service Fabric est une ressource que vous possédez. Pour éviter tout accès non autorisé à cette ressource, vous devez la sécuriser, notamment lorsque des charges de travail de production sont exécutées dessus. Cet article fournit une vue d’ensemble des scénarios de sécurité pour les clusters s’exécutant sur Azure et Windows Server et des différentes technologies utilisées pour implémenter ces scénarios. Les scénarios de sécurité des clusters sont les suivants :

- Sécurité nœud à nœud
- Sécurité client à nœud
- Contrôle d’accès en fonction du rôle (RBAC)

## Sécurité nœud à nœud
Sécurise la communication entre les machines virtuelles et les ordinateurs du cluster. Cette sécurité garantit que seuls les ordinateurs qui sont autorisés à rejoindre le cluster peuvent participer à l’hébergement des applications et des services dans le cluster.

![Diagramme de communication nœud à nœud][Node-to-Node]

Les clusters qui s’exécutent sur Azure ou les clusters autonomes sous Windows peuvent utiliser la [Sécurité par certificat](https://msdn.microsoft.com/library/ff649801.aspx) ou la [Sécurité Windows](https://msdn.microsoft.com/library/ff649396.aspx). La sécurité par certificat est configurée lors de la création du cluster (avec le portail Azure ou des modèles ARM) en spécifiant un certificat principal et un certificat secondaire facultatif. Les certificats principaux et secondaires que vous spécifiez doivent être différents de ceux du client administrateur et du client en lecture seule que vous spécifiez pour la [Sécurité client à nœud](#client-to-node-security). Consultez [Sécuriser un cluster Service Fabric sur Azure à l’aide de certificats](service-fabric-secure-azure-cluster-with-certs.md) ou [Configurer un cluster à l’aide d’un modèle ARM](service-fabric-cluster-creation-via-arm.md) pour savoir comment configurer la sécurité par certificat dans un cluster s’exécutant sur Azure.

## Sécurité client à nœud
Authentifie les clients et sécurise la communication entre un client et des nœuds individuels du cluster. Ce type de sécurité authentifie et sécurise les communications du client, ce qui garantit que seuls les utilisateurs autorisés peuvent accéder au cluster et aux applications déployées sur le cluster. Les clients sont identifiés de manière grâce à leurs informations d’identification de sécurité Windows ou à leurs informations d’identification de sécurité de certificat.

![Diagramme de communication client à nœud][Client-to-Node]

Les clusters qui s’exécutent sur Azure ou les clusters autonomes sous Windows peuvent utiliser la [Sécurité par certificat](https://msdn.microsoft.com/library/ff649801.aspx) ou la [Sécurité Windows](https://msdn.microsoft.com/library/ff649396.aspx). La sécurité par certificat client à nœud est configurée lors de la création du cluster (avec le portail Azure ou des modèles ARM) en spécifiant un certificat de client administrateur et un certificat de client en lecture seule. Les certificats de client administrateur et de client en lecture seule que vous spécifiez doivent être différents des certificats principaux et secondaires que vous spécifiez pour la [Sécurité nœud à nœud](#node-to-node-security). Les clients se connectant au cluster avec le certificat administrateur ou le certificat principal ont un accès complet aux fonctions de gestion. Les clients se connectant au cluster avec le certificat de client en lecture seule ont uniquement un accès en lecture aux fonctions de gestion. Consultez [Sécuriser un cluster Service Fabric sur Azure à l’aide de certificats](service-fabric-secure-azure-cluster-with-certs.md) ou [Configurer un cluster à l’aide d’un modèle ARM](service-fabric-cluster-creation-via-arm.md) pour savoir comment configurer la sécurité par certificat dans un cluster s’exécutant sur Azure.

Service Fabric utilise des certificats de serveur X.509 que vous spécifiez dans le cadre des configurations du type de nœud, lorsque vous créez un cluster. La fin de cet article propose un rapide aperçu de ce que sont ces certificats et de la façon dont vous pouvez les acquérir ou les créer.

Les clusters s’exécutant sur Azure peuvent également sécuriser l’accès aux points de terminaison de gestion à l’aide d’Azure Active Directory (AAD). Consultez [Créer un cluster Service Fabric à l’aide d’Azure Active Directory pour l’authentification client](service-fabric-cluster-security-client-auth-with-aad.md) pour plus d’informations sur la façon de créer les artefacts AAD nécessaires, de les remplir pendant la création du cluster et de se connecter à ces clusters par la suite.

## Contrôle d’accès en fonction du rôle (RBAC)
Le contrôle d'accès permet à l'administrateur du cluster de limiter l'accès à certaines opérations de cluster pour différents groupes d'utilisateurs, renforçant ainsi la sécurité du cluster. Deux types de contrôle d’accès différents sont pris en charge pour les clients se connectant à un cluster : administrateur et utilisateur. Les administrateurs ont un accès complet aux fonctions de gestion (y compris les fonctionnalités de lecture/écriture). Les utilisateurs, par défaut, ont uniquement un accès en lecture aux fonctionnalités de gestion (par exemple, aux fonctionnalités de requête) et la capacité à résoudre les applications et les services. Vous spécifiez les rôles client utilisateur et administrateur au moment de la création du cluster en fournissant des certificats séparés pour chacun. Pour plus d’informations sur les paramètres de contrôle d’accès par défaut et sur la modification des paramètres par défaut, consultez [Contrôle d’accès en fonction du rôle pour les clients](service-fabric-cluster-security-roles.md).


## Certificats X.509 et Service Fabric
Les certificats numériques X.509 sont couramment utilisés pour authentifier les clients et serveurs et pour chiffrer et signer numériquement les messages. Pour plus d’informations sur ces certificats, consultez [Utilisation des certificats](http://msdn.microsoft.com/library/ms731899.aspx).

Quelques éléments importants à prendre en compte :

- Les certificats utilisés dans les clusters qui exécutent des charges de travail de production doivent être créés en utilisant un service de certificats Windows Server correctement configuré ou obtenus auprès d’une [autorité de certification (AC)](https://en.wikipedia.org/wiki/Certificate_authority) approuvée.
- En production, n’utilisez jamais de certificats temporaires ou de test créés avec des outils comme MakeCert.exe.
- Vous pouvez utiliser un certificat auto-signé, mais seulement pour les clusters de test et non en production.

### Certificats X.509 de serveur

La tâche principale des certificats de serveur est d’authentifier un serveur (nœud) pour les clients ou d’authentifier un serveur (nœud) pour un serveur (nœud). L’une des vérifications initiales lorsqu’un nœud ou un client authentifie un nœud consiste à vérifier la valeur du nom commun dans le champ Objet. Ce nom commun ou l’un des autres noms de l’objet des certificats doit figurer dans la liste des noms communs autorisés.

L’article suivant décrit comment générer des certificats avec d’autres noms de l’objet (SAN) : [Ajout d’un autre nom de l’objet à un certificat LDAP sécurisé](http://support.microsoft.com/kb/931351).

Le champ Objet peut contenir plusieurs valeurs, chacune étant précédée d’une initialisation pour indiquer le type de valeur. En règle générale, l’initialisation est « CN » pour « nom commun », par exemple, « CN = www.contoso.com ». Il est également possible que le champ Objet soit vide. Si le champ facultatif Autre nom de l’objet est renseigné, il doit contenir le nom commun du certificat et une entrée par autre nom de l’objet. Ils sont entrés sous forme de valeurs de nom DNS.

La valeur du champ Rôles prévus du certificat doit inclure une valeur appropriée, comme Authentification serveur ou Authentification client.

### Certificats X.509 de client

Les certificats clients ne sont généralement pas émis par une autorité de certification tierce. Au lieu de cela, le magasin personnel de l’emplacement actuel de l’utilisateur contient généralement des certificats clients placés là par une autorité racine avec un rôle prévu d’authentification client. Le client peut utiliser un certificat lorsque l’authentification mutuelle est requise.

>[AZURE.NOTE] Toutes les opérations de gestion sur un cluster Service Fabric requièrent des certificats de serveur. Les certificats clients ne peuvent pas être utilisés pour la gestion.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## Étapes suivantes
Une fois le cluster configuré, découvrez les mises à niveau de cluster :

- [Processus de mise à niveau du cluster Service Fabric et attentes](service-fabric-cluster-upgrade.md)

Découvrez plus en détails la sécurité des applications :

- [Sécurité des applications et RunAs](service-fabric-application-runas-security.md)

- [Sécuriser les communications des services](service-fabric-reliable-services-secure-communication.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_0608_2016-->