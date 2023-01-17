```
PUT borrowers/_doc/1
{
  "id": "001",
  "name": "Joe",
  "credit_rating": 450
}
```

```
PUT borrowers/_doc/2
{
  "id": "002",
  "name": "Mary",
  "credit_rating": 650
}
```


### 1.1 Write a script that will accept two parameters, first_name and last_name and create a greeting. NOTE: You do not need to create a document to execute painless script.

Example:
```
POST _scripts/painless/_execute
{
  "script":{
    "lang":"painless",
    "source": "'This is ' + params.first_name + params.last_name ",
    "params":{
      "first_name": "John",
      "last_name": "Doe23"
    }
  }
}


#Output
{
  "result": "This is JohnDoe23"
}
```

```
POST _scripts/painless/_execute
{
  "script":{
    "lang":"painless",
    "source": "'Welcome ' + params.first_name + params.last_name + '!' ",
    "params":{
      "first_name": "John",
      "last_name": "McCafee"
    }
  }
}

#Output
{
  "result": "Welcome JohnMcCafee!"
}
```


### 1.2 A temperature in degrees Farenheit (℉) can be converted to degrees Celsius (℃) using the formula:

Formula: (℃) = (℉ - 32) x 5/9

5/9 = 0.5556

### Write a script to convert from degrees Farenheit to degrees Celsius. Use parameters to pass the degrees Farenheit to the script and encapsulate the formula in a farenheitToCelsius function inside the script.

```
POST _scripts/painless/_execute
{
  "script":{
    "lang":"painless",
    "source": 
        """
        double farenheitToCelsius(double f) {
            double c = (f - 32) / 1.8;
            return c;
        }
        return farenheitToCelsius(params.f);
        """,
    "params":{
      "f":"20"
    }
  }
}


