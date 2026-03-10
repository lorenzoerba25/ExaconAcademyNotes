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

## Metodi dei Dataframe

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

## Selezione delle colonne dei Dataframe

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

## Modificare o creare una colonna

Per modificare una colonna, nome e/o valori, utilizziamo il metodo `withColumn` che richiede due parametri:
- nome della nuova colonna o di quella da sovrascrivere
- oggetto di tipo colonna che rappresenta il valore da sovrascrivere

```python
# aumento del 5% il salario degli impiegati
df_employees_mod = df_employees.withColumn("SALARY", df_employees["SALARY"]*1.05)
df_employees_mod.show(5)

#Output:

+---+----------+---------+---------+-------+-------+----+
| ID|FIRST_NAME|LAST_NAME|HIRE_DATE|    JOB| SALARY|DEPT|
+---+----------+---------+---------+-------+-------+----+
|100|    Steven|     King|     1987|AD_PRES|25200.0|  90|
|101|     Neena|  Kochhar|     1989|  AD_VP|17850.0|  90|
|102|       Lex|   DeHaan|     1993|  AD_VP|17850.0|  90|
|103| Alexander|   Hunold|     1990|IT_PROG| 9450.0|  60|
|104|     Bruce|    Ernst|     1991|IT_PROG| 6300.0|  60|
+---+----------+---------+---------+-------+-------+----+
```

Attraverso il metodo `cast` possiamo fissare il datatype della colonna.
```python
df_employees_mod = df_employees.withColumn("SALARY", (df_employees["SALARY"]*1.05).cast("float"))
```

A volte però risulta utile modificare i valori di una colonna solo se si verificano alcune condizioni. In questo caso usiamo in combinazione a `withColumn` il metodo `when`, potenzialmente in cascata ed eventualmente alla fine `otherwise`, che richiede 2 parametri in input:
- condizione che voglio soddisfare
- valore che voglio attribuire se la condizione è vera (se falsa inserisce `null`)
```python
df_level = df_employees.withColumn("LEVEL", when(df_employees["SALARY"] < 5000, 1)\
    .when((df_employees["SALARY"] >= 5000) & (df_employees["SALARY"] <= 15000), 2)\
    .otherwise(3))
```
## Filtrare il contenuto di una colonna

Un primo metodo che consente di fare un filtraggio del contenuto di una colonna è il metodo `filter` che come argomento richiede solamente la condizione di filtraggio:
```python
df_level.filter(df_level["JOB"] == 'IT_PROG').show(5)

#Output:

+---+----------+---------+---------+-------+------+----+-----+
| ID|FIRST_NAME|LAST_NAME|HIRE_DATE|    JOB|SALARY|DEPT|LEVEL|
+---+----------+---------+---------+-------+------+----+-----+
|103| Alexander|   Hunold|     1990|IT_PROG|9000.0|  60|    2|
|104|     Bruce|    Ernst|     1991|IT_PROG|6000.0|  60|    2|
|105|     David|   Austin|     1997|IT_PROG|4800.0|  60|    1|
|106|     Valli|Pataballa|     1998|IT_PROG|4800.0|  60|    1|
|107|     Diana|  Lorentz|     1999|IT_PROG|4200.0|  60|    1|
+---+----------+---------+---------+-------+------+----+-----+
```

Un secondo metodo è il metodo `where`, simile al precedente:
```python
df_level.where(df_level["JOB"] == 'IT_PROG')
```

Possiamo inoltre indicare condizioni composte attraverso la logica booleana, `|, &, ~`:
```python
# per negare una condizione metti ~ davanti alla condizione da negare
df_level.where((df_level["JOB"] == "IT_PROG") & ~(df_level["HIRE_DATE"]==1997))
```

Il metodo `isin` opera in modo del tutto analogo all'operatore `IN` di SQL:
```python
df_level.filter((df_level["DEPT"].isin([10, 20, 30])) | (df_level["LEVEL"] > 2))
```

Infine abbiamo i metodi `like` (case sensitive) e `ilike` (case insensitive) per verificare se c'è match tra due stringhe. La logica è analoga a `LIKE` in SQL con le wildcard `%, _`:
```python
# "MK%" ----> una stringa che inizia con MK e poi ha altri caratteri
df_level.where(df_level["JOB"].like("MK%"))

# ilike non è case sensitive 
df_level.where(df_level["JOB"].ilike("mk%"))

# "mk_" ----> una stringa che inizia con MK o mk e poi ha un solo carattere dopo

df_level.where(df_level["JOB"].ilike("mk_"))
```

## Aggregazione 

Un primo approccio di aggregazione può avvenire usando funzioni di aggregazione senza raggruppamento, direttamente in una select:
```python
df_employees.select(sum("SALARY")).show(5)

# Output:
+-----------+
|sum(SALARY)|
+-----------+
|   691600.0|
+-----------+
```

Un secondo approccio, più comune, è quello di raggruppare prima e poi applicare le funzioni di aggregazione attraverso il metodo `groupBy` che restituisce un oggetto di tipo `GroupedData`:
```python
df_employees.groupBy("DEPT").sum("SALARY").show(5)

# Outptut:

+----+-----------+
|DEPT|sum(SALARY)|
+----+-----------+
|  70|    10000.0|
|  10|     4400.0|
|  80|   311700.0|
|  50|   156400.0|
|  20|    19000.0|
+----+-----------+

# Se omettiamo la colonna in sum() calcola la somma per ogni colonna
df_employees.groupBy("DEPT").sum().show(5)

# Output:
+----+-------+--------------+-----------+---------+
|DEPT|sum(ID)|sum(HIRE_DATE)|sum(SALARY)|sum(DEPT)|
+----+-------+--------------+-----------+---------+
|  70|    204|          1994|    10000.0|       70|
|  10|    200|          1987|     4400.0|       10|
|  80|   5670|         69930|   311700.0|     2800|
|  50|   7090|         89898|   156400.0|     2250|
|  20|    403|          3993|    19000.0|       40|
+----+-------+--------------+-----------+---------+
```

Elenchiamo di seguito alcuni esempi di funzioni di aggregazione:
```python
df_employees.groupBy("DEPT").sum("SALARY").show(3)
df_employees.groupBy("DEPT").min("SALARY").show(3)
df_employees.groupBy("DEPT").max("SALARY").show(3)
df_employees.groupBy("DEPT").mean("SALARY").show(3)
df_employees.groupBy("DEPT").count().show(3)
# Max su più colonne
df_employees.groupBy("DEPT").max("SALARY", "HIRE_DATE")
```

È possibile inoltre averèuna chiave di aggregazione composta specificando più colonne in `groupBy`:
```python
df_employees.groupBy("DEPT", "JOB").count().show(5)

# Output:
+----+----------+-----+
|DEPT|       JOB|count|
+----+----------+-----+
|  80|    SA_MAN|    5|
|  60|   IT_PROG|    5|
| 100|FI_ACCOUNT|    5|
|  80|    SA_REP|   30|
|  10|   AD_ASST|    1|
+----+----------+-----+
```

Importante notare che se chiamiamo `count` su un oggetto `GroupedData` otteniamo un nuovo dataframe (eseguiamo una trasformazione) mentre se la eseguiamo su un dataframe otteniamo un numero (eseguiamo un'azione).
```python
print(type(df_employees.groupBy("DEPT", "JOB").count()))
print(type(df_employees.count()))

# Output:

<class 'pyspark.sql.connect.dataframe.DataFrame'>
<class 'int'>
```

Esempio di applicazione di tutti i metodi:
```python
# facciamo numerica per i job del dipartimento 80
df_employees.groupBy("DEPT", "JOB").count().filter(df_employees["DEPT"] == 80).show(5)

# Output:
+----+------+-----+
|DEPT|   JOB|count|
+----+------+-----+
|  80|SA_MAN|    5|
|  80|SA_REP|   30|
+----+------+-----+
```

Molto importante è il metodo `col` che permette di specificare una colonna del dataframe ma a differenza della *dot-notation* o *brackets-notation* restituisce un oggetto di tipo `Column` con il nome della colonna passata (colonna che deve esistere). Per esempio se vogliamo filtrare su una colonna `"count"` generata dal conteggio, ```df_employees.groupBy("DEPT", "JOB").count().filter(df_employees["count"] > 5)``` produce errore in quanto la colonna `"count"` nel dataframe non esiste. Possiamo risolvere il problema in due modi:
1. assegnare un nome al dataframe che otteniamo dopo la trasformazione prodotta da `count`
2. usare il costrutto `col`
```python
from pyspark.sql.functions import col

# Metodo 1
df_employees_with_count = df_employees.groupBy("DEPT", "JOB").count()
df_employees_with_count.filter(df_employees_with_count["count"] > 5).show(5)

# Metodo 2
df_employees.groupBy("DEPT", "JOB").count().filter(col("count") > 5).show(5)

# Output:
+----+--------+-----+
|DEPT|     JOB|count|
+----+--------+-----+
|  50|SH_CLERK|   20|
|  80|  SA_REP|   30|
|  50|ST_CLERK|   20|
+----+--------+-----+
```

Fino ad ora abbiamo visto funzioni di aggregazione applicate singolarmente e in modo separato fra di loro. È possibile calcolare diversi tipi di aggregazione rispetto alla stessa chiave di raggruppamento tramite il metodo `agg` il quale supporta due approcci:
1. Usare un dizionario del tipo (colonna su cui calcolare l'aggreggato, funzione di aggregazione)
2. Passare come parametri le funzioni di aggregazione come `funzione("colonna")` precedentemente importate

```python
# Metodo 1
df_employees.groupBy("DEPT", "JOB").agg({"SALARY":"max", "*":"count"}).show(5)

# Metodo 2 con rinominazione delle colonne (opzionale)
df_employees.groupBy("DEPT", "JOB").agg(max("SALARY").alias("MAX_SALARY"), count("*").alias("COUNT")).show(5)
```

## Rinominare una colonna
Per rinominare una o più colonne di un dataframe possiamo usare uno dei seguenti metodi:
- `withColumnRenamed` che richiede in input due parametri
  - nome della vecchia colonna
  - nome della nuova colonna
- `withColumnsRenamed` che richiede in input un set di nomi con un pattern `{vecchia_colonna1, nuova_colonna1, vecchia_colonna2, nuova_colonna2 ...}`
- `alias` utilizzato solitamente nell'istruzione `select`
- `selectExpr` dove specifichiamo le colonne da rinominare con sintassi `AS` di SQL.

```python
df_employees.withColumnRenamed("HIRE_DATE", "HIRE_YEAR").show(3)

df_employees.withColumnsRenamed({"HIRE_DATE":"HIRE_YEAR", "JOB":"JOB_ID"}).show(3)

df_employees.select(col("HIRE_DATE").alias("HIRE_YEAR")).show(3)

df_rename.selectExpr("_c0 as ID", "_c1 as FIRST_NAME").show(3)

```

In `selectExpr` possiamo indicare qualsiasi istruzione SQL Like prestando attenzione ad alcuni aspetti. Quando una colonna per esempio si chiama `"max(SALARY)"` nella selectExpr non possiamo indicare `max(SALARY)` in quanto la interpreta come una funzione max sulla colonna SALARY. Per evidenziarla come testo andiamo a usare i backtick in questo modo:
```python
df_rename = df_employees.groupBy("DEPT", "JOB").agg({"SALARY":"max", "*":"count"})
df_rename.show(1)

# Output:
+----+--------+-----------+--------+
|DEPT|     JOB|max(SALARY)|count(1)|
+----+--------+-----------+--------+
|  30|PU_CLERK|     3100.0|       5|
+----+--------+-----------+--------+

# DA' ERRORE, anche con singoli apici ' '
#df_rename.selectExpr("DEPT", "JOB", "max(SALARY) as MAX_SALARY", "count(*) as COUNT").show(5)
df_rename.selectExpr("DEPT", "JOB", "`max(SALARY)` as MAX_SALARY", "`count(1)` as COUNT").show(5)

# Output:

+----+--------+----------+-----+
|DEPT|     JOB|MAX_SALARY|COUNT|
+----+--------+----------+-----+
|  30|PU_CLERK|    3100.0|    5|
|  60| IT_PROG|    9000.0|    5|
|  50|SH_CLERK|    4200.0|   20|
|  70|  PR_REP|   10000.0|    1|
|  20|  MK_REP|    6000.0|    1|
+----+--------+----------+-----+
```

Un altro esempio di espressioni SQL Like che possiamo usare:
```python
df_schema = df_employees.selectExpr("ID", "SALARY * 1.05 as SALARY", "upper('last_NAME') as LAST_NAME", "current_timestamp() as TIMESTAMP")
df_schema.show(5)

# Output:
+---+-------+---------+--------------------+
| ID| SALARY|LAST_NAME|           TIMESTAMP|
+---+-------+---------+--------------------+
|100|25200.0|LAST_NAME|2026-01-21 22:21:...|
|101|17850.0|LAST_NAME|2026-01-21 22:21:...|
|102|17850.0|LAST_NAME|2026-01-21 22:21:...|
|103| 9450.0|LAST_NAME|2026-01-21 22:21:...|
|104| 6300.0|LAST_NAME|2026-01-21 22:21:...|
+---+-------+---------+--------------------+
```

## Ordinamento di un dataframe
Come in SQL anche in Spark possiamo ordinare un dataframe su una o più colonne grazie al metodo `orderBy` che richiede due argomenti:
1. colonna su cui applicare l'ordinamento
2. tipo di ordinamento `ascending=False` oppure `ascending=True`

```python
df_employees.orderBy("HIRE_DATE", ascending=False).show(5)

# Output:
+---+----------+----------+---------+--------+-------+----+
| ID|FIRST_NAME| LAST_NAME|HIRE_DATE|     JOB| SALARY|DEPT|
+---+----------+----------+---------+--------+-------+----+
|136|     Hazel|Philtanker|     2000|ST_CLERK| 2200.0|  50|
|165|     David|       Lee|     2000|  SA_REP| 6800.0|  80|
|149|     Eleni|   Zlotkey|     2000|  SA_MAN|10500.0|  80|
|128|    Steven|    Markle|     2000|ST_CLERK| 2200.0|  50|
|164|    Mattea|   Marvins|     2000|  SA_REP| 7200.0|  80|
+---+----------+----------+---------+--------+-------+----+
```

Per ordinare su più colonne abbiamo vari modi:
1. utilizzare una lista di nomi di colonne e una lista di booleani per i relativi ordinamenti
2. utilizzare oggetti di tipo colonna (tramite `col` o direttamente dal dataframe) a cui si applica la funzione `desc` o `asc`
3. utilizare una lista di colonne identificate tramite indice

```python
# 1.
# ordino per data e poi salario
df_employees.orderBy(["HIRE_DATE", "SALARY"], ascending=[False, True]).show(5)

# 2. 
from pyspark.sql.functions import col
df_employees.orderBy(col("HIRE_DATE").asc(), col("SALARY")).show(5)

# 3.
# ordino per nome e cognome
df_employees.orderBy([2,3]).show(5)
```

## Concatenazione e gestione dei letterali
È possibile definire nuove colonne nel dataframe come concatenazione di altre colonne tramite i metodi `concat, lit, concat_ws`. Nel dettagio:
- `lit` restituisce un oggetto di tipo `Column` con valore costante
- `concat` permette di concatenare le colonne passate come parametro, quindi richiede solo oggetti di tipo colonna
- `concat_ws` permette di concatenare le colonne passate come parametro con un separatore costante

```python
from pyspark.sql.functions import lit, concat, concat_ws, when, count
df_employees.withColumn("FULL_NAME", concat(col("FIRST_NAME"), lit(" "), col("LAST_NAME"))).show(5)

df_employees.withColumn("FULL_NAME", concat_ws(" ", col("FIRST_NAME"), col("LAST_NAME"))).show(5)

# Output:
+---+----------+---------+---------+-------+-------+----+----------------+
| ID|FIRST_NAME|LAST_NAME|HIRE_DATE|    JOB| SALARY|DEPT|       FULL_NAME|
+---+----------+---------+---------+-------+-------+----+----------------+
|100|    Steven|     King|     1987|AD_PRES|24000.0|  90|     Steven King|
|101|     Neena|  Kochhar|     1989|  AD_VP|17000.0|  90|   Neena Kochhar|
|102|       Lex|   DeHaan|     1993|  AD_VP|17000.0|  90|      Lex DeHaan|
|103| Alexander|   Hunold|     1990|IT_PROG| 9000.0|  60|Alexander Hunold|
|104|     Bruce|    Ernst|     1991|IT_PROG| 6000.0|  60|     Bruce Ernst|
+---+----------+---------+---------+-------+-------+----+----------------+
```
## Gestione dei null
Per la gestione dei valori `NULL` nel dataframe possiamo utilizziamo principalmente due metodi:
- `dropna` che permette di scartare i record con valori null e richiede due argomenti:
  1. modalità di rimozione (`any` scarta i record che hanno almeno una colonna con `null`, `all` scarta i record che hanno tutte le colonne nulle)
  2. subset di colonne su cui vogliamo considerare i null `subset=[...]`

```python
df_na.dropna("all").show(10)
df_na.dropna("any").show(10)
df_na.dropna("any", subset=["TEST_NA"]).show(10)
```

- `fillna` che permette di attribuire un valore specifico a tutti i `null` e abbiamo tre modalità
  1. passiamo come argomento un unico valore che viene utilizzato come sostituto di tutti i `null`
  2. utilizziamo il metodo 1. a cui passiamo un subset come parametro opzionale per specificare le colonne su cui agire
  3. utilizzare un dizionario come parametro dove specifichiamo come chiave la colonna su cui agire e come valore il valore di filling
```python
#1.
df_na.fillna(100).show(10)

#2.
df_na.fillna(100, subset=["TEST_NA"]).show(10)

#3.
df_na.fillna({"TEST_NA":100, "TEST2_NA": 111}).show(10)
```

Un altro metodo è `replace`, utlizzato non solo per sostituire i valori `null` ma anche valori generici. Il metodo `replace` richiede tre argomenti:
1. valore o lista di valori da rimpiazzare
2. valore o listàdi valori che rimpiazzano
3. subset opzionale dove indichiamo le colonne su cui fare la replace

```python
df_na.replace(1, 500).show(10)

# attenzione: es così rischi di rimpiazzare anche l'ID (quindi meglio specificare anche dove)
df_na.replace(100, 500).show(10)

df_na.replace(1, 500, subset=["TEST_NA"]).show(10)  # METODO MIGLIORE
```

Altro esempio:
```python
# nella colonna dept sostituisco 50 e 60 con 120 (quindi mappo due valori in uno stesso)
df_na.replace([50, 60], 120, "DEPT").show(10)

# sostituisco 90 con 60 e 120 con 130 
df_na.replace([90, 60], [120, 130], "DEPT").show(10)
```

## Gestione dei duplicati

Per verificare se ci sono record duplicati (record identici) e capire quali sono possiamo fare un raggruppamento su tutte le colonne, applicare un conteggio e filtrare la dove il counter è maggiore di 1:
```python
df_na.groupBy(df_na.columns).agg(count("*").alias("count")).filter("count > 1").show()
```

Infine possiamo applicare il metodo `dropDuplicates` o il metodo `distinct` dove entrambi restituiscono il dataframe pulito. Inoltre è possibile specificare una o più colonne su cui cercare i duplicati e scartarli:
```python
df_pulito = df_na.dropDuplicates()
#equivalente
df_pulito = df_na.distinct()

# considero record duplicati se hanno id e department uguale
df_pulito = df_na.dropDuplicates(["ID", "DEPT"])
```


## Join in Spark
Esempio di `INNER JOIN`:
```python
df_air_data = df_air_quality.join(df_sensors, "idsensore", how="inner")

# Se le chiavi di join sono diverse avremmo dovuto fare
# df_air_data = df_air_quality.join(df_sensors, df_air_quality["idsensore"] == df_sensors["idsensore"], how="inner")

#Output
+---------+-------------------+------+-----+-----------+---------------+-----------+------+
|idsensore|               data|valore|stato|idoperatore|nometiposensore|unitamisura|comune|
+---------+-------------------+------+-----+-----------+---------------+-----------+------+
|    17126|2024-05-24 01:00:00|   0.9|   VA|          1|        Benzene|      µg/m³|Milano|
|    17126|2024-05-24 02:00:00|   1.1|   VA|          1|        Benzene|      µg/m³|Milano|
|    17126|2024-05-24 03:00:00|   1.0|   VA|          1|        Benzene|      µg/m³|Milano|
|    17126|2024-05-24 04:00:00|   1.0|   VA|          1|        Benzene|      µg/m³|Milano|
|    17126|2024-05-24 05:00:00|   0.7|   VA|          1|        Benzene|      µg/m³|Milano|
+---------+-------------------+------+-----+-----------+---------------+-----------+------+
```

Esempio di `LEFT JOIN`:
```python
df_air_data_left = df_air_quality.join(df_sensors, "idsensore", how="left_outer")
```

Esempio di `RIGHT JOIN`:
```python
df_air_data_left = df_air_quality.join(df_sensors, "idsensore", how="right_outer")
```

Esempio di `SEMI JOIN` (restituisce i record della tabella di sinistra per cui esiste un corrispondente nella tabella di destra, senza aggiungere campi della tabella di destra e senza duplicare le righe di sinistra in caso di match multipli):
```python
df_semi = df_sensors.join(df_air_quality, "idsensore", how="semi")
```

Esempio di `CROSS JOIN`:
```python
df_cross = df_sensors.join(df_air_quality, how="cross")
```