<properties
	pageTitle="Création d'un service à l'aide d'une base de données SQL existante avec le serveur principal Mobile Services .NET | Microsoft Azure"
	description="Découvrez comment utiliser une base de données locale ou de cloud existante avec votre service mobile .NET"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>


# Création d'un service à l'aide d'une base de données SQL existante avec le serveur principal Mobile Services .NET

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Le backend .NET Mobile Services permet de tirer facilement parti des actifs existants pour la création d'un service mobile. L'un des scénarios les plus intéressants consiste à utiliser une base de données SQL existante (localement ou sur le cloud), qui peut déjà être utilisée par d'autres applications, pour rendre les données existantes disponibles pour les clients mobiles. Dans ce cas, il est nécessaire que le modèle de base de données (ou *schéma*) reste inchangé, afin que les solutions existantes continuent de fonctionner.

<a name="ExistingModel"></a>
## Exploration du modèle de base de données existant

Ce didacticiel fait appel à la base de données créée avec votre service mobile. Cependant, le modèle créé par défaut ne sera pas utilisé. Nous allons plutôt créer un modèle arbitraire qui représentera une application existante que vous possédez peut-être. Pour plus de détails sur la connexion à une base de données locale, consultez la rubrique [Connexion à un serveur SQL serveur local à partir d'un service mobile Azure au moyen de connexions hybrides](mobile-services-dotnet-backend-hybrid-connections-get-started.md).

1. Créez tout d'abord un projet de serveur Mobiles Services dans **Visual Studio 2013 Update 2** ou avec le projet de démarrage rapide, que vous pouvez télécharger sous l'onglet Mobile Services pour votre service dans le [portail Azure Classic](http://manage.windowsazure.com). Dans le cadre de ce didacticiel, nous partons du principe que le nom de votre projet de serveur est **ShoppingService**.

2. Créez un fichier **Customer.cs** dans le dossier **Models** et utilisez l'implémentation suivante. Vous devrez ajouter une référence d'assembly à **System.ComponentModel.DataAnnotations** dans votre projet.

        using System.Collections.Generic;
        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Customer
            {
                [Key]
                public int CustomerId { get; set; }

                public string Name { get; set; }

                public virtual ICollection<Order> Orders { get; set; }

            }
        }

3. Créez un fichier **Order.cs** dans le dossier **Models** et utilisez l'implémentation suivante :

        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Order
            {
                [Key]
                public int OrderId { get; set; }

                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                public int CustomerId { get; set; }

                public virtual Customer Customer { get; set; }

            }
        }

    Notez que ces deux classes partagent une *relation* : chaque élément **Order** est associé à un élément **Customer** et un élément **Customer** peut être associé à plusieurs éléments **Orders**. L'existence de relations est commune dans les modèles de données existants.

4. Créez un fichier **ExistingContext.cs** dans le dossier **Models** et implémentez-le de la façon suivante :

        using System.Data.Entity;

        namespace ShoppingService.Models
        {
            public class ExistingContext : DbContext
            {
                public ExistingContext()
                    : base("Name=MS_TableConnectionString")
                {
                }

                public DbSet<Customer> Customers { get; set; }

                public DbSet<Order> Orders { get; set; }

            }
        }

La structure ci-dessus imite un modèle Entity Framework existant, que vous utilisez peut-être déjà pour une application existante. Notez que le modèle ignore totalement Mobile Services à ce stade.

<a name="DTOs"></a>
## Création d'objets de transfert de données pour votre service mobile

Le modèle de données que vous souhaitez utiliser avec votre service mobile peut être arbitrairement complexe ; il peut contenir des centaines d'entités liées par différentes relations. Lors de la création d'une application mobile, il est généralement préférable de simplifier le modèle de données et d'éliminer les relations (ou de les gérer manuellement) afin de minimiser la charge utile envoyée depuis et vers l'application et le service. Dans cette section, nous allons créer un ensemble d'objets simplifiés (nommés « objets de transfert de données »), qui sont mappés aux données que vous possédez dans votre base de données, mais ne contiennent que l'ensemble minimal de propriétés nécessaire à votre application mobile.

1. Créez le fichier **MobileCustomer.cs** dans le dossier **DataObjects** de votre projet de service et utilisez l'implémentation suivante :

        using Microsoft.WindowsAzure.Mobile.Service;

        namespace ShoppingService.DataObjects
        {
            public class MobileCustomer : EntityData
            {
                public string Name { get; set; }
            }
        }

    Notez que cette classe est semblable à la classe **Customer** dans le modèle, à la différence près que la propriété de relation avec la classe **Order** est supprimée. Pour qu'un objet fonctionne correctement avec la synchronisation hors connexion de Mobile Services, un ensemble de *propriétés système* lui est nécessaire pour un accès concurrentiel optimiste. Vous noterez donc que l'objet de transfert de données hérite d'[**EntityData**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx), qui contient ces propriétés. La propriété **CustomerId** basée sur un entier, issue du modèle original, est remplacée par la propriété **Id** basée sur une chaîne depuis **EntityData**, qui sera l'**Id** utilisé par Mobile Services.

2. Créez le fichier **MobileOrder.cs** dans le dossier **DataObjects** de votre projet de service.

        using Microsoft.WindowsAzure.Mobile.Service;
        using Newtonsoft.Json;
        using System.ComponentModel.DataAnnotations;
        using System.ComponentModel.DataAnnotations.Schema;

        namespace ShoppingService.DataObjects
        {
            public class MobileOrder : EntityData
            {
                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                [NotMapped]
                public int CustomerId { get; set; }

                [Required]
                public string MobileCustomerId { get; set; }

                public string MobileCustomerName { get; set; }
            }
        }

    La propriété de relation **Customer** a été remplacée par le nom **Customer** et une propriété **MobileCustomerId** pouvant être utilisée pour modifier manuellement la relation sur le client. Pour l'instant, vous pouvez ignorer la propriété **CustomerId**, elle ne sera utilisée que par la suite.

3. Notez que, suite à l'ajout des propriétés système sur la classe de base **EntityData**, nos objets de transfert de données possèdent désormais plus de propriétés que les types de modèle. De toute évidence, nous avons besoin d'un endroit où stocker ces propriétés. Nous allons donc ajouter quelques colonnes supplémentaires à la base de données originale. Bien que cette action modifie la base de données, elle ne corrompt pas les applications existantes, car les modifications ne sont que des ajouts (nouvelles colonnes ajoutées au schéma). Pour cela, ajoutez les instructions suivantes au début des fichiers **Customer.cs** et **Order.cs** :

        using System.ComponentModel.DataAnnotations.Schema;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System;

4. Ensuite, ajoutez ces propriétés supplémentaires à chacune des classes :

        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        [Index]
        [TableColumn(TableColumnType.CreatedAt)]
        public DateTimeOffset? CreatedAt { get; set; }

        [TableColumn(TableColumnType.Deleted)]
        public bool Deleted { get; set; }

        [Index]
        [TableColumn(TableColumnType.Id)]
        [MaxLength(36)]
        public string Id { get; set; }

        [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
        [TableColumn(TableColumnType.UpdatedAt)]
        public DateTimeOffset? UpdatedAt { get; set; }

        [TableColumn(TableColumnType.Version)]
        [Timestamp]
        public byte[] Version { get; set; }

4. Les propriétés système ajoutées possèdent des comportements intégrés (par exemple, mise à jour automatique des paramètres Créé/mis à jour le) qui se produisent de manière transparente avec les opérations de base de données. Pour activer ces comportements, nous devons apporter une modification au fichier **ExistingContext.cs**. À son début, ajoutez ce qui suit :

        using System.Data.Entity.ModelConfiguration.Conventions;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.Linq;

5. Dans le corps du fichier **ExistingContext**, remplacez [**OnModelCreating**](http://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) :

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceTableColumn", (property, attributes) => attributes.Single().ColumnType.ToString()));

            base.OnModelCreating(modelBuilder);
        }

5. Remplissons la base de données avec des exemples de données. Ouvrez le fichier **WebApiConfig.cs**. Créez un nouveau [**IDatabaseInitializer**](http://msdn.microsoft.com/library/gg696323.aspx) et configurez-le dans la méthode **Register**, tel qu'indiqué ci-dessous.

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.Models;
        using System;
        using System.Collections.Generic;
        using System.Collections.ObjectModel;
        using System.Data.Entity;
        using System.Web.Http;

        namespace ShoppingService
        {
            public static class WebApiConfig
            {
                public static void Register()
                {
                    ConfigOptions options = new ConfigOptions();

                    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

                    Database.SetInitializer(new ExistingInitializer());
                }
            }

            public class ExistingInitializer : ClearDatabaseSchemaIfModelChanges<ExistingContext>
            {
                protected override void Seed(ExistingContext context)
                {
                    List<Order> orders = new List<Order>
                    {
                        new Order { OrderId = 10, Item = "Guitars", Quantity = 2, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 20, Item = "Drums", Quantity = 10, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 30, Item = "Tambourines", Quantity = 20, Id = Guid.NewGuid().ToString() }
                    };

                    List<Customer> customers = new List<Customer>
                    {
                        new Customer { CustomerId = 1, Name = "John", Orders = new Collection<Order> {
                            orders[0]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 2, Name = "Paul", Orders = new Collection<Order> {
                            orders[1]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 3, Name = "Ringo", Orders = new Collection<Order> {
                            orders[2]}, Id = Guid.NewGuid().ToString()},
                    };

                    foreach (Customer c in customers)
                    {
                        context.Customers.Add(c);
                    }

                    base.Seed(context);
                }
            }
        }

<a name="Mapping"></a>
## Établissement d'un mappage entre des objets de transfert de données et un modèle

Nous possédons désormais les types de modèle **Customer** et **Order** et les objets de transfert de données **MobileCustomer** et **MobileOrder**. Cependant, nous devons faire en sorte que le backend se transforme automatiquement entre les deux. Ici, Mobile Services s'appuie sur [**AutoMapper**](http://automapper.org/), un mappeur de relationnel objet, qui est déjà référencé dans le projet.

1. Ajoutez ce qui suit au début du fichier **WebApiConfig.cs** :

        using AutoMapper;
        using ShoppingService.DataObjects;

2. Pour définir le mappage, ajoutez ce qui suit à la méthode **Register** de la classe **WebApiConfig**.

        Mapper.Initialize(cfg =>
        {
            cfg.CreateMap<MobileOrder, Order>();
            cfg.CreateMap<MobileCustomer, Customer>();
            cfg.CreateMap<Order, MobileOrder>()
                .ForMember(dst => dst.MobileCustomerId, map => map.MapFrom(x => x.Customer.Id))
                .ForMember(dst => dst.MobileCustomerName, map => map.MapFrom(x => x.Customer.Name));
            cfg.CreateMap<Customer, MobileCustomer>();

        });

AutoMapper mappe désormais les objets les uns aux autres. Toutes les propriétés possédant les noms correspondants seront associées. Par exemple, **MobileOrder.CustomerId** sera automatiquement mappée à **Order.CustomerId**. Les mappages personnalisés peuvent être configurés comme dans l'exemple ci-dessus, dans lequel nous mappons la propriété **MobileCustomerName** à la propriété **Name** de la propriété de relation **Customer**.

<a name="DomainManager"></a>
## Implémentation d'une logique spécifique du domaine

L'étape suivante consiste à implémenter un [**MappedEntityDomainManager**](http://msdn.microsoft.com/library/dn643300.aspx), qui fait office de couche d'abstraction entre notre banque de données mappée et le contrôleur qui servira le trafic HTTP à partir de nos clients. Nous pourrons également écrire notre contrôleur dans la section suivante, uniquement en termes d'objets de transfert de données. Le **MappedEntityDomainManager** que nous ajoutons ici gérera la communication avec la banque de données originale, tout en permettant d'y implémenter une logique qui lui est spécifique.

1. Ajoutez un fichier **MobileCustomerDomainManager.cs** au dossier **Models** de votre projet. Collez l'implémentation suivante :

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Data.Entity;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileCustomerDomainManager : MappedEntityDomainManager<MobileCustomer, Customer>
            {
                private ExistingContext context;

                public MobileCustomerDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                public static int GetKey(string mobileCustomerId, DbSet<Customer> customers, HttpRequestMessage request)
                {
                    int customerId = customers
                       .Where(c => c.Id == mobileCustomerId)
                       .Select(c => c.CustomerId)
                       .FirstOrDefault();

                    if (customerId == 0)
                    {
                        throw new HttpResponseException(request.CreateNotFoundResponse());
                    }
                    return customerId;
                }

                protected override T GetKey<T>(string mobileCustomerId)
                {
                    return (T)(object)GetKey(mobileCustomerId, this.context.Customers, this.Request);
                }

                public override SingleResult<MobileCustomer> Lookup(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return LookupEntity(c => c.CustomerId == customerId);
                }

                public override async Task<MobileCustomer> InsertAsync(MobileCustomer mobileCustomer)
                {
                    return await base.InsertAsync(mobileCustomer);
                }

                public override async Task<MobileCustomer> UpdateAsync(string mobileCustomerId, Delta<MobileCustomer> patch)
                {
                    int customerId = GetKey<int>(mobileCustomerId);

                    Customer existingCustomer = await this.Context.Set<Customer>().FindAsync(customerId);
                    if (existingCustomer == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileCustomer existingCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);
                    patch.Patch(existingCustomerDTO);
                    Mapper.Map<MobileCustomer, Customer>(existingCustomerDTO, existingCustomer);

                    await this.SubmitChangesAsync();

                    MobileCustomer updatedCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);

                    return updatedCustomerDTO;
                }

                public override async Task<MobileCustomer> ReplaceAsync(string mobileCustomerId, MobileCustomer mobileCustomer)
                {
                    return await base.ReplaceAsync(mobileCustomerId, mobileCustomer);
                }

                public override async Task<bool> DeleteAsync(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return await DeleteItemAsync(customerId);
                }
            }
        }

    Une partie importante de cette classe est la méthode **GetKey**, dans laquelle nous indiquons comment localiser la propriété d'ID de l'objet dans le modèle de données original.

2. Ajoutez un fichier **MobileOrderDomainManager.cs** au dossier **Models** de votre projet :

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileOrderDomainManager : MappedEntityDomainManager<MobileOrder, Order>
            {
                private ExistingContext context;

                public MobileOrderDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                protected override T GetKey<T>(string mobileOrderId)
                {
                    int orderId = this.context.Orders
                        .Where(o => o.Id == mobileOrderId)
                        .Select(o => o.OrderId)
                        .FirstOrDefault();

                    if (orderId == 0)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }
                    return (T)(object)orderId;
                }

                public override SingleResult<MobileOrder> Lookup(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return LookupEntity(o => o.OrderId == orderId);
                }

                private async Task<Customer> VerifyMobileCustomer(string mobileCustomerId, string mobileCustomerName)
                {
                    int customerId = MobileCustomerDomainManager.GetKey(mobileCustomerId, this.context.Customers, this.Request);
                    Customer customer = await this.context.Customers.FindAsync(customerId);
                    if (customer == null)
                    {
                        throw new HttpResponseException(Request.CreateBadRequestResponse("Customer with name '{0}' was not found", mobileCustomerName));
                    }
                    return customer;
                }

                public override async Task<MobileOrder> InsertAsync(MobileOrder mobileOrder)
                {
                    Customer customer = await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);
                    mobileOrder.CustomerId = customer.CustomerId;
                    return await base.InsertAsync(mobileOrder);
                }

                public override async Task<MobileOrder> UpdateAsync(string mobileOrderId, Delta<MobileOrder> patch)
                {
                    Customer customer = await VerifyMobileCustomer(patch.GetEntity().MobileCustomerId, patch.GetEntity().MobileCustomerName);

                    int orderId = GetKey<int>(mobileOrderId);

                    Order existingOrder = await this.Context.Set<Order>().FindAsync(orderId);
                    if (existingOrder == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileOrder existingOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);
                    patch.Patch(existingOrderDTO);
                    Mapper.Map<MobileOrder, Order>(existingOrderDTO, existingOrder);

                    // This is required to map the right Id for the customer
                    existingOrder.CustomerId = customer.CustomerId;

                    await this.SubmitChangesAsync();

                    MobileOrder updatedOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);

                    return updatedOrderDTO;
                }

                public override async Task<MobileOrder> ReplaceAsync(string mobileOrderId, MobileOrder mobileOrder)
                {
                    await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);

                    return await base.ReplaceAsync(mobileOrderId, mobileOrder);
                }

                public override Task<bool> DeleteAsync(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return DeleteItemAsync(orderId);
                }
            }
        }

    Dans ce cas, les méthodes **InsertAsync** et **UpdateAsync** sont intéressantes : c'est là que nous appliquons la relation selon laquelle chaque classe **Order** doit être associée à une classe **Customer** valide. Dans **InsertAsync**, vous noterez que nous remplissons la propriété **MobileOrder.CustomerId**, qui effectue le mappage à la propriété **Order.CustomerId**. Nous obtenons cette valeur en observant l’élément **Client** avec l’élément **MobileOrder.MobileCustomerId** correspondant. En effet, par défaut, le client ne connaît que l'ID Mobile Services (**MobileOrder.MobileCustomerId**) de la classe **Customer**, qui est différent de sa clé primaire réelle, nécessaire pour définir la clé étrangère (**MobileOrder.CustomerId**) d'**Order** à **Customer**. Ceci n'est utilisé qu'en interne dans le service, pour faciliter l'opération d'insertion.

Nous pouvons désormais créer des contrôleurs pour exposer nos objets de transfert de données à nos clients.

<a name="Controller"></a>
## Implémentation d'une classe TableController à l'aide d'objets de transfert de données

1. Dans le dossier **Controllers**, ajoutez le fichier **MobileCustomerController.cs** :

        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileCustomerController : TableController<MobileCustomer>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    var context = new ExistingContext();
                    DomainManager = new MobileCustomerDomainManager(context, Request, Services);
                }

                public IQueryable<MobileCustomer> GetAllMobileCustomers()
                {
                    return Query();
                }

                public SingleResult<MobileCustomer> GetMobileCustomer(string id)
                {
                    return Lookup(id);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PatchAsync(string id, Delta<MobileCustomer> patch)
                {
                    return base.UpdateAsync(id, patch);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PostAsync(MobileCustomer item)
                {
                    return base.InsertAsync(item);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task DeleteAsync(string id)
                {
                    return base.DeleteAsync(id);
                }
            }
        }

    Vous noterez l'utilisation de l'attribut AuthorizeLevel pour restreindre l'accès public aux opérations Insérer/Mettre à jour/Supprimer sur le contrôleur. Dans le cadre de ce scénario, la liste Customers sera en lecture seule, mais nous autoriserons la création de nouveaux éléments Orders et leur association aux clients existants.

2. Dans le dossier **Controllers**, ajoutez le fichier **MobileOrderController.cs** :

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Description;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileOrderController : TableController<MobileOrder>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    ExistingContext context = new ExistingContext();
                    DomainManager = new MobileOrderDomainManager(context, Request, Services);
                }

                public IQueryable<MobileOrder> GetAllMobileOrders()
                {
                    return Query();
                }

                public SingleResult<MobileOrder> GetMobileOrder(string id)
                {
                    return Lookup(id);
                }

                public Task<MobileOrder> PatchMobileOrder(string id, Delta<MobileOrder> patch)
                {
                    return UpdateAsync(id, patch);
                }

                [ResponseType(typeof(MobileOrder))]
                public async Task<IHttpActionResult> PostMobileOrder(MobileOrder item)
                {
                    MobileOrder current = await InsertAsync(item);
                    return CreatedAtRoute("Tables", new { id = current.Id }, current);
                }

                public Task DeleteMobileOrder(string id)
                {
                    return DeleteAsync(id);
                }
            }
        }

3. Vous pouvez désormais exécuter votre service. Appuyez sur **F5** et utilisez le client de test intégré dans la page d'aide pour modifier les données.

Veuillez noter que les deux implémentations de contrôleur exercent une utilisation exclusive des objets de transfert de données **MobileCustomer** et **MobileOrder** et ignorent le modèle sous-jacent. Ces objets de transfert de données sont immédiatement sérialisés en JSON et peuvent être utilisés pour échanger des données avec le Kit de développement logiciel (SDK) client Mobile Services sur toutes les plateformes. Par exemple, en cas de génération d’une application Windows Store, le type côté client correspondant sera semblable à celui présenté ci-dessous. Le type sera analogue sur les autres plateformes client.

    using Microsoft.WindowsAzure.MobileServices;
    using System;

    namespace ShoppingClient
    {
        public class MobileCustomer
        {
            public string Id { get; set; }

            public string Name { get; set; }

            [CreatedAt]
            public DateTimeOffset? CreatedAt { get; set; }

            [UpdatedAt]
            public DateTimeOffset? UpdatedAt { get; set; }

            public bool Deleted { get; set; }

            [Version]
            public string Version { get; set; }

        }

    }

Vous pouvez ensuite créer l'application cliente pour accéder au service.

<!---HONumber=AcomDC_0727_2016-->