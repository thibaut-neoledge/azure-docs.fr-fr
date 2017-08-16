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
ms.date: 08/04/2017
ms.author: kgremban
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: bdca442755507c4ffe8d43692c5b7f2aa3a746f3
ms.contentlocale: fr-fr
ms.lasthandoff: 08/07/2017

---

# <a name="work-with-existing-on-premises-proxy-servers"></a>Travailler avec des serveurs proxy locaux existants

Cet article explique comment configurer les connecteurs de proxy d’application Azure Active Directory (Azure AD) pour fonctionner avec des serveurs proxy sortants. Il est destiné aux clients avec des environnements réseau qui ont des proxys existants.

Nous allons commencer par examiner les scénarios de déploiement principaux suivants :
* Configurer des connecteurs pour contourner vos proxys sortants locaux.
* Configurer des connecteurs pour utiliser un proxy sortant afin d’accéder au proxy d’application Azure AD.

Pour plus d’informations sur le fonctionnement des connecteurs, consultez [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md).

## <a name="configure-the-outbound-proxy"></a>Configuration du serveur proxy sortant

Si votre environnement comporte un proxy sortant, utilisez un compte avec les autorisations appropriées pour le configurer. Étant donné que le programme d’installation s’exécute dans le contexte de l’utilisateur qui effectue l’installation, vous pouvez vérifier la configuration à l’aide de Microsoft Edge ou d’un autre navigateur Internet.

Pour configurer les paramètres de proxy dans Microsoft Edge :

1. Accédez à **Paramètres** > **Afficher les paramètres avancés** > **Ouvrir les paramètres de proxy** > **Configuration manuelle du proxy**.
2. Définissez **Utiliser un serveur proxy** sur **Activé**, cochez la case **Ne pas utiliser le serveur proxy pour les adresses intranet locale**, puis modifiez l’adresse et le port pour refléter votre serveur proxy local.
3. Renseignez les paramètres de proxy nécessaires.

   ![Boîte de dialogue des paramètres de proxy](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)

## <a name="bypass-outbound-proxies"></a>Proxys sortants de contournement

Les connecteurs ont des composants de système d’exploitation sous-jacents qui effectuent des demandes sortantes. Ces composants tentent automatiquement de localiser un serveur de proxy sur le réseau. Ils utilisent Web Proxy Auto-Discovery (WPAD), si WPAD est activé dans l’environnement.

Les composants du système d’exploitation tentent de localiser un serveur proxy en effectuant une recherche DNS pour wpad.domainsuffix. Si un DNS est renvoyé, une requête HTTP est alors effectuée sur l’adresse IP pour wpad.dat. Cette requête devient le script de configuration de proxy dans votre environnement. Le connecteur utilise ce script pour sélectionner un serveur proxy sortant. Toutefois, le trafic du connecteur peut ne pas toujours passer, en raison des paramètres de configuration supplémentaires nécessaires sur le serveur proxy.

Vous pouvez configurer le connecteur de sorte qu’il contourne votre proxy local pour vous assurer qu’il utilise une connexion directe aux services Azure. C’est ce que nous recommandons (si votre stratégie réseau le permet), car cela signifie que vous aurez une configuration de moins à mettre à jour.

Pour désactiver l’utilisation d’un proxy sortant pour le connecteur, modifiez le fichier C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config et ajoutez la section *system.net* indiquée dans cet exemple de code :

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
Pour être sûr que le connecteur du service de mise à jour contourne également le proxy, apportez une modification similaire au fichier ApplicationProxyConnectorUpdaterService.exe.config situé dans C:\Program Files\Microsoft AAD application Proxy Connector Updater.

Veillez à faire des copies des fichiers d’origine, au cas où vous devriez restaurer les fichiers par défaut.

## <a name="use-the-outbound-proxy-server"></a>Utiliser le serveur proxy sortant

Certains environnements clients requièrent que tout le trafic sortant passe par un proxy sortant, sans exception. Par conséquent, le contournement du serveur proxy n’est pas une option.

Vous pouvez configurer le trafic du connecteur pour passer par le serveur proxy sortant comme indiqué dans le diagramme suivant :

 ![Configuration du trafic de connecteur pour passer par un proxy sortant vers un proxy d’application Azure AD](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

Conséquence de la présence seule de trafic sortant, il est inutile de configurer l’accès entrant à travers vos pare-feu.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Étape 1 : Configurer le connecteur et les services liés pour passer par le proxy sortant

Comme indiqué précédemment, si WPAD est activé dans l’environnement et correctement configuré, le connecteur détecte automatiquement le serveur proxy sortant et tente de l’utiliser. Toutefois, vous pouvez configurer explicitement le connecteur pour passer par un proxy sortant.

Pour ce faire, modifiez le fichier C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config et ajoutez la section *system.net* indiquée dans cet exemple de code. Modifiez *proxyserver:8080* afin de refléter le nom de votre serveur proxy local ou l’adresse IP et le port qu’il écoute.

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

Configurez ensuite le service de mise à jour du connecteur pour utiliser le proxy en apportant une modification similaire au fichier situé dans C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Étape 2 : Configurer le serveur proxy pour autoriser le passage du trafic à partir du connecteur et des services connexes

Il y a quatre aspects à prendre en compte au niveau du proxy sortant :
* Règles sortantes du proxy
* Authentification du proxy
* Ports du proxy
* Inspection SSL

#### <a name="proxy-outbound-rules"></a>Règles sortantes du proxy
Autorisez l’accès aux points de terminaison suivants pour l’accès au service de connecteur :

* *.msappproxy.net
* *.servicebus.windows.net

Pour l’inscription initiale, autorisez l’accès aux points de terminaison suivants :

* login.windows.net
* login.microsoftonline.com

Si vous ne pouvez pas autoriser la connectivité par le nom de domaine complet et devez spécifier des plages d’adresses IP à la place, utilisez ces options :

* Autoriser l’accès sortant du connecteur vers toutes les destinations.
* Autoriser l’accès sortant du connecteur vers des [plages d’adresses IP de centre de données Azure](https://www.microsoft.com/en-gb/download/details.aspx?id=41653). Le problème lié à l’utilisation de la liste de plages d’adresses IP de centre de données Azure est qu’elle est mise à jour chaque semaine. Vous devez mettre un processus en place pour garantir que vos règles d’accès sont mises à jour en conséquence.

#### <a name="proxy-authentication"></a>Authentification du proxy

L’authentification proxy n'est actuellement pas prise en charge. Notre recommandation actuelle est de permettre au connecteur d’accéder de façon anonyme aux destinations Internet.

#### <a name="proxy-ports"></a>Ports du proxy

Le connecteur établit les connexions sortantes SSL à l’aide de la méthode CONNECT. Cette méthode sert à définir un tunnel via le serveur proxy sortant. Configurez le serveur proxy pour autoriser le tunneling vers les ports 443 et 80.

>[!NOTE]
>Lorsque Service Bus s’exécute via le protocole HTTPS, il utilise le port 443. Toutefois, par défaut, Service Bus tente des connexions TCP directes et bascule sur HTTPS uniquement si la connectivité directe échoue.

Pour vous assurer que le trafic Service Bus est également envoyé via le serveur proxy sortant, assurez-vous que le connecteur ne peut pas se connecter directement aux services Azure pour les ports 9350, 9352 et 5671.

#### <a name="ssl-inspection"></a>Inspection SSL
N’utilisez pas l’inspection SSL pour le trafic de connecteur, car cela entraîne des problèmes pour le trafic du connecteur.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Résoudre les problèmes courants de proxy de connecteur et de connectivité du service
Vous devriez maintenant voir tout le trafic transitant par le proxy. Si vous rencontrez des problèmes, les informations de résolution des problèmes suivantes devraient vous aider.

Le meilleur moyen d’identifier et de résoudre les problèmes de connectivité de connecteur consiste à prendre une capture réseau sur le service de connecteur au démarrage de ce dernier. Cela peut être une tâche ardue, aussi examinons quelques conseils rapides sur la capture et le filtrage des traces réseau.

Vous pouvez utiliser l’outil de surveillance de votre choix. Dans le cadre de cet article, nous avons utilisé Microsoft Network Monitor 3.4. Vous pouvez [le télécharger à partir de Microsoft](https://www.microsoft.com/download/details.aspx?id=4865).

Les exemples et les filtres que nous utilisons dans les sections suivantes sont spécifiques à Network Monitor, mais les principes peuvent être appliqués à n’importe quel outil d’analyse.

### <a name="take-a-capture-by-using-network-monitor"></a>Prendre une capture à l’aide de Network Monitor

Pour démarrer une capture :

1. Ouvrez Network Monitor, puis cliquez sur **Nouvelle capture**.
2. Cliquez sur le bouton **Démarrer**.

   ![Fenêtre de Network Monitor](./media/application-proxy-working-with-proxy-servers/network-capture.png)

Lorsque vous avez terminé une capture, cliquez sur le bouton **Arrêter** pour l’arrêter.

### <a name="take-a-capture-of-connector-traffic"></a>Effectuez une capture du trafic de connecteur

Pour commencer la résolution des problèmes, procédez comme suit :

1. À partir de services.msc, arrêtez le service de connecteur de proxy d’application Azure AD.
2. Démarrez la capture réseau.
3. Démarrez le service de connecteur de proxy d’application Azure AD.
4. Arrêtez la capture réseau.

   ![Service de connecteur de proxy d’application Azure AD dans services.msc](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-requests-from-the-connector-to-the-proxy-server"></a>Examiner les requêtes du connecteur au serveur proxy

Maintenant que vous avez une capture réseau, vous pouvez la filtrer. La clé de l’analyse de la trace est de comprendre comment filtrer la capture.

Voici un filtre (où 8080 est le port du service proxy) :

**(http.Request or http.Response) and tcp.port==8080**

Si vous entrez ce filtre dans la fenêtre de **filtre d’affichage** et sélectionnez **Appliquer**, le trafic capturé est filtré en fonction du filtre.

Le filtre précédent affiche uniquement les requêtes et réponses HTTP vers/depuis le port du proxy. Pour un démarrage de connecteur sur lequel le connecteur est configuré pour utiliser un serveur proxy, ce filtre ressemblerait à ceci :

 ![Exemple de liste de requêtes et réponses HTTP filtrées](./media/application-proxy-working-with-proxy-servers/http-requests.png)

Vous recherchez maintenant spécifiquement les requêtes CONNECT indiquant une communication avec le serveur proxy. En cas de succès, vous obtenez une réponse HTTP OK (200).

Si vous voyez d’autres codes de réponse, comme 407 ou 502, le proxy nécessite une authentification ou n’autorise pas le trafic pour une raison quelconque. À ce stade, vous impliquez l’équipe de support technique de votre serveur proxy.

### <a name="identify-failed-tcp-connection-attempts"></a>Identifier les tentatives de connexion TCP ayant échoué

L’autre scénario courant qui peut vous intéresser est lorsque le connecteur essaie de se connecter directement, mais qu’il échoue.

Voici un autre filtre Network Monitor qui vous permet d’identifier facilement ce problème :

**property.TCPSynRetransmit**

Un paquet SYN est le premier paquet envoyé pour établir une connexion TCP. Si ce paquet ne renvoie aucune réponse, le SYN est renvoyé. Vous pouvez utiliser le filtre précédent pour voir les SYN retransmis. Ensuite, vous pouvez vérifiez si ces SYN correspondent à du trafic lié à un connecteur.

L’exemple suivant montre une tentative de connexion ayant échoué pour le port de Service Bus 9352 :

 ![Exemple de réponse à une tentative de connexion ayant échoué](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

Si vous voyez quelque chose comme la réponse précédente, le connecteur tente de communiquer directement avec le service Service Bus d’Azure. Si vous vous attendez à ce que le connecteur établisse des connexions directes avec les services Azure, cette réponse est une indication claire que vous avez un problème de réseau ou de pare-feu.

>[!NOTE]
>Si vous avez effectué la configuration de sorte à utiliser un serveur proxy, cette réponse peut indiquer que Service Bus tente une connexion TCP directe avant de tenter de se connecter via HTTPS.
>

L’analyse des traces réseau n’est pas faite pour tout le monde. Mais il peut s’agir d’un outil précieux pour obtenir rapidement des informations sur ce qui se passe avec votre réseau.

Si vous continuez à rencontrer des problèmes de connectivité avec le connecteur, créez un ticket auprès de notre équipe de support technique. L’équipe peut vous aider dans la suite de la résolution des problèmes.

Pour plus d’informations sur la résolution des erreurs avec le connecteur de proxy d’application, consultez [Résoudre les problèmes du proxy d’application](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-troubleshoot).

## <a name="next-steps"></a>Étapes suivantes

[Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md)<br>
[Comment installer silencieusement le connecteur du Proxy d'application Azure AD](active-directory-application-proxy-silent-installation.md)

