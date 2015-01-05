<properties urlDisplayName="custom dns storage" pageTitle="Configuration d'un nom de domaine pour les données d'objets blob d'un compte de stockage | Microsoft Azure" metaKeywords="" description="Learn how to configure a custom domain for accessing blob data in an Azure storage account." metaCanonical="" services="storage" documentationCenter="" title="Configure a custom domain name for blob data in a storage account" solutions="" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="tamram" />


# Configuration d'un nom de domaine personnalisé pour des données d'objets blob dans un compte de stockage Azure
Vous pouvez configurer un domaine personnalisé pour accéder à des données d'objets blob dans votre compte de stockage Azure. Le point de terminaison par défaut pour le service BLOB est https://<*mystorageaccount*>.blob.core.windows.net. Si vous mappez un domaine personnalisé et un sous-domaine, tel que **www.contoso.com** au point de terminaison du service BLOB pour votre compte de stockage, vos utilisateurs peuvent également accéder aux données d'objets blob de votre compte de stockage à l'aide de ce domaine. 


> [WACOM.NOTE]	Les procédures décrites ici s'appliquent aux comptes de stockage Azure. Pour les services cloud, consultez la page <a href = "/fr-fr/develop/net/common-tasks/custom-dns/">Configuration d'un nom de domaine personnalisé pour Azure Cloud Service</a> ; pour les sites web, consultez la page <a href="/fr-fr/develop/net/common-tasks/custom-dns-web-site/">Configuration d'un nom de domaine personnalisé pour un site Web Azure</a>. 

> [WACOM.NOTE]	Un compte de stockage premium ne peut pas être mappé sur un nom de domaine personnalisé. Consultez la page [Stockage Premium : stockage haute performance pour les charges de travail d'Azure Virtual Machine](http://go.microsoft.com/fwlink/?LinkId=521898) pour en savoir plus sur les comptes de stockage premium.

Il existe deux méthodes pour mapper votre domaine personnalisé au point de terminaison de service BLOB de votre compte de stockage. La plus simple consiste à créer un mappage d'enregistrement CNAME entre votre domaine personnalisé et son sous-domaine et le point de terminaison de service BLOB. Un enregistrement CNAME est une fonctionnalité DNS qui mappe un domaine source à un domaine cible. Dans cet exemple, le domaine source est votre domaine personnalisé et son sous-domaine. Notez que le sous-domaine est dans tous les cas indispensable. Le domaine cible est le point de terminaison de votre service BLOB.

Le processus consistant à mapper votre domaine personnalisé à votre point de terminaison de service BLOB peut entraîner un problème d'indisponibilité de courte durée du service au moment où vous enregistrez le domaine dans le portail de gestion Azure. Si votre domaine personnalisé prend en charge, à ce moment-là, une application visée par un contrat de niveau de service (SLA) interdisant toute interruption de service, vous pouvez utiliser le sous-domaine Azure **asverify** en tant qu'étape d'enregistrement intermédiaire pour permettre aux utilisateurs d'accéder à votre domaine pendant que vous procédez au mappage DNS.

Le tableau suivant contient des exemples d'URL permettant d'accéder aux données d'objets blob dans un compte de stockage nommé **mystorageaccount**. Le domaine personnalisé enregistré pour le compte de stockage est **www.contoso.com** :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody>
		<tr>
			<td style="width: 100px;"><strong>Type de ressource</strong></td>
			<td><strong>Formats d'URL</strong></td>
		</tr>
		<tr>
			<td>Compte de stockage</td>
			<td><strong>URL par défaut :</strong> http://mystorageaccount.blob.core.windows.net<br />
			<strong>URL de domaine personnalisée :</strong> http://www.contoso.com</td>
		</tr>
		<tr>
			<td>Blob</td>
			<td><strong>URL par défaut :</strong> http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<br /><strong>URL de domaine personnalisée :</strong>
			http://www.contoso.com/mycontainer/myblob</td>
		</tr>
		<tr>
			<td>Conteneur racine</td>
			<td><strong>URL par défaut :</strong> http://mystorageaccount.blob.core.windows.net/myblob 
			<br/>ou<br />
			http://mystorageaccount.blob.core.windows.net/$root/myblob<br />
			<strong>URL de domaine personnalisée :</strong> http://www.contoso.com/myblob
			<br/>ou<br />
			http://www.contoso.com/$root/myblob</td>
		</tr>
	</tbody>
</table>

Cette tâche vous explique les points suivants :



- <a href="#register-domain">Enregistrement d'un domaine personnalisé pour votre compte de stockage</a>
- <a href="#register-asverify">Enregistrement d'un domaine personnalisé pour votre compte de stockage à l'aide du sous-domaine intermédiaire asverify</a>
- <a name="#verify-subdomain">Vérification du mappage entre votre domaine personnalisé et le point de terminaison du service BLOB</a>

<h2><a name="register-domain"></a>Enregistrement d'un domaine personnalisé pour votre compte de stockage</h2>

Cette procédure permet d'enregistrer un domaine personnalisé si vous n'êtes pas inquiet à l'idée que les utilisateurs ne puissent pas accéder au domaine pendant un court instant, ou si votre domaine personnalisé n'héberge actuellement aucune application. 

Si, à ce moment-là, votre domaine personnalisé prend en charge une application sans interruption de service, utilisez la procédure décrite dans <a href="#register-asverify">Enregistrement d'un domaine personnalisé pour votre compte de stockage à l'aide du sous-domaine intermédiaire asverify</a>.

Pour configurer un nom de domaine personnalisé, vous devez créer un enregistrement CNAME auprès de votre bureau d'enregistrement de domaine. L'enregistrement CNAME spécifie un alias pour un nom de domaine ; dans cet exemple, il mappe l'adresse de votre domaine personnalisé au point de terminaison du service BLOB pour votre compte de stockage.

Chaque bureau d'enregistrement possède sa propre méthode de spécification des enregistrements CNAME, même si le fonctionnement général reste souvent similaire. Notez que de nombreuses formules de base pour l'enregistrement de domaine n'offrent pas la configuration DNS. Avant de pouvoir créer l'enregistrement CNAME, vous devrez peut-être mettre à niveau votre formule d'enregistrement de domaine. 

1.  Dans le portail de gestion Azure, accédez à l'onglet **Stockage**.

2.  Dans cet onglet **Stockage**, cliquez sur le nom du compte de stockage dont vous souhaitez mapper le domaine personnalisé.

3.  Cliquez sur l'onglet **Configurer**.

4.  Au bas de l'écran, cliquez sur **Gérer le domaine** pour afficher la boîte de dialogue **Gérer le domaine personnalisé**. Le texte figurant en haut de la boîte de dialogue contient des informations sur la manière de créer l'enregistrement CNAME. Pour cette procédure, ignorez le texte qui fait référence au sous-domaine **asverify**.

5.  Connectez-vous au site web du bureau d'enregistrement de votre DNS et accédez à la page de gestion DNS. Vous pourrez trouver ces informations dans une section telle que **Nom de domaine**, **DNS** ou **Nom Server Management**.

6.  Recherchez la section relative à la gestion des enregistrements CNAME. Pour cela, accédez à une page de paramètres avancés et recherchez les mots **CNAME**, **Alias** ou **Sous-domaines**.

7.  Créez un nouvel enregistrement CNAME et indiquez un alias de sous-domaine tel que **www** ou **photos**. Indiquez ensuite un nom d'hôte, à savoir votre point de terminaison de service BLOB, au format **mystorageaccount.blob.core.windows.net** (où **mystorageaccount** correspond au nom de votre compte de stockage). Le nom d'hôte à utiliser est indiqué dans le texte de la boîte de dialogue **Gérer le domaine personnalisé**.

8.  Après avoir créé un enregistrement CNAME, retournez dans la boîte de dialogue **Gérer le domaine personnalisé** et entrez le nom de votre domaine personnalisé, en incluant le sous-domaine, dans le champ **Nom du domaine personnalisé**. Par exemple, si votre domaine est **contoso.com** et que votre sous-domaine est **www**, entrez **www.contoso.com** ; si votre sous-domaine est **photos**, entrez **photos.contoso.com**. Notez que le sous-domaine est obligatoire.

9. Cliquez sur le bouton **Enregistrer** pour enregistrer votre domaine personnalisé. 

	Si l'enregistrement est réussi, le message **Votre domaine personnalisé est activé. **s'affiche. Les utilisateurs peuvent désormais afficher les données d'objet blob sur votre domaine personnalisé, à condition de disposer des autorisations nécessaires. 

<h2><a name="register-asverify"></a>Enregistrement d'un domaine personnalisé pour votre compte de stockage à l'aide du sous-domaine intermédiaire asverify</h2>

Cette procédure permet d'enregistrer votre domaine personnalisé si ce dernier prend en charge une application visée par un contrat SLA qui interdit toute interruption de service. En créant un enregistrement CNAME qui pointe du sous-domaine asverify.&lt;subdomain&gt;.&lt;customdomain&gt; vers le compte de stockage asverify.&lt;storageaccount&gt;.blob.core.windows.net, vous pouvez pré-enregistrer votre domaine auprès d'Azure. Vous pouvez ensuite créer un second enregistrement CNAME qui pointe du sous-domaine &lt;subdomain&gt;.&lt;customdomain&gt; vers le compte de stockage &lt;storageaccount&gt;.blob.core.windows.net, afin de diriger le trafic allant vers votre domaine personnalisé vers votre point de terminaison de service BLOB.

Le sous-domaine asverify est un sous-domaine spécial reconnu par Azure. En ajoutant le sous-domaine **asverify** à votre propre sous-domaine, vous permettez à Azure de reconnaître votre domaine personnalisé sans modifier l'enregistrement DNS du domaine. Une fois l'enregistrement DNS pour le domaine modifié, il sera mappé au point de terminaison de service BLOB sans aucune interruption de service.

1.  Dans le portail de gestion Azure, accédez à l'onglet **Stockage**.

2.  Dans cet onglet **Stockage**, cliquez sur le nom du compte de stockage dont vous souhaitez mapper le domaine personnalisé.

3.  Cliquez sur l'onglet **Configurer**.

4.  Au bas de l'écran, cliquez sur **Gérer le domaine** pour afficher la boîte de dialogue **Gérer le domaine personnalisé**. Le texte figurant en haut de la boîte de dialogue contient des informations sur la manière de créer l'enregistrement CNAME à l'aide du sous-domaine **asverify**.

5.  Connectez-vous au site web du bureau d'enregistrement de votre DNS et accédez à la page de gestion DNS. Vous pourrez trouver ces informations dans une section telle que **Nom de domaine**, **DNS** ou **Nom Server Management**.

6.  Recherchez la section relative à la gestion des enregistrements CNAME. Pour cela, accédez à une page de paramètres avancés et recherchez les mots **CNAME**, **Alias**     ou **Sous-domaines**.

7.  Créez un enregistrement CNAME et indiquez un alias de sous-domaine qui inclut le sous-domaine asverify. Par exemple, le sous-domaine que vous spécifiez sera au format **asverify.www** ou **asverify.photos**. Indiquez ensuite un nom d'hôte, à savoir votre point de terminaison de service BLOB, au format **asverify.mystorageaccount.blob.core.windows.net** (où **mystorageaccount** correspond au nom de votre compte de stockage). Le nom d'hôte à utiliser est indiqué dans le texte de la boîte de dialogue **Gérer le domaine personnalisé**.

8.  Après avoir créé un enregistrement CNAME, retournez dans la boîte de dialogue **Gérer le domaine personnalisé** et saisissez le nom de votre domaine personnalisé dans le champ **Nom du domaine personnalisé**. Par exemple, si votre domaine est **contoso.com** et que votre sous-domaine est **www**, entrez **www.contoso.com** ; si votre sous-domaine est **photos**, entrez **photos.contoso.com**. Notez que le sous-domaine est obligatoire.

9.	Activez la case à cocher **Avancé : utiliser le sous-domaine " asverify " pour pré-enregistrer mon domaine personnalisé**. 

10. Cliquez sur le bouton **Enregistrer** pour pré-enregistrer votre domaine personnalisé. 

	Si le pré-enregistrement est réussi, le message **Votre domaine personnalisé est activé. **s'affiche. 

11. À ce stade, votre domaine personnalisé a été vérifié par Azure, mais le trafic en direction de votre domaine n'est pas encore dirigé vers votre compte de stockage. Pour terminer le processus, retournez sur le site web du bureau d'enregistrement de votre DNS et créez un autre enregistrement CNAME qui mappe votre sous-domaine à votre point de terminaison de service BLOB. Par exemple, spécifiez le sous-domaine sous la forme **www** ou **photos** et le nom d'hôte sous la forme **mystorageaccount.blob.core.windows.net** (où **mystorageaccount** correspond au nom de votre compte de stockage). Cette étape marque la fin de l'enregistrement de votre domaine personnalisé.

12. Pour finir, vous pouvez supprimer l'enregistrement CNAME que vous avez créé à l'aide du sous-domaine **asverify**, car il n'a plus d'utilité.

Les utilisateurs peuvent désormais afficher les données d'objet blob sur votre domaine personnalisé, à condition de disposer des autorisations nécessaires.

<a name="verify-subdomain"> </a>

<h2>Vérification du mappage entre votre domaine personnalisé et le point de terminaison du service BLOB</h2>

Pour vérifier que votre domaine personnalisé est correctement mappé à votre point de terminaison de service BLOB, créez un objet blob dans un conteneur public au sein de votre compte de stockage. Dans un navigateur Web, utilisez un URI pour accéder à l'objet blob :

-   http://<*sousdomaine.domainepersonnalisé*>/<*monconteneur*>/<*monblob*>

Vous pouvez par exemple utiliser l'URI suivant pour accéder à un formulaire Web via un domaine personnalisé
**photos.contoso.com** qui renvoie à un objet blob dans votre conteneur
**myforms** (mes formulaires) :

-   http://photos.contoso.com/myforms/applicationform.htm

## Ressources supplémentaires

-   <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/gg680307.aspx">Mappage du contenu CDN à un domaine personnalisé</a>

<!--HONumber=35.1-->
