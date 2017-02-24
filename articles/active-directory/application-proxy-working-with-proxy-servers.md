---
title: Travailler avec des serveurs proxy locaux existants et Azure AD | Microsoft Docs
description: Explique comment travailler avec des serveurs proxy locaux existants.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2fab64ef8bf07532c4fd2fff4f4af68dc0b44b45
ms.openlocfilehash: 659edc19c9f043d6a388f1bb293c72e379fa6e4a


---

# <a name="work-with-existing-on-premises-proxy-servers"></a>Travailler avec des serveurs proxy locaux existants

Cet article explique comment configurer le connecteur de proxy d’application pour fonctionner avec des serveurs proxy sortants. Il est destiné aux clients avec des environnements réseau qui ont des proxys existants.

Nous allons commencer par examiner les scénarios de déploiement principaux suivants :
* Configuration de connecteurs pour contourner vos proxys sortants locaux.
* Configuration des connecteurs pour utiliser un proxy sortant afin d’accéder au proxy d’application Azure AD.

Pour en savoir plus sur le fonctionnement des connecteurs, voir [Offrir un accès à distance sécurisé aux applications locales](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="configure-your-connectors"></a>Configurer vos connecteurs

Le service du connecteur principal utilise Azure Service Bus pour gérer certaines communications sous-jacentes pour le service de proxy d’application Azure AD. Service Bus apporte des exigences de configuration supplémentaires.

Pour plus d’informations sur la résolution des problèmes de connectivité d’Azure AD, consultez [Guide pratique de résolution des problèmes de connectivité au proxy d’application Azure AD](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems). 

## <a name="configure-the-outbound-proxy"></a>Configuration du serveur proxy sortant

Si vous avez un proxy sortant dans votre environnement, assurez-vous que le compte qui effectue l’installation est correctement configuré pour utiliser le proxy sortant. Comme le programme d’installation s’exécute dans le contexte de l’utilisateur qui effectue l’installation, cela peut être effectué à l’aide de Microsoft Edge ou d’un autre navigateur Internet. 

Pour configurer les paramètres de proxy à l’aide de Microsoft Edge, accédez à Paramètres, affichez les paramètres avancés, ouvrez les paramètres de proxy, puis procédez à l’installation manuelle du proxy. Définissez « Utiliser un serveur proxy » sur Activez, cochez « Ne pas utiliser le serveur proxy pour les adresses intranet locales », puis modifiez l’adresse et le port pour refléter votre serveur proxy local.

Définissez les paramètres de proxy nécessaires, comme indiqué dans la zone d’options ci-dessous.

 ![Adresses locales AzureAD Bypass](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)
 
## <a name="bypass-outbound-proxies"></a>Proxys sortants de contournement

Par défaut, les composants du système d’exploitation sous-jacent utilisés par le connecteur pour effectuer des requêtes sortantes tentent automatiquement de localiser un serveur proxy sur le réseau à l’aide de Web Proxy Auto-Discovery (WPAD), si WPAD est activé dans l’environnement.

Cela fonctionne généralement en effectuant une recherche DNS pour wpad.domainsuffix. Si cela se résout dans le DNS, une requête HTTP sera ensuite envoyée à l’adresse IP pour wpad.dat, qui sera le script de configuration de proxy dans votre environnement. Le connecteur utilise cela pour sélectionner un serveur proxy sortant. Toutefois, le trafic du connecteur peut ne toujours pas passer, en raison des paramètres de configuration supplémentaires nécessaires sur le serveur proxy. 

Dans la section suivante, nous abordons les étapes de configuration nécessaires sur le serveur proxy sortant pour que le trafic puisse passer. Mais tout d’abord, nous allons voir comment configurer le connecteur pour contourner votre proxy local pour vous assurer qu’il utilise une connexion directe aux services Azure. Il s’agit de la méthode recommandée (à condition que votre stratégie réseau le permette), car cela signifie que vous disposerez d’une configuration de moins à mettre à jour.

Pour désactiver l’utilisation d’un proxy sortant pour le connecteur, modifiez le fichier C:\Program Files\Microsoft AAD application Proxy Connector\ApplicationProxyConnectorService.exe.config et ajoutez la section [system.net] indiquée dans l’exemple de code ci-dessous :

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>
<defaultProxy enabled="false"></defaultProxy>
</system.net>
 <runtime>
<gcServer enabled="true"/>
  </runtime>
  <appSettings>
<add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```
Pour vous assurer que le connecteur du service de mise à jour contourne également le proxy, apportez une modification similaire au fichier ApplicationProxyConnectorUpdaterService.exe.config situé dans C:\Program Files\Microsoft AAD application Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

N’oubliez pas de faire des copies des fichiers d’origine, si jamais vous deviez restaurer les fichiers par défaut. 

## <a name="use-the-outbound-proxy-server"></a>Utiliser le serveur proxy sortant

Comme mentionné ci-dessus, dans certains environnements clients, il existe une condition requise qui stipule que tout le trafic sortant doit passer par un proxy sortant, sans exception. Par conséquent, le contournement du serveur proxy n’est pas une option.

Vous pouvez configurer le trafic du connecteur pour passer par le serveur proxy sortant comme indiqué ci-dessous.

 ![Adresses locales AzureAD Bypass](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

Conséquence de la présence seule de trafic sortant, il est inutile de configurer d’équilibrage de charge entre les connecteurs ou de configurer l’accès entrant à travers vos pare-feu.

Dans tous les cas, vous devrez exécuter la procédure suivante :
1. Configurez le connecteur et le service de mise à jour de sorte à passer par le proxy sortant.
2. Configurez les paramètres du proxy pour autoriser les connexions au service de proxy d’application Azure AD.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Étape 1 : Configurer le connecteur et les services liés pour passer par le proxy sortant

Comme indiqué ci-dessus, si WPAD est activé dans l’environnement et correctement configuré, le connecteur détecte automatiquement le serveur proxy sortant et tente de l’utiliser. Toutefois, vous pouvez configurer explicitement le connecteur pour passer par un proxy sortant. 

Pour ce faire, modifiez le fichier C:\Program Files\Microsoft AAD application Proxy Connector\ApplicationProxyConnectorService.exe.config et ajoutez la section [system.net] indiquée dans l’exemple de code ci-dessous :

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
</system.net>
  <runtime>
     <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

>[!NOTE]
>Modifiez _proxyserver:8080_ afin de refléter le nom de votre serveur proxy local ou l’adresse IP et le port qu’il écoute.
>

Vous devez ensuite configurer le service de mise à jour du connecteur pour utiliser le proxy en apportant une modification similaire au fichier situé dans C:\Program Files\Microsoft AAD application Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

###<a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Étape 2 : Configurer le serveur proxy pour autoriser le passage du trafic à partir du connecteur et des services connexes

Il y a 4 aspects à prendre en compte au niveau du proxy sortant :
* Règles sortantes du proxy
* Authentification du proxy
* Ports du proxy
* Inspection SSL

#### <a name="2a-proxy-outbound-rules"></a>2.A : Règles sortantes du proxy
Autorisez l’accès aux points de terminaison suivants pour l’accès au service de connecteur :

* *.msappproxy.net 
* *.servicebus.microsoft.net 

Pour l’inscription initiale, autorisez l’accès aux points de terminaison suivants :

* login.windows.net 
* login.microsoftonline.com. 

Les canaux de contrôle Service Bus sous-jacents utilisés par le service du connecteur nécessitent en outre une connectivité vers des adresses IP spécifiques. Notez que nous travaillons en collaboration avec l’équipe Service Bus pour passer à un nom de domaine complet à la place. Pour l’heure, cependant, il existe deux options pour : 
 
* Autoriser l’accès sortant du connecteur vers toutes les destinations, ou
* Autoriser l’accès sortant aux plages d’adresses IP de centres de données Azure répertoriées sur https://www.microsoft.com/en-gb/download/details.aspx?id=41653 pour le connecteur

>[!NOTE]
>Le problème avec la liste de plages d’adresses IP de centres de données Azure est qu’elle est mise à jour chaque semaine, vous devez donc mettre un processus en place pour vous assurer que les règles d’accès sont mises à jour en conséquence.
>

#### <a name="2b-proxy-authentication"></a>2.B : Authentification du proxy

L’authentification proxy n'est actuellement pas prise en charge. Notre recommandation actuelle est de permettre au connecteur d’accéder de façon anonyme aux destinations Internet.

#### <a name="2c-proxy-ports"></a>2.C : Ports du proxy

Le connecteur établit les connexions sortantes SSL à l’aide de la méthode CONNECT. Cela sert à définir un tunnel via le serveur proxy sortant. Certains serveurs proxy autorisent, par défaut, uniquement le tunneling sortant sur les ports SSL standard, 443 par exemple. Si c’est le cas, le serveur proxy devra être configuré pour autoriser le tunneling vers des ports supplémentaires.

Configurez le serveur proxy pour autoriser le tunneling vers les ports SSL non standard 8080, 9090, 9091 et 10100-10120.

>[!NOTE]
>Lorsque Service Bus s’exécute via le protocole HTTPS, il utilise le port 443. Toutefois, par défaut, Service Bus tente uniquement des connexions TCP directes et bascule sur HTTPS uniquement si la connexion directe échoue.> 
> 

Pour vous assurer que le trafic Service Bus est également envoyé via le serveur proxy sortant, vous devez vous assurer que le connecteur ne peut pas se connecter directement aux services Azure pour les ports 9350, 9352 et 5671.

#### <a name="2d-ssl-inspection"></a>2.D : Inspection de site
N’utilisez pas l’inspection SSL pour le trafic de connecteur, car cela entraînera des problèmes pour le trafic du connecteur. 

Et voilà ! Vous devriez maintenant voir tout le trafic transitant par le proxy. Si vous rencontrez des problèmes, les étapes de dépannage suivantes devraient vous aider.

### <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Résoudre les problèmes courants de proxy de connecteur et de connectivité du service

Le meilleur moyen d’identifier et de résoudre les problèmes de connectivité de connecteur consiste à prendre une capture réseau sur le service de connecteur au démarrage de ce dernier. Cela peut être une tâche ardue, aussi examinons quelques conseils rapides sur la capture et le filtrage des traces réseau.

Vous pouvez utiliser l’outil de surveillance de votre choix. Dans le cadre de cet article, nous avons utilisé Microsoft Network Monitor 3.4, que vous pouvez télécharger sur https://www.microsoft.com/en-us/download/details.aspx?id=4865.

Les exemples et les filtres que nous utilisons ci-dessous sont spécifiques à Network Monitor, mais les principes peuvent être appliqués à n’importe quel outil d’analyse.

### <a name="take-a-capture-using-microsoft-network-monitor"></a>Effectuer une capture avec Microsoft Network Monitor

Pour démarrer une capture, ouvrez Network Monitor, puis cliquez sur Nouvelle capture. Appuyez sur le bouton Démarrer pour commencer.

 ![AzureAD Network Monitor](./media/application-proxy-working-with-proxy-servers/network-capture.png)

Après avoir terminé une capture, cliquez sur le bouton Arrêter pour arrêter la capture.

### <a name="take-a-capture-of-connector-traffic"></a>Effectuez une capture du trafic de connecteur

Pour commencer la résolution des problèmes, procédez comme suit : 

1. À partir de services.msc, arrêtez le service de connecteur de proxy d’application Microsoft AAD (comme illustré ci-dessous).
2. Démarrez la capture réseau.
3. Démarrez le service de connecteur de proxy d’application Microsoft AAD.
4. Arrêtez la capture réseau.

 ![AzureAD Network Monitor](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-connector-to-proxy-server-requests"></a>Examinez les requêtes du serveur proxy au connecteur

Maintenant que vous avez une capture réseau, vous pouvez la filtrer. La clé de l’analyse de la trace est de comprendre comment filtrer la capture.

Voici un filtre pour cela (où 8080 est le port du service proxy) : 

(http.Request or http.Response) and tcp.port==8080

Si vous entrez ce filtre dans la fenêtre de filtre d’affichage et sélectionnez Appliquer, le trafic capturé sera filtré en fonction du filtre.

Le filtre ci-dessus affiche uniquement les requêtes et réponses HTTP vers/depuis le port du proxy. Pour un démarrage de connecteur sur lequel le connecteur est configuré pour utiliser un serveur proxy, cela ressemblerait à ceci :

 ![AzureAD Network Monitor](./media/application-proxy-working-with-proxy-servers/http-requests.png)

Vous cherchez maintenant spécifiquement les requêtes CONNECT indiquant qu’il s’agit d’une communication avec le serveur proxy. En cas de succès, vous obtiendrez une réponse HTTP OK (200).

Si vous voyez d’autres codes de réponse, comme 407 ou 502, cela indique que le proxy nécessite une authentification ou n’autorise pas le trafic pour une raison quelconque. À ce stade, vous devez impliquer l’équipe de support technique de votre serveur proxy.

### <a name="identify-failed-tcp-connection-attempts"></a>Identifier les tentatives de connexion TCP ayant échoué

L’autre scénario courant qui peut vous intéresser est lorsque le connecteur essaie de se connecter directement, mais qu’il échoue. 

Voici un autre filtre Network Monitor qui vous permet d’identifier facilement cela :

property.TCPSynRetransmit

Un paquet SYN est le premier paquet envoyé pour établir une connexion TCP. Si aucune réponse n’est reçue, le SYN est renvoyé. Le filtre ci-dessus vous permet de vous permet de voir les requêtes SYN de nouveau transmises. Ensuite, vous pouvez examiner les résultats pour déterminer s’ils correspondent à du trafic lié à un connecteur. 

L’exemple suivant montre une tentative de connexion ayant échoué pour le port de Service Bus 9352 :

 ![AzureAD Network Monitor](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

Si vous voyez quelque chose comme la réponse ci-dessus, cela signifie que le connecteur tente de communiquer directement avec le service Service Bus d’Azure. Si vous vous attendez à ce que le connecteur établisse des connexions directes avec les services Azure, cela est une indication claire que vous avez un problème de réseau/pare-feu.

>[!NOTE]
>Si vous avez effectué la configuration de sorte à utiliser un serveur proxy, il est possible que Service Bus tente une connexion TCP directe avant de tenter de se connecter via HTTPS, gardez donc cela à l’esprit.
>

L’analyse des traces réseau n’est pas faite pour tout le monde. Mais il peut s’agit d’un outil extrêmement précieux pour obtenir rapidement des informations sur ce qui se passe votre réseau. 

Si vous continuez à rencontrer des problèmes de connectivité avec le connecteur, créez un ticket auprès de notre équipe de support technique, qui sera heureuse de vous aider à la résolution des problèmes.

Pour plus d’informations sur la résolution des erreurs du connecteur de proxy d’application, consultez [Résoudre les problèmes du proxy d’application](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot).

## <a name="next-steps"></a>Étapes suivantes

[Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md)<br>
[Comment installer silencieusement le connecteur du Proxy d'application Azure AD](active-directory-application-proxy-silent-installation.md)







<!--HONumber=Feb17_HO1-->


