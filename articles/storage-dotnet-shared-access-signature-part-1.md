<properties 
	pageTitle="Signatures d'accès partagé : présentation du modèle SAP | Microsoft Azure" 
	description="Découvrez comment déléguer l'accès aux ressources d'objets blob, de files d'attente et de tables avec les signatures d'accès partagé" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>



# Signatures d'accès partagé, partie 1 : présentation du modèle SAP

L'utilisation d'une signature d'accès partagé (SAP) offre un moyen efficace pour octroyer aux autres clients un accès limité aux objets blobs, tables et files d'attente dans votre compte de stockage, sans être obligé d'exposer votre clé de compte. Dans la première partie de ce didacticiel consacré aux signatures d'accès partagé, nous allons vous présenter un aperçu du modèle SAP et examiner les meilleures pratiques concernant les signatures d'accès partagé. [La partie 2](../storage-dotnet-shared-access-signature-part-2/) du didacticiel décrit étape par étape la procédure de création de signatures d'accès partagé avec le service BLOB.

## Présentation de la signature d'accès partagé ##

Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. Cela vous permet d'octroyer à un client des autorisations d'accès limitées à vos objets blob, files d'attente ou tables pendant une période donnée, et avec un ensemble défini d'autorisations, sans avoir à partager les clés d'accès de votre compte. La signature d'accès partagé est un URI qui englobe dans ses paramètres de requête toutes les informations nécessaires pour un accès authentifié à une ressource de stockage. Pour accéder aux ressources de stockage avec la signature d'accès partagé, il suffit au client de transmettre cette dernière à la méthode ou au constructeur approprié.

## Quand devez-vous utiliser une signature d'accès partagé ? ##

Vous pouvez utiliser une signature d'accès partagé lorsque vous voulez fournir un accès aux ressources dans votre compte de stockage à un client qui ne peut pas être approuvé avec la clé du compte. Vos clés de compte de stockage incluent une clé primaire et une clé secondaire, qui octroient toutes deux un accès administratif à votre compte et à toutes ses ressources. En exposant l'une ou l'autre des clés de votre compte, vous courez le risque d'une utilisation malveillante ou négligente de ce dernier. Les signatures d'accès partagé offrent une alternative sûre qui permet aux autres clients de lire, d'écrire et de supprimer des données dans votre compte de stockage en fonction des autorisations que vous avez octroyées, sans avoir besoin de la clé du compte.

Un service où les utilisateurs lisent et écrivent leurs propres données dans votre compte de stockage correspond à un scénario courant dans lequel une signature d'accès partagé peut s'avérer utile. Dans un scénario où un compte de stockage stocke les données utilisateur, il existe deux modèles de conception types :


1\. Les clients chargent et téléchargent les données par le biais d'un service proxy frontal, qui se charge de l'authentification. Ce service présente l'avantage de permettre la validation des règles métier, mais pour de grosses quantités de données ou des transactions à haut volume, la création d'un service qui peut être mis à l'échelle en fonction de la demande peut se révéler coûteuse ou difficile.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\.	Un service léger authentifie le client en fonction des besoins, puis génère une signature d'accès partagé. Une fois que le client reçoit la signature, il peut accéder aux ressources du compte de stockage directement avec les autorisations définies par la signature d'accès partagé et pendant l'intervalle autorisé par cette dernière. La signature d'accès partagé atténue la nécessité du routage de toutes les données via le service proxy frontal.

![sas-storage-provider-service][sas-storage-provider-service]

De nombreux services dans le monde réel peuvent utiliser un mélange de ces deux approches, suivant le scénario concerné, certaines données étant traitées et validées par l'intermédiaire du proxy frontal, tandis que les autres sont enregistrées et/ou lues directement avec la signature d'accès partagé.

## Fonctionnement d'une signature d'accès partagé ##

Une signature d'accès partagé est un URI qui désigne une ressource de stockage et inclut un ensemble spécial de paramètres de requête indiquant comment le client peut accéder à la ressource. L'un de ces paramètres, la signature, est construit à partir des paramètres de signature d'accès partagé et signé avec la clé du compte. Cette signature est utilisée par Azure Storage pour authentifier la signature d'accès partagé.

Une signature d'accès partagé est définie par les contraintes suivantes, chacune représentée par un paramètre sur l'URI :

- **La ressource de stockage.** Les ressources de stockage pour lesquelles vous pouvez déléguer l'accès incluent les conteneurs, les objets blob, les files d'attente, les tables et les plages d'entités de table.
- **Heure de début.** Indique l'heure à partir de laquelle la SAP est valide. L'heure de début de la signature d'accès partagé est facultative. En cas d'omission, la SAP prend effet immédiatement.
- **Délai d'expiration.** Indique l'heure à partir de laquelle la signature n'est plus valide. Les meilleures pratiques recommandent de spécifier un délai d'expiration pour une SAP ou de l'associer à une stratégie d'accès stockée (voir ci-dessous).
- **Autorisations.** Les autorisations spécifiées sur la signature d'accès partagé indiquent quelles opérations le client peut exécuter avec cette dernière sur la ressource de stockage.

Voici un exemple d'URI de signature d'accès partagé qui fournit des autorisations de lecture et d'écriture sur un objet blob. La table décompose chaque partie de l'URI pour comprendre comment elle contribue à la signature d'accès partagé :

https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

<table border="1" cellpadding="0" cellspacing="0">
    <tbody>
        <tr>
            <td valign="top" width="213">
                <p>
                    URI de l'objet blob
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Adresse de l'objet blob. Notez que l'utilisation de HTTPS est fortement recommandée.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Version des services de stockage
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sv=2012-02-12
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Pour la version 2012-02-12 des services de stockage et les versions ultérieures, ce paramètre indique la version à utiliser.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Heure de début
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    st=2013-04-29T22%3A18%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Spécifiée au format ISO 8061. Si vous voulez que la signature d'accès partagé soit valide immédiatement, omettez l'heure de début.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Heure d'expiration
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    se=2013-04-30T02%3A23%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Spécifiée au format ISO 8061.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Ressource
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sr=b
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    La ressource est un objet blob.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Autorisations
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sp=rw
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Les autorisations octroyées par la signature d'accès partagé incluent les opérations de lecture (r) et d'écriture (w).
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Signature
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Utilisée pour authentifier l'accès à l'objet blob. La signature est un HMAC calculé sur une chaîne de signature et une clé à l'aide de l'algorithme SHA256, puis codé en Base64.
                </p>
            </td>
        </tr>
    </tbody>
</table>


## Contrôle des signatures d'accès partagé avec une stratégie d'accès stockée ##

Une signature d'accès partagé peut prendre deux formes :

- **SAP ad hoc :** lorsque vous créez une signature d'accès partagé ad hoc, l'heure de début, l'heure d'expiration et les autorisations associées à cette dernière sont spécifiées sur l'URI de signature d'accès partagé (ou implicites, dans le cas où l'heure de début est omise). Ce type d'association de sécurité peut être créé sur un conteneur, un objet blob, une table ou une file d'attente.
- **Associations de sécurité avec la stratégie d'accès stockée :** une stratégie d'accès stockée est définie sur un conteneur de ressources (un conteneur d'objets blob, une table ou une file d'attente) et peut être utilisée pour gérer les contraintes pour une ou plusieurs signatures d'accès partagé. Lorsque vous associez une SAP à une stratégie d'accès stockée, la signature hérite des contraintes (heure de début, heure d'expiration, autorisations) définies pour la stratégie d'accès.

La différence entre les deux formes est importante pour un scénario clé : la révocation. Une signature d'accès partagé est une URL. Par conséquent, toute personne qui obtient la signature peut s'en servir, quel que soit celui qui l'a demandée initialement. Si une clé d'accès partagé est publiée publiquement, elle peut être utilisée par n'importe qui. Une clé d'accès partagé qui est distribuée est valide jusqu'à ce que l'un des quatre événements suivants ait lieu :

1.	L'heure d'expiration spécifiée sur la signature d'accès partagé est atteinte.
2.	L'heure d'expiration spécifiée sur la stratégie d'accès stockée référencée par la signature d'accès partagé est atteinte (si une stratégie d'accès stockée est référencée et si elle spécifie une heure d'expiration). Cela peut arriver soit parce que l'intervalle s'est écoulé, soit parce que vous avez modifié la stratégie d'accès stockée pour définir une heure d'expiration dans le passé, ce qui est une manière de révoquer la signature d'accès partagé.
3.	La stratégie d'accès stockée référencée par la signature d'accès partagé est supprimée, ce qui est une autre manière de révoquer la signature d'accès partagé. Notez que si vous recréez la stratégie d'accès stockée avec exactement le même nom, tous les jetons de signature d'accès partagé existants seront de nouveau valides en fonction des autorisations associées à cette stratégie d'accès stockée (en partant du principe que l'heure d'expiration sur la signature d'accès partagé n'est pas passée). Si vous avez l'intention de révoquer la signature d'accès partagé, veillez à utiliser un nom différent si vous recréez la stratégie d'accès avec une heure d'expiration située dans le futur.
4.	La clé de compte qui a été utilisée pour créer la signature d'accès partagé est régénérée.  Notez que cela provoquera un échec d'authentification pour tous les composants de l'application qui utilisent cette clé de compte jusqu'à ce qu'ils soient mis à jour afin d'utiliser soit l'autre clé de compte valide soit la clé de compte nouvellement régénérée.
 
## Meilleures pratiques pour l'utilisation des signatures d'accès partagé ##

Lorsque vous utilisez des signatures d'accès partagé dans vos applications, vous devez être conscient de deux risques potentiels :

- Si une signature d'accès partagé est divulguée, toute personne qui se la procure peut s'en servir et votre compte de stockage court donc le risque d'être compromis.
- Si une signature d'accès partagé fournie à une application cliente expire et que l'application est incapable d'en récupérer une nouvelle à partir de votre service, la fonctionnalité de votre application risque d'être entravée.  

Les recommandations suivantes relatives à l'utilisation des signatures d'accès partagé vous aideront à limiter ces risques :

1. **Utilisez toujours le protocole HTTPS** pour créer ou distribuer une signature d'accès partagé..  Si une signature d'accès partagé est transmise sur HTTP et interceptée, un pirate qui lance une attaque de type " attaque de l'intercepteur " (man-in-the-middle) peut lire la signature et s'en servir exactement comme l'utilisateur concerné aurait pu le faire, d'où le risque que les données sensibles soient compromises ou que les données soient altérées par l'utilisateur malveillant.
2. **Référence des stratégies d'accès stockées lorsque cela est possible.** Les stratégies d'accès stockées vous donnent la possibilité de révoquer les autorisations sans avoir à régénérer les clés de compte de stockage. Définissez la date d'expiration sur une très longue période (voire infinie) et assurez-vous qu'elle est régulièrement repoussée.
3. **Utilisez des délais d'expiration à court terme sur une signature ad hoc.** De cette façon, même si une SAP est compromise à votre insu, elle ne sera viable que pour une courte durée. Cette pratique est particulièrement importante si vous ne pouvez pas référencer une stratégie d'accès stockée. Cette pratique vous permet également de limiter la quantité de données qui peuvent être écrite dans un objet blob en limitant le temps disponible pour le téléchargement.
4. **Disposer de clients renouvelant automatiquement les SAP si nécessaire.** Les clients doivent renouveler les SAP bien avant l'expiration prévue, afin de permettre de nouvelles tentatives si le service qui fournit la SAP n'est pas disponible. Si vos SAP sont destinées à être utilisées pour un petit nombre d'opérations immédiates de courte durée, devant terminées avant l'heure d'expiration indiquée, alors cela n'est peut-être pas nécessaire, étant donné que les SAP ne sont généralement pas renouvelées.  Toutefois, si vous possédez un client qui effectue régulièrement des demandes via des SAP, le risque d'expiration entre en jeu. La principale considération consiste à trouver l'équilibre entre la nécessité que les SAP soient de courte durée (comme indiqué plus haut) et la nécessité d'assurer que le client demande le renouvellement assez tôt pour éviter une interruption en raison de l'expiration de la SAP avant le renouvellement effectif.
5. **Soyez prudent avec l'heure de début des SAP.** Si vous définissez l'heure de début pour un SAS à **maintenant**, des différences dans l'heure actuelle en fonction des différentes machines peuvent entraîner des défaillances par intermittence pour les premières minutes.  En règle générale, définissez l'heure de début, sur au moins 15 minutes plus tôt, ou ne la définissez pas du tout, elle sera alors valide immédiatement dans tous les cas. La même chose s'applique généralement à la date d'expiration : n'oubliez pas que vous pouvez observer jusqu'à 15 minutes de décalage dans les deux sens sur toute demande. Remarque pour les clients utilisant une version plus ancienne que le 12/02/2012 : la durée maximale d'une SAP qui ne fait pas référence à une stratégie d'accès stockée est de 1 heure et toutes les stratégies spécifiant une période plus longue échoueront.
6.	**Soyez précis quant à la ressource sollicitée.** Une meilleure pratique de sécurité consiste à fournir à l'utilisateur les privilèges minimaux requis.  Si un utilisateur a uniquement besoin d'accès en lecture à une seule entité, accordez-leur l'accès en lecture à cette entité uniquement et non l'accès en lecture/écriture/suppression à toutes les entités. Cela permet également d'atténuer la menace que les SAP soit compromises, étant donné que la SAP s'avère moins utile si elle tombe entre de mauvaises mains.
7.	**Savoir que votre compte sera facturé pour toute utilisation, y compris avec les SAP.** Si vous un donnez accès en écriture à un objet blob, un utilisateur peut choisir de charger un objet blob de 200 Go. Si vous leur avez donné accès en lecture, ils peuvent choisir de le télécharger 10 fois, ce qui représente 2 To de frais de sortie pour vous.  Accordez des autorisations limitées pour atténuer les risques liés aux utilisateurs malveillants. Une SAP à durée de vie courte permet de réduire cette menace (n'oubliez pas le décalage d'horloge pour l'heure de fin).
8.	**Validez les données écrites à l'aide de SAP.** Lorsqu'une application cliente écrit des données dans votre compte de stockage, n'oubliez pas qu'il peut y avoir des problèmes avec ces données. Si votre application requiert que ces données soient validées ou autorisées avant d'être utilisées, vous devez effectuer cette validation une fois que les données sont écrites et avant qu'elles ne soient utilisées par votre application. Cette pratique protège également contre les données endommagées ou malveillantes écrites à votre compte, soit par un utilisateur qui a obtenu les SAP, soit par un utilisateur se servant de ces derniers.
9. **N'utilisez pas toujours SAP.** Parfois les risques associés à une opération particulière par rapport à votre compte de stockage dépassent les avantages des SAP.  Pour ces opérations, créez un service de niveau intermédiaire qui écrit dans votre compte de stockage après l'exécution de la règle de validation, d'authentification et d'audit. En outre, il est parfois plus simple de gérer l'accès par d'autres moyens. Par exemple, si vous souhaitez rendre tous les objets blob dans un conteneur publiquement lisibles, vous pouvez rendre le conteneur Public, au lieu de fournir une SAP à chaque client pour l'accès.
10.	**Utilisez Storage Analytics pour analyser votre application.** Vous pouvez utiliser la journalisation et les mesures afin d'observer tout pic dans les échecs d'authentification en cas de panne dans votre service de fournisseur SAP ou de suppression accidentelle d'une stratégie d'accès stockée. Consultez le [blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) pour plus d'informations.

## Conclusion ##

Les signatures d'accès partagé sont utiles pour fournir des autorisations d'accès limitées à votre compte de stockage aux clients qui ne doivent pas avoir la clé du compte.  À ce titre, elles sont un élément crucial du modèle de sécurité pour toute application utilisant Azure Storage.  Si vous suivez les meilleures pratiques énumérées ci-dessus, vous pouvez utiliser une signature d'accès partagé pour offrir une plus grande souplesse d'accès aux ressources de votre compte de stockage, sans compromettre la sécurité de votre application.

## Étapes suivantes ##

[Signatures d'accès partagé, partie 2 : création et utilisation d'une signature d'accès partagé avec le service](../storage-dotnet-shared-access-signature-part-2/)

[Gestion de l'accès aux ressources de stockage Azure](http://msdn.microsoft.com/fr-FR/library/windowsazure/ee393343.aspx)

[Délégation de l’accès avec une signature d’accès partagé (API REST)](http://msdn.microsoft.com/fr-FR/library/windowsazure/ee395415.aspx)

[Présentation des signatures d’accès partagé de table et de file d’attente](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
[sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png


<!--HONumber=42-->
