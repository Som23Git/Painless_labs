#Basic Example(with inline code)

```
POST _scripts/painless/_execute
{
    "script":{
        "lang":"painless",
        "source": "2 * 4",
        "params":{}
    }
}
```

#Basic Example(same code but with block code)

POST _scripts/painless/_execute
{
    "script":{
        "lang":"painless",
        "source": """
        return 2 * 4
        """,
        "params":{}
    }
}

#Using variables and data types
#### Using block code
```
POST _scripts/painless/_execute
{
  "script":{
    "lang": "painless",
    "source":
    """
    int x = 10;
    int y = 123;
    return x * y;
    """
  }
}
```
#### Using inline code
```
POST _scripts/painless/_execute
{
    "script":{
        "lang":"painless",
        "source": "int x = 10; int y =100; x * y"
    }
}
```

#### Using dataypes and variables in a declaration
```
POST _scripts/painless/_execute
{
"script":{
  "lang": "painless",
  "source":
  """
  int age = 25;
  String firstName = "John";
  String lastName = "doe";
  double averageScore = (39 * 89) + 90;
  boolean isGoodScore = averageScore > 100;
  return firstName + " " + lastName + " (age " + age +
        ") has an average score of " + averageScore +
        " -- it is " + isGoodScore + " that this is a good score";
  
  """
  }
}

#Output
{
  "result": "John doe (age 25) has an average score of 3561.0 -- it is true that this is a good score"
}
```

### Multiplication table for an integer m.
### Write a script to find the multiplication tables for all positive integers up to m (with each table going up to m*n) and return them in a Map. Continue to use functions to organize and encapsulate your logic.

```
POST _scripts/painless/_execute
{
    "script":{
        "lang":"painless",
        "source":
        """
        int m = params.m;
        int n = params.n;
        ArrayList mulTable = [];
        for(int i=1;i<n;i++){
            mulTable.add(m + "x" + i + "=" + m * i);
        }
        return [m:mulTable]     //Important Line
        """,
        "params":{
            "m": 20,
            "n": 10
        }
    }
}

# Output
{
  "result": "{20=[20x1=20, 20x2=40, 20x3=60, 20x4=80, 20x5=100, 20x6=120, 20x7=140, 20x8=160, 20x9=180]}"
}
```

### Addition table for an integer m.
```
POST _scripts/painless/_execute
{
  "script":{
    "lang":"painless",
    "source": 
    """
    int x = params.x;
    int y = params.y;
    ArrayList addTable = [];
    for(int i = 0; i<x; i++){
      addTable.add(y + "+" + i + "=" + (y + i));
    }
    return addTable;
    """,
    "params":{
      "x": 10,
      "y":5
    }
  }
}

# Output
{
  "result": "[5+0=5, 5+1=6, 5+2=7, 5+3=8, 5+4=9, 5+5=10, 5+6=11, 5+7=12, 5+8=13, 5+9=14]"
}
```

### Subtraction table for an integer y and x.
```
POST _scripts/painless/_execute
{
  "script":{
    "lang":"painless",
    "source":
    """
    int x = params.x;
    int y = params.y;
    String z = "Hello";
    ArrayList subTable = [];
    for(int i = 15; i<y;){
      subTable.add(x + "-" + i + "=" + (x-i));
      i = i + 5;
    }
    return subTable;
    """,
    "params":{
      "x":100,
      "y":55
    }
  }
}

# Output
{
  "result": "[100-15=85, 100-20=80, 100-25=75, 100-30=70, 100-35=65, 100-40=60, 100-45=55, 100-50=50]"
}
```

## Exercise 1

```
#Input
"configItems":
[
    {
        "resourceId":"foo0",
        "type":"bar0"
    },
    {
        "resourceId":"foo1",
        "type":"bar1"
    },
    {
        "type":"bar2"
    },
    {
        "resourceId":"foo3"
    }
]

#Expected output

{
    "result": "{foo0=bar0, foo1=bar1, Unknown_resource_id=bar3, foo4=Unknown_type}" 
}
```
Write a script for the above input
```
#Trial One

POST _scripts/painless/_execute
{
    "script":{
        "lang":"painless",
        "source":
        """
        Map nestedObjectstoList(List nestedObjects){
          Map entries = new HashMap();
          for(nestedObject in nestedObjects){
            entries.put(
              nestedObject.get("resourceId"),
              nestedObject.get("type")
              )
          }
          return entries;
        }
        return nestedObjectstoList(params.configItems);
        """,
        "params":{
            "configItems":
            [
                {
                    "resourceId":"foo0",
                    "type":"bar0"
                },
                {
                    "resourceId":"foo1",
                    "type":"bar1"
                },
                {
                    "type":"bar2"
                },
                {
                    "resourceId":"foo3"
                }
            ]
        }
    }
}

# Output for trial 1
{
  "result": "{null=bar2, foo0=bar0, foo1=bar1, foo3=null}"
}

```

```
# Trial two - adding Default options, if object properties is not available so this replaces the "null"

POST _scripts/painless/_execute
{
  "script":{
    "lang":"painless",
    "source":
    """
      Map nestedObjectstoList(List nestedObjects){
        Map entries = new HashMap();
        for(nestedObject in nestedObjects){
          entries.put(
          nestedObject.getOrDefault("resourceId","Unknown_Resource"),
          nestedObject.getOrDefault("type","Unknown_type")
          )
        }
        return entries;
      }
      return nestedObjectstoList(params.configItems);
    """,
    "params":{
      "configItems":
            [
                {
                    "resourceId":"foo0",
                    "type":"bar0"
                },
                {
                    "resourceId":"foo1",
                    "type":"bar1"
                },
                {
                    "type":"bar2"
                },
                {
                    "resourceId":"foo3"
                }
            ]
    }
  }
}

# Output for Trial two 2
{
  "result": "{Unknown_Resource=bar2, foo0=bar0, foo1=bar1, foo3=Unknown_type}"
}
```
```
# Trial three - replacing the resourceId and type as a param i.e. declaring it as keyField and valueField so taking it in the function.

POST _scripts/painless/_execute
{
  "script":{
    "lang":"painless",
    "source":
    """
    Map nestedObjectstoList(List nestedObjects,String keyField,String valueField){
      Map entries = new HashMap();
      for(nestedObject in nestedObjects){
        entries.put(
          nestedObject.getOrDefault(keyField,"Unknown_Key"),
          nestedObject.getOrDefault(valueField,"Unknown_value")
          )
      }
      return entries;
    }
    return nestedObjectstoList(params.configItems, params.keyField, params.valueField);
    """,
    "params":{
      "keyField":"resourceId",
      "valueField":"type",
      "configItems":
            [
                {
                    "resourceId":"foo0",
                    "type":"bar0"
                },
                {
                    "resourceId":"foo1",
                    "type":"bar1"
                },
                {
                    "type":"bar2"
                },
                {
                    "resourceId":"foo3"
                }
            ]
    }
  }
}


# Output for trial three
{
  "result": "{Unknown_Key=bar2, foo0=bar0, foo1=bar1, foo3=Unknown_value}"
}

```

### Write a script that will test is an input integer n is even or odd. Encapsulate the logic in functions as necessary.

```
#Basic Trial one

POST _scripts/painless/_execute
{
  "script":{
    "lang":"painless",
    "source": 
    """
      int x = params.n;
      if((x%2) == 0){
        return "even"
      } else
      {
        return "odd"
      }
    """,
    "params":{
      "n":1234
    }
  }
}

# Output
{
  "result": "even"
}

```


### Get-loan-quote query and results.
```
# Basic Trial one

GET borrowers/_search
{
  "_source":false,
  "fields":[
    "name",
    "id",
    "credit_rating", 
    "loan_quote.risk_rating",
    "loan_quote.loan_id",
    "loan_quote.interest_rate"
  ],
  "runtime_mappings":{
    "loan_quote":{
      "type": "composite",
      "script":{
        "lang": "painless",
        "source":
        """
        if(doc['credit_rating'].value > 500){
          emit(['risk_rating': 'Poor']);
        }
        """
      },
        "fields":{
          "risk_rating":{
            "type": "keyword"
          },
          "loan_id":{
            "type":"keyword"
          },
          "interest_rate":{
            "type":"double"
          }
        }
      }
    }
  }
  ```