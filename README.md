## Ejercicio (Logit-Probit)

Primero cargamos la base de datos

    load("logitprobit.Rdata")
Para este ejercicio vamos a seleccionar 5 variables de la base de datos y colocarles 
unos nombres mas concretos

    datos1<-datos[,c("P7170s1","Inglabo","P6040","P6430","P6880")]
    colnames(datos1)<-c("satisfecho","ingreso","edad","forma","donde")
    dim(datos1); head(datos1)
1. 787044     
2.  5

|Satisfecho|Sexo| Ingreso|Forma |Donde | 
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

|Satisfecho  |Sexo  |Ingreso |Forma |Donde | 
|--|--|--|--|--|
| 1|1500000  |41 |2 |7 | 
| 1|460000 |26 |4 |4 |
| 2|600000 |36 |4 |4 |
| 2|280000 |37 |4 |2 |
| 1|100000 |71 |4 |6 |
|1 |616000 |21 |1 |7 |

Algunas variables seleccionadas son categóricas por lo que necesitamos convertirlas adecuadamente en factores, también, requerimos identificar correctamente cada categoría:

    datos2$satisfecho<-replace(datos2$satisfecho,datos2$satisfecho==2,0)
    datos2$satisfaccion<-datos2$satisfecho
    datos2$satisfaccion<-replace(datos2$satisfaccion,datos2$satisfaccion==1,
    "Satisfecho")
    datos2$satisfaccion<-replace(datos2$satisfaccion,datos2$satisfaccion==0,
    "Insatisfecho")
    datos2$satisfaccion<-factor(datos2$satisfaccion)
    summary(datos2$satisfaccion)
**Insatisfecho**

*51668*

**Satisfecho**

*265428*

  

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
**Cuenta propia**

*159124*

**Empleado**

*152720*

**Otros**

*413*

**Pensionado**

*4839*

![png](menosdesarrollados.jpg)

a ver
