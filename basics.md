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

### Script parameters.

```
POST _scripts/painless/_execute
{
    "script":{
        "lang":"painless",
        "source":
        """
        String name = params.name;
        return "The name is " + name + "!";
        """,
        "params":{
            "name": "Som"
        }
    }
}

# Output
{
  "result": "The name is Som!"
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


```
# Loan Quote problem just testing phase

GET borrowers/_search
{
  "_source": false, 
  "fields": [
    "loan_quote.loan_id","loan_quote.risk_rating","loan_quote.interest_rate"
  ],
  "runtime_mappings": {
    "loan_quote": {
      "type": "composite",
      "script": {
        "lang": "painless",
        "source": 
        """
        if(doc['credit_rating'].value > params.risk_rating){
        String loanId = params['_source']['name'] + '-' + params['_source']['id'];
        emit(['loan_id': loanId]);
        } else{
        emit(['risk_rating': 'Bad']);
        }
        """,
        "params": {
          "risk_rating": 500
        }
      },
      "fields":{
        "loan_id":{
          "type": "keyword"
        },
        "risk_rating":{
          "type": "keyword"
        },
        "interest_rate":{
          "type": "double"
        }
      }
    }
  }
}

#Important Notes:

doc[''].value -> Is used for keyword fields
params[''] -> Is used for text field or fields other than keyword type.


# Output

 {
  "took": 16,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 3,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {
        "_index": "borrowers",
        "_id": "1",
        "_score": 1,
        "fields": {
          "loan_quote.risk_rating": [
            "Bad"
          ]
        }
      },
      {
        "_index": "borrowers",
        "_id": "2",
        "_score": 1,
        "fields": {
          "loan_quote.loan_id": [
            "Mary-002"
          ]
        }
      },
      {
        "_index": "borrowers",
        "_id": "4",
        "_score": 1,
        "fields": {
          "loan_quote.risk_rating": [
            "Bad"
          ]
        }
      }
    ]
  }
}
```

```
#Testing the script with the basic params
POST _scripts/painless/_execute
{
  "script":{
    "lang":"painless",
    "source":
    """
    String loan_id = (params.credit_rating > params.risk_rating)? "True" : "False";
    double credit_rating = params.credit_rating;
    return([loan_id, credit_rating]);
    """,
    "params":{
      "risk_rating":500,
      "good_interest_rate": 1.5,
      "bad_interest_rate": 2.0,
      "credit_rating": 400
    }
  }
}

#Output
{
  "result": "[False, 400.0]"
}
```

```
# Testing with the Map function loan_quote

POST _scripts/painless/_execute
{
  "script":{
    "lang":"painless",
    "source":
    """
    Map getLoanQuote(int credit_rating, int risk_rating, double good_interest_rate){
      String result = credit_rating + " Credit Rating!";
      return (["result":result]);
    }
    return getLoanQuote(params.credit_rating, params.risk_rating, params.good_interest_rate);
 
    """,
    "params":{
      "risk_rating":500,
      "good_interest_rate": 1.5,
      "bad_interest_rate": 2.0,
      "credit_rating": 400
    }
  }
}

# Output
{
  "result": "{result=400 Credit Rating!}"
}

```

# The area of a regular polygon with n sides of length s can be found using the following formula
# (s^2 * n)/(4*tan(180deg/n))

```

POST _scripts/painless/_execute
{
  "script":{
    "lang":"painless",
    "source": 
    """
    double calcPolygon(double s, double n){
      double result = ((s * s * n)/(4 * Math.tan(Math.toRadians(180/n))));
      return result;
    }
    return calcPolygon(params.sides,params.length);
    """,
    "params":{
      "sides": 3,
      "length": 10
    }
  }
}

# Output
{
  "result": "69.2478795864432"
}

```

# The function that takes a input string "Hello World" and then reverse the string
# Using replace() method but it's hardcoding which will not work when dynamically ingesting data

```
POST _scripts/painless/_execute
{
  "script":{
    "lang": "painless",
    "source": 
    """
    String name = "Hello World!";
    String reverse = name.replace("Hello World","dlroW olleH");
    return reverse;
    """,
    "params":{
      
    }
  }
}

# Hardcoded Output
{
  "result": "dlroW olleH!"
}
```

# Dynamically ingested data to reverse string

```
POST _scripts/painless/_execute
{
  "script":{
    "lang": "painless",
    "source": 
    """
    String reverse(String name){
    String y = "";
    int x = name.length();
    for(int i=x-1; i>=0; i--){
     y = y + name.charAt(i);
    }
    return y;
    }
    return reverse(params.name)
    """,
    "params":{
      "name":"Hello World"
    }
  }
}

# Output
{
  "result": "dlroW olleH"
}
```
# Find the number of years between the day
# Important link: https://www.elastic.co/guide/en/elasticsearch/painless/current/painless-datetime.html
# Strings containing dates in ISO 8601 format can be converted to ZonedDateTime objects in a script.

```
POST _scripts/painless/_execute
{
  "script":{
    "lang": "painless",
    "source": 
    """
    String datetime = params.x;
    ZonedDateTime zdt1 = ZonedDateTime.parse(datetime);
    ZonedDateTime zdt2 = ZonedDateTime.parse(params.y);
    long zdt3 = ChronoUnit.YEARS.between(zdt1, zdt2);
    int year1 = zdt1.getYear();
    int year2 = zdt2.getYear();
    int resultYear = year2 - year1;
    return (["parsedX": zdt1,"parsedY": zdt2, "final":zdt3,"year1":year1,"year2":year2,"resultYear":resultYear]);
    """,
    "params":{
      "x": "1983-10-13T22:15:30Z",
      "y": "2023-10-13T10:15:30Z"
    }
  }
}

# Based on the above, we could use s.getYear() method and find the difference or we can use the complete datetype that will find the difference

# Output
{
  "result": "{parsedY=2023-10-13T10:15:30Z, resultYear=40, final=39, parsedX=1983-10-13T22:15:30Z, year1=1983, year2=2023}"
}
```

# Problems: Write a script that will accept a numeric score as a parameter and convert it to a letter grade. Use following scale: 
# A - (93 - 100), B - (85 - 92), C - (76 - 84), D - (65 - 75), F - (0 - 64)
# Encapsulate your logic in a toLetterGrade function. Return the answer as a Map with two keys numeric_grade and letter_grade

```
# My version of solution
POST _scripts/painless/_execute
{
  "script":{
    "lang": "painless",
    "source": 
    """ 
    Map toLetterGrade(int x){
        if(x >= 93){
          return (["A": x]);
        }else if(x >= 85 && x < 93){
          return (["B": x]);
        } else if(x >= 76 && x <= 84){
          return (["C": x]);
        }else if(x >= 65 && x <= 75){
          return (["D": x]);
        }else{
          return (["F": x]);
        }
      }
    return toLetterGrade(params.numericScore);
    """,
    "params":{
      "numericScore": 98,
      "letterGrade1": "A",
      "letterGrade2": "B",
      "letterGrade3": "C",
      "letterGrade4": "D",
      "letterGrade5": "F"
    }
  }
}

# Output
{
  "result": "{A=98}"
}
```

```
# Actual version from Elasticsearch
POST _scripts/painless/_execute
{
    "script": {
        "lang": "painless",
        "source": """
        String toLetterGrade(long g) {
            if (g < 65) { return "F"; }
            else if (g < 76) { return "D"; }
            else if (g < 85) { return "C"; }
            else if (g < 93) { return "B"; }
            else { return "A"; }
        }

        long grade = params.grade;
        return ["numeric_grade": grade, "letter_grade": toLetterGrade(grade)];
        """,
        "params": {
            "grade": 75
        }
    }
}

# Output
{
  "result": "{numeric_grade=75, letter_grade=D}"
}
```

# Randomly create the numeric score and fed that into the toLetterGrade function to generate the letterGrade dynamically
```
POST _scripts/painless/_execute
{
  "script":{
    "lang": "painless",
    "source": 
    """ 
        Map toLetterGrade(){
        double x = Math.ceil(Math.random() * 100);
        if(x >= 93.0){
          return (["A": x]);
        }else if(x >= 85.0 && x < 93.0){
          return (["B": x]);
        } else if(x >= 76.0 && x <= 84.0){
          return (["C": x]);
        }else if(x >= 65.0 && x <= 75.0){
          return (["D": x]);
        }else{
          return (["F": x]);
        }
      }
    return toLetterGrade();
    """
  }
}

# Output
{
  "result": "{B=88.0}"
}
```
# Calculate or multiply using a powers or exponential variable

```
POST _scripts/painless/_execute
{
  "script":{
    "lang": "painless",
    "source": 
    """
    return 10 * Math.pow(2,params.n)
    """,
    "params":{
      "n": 1
    }
  }
}

# Output
{
  "result": "20.0"
}
```

# Use a loop to write a script to raise a number x to a non-negative integer power n. Encapsulate your logic in a power function inside the script.
# Very important to notice that the ArrayList is different from Array so used add.method()
```
POST _scripts/painless/_execute
{
  "script":{
    "lang": "painless",
    "source": 
    """
    ArrayList power(int x){
      ArrayList y = [];
      for(int i=1; i< 10;i++){
        y.add(Math.pow(2*x,i));
    }
    return y;
    }
    return power(params.n)
    """,
    "params":{
      "n": 1
    }
  }
}

# Output
{
  "result": "[2.0, 4.0, 8.0, 16.0, 32.0, 64.0, 128.0, 256.0, 512.0]"
}
```

```
# The actual version of Elasticsearch
POST _scripts/painless/_execute
{
    "script": {
        "lang": "painless",
        "source": """
        double power(double x, int n) {
            double y = 1.0;
            for (int i = 1; i <= n; i++) {
                y *= x;
            }
            return y;
        }

        return power(params.x, params.n);
        """,
        "params": {
            "x": 3.9,
            "n": 4
        }
    }
}

# Output
{
  "result": "231.34409999999997"
}
```

# Use a loop to increment the value of n to 100

```
POST scripts/painless/execute
{
  "script":{
    "lang": "painless",
    "source":
    """
    int incrementValue(int n){
    for(int i=0;i<n;i++,n++){
      ArrayList x[i] = n;
    }
    return x;
    }
    return incrementValue(params.valueN)
    """,
    "params":
    {
      "valueN": 98;
    }
  }
}

# Output
{
  "result": "[98,99,100]"
}

```