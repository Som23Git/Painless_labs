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