---
title: "Connecter des ordinateurs hors connexion à Operations Management Suite à l’aide de la passerelle OMS | Microsoft Docs"
description: "Connectez des ordinateurs hors connexion qui sont gérés par Operations Management Suite et surveillés par System Center Operations Manager à l’aide de la passerelle OMS pour envoyer des données au service Operations Management Suite."
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
ms.date: 03/27/2017
ms.author: magoedte; banders
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19bf9b3d65210458e4f018dc591e35e59287cd8e
ms.lasthandoff: 04/03/2017

---

# <a name="connect-offline-computers-to-operations-management-suite-by-using-oms-gateway"></a>Connecter des ordinateurs hors connexion à Operations Management Suite à l’aide de la passerelle OMS

Les ordinateurs gérés par Operations Management Suite (OMS) et surveillés par System Center Operations Manager (Operations Manager) peuvent envoyer des données au service Operations Management Suite lorsqu’ils n’ont pas accès à Internet. La passerelle OMS est un proxy de transfert HTTP qui prend en charge le tunneling HTTP à l’aide de la commande HTTP CONNECT. La passerelle OMS peut collecter des données et les envoyer au service Operations Management Suite pour le compte d’ordinateurs hors connexion.  

Vous pouvez utiliser la passerelle OMS avec :

* Runbook Worker hybride Azure Automation.  
* Ordinateurs Windows disposant de Microsoft Monitoring Agent et directement connectés à un espace de travail Operations Management Suite.
* System Center Operations Manager 2012 SP1 avec correctif cumulatif (UR) 7, System Center Operations Manager 2012 R2 UR3, ou groupe d’administration System Center Operations Manager 2016 intégré dans Operations Management Suite.  

Certaines stratégies de sécurité informatique n’autorisent pas les ordinateurs en réseau à se connecter à Internet. Cela peut être le cas pour les appareils de point de vente (PDV) ou les serveurs prenant en charge les services informatiques. Or, vous devez connecter les ordinateurs à Operations Management Suite pour les gérer et les contrôler. Vous pouvez configurer des ordinateurs hors connexion afin qu’ils communiquent directement avec la passerelle OMS. La passerelle reçoit la configuration et transfère les données pour le compte des ordinateurs hors connexion.  

Si vous configurez des ordinateurs disposant de l’agent Operations Management Suite pour qu’ils se connectent directement à un espace de travail Operations Management Suite, tous les ordinateurs communiquent par le biais de la passerelle OMS. La passerelle transfère directement les données des agents à Operations Management Suite. La passerelle n’analyse pas les données en transit.

Lorsque vous intégrez un groupe d’administration Operations Manager dans Operations Management Suite, vous pouvez configurer les serveurs d’administration afin qu’ils se connectent à la passerelle OMS. Les serveurs reçoivent des informations de configuration et envoient ensuite les données collectées en fonction de la solution que vous avez configurée. Les agents Operations Manager envoient certaines données, comme les alertes Operations Manager, les évaluations de la configuration, les espaces d’instance et les données de capacité, au serveur d’administration. D’autres données volumineuses, telles que les journaux IIS (Internet Information Services), les données de performances et les événements de sécurité, sont envoyées directement à la passerelle OMS. 

Un serveur de passerelle Operations Manager déployé dans un réseau de périmètre (également appelé *DMZ* (*zone démilitarisée*) et *sous-réseau filtré*) ou tout autre réseau isolé pour surveiller que les systèmes non fiables ne peut pas communiquer avec la passerelle OMS. Les serveurs de passerelle Operations Manager peuvent uniquement générer des rapports sur un serveur d’administration. Lorsque vous configurez un groupe d’administration Operations Manager pour qu’il communique avec la passerelle OMS, les informations de configuration de proxy sont automatiquement distribuées à tous les ordinateurs gérés par agent qui sont configurés pour collecter des données pour Log Analytics. Les informations de configuration de proxy sont distribuées automatiquement même si le paramètre est vide.    

Pour fournir une haute disponibilité aux groupes directement connectés ou aux groupes Operations Management qui communiquent avec Operations Management Suite par le biais de la passerelle, vous pouvez utiliser l’équilibrage de charge réseau. L’équilibrage de charge réseau redirige et répartit le trafic entre plusieurs serveurs de passerelle. Si un serveur de passerelle tombe en panne, le trafic est redirigé vers un autre nœud disponible.  

Nous vous recommandons d’installer l’agent Operations Management Suite sur l’ordinateur qui exécute le logiciel de passerelle OMS. De cette façon, vous pouvez surveiller la passerelle OMS et analyser les données d’événement ou de performances. En outre, l’agent permet à la passerelle OMS d’identifier les points de terminaison de service dont il a besoin pour communiquer.

Chaque agent doit disposer d’une connexion réseau avec sa passerelle, afin que les agents puissent automatiquement transférer des données vers la passerelle et recevoir des données de cette dernière. Nous vous recommandons de ne pas installer la passerelle sur un contrôleur de domaine.

Le schéma suivant affiche le flux de données entre les agents directs et Operations Management Suite par le biais du serveur de passerelle. La configuration du proxy pour les agents doit correspondre au port sur lequel la passerelle OMS est configurée afin de communiquer avec Operations Management Suite.  

![Schéma présentant la communication entre l’agent direct et Operations Management Suite](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Le schéma suivant illustre le flux de données entre un groupe d’administration Operations Manager et Operations Management Suite.   

![Schéma présentant la communication entre Operations Manager et Operations Management Suite](./media/log-analytics-oms-gateway/oms-omsgateway-opsmgrconnect.png)

## <a name="prerequisites"></a>Composants requis

L’ordinateur que vous choisissez pour exécuter la passerelle OMS présenter la configuration suivante :

* Système d’exploitation Windows 10, Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 ou Windows Server 2008
* Microsoft .NET Framework 4.5
* Processeur 4 cœurs minimum
* 8 Go de mémoire minimum 

### <a name="language-availability"></a>Langues disponibles

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

## <a name="download-oms-gateway"></a>Télécharger la passerelle OMS

Vous disposez de trois moyens d’obtenir la dernière version de la passerelle OMS :

* Téléchargez-la à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

* Téléchargez-la à partir du portail Operations Management Suite. Après vous être connecté à votre espace de travail Operations Management Suite :

    1. Allez dans **Paramètres** > **Sources connectées** > **Serveurs Windows**. 
    2. Sélectionnez **Télécharger la passerelle OMS**.

* Téléchargez-la à partir du [portail Azure](https://portal.azure.com). Après votre connexion :  

   1. Dans la liste des services, sélectionnez **Log Analytics**.  
   2. Sélectionnez un espace de travail.
   3. Dans le panneau de votre espace de travail, sous **Général**, sélectionnez **Démarrage rapide**.
   4. Sous **Choisir une source de données pour se connecter à l’espace de travail**, sélectionnez **Ordinateurs**.
   5. Dans le panneau **Agent direct**, sélectionnez **Télécharger la passerelle OMS**.<br><br> ![télécharger la passerelle OMS](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-oms-gateway"></a>Installer la passerelle OMS

Pour installer la passerelle, exécutez les opérations suivantes. 

> [!NOTE]
> Si vous une version antérieure de la passerelle (anciennement appelée Log Analytics Forwarder) est installée, cette dernière est mise à niveau vers la version actuelle de la passerelle OMS.
>

1. Dans le dossier de destination, double-cliquez sur **OMS Gateway.msi**.
2. Sur la page d’**accueil**, sélectionnez **Suivant**.<br><br> ![Assistant d’installation de la passerelle](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. Sur la page du **contrat de licence**, sélectionnez **J’accepte les termes du contrat de licence** pour accepter les termes du contrat de licence logiciel Microsoft. 
4. Sur la page relative au **port et à l’adresse proxy** :

   1. Saisissez le numéro de port TCP à utiliser pour la passerelle. Le programme d’installation configure une règle entrante avec ce numéro de port sur le pare-feu Windows. La valeur par défaut est 8080.
      Les numéros de port valides sont compris entre 1 et 65535. Si la valeur saisie n’est pas comprise dans cette plage, un message d’erreur s’affiche.
   2. Éventuellement, si le serveur sur lequel la passerelle est installée doit communiquer par le biais d’un proxy, saisissez l’adresse proxy à laquelle la passerelle doit se connecter. Par exemple, **http://myorgname.corp.contoso.com:80**. Si la zone d’adresse du proxy est vide, la passerelle tente de se connecter directement à Internet. Si votre serveur proxy requiert une authentification, entrez un nom d'utilisateur et un mot de passe.<br><br> ![Configuration du proxy de l’assistant d’installation de la passerelle](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
5. Si Microsoft Update n’est pas activé sur l’ordinateur, la page **Microsoft Update** s’affiche. Vous pouvez choisir de l’activer sur cette page. Sinon, passez à l’étape suivante.
6. Sur la page **Dossier de destination**, conservez le dossier par défaut C:\Program Files\OMS Gateway ou indiquez l’emplacement où vous souhaitez installer la passerelle.
7. Sur la page **Prêt pour l’installation**, sélectionnez **Installer**. Une boîte de dialogue demandant l’autorisation de procéder à l’installation peut s’afficher. Sélectionnez **Oui**.
8. Sélectionnez **Terminer**. Pour vérifier que le service est en cours d’exécution, ouvrez le composant logiciel enfichable Services.msc et assurez-vous que **Passerelle OMS** apparaît dans la liste des services. Son état doit être **Exécution en cours**.<br><br> ![Vérifier les services pour la passerelle OMS](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="set-up-network-load-balancing"></a>Configurer l’équilibrage de charge réseau 
Vous pouvez configurer une haute disponibilité de la passerelle OMS avec l’équilibrage de charge réseau. Vous pouvez utiliser la fonctionnalité d’équilibrage de charge réseau dans Windows Server ou les équilibreurs de charge basés sur le matériel. Pour gérer le trafic, l’équilibrage de charge redirige les connexions demandées à partir d’agents Operations Management Suite ou de serveurs d’administration Operations Manager sur ses nœuds. Si un serveur de passerelle tombe en panne, le trafic est redirigé vers d’autres nœuds.

Pour apprendre à concevoir et déployer un cluster d’équilibrage de charge réseau Windows Server 2016, consultez [Équilibrage de charge réseau](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  

Pour configurer un cluster d’équilibrage de charge réseau Windows Server, procédez comme suit :  

1. À l’aide d’un compte d’administrateur, connectez-vous au serveur Windows qui est membre du cluster d’équilibrage de charge réseau.  
2. Dans le Gestionnaire de serveur, ouvrez le Gestionnaire d’équilibrage de charge réseau.
3. Sélectionnez **Outils**, puis **Gestionnaire d’équilibrage de charge réseau**.
4. Pour connecter un serveur de passerelle OMS à Microsoft Monitoring Agent qui est installé, faites un clic droit sur l’adresse IP du cluster, puis sélectionnez **Ajouter l’hôte au cluster**.<br><br> ![Gestionnaire d’équilibrage de charge réseau : Ajouter l’hôte au cluster](./media/log-analytics-oms-gateway/nlb02.png)<br> 
5. Entrez l’adresse IP du serveur de passerelle que vous voulez connecter.<br><br> ![Gestionnaire d’équilibrage de charge réseau : Ajouter l’hôte au cluster, Connexion](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="set-up-the-operations-management-suite-agent-and-an-operations-manager-management-group"></a>Configurer l’agent Operations Management Suite et un groupe d’administration Operations Manager
Dans cette section, vous découvrirez comment configurer les agents Operations Management Suite directement connectés ainsi qu’un groupe d’administration Operations Manager. Vous pouvez également configurer un Runbook Worker hybride Azure Automation afin d’utiliser la passerelle OMS pour communiquer avec Operations Management Suite.  

Pour en savoir plus sur la configuration requise et les étapes d’installation de l’agent Operations Management Suite sur les ordinateurs Windows directement connectés à Operations Management Suite, consultez [Connect Windows computers to Operations Management Suite (Connecter des ordinateurs Windows à Operations Management Suite)](log-analytics-windows-agents.md). Pour les ordinateurs Linux, consultez [Connect Linux computers to Operations Management Suite (Connecter des ordinateurs Linux à Operations Management Suite)](log-analytics-linux-agents.md). 

### <a name="set-up-the-operations-management-suite-agent-and-operations-manager-to-use-the-oms-gateway-as-a-proxy-server"></a>Configurer l’agent Operations Management Suite et Operations Manager pour utiliser la passerelle OMS en tant que serveur proxy

### <a name="set-up-a-standalone-operations-management-suite-agent"></a>Configurer un agent Operations Management Suite autonome
Pour plus d’informations sur la configuration d’un agent dans le but qu’il utilise un serveur proxy, consultez [Configurer les paramètres de pare-feu et de proxy avec Microsoft Monitoring Agent](log-analytics-proxy-firewall.md). Dans ce cas, le serveur proxy est la passerelle. Si vous avez déployé plusieurs serveurs de passerelle derrière un équilibreur de charge réseau, la configuration du proxy de l’agent Operations Management Suite est l’adresse IP virtuelle de l’équilibreur de charge réseau :<br><br> ![Propriétés de Microsoft Monitoring Agent : Paramètres de proxy](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="set-up-operations-manager-all-agents-use-the-same-proxy-server"></a>Configurer Operations Manager (tous les agents utilisent le même serveur proxy)
Configurez Operations Manager pour ajouter le serveur de passerelle. La configuration du proxy Operations Manager est automatiquement appliquée à tous les agents générant un rapport pour Operations Manager, même si le paramètre est vide.

Pour utiliser la passerelle OMS avec Operations Manager, vous devez remplir les conditions suivantes :

* Microsoft Monitoring Agent (version de l’agent **8.0.10900.0** et versions ultérieures) doit être installé sur le serveur de passerelle. Il doit être défini pour les espaces de travail de l’agent Operations Management Suite avec lequel vous souhaitez communiquer.
* La passerelle doit disposer d’une connectivité Internet, ou être connectée à un serveur proxy connecté à Internet.

> [!NOTE]
> Si vous ne spécifiez pas de valeur pour la passerelle, les valeurs vides sont transférées à tous les agents.

Pour connecter votre serveur Operations Manager à votre espace de travail de l’agent Operations Management Suite :

1. Dans la console Operations Manager, accédez à **Operations Management Suite** > **Connexion** > **Configurer le serveur proxy**.<br><br> ![Operations Manager : Configurer le serveur proxy](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. Sélectionnez **Utiliser un serveur proxy pour accéder à Operations Management Suite**. Entrez l’adresse IP du serveur de passerelle OMS ou l’adresse IP virtuelle de l’équilibreur de charge réseau. Assurez-vous de démarrer avec le préfixe **http://**.<br><br> ![Operations Manager : Adresse du serveur proxy](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. Sélectionnez **Terminer**. 

### <a name="set-up-operations-manager-specific-agents-use-the-proxy-server"></a>Configurer Operations Manager (des agents spécifiques utilisent le serveur proxy)
Pour les environnements complexes ou volumineux, vous pouvez souhaiter que seuls des serveurs spécifiques (ou des groupes) utilisent le serveur de passerelle OMS. Pour ces serveurs, vous ne pouvez pas mettre à jour l’agent Operations Manager directement. La valeur est remplacée par la valeur globale du groupe d’administration. Au lieu de cela, remplacez la règle utilisée pour transférer ces valeurs.

> [!NOTE] 
> Vous pouvez utiliser la même technique de configuration pour exécuter plusieurs serveurs de passerelle OMS dans votre environnement. Par exemple, vous pouvez exiger que des serveurs de passerelle OMS spécifiques soient spécifiés par région.

1. Ouvrez la console System Center Operations Manager, puis sélectionnez l’espace de travail **Création**.  
2. Sélectionnez **Règles** et, dans la barre d’outils System Center Operations Manager, sélectionnez le bouton **Étendue**. Si ce bouton n’est pas disponible, vérifiez que vous avez bien sélectionné un objet, et non un dossier, dans le volet **Analyse**. La boîte de dialogue **Objets du pack de gestion de l’étendue** affiche une liste des classes, groupes ou objets couramment ciblés. 
3. Dans la zone **Rechercher** , entrez **Service de contrôle d’intégrité**. Sélectionnez-le dans la liste. Sélectionnez **OK**.  
4. Dans la barre d’outils de la console Operations Manager, recherchez la règle **Règle du paramètre proxy Advisor**. Sélectionnez **Remplace**, puis pointez sur **Remplacer la règle\D’un objet de classe spécifique : Service d’intégrité**. Sélectionnez un objet spécifique dans la liste. Si vous le souhaitez, vous pouvez créer un groupe personnalisé qui contient l’objet de service de contrôle d’intégrité des serveurs auxquels vous souhaitez appliquer ce remplacement. Appliquez ensuite ce remplacement à ce groupe.
5. Dans la boîte de dialogue **Propriétés du remplacement**, en regard du paramètre **WebProxyAddress**, sélectionnez la colonne **Remplacer**. Dans la zone **Remplacer la valeur**, entrez l’URL du serveur de passerelle OMS. Assurez-vous de démarrer avec le préfixe **http://**.

   >[!NOTE]
   > Vous n’avez pas besoin d’activer la règle. La règle est gérée automatiquement avec un remplacement dans le pack d’administration System Center Advisor Secure Reference Override. Ce pack d’administration cible le groupe de serveur de surveillance System Center Advisor.
   > 

6. Pour spécifier un pack d’administration, effectuez l’une des opérations suivantes :
    * Dans la liste **Sélectionner un pack d’administration de destination**, sélectionnez un pack d’administration.
    * Pour créer un pack d’administration non scellé, sélectionnez **Nouveau**. 
7. Sélectionnez **OK**. 

### <a name="set-up-automation-hybrid-workers"></a>Créer des Workers hybrides Automation
Si vous disposez de Runbooks Workers hybrides Azure Automation dans votre environnement, vous pouvez créer une solution de contournement manuelle et temporaire afin que la passerelle les prenne en charge.

Lors des étapes suivantes, vous devez connaître la région Azure dans laquelle réside le compte Automation. Pour trouver son emplacement :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez le service Azure Automation.
3. Sélectionnez le compte Azure Automation approprié.
4. Sous **Emplacement**, consultez la région.<br><br> ![Portail Azure : Emplacement du compte Automation](./media/log-analytics-oms-gateway/location.png)  

Utilisez les tableaux suivants pour identifier l’URL pour chaque emplacement :

**URL de service de données d’exécution de la tâche**

| Emplacement | URL |
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

| Emplacement | URL |
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

Si votre ordinateur est automatiquement inscrit en tant que Runbook Worker hybride pour l’application de correctifs à l’aide de la solution de gestion des mises à jour, suivez ces étapes :

1. Ajoutez les URL de service de données d’exécution du travail à la liste d’**hôtes autorisés** sur la passerelle OMS. Par exemple :  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Redémarrez le service de passerelle OMS en utilisant la cmdlet Azure PowerShell suivante : `Restart-Service OMSGatewayService`

Si votre ordinateur est intégré dans Azure Automation à l’aide de la cmdlet d’inscription Runbook Worker hybride, suivez ces étapes :

1. Ajoutez l’URL d’inscription de service de l’agent à la liste d’**hôtes autorisés** sur la passerelle OMS. Par exemple : `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Ajoutez les URL de service de données d’exécution du travail à la liste d’**hôtes autorisés** sur la passerelle OMS. Par exemple :  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Redémarrez le service de passerelle OMS :  `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Applets de commande PowerShell utiles
Les cmdlets peuvent vous aider à mettre à jour les paramètres de la passerelle OMS. Avant d’utiliser les cmdlets PowerShell, veillez à :

1. Installer la passerelle OMS (MSI).
2. Ouvrez une fenêtre de console PowerShell.
3. Pour importer le module, saisissez cette commande : `Import-Module OMSGateway`. Si aucune erreur ne se produit, cela signifie que le module a été importé avec succès : vous pouvez donc utiliser les cmdlets. 
4. Entrez `Get-Module OMSGateway`.
5. Une fois que vous avez apporté vos modifications à l’aide des cmdlets, redémarrez le service de passerelle OMS.

Si vous obtenez une erreur à l’étape 3, cela signifie que le module n’a pas été importé. Cette erreur peut se produire si PowerShell est dans l’incapacité de trouver le module. Le module est répertorié dans le dossier d’installation de la passerelle : C:\Program File\Microsoft OMS Gateway\PowerShell.

| Applet de commande | parameters | Description | Exemples |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |Clé (obligatoire) <br> Valeur |Modifie la configuration de service |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |Clé |Récupère la configuration de service |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |Adresse <br> Nom d’utilisateur <br> Mot de passe |Définit l’adresse (et les informations d’identification) du proxy de relais (en amont) |1. Définir un proxy de réponse et les informations d’identification : `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. Définir un proxy de réponse n’ayant pas besoin d’authentification : `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. Désactiver le paramètre de proxy de réponse, pour indiquer qu’il n’est pas nécessaire : `Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |Récupère l’adresse du proxy de relais (en amont) |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |Hôte (obligatoire) |Ajoute l’hôte à la liste d’éléments autorisés |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |Hôte (obligatoire) |Supprime l’hôte de la liste d’éléments autorisés |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |Récupère l’hôte actuellement autorisé (uniquement l’hôte autorisé configuré localement, et non les hôtes autorisés téléchargés automatiquement) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |Objet (obligatoire) |Ajoute l’objet du certificat client à la liste d’éléments autorisés |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |Objet (obligatoire) |Supprime l’objet du certificat client de la liste d’éléments autorisés |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |Récupère les objets de certificat client actuellement autorisés (uniquement les objets configurés localement, et non les objets autorisés téléchargés automatiquement) |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshooting"></a>Résolution des problèmes
Pour collecter des événements consignés par la passerelle, vous devez également avoir installé l’agent Operations Management Suite.<br><br> ![Observateur d’événements : Journal de la passerelle OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**Identifiants et descriptions des événements de la passerelle OMS**

Le tableau suivant présente les identifiants et descriptions d’événements pour les événements du journal de la passerelle OMS :

| ID | Description |
| --- | --- |
| 400 |Toute erreur d’application ne disposant pas d’un identifiant spécifique |
| 401 |Configuration incorrecte. Par exemple : listenPort = "\<text\>" au lieu d’un nombre entier |
| 402 |Exception lors de l’analyse des messages de liaison TLS (Transport Layer Security) |
| 403 |Erreur de mise en réseau. Par exemple : Impossible de se connecter au serveur cible |
| 100 |Informations générales |
| 101 |Le service a démarré |
| 102 |Le service s’est arrêté |
| 103 |Réception d’une commande HTTP CONNECT de la part du client |
| 104 |L’élément n’est pas une commande HTTP CONNECT |
| 105 |Le serveur de destination n’est pas dans la liste autorisée ou le port de destination n’est pas un port sécurisé (443) <br> <br> Vérifiez que l’agent Microsoft Monitoring Agent sur votre serveur de passerelle et les agents communiquant avec la passerelle sont connectés au même espace de travail Log Analytics. |
| 105 |ERREUR TcpConnection – Certificat client non valide : CN=passerelle <br><br> Assurez-vous que : <br>    <br> &#149; Vous utilisez la passerelle OMS version 1.0.395.0 ou une version ultérieure. <br> &#149; L’agent Microsoft Monitoring Agent sur votre serveur de passerelle et les agents communiquant avec la passerelle sont connectés au même espace de travail Log Analytics. |
| 106 |Toute raison pour laquelle la session TLS est suspecte et rejetée |
| 107 |La session TLS a été vérifiée |

**Compteurs de performances à collecter**

Le tableau suivant montre les compteurs de performances disponibles pour la passerelle OMS. Vous pouvez ajouter des compteurs par le biais de l’Analyseur de performances Windows.

| Nom | Description |
| --- | --- |
| Passerelle OMS/Connexion du client actif |Nombre de connexions du réseau client actif (TCP) |
| Passerelle OMS/Nombre d’erreurs |Nombre d’erreurs |
| Passerelle OMS/Client connecté |Nombre de clients connectés |
| Passerelle OMS/Nombre de rejets |Nombre de rejets dus à des erreurs de validation de TLS |

![Compteurs de performances de la passerelle OMS](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Obtenir de l’aide
Dans le portail Azure, lorsque vous êtes connecté, vous pouvez créer une demande d’assistance pour la passerelle OMS ou tout autre service ou toute fonctionnalité de service Azure.
Pour demander de l’aide, sélectionnez le symbole en forme de point d’interrogation dans le coin supérieur droit du portail, puis sélectionnez **Nouvelle demande de support**. Remplissez le nouveau formulaire de demande de support.

![Nouvelle demande de support](./media/log-analytics-oms-gateway/support.png)

Vous pouvez également laisser des commentaires concernant Microsoft Operations Management Suite ou Log Analytics sur le [forum de commentaires de Microsoft Azure](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Étapes suivantes
Pour collecter des données à partir de sources connectées dans votre espace de travail Operations Management Suite, vous pouvez [ajouter des sources de données](log-analytics-data-sources.md). Vous pouvez stocker les données dans le référentiel Operations Management Suite.

