---
title: "Propriétés du rôle Azure"
description: "Découvrez comment utiliser la boîte à outils Azure pour Eclipse pour configurer les paramètres de rôle Azure."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 5c0ec412-5702-465a-8f47-87a8ce99a267
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6b65a0c306ec9316fd8a86f8ebb2808cce65200a


---
# <a name="azure-role-properties"></a>Propriétés du rôle Azure
Vous avez la possibilité de définir différents paramètres de configuration pour votre rôle Azure dans la boîte à outils Azure pour Eclipse.

## <a name="configuring-azure-role-properties"></a>Configuration des propriétés de rôle Azure
La configuration des propriétés de votre rôle Azure s’effectue via les boîtes de dialogue de propriété du rôle de travail. Ouvrez le menu contextuel correspondant au rôle dans le volet de l’Explorateur de projets Eclipse, puis sélectionnez le sous-menu **Azure** . (Si le rôle n’apparaît pas dans l’Explorateur de projets Eclipse, développez votre projet dans l’Explorateur de projets Microsoft Azure).

![][ic789599]

Les différentes propriétés qui peuvent être définies à partir des boîtes de dialogue **Propriétés** sont décrites dans cette rubrique. Notez que de nombreuses propriétés sont renseignées automatiquement lorsque vous créez un projet de déploiement Azure.

Les rôles Azure proposent les pages de propriétés qui suivent.

* [Propriétés de machine virtuelle](#virtual_machine_properties)
* [Propriétés de mise en cache](#caching_properties)
* [Propriétés de certificats](#certificates_properties)
* [Propriétés des composants](#components_properties)
* [Propriétés de débogage](#debugging_properties)
* [Propriétés des points de terminaison](#endpoints_properties)
* [Propriétés des variables d’environnement](#environment_variables_properties)
* [Propriétés d’équilibre de charge/affinité de session (appelées « sessions temporaires »)](#session_affinity_properties)
* [Propriétés du stockage local](#local_storage_properties)
* [Propriétés de configuration de serveur](#server_configuration_properties)
* [Propriétés de déchargement SSL](#ssl_offloading_properties)

<a name="virtual_machine_properties"></a>

### <a name="virtual-machine-properties"></a>Propriétés de machine virtuelle
Ouvrez le menu contextuel correspondant au rôle dans le volet de l’Explorateur de projets Eclipse, cliquez sur **Azure**, puis sur **Propriétés**. Vous aurez alors la possibilité de modifier la taille de la machine virtuelle, mais également le nombre d’instances, comme illustré dans l’image suivante.

![][ic719499]

> [!NOTE]
> Windows uniquement : lorsque vous définissez le nombre d’instances sur une valeur supérieure à 1 et configurez également un serveur d’applications, la boîte à outils permet à une seule instance de rôle de s’exécuter dans l’émulateur, indépendamment de ce paramètre. Il s’agit d’éviter les conflits de liaisons de port entre les différentes instances de serveur (par exemple, que toutes essaient d’établir une liaison avec le port 8080) lorsqu’elles s’exécutent sur le même ordinateur. Le paramètre de nombre d’instances souhaité est conservé, mais ne prend effet qu’au moment du déploiement sur le cloud.
> 
> 

<a name="caching_properties"></a> 

### <a name="caching-properties"></a>Propriétés de mise en cache
Ouvrez le menu contextuel du rôle dans le volet Explorateur de projets Eclipse, cliquez sur **Azure**, puis sur **Mise en cache**. Dans cette boîte de dialogue, vous pouvez activer les mises en cache colocalisées nommées compatibles  memcache, ce qui vous permet d’accélérer l’exécution de vos applications web.

![][ic719483]

Dans la page de propriété **Mise en cache** , vous pouvez spécifier des paramètres globaux des éléments suivants :

* indique si la mise en cache colocalisée est activée ou non.
* taille du cache sous forme de pourcentage de mémoire.
* nom du compte de stockage pour l’enregistrement de l’état de la mise en cache lorsque votre application s’exécute en tant que service cloud, ou aucun, si vous ne souhaitez pas enregistrer l’état de la mise en cache. (Le nom de compte de stockage n’est pas utilisé lorsque vous exécutez votre application dans l’émulateur de calcul.) Si vous attribuez au nom de compte de stockage la valeur **(auto)** (la valeur par défaut), votre configuration de mise en cache utilise automatiquement le compte de stockage que vous sélectionnez dans la boîte de dialogue **Publier sur Azure**.

> [!NOTE]
> Le paramètre **(auto)** aura l’effet souhaité uniquement si vous publiez votre déploiement en utilisant la boîte à outils Eclipse de l’assistant Publication. Dans le cas contraire, si vous publiez le fichier .cspkg manuellement en utilisant un mécanisme externe tel que le [portail de gestion Azure][portail de gestion Azure], le déploiement ne fonctionnera pas correctement.
> 
> 

La boîte de dialogue suivante présente les propriétés d’une mémoire cache.

![][ic719501]

* **Nom :** nom de la mise en cache colocalisée.
* **Numéro de port :** numéro de port à utiliser pour la mise en cache.
* **Stratégie d’expiration :** une des valeurs qui suivent indique le moment où clé de mise en cache expire.
  * **Absolu :** la clé expire lorsque le délai spécifié par **Minutes de validité** est atteint.
  * **N’expire jamais :** la clé n’a pas de date d’expiration.
  * **Fenêtre coulissante :** la clé expire si aucun accès n’a duré le temps spécifié dans **Minutes de validité** ; à chaque accès, l’heure d’expiration est réinitialisée.
* **Minutes de validité :** nombre maximal de minutes de validité d’une clé, en fonction de la stratégie d’expiration.
* **Haute disponibilité avec sauvegardes répliquées sur des instances de rôle différentes :** si cette option est activée, elle offre la haute disponibilité en utilisant des sauvegardes répliquées sur différentes instances de rôle. Notez qu’au moins deux instances de rôles doivent fonctionner pour que le déploiement de cette fonctionnalité fonctionne.

Pour ajouter un nouveau cache, cliquez sur le bouton **Ajouter** situé dans la page de propriété **Mise en cache**, et une boîte de dialogue **Configurer un cache nommé** s’ouvre. Attribuez des valeurs aux propriétés décrites ci-dessus.

Pour modifier une mémoire cache nommée, sélectionnez-la, puis cliquez sur le bouton **Modifier** situé dans la page de propriété **Mise en cache**. Une boîte de dialogue vous permettant de modifier les propriétés de mise en cache s’ouvrira. Appuyez sur **OK** pour enregistrer les valeurs de mise en mémoire cache.

Pour supprimer un cache, sélectionnez-le, puis cliquez sur le bouton **Supprimer** dans la page de propriété **Mise en cache**, puis cliquez sur **Oui** pour confirmer la suppression.

Pour plus d’informations sur l’utilisation de la mise en cache, consultez [Utilisation de la mise en cache colocalisée][Utilisation de la mise en cache colocalisée].

<a name="certificates_properties"></a> 

### <a name="certificates-properties"></a>Propriétés de certificats
Ouvrez le menu contextuel du rôle dans le volet Explorateur de projets Eclipse, cliquez sur **Azure**, puis sur **Certificats**.

![][ic710964]

Dans cette boîte de dialogue, vous pouvez ajouter ou supprimer des certificats référencés par votre projet Eclipse. Notez que les certificats répertoriés ici ne sont pas automatiquement stockés dans un fichier keystore Java et par conséquent, ne sont pas automatiquement disponibles à l’utilisation depuis une application Java. Ils sont simplement enregistrés auprès d’Azure ce qui permet de les précharger dans les fenêtres de stockage de certificat sur les machines virtuelles exécutant votre déploiement et de les utiliser avec d’autres logiciels Windows. Actuellement, la seule fonctionnalité de la boîte à outils qui utilise les certificats référencés dans la boîte de dialogue **Certificats** est [Déchargement SSL][Déchargement SSL] en raison de sa dépendance vis-à-vis d’IIS (Internet Information Services) et d’Application Request Routing (ARR) qui exigent que le certificat correspondant soit mis à disposition.

Lorsque vous déployez votre projet sur Azure à l’aide de l’Assistant Publication, vous devez référencer les fichiers PFX (Personal Information Exchange) correspondant à ces certificats, ainsi que leurs mots de passe, afin de les télécharger automatiquement vers le service Azure, mais uniquement s’ils n’ont pas été téléchargés au préalable.

<a name="components_properties"></a> 

### <a name="components-properties"></a>Propriétés des composants
Ouvrez le menu contextuel du rôle dans le volet Explorateur de projets Eclipse, cliquez sur **Azure**, puis sur **Composants**. Dans cette boîte de dialogue, vous avez la possibilité d’ajouter, de modifier ou de supprimer des composants de votre rôle, mais également de modifier l’ordre dans lequel ils sont traités.

![][ic719502]

La fonctionnalité des composants vous permet d’ajouter des dépendances à votre projet de déploiement Azure, notamment des projets d’application Java, des fichiers spéciaux et les instructions de ligne de commande exécutables nécessaires à votre déploiement.

Pour chaque composant, vous pouvez spécifier :

* L’opération à effectuer lors de l’importation du composant dans votre projet de déploiement Azure lors sa création.
* L’opération à exécuter lors du déploiement de ce composant dans le cloud Azure.

> [!NOTE]
> Lorsque vous spécifiez des fichiers de composants ou des lignes de commande, n’oubliez pas que votre déploiement est destiné à être publié sur une machine virtuelle Windows, de sorte que les étapes de personnalisation doivent être valides sur un système d’exploitation Windows. 
> 
> 

Les composants possèdent les propriétés suivantes :

* **Importation :** méthode qui indique la façon dont le composant est importé dans le projet au moment où ce dernier est généré. Il peut s’agir de l’une des valeurs suivantes :
  * **copie :** le composant est copié depuis le chemin d’accès local spécifié par la propriété **De** dans le répertoire **approot** du rôle.
  * **EAR :** le composant est une archive d’entreprise Java (EAR) et est importé à partir d’un projet d’application d’entreprise dans le chemin d’accès local spécifié par la propriété **De**. (Il est détecté automatiquement par la boîte à outils en fonction de la nature du projet à cet emplacement.)
  * **JAR :** le composant est une archive Java (JAR) et est importé à partir d’un projet Java dans le chemin d’accès local spécifié par la propriété **De**. (Il est détecté automatiquement par la boîte à outils en fonction de la nature du projet à cet emplacement.)
  * **aucun :** aucune action n’est exécutée pour importer le composant. Ceci s’applique lorsque le composant est supposé être déjà présent dans le répertoire **approot** du rôle, ou lorsqu’il s’agit d’une simple instruction de ligne de commande exécutable, comme spécifié dans la propriété **En tant que** lorsque la méthode de **déploiement** est **exec**.
  * **WAR :** le composant est une archive d’application web Java (WAR) et est importé à partir d’un projet web dynamique dans le chemin d’accès local spécifié par la propriété **De**. (Il est détecté automatiquement par la boîte à outils en fonction de la nature du projet à cet emplacement.)
  * **zip :** le composant est un fichier compressé et est importé par la compression du répertoire ou du fichier spécifié par la propriété **De**.
* **De :** chemin d’accès source sur votre ordinateur local vers le dossier ou le fichier qui représente le ou les éléments à importer dans votre déploiement. Les variables d’environnement Windows peuvent être utilisées dans cette propriété. Tous les composants importables seront importés dans le répertoire **approot** du rôle au moment de la génération du projet.
  
    Notez que vous avez la possibilité de déployer un composant à partir d’un téléchargement lorsque vous procédez à un déploiement sur le cloud (et non dans l’émulateur de calcul). Consultez les informations connexes ci-dessous concernant l’ajout d’un composant.    
* **En tant que :** nom de fichier sous lequel le composant sera importé dans le répertoire **approot** du rôle et enfin, déployé dans le cloud Azure. Laissez cette propriété vide pour garder le même nom sur l’ordinateur local. (Pour les composants exécutables, c’est-à-dire ceux dont la méthode de **déploiement** est définie sur **exec**, il peut s’agir d’une instruction de ligne de commande Windows arbitraire.)
  
  > [!IMPORTANT]
  > Si vous utilisez des espaces dans cette valeur, ils sont gérés différemment selon la méthode de déploiement. Si la méthode de déploiement est **exec**, les espaces sont considérés comme des séparateurs d’arguments de ligne de commande et non comme faisant partie du nom de fichier. Pour toutes les autres méthodes de déploiement, les espaces sont interprétés comme des composants du nom de fichier.
  > 
  > 
* **Déployer :** méthode qui indique l’action appliquée au composant au démarrage du déploiement. Il peut s’agir de l’une des valeurs suivantes :
  
  * **copie :** le composant de téléchargement est copié dans le chemin de destination spécifié par la propriété **À**.
  * **exec :** le composant est une instruction de ligne de commande Windows exécutée dans le contexte du chemin d’accès spécifié par la propriété **À** au moment où le déploiement démarre.
  * **aucune :** aucune action n’est appliquée au composant lorsque le déploiement démarre.
  * **zip :** le composant de téléchargement est extrait dans le chemin de destination spécifié par la propriété **À**. Cette méthode est disponible uniquement lorsque la propriété **Importer** est définie sur **zip**.
* **À :** chemin de destination de la machine virtuelle sur laquelle le composant va être déployé. Des variables d’environnement Windows peuvent être utilisées dans cette propriété, et les chemins d’accès sont relatifs à **approot**.

Pour ajouter un nouveau composant, cliquez sur le bouton **Ajouter** situé dans la page de propriété **Composants** et une boîte de dialogue **Composant de rôle Azure** s’ouvre. Attribuez des valeurs aux propriétés décrites ci-dessus. 

Ce qui suit est un exemple d’ajout d’un nouveau composant WAR.

![][ic719503]

Lors d’un déploiement sur le cloud (et non sur l’émulateur de calcul), si vous souhaitez déployer le composant à partir d’un téléchargement, assurez-vous que l’option **Dans le cloud, ne pas inclure dans le package, mais déployer à partir de** est activée. Si vous souhaitez effectuer le téléchargement à partir de votre compte de stockage Azure, sélectionnez-le dans la liste déroulante **Compte de stockage** (vous pouvez cliquer sur le lien **Comptes** pour modifier le contenu de la liste). Le champ **URL** sera alors partiellement renseigné. Remplissez ensuite la partie de l’URL restante. Si vous ne souhaitez pas utiliser le stockage Azure, sélectionnez **(aucun)** dans la liste déroulante **Compte de stockage**, puis saisissez l’URL de votre composant dans le champ **URL**. Spécifiez l’une des méthodes suivantes :

* **copie :** le composant de téléchargement est copié dans le chemin d’accès de destination spécifié par le chemin d’accès **Vers le répertoire**.
* **même :** la méthode utilisée pour le **déploiement à partir du téléchargement** est la même que pour le **déploiement à partir d’un package**.
* **zip :** le composant de téléchargement est extrait dans le chemin d’accès de destination spécifié par le chemin d’accès **Vers le répertoire**.

Pour modifier un composant, sélectionnez-le, puis cliquez sur le bouton **Modifier** situé dans la page de propriété **Composants**. Une boîte de dialogue vous permettant de modifier les propriétés du composant s’ouvre. Appuyez sur **OK** pour enregistrer les valeurs du composant.

Pour supprimer un composant, sélectionnez-le, cliquez sur le bouton **Supprimer** dans la page de propriété **Composants**, puis cliquez sur **Oui** pour confirmer la suppression.

Les règles sont traitées dans l’ordre répertorié. Utilisez les boutons **Monter** et **Descendre** pour organiser l’ordre.

> [!NOTE]
> La fonctionnalité de configuration du serveur s’appuie également sur des composants. Ces composants ne peuvent pas être supprimés ou modifiés si l’on ne supprime pas la configuration du serveur correspondant. Vous êtes interrogé à ce sujet lorsque vous tentez d’apporter des modifications à ces composants.
> 
> 

<a name="debugging_properties"></a> 

### <a name="debugging-properties"></a>Propriétés de débogage
Ouvrez le menu contextuel du rôle dans le volet Explorateur de projets Eclipse, cliquez sur **Azure**, puis sur **Déboguer**. Dans cette boîte de dialogue, vous avez la possibilité d’activer ou de désactiver le débogage à distance, ainsi que de créer des configurations de débogage, comme indiqué dans l’image suivante.

![][ic719504]

Pour plus d’informations sur le débogage, consultez [Débogage des applications Azure dans Eclipse][Débogage des applications Azure dans Eclipse].

<a name="endpoints_properties"></a> 

### <a name="endpoints-properties"></a>Propriétés des points de terminaison
Ouvrez le menu contextuel du rôle dans le volet Explorateur de projets Eclipse, cliquez sur **Azure**, puis sur **Points de terminaison**. Dans cette boîte de dialogue, vous avez la possibilité de créer un point de terminaison, ainsi que de le modifier ou de le supprimer, comme illustré dans l’image suivante.

![][ic719505]

Pour ajouter un point de terminaison, cliquez sur le bouton **Ajouter** dans la page de propriété **Points de terminaison**. La boîte de dialogue **Ajouter le point de terminaison** s’ouvre.

![][ic710897]

Saisissez un nom pour le point de terminaison, sélectionnez le type (**Entrée**, **Interne**, ou **Entrée d’instance**), puis spécifiez le port public et privé. Appuyez sur **OK** pour enregistrer les nouvelles valeurs de point de terminaison.

Selon le type de point de terminaison, vous pouvez utiliser des plages de ports comme suit :

* Pour un point de terminaison d’instance d’entrée, le port public peut être une plage de ports (par exemple **2000-2010**) et le port privé une valeur fixe.
* Dans le cas d’un point de terminaison interne, le port public n’est pas utilisé, et le port privé peut être une plage, être vide ou défini sur un astérisque pour indiquer qu’il est défini automatiquement par Azure.
* Dans le cas d’un point de terminaison d’entrée, le port public ne peut être qu’une valeur fixe, et le port privé, lui, peut correspondre à une valeur fixe, une plage vide ou être défini sur un astérisque pour indiquer qu’il est défini automatiquement par Azure.

Si vous souhaitez utiliser un numéro de port unique et non une plage de numéros, laissez la zone de texte de la fin de plage vide.

Pour les ports qui ont pour valeur Automatique, si vous devez déterminer le port effectivement utilisé lors de l’exécution, votre application peut utiliser l’API Azure Service Runtime, qui est décrit dans le [résumé de package com.microsoft.windowsazure.serviceruntime][résumé de package com.microsoft.windowsazure.serviceruntime].

Pour voir comment les points de terminaison d’entrée d’instance peuvent être utilisés pour le débogage d’un déploiement à plusieurs instances, consultez [Débogage d’une instance de rôle spécifique dans un déploiement à plusieurs instances][Débogage d’une instance de rôle spécifique dans un déploiement à plusieurs instances].

Pour modifier un point de terminaison, sélectionnez-le puis cliquez sur le bouton **Modifier** situé dans la page de propriété **Points de terminaison**. Une boîte de dialogue vous permettant de modifier le nom du point de terminaison, le type et les ports public et privé. Appuyez sur **OK** pour enregistrer les valeurs de point de terminaison modifiées.

Pour supprimer un point de terminaison, sélectionnez-le, puis cliquez sur le bouton **Supprimer** dans la page de propriété **Points de terminaison**, puis cliquez sur **Oui** pour confirmer la suppression.

Pour configurer correctement certaines fonctionnalités (telles que la mise en cache, le débogage distant, l’affinité de session ou le déchargement SSL) activées par l’utilisateur sur un rôle, la boîte à outils peut configurer automatiquement des points de terminaison spéciaux qui seront répertoriés au même titre que les points de terminaison définis par l’utilisateur. La boîte à outils empêche l’utilisateur de modifier ou supprimer automatiquement des points de terminaison générés tant que la fonctionnalité associée est activée.

<a name="environment_variables_properties"></a> 

### <a name="environment-variables-properties"></a>Propriétés des variables d’environnement
Ouvrez le menu contextuel du rôle dans le volet Explorateur de projets Eclipse, cliquez sur **Azure**, puis sur **Variables d’environnement**. Dans cette boîte de dialogue, vous avez la possibilité de créer une variable d’environnement, mais également de la modifier ou de la supprimer une, comme illustré dans l’image suivante.

![][ic719506]

Les variables d’environnement sont disponibles pour votre script de démarrage lorsque le rôle démarre.

> [!NOTE]
> Lorsque vous spécifiez des variables d’environnement ou des lignes de commande, n’oubliez pas que votre déploiement sera publié sur une machine virtuelle Windows, de sorte que les variables d’environnement doivent être valides sur un système d’exploitation Windows.
> 
> 

Pour obtenir un exemple de variable d’environnement disponible au démarrage du rôle, créez une nouvelle variable d’environnement en cliquant sur un bouton **Ajouter** . L’exemple qui suit montre la création d’une variable d’environnement nommée **MyRoleVersion** et à laquelle la valeur **1.0** est affectée.

![][ic659268]

Dans votre code jsp, vous pouvez afficher la valeur en utilisant la méthode `System.getenv` :

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

Résultat de cette sortie lors de l’exécution de votre application :

![][ic552233]

Pour modifier une variable d’environnement, sélectionnez-la, puis cliquez sur le bouton **Modifier** situé dans la page de propriété **Variables d’environnement**. Une boîte de dialogue vous permettant de modifier les propriétés du cache s’ouvre. Appuyez sur **OK** pour enregistrer les valeurs des variables d’environnement.

Pour supprimer une variable d’environnement, sélectionnez-la, puis cliquez sur le bouton **Supprimer** situé dans la page de propriété **Variables d’environnement**, puis cliquez sur **Oui** pour confirmer la suppression.

Pour configurer correctement certaines fonctionnalités (telles que la configuration serveur, le débogage distant ou le stockage local) activées par l’utilisateur sur un rôle, la boîte à outils peut configurer automatiquement des variables d’environnement spéciales qui seront répertoriées avec des variables d’environnement définies par l’utilisateur. La boîte à outils empêche l’utilisateur de modifier ou de supprimer les variables d’environnement générées aussi longtemps que la fonctionnalité associée est activée.

<a name="session_affinity_properties"></a> 

### <a name="load-balancing--session-affinity-aka-sticky-sessions-properties"></a>Propriétés d’équilibre de charge/affinité de session (appelées « sessions temporaires »)
Ouvrez le menu contextuel du rôle dans le volet Explorateur de projets Eclipse, cliquez sur **Azure**, puis sur **Équilibrage de la charge**. Dans cette boîte de dialogue, vous avez la possibilité d’activer ou de désactiver l’affinité de session, comme indiqué dans l’image suivante.

![][ic719492]

Pour plus d’informations, voir l’article [Affinité de session][Affinité de session]. Notez également le comportement de cette fonctionnalité dans le contexte du déchargement SSL, comme décrit dans [Déchargement SSL][Déchargement SSL].

<a name="local_storage_properties"></a> 

### <a name="local-storage-properties"></a>Propriétés du stockage local
Ouvrez le menu contextuel du rôle dans le volet Explorateur de projets Eclipse, cliquez sur **Azure**, puis sur **Stockage local**. Dans cette boîte de dialogue, vous avez la possibilité de créer, modifier ou supprimer un stockage local de la machine virtuelle qui exécute votre application. Il est possible de définir des valeurs de taille de stockage locales, et de déterminer si le contenu doit être conservé au moment du recyclage du rôle, comme illustré dans l’image suivante.

![][ic719508]

Vous pouvez également spécifier une variable d’environnement qui correspond au stockage local.

Par défaut, tous les éléments que vous déployez dans Azure sont placés (et décompressés) dans le dossier **approot** de l’instance de rôle. Bien que la plupart des déploiements simples puissent rester dans le dossier après la décompression, l’espace affecté au répertoire **approot** est limité et n’est pas bien défini (moins de 1 Go est un principe de base raisonnable). Par conséquent, pour vous assurer qu’Azure attribue suffisamment d’espace disque à des déploiements plus volumineux ne pouvant pas tenir dans le dossier **approot**, vous devez définir une ressource de stockage local à l’aide de la boîte de dialogue **Stockage local**. Pour connaître un moyen plus simple d’exécuter cette opération, consultez la page [Déploiements à grande échelle][Déploiements à grande échelle].

Il est facile de référencer les ressources de stockage à partir de scripts de démarrage (par exemple, votre **startup.cmd**) en utilisant la variable d’environnement automatiquement associée à la ressource par la boîte à outils Eclipse, comme indiqué dans la boîte de dialogue **Stockage local**. Cette variable d’environnement contiendra le chemin complet de la ressource locale que vous avez configuré au moment de l’exécution du script de démarrage. 

Pour modifier une ressource de stockage locale, sélectionnez-la, puis cliquez sur le bouton **Modifier** situé dans la page de propriété **Stockage local**. Une boîte de dialogue vous permettant de modifier les propriétés de ressource locale s’ouvre. Appuyez sur **OK** pour enregistrer les valeurs de ressource de stockage locales.

Pour supprimer une ressource de stockage locale, sélectionnez-la, cliquez sur le bouton **Supprimer** situé dans la page de propriété **Stockage local**, puis cliquez sur **Oui** pour confirmer la suppression.

<a name="server_configuration_properties"></a> 

### <a name="server-configuration-properties"></a>Propriétés de configuration de serveur
Ouvrez le menu contextuel correspondant au rôle dans le volet Explorateur de projets Eclipse, cliquez sur **Azure**, puis sur **Configuration du serveur**. Dans cette boîte de dialogue, vous avez la possibilité d’ajouter, de supprimer et de modifier JDK et le serveur d’applications Java utilisé par votre déploiement, mais aussi d’ajouter ou supprimer les applications (notamment les fichiers WAR, JAR ou EAR) utilisées par votre déploiement.

### <a name="jdk-configuration"></a>Configuration JDK
Cette boîte de dialogue vous permet de spécifier le package JDK à utiliser pour votre déploiement. Si vous utilisez Eclipse sous Windows, vous pouvez spécifier le package JDK à utiliser localement lors de l’exécution dans l’émulateur Azure et vous avez la possibilité de déployer cette installation locale vers Azure. Sur les systèmes d’exploitation autres que Windows, le paramètre d’émulateur JDK n’est pas applicable et il est impossible de déployer le JDK installé en local, car il n’est pas compatible avec Windows. Toutefois, quel que soit le système d’exploitation que vous utilisez, vous avez toujours la possibilité de choisir parmi les packages JDK tiers à déployer sur Azure, ou sélectionner votre propre package JDK compatible Windows depuis un autre emplacement de téléchargement.

Voici un exemple de la façon dont vous pouvez spécifier un JDK sur une application.

![][ic780647]

Si vous utilisez Eclipse sur Windows, vous pouvez spécifier un JDK à utiliser avec l’émulateur de calcul. Pour ce faire, vérifiez que l’option **Utiliser le JDK à partir de ce chemin d’accès pour un test local** est activée dans la section **Déploiement d’émulateur**. Ensuite, spécifiez le chemin d’accès local vers votre JDK ; vous pouvez accéder à différents JDK si celui que vous souhaitez utiliser n’est pas sélectionné automatiquement. Vous avez également la possibilité de déployer votre JDK sur votre service cloud Azure ; pour ce faire, sélectionnez l’option **Déployer mon JDK local (chargement automatique dans un stockage cloud)** dans la section **Déploiement cloud**.

Remarque : sur les systèmes d’exploitation autres que Windows, les paramètres de **déploiement d’émulateur** et l’option **Déployer mon JDK local** ne sont pas disponibles. L’exemple suivant illustre la spécification d’un JDK sur un Mac ou un autre système d’exploitation autre que Windows pris en charge :

![][ic789643]

Quel que soit le système d’exploitation que vous utilisez, vous disposez des deux options de **déploiement Cloud** suivantes comme source et type de votre package JDK :

* **Déployer un package JDK tiers disponible Azure** 
* **Déploiement depuis un téléchargement personnalisé** 

Si vous utilisez l’option **Déployer un package JDK tiers disponible dans Azure** :

1. Cochez la case **Déployer un package JDK tiers disponible dans Azure**.
2. Dans la liste déroulante, sélectionnez le package JDK tiers disponible sur Azure.
3. Votre onglet **JDK** ressemble à ce qui suit sur Windows :  ![][ic780648]
    Il doit se présenter comme suit sur Mac OS ou d’autres systèmes d’exploitation autres que Windows pris en charge :  ![][ic789643]
4. Cliquez sur **OK** pour enregistrer vos modifications.
5. Lorsque vous êtes invité à accepter le contrat de licence d’un fournisseur de package JDK tiers, prenez connaissance des termes du contrat de licence. Si vous les acceptez, cliquez sur **Oui** pour fermer la boîte de dialogue **Accepter le contrat de licence**.
    Notez que la logique sous-jacente des éléments apparaissant dans la liste déroulante pour l’option **Déployer un package JDK tiers disponible dans Azure** peut être personnalisée. Pour personnaliser les éléments, dans la boîte de dialogue **JDK**, cliquez sur le lien **Personnaliser**. Cela fermera la page de propriété **JDK** et ouvrira le fichier **componentsets.xml** dans Eclipse, et vous pourrez le modifier par la suite si nécessaire. La documentation pour **componentsets.xml** est incluse dans le fichier **componentsets.xml** lui-même.

Si vous utilisez l’option **Déployer un JDK à partir d’un téléchargement personnalisé** :

1. Créez un fichier zip de votre répertoire d’installation JDK, en vous assurant que le nœud du répertoire lui-même est enfant de la structure du fichier zip, mais que son contenu ne l’est pas. Prenez note du nom de l’annuaire, car vous en aurez besoin plus tard, et n’oubliez pas que cette installation de JDK sera déployée sur une machine virtuelle Windows.
2. Téléchargez le fichier zip dans votre compte de stockage Azure en tant qu’objet blob. Vous pouvez effectuer cette opération en utilisant un outil externe disponible pour télécharger des objets blob dans le stockage Azure. Il est recommandé d’utiliser un objet blob privé. Prenez note de l’URL d’objet blob du contenu du zip.
3. Cochez la case **Déployer un JDK à partir d’un téléchargement personnalisé**.
    Si vous souhaitez effectuer le téléchargement à partir de votre compte de stockage Azure, sélectionnez-le dans la liste déroulante **Compte de stockage** (vous pouvez cliquer sur le lien **Comptes** pour modifier le contenu de la liste). Le champ **URL** sera alors partiellement renseigné. Remplissez ensuite la partie de l’URL restante. Si vous ne souhaitez pas utiliser le stockage Azure, sélectionnez **(aucun)** dans la liste déroulante **Compte de stockage**, puis saisissez l’URL de votre téléchargement JDK dans le champ **URL**. Si vous utilisez le stockage Azure, les noms d’objets blob dans l’URL doivent être en minuscules.
4. Assurez-vous que la zone de texte **JAVA_HOME** contient bien le nom de répertoire correct. Par défaut, elle fait référence au nom du répertoire JDK que vous avez choisi pour l’utilisation en local. Mais si le répertoire contenu dans le fichier zip porte un autre nom (par exemple, en raison de l’utilisation d’une version différente), mettez à jour le nom de répertoire dans la zone de texte **JAVA_HOME** en conséquence, car ce paramètre sera utilisé dans le cloud (et non dans l’émulateur de calcul).
5. Cliquez sur **OK** pour enregistrer vos modifications.

Vous avez terminé. Maintenant, lorsque vous faites une compilation pour le cloud, vous pouvez constater que la taille du package est beaucoup plus limitée, le processus de génération prend généralement moins de temps, comme la publication sur le cloud. Notez que les options **Déployer mon JDK local (chargement automatique vers un stockage cloud)** ou **Déployer un JDK à partir d’un téléchargement personnalisé** sont appliquées uniquement lorsque votre application est déployée dans le cloud. Elles n’ont aucun effet sur votre expérience avec l’émulateur de calcul ; la version locale des composants est toujours utilisée lors du déploiement vers l’émulateur de calcul. 

### <a name="server-configuration"></a>Configurer le serveur
Voici un exemple de la façon dont vous pouvez spécifier un serveur d’applications.

![][ic796926]

Vérifiez que la case à cocher **Déployer un serveur de ce type** est sélectionnée, puis choisissez le type de serveur d’applications à utiliser.

Pour spécifier un serveur à utiliser pour le déploiement cloud, vous pouvez utiliser les options suivantes :

1. **Déployer un serveur tiers disponible sur Azure** : cela s’applique en particulier dans les scénarios de développement/test où l’efficacité du déploiement et la simplicité sont une priorité et lorsque le serveur ne nécessite pas une configuration personnalisée. C’est également le cas lorsque vous souhaitez utiliser un de ces serveurs comme point de départ, mais que vous intégrez les étapes de personnalisation du serveur appropriées dans le programme de démarrage de votre déploiement.
2. **Déployer à partir d’un téléchargement personnalisé** : cette option s’applique en particulier à des scénarios de production lorsque vous disposez d’un serveur spécialement préparé et configuré à utiliser dans le cloud.
3. **Déployer l’installation de mon serveur local** : cette option s’applique plus particulièrement si l’installation de votre serveur local est déjà configurée et personnalisée pour vous. Si vous choisissez cette option, vous devez également spécifier le chemin d’accès de votre serveur local dans la zone de texte **Chemin d’accès au serveur Local** ci-dessous.

Si vous utilisez l’option **Déployer un serveur tiers disponible sur Azure** :

1. Cochez la case **Déployer un serveur tiers disponible sur Azure**.
2. Dans le menu déroulant, sélectionnez le logiciel de serveur à utiliser avec votre déploiement dans le cloud. Notez que si vous avez déjà spécifié un type de serveur à utiliser, vous ne pourrez sélectionner qu’un serveur cloud qui se trouve dans la même famille que ce type de serveur. Mais si vous n’avez pas choisi de type de serveur, vous pouvez choisir parmi tous les serveurs actuellement disponibles sur Azure et le type de serveur est automatiquement sélectionné pour vous.
3. Cliquez sur **OK** pour enregistrer vos modifications.

Si vous utilisez l’option **Déployer à partir d’un téléchargement personnalisé** :

1. Assurez-vous que vous avez sélectionné le type de serveur en respectant les étapes qui précèdent. Cette option déterminera la façon dont le plug-in déploiera le serveur à partir de votre téléchargement personnalisé, car il doit être de la même famille que le type de serveur sélectionné.
2. Cochez la case **Déploiement depuis un téléchargement personnalisé**.
    Si vous souhaitez effectuer le téléchargement à partir de votre compte de stockage Azure, sélectionnez le compte de stockage dans la liste déroulante **Compte de stockage** (vous pouvez cliquer sur le lien **Comptes** pour modifier le contenu de la liste). Le champ **URL** est partiellement renseigné. Remplissez la partie restante de l’URL vers le fichier zip de téléchargement serveur (lors de l’utilisation du stockage Azure, les noms d’objets blob dans l’URL doivent être en minuscules). Si vous ne souhaitez pas utiliser le stockage Azure, sélectionnez **(aucun)** dans la liste déroulante **Compte de stockage**, puis saisissez l’URL de votre fichier zip de téléchargement serveur dans le champ **URL**. Le fichier zip contient un dossier enfant représentant votre répertoire d’installation de serveur d’applications. Par exemple, si vous utilisez un fichier zip pour Apache Tomcat 7.0.35, vous devez trouver à l’intérieur un dossier enfant représentant le répertoire d’installation, par exemple **apache-tomcat-7.0.35**. 
3. Spécifiez la valeur de la variable d’environnement du répertoire de base. Le cas échéant, elle prendra par défaut la valeur utilisée pour votre serveur d’applications local, mais vous pouvez spécifier une valeur différente si le serveur d’applications cloud est différent de votre serveur d’applications local. Vous devez toutefois être sûr que votre serveur d’applications cloud est de la même famille que le type de serveur sélectionné plus tôt.
    Si vous mettez à jour le fichier zip du serveur d’applications cloud, vous pouvez manuellement changer la configuration de répertoire de base ou l’adapter à la configuration locale (si vous avez également modifié votre serveur d’applications local).
4. Cliquez sur **OK** pour enregistrer vos modifications.

La logique sous-jacente pour laquelle les éléments apparaissent dans l’onglet **Serveur** de la page de propriété **Configuration du serveur** peut être personnalisée. Il s’agit d’une fonctionnalité avancée dont vous pourriez avoir besoin si vos besoins dépassent les valeurs par défaut ou si vous souhaitez ajouter d’autres serveurs. Pour personnaliser le programme, dans la boîte de dialogue **Serveur**, cliquez sur le lien **Personnaliser**. Cette opération ferme la page de propriété **Configuration du serveur** et ouvre le fichier **componentsets.xml** dans Eclipse. Vous pourrez par la suite le modifier si nécessaire pour étendre le modèle de configuration serveur. La documentation pour **componentsets.xml** est incluse dans le fichier **componentsets.xml** lui-même.

Si vous utilisez l’option **Déployer mon serveur local (chargement automatique vers un stockage cloud)** :

1. Cochez la case nommée **Déployer mon serveur local (chargement automatique dans un stockage cloud)**.
2. Avec la liste déroulante **Compte de stockage**, sélectionnez **(auto)**. Si vous spécifiez **(auto)** à ce stade, la boîte à outils Eclipse utilisera pour votre serveur le même compte de stockage que celui que vous sélectionnez pour votre déploiement dans la boîte de dialogue **Publier sur Azure**.
3. Cliquez sur **OK** pour enregistrer vos modifications.

Sélectionnez un chemin d’installation de serveur sur votre ordinateur dans la zone de texte **Chemin d’accès au serveur Local** si l’une des conditions suivantes est vraie :

* Vous souhaitez tester votre déploiement dans l’émulateur (s’applique uniquement à Windows).
* Vous souhaitez déployer votre serveur installé localement dans le cloud.
* Vous souhaitez utiliser un téléchargement de serveur personnalisé propre dans le cloud, auquel cas, vous devez également vérifier que l’option **Déployer mon serveur local (chargement automatique dans un stockage cloud)** est sélectionnée ci-dessus.

Si aucune des options qui précèdent ne s’applique à votre situation, la configuration du serveur local est facultative.

### <a name="applications-configuration"></a>Configuration des applications
Voici un exemple de la façon dont vous pouvez spécifier une application.

![][ic719512]

Cliquez sur **Ajouter** pour ajouter une autre application, ou sur **Supprimer** pour en supprimer une. Pour des raisons d’efficacité, si vous souhaitez utiliser un téléchargement comme source d’une application lors du déploiement dans le cloud, utilisez [Propriétés des composants](#components_properties) pour spécifier une URL, un compte de stockage, etc. 

Depuis la version d’avril 2014, vos applications sont automatiquement téléchargées dans le même compte de stockage (sous le conteneur **eclipsedeploy** ) que celui qui est sélectionné pour votre déploiement. La logique de démarrage de votre déploiement contient une étape qui télécharge d’abord ces applications depuis ce compte de stockage. Vous pouvez donc mettre à niveau les applications de votre déploiement sans avoir à recréer et redéployer l’intégralité du package, en téléchargeant manuellement les versions plus récentes de l’application directement dans ce compte de stockage (en utilisant le portail Azure, par exemple), en remplaçant les fichiers WAR téléchargés à l’origine par la boîte à outils. Initiez ensuite le recyclage de toutes les instances de rôle à l’aide du portail de gestion Azure, ou via les utilitaires de ligne de commande. (Le déclenchement du recyclage de rôle directement depuis la boîte à outils Eclipse n’est pas pris en charge actuellement.)

### <a name="notes-about-server-configuration"></a>Notes au sujet de la configuration du serveur
Les modifications apportées dans la page de propriété **Configuration du serveur** sont répercutées dans les éléments `<component>` du fichier package.xml.

Lorsque vous utilisez les options **Télécharger automatiquement...** ou **Déployer à partir du téléchargement...** pour le JDK ou le serveur d’applications, si vous procédez à la génération sur le cloud (et non l’émulateur de calcul) et êtes connecté au réseau, il se peut que vous receviez des messages de notification de génération sur la sortie de console, car le compilateur Ant vérifie la disponibilité du téléchargement :

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...` 

Si vous avez sélectionné l’option **Déployer à partir du téléchargement...** , l’avertissement suivant peut s’afficher, mais la compilation se poursuit :

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).` 

Cet avertissement est le seul élément qui indique que la disponibilité du téléchargement n’a pas été vérifiée. Par conséquent, pour une raison quelconque, si un déploiement échoue dans le cloud vérifiez si vous avez reçu cet avertissement.

Si vous souhaitez désactiver la vérification du téléchargement (par exemple, si vous pensez qu’elle ralentit inutilement la compilation), définissez l’attribut `verifydownloads` sur `false` dans l’élément `<windowsazurepackage>` de package.xml : 

`<windowsazurepackage verifydownloads="false" ...>` 

Si vous avez sélectionné l’option **Télécharger automatiquement...** , dans la fenêtre console, vous verrez des messages de compilation indiquant la progression du téléchargement toutes les 5 secondes, à chaque fois qu’un téléchargement est nécessaire.

<a name="ssl_offloading_properties"></a> 

### <a name="ssl-offloading-properties"></a>Propriétés de déchargement SSL
Ouvrez le menu contextuel correspondant au rôle dans le volet Explorateur de projets Eclipse, cliquez sur **Azure**, puis sur **Déchargement SSL**. 

![][ic719481]

Dans cette boîte de dialogue, vous pouvez activer le déchargement SSL, ce qui vous permet d’activer en toute simplicité la prise en charge du protocole sécurisé HTTPS (Hypertext Transfer Protocol Secure) dans votre déploiement Java sur Azure, sans avoir à configurer SSL dans votre serveur d’applications Java. Pour plus d’informations, consultez [Déchargement SSL][Déchargement SSL] et [Comment utiliser le déchargement SSL][Comment utiliser le déchargement SSL].

## <a name="see-also"></a>Voir aussi
[Kit de ressources Azure pour Eclipse][Kit de ressources Azure pour Eclipse]

[Installation du kit de ressources Azure pour Eclipse][Installation du kit de ressources Azure pour Eclipse]

[Création d’une application Hello World pour Azure dans Eclipse][Création d’une application Hello World pour Azure dans Eclipse]

[Propriétés du projet Azure][Propriétés du projet Azure]

[Liste du compte de stockage Azure][Liste du compte de stockage Azure]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure][Centre de développement Java pour Azure].

<!-- URL List -->

[Centre de développement Java pour Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[portail de gestion Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Propriétés du projet Azure]: http://go.microsoft.com/fwlink/?LinkID=699524
[Liste du compte de stockage Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[résumé de package com.microsoft.windowsazure.serviceruntime]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Création d’une application Hello World pour Azure dans Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Débogage d’une instance de rôle spécifique dans un déploiement à plusieurs instances]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Débogage des applications Azure dans Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Déploiements à grande échelle]: http://go.microsoft.com/fwlink/?LinkID=699536
[Utilisation de la mise en cache colocalisée]: http://go.microsoft.com/fwlink/?LinkID=699542
[Comment utiliser le déchargement SSL]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installation du kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Affinité de session]: http://go.microsoft.com/fwlink/?LinkID=699548
[Déchargement SSL]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->



<!--HONumber=Nov16_HO3-->


