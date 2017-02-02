---
title: "Connexion d’ordinateurs et d’appareils à OMS à l’aide de la passerelle OMS | Microsoft Docs"
description: "Connectez vos appareils gérés par OMS et les ordinateurs contrôlés par Operations Manager à la passerelle OMS pour leur permettre d’envoyer des données au service OMS lorsqu’ils n’ont pas accès à Internet."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: a3d79c2df96c21d1af77c8ea1f53a2cc4e28343e
ms.openlocfilehash: 30b1a9144e06db92ba12030dfe37a83e79d62857


---
# <a name="connect-computers-and-devices-to-oms-using-the-oms-gateway"></a>Connecter des ordinateurs et appareils à OMS à l’aide de la passerelle OMS
Ce document décrit comment vos appareils gérés par OMS et les ordinateurs contrôlés par System Center Operations Manager (SCOM) peuvent envoyer des données au service OMS lorsqu’ils n’ont pas accès à Internet. La passerelle OMS peut collecter les données et les envoyer au service OMS en leur nom.

La passerelle est un proxy de transfert HTTP qui prend en charge le tunneling HTTP à l’aide de la commande HTTP CONNECT. La passerelle peut gérer jusqu'à 2 000 appareils OMS connectés simultanément lorsqu’elle est exécutée sur un serveur 16 Go doté d’un processeur 4 cœurs sous Windows.

Par exemple, les serveurs de votre entreprise ou grande organisation peuvent être connectés au réseau, mais ne pas disposer d’une connectivité Internet. Ou alors, vous pouvez avoir plusieurs appareils de points de vente (PDV), mais aucun moyen de les surveiller directement. Enfin, Operations Manager peut par exemple utiliser la passerelle OMS en tant que serveur proxy. Dans ces exemples, la passerelle OMS peut transférer des données entre les agents installés sur ces serveurs ou les appareils de PDV d’une part, et OMS d’autre part.

Au lieu que chaque agent individuel envoie des données directement à OMS, ce qui nécessite une connexion directe à Internet, toutes les données de l’agent sont envoyées via un seul ordinateur connecté à Internet. Cet ordinateur se trouve à l’endroit où vous installez et utilisez la passerelle. Dans ce scénario, vous pouvez installer des agents sur tous les ordinateurs sur lesquels vous souhaitez collecter des données. La passerelle transfère ensuite directement les données des agents à OMS. Elle n’analyse aucune des données transférées.

Vous devez installer l’agent OMS sur l’ordinateur sur lequel la passerelle est également installée. Cela vous permet de surveiller la passerelle OMS et d'analyser les données des performances ou des événements pour le serveur sur lequel il est installé. En outre, l’agent permet à la passerelle OMS d’identifier les points de terminaison de service dont il a besoin pour communiquer.

La passerelle doit avoir accès à Internet pour charger des données sur OMS. Chaque agent doit également disposer d’une connexion réseau avec sa passerelle, afin que les agents puissent automatiquement transférer des données vers la passerelle et recevoir des données de cette dernière. Pour de meilleurs résultats, n'installez pas la passerelle sur un ordinateur qui est également un contrôleur de domaine.

Voici un schéma illustrant le flux de données entre les agents directs et OMS.

![schéma agent direct](./media/log-analytics-oms-gateway/direct-agent-diagram.png)

Voici un schéma illustrant le flux de données entre Operations Manager et OMS.

![schéma Operations Manager](./media/log-analytics-oms-gateway/scom-mgt-server.png)

## <a name="language-availability"></a>Langues disponibles

La passerelle OMS est disponible dans les langues suivantes :

- Chinois (simplifié)
- Chinois (traditionnel)
- Tchèque
- Néerlandais
- Français
- Français
- Allemand
- Hongrois
- Italien
- Japonais
- Coréen
- Polonais
- Portugais (Brésil)
- Portugais (Portugal)
- Russe
- Espagnol (international)

## <a name="download-the-oms-gateway"></a>Télécharger la passerelle OMS

Il existe trois méthodes d'obtenir le fichier de configuration de la passerelle OMS.

### <a name="microsoft-download-center"></a>Centre de téléchargement Microsoft

- Téléchargez la dernière version de la passerelle OMS sur le [Centre de téléchargement de Microsoft](http://download.microsoft.com/download/2/5/C/25CF992A-0347-4765-BD7D-D45D5B27F92C/OMS%20Gateway.msi).

### <a name="oms-portal"></a>Portail OMS

1.  Connectez-vous à votre espace de travail OMS.
2.  Sélectionnez **Paramètres** > **Sources connectées** > **Serveurs Windows**.
3.  Cliquez sur **Télécharger la passerelle OMS**.


### <a name="azure-portal"></a>Portail Azure

1. Accédez au [Portail Azure](https://portal.azure.com) , connectez-vous, parcourez la liste des services, puis sélectionnez **Log Analytics**.
2. Sélectionnez un espace de travail.
3. Dans le panneau de votre espace de travail, sous **Général**, cliquez sur **Démarrage rapide**.
4. Sous **Choisir une source de données pour se connecter à l’espace de travail**, cliquez sur **Ordinateurs**.
4. Dans le panneau **Agent direct**, cliquez sur **Télécharger la passerelle OMS**.  
    ![télécharger la passerelle OMS](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-the-oms-gateway"></a>Installer la passerelle OMS
Lorsque vous installez cette passerelle, elle remplace les versions de passerelles précédentes (redirecteur Log Analytics).

Configuration requise : .Net Framework 4.5, Windows Server 2012 R2 SP1 et versions ultérieures


1. Pour démarrer l’installation, double-cliquez sur **OMS Gateway.msi**.
2. Sur la page d'accueil, cliquez sur **Suivant**.  
    ![Assistant d’installation de la passerelle](./media/log-analytics-oms-gateway/gateway-wizard01.png)
3. Dans la page du contrat de licence, sélectionnez **J’accepte les termes du contrat de licence** pour accepter le CLUF, puis cliquez sur **Suivant**.
4. Sur la page relative au port et à l’adresse proxy :
   1. Saisissez le numéro de port TCP à utiliser pour la passerelle. Le programme d’installation ouvre ce port à partir du pare-feu Windows. La valeur par défaut est 8080.
      Les numéros de port valides sont compris entre 1 et 65535. Si la valeur saisie n’est pas comprise dans cette plage, un message d’erreur s’affiche.
   2. Éventuellement, si le serveur sur lequel la passerelle est installée doit utiliser un proxy, saisissez l’adresse proxy à laquelle la passerelle doit se connecter. Par exemple, `http://myorgname.corp.contoso.com:80` Si vide, la passerelle essaie de se connecter à Internet directement. Dans le cas contraire, la passerelle se connecte au proxy. Si votre serveur proxy requiert une authentification, tapez votre nom d'utilisateur et votre mot de passe.
       ![Configuration du proxy de l’assistant d’installation de la passerelle](./media/log-analytics-oms-gateway/gateway-wizard02.png)  
   3. Cliquez sur **Suivant**
5. Si vous n’avez pas activé les mises à jour Microsoft, la page des mises à jour Microsoft s’affiche, et vous pouvez choisir d’activer les mises à jour. Effectuez une sélection, puis cliquez sur **Suivant**. Sinon, passez à l’étape suivante.
6. Dans la page dossier de Destination, conservez le dossier par défaut **%ProgramFiles%\OMS Gateway** ou entrez l’emplacement où vous voulez installer la passerelle, puis cliquez sur **Suivant**.
7. Dans la page Prêt pour l’installation, cliquez sur **Installer**. Un contrôle de compte utilisateur peut apparaître et demander une autorisation d’installation. Dans ce cas, cliquez sur **Oui**.
8. Une fois l’installation terminée, cliquez sur **Terminer**. Vous pouvez vérifier que le service est en cours d’exécution en ouvrant le composant logiciel enfichable services.msc et en vérifiant que **Passerelle OMS** apparaît dans la liste des services.  
    ![Services – Passerelle OMS](./media/log-analytics-oms-gateway/gateway-service.png)

## <a name="install-an-agent-on-devices"></a>Installer un agent sur des appareils
Si nécessaire, consultez la page [Connecter des ordinateurs Windows à Log Analytics](log-analytics-windows-agents.md) pour obtenir plus d’informations sur l’installation directe d’agents connectés. Cet article décrit comment installer l’agent à l’aide d’un assistant d’installation ou à l’aide de la ligne de commande.

## <a name="configure-oms-agents"></a>Configurer des agents OMS
Consultez [Configurer les paramètres de pare-feu et de proxy avec Microsoft Monitoring Agent](log-analytics-proxy-firewall.md) pour plus d’informations sur la configuration d’un agent pour qu’il utilise un serveur proxy, qui dans ce cas est la passerelle.

Les agents Operations Manager envoient certaines données liées à Operations Manager, telles que des alertes, évaluations de la configuration, espaces d’instance et données de capacité, via le serveur d’administration. D’autres données volumineuses, telles que les journaux IIS, et les données de performances et de sécurité, sont envoyées directement à la passerelle OMS. Consultez [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md) pour une liste complète des données envoyées via chaque canal.

> [!NOTE]
> Si vous envisagez d’utiliser la passerelle avec un équilibrage de la charge réseau, consultez la page [Configuration éventuelle de l’équilibrage de charge réseau](#optionally-configure-network-load-balancing).
>
>

## <a name="configure-a-scom-proxy-server"></a>Configurer un serveur proxy SCOM
Vous configurez Operations Manager pour que la passerelle ajoutée agisse comme un serveur proxy. Lorsque vous mettez à jour la configuration du proxy, elle est automatiquement appliquée à tous les agents Operations Manager.

Pour que la passerelle prenne en charge Operations Manager, vous devez disposer des éléments suivants :

* Microsoft Monitoring Agent (version **8.0.10900.0** et versions ultérieures) installé sur le serveur de passerelle et configuré pour les espaces de travail OMS avec lequel vous voulez communiquer.
* La passerelle doit disposer d’une connectivité Internet, ou être connectée à un serveur proxy connecté à Internet.

### <a name="to-configure-scom-for-the-gateway"></a>Pour configurer SCOM pour la passerelle
1. Ouvrez la console Operations Manager, puis, sous **Operations Management Suite**, cliquez sur **Connexion**, puis sur **Configurer le serveur proxy** :  
    ![Operations Manager – Configurer le serveur proxy](./media/log-analytics-oms-gateway/scom01.png)
2. Sélectionnez **Utiliser un serveur proxy pour accéder à Operations Management Suite** puis saisissez l’adresse IP du serveur de la passerelle OMS. Vérifiez que vous démarrez avec le préfixe `http://` :  
    ![Operations Manager – Adresse du serveur proxy](./media/log-analytics-oms-gateway/scom02.png)
3. Cliquez sur **Terminer**. Votre serveur Operations Manager est connecté à votre espace de travail OMS.

## <a name="configure-network-load-balancing"></a>Configuration de l’équilibrage de la charge réseau
Vous pouvez configurer la passerelle afin qu’elle offre une haute disponibilité à l’aide de l’équilibrage de la charge réseau, en créant un cluster. Le cluster gère le trafic de vos agents en redirigeant les connexions demandées à partir des Microsoft Monitoring Agents vers ses nœuds. Si un serveur de passerelle tombe en panne, le trafic est redirigé vers d’autres nœuds.

1. Ouvrez le Gestionnaire d’équilibrage de charge réseau et créez un cluster.
2. Faites un clic droit sur le cluster avant d’ajouter des passerelles, puis sélectionnez **Propriétés du cluster**. Configurer le cluster afin qu’il dispose de sa propre adresse IP :  
    ![Gestionnaire d’équilibrage de charge réseau – Adresses IP de cluster](./media/log-analytics-oms-gateway/nlb01.png)
3. Pour vous connecter à un serveur de passerelle OMS sur lequel Microsoft Monitoring Agent est installé, faites un clic droit sur l’adresse IP du cluster, puis cliquez sur **Ajouter l’hôte au cluster**.  
    ![Gestionnaire d’équilibrage de charge réseau – Ajouter l’hôte au cluster](./media/log-analytics-oms-gateway/nlb02.png)
4. Entrez l’adresse IP du serveur de passerelle que vous voulez connecter :  
    ![Gestionnaire d’équilibrage de charge réseau – Ajouter l’hôte au cluster : Connexion](./media/log-analytics-oms-gateway/nlb03.png)
5. Sur les ordinateurs qui n’ont pas de connectivité Internet, veillez à utiliser l’adresse IP du cluster lorsque vous configurez les **Propriétés de Microsoft Monitoring Agent** :  
    ![Propriétés de Microsoft Monitoring Agent – Paramètres de proxy](./media/log-analytics-oms-gateway/nlb04.png)

## <a name="configure-for-automation-hybrid-workers"></a>Configuration pour les workers hybrides Automation
Si vous avez des workers hybrides Automation dans votre environnement, les étapes suivantes fournissent des solutions manuelles de contournement temporaires pour configurer la passerelle afin qu’elle les prenne en charge.

Dans les étapes suivantes, vous devez connaître la région Azure dans laquelle réside le compte Automation. Pour localiser l’emplacement :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez le service Azure Automation.
3. Sélectionnez le compte Azure Automation approprié.
4. Regardez sa région sous **Emplacement**.  
    ![Portail Azure – Emplacement du compte Automation](./media/log-analytics-oms-gateway/location.png)

Utilisez les tableaux suivants pour identifier l’URL pour chaque emplacement :

**URL de service de données d’exécution de la tâche**

| **location** | **URL** |
| --- | --- |
| États-Unis - partie centrale septentrionale |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Europe de l'Ouest |we-jobruntimedata-prod-su1.azure-automation.net |
| Centre-Sud des États-Unis |scus-jobruntimedata-prod-su1.azure-automation.net |
| Est des États-Unis 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Canada central |cc-jobruntimedata-prod-su1.azure-automation.net |
| Europe du Nord |ne-jobruntimedata-prod-su1.azure-automation.net |
| Asie du Sud-Est |sea-jobruntimedata-prod-su1.azure-automation.net |
| Inde centrale |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japon |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australie |ase-jobruntimedata-prod-su1.azure-automation.net |

**URL de service de l’agent**

| **location** | **URL** |
| --- | --- |
| États-Unis - partie centrale septentrionale |ncus-agentservice-prod-1.azure-automation.net |
| Europe de l'Ouest |we-agentservice-prod-1.azure-automation.net |
| Centre-Sud des États-Unis |scus-agentservice-prod-1.azure-automation.net |
| Est des États-Unis 2 |eus2-agentservice-prod-1.azure-automation.net |
| Canada central |cc-agentservice-prod-1.azure-automation.net |
| Europe du Nord |ne-agentservice-prod-1.azure-automation.net |
| Asie du Sud-Est |sea-agentservice-prod-1.azure-automation.net |
| Inde centrale |cid-agentservice-prod-1.azure-automation.net |
| Japon |jpe-agentservice-prod-1.azure-automation.net |
| Australie |ase-agentservice-prod-1.azure-automation.net |

Si votre ordinateur est automatiquement inscrit en tant que worker hybride pour l’application de correctifs à l’aide de la solution de gestion des mises à jour, suivez ces étapes :

1. Ajoutez les URL de service de données d’exécution de la tâche à la liste d’hôtes autorisés sur la passerelle OMS. Par exemple :  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Redémarrez le service de passerelle OMS en utilisant l’applet de commande PowerShell suivante : `Restart-Service OMSGatewayService`

Si votre ordinateur est intégré à Azure Automation à l’aide de l’applet de commande d’enregistrement de worker hybride, suivez ces étapes :

1. Ajoutez l’URL d’enregistrement de service de l’agent à la liste d’hôtes autorisés sur la passerelle OMS. Par exemple : `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Ajoutez les URL de service de données d’exécution de la tâche à la liste d’hôtes autorisés sur la passerelle OMS. Par exemple :  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Redémarrez le service de passerelle OMS.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Applets de commande PowerShell utiles
Les applets de commande peuvent vous aider à effectuer des tâches qui sont nécessaires pour mettre à jour les paramètres de configuration de la passerelle OMS. Avant de les utiliser, veillez à suivre les instructions suivantes :

1. Installez la passerelle OMS (MSI).
2. Ouvrez la fenêtre PowerShell.
3. Pour importer le module, saisissez cette commande : `Import-Module OMSGateway`
4. Si aucune erreur ne s’est produite lors des étapes précédentes, cela signifie que le module a été importé, et que les applets de commande peuvent être utilisées. Saisissez `Get-Module OMSGateway`
5. Une fois que vous avez apporté des modifications à l’aide des applets de commande, assurez-vous de redémarrer le service de passerelle.

Si vous obtenez une erreur à l’étape 3, cela signifie que le module n’a pas été importé. L’erreur peut se produire lorsque PowerShell ne trouve pas le module. Vous pouvez le trouver dans le dossier d’installation de la passerelle : C:\Program File\Microsoft OMS Gateway\PowerShell.

| **Applet de commande** | **Paramètres** | **Description** | **Exemples** |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |Clé (obligatoire) <br> Valeur |Modifie la configuration du service |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |Clé |Récupère la configuration du service |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |Adresse <br> Nom d’utilisateur <br> Mot de passe |Définit l’adresse (et les informations d’identification) du proxy de relais (en amont) |1. Définir un proxy de réponse et les informations d’identification : `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. Définir un proxy de réponse n’ayant pas besoin d’authentification : `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. Désactivez le paramètre de proxy de réponse, pour indiquer qu’il n’est pas nécessaire : `Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |Récupère l’adresse du proxy de relais (en amont) |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |Hôte (obligatoire) |Ajoute l’hôte à la liste d’éléments autorisés |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |Hôte (obligatoire) |Supprime l’hôte de la liste d’éléments autorisés |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |Récupère l’hôte actuellement autorisé (uniquement l’hôte configuré localement, pas les hôtes autorisés téléchargés automatiquement) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |Objet (obligatoire) |Ajoute l’objet du certificat client à la liste d’éléments autorisés |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |Objet (obligatoire) |Supprime l’objet du certificat client de la liste d’éléments autorisés |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |Récupère les objets de certificat client actuellement autorisés (uniquement les objets configurés localement, pas les objets autorisés téléchargés automatiquement) |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshoot"></a>Résolution des problèmes
Vous devez installer l’agent OMS sur les ordinateurs sur lesquels la passerelle est installée. Vous pouvez ensuite utiliser l’agent pour collecter les événements consignés par la passerelle.

![Observateur d’événements – Journal de la passerelle OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**Identifiants et descriptions des événements de la passerelle OMS**

Le tableau suivant montre les identifiants et descriptions d’événements pour les événements du journal de la passerelle OMS.

| **Identifiant** | **Description** |
| --- | --- |
| 400 |Toute erreur d’application ne disposant pas d’un identifiant spécifique |
| 401 |Configuration incorrecte. Par exemple : listenPort = "texte" au lieu d’un nombre entier |
| 402 |Exception lors de l’analyse des messages de liaison TLS |
| 403 |Erreur de mise en réseau. Par exemple : impossible de se connecter au serveur cible |
| 100 |Informations générales |
| 101 |Le service a démarré |
| 102 |Le service s’est arrêté |
| 103 |Réception d’une commande HTTP CONNECT de la part du client |
| 104 |L’élément n’est pas une commande HTTP CONNECT |
| 105 |Le serveur de destination n’est pas dans la liste autorisée ou le port de destination n’est pas un port sécurisé (443) <br> <br> Vérifiez que l’agent MMA sur votre serveur de passerelle et les agents communiquant avec la passerelle sont connectés au même espace de travail Log Analytics. |
| 105 |ERREUR TcpConnection – Certificat client non valide : CN=passerelle <br><br> Assurez-vous que : <br>    <br> &#149; Vous utilisez une passerelle ayant le numéro de version 1.0.395.0 ou supérieur. <br> &#149; L’agent MMA sur votre serveur de passerelle et les agents communiquant avec la passerelle sont connectés au même espace de travail Log Analytics. |
| 106 |Toute raison pour laquelle la session TLS est suspecte et rejetée |
| 107 |La session TLS a été vérifiée |

**Compteurs de performances à collecter**

Le tableau suivant montre les compteurs de performances disponibles pour la passerelle OMS. Vous pouvez ajouter des compteurs à l’aide de l’Analyseur de performances.

| **Nom** | **Description** |
| --- | --- |
| Passerelle OMS/Connexion du client actif |Nombre de connexions du réseau client actif (TCP) |
| Passerelle OMS/Nombre d’erreurs |Nombre d’erreurs |
| Passerelle OMS/Client connecté |Nombre de clients connectés |
| Passerelle OMS/Nombre de rejets |Nombre de rejets dus à des erreurs de validation de TLS |

![Compteurs de performances de la passerelle OMS](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Obtenir de l’aide
Lorsque vous êtes connecté au portail Azure, vous pouvez créer une demande d’assistance pour la passerelle OMS ou tout autre service Azure ou toute fonctionnalité d’un service.
Pour demander de l’aide, cliquez sur le symbole en forme de point d’interrogation dans le coin supérieur droit du portail, puis cliquez sur **Nouvelle demande de support**. Ensuite, remplissez le nouveau formulaire de demande de support.

![Nouvelle demande de support](./media/log-analytics-oms-gateway/support.png)

Vous pouvez également laisser des commentaires sur OMS ou Log Analytics sur le [forum de commentaires de Microsoft Azure](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Étapes suivantes
* [Ajoutez des sources de données](log-analytics-data-sources.md) pour collecter des données à partir des sources connectées de votre espace de travail OMS et les stocke dans le référentiel OMS.



<!--HONumber=Dec16_HO2-->


