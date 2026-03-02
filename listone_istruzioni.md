# Il listone

## Creazione di un dataframe
Un dataframe può essere creato a partire da una lista:

```python
data = [(100,"Steven","King",1987,"AD_PRES",24000.0,90),
(101,"Neena","Kochhar",1989,"AD_VP",17000.0,90),
(102,"Lex","DeHaan",1993,"AD_VP",17000.0,90),
(103,"Alexander","Hunold",1990,"IT_PROG",9000.0,60),
(104,"Bruce","Ernst",1991,"IT_PROG",6000.0,60),
(105,"David","Austin",1997,"IT_PROG",4800.0,60)
]

#createDataFrame requires 2 args:
# dataframe, an optional schema
data_df = spark.createDataFrame(data)
```

Per visualizzare un dataframe usiamo il metodo `show()` che richiede tre parametri opzionali:
1. `n` = numero di record da stampare (default 20)
2. `truncate` = flag booleano per troncare o meno i valori (di default a `true`)
3. `vertical` = flag boolean per stampare verticalmente (di default a `false`)
```python
data_df.show()

#Output:
+---+---------+-------+----+-------+-------+---+
| _1|       _2|     _3|  _4|     _5|     _6| _7|
+---+---------+-------+----+-------+-------+---+
|100|   Steven|   King|1987|AD_PRES|24000.0| 90|
|101|    Neena|Kochhar|1989|  AD_VP|17000.0| 90|
|102|      Lex| DeHaan|1993|  AD_VP|17000.0| 90|
|103|Alexander| Hunold|1990|IT_PROG| 9000.0| 60|
|104|    Bruce|  Ernst|1991|IT_PROG| 6000.0| 60|
|105|    David| Austin|1997|IT_PROG| 4800.0| 60|
+---+---------+-------+----+-------+-------+---+

```

Per visualizzare lo schema di un Dataframe usiamo il metodo `printSchema`:
```python
data_df.printSchema()

#Output:
root
 |-- _1: long (nullable = true)
 |-- _2: string (nullable = true)
 |-- _3: string (nullable = true)
 |-- _4: long (nullable = true)
 |-- _5: string (nullable = true)
 |-- _6: double (nullable = true)
 |-- _7: long (nullable = true)
```

Uno schema può essere definito a partire da una lista di nomi di colonne, senza specificare il datatype, come parametro del metodo `createDataFrame`:
```python
col_names = ["ID", "FIRST_NAME", "LAST_NAME", "HIRE_DATE", "JOB", "SALARY", "DEPT"]
data_df = spark.createDataFrame(data, col_names)
data_df.show()

#Output:
+---+----------+---------+---------+-------+-------+----+
| ID|FIRST_NAME|LAST_NAME|HIRE_DATE|    JOB| SALARY|DEPT|
+---+----------+---------+---------+-------+-------+----+
|100|    Steven|     King|     1987|AD_PRES|24000.0|  90|
|101|     Neena|  Kochhar|     1989|  AD_VP|17000.0|  90|
|102|       Lex|   DeHaan|     1993|  AD_VP|17000.0|  90|
|103| Alexander|   Hunold|     1990|IT_PROG| 9000.0|  60|
|104|     Bruce|    Ernst|     1991|IT_PROG| 6000.0|  60|
|105|     David|   Austin|     1997|IT_PROG| 4800.0|  60|
+---+----------+---------+---------+-------+-------+----+
```

Per definire uno schema completo (nome colonna, datatype e constraints) usiamo l'oggetto `StrcutType` che possiede una lista di `StructField`, un oggetto che contiene info sul singolo campo, e passato in input a `createDataFrame`.

`StructuType` possiede tre campi:
1. nome colonna
2. datatype
3. nullable (flag booleano)

Ovviamente lo schema deve rispecchiare i datatype presenti nella lista `data` altrimenti verrà generato un errore (per esempio il campo ID definito come stringa e non come intero).

```python
schema_fields = [
    StructField("ID", IntegerType(), True),
    StructField("FIRST_NAME", StringType(), True),
    StructField("LAST_NAME", StringType(), True),
    StructField("HIRE_DATE", IntegerType(), True),
    StructField("JOB", StringType(), True),
    StructField("SALARY", FloatType(), True),
    StructField("DEPT", IntegerType(), True)
]
# creo uno schema
schema = StructType(schema_fields)
data_df = spark.createDataFrame(data, schema)
data_df.show()

#Output:
+---+----------+---------+---------+-------+-------+----+
| ID|FIRST_NAME|LAST_NAME|HIRE_DATE|    JOB| SALARY|DEPT|
+---+----------+---------+---------+-------+-------+----+
|100|    Steven|     King|     1987|AD_PRES|24000.0|  90|
|101|     Neena|  Kochhar|     1989|  AD_VP|17000.0|  90|
|102|       Lex|   DeHaan|     1993|  AD_VP|17000.0|  90|
|103| Alexander|   Hunold|     1990|IT_PROG| 9000.0|  60|
|104|     Bruce|    Ernst|     1991|IT_PROG| 6000.0|  60|
|105|     David|   Austin|     1997|IT_PROG| 4800.0|  60|
+---+----------+---------+---------+-------+-------+----+
```

Un dataframe può essere creato a partire da un dizionario (coppia nome colonna,valore):
```python
data_dict = [{"id":100,"first_name":"Steven","last_name":"King","year":1987,"role":"AD_PRES","salary":24000.0,"dep_id":90},
{"id":101,"first_name":"Neena","last_name":"Kochhar","year":1989,"role":"AD_VP","salary":17000.0,"dep_id":90}
]

#Output:
+------+----------+---+---------+-------+-------+----+
|dep_id|first_name| id|last_name|   role| salary|year|
+------+----------+---+---------+-------+-------+----+
|    90|    Steven|100|     King|AD_PRES|24000.0|1987|
|    90|     Neena|101|  Kochhar|  AD_VP|17000.0|1989|
+------+----------+---+---------+-------+-------+----+
```

Un dataframe può essere creato a partire da un file con due modalità:
- funzione `spark.read.load()` per file di qualsiasi formato
- funzione `spark.read.csv()` per file csv

Il metodo `load` richiede in input alcuni parametri come il percorso `path` assoluto del file, il formato del file `format`, il carattere separatore `sep`, il flag `header` per specificare se il file possiede un header, il flag `inferSchema` per decidere se sia Spark a definire i datatype (sconsigliato).

```python
path = "/Volumes/workspace/lerba/employees/"
df_employees = spark.read.load(path+"employees.csv", format="csv", sep=",", header="false", inferSchema="true")
df_employees.show(10)

#Output:
+---+---------+---------+----+----------+-----+---+
|_c0|      _c1|      _c2| _c3|       _c4|  _c5|_c6|
+---+---------+---------+----+----------+-----+---+
|100|   Steven|     King|1987|   AD_PRES|24000| 90|
|101|    Neena|  Kochhar|1989|     AD_VP|17000| 90|
|102|      Lex|   DeHaan|1993|     AD_VP|17000| 90|
|103|Alexander|   Hunold|1990|   IT_PROG| 9000| 60|
|104|    Bruce|    Ernst|1991|   IT_PROG| 6000| 60|
|105|    David|   Austin|1997|   IT_PROG| 4800| 60|
|106|    Valli|Pataballa|1998|   IT_PROG| 4800| 60|
|107|    Diana|  Lorentz|1999|   IT_PROG| 4200| 60|
|108|    Nancy|Greenberg|1994|    FI_MGR|12000|100|
|109|   Daniel|   Faviet|1994|FI_ACCOUNT| 9000|100|
+---+---------+---------+----+----------+-----+---+
```

Il metodo `spark.read.csv()` ammette alcune opzioni modificabvili come i parametri visti precedentemente e l'opzione `mode` che ammette tre valori:
- `PERMISSIVE`: non vengono scaratati i record malformati ma il campo viene impostato a `NULL`

```python
df_employees_err = spark.read\
    .option("header", "false")\
    .option("sep", ",")\
    .option("mode", "PERMISSIVE")\
    .schema(schema)\
    .csv(path+"employees_err.csv")

df_employees_err.show(10)

#Output:
+---+----------+---------+---------+----------+-------+----+
| ID|FIRST_NAME|LAST_NAME|HIRE_DATE|       JOB| SALARY|DEPT|
+---+----------+---------+---------+----------+-------+----+
|100|    Steven|     King|     1987|   AD_PRES|   NULL|  90|
|101|     Neena|  Kochhar|     1989|     AD_VP|17000.0|  90|
|102|       Lex|   DeHaan|     1993|     AD_VP|17000.0|  90|
|103| Alexander|   Hunold|     1990|   IT_PROG| 9000.0|  60|
|104|     Bruce|    Ernst|     1991|   IT_PROG| 6000.0|  60|
|105|     David|   Austin|     1997|   IT_PROG| 4800.0|  60|
|106|     Valli|Pataballa|     1998|   IT_PROG| 4800.0|  60|
|107|     Diana|  Lorentz|     1999|   IT_PROG| 4200.0|  60|
|108|     Nancy|Greenberg|     1994|    FI_MGR|12000.0| 100|
|109|    Daniel|   Faviet|     1994|FI_ACCOUNT| 9000.0| 100|
+---+----------+---------+---------+----------+-------+----+
```

Il metodo permissive prevede che i record malformati vengono memorizzati in una colonna chiamata `_corrupt_record` esplicitamente dichiarata nello schema:

```python
schema_fields_err = [
    StructField("ID", IntegerType(), True),
    StructField("FIRST_NAME", StringType(), True),
    StructField("LAST_NAME", StringType(), True),
    StructField("HIRE_DATE", IntegerType(), True),
    StructField("JOB", StringType(), True),
    StructField("SALARY", FloatType(), True),
    StructField("DEPT", IntegerType(), True),
    StructField("_corrupt_record", StringType(), True)
]
schema_err = StructType(schema_fields_err)

df_employees_err = spark.read\
    .option("header", "false")\
    .option("sep", ",")\
    .option("mode", "PERMISSIVE")\
    .schema(schema_err)\
    .csv(path+"employees_err.csv")

df_employees_err.show(11, truncate=False)

#Output:
+---+----------+---------+---------+----------+-------+----+--------------------------------------+
|ID |FIRST_NAME|LAST_NAME|HIRE_DATE|JOB       |SALARY |DEPT|_corrupt_record                       |
+---+----------+---------+---------+----------+-------+----+--------------------------------------+
|100|Steven    |King     |1987     |AD_PRES   |NULL   |90  |100,Steven,King,1987,AD_PRES,xyz,90   |
|101|Neena     |Kochhar  |1989     |AD_VP     |17000.0|90  |NULL                                  |
|102|Lex       |DeHaan   |1993     |AD_VP     |17000.0|90  |NULL                                  |
|103|Alexander |Hunold   |1990     |IT_PROG   |9000.0 |60  |NULL                                  |
|104|Bruce     |Ernst    |1991     |IT_PROG   |6000.0 |60  |NULL                                  |
|105|David     |Austin   |1997     |IT_PROG   |4800.0 |60  |NULL                                  |
|106|Valli     |Pataballa|1998     |IT_PROG   |4800.0 |60  |NULL                                  |
|107|Diana     |Lorentz  |1999     |IT_PROG   |4200.0 |60  |NULL                                  |
|108|Nancy     |Greenberg|1994     |FI_MGR    |12000.0|100 |NULL                                  |
|109|Daniel    |Faviet   |1994     |FI_ACCOUNT|9000.0 |100 |NULL                                  |
|110|John      |Chen     |1997     |FI_ACCOUNT|8200.0 |NULL|110,John,Chen,1997,FI_ACCOUNT,8200,AAA|
+---+----------+---------+---------+----------+-------+----+--------------------------------------+
```

- `DROPMALFORMED`: esclude i record malformati

```python
df_employees_err = spark.read\
    .option("header", "false")\
    .option("sep", ",")\
    .option("mode", "DROPMALFORMED")\
    .schema(schema)\
    .csv(path+"employees_err.csv")

df_employees_err.show(10)

#Output:
+---+----------+---------+---------+----------+-------+----+
| ID|FIRST_NAME|LAST_NAME|HIRE_DATE|       JOB| SALARY|DEPT|
+---+----------+---------+---------+----------+-------+----+
|101|     Neena|  Kochhar|     1989|     AD_VP|17000.0|  90|
|102|       Lex|   DeHaan|     1993|     AD_VP|17000.0|  90|
|103| Alexander|   Hunold|     1990|   IT_PROG| 9000.0|  60|
|104|     Bruce|    Ernst|     1991|   IT_PROG| 6000.0|  60|
|105|     David|   Austin|     1997|   IT_PROG| 4800.0|  60|
|106|     Valli|Pataballa|     1998|   IT_PROG| 4800.0|  60|
|107|     Diana|  Lorentz|     1999|   IT_PROG| 4200.0|  60|
|108|     Nancy|Greenberg|     1994|    FI_MGR|12000.0| 100|
|109|    Daniel|   Faviet|     1994|FI_ACCOUNT| 9000.0| 100|
|111|    Ismael|  Sciarra|     1997|FI_ACCOUNT| 7700.0| 100|
+---+----------+---------+---------+----------+-------+----+
```
- `FAILFAST`: interrompe la lettura quando trova un record malformato

```python
df_employees_err = spark.read\
    .option("header", "false")\
    .option("sep", ",")\
    .option("mode", "FAILFAST")\
    .schema(schema)\
    .csv(path+"employees_err.csv")

df_employees_err.show(10)

#Output:
+---+----------+---------+---------+----------+-------+----+
| ID|FIRST_NAME|LAST_NAME|HIRE_DATE|       JOB| SALARY|DEPT|
+---+----------+---------+---------+----------+-------+----+
|101|     Neena|  Kochhar|     1989|     AD_VP|17000.0|  90|
|102|       Lex|   DeHaan|     1993|     AD_VP|17000.0|  90|
|103| Alexander|   Hunold|     1990|   IT_PROG| 9000.0|  60|
|104|     Bruce|    Ernst|     1991|   IT_PROG| 6000.0|  60|
|105|     David|   Austin|     1997|   IT_PROG| 4800.0|  60|
|106|     Valli|Pataballa|     1998|   IT_PROG| 4800.0|  60|
|107|     Diana|  Lorentz|     1999|   IT_PROG| 4200.0|  60|
|108|     Nancy|Greenberg|     1994|    FI_MGR|12000.0| 100|
|109|    Daniel|   Faviet|     1994|FI_ACCOUNT| 9000.0| 100|
+---+----------+---------+---------+----------+-------+----+
```

### Metodi dei Dataframe

Il metodo `count` restituisce il numero di record nel dataframe:

```python
df_employees.count()

#Output:
107
```

L'attributo `columns` restituisce la lista dei nomi delle colonne del dataframe:
```python
df_employees.columns

#Output:
['ID', 'FIRST_NAME', 'LAST_NAME', 'HIRE_DATE', 'JOB', 'SALARY', 'DEPT']
```

Il metodo `collect` restituisce una lista di oggetti di tipo `Row` corrispondenti alle righe del dataframe:
```python
df_employees.collect()
[Row(ID=100, FIRST_NAME='Steven', LAST_NAME='King', HIRE_DATE=1987, JOB='AD_PRES', SALARY=24000.0, DEPT=90),
 Row(ID=101, FIRST_NAME='Neena', LAST_NAME='Kochhar', HIRE_DATE=1989, JOB='AD_VP', SALARY=17000.0, DEPT=90),
 Row(ID=102, FIRST_NAME='Lex', LAST_NAME='DeHaan', HIRE_DATE=1993, JOB='AD_VP', SALARY=17000.0, DEPT=90),
 Row(ID=103, FIRST_NAME='Alexander', LAST_NAME='Hunold', HIRE_DATE=1990, JOB='IT_PROG', SALARY=9000.0, DEPT=60),
 Row(ID=104, FIRST_NAME='Bruce', LAST_NAME='Ernst', HIRE_DATE=1991, JOB='IT_PROG', SALARY=6000.0, DEPT=60),
 Row(ID=105, FIRST_NAME='David', LAST_NAME='Austin', HIRE_DATE=1997, JOB='IT_PROG', SALARY=4800.0, DEPT=60),
 Row(ID=106, FIRST_NAME='Valli', LAST_NAME='Pataballa', HIRE_DATE=1998, JOB='IT_PROG', SALARY=4800.0, DEPT=60),
 ...]
 ```

Il metodo `take` e `head` restituiscono una lista di `Row` con le prime `n` righe del dataframe:
```python
df_employees.take(5)
df_employees.head(5)

#Output:
[Row(ID=100, FIRST_NAME='Steven', LAST_NAME='King', HIRE_DATE=1987, JOB='AD_PRES', SALARY=24000.0, DEPT=90),
 Row(ID=101, FIRST_NAME='Neena', LAST_NAME='Kochhar', HIRE_DATE=1989, JOB='AD_VP', SALARY=17000.0, DEPT=90),
 Row(ID=102, FIRST_NAME='Lex', LAST_NAME='DeHaan', HIRE_DATE=1993, JOB='AD_VP', SALARY=17000.0, DEPT=90),
 Row(ID=103, FIRST_NAME='Alexander', LAST_NAME='Hunold', HIRE_DATE=1990, JOB='IT_PROG', SALARY=9000.0, DEPT=60),
 Row(ID=104, FIRST_NAME='Bruce', LAST_NAME='Ernst', HIRE_DATE=1991, JOB='IT_PROG', SALARY=6000.0, DEPT=60)]
```

Il metodo `first` restituisce un oggetto di tipo `Row` contenente la prima riga del dataframe:

```python
first_row = df_employees.first()
print(first_row)

#Output:
Row(ID=100, FIRST_NAME='Steven', LAST_NAME='King', HIRE_DATE=1987, JOB='AD_PRES', SALARY=24000.0, DEPT=90)
```

Per accedere al campo *n-esimo* della riga possiamo usare la posizione o il nome della colonna:
```python
print(first_row[0])
print(first_row["ID"])

#Output:
100
100
```

Il metodo `describe` restituisce un dataframe di sintesi statistica. Può essere applicato sull'intero dataframe o su alcune colonne:
```python
df_employees.describe().show()
#df_employees.describe("SALARY", "DEPT").show()

#Output:
+-------+----------------+----------+---------+------------------+----------+-----------------+-----------------+
|summary|              ID|FIRST_NAME|LAST_NAME|         HIRE_DATE|       JOB|           SALARY|             DEPT|
+-------+----------------+----------+---------+------------------+----------+-----------------+-----------------+
|  count|             107|       107|      107|               107|       107|              107|              107|
|   mean|           153.0|      NULL|     NULL|1997.0747663551401|      NULL|6463.551401869159|63.36448598130841|
| stddev|31.0322412983658|      NULL|     NULL|2.4713487822210825|      NULL|3910.638429839391|20.87446418852039|
|    min|             100|      Adam|     Abel|              1987|AC_ACCOUNT|           2100.0|               10|
|    max|             206|   Winston|  Zlotkey|              2000|    ST_MAN|          24000.0|              110|
+-------+----------------+----------+---------+------------------+----------+-----------------+-----------------+
```

### Selezione delle colonne dei Dataframe

Una colonna può essere selezionata tramite *dot-notation* o tramite *brackets* dove entrambi restituiscono un oggetto di tipo colonna:

```python
df_employees.ID
df_employees["ID"]    # METODO PREFERIBILE

#Output:
Column<'ID'>
```

Il nome di una colonna può essere rinominato attraverso il metodo `alias`:
```python
df_employees.ID.alias("new_id")
df_employees["ID"].alias("new_id")

#Output:
Column<'ID as new_id'>
```

Un altro metodo avviene tramite il metodo `select` che richiede una o più colonne e consente di visualizzare i valori assunti dalla colonna tramite il metodo `show`:
```python
#df_employees.select("FIRST_NAME").show(5)                  # ---> seleziona una colonna
df_employees.select("FIRST_NAME", "LAST_NAME").show(5)      # ---> seleziona più colonne

#Output:
+----------+---------+
|FIRST_NAME|LAST_NAME|
+----------+---------+
|    Steven|     King|
|     Neena|  Kochhar|
|       Lex|   DeHaan|
| Alexander|   Hunold|
|     Bruce|    Ernst|
+----------+---------+
```

In questo caso selezioniamo più colonne utilizzando come argomento oggetti di tipo colonna, solitamentèutilizzato quando applichiamo operazioni ad una colonna (per esempio modificare i record restituiti nella visualizzazione):
```python
#Selezione attraverso oggetti di tipo colonna
df_employees.select(df_employees["FIRST_NAME"], df_employees["LAST_NAME"]).show(5)

#Esempio di utilizzo estrarre salario e aggiungere 1000 ad ogni valore
#in questo modo: ERRORE
#df_employees.select("ID","SALARY"+1000).show(5) 

# MODO CORRETTO:
#df_employees.select("ID", df_employees["SALARY"]+1000).show(5)

df_employees.select("ID", (df_employees["SALARY"]+1000).alias("SALARY_INC")).show(5)

#Output:
+---+----------+
| ID|SALARY_INC|
+---+----------+
|100|   25000.0|
|101|   18000.0|
|102|   18000.0|
|103|   10000.0|
|104|    7000.0|
+---+----------+
```

Per selezionare tutte le colonne usiamo l'attributo `columns` oppure la *star-notation* di SQL:
```python
df_employees.select("*").show(5)
df_employees.select(df_employees.columns).show(5)

#Output:

+---+----------+---------+---------+-------+-------+----+
| ID|FIRST_NAME|LAST_NAME|HIRE_DATE|    JOB| SALARY|DEPT|
+---+----------+---------+---------+-------+-------+----+
|100|    Steven|     King|     1987|AD_PRES|24000.0|  90|
|101|     Neena|  Kochhar|     1989|  AD_VP|17000.0|  90|
|102|       Lex|   DeHaan|     1993|  AD_VP|17000.0|  90|
|103| Alexander|   Hunold|     1990|IT_PROG| 9000.0|  60|
|104|     Bruce|    Ernst|     1991|IT_PROG| 6000.0|  60|
+---+----------+---------+---------+-------+-------+----+
```

Possiamo selezionare le colonne attraverso un range sull'attributo `columns`:
```python
df_employees.select(df_employees.columns[0:4]).show(5)

#Output:

+---+----------+---------+---------+
| ID|FIRST_NAME|LAST_NAME|HIRE_DATE|
+---+----------+---------+---------+
|100|    Steven|     King|     1987|
|101|     Neena|  Kochhar|     1989|
|102|       Lex|   DeHaan|     1993|
|103| Alexander|   Hunold|     1990|
|104|     Bruce|    Ernst|     1991|
+---+----------+---------+---------+
```

Un dataframe può avere anche dati annidati, per esempio una tupla all'interno di una colonna. In questo caso lo schema viene definito in questo modo:
- oggetto `StructType` esterno composto da lista di `StructField`
  - se un campo è *raw* viene definito tramite un semplice `StructField`
  - se un campo è annidato/composto viene definito un oggetto `StructField` con datatype un nuovo `StructType` dove all'interno vengono definiti i vari `StructField` *raw* o con altri `StructType`

```python
nested_data = [
        ((None,"xxx","yyy"),25,"M"),
        (("aaa","bbb",""),40,"F"),
        (("xxx","yyy","xxx"),52,"F"),
        (("vvv","aaa",None),31,"M")
        ]
s = StructType([
    StructField('contacts', StructType([
         StructField('phone_number', StringType(), True),
         StructField('mobile', StringType(), True),
         StructField('mail', StringType(), True)
         ])),
     StructField('age', IntegerType(), True),
     StructField('gender', StringType(), True)
     ])
 
df_nested = spark.createDataFrame(data = nested_data, schema = s)

df_nested.show(5)

#Output:

+----------------+---+------+
|        contacts|age|gender|
+----------------+---+------+
|{NULL, xxx, yyy}| 25|     M|
|    {aaa, bbb, }| 40|     F|
| {xxx, yyy, xxx}| 52|     F|
|{vvv, aaa, NULL}| 31|     M|
+----------------+---+------+
```

Dopo aver creato un dataset con dati annidati, possiamo applicare una serie di notazioni per accedere ai campi composti:
- accesso tramite colonna che restituisce i dati composti:
```python
# seleziono campo contacts
df_nested.select("contacts").show(5)

#Output:
+----------------+
|        contacts|
+----------------+
|{NULL, xxx, yyy}|
|    {aaa, bbb, }|
| {xxx, yyy, xxx}|
|{vvv, aaa, NULL}|
+----------------+
```

- accesso ai campi annidati dell'attributo composto tramite *dot-notation*:
```python
df_nested.select("contacts.phone_number", "contacts.mobile").show(5)

#Output:
+------------+------+
|phone_number|mobile|
+------------+------+
|        NULL|   xxx|
|         aaa|   bbb|
|         xxx|   yyy|
|         vvv|   aaa|
+------------+------+
```

- accesso a tutti i campi annidati dell'attributo composto tramite *star-operator*:
```python
df_nested.select("contacts.*").show(5)

#Output:
+------------+------+----+
|phone_number|mobile|mail|
+------------+------+----+
|        NULL|   xxx| yyy|
|         aaa|   bbb|    |
|         xxx|   yyy| xxx|
|         vvv|   aaa|NULL|
+------------+------+----+
```

In un dataframe le colonne possono essere estratte per esclusione tramite il metodo `drop` che restituisce un nuovo dataframe senza le colonne specificate:

```python
df_employees.drop("ID").show(5)

#Output:

+----------+---------+---------+-------+-------+----+
|FIRST_NAME|LAST_NAME|HIRE_DATE|    JOB| SALARY|DEPT|
+----------+---------+---------+-------+-------+----+
|    Steven|     King|     1987|AD_PRES|24000.0|  90|
|     Neena|  Kochhar|     1989|  AD_VP|17000.0|  90|
|       Lex|   DeHaan|     1993|  AD_VP|17000.0|  90|
| Alexander|   Hunold|     1990|IT_PROG| 9000.0|  60|
|     Bruce|    Ernst|     1991|IT_PROG| 6000.0|  60|
+----------+---------+---------+-------+-------+----+
```

La selezione di una colonna può restituire valori distinti tramite il metodo `distinct`:

```python
df_employees.select("JOB").distinct().show(5)

#Output:

+--------+
|     JOB|
+--------+
|PU_CLERK|
|  PU_MAN|
|   AD_VP|
|  AC_MGR|
| AD_PRES|
+--------+
```
