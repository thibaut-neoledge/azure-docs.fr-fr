<properties 
	pageTitle="Notes de publication pour Azure BizTalk Services | Microsoft Azure BizTalk Services" 
	description="" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="msftman" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="deonhe"/>

# Notes de publication pour Azure BizTalk Services

Les notes de version des Services Microsoft Azure BizTalk reprennent les problèmes connus dans cette version.

## Nouveautés de la mise à jour BizTalk Services de novembre
* Le chiffrement au repos peut être activé dans le portail BizTalk Services. Consultez [Activer le chiffrement au repos dans le portail BizTalk Services](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## Historique de mise à jour

### Mise à jour d’octobre

* Les comptes de société sont pris en charge :  
 * **Scénario** : vous avez enregistré un déploiement de BizTalk Service en utilisant un compte Microsoft (tel que user@live.com). Dans ce scénario, seuls les utilisateurs Microsoft Account peuvent gérer le service BizTalk avec le portail BizTalk Services. Il est impossible d’utiliser un compte professionnel.  
 * **Scénario** : vous avez enregistré un déploiement BizTalk Service en utilisant un compte professionnel dans un répertoire Azure Active Directory (au même titre que user@fabrikam.com ou user@contoso.com). Dans ce scénario, seuls les utilisateurs Azure Active Directory appartenant à la même organisation peuvent gérer le service BizTalk à l’aide du portail BizTalk Services. Il est impossible d’utiliser un compte Microsoft.  
* Lorsque vous créez un service BizTalk dans le portail Azure classique, vous êtes automatiquement inscrit dans le portail BizTalk Services.
 * **Scénario** : vous vous connectez au portail Azure Classic, créez un service BizTalk, puis sélectionnez **Gérer** pour la première fois. Lorsque le portail BizTalk Services s’ouvre, le service BizTalk est automatiquement inscrit et est prêt pour vos déploiements. Consultez [Enregistrement et mise à niveau d’un déploiement de service BizTalk sur le portail BizTalk Services](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### Mise à jour du 14 août
* Découplage accord et pont : les accords et les ponts entre partenaires commerciaux sont désormais découplés dans le portail BizTalk Services. Vous pouvez maintenant créer des contrats et des ponts séparément, et, au moment de l’exécution, les ponts sont associés à un accord selon les valeurs présentes dans le message EDI. Consultez [Création d’accords dans Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689908.aspx), [Création d’un pont EDI à l’aide du portail Services BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [Création d’un pont AS2 à l’aide du portail BizTalk Services](https://msdn.microsoft.com/library/azure/dn793993.aspx) et [Association des ponts à l’exécution des contrats ?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* L’option permettant de créer des modèles pour les accords est abandonnée.  
* Pour le côté envoi de l’accord, vous pouvez maintenant spécifier différents ensembles de délimiteurs pour chaque schéma. Cette configuration est spécifiée dans les paramètres de protocole pour le côté envoi de l’accord. Pour plus d’informations, consultez [Créer un accord X12 dans Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx) et [Créer un accord EDIFACT dans Azure BizTalk Services](https://msdn.microsoft.com/library/azure/dn606267.aspx). Deux nouvelles entités sont également ajoutées à l’API TPM OM dans le même but. Consultez la page [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) et [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Les constructions XSD standard, et notamment les types dérivés, sont désormais prises en charge. Consultez la page [Utiliser les constructions XSD standard dans vos mappages](https://msdn.microsoft.com/library/azure/dn793987.aspx) et [Utiliser les types dérivés dans les scénarios de mappage et des exemples](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 prend en charge les nouveaux algorithmes MIC pour la signature des messages et de nouveaux algorithmes de chiffrement. Voir [Créer un accord AS2 dans Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689890.aspx).
## Problèmes connus

### Problèmes de connectivité après la mise à jour du portail BizTalk Services

  Si le portail BizTalk Services est ouvert pendant une mise à jour de Services BizTalk en vue du déploiement de modifications dans le service, vous pouvez rencontrer des problèmes de connectivité avec le portail BizTalk Services. Pour résoudre ce problème, vous pouvez redémarrer le navigateur, supprimer le cache du navigateur ou démarrer le portail en mode privé.

### L’IDE Visual Studio ne peut pas localiser l’artefact si vous cliquez sur une erreur ou sur un avertissement dans un projet BizTalk Services
Installez Visual Studio 2012 Update 3 RC 1 pour résoudre le problème.

### Référence de projet de liaison personnalisée
Considérez les situations suivantes avec un projet BizTalk Services dans une solution Visual Studio :
* Dans la même solution Visual Studio, il existe un projet BizTalk Services et un projet de liaison personnalisée. Le projet BizTalk Services comporte une référence à ce fichier de projet de liaison personnalisée. 
* Le projet BizTalk Services comporte une référence à une DLL de liaison/comportement personnalisée.

Vous « générez » la solution dans Visual Studio avec succès. Ensuite, vous « régénérez » ou « Nettoyez » la solution. Après cela, lorsque vous réexécutez une régénération ou un nettoyage, l’erreur qui suit se produit : Impossible de copier le fichier <Path to DLL> dans « bin\\Debug\\FileName.dll ». Le processus ne peut pas accéder au fichier « bin\\Debug\\FileName.dll », car il est utilisé par un autre processus.

#### Solution de contournement
* Si [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) est installé, vous disposez des deux options suivantes :

  * Redémarrer Visual Studio ou

  * Redémarrer la solution. Effectuez ensuite une seule compilation de la solution.

* Si [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) n’est pas installé, ouvrez le Gestionnaire des tâches, cliquez sur l’onglet Processus et cliquez sur le processus MSBuild.exe, puis cliquez sur le bouton Terminer le processus.

### L’acheminement vers les points de terminaison BasicHttpRelay n’est pas pris en charge à partir de ponts ou du portail BizTalk Services si les caractères non imprimables sont utilisés en tant qu’en-têtes HTTP

Si vous utilisez des caractères non imprimables dans le cadre des propriétés promues des messages, ces messages ne peuvent pas être acheminés vers les destinations de relais qui utilisent la liaison BasicHttpRelay. En outre, les propriétés promues disponibles dans le cadre du suivi sont codées URL pour les objets blob et non codés pour les destinations.

### MDN est envoyé de manière asynchrone même si l’option Envoi MDN asynchrone est désactivée  
Imaginez le scénario : si vous sélectionnez la case à cocher **Envoyer MDN asynchrone** et spécifiez une URL pour envoyer le MDN asynchrone, puis désélectionnez de nouveau la case à cocher **Envoyer MDN asynchrone**. Le MDN est toujours envoyé à l’URL spécifiée, même si l’option d’envoi MDN asynchrone n’est pas sélectionnée. Pour résoudre ce problème, vous devez effacer l’URL spécifiée avant la désactivation de la case à cocher **Envoyer MDN asynchrone**, puis déployer l’accord AS2.

### Les espaces situés au-delà d’un échange valide génèrent un message vide à envoyer au point de terminaison d’interruption  
S’il existe des espaces au-delà d’un segment IEA, le désassembleur les traite comme s’il s’agissait de la fin de l’échange actuel et examine l’ensemble d’espaces suivant comme un nouveau message. Comme il ne s’agit pas d’un échange valide, vous pouvez constater qu’un message de réussite est envoyé à la destination d’itinéraire et un message vide est envoyé au point de terminaison d’interruption.
### Suivi dans le portail Azure BizTalk Services  
Les événements de suivi sont enregistrés jusqu’au traitement de messages EDI et une corrélation. Si un message échoue en dehors de la phase de protocole, le suivi sera affiché comme réussi. Dans ce cas, reportez-vous à la section journal de la colonne **Détails** dans **Suivi** pour les détails de l’erreur. Les paramètres de réception et d’envoi X 12 ([Créer un accord X12 dans Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx)) fournissent des informations sur l’étape de protocole.

### Accord de mise à jour  
En cas de configuration d’un accord, le portail BizTalk Services vous permet de modifier le qualificateur d’une identité. Cela peut entraîner des propriétés incohérentes. Par exemple, il existe un accord utilisant ZZ:1234567 et le qualificateur ZZ:7654321. Dans les paramètres de profil du portail BizTalk Services, vous remplacez ZZ:1234567 par 01:ChangedValue. Ouvrez l’accord et 01:ChangedValue s’affiche à la place de ZZ:1234567. Pour modifier le qualificateur d’une identité, supprimez l’accord, mettez à jour **identités** dans le profil du partenaire, puis recréez l’accord.
> AZURE.WARNING Ce comportement a un impact sur X12 et AS2.

### Pièces jointes AS2  
Les pièces jointes aux messages AS2 ne sont pas prises en charge dans les envois ou la réception. Plus précisément, les pièces jointes sont ignorées silencieusement et le corps du message est traité comme un message AS2 classique.
### Ressources : mémoriser le chemin d’accès  
Lorsque vous ajoutez des **ressources**, la fenêtre de dialogue peut ne pas conserver le chemin d’accès utilisé précédemment pour ajouter une ressource. Pour vous souvenir du chemin d’accès utilisé précédemment, essayez d’ajouter le site web du portail BizTalk Services aux **Sites de confiance** dans Internet Explorer.
### Si vous renommez le nom de l’entité d’un pont et fermez le projet sans enregistrer les modifications, la réouverture de l’entité génère une erreur
Imaginez un scénario dans l’ordre suivant :
* Ajoutez un pont (par exemple, un pont unidirectionnel XML) à un projet BizTalk Service  

* Renommez le pont en spécifiant une valeur pour la propriété Nom d’entité. Cette opération renomme le fichier .bridgeconfig associé au nom que vous avez spécifié.

* Fermez le fichier .bcs (en fermant l’onglet dans Visual Studio) sans enregistrer les modifications.

* Ouvrez de nouveau le fichier .bcs à partir de l’Explorateur de solutions. Vous remarquerez que lorsque le fichier .bridgeconfig associé porte le nouveau nom que vous avez spécifié, le nom d’entité figurant sur l’aire de conception reste l’ancien nom. Si vous essayez d’ouvrir la configuration du pont en double-cliquant sur le composant de pont, vous obtenez l’erreur suivante : l’entité « <old name> » associée au fichier « <old name>.bridgeconfig » n’existe pas. Pour éviter de rencontrer ce scénario, veillez à enregistrer les modifications après avoir renommé les entités dans un projet BizTalk Services.
### Le projet de BizTalk Service est généré avec succès même si un artefact a été exclu d’un projet Visual Studio
Imaginez un scénario dans lequel vous ajoutez un artefact (par exemple, un fichier XSD) à un projet BizTalk Service, incluez cet artefact dans la configuration du pont (par exemple, en le spécifiant comme un type de message de demande), puis excluez-le du projet Visual Studio. Dans un tel cas, la compilation du projet ne génère pas d’erreur aussi longtemps que l’artefact supprimé est disponible sur le disque, à l’endroit où il a été inclus dans le projet Visual Studio.
### Le projet BizTalk Service ne vérifie pas la disponibilité de schéma lors de la configuration des ponts
Dans un projet BizTalk Services, si un schéma ajouté au projet importe un autre schéma, le projet BizTalk Service ne vérifie pas si le schéma importé est ajouté au projet. Si vous essayez de créer un tel projet, vous n’obtenez pas d’erreur de configuration.
### Le message de réponse d’un pont de requête-réponse XML est toujours rédigé avec le jeu de caractères UTF-8
Dans cette version, le jeu de caractères du message de réponse d’un pont demande-réponse XML est toujours défini sur UTF-8.
### Types de données définis par l’utilisateur
Les adaptateurs BizTalk Adapter Pack présents dans la fonctionnalité BizTalk Adapter Service peuvent utiliser des types de données définis par l’utilisateur pour les opérations d’adaptateur. Lorsque vous utilisez des types de données définis par l’utilisateur, copiez les fichiers (.dll) sur le lecteur:\\Program Files\\Microsoft Service\\BAServiceRuntime\\bin\\ d’adaptateur BizTalk ou dans Global Assembly Cache (GAC) sur le serveur qui héberge le service de l’adaptateur BizTalk. Dans le cas contraire, l’erreur suivante peut se produire sur le client :
```<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <faultcode>s:Client</faultcode>
  <faultstring xml:lang="fr-FR">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
  <detail>
    <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
    </AFConnectRuntimeFault>
  </detail>
</s:Fault> ```  
> [AZURE.IMPORTANT] Pour installer un fichier dans Global Assembly Cache, il est recommandé d’utiliser GACUtil.exe. Les documents GACUtil.exe expliquent comment utiliser cet outil et les options de ligne de commande Visual Studio.

### Redémarrage du site web BizTalk Adapter Service
L’installation de l’**exécution BizTalk Adapter Service*** crée le site web **BizTalk Adapter Service** dans IIS, qui contient l’application **BAService**. L’application **BAService** en interne utilise une liaison de relais pour étendre la portée du point de terminaison de service local sur le cloud. Pour un service hébergé localement, le point de terminaison de relais correspondant est inscrit sur le bus de Service uniquement lorsque le service local démarre.

Si vous arrêtez et démarrez une application, la configuration de démarrage automatique de l’application n’est pas respectée. Alors, lorsque **BAService** est arrêté, vous devez toujours redémarrer le site web **Service d’adaptateur BizTalk** à la place. Ne démarrez pas ou n’arrêtez pas l’application **BAService**.
### Les caractères spéciaux ne doivent pas être utilisés pour les noms d’adresse et d’entité des composants métier.
Vous ne devez pas utiliser de caractères spéciaux avec les nom et adresse d’entité et des composants métier. Si vous procédez ainsi, vous obtiendrez une erreur lors du déploiement du projet BizTalk Services. Pour certains caractères tels que « % », le site web de Service d’adaptateur BizTalk peut entrer en état d’arrêt et vous devrez alors démarrer manuellement.
### Test de mappage avec propriété de contexte Get
Si une transformation contient une opération de mappage **Obtenir une propriété de contexte**, l’opération **Tester le mappage** échoue. Pour contourner provisoirement la solution, remplacez l’opération de mappage **Obtenir la propriété de contexte** par une opération de mappage par concaténation de chaîne contenant des données fictives. Cette opération renseignera le schéma cible et vous permettra de tester une autre fonctionnalité de transformation.
### La propriété Tester le mappage ne s’affiche pas
Les propriétés **Tester le mappage** ne s’affichent pas dans Visual Studio. Cela peut se produire si la fenêtre **Propriétés** et l’**Explorateur de solutions** ne sont pas ancrées simultanément. Pour résoudre ce problème, ancrez les fenêtres **Propriétés** et l’**Explorateur de solutions**.
### La liste déroulante Reformat DateTime est affichée en grisé
Une opération de mappage de reformatage de DateTime est ajoutée à l’aire de conception et configurée, la liste déroulante Format peut être grisée. Cela peut se produire si l’affichage de l’ordinateur **Moyen-125 %** ou **Plus grand – 150 %**. Pour résoudre l’opération, définissez l’affichage sur **Plus petit - 100 % (par défaut)** à l’aide de la procédure ci-dessous :
1. Ouvrez le **Panneau de configuration** et cliquez sur **Apparence et personnalisation**.
2. Cliquez sur **Affichage**.
3. Cliquez sur **Plus petite-100 % (par défaut)**, puis cliquez sur **Appliquer**.

La liste déroulante **Format** devrait désormais fonctionner comme prévu.
### Duplication des accords dans le portail BizTalk Services
Examinez le scénario suivant :
1. Création d’un accord à l’aide de l’API OM de gestion des partenaires commerciaux.
2. Ouvrez l’accord dans le portail BizTalk Services dans deux onglets différents.
3. Déployez l’accord depuis les deux onglets.
4. Par conséquent, les deux accords sont alors déployés, ce qui se traduit par des entrées en double dans le portail BizTalk Services

**Solution de contournement**. Ouvrez l’un des accords dupliqués sur le portail BizTalk Services et annulez le déploiement.

### Les ponts n’utilisent pas de certificat mis à jour même après qu’un certificat a été mis à jour dans le magasin d’artefacts
Examinez les scénarios suivants :

**Scénario 1 : à l’aide de certificats basés sur l’empreinte numérique pour sécuriser le transfert de message d’un pont vers un point de terminaison** Envisagez un scénario dans lequel vous utilisez les certificats basés sur l’empreinte numérique dans votre projet de BizTalk Service. Vous pouvez mettre à jour le certificat dans le portail BizTalk Services avec le même nom, mais avec une autre empreinte numérique, mais ne mettez pas le projet BizTalk Service à jour en conséquence. Dans un tel scénario, le pont peut continuer de traiter les messages, car les anciennes données de certificat se trouvent toujours dans la mémoire cache du canal. Après cela, le traitement du message échoue.

**Solution de contournement** : mettez à jour le certificat dans le projet BizTalk Service et redéployez le projet.

**Scénario 2 : en utilisant des procédures basées sur des noms pour identifier les certificats pour sécuriser le transfert de message depuis un pont vers un point de terminaison de service**

Envisagez un scénario dans lequel vous utilisez des procédures basées sur le nom pour identifier des certificats dans votre projet BizTalk Service. Il est possible de mettre à jour le certificat dans le portail BizTalk Services, mais ne mettez pas le projet BizTalk Service à jour en conséquence. Dans un tel scénario, le pont peut continuer de traiter les messages, car les anciennes données de certificat se trouvent toujours dans la mémoire cache du canal. Après cela, le traitement du message échoue.

**Solution de contournement** : mettez à jour le certificat dans le projet BizTalk Service et redéployez le projet.

### Les ponts continuent de traiter les messages même lorsque la base de données SQL est hors connexion
Les ponts BizTalk Services continuent de traiter les messages pendant un certain temps, même si Azure SQL Database Microsoft (qui stocke les informations en cours d’exécution telles que les artefacts déployés et les pipelines), est hors connexion, et ce, parce que les Services BizTalk utilisent les artefacts mis en cache et la configuration du pont. Si vous ne souhaitez pas que les ponts traitent les messages lorsque la base de données SQL est hors connexion, vous pouvez utiliser les applets de commande PowerShell BizTalk Services pour arrêter ou suspendre le service BizTalk. Consultez [Exemple Azure BizTalk Service Management](http://go.microsoft.com/fwlink/p/?LinkID=329019) pour que les applets de commande Windows PowerShell gèrent les opérations.
### La lecture d’un message XML au sein d’un composant de code personnalisé d’un pont inclut un caractère BOM supplémentaire
Imaginez un scénario dans lequel vous souhaitez lire un message XML dans le code personnalisé d’un pont. Si vous utilisez l’API .NET System.Text.Encoding.UTF8.GetString(bytes) un caractère BOM supplémentaire est inclus dans la sortie au début du message. Par conséquent, si vous ne souhaitez pas que la sortie inclue le caractère BOM supplémentaire, vous devez utiliser ```System.IO.StreamReader().ReadToEnd()```.
### L’envoi de messages à un pont à l’aide de WCF n’évolue pas
Le message envoyé à un pont à l’aide de WCF n’est pas mis à l’échelle. Il est préférable d’utiliser HttpWebRequest si vous souhaitez un client évolutif.
### Mise à niveau : Erreur de fournisseur de jetons après la mise à niveau de la version préliminaire de BizTalk Services vers disponibilité générale
Il existe un accord EDI ou AS2 avec des lots actifs. Lorsque le BizTalk Service est mis à niveau de la version préliminaire vers la disponibilité générale, les événements suivants peuvent se produire :
* Erreur : Le fournisseur de jetons n’a pas pu fournir de jeton de sécurité. Le fournisseur de jeton a renvoyé le message : le nom distant ne peut pas être résolu.

* Les tâches de traitement par lots sont annulées.

**Solution de contournement** : une fois le service BizTalk mis à jour vers GA (General Availability), redéployez l’accord.

### MISE À NIVEAU : la boîte à outils affiche les anciennes icônes des ponts après la mise à niveau du kit de développement logiciel BizTalk Services.
Une fois que vous avez mis à niveau une version antérieure du kit de développement logiciel BizTalk Services, dont les anciennes icônes représentaient les ponts, la boîte à outils continue d’afficher les anciennes icônes pour les ponts. Toutefois, si vous ajoutez un pont à l’aire de conception du projet BizTalk Service, la surface affiche la nouvelle icône.

**Solution de contournement**. Vous pouvez contourner ce problème en supprimant les fichiers .tbd sous <system drive> : \\Users < utilisateur > \\AppData\\Local\\Microsoft\\VisualStudio\\11.0.

### MISE À NIVEAU : la mise à jour du portail BizTalk de la version préliminaire vers la disponibilité générale peut générer un message d’erreur indiquant que la fonctionnalité EDI n’est pas disponible
Si vous êtes connecté au portail de Services BizTalk lors de la mise à jour des services BizTalk de la version préliminaire vers la disponibilité générale, vous pouvez obtenir l’erreur suivante sur le portail :

Cette fonctionnalité n’est pas disponible dans le cadre de cette édition des Services Microsoft Azure BizTalk. Pour utiliser ces fonctionnalités de bascule vers une édition adéquate.

**Résolution** : déconnectez-vous du portail, fermez et ouvrez le navigateur, puis connectez-vous au portail.
### Mise à niveau : les nouvelles données de suivi ne s’affichent pas une fois que BizTalk Services est mis à niveau vers la disponibilité générale
Prenons un scénario où vous avez un pont XML déployé sur un abonnement BizTalk Services en version préliminaire. Vous envoyez des messages au pont et les données de suivi correspondantes sont disponibles sur le portail BizTalk Services. Maintenant, si les bits de runtime de portail BizTalk Services et BizTalk Services sont mis à niveau vers la disponibilité générale (GA) et si vous envoyez un message au point de terminaison de pont que vous avez déployé précédemment, les données de suivi n’apparaissent pas pour les messages envoyés après la mise à niveau.

### Pipelines et ponts
Dans ce document, les termes « pipeline » et « pont » sont utilisés indifféremment. Les deux termes désignent la même chose, c’est-à-dire une unité de traitement de message déployée sur BizTalk Services.

### Concepts  

[BizTalk Services](https://msdn.microsoft.com/library/azure/hh689864.aspx)

<!---HONumber=AcomDC_0302_2016-->