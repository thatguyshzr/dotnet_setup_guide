# Setting up a C# project
-------------------------------------------------------------
New project > ASP.NET Core Web API C#

## Files to create:
#### __Model__ folder
Create a file ```AppName.cs``` and ```AppDBContext.cs``` in __Model__ folder.
* Right click on **Model** folder > Add > Class > save as ```AppName.cs```
* Right click on **Model** folder > Add > Class > save as ```AppDBContext.cs```

```AppDBContext.cs``` name shouldnt be changed.
#### __Controllers__ folder
Create a file ```AppNameController.cs``` in __Controller__ folder.
* Right click on **Controller** folder > Add > Controller
* Common > API > API Controller with read/write actions
* API Controller with read/write actions > save as ```AppNameController.cs```

File name is for user understanding.

#### __DAO__ folder
Create a file ```AppNameDAO.cs``` in __DAO__ folder.
* Right click on **DAO** folder > Add > Class > Interface > save as ```AppNameDAO.cs```

File name is for user understanding.

Change ```interface AppNameDAO``` to  ```public interface AppNameDAO```

#### __Repository__ folder
Create a file ```AppNameRepository.cs``` in __Repository__ folder.
* Right click on **Repository** folder > Add > Class > Interface > save as ```AppNameRepository.cs```
--------------------------------------------------------------------
## File structure:
```
project_name
    - Connected Services    (ignore)
    - Dependencies          (ignore)
    - Properties            (ignore)
    - Controllers
        - AppNameController
    - Model
        - AppDBContext.cs
        - AppName.cs
    - Repository
        - AppNameRepository.cs
    - appsettings.json
    - Program.cs            (ignore)
    - Startup.cs
```


## File Explaination
### Model/AppName.cs
Define columns in database
**public int id { get; set; }**
```public``` ```dtype``` ```column_name_from_db``` ```{ to_get_data; to_set_data; } ```

#### Model/AppDBContext.cs
Communicates with DB
- **public class AppDBContext:DbContext**
Inherits DbContext from ```Microsoft.EntityFrameworkCore;``` package.

- **public DbSet<AppName>table_name { get; set; }**
```DbSet```
```<AppName>``` class name defined in Model/AppName.cs
```table_name``` table name in database
```{ get; set; }``` { to_get_data; to_set_data; }

#### DAO/AppNameDAO.cs
Define functions name. We write the logic in AppNameRepository.cs
- **public string AddData(AppName app_name_variable);**
```string``` if adding data to database, nothing is returned. Since we have to return something from the function, return string.
```AddData``` Function we'll write logic for in AppNameRepository.cs
```(AppName app_name_variable)``` creating an object from our main class, AppName

- **public List<AppName> GetData();**
```List<AppName>``` retrieving data from DB, recieve list of JSON

- **public string UpdateData(User user);**
```string``` updating data, same like adding data.


#### Repository/AppNameRepository.cs
- **public class AppNameRepository : AppNameDAO**
```: AppNameDAO``` inherit function names defined in AppNameDAO

- **private readonly AppDBContext db_variable_name;**
```private``` private so other class dont read the DB.
```AppDBContext db_variable_name;``` define db variable to use.

- **public AppNameRepository(AppDBContext db_variable_name)**
To use the private DB


#### Controllers/AppNameController.cs
- Import AppNameDAO privately and use it
```
private readonly AppNameDAO appnameDAO;
public UserController(AppNameDAO appnameDAO)
{
    this.appnameDAO = appnameDAO;
}
```

- get data from DB
```
[HttpGet]
public IActionResult GetData()
{
    return Ok(userDAO.GetData());
}
```
```[HttpGet]``` get method
```IActionResult``` makes sure to return something
```GetData()``` function name defined in DAO and Repository
```return Ok(AppNameDAO.GetData());``` returns Ok status with whatever returned in the function
```[HttpPost]``` post method
```[HttpPut]``` to update data


#### appsettings.json
Set up connection.
```
"ConnectionStrings": {
    "DBNameVariable": "Data Source=localhost;Initial Catalog= DBName;
                        User Id= user_name;Password= password",
  }
  ```


 #### Startup.cs
 Add this to ```public void ConfigureServices(IServiceCollection services) {}```
 ```
 services.AddControllers();
services.AddTransient<UserDAO, UserRepository>();
services.AddDbContext<AppDBContext>(options => options.UseSqlServer(Configuration.GetConnectionString("DBNameVariable")));
```
```DBNameVariable``` should be same as in **appsettings.json** > **"ConnectionStrings"**


----------------------------------------------------------------
# Setting up (basic) DB
- localhost > Databases > AppStore > Tables > right click > New > Table

|Column Name | Data Type | Allow Nulls|
|------------|-----------|------------|
|id          |int        | &#9744;    |
|name        |varchar(MAX)| &#9745;   |
|mail          |varchar(50)| &#9745;    |

- Right click on ```id``` > ```Set Primary Key```
- Click on ```id``` > Column Properties > Identity Specification > ```(Is Identity)``` = ```Yes```

This will automatically increment ```id``` with every new entry.

-------------------------------------------------------------------------------
# Setting up Postman
- Execute the .cs project.
- Copy the port name (ex. ```https://localhost:44340/```)
- Paste in postman as ```https://localhost:44340/api/AppName``` 
Defined by __AppNameController.cs__ ```[Route("api/[controller]"]```

### Send data to DB (Add: POST, Display: GET, Update: PUT)
##### To add data to DB (POST):
- Select POST
- Body > Raw > JSON
```
{
  "name": "nine",
  "mail": "nine@asd.com"
}
```
- Send

Don't send primary key (id).

##### To get data from DB (GET):
- Select GET
- Body > none
- Send

##### To update data in DB (PUT):
- Select PUT
- Body > Raw > JSON
```
{
  "id" : 2,
  "name": "nine",
  "mail": "nine@asd.com"
}
```
- Send
 

Add primary key (id). Primary key must already exist in the DB.

------------------------------------------------------------------------


