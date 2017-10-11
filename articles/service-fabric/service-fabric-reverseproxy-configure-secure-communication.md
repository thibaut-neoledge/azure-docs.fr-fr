---
title: "Communication sécurisée avec le proxy inverse Azure Service Fabric | Microsoft Docs"
description: "Configurer le proxy inverse pour permettre la communication de bout en bout sécurisée."
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 568f9638c59282bcd7d3fae058a1588a889c22dc
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2017
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Se connecter à un service sécurisé avec le proxy inverse

Cet article explique comment établir une connexion sécurisée entre le proxy inverse et les services, permettant ainsi un canal sécurisé de bout en bout.

La connexion à des services sécurisés est prise en charge uniquement quand le proxy inverse est configuré pour écouter sur le protocole HTTPS. Le reste du document suppose que c’est le cas.
Reportez-vous à [Proxy inverse dans Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) pour configurer le proxy inverse dans Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Établissement d’une connexion sécurisée entre le proxy inverse et les services 

### <a name="reverse-proxy-authenticating-to-services"></a>Authentification du proxy inverse auprès des services :
Le proxy inverse s’identifie auprès des services à l’aide de son certificat, spécifié avec la propriété ***reverseProxyCertificate*** dans la [section du type de ressource](../azure-resource-manager/resource-group-authoring-templates.md) du **cluster**. Les services peuvent implémenter la logique qui vérifie le certificat présenté par le serveur proxy inverse. Les services peuvent spécifier les détails du certificat client accepté en tant que paramètres de configuration dans le package de configuration. Celui-ci peut être lu au moment de l’exécution et utilisé pour valider le certificat présenté par le proxy inverse. Reportez-vous à [Gestion des paramètres d’application](service-fabric-manage-multiple-environment-app-configuration.md) pour ajouter les paramètres de configuration. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Vérification par le proxy inverse de l’identité du service via le certificat présenté par le service :
Pour effectuer la validation de certificat de serveur des certificats présentés par les services, le proxy inverse prend en charge une des options suivantes : None, ServiceCommonNameAndIssuer et ServiceCertificateThumbprints.
Pour sélectionner une de ces trois options, spécifiez le paramètre **ApplicationCertificateValidationPolicy** dans la section parameters de l’élément ApplicationGateway/Http sous [fabricSettings](service-fabric-cluster-fabric-settings.md).

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "None"
              }
            ]
          }
        ],
        ...
}
```

Pour des informations complètes sur la configuration de chacune de ces options, reportez-vous à la section suivante.

### <a name="service-certificate-validation-options"></a>Options de validation du certificat de service 

- **None** : le proxy inverse ignore la vérification du certificat de service proxy et établit une connexion sécurisée. Il s’agit du comportement par défaut.
Attribuez à **ApplicationCertificateValidationPolicy** la valeur **None** dans la section parameters de l’élément ApplicationGateway/Http.

- **ServiceCommonNameAndIssuer** : le proxy inverse vérifie le certificat présenté par le service en fonction du nom commun du certificat et de l’empreinte de l’émetteur immédiate : attribuez à **ApplicationCertificateValidationPolicy** la valeur **ServiceCommonNameAndIssuer** dans la section parameters de l’élément ApplicationGateway/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCommonNameAndIssuer"
              }
            ]
          }
        ],
        ...
}
```

Pour spécifier la liste des empreintes de l’émetteur et de noms communs de service, ajoutez un élément **ApplicationGateway/Http/ServiceCommonNameAndIssuer** sous fabricSettings, comme indiqué ci-dessous. Plusieurs paires empreinte d’émetteur/nom commun du certificat peuvent être ajoutées à l’élément de tableau parameters. 

Si le proxy inverse du point de terminaison se connecte pour présenter un certificat dont le nom commun et l’empreinte de l’émetteur correspondent à l’une des valeurs spécifiées ici, un canal SSL est établi. Si aucune des valeurs ne correspond aux détails du certificat, le proxy inverse fait échouer la demande du client avec un code d’état 502 (passerelle incorrecte). La ligne d’état HTTP contient également l’expression « Certificat SSL non valide ». 

```json
{
"fabricSettings": [
          ...
          {
             "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
            "parameters": [
              {
                "name": "WinFabric-Test-Certificate-CN1",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
              },
              {
                "name": "WinFabric-Test-Certificate-CN2",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
              }
            ]
          }
        ],
        ...
}
```


- **ServiceCertificateThumbprints** : le proxy inverse vérifie le certificat de service proxy en fonction de son empreinte. Vous pouvez opter pour cette approche si les services sont configurés avec des certificats auto-signés : attribuez à **ApplicationCertificateValidationPolicy** la valeur **ServiceCertificateThumbprints** dans la section parameters de l’élément ApplicationGateway/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCertificateThumbprints"
              }
            ]
          }
        ],
        ...
}
```

Spécifiez également les empreintes avec une entrée **ServiceCertificateThumbprints** dans la section parameters de l’élément ApplicationGateway/Http. Plusieurs empreintes peuvent être spécifiées sous forme de liste séparée par des virgules dans le champ value, comme indiqué ci-dessous :

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "ServiceCertificateThumbprints",
                "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
              }
            ]
          }
        ],
        ...
}
```

Si l’empreinte du certificat de serveur est répertoriée dans cette entrée de configuration, le proxy inverse fait réussir la connexion SSL. Dans le cas contraire, il met fin à la connexion et fait échouer la demande du client avec un code 502 (passerelle incorrecte). La ligne d’état HTTP contient également l’expression « Certificat SSL non valide ».

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Logique de sélection du point de terminaison quand les services exposent des points de terminaison sécurisés et non sécurisés
Service Fabric prend en charge la configuration de plusieurs points de terminaison pour un service. Voir [Spécifier des ressources dans un manifeste de service](service-fabric-service-manifest-resources.md).

Le proxy inverse sélectionne un des points de terminaison pour transférer la demande en fonction du paramètre de requête **ListenerName**. Si ce paramètre n’est pas spécifié, il peut choisir n’importe quel point de terminaison dans la liste de points de terminaison. Maintenant, il peut s’agir d’un point de terminaison HTTP ou HTTPS. Certains scénarios ou exigences peuvent justifier le fonctionnement du proxy inverse en « mode sécurisé uniquement », situations dans lesquelles vous ne souhaitez pas que le proxy inverse sécurisé transfère les demandes vers des points de terminaison non sécurisés. Pour ce faire, vous pouvez spécifier l’entrée de configuration **SecureOnlyMode** avec la valeur **true** dans la section parameters de l’élément ApplicationGateway/Http.   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> 
> En mode **SecureOnlyMode**, si le client a spécifié un **ListenerName** correspondant à un point de terminaison HTTP (non sécurisé), le proxy inverse fait échouer la demande avec un code d’état 404 HTTP (introuvable).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Configuration de l’authentification de certificat client par le biais du proxy inverse
L’arrêt SSL se produit au niveau du proxy inverse et toutes les données de certificat client sont perdues. Pour que les services effectuent l’authentification du certificat client, définissez le paramètre **ForwardClientCertificate** dans la section parameters de l’élément ApplicationGateway/Http.

1. Quand **ForwardClientCertificate** est défini sur **false**, le proxy inverse ne demande pas le certificat client pendant la négociation SSL avec le client.
Il s’agit du comportement par défaut.

2. Quand **ForwardClientCertificate** est défini sur **true**, le proxy inverse demande le certificat du client pendant la négociation SSL avec le client.
Ensuite, il envoie les données du certificat client dans un en-tête HTTP personnalisé nommé **X-Client-Certificate**. La valeur d’en-tête est la chaîne de format PEM encodée en base 64 du certificat du client. Le service peut faire réussir ou échouer la demande avec le code d’état approprié après avoir inspecté les données du certificat.
Si le client ne présente pas un certificat, le proxy inverse transfère un en-tête vide et laisse le service gérer le cas.

> Le proxy inverse est un simple redirecteur. Il n’effectue aucune validation du certificat du client.


## <a name="next-steps"></a>Étapes suivantes
* Reportez-vous à [Configure reverse proxy to connect to secure services](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) (Configurer le proxy inverse pour se connecter à des services sécurisés) pour obtenir des exemples de modèles Azure Resource Manager illustrant la configuration du proxy inverse sécurisé avec les différentes options de validation de certificat de service.
* Pour obtenir un exemple de communication HTTP entre services, consultez cet [exemple de projet sur GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Appels de procédure distante avec Reliable Services à distance](service-fabric-reliable-services-communication-remoting.md)
* [API Web qui utilise OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Gérer les certificats de cluster](service-fabric-cluster-security-update-certs-azure.md)
