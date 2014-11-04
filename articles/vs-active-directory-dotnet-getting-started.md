<properties title="Prise en main de l'authentification Active Directory" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Mise en route][Mise en route]
> -   [Que s'est-il passé ?][Que s'est-il passé ?]

## Prise en main d'Azure Active Directory (Projets .NET)

##### Demander une authentification pour l'accès aux contrôleurs

Tous les contrôleurs de votre projet ont été dotés de l'attribut **Authorize**. Cet attribut permet de demander à l'utilisateur de s'authentifier avant d'accéder à ces contrôleurs. Pour autoriser un accès anonyme au contrôleur, cet attribut doit être supprimé du contrôleur. Pour définir plus précisément les autorisations, appliquez cet attribut à chaque méthode nécessitant une autorisation, au lieu de l'appliquer à la classe de contrôleur.

##### Ajouter des contrôles SignIn/SignOut

Pour ajouter des contrôles SignIn/SignOut à l'une de vos vues, vous pouvez utiliser la vue partielle \*\*\_LoginPartial.cshtml\*\*. Voici un exemple où cette fonctionnalité est ajoutée à la vue \*\*\_Layout.cshtml\*\* standard. (Notez le dernier élément de la classe div avec navbar-collapse) :

<pre class="prettyprint">
    &lt;!DOCTYPE html&gt; 
     &lt;html&gt; 
     &lt;head&gt; 
         &lt;meta charset=&quot;utf-8&quot; /&gt; 
        &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot;&gt; 
        &lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
        @Styles.Render(&quot;~/Content/css&quot;) 
        @Scripts.Render(&quot;~/bundles/modernizr&quot;) 
    &lt;/head&gt; 
    &lt;body&gt; 
        &lt;div class=&quot;navbar navbar-inverse navbar-fixed-top&quot;&gt; 
            &lt;div class=&quot;container&quot;&gt; 
                &lt;div class=&quot;navbar-header&quot;&gt; 
                    &lt;button type=&quot;button&quot; class=&quot;navbar-toggle&quot; data-toggle=&quot;collapse&quot; data-target=&quot;.navbar-collapse&quot;&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                    &lt;/button&gt; 
                    @Html.ActionLink(&quot;Application name&quot;, &quot;Index&quot;, &quot;Home&quot;, new { area = &quot;&quot; }, new { @class = &quot;navbar-brand&quot; }) 
                &lt;/div&gt; 
                &lt;div class=&quot;navbar-collapse collapse&quot;&gt; 
                    &lt;ul class=&quot;nav navbar-nav&quot;&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;Home&quot;, &quot;Index&quot;, &quot;Home&quot;)&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;About&quot;, &quot;About&quot;, &quot;Home&quot;)&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;Contact&quot;, &quot;Contact&quot;, &quot;Home&quot;)&lt;/li&gt; 
                    &lt;/ul&gt; 
                    @Html.Partial(&quot;_LoginPartial&quot;) 
                &lt;/div&gt; 
            &lt;/div&gt; 
        &lt;/div&gt; 
        &lt;div class=&quot;container body-content&quot;&gt; 
            @RenderBody() 
            &lt;hr /&gt; 
            &lt;footer&gt; 
                &lt;p&gt;© @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
            &lt;/footer&gt; 
        &lt;/div&gt; 
        @Scripts.Render(&quot;~/bundles/jquery&quot;) 
        @Scripts.Render(&quot;~/bundles/bootstrap&quot;) 
        @RenderSection(&quot;scripts&quot;, required: false) 
    &lt;/body&gt; 
    &lt;/html&gt;
</pre>

[En savoir plus sur Azure Active Directory][En savoir plus sur Azure Active Directory]

  [Mise en route]: /documentation/articles/vs-active-directory-dotnet-getting-started/
  [Que s'est-il passé ?]: /documentation/articles/vs-active-directory-dotnet-what-happened/
  [En savoir plus sur Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
