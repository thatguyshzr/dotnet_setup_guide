# Add DB table columns values in .cshtml dropdown

## SQL
view values from table
```sql
select distinct ColumnName
from TableName
order by ColumnName
```
----------------------------------------------

## C#
## Step 1
get values from DB to api
### AppNameRepository.cs
```C#
public List<double> GetValuesForDropDown()
        {
            List<double> dropdown_values = new List<double>();
            string sql = "select distinct ColumnName from from TableName order by ColumnName";
            using (SqlConnection con = new SqlConnection(ConfigurationExtensions.GetConnectionString(this.configuration, "CCDBConnection")))
            {
                SqlCommand cmd = new SqlCommand(sql, con);
                con.Open();
                SqlDataReader sdr = cmd.ExecuteReader();
                if (sdr.HasRows)
                {
                    while (sdr.Read())
                    {
                        dropdown_values.Add(double.Parse(sdr["ColumnName"].ToString()));
                    }
                }
            }
            return dropdown_values;
        }
```

## Step 2
### MasterDAO.cs
Add this in ```public interface MasterDAO { }```
```C#
List<double> GetValuesForDropDown();
```

## Step 3
### AppNameController.cs
Change ```[Route("api/[controller]")]``` to ```[Route("api/[controller]/[action]")]```
```C#
[HttpGet]
public IActionResult GetValuesForDropDown()
{
    var dropdown_values = masterDAO.GetValuesForDropDown();
    return Ok(dropdown_values);
}
```
---------------------------------------------
## Step 4
## Check for values
- Run the code > in the api window, link= https://localhost:44359/api/[controller_name]/GetValuesForDropDown

- controller_name = ```public class _____Controller : ControllerBase``` in MasterDAO.cs

- a list of values should be printed out.

--------------------------------------------
## Step 5
## html
### index.cshtml
In html
```html
<select id="my_value" class="form-control">
    <option class="form-control" value=50 selected>--Select--</option>
</select>
```

in ```@section Scripts{ }```

```html
<script>
$(document).ready(function () {
    $.ajax({
        type: "GET", url: "https://localhost:44359/api/[controller_name]/[function_name]", dataType: "json", contentType: "application/json", success: function (res) {
            $.each(res, function (data, value) {
                $("#my_value").append($("<option></option>").val(parseFloat(value)).html(parseFloat(value)));
            })
        }
    });
})
</script>
```

https://www.c-sharpcorner.com/blogs/bind-dropdown-using-jquery-ajax

- function_name = In AppNameController.cs
```C#
[HttpPost]
public IActionResult function_name(class_name variable_name)
{
    LV_Calculator lVCalculator = new LV_Calculator(this.masterDAO);
    return this.Ok(lVCalculator.Calculate(data));
}
```

