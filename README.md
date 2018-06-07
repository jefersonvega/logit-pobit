## Ejercicio (Logit-Probit)

Primero cargamos la base de datos

    load("logitprobit.Rdata")
    
Para este ejercicio vamos a seleccionar 5 variables de la base de datos de la gran encuesta integrada de hogares (GEIH) del año 2015 y colocarles unos nombres mas concretos

```bash

    datos1<-datos[,c("P7170s1","Inglabo","P6040","P6430","P6880")]
    colnames(datos1)<-c("satisfecho","ingreso","edad","forma","donde")
    dim(datos1); head(datos1)

```

Breve descripción de las variables

```bash

    P7170s1: ¿Está satisfecho con su trabajo actual?
    Inglabo: Ingresos laborales
    P6O40: ¿Cuantos años cumplidos tiene?
    P6430: En este trabajo es:
    P6880:Donde realiza principalmente su trabajo:
   
   ```
   
1. 787044     
2.  5

|Satisfecho|Ingreso|Edad|Forma |Donde | 
|--|--|--|--|--|
|1 |1500000 |41 |2| 7|
|NA|NA|42|NA|NA|
|NA|NA|9|NA|NA|
|NA|NA|8|NA|NA|
|1|460000|26|4|4|
|NA|NA|29|NA|NA|

Los _NA's_ son valores perdidos por lo que es necesario determinar que hacer con los datos. Al tratarse de la Encuesta Integrada de Hogares, esos datos corresponden a las personas que no están en edad de trabajar por lo que lo mejor es omitirlos:

    datos2 <- na.omit(datos1);dim(datos2); head(datos2)

1. 317096    

2. 5


|Satisfecho  |Ingreso  |Edad |Forma |Donde | 
|--|--|--|--|--|
| 1|1500000  |41 |2 |7 | 
| 1|460000 |26 |4 |4 |
| 2|600000 |36 |4 |4 |
| 2|280000 |37 |4 |2 |
| 1|100000 |71 |4 |6 |
|1 |616000 |21 |1 |7 |



Algunas variables seleccionadas son categóricas por lo que necesitamos convertirlas adecuadamente en factores, también, requerimos identificar correctamente cada categoría:

```bash

    datos2$satisfecho<-replace(datos2$satisfecho,datos2$satisfecho==2,0)
    datos2$satisfaccion<-datos2$satisfecho
    datos2$satisfaccion<-replace(datos2$satisfaccion,datos2$satisfaccion==1,
    "Satisfecho")
    datos2$satisfaccion<-replace(datos2$satisfaccion,datos2$satisfaccion==0,
    "Insatisfecho")
    datos2$satisfaccion<-factor(datos2$satisfaccion)
    summary(datos2$satisfaccion)

```

**Insatisfecho**
*51668*

**Satisfecho**
*265428*


En relación a las variables categoricas forma y donde, vamos a relizar unas agrupaciones 

```
 table(datos2$forma)

     1      2      3      4      5      6      7      8      9 
123919  16346  12455 148342  10782     94      9   4839    310 
 
 table(datos2$donde)

     1      2      3      4      5      6      7      8      9     10     11 
 36134  34719    815  23309  16791  19121 147079  25370  12783    707    268 
```

```bash
  

    datos2$forma<-replace(datos2$forma,datos2$forma==1,"empleado")
    datos2$forma<-replace(datos2$forma,datos2$forma==2,"empleado")
    datos2$forma<-replace(datos2$forma,datos2$forma==3,"empleado")
    datos2$forma<-replace(datos2$forma,datos2$forma==4,"cuenta propia")
    datos2$forma<-replace(datos2$forma,datos2$forma==5,"cuenta propia")
    datos2$forma<-replace(datos2$forma,datos2$forma==6,"otros")
    datos2$forma<-replace(datos2$forma,datos2$forma==7,"otros")
    datos2$forma<-replace(datos2$forma,datos2$forma==8,"pensionado")
    datos2$forma<-replace(datos2$forma,datos2$forma==9,"otros")
    datos2$forma<-factor(datos2$forma)
    summary(datos2$forma)
    
```
    
**Cuenta propia**

*159124*

**Empleado**

*152720*

**Otros**

*413*

**Pensionado**

*4839*

```bash

    datos2$donde<-replace(datos2$donde,datos2$donde==1, "vivienda")
    datos2$donde<-replace(datos2$donde,datos2$donde==2, "vivienda")
    datos2$donde<-replace(datos2$donde,datos2$donde==3, "ambulante")
    datos2$donde<-replace(datos2$donde,datos2$donde==4, "ambulante")
    datos2$donde<-replace(datos2$donde,datos2$donde==5, "ambulante")
    datos2$donde<-replace(datos2$donde,datos2$donde==6, "ambulante")
    datos2$donde<-replace(datos2$donde,datos2$donde==7, "local")
    datos2$donde<-replace(datos2$donde,datos2$donde==8, "rural")
    datos2$donde<-replace(datos2$donde,datos2$donde==9, "exteriores")
    datos2$donde<-replace(datos2$donde,datos2$donde==10, "exteriores")
    datos2$donde<-replace(datos2$donde,datos2$donde==11, "exteriores")
    datos2$donde<-factor(datos2$donde)
    summary(datos2$donde)

```

**Ambulante**

*60036*

**Exteriores**

*13758*

**Local**

*147079*

**Rural**

*25370*

**Vivienda**

*70853*

Es necesario en este punto del trabajo tomar algunas determinaciones, como la edad de los encuestados y el nievel salarial de los mismos. Por lo tanto, crearemos un intervalo para la edad de los 18 a los 60 años, y para el ingreso desde $500 000, hasta $5 000 000.

```bash

    salario=subset(datos2,ingreso>=500000 & ingreso<=5000000)
    edades=subset(salario,edad>=18 & edad<=60)
    dim(edades)

 1. 200653
 2. 6

    summary(edades)

  satisfecho       ingreso             edad                 forma       
 Min.   :0.000   Min.   : 500000   Min.   :18.00   cuenta propia: 72214  
 1st Qu.:1.000   1st Qu.: 644350   1st Qu.:28.00   empleado     :125811  
 Median :1.000   Median : 800000   Median :36.00   otros        :    45  
 Mean   :0.876   Mean   :1083091   Mean   :37.26   pensionado   :  2583  
 3rd Qu.:1.000   3rd Qu.:1200000   3rd Qu.:46.00                         
 Max.   :1.000   Max.   :5000000   Max.   :60.00                         
        donde              satisfaccion   
 ambulante : 36133   Insatisfecho: 24883  
 exteriores: 10834   Satisfecho  :175770  
 local     :119246                        
 rural     :  8954                        
 vivienda  : 25486       
 
 ```
            
A continuación vamos a generar una variable categorica para la edad que posteriormente usaremos solo para propositos de estadistica descriptiva

    etapa=vector()
    etapa[edades$edad<30]="Jovenes"
    etapa[edades$edad>=30 & edades$edad<50]="Adultos"
    etapa[edades$edad>=50]="Adultos Mayores"
    edades$etapa=as.factor(etapa)

**Adultos**

*105520*

**Adultos Mayores**

*35530*

**Jovenes**

*59603*

```
```

Ahora se realizara un analisis descriptivo de las variables a través de tablas de contingencia

    with(edades, addmargins(prop.table( table(etapa,satisfaccion))*100))

|  | Insatisfecho |Satisfecho | Sum|
|--|--|--|--|
| Adultos |  6.328338 |46.259961  |52.588299 |
| Adultos Mayores| 1.571369   |16.135817 | 17.707186 |
| Jovenes| 4.501303   | 25.203212  |29.704515 | 
|sum|12.401011 |87.598989 |100.000000 | 


    with(edades, addmargins(prop.table( table(forma,satisfaccion))*100))

|  |Insatisfecho  |Satisfecho  |Sum |
|--|--|--|--|
|Cuenta propia  |5.127758  |30.86174  |35.98949  |
|Empleado  |7.100317  |55.60046  |62.70078  |
|Otros  | 0.005980474  |0.01644630  |0.02242678  |
|Pensionado| 0.1669549|1.120342   |1.287297  |
|Sum    | 12.40101 |87.59899   |100.00000000| 


    with(datos2, addmargins(prop.table( table(donde,satisfaccion))*100))

|  |Insatisfecho  |Satisfecho |Sum |
|--|--|--|--|
|Ambulante  |3.1427390|14.8649659 |18.0077048 |
|Exteriores |0.8387614  |4.5606096 |5.3993711  |
|Local |6.2794974 |53.1494670 |59.4289644 |
|Rural |0.5432264 |3.9192038 |4.4624302 |
|Vivienda | 1.5967865 |11.1047430 |12.7015295 |
|Sum |12.4010107 |87.5989893 |100.000000 | 


Para complementar este análisis graficaremos

``` bash

    par(lab=c(25,25,2))
    with(edades,barplot(prop.table(table(satisfaccion,etapa))*100,beside=T,
    main="Satisfacción según la clasificación de edad",xlab="Clasificación",
    ylab="Porcentaje",col=c("green","blue"),ylim=c(0,50)))
    legend("topright",c("Insatisfecho","satisfecho"),cex=1.6,col=c("green","blue"),
    lty=1:1)

```

![png](edades.jpg)


``` bash


     par(lab=c(25,25,2))
     with(edades,barplot(prop.table(table(satisfaccion,forma))*100,beside=T,
     main="Satisfacción según el tipo de trabajo",xlab="Clasificación",
     ylab="Porcentaje",col=c("green","blue"),ylim=c(0,56)))

    legend("topright",c("Insatisfecho","satisfecho"),cex=1.6,fill=c("green","blue"),
    lty=1:1)

```

![png](forma.jpg)


``` bash

    with(edades,barplot(prop.table(table(satisfaccion,donde))*100,beside=T,
    main="Satisfacción según el lugar de trabajo",xlab="Clasificación",
    ylab="Porcentaje",col=c("red","grey")))

    legend("topright",c("Insatisfecho","satisfecho"),cex=1.0,fill=c("red","grey"),
    lty=1:1)

```

![png](donde.jpg)


Una mirada a las variables cuantitativas

```bash
    with(edades,hist(ingreso*1e-6, main = "Ingreso (en Millones)", xlab="", freq=F,
    col=c("blue")))

```

![png](ingreso.jpg)

Grafica de densidad de Kernel

```bash

    
    d <- density(edades$edad)
       
    d <- density(edades$edad)
    plot(d, main=" Densidad de la edad")
    polygon(d, col="blue", border="green")
    rug(datos2$edad, col="brown")
    
```

![png](densidad.jpg)


ahora veremos las medidas de dispersión


```bash

    stats <- function(x){
    x <- x
    m <- mean(x)
    n <- length(x)
    s <- sd(x)
    skew <- sum((x-m)^3/s^3)/n
    kurt <- sum((x-m)^4/s^4)/n - 3
    return(c(tamaño=n, media=m, "desviación estándar"=s, simetría=skew,
    kurtosis=kurt))}
    
    sapply(edades, stats)
```

```bash
                         satisfecho      ingreso          edad        forma
tamaño               2.006530e+05 2.006530e+05  2.006530e+05 2.006530e+05
media                8.759899e-01 1.083091e+06  3.726093e+01           NA
desviación estándar  3.295939e-01 7.326291e+05  1.109725e+01 5.478201e-01
simetría            -2.281523e+00 2.462127e+00  2.304941e-01           NA
kurtosis             3.205363e+00 6.879880e+00 -1.012533e+00           NA
                           donde satisfaccion        etapa
tamaño              2.006530e+05 2.006530e+05 2.006530e+05
media                         NA           NA           NA
desviación estándar 1.146148e+00 3.295939e-01 8.778184e-01
simetría                      NA           NA           NA
kurtosis                      NA           NA           NA
There were 16 warnings (use warnings() to see them)

```

veamos en detalle las variables que nos interesan


|  | ingreso | edad |
|--|--|--|
|tamaño  |200653|200653|
|Min. |500000|18 |
|1 st Qu . |644350 |28.00 |
|Mediana|800000 |36.00  |
|media   |1083091 |37.26|
|3rd Qu.|1200000|46.00|
|Max. |5000000 |60.00 |
|desviación estándar |732629|11.097 |
|simetría |2.462127e+00 |2.304941e-01  | 
|kurtosis  |6.879880e+00 |-1.012533e+00 | 


----------
construcción de los modelos

    # Modelo
    logit <- glm(satisfecho ~ ingreso + edad + forma+ 
          donde, data = edades, family = "binomial")
          summary(logit)

```bash
Call:
glm(formula = satisfecho ~ ingreso + edad + forma + donde, family = "binomial", 
    data = edades)

Deviance Residuals: 
    Min       1Q   Median       3Q      Max  
-3.1899   0.3789   0.4902   0.5620   1.0633  

Coefficients:
                  Estimate Std. Error z value Pr(>|z|)    
(Intercept)      1.794e-01  3.092e-02   5.801 6.58e-09 ***
ingreso          6.243e-07  1.520e-08  41.061  < 2e-16 ***
edad             1.928e-02  6.522e-04  29.554  < 2e-16 ***
formaempleado    2.369e-01  1.560e-02  15.185  < 2e-16 ***
formaotros      -5.765e-01  3.419e-01  -1.686   0.0918 .  
formapensionado  1.591e-01  7.019e-02   2.266   0.0234 *  
dondeexteriores  1.225e-01  3.040e-02   4.031 5.56e-05 ***
dondelocal       4.452e-01  1.822e-02  24.429  < 2e-16 ***
donderural       4.100e-01  4.116e-02   9.961  < 2e-16 ***
dondevivienda    3.907e-01  2.364e-02  16.530  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 150425  on 200652  degrees of freedom
Residual deviance: 145492  on 200643  degrees of freedom
AIC: 145512

Number of Fisher Scoring iterations: 5

```

    probit <- glm(satisfecho ~ ingreso + edad + forma+ 
           donde, data = edades, family = binomial(link = "probit"))
           summary(probit)
```bash
Call:
glm(formula = satisfecho ~ ingreso + edad + forma + donde, family = binomial(link = "probit"), 
    data = edades)

Deviance Residuals: 
    Min       1Q   Median       3Q      Max  
-3.3014   0.3868   0.4925   0.5614   1.0285  

Coefficients:
                  Estimate Std. Error z value Pr(>|z|)    
(Intercept)      2.408e-01  1.657e-02  14.533  < 2e-16 ***
ingreso          2.866e-07  7.045e-09  40.682  < 2e-16 ***
edad             1.033e-02  3.458e-04  29.870  < 2e-16 ***
formaempleado    1.249e-01  8.350e-03  14.959  < 2e-16 ***
formaotros      -3.500e-01  2.021e-01  -1.732   0.0832 .  
formapensionado  8.039e-02  3.744e-02   2.147   0.0318 *  
dondeexteriores  6.657e-02  1.681e-02   3.960 7.49e-05 ***
dondelocal       2.404e-01  9.937e-03  24.188  < 2e-16 ***
donderural       2.176e-01  2.194e-02   9.919  < 2e-16 ***
dondevivienda    2.081e-01  1.283e-02  16.212  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 150425  on 200652  degrees of freedom
Residual deviance: 145656  on 200643  degrees of freedom
AIC: 145676

Number of Fisher Scoring iterations: 5
```

de donde debemos elegir con cual quedarnos 

    AIC(logit,probit)

|  | df   |AIC |
|--|--|--|
|logit  | 10   |145511.7 |
|probit | 10  |145676.0 | 

pocedemos con los test de independencia

    library(aod)
    
    wald.test(b = coef(logit), Sigma = vcov(logit), Terms = 4:5)# Para forma de
    trabajo= empleado y otros
```bash

Wald test:
----------

Chi-squared test:
X2 = 235.0, df = 2, P(> X2) = 0.0
```

 
    wald.test(b = coef(logit), Sigma = vcov(logit), Terms = 6)# Para forma de
    trabajo =otros
```bash

Wald test:
----------

Chi-squared test:
X2 = 5.1, df = 1, P(> X2) = 0.023
```
    
    wald.test(b = coef(logit), Sigma = vcov(logit), Terms = 7:8) # Para lugar de
    trabajo= exteriores y local 
```bash

Wald test:
----------

Chi-squared test:
X2 = 633.8, df = 2, P(> X2) = 0.0
```
    
    wald.test(b = coef(logit), Sigma = vcov(logit), Terms = 9:10) # Para lugar de
    trabajo= rural y vivienda
```bash

Wald test:
----------

Chi-squared test:
X2 = 318.6, df = 2, P(> X2) = 0.0
```
summary(logit)

```bash

Call:
glm(formula = satisfecho ~ ingreso + edad + forma + donde, family = "binomial", 
    data = edades)

Deviance Residuals: 
    Min       1Q   Median       3Q      Max  
-3.1899   0.3789   0.4902   0.5620   1.0633  

Coefficients:
                  Estimate Std. Error z value Pr(>|z|)    
(Intercept)      1.794e-01  3.092e-02   5.801 6.58e-09 ***
ingreso          6.243e-07  1.520e-08  41.061  < 2e-16 ***
edad             1.928e-02  6.522e-04  29.554  < 2e-16 ***
formaempleado    2.369e-01  1.560e-02  15.185  < 2e-16 ***
formaotros      -5.765e-01  3.419e-01  -1.686   0.0918 .  
formapensionado  1.591e-01  7.019e-02   2.266   0.0234 *  
dondeexteriores  1.225e-01  3.040e-02   4.031 5.56e-05 ***
dondelocal       4.452e-01  1.822e-02  24.429  < 2e-16 ***
donderural       4.100e-01  4.116e-02   9.961  < 2e-16 ***
dondevivienda    3.907e-01  2.364e-02  16.530  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 150425  on 200652  degrees of freedom
Residual deviance: 145492  on 200643  degrees of freedom
AIC: 145512

Number of Fisher Scoring iterations: 5

```

    l <- cbind(0, 0, 0, 0, 1, -1, 0, 0,0,0)
    wald.test(b = coef(logit), Sigma = vcov(logit), L = l)
```bash

Wald test:
----------

Chi-squared test:
X2 = 5.7, df = 1, P(> X2) = 0.017

```



Estimaciones con nuevas observaciones

```bash

    newdatap1 <- data.frame(ingreso = rep(seq(from = min(edades$ingreso),
    to = max(edades$ingreso), length.out =20),4*5),
    edad = rep(seq(from = min(edades$edad),
    to = max(edades$edad), length.out =20), 4*5),
    forma= rep(rep(c("empleado","cuenta propia","otros","pensionado"), each =20),
    5),donde= rep(rep(c("vivienda","ambulante", "local","rural","exteriores"),
    each =20), 4))
    
    head(newdatap1)
    
```

```bash

    newdatap1[, c("p", "se")] <- predict(logit, newdatap1, type = "response",
    se.fit = TRUE)[-3]
    head(newdatap1)

```


|ingreso   |  edad   | forma |donde  | p |se |
|--|--|--|--|--|--|
|500000.0 |18.00000  |empleado |vivienda  |0.8124699 |0.003765462 | 
|736842.1 |20.21053 |empleado |vivienda  |0.8397818 |0.003215223 |
|973684.2 |22.42105 |empleado |vivienda  |0.8637829 |0.002782295 | 
|1210526.3 |24.63158 |empleado |vivienda  |0.8846823 |0.002446615 |
|1447368.4 |26.84211 |empleado |vivienda  |0.9027362 |0.002184442 |
|1684210.5 |29.05263 |empleado |vivienda  |0.9182249  |0.001973296 |

```bash

    ggplot(newdatap1, aes(x = ingreso, y = p, colour =forma)) +
    geom_line() + facet_wrap(~donde)

```

![png](predict1.jpg)

