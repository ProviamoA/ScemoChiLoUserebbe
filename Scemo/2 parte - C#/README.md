# Collegare database 

su appsettings.json

```
"Server=.\\SQLEXPRESS;Database=Autovelox;User Id = sa; Password = Orbassano2003; MultipleActiveResultSets=true;TrustServerCertificate=True"
```


# Installare pacchetti 

Gestione pacchetti NuGet -> cerca quello che devi installare

```
Install-Package Microsoft.EntityFrameworkCore.SqlServer
Install-Package Microsoft.EntityFrameworkCore.Tools
```

su terminale per collegare db:

```
Scaffold-DbContext "Server=localhost\SQLEXPRESS;Database=BikeStores;Trusted_Connection=True;TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Data
```



# Su program.cs bisogna copiare riga 18 e 19 e metterla sotto scrivendo però il nome del db

![alt text](<image.png>)



# Quando dice crea un servizio intende di creare controller

Tasto destro su controller --> aggiungi --> controller --> controller MVC con visualizzazioni ecc --> scegliere la tabella che si vuole utilizzare

vedere immagine ![alt text](<funzione per vedere autovelox.png>)



# Aggiungere link su pagina , necessario creare prima il controller

Andare su Views/Shared _Layout.cshtml e copiare da come si vede nell'immagine poi modificare asp-controller con il nome del controller creato

![alt text](<link.png>)

# Per aggiungere bottoni del cerca

Views --> Mappe(nome della tabella) --> Index.cshtml

Scriverlo sotto h1

```
<p>
    <form asp-action="Index" method="get" class="d-flex gap-2">
        <input type="text" name="cercaComune" class="form-control" placeholder="Cerca per comune"/>
        <input type="text" name="cercaProvincia" class="form-control" placeholder="Cerca per provincia" />
        <input type="text" name="cercaRegione" class="form-control" placeholder="Cerca per regione" />
        <input type="submit" value="Cerca" class="btn btn-primary"/>
    </form>



    <form asp-action="Dettaglio" method="get" class="d-flex gap-2">
        <input type="number" name="cercaID" class="form-control" placeholder="Cerca per ID" />
        <input type="submit" value="Cerca" class="btn btn-primary" />
    </form>
</p>
```

# Per aggiungere funziona del cerca andare sul controller creato e aggiugnere questo sotto il GET

Controllers --> mappe (nome della tabella)

```
public async Task<IActionResult> Index(string cercaComune, string? cercaRegione, string? cercaProvincia)
{
    var autoveloxContext = _context.Mappa.Include(m => m.IdComuneNavigation).AsQueryable();

    if (!string.IsNullOrEmpty(cercaComune))
    {
        autoveloxContext = autoveloxContext.Where(m => m.IdComuneNavigation != null && m.IdComuneNavigation.Denominazione.Contains(cercaComune));
    }

    if(!string.IsNullOrEmpty(cercaProvincia))
    {
        autoveloxContext= autoveloxContext.Where(m=>m.IdComuneNavigation != null && m.IdComuneNavigation.IdProvinciaNavigation.Denominazione.Contains(cercaProvincia));
    }

    if (!string.IsNullOrEmpty(cercaRegione))
    {
        autoveloxContext= autoveloxContext.Where(m=>m.IdComuneNavigation != null && m.IdComuneNavigation.IdProvinciaNavigation.IdRegioneNavigation.Denominazione.Contains(cercaRegione));
    }
    return View(await autoveloxContext.ToListAsync());
}
```

# Cerca per ID metterlo sotto il codice di prima 

```
public async Task<IActionResult> Dettaglio(int? cercaID)
{
    if (cercaID == null)
        return NotFound();

    var autoveloxContext = await _context.Mappa.Include(m => m.IdComuneNavigation).FirstOrDefaultAsync(m => m.Id == cercaID);

    if (autoveloxContext == null)
        return NotFound();

    return View("Details", autoveloxContext);
}
```

# Per eliminare funzioni CRUD

Basta eliminare o commentare le parti dove vediamo scritto delete, create, edit

# Per fare migrazione per la parte di registrazione utente

Crea una migrazione

```
Add-Migration InitIdentity -Context ApplicationDbContext
```

Applica la migrazione

```
Update-Database -Context ApplicationDbContext
```

Ricordarsi di modificare da true a false nelle righe di codice seguente: (program.cs)

```
builder.Services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = false)
```

# per creare dashboard

creare controller vuoto e chiamarlo Dashboard

dentro il controller mettere questo:

```
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using System.Threading.Tasks;
using System.Linq;
using Autovelox5.Data; cambiare con nomeprogetto.data

[Authorize] // Permette solo ad utenti autenticati di accedere
public class DashboardController : Controller
{
    private readonly AutoveloxContext _context;

    public DashboardController(AutoveloxContext context)
    {
        _context = context;
    }

    public async Task<IActionResult> Index()
    {
        // Numero totale di autovelox
        int totaleAutovelox = await _context.Mappas.CountAsync();

        // Numero di autovelox per regione
        var autoveloxPerRegione = await _context.Mappas
            .Include(m => m.IdComuneNavigation)
                .ThenInclude(c => c.IdProvinciaNavigation)
                    .ThenInclude(p => p.IdRegioneNavigation)
            .GroupBy(m => m.IdComuneNavigation.IdProvinciaNavigation.IdRegioneNavigation.Denominazione)
            .Select(g => new {
                Regione = g.Key,
                Conteggio = g.Count()
            }).ToListAsync();

        ViewBag.Totale = totaleAutovelox;
        ViewBag.AutoveloxPerRegione = autoveloxPerRegione;

        return View();
    }
}
```

andare dentro su **View** e creare cartella nome dashboard , tasto destro --> aggiugni visualizzazione, **visualizzazoine razor vuota**

```
@{
    ViewData["Title"] = "Dashboard";
}

<h1>Dashboard Autovelox</h1>

<h3>Totale Autovelox in Italia: @ViewBag.Totale</h3>

<h4>Autovelox per Regione</h4>
<table class="table table-bordered">
    <thead>
        <tr>
            <th>Regione</th>
            <th>Numero Autovelox</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var item in ViewBag.AutoveloxPerRegione)
        {
            <tr>
                <td>@item.Regione</td>
                <td>@item.Conteggio</td>
            </tr>
        }
    </tbody>
</table>
```

aggiungere link su views --> shared --> layout.cshtml
