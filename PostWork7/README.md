## Postwork 7. Predicciones de la temperatura global

### OBJETIVO

- Estimar modelos ARIMA y realizar predicciones

#### DESARROLLO
Utilizando el siguiente vector numérico, realiza lo que se indica:
```R
url = "https://raw.githubusercontent.com/beduExpert/Programacion-R-Santander-2022/main/Sesion-07/Data/global.txt"
Global <- scan(url, sep="")
```
- [global.txt](/global.txt)

1) Crear una objeto de serie de tiempo con los datos de Global. La serie debe ser mensual 
comenzado en Enero de 1856

"En este apartado se construirá la serie de tiempo con los datos de Global.
La serie comenzó en enero de 1856 y su periodicidad fue de tipo mensual:"

```r
Global.ts <- ts(Global, start = c(1856,1), freq = 12)
Global.ts
class(Global.ts)
##[1] "ts"
```


2) Realizar una gráfica de la serie de tiempo anterior de 2005

#### A continuación se realizará el gráfico de la serie de tiempo elaborada en el apartado anterior:
```r
plot(Global.ts, 
     main = "Serie de tiempo de los datos de Global", 
     xlab = "Tiempo",
     ylab = "Temperatura",
     sub = "Mensual de Enero de 1856 - Diciembre de 2005",
     col = "blue")
grid()
```
<p align="center">
  <img src="img/pw7img1.png" alt="Graph" width="480" height="357">
</p>

#### Con base en los resultados obtenidos, puede concluirse que la serie no es estacionaria debido a que ésta no muestra un comportamiento estable a lo largo del tiempo, es decir su media y varianza no son constantes en el tiempo

3) Realizar una gráfica de la serie de tiempo anterior, transformando a la 
primera diferencia.

```r
plot(diff(Global.ts), xlab = "", ylab = "",col = "blue")
title(main = "Serie Diferenciada de los datos de Global",
      xlab = "Tiempo", ylab = "Dif Serie",
      sub = "Gráfica de la serie diferenciada de Primer Órden",
      )
grid()
```
<p align="center">
  <img src="img/pw7img2.png" alt="Graph" width="480" height="357">
</p>

4) ¿Consideras que la serie es estacionaria en niveles o en primera diferencia?

#### Con base en los resultados obtenidos, puede concluirse que la serie es estacionaria en primera diferencia debido a que ésta muestra un comportamiento estable a lo largo del tiempo, es decir, su media y varianza son constantes en el tiempo.

5) Con base en tu respuesta anterior, obten las funciones de autocorrelación y autocorrelación parcial:

```r
acf(diff(Global.ts))
```
<p align="center">
  <img src="img/pw7img3.png" alt="Graph" width="480" height="357">
</p>

```r
pacf(diff(Global.ts))
```
<p align="center">
  <img src="img/pw7img4.png" alt="Graph" width="480" height="357">
</p>

>6. De acuerdo con el gráfico de la función de autocorrelación puede estimarse que el modelo ARIMA a construir deberá contener 1 término autorregresivo. 
>Por otro lado, la gráfica de la función de autocorrelación parcial sugiere que el modelo ARIMA debe utilizar entre 1 y 4 rezagos para el proceso de media móvil. Finalmente, el modelo ARIMA que se construirá tendrá un orden de integración-diferenciación de uno.
>Por todo lo anterior, se construirán cuatro modelos ARIMA, y se seleccionará el mejor con base en el criterio del AIC: 

```r
arima1 <- arima(Global.ts,order = c(1,1,1))
arima1
# Call:
#   arima(x = Global.ts, order = c(1, 1, 1))
# 
# Coefficients:
#   ar1      ma1
# 0.3797  -0.8700
# s.e.  0.0433   0.0293
# 
# sigma^2 estimated as 0.01644:  log likelihood = 1142.13,  aic = -2278.26

arima2 <- arima(Global.ts,order = c(1,1,2))
arima2
# Call:
#   arima(x = Global.ts, order = c(1, 1, 2))
# 
# Coefficients:
#   ar1      ma1     ma2
# 0.7593  -1.2992  0.3190
# s.e.  0.0354   0.0487  0.0452
# 
# sigma^2 estimated as 0.01616:  log likelihood = 1157.48,  aic = -2306.96

arima3 <- arima(Global.ts,order = c(1,1,3))
arima3
# Call:
#   arima(x = Global.ts, order = c(1, 1, 3))
# 
# Coefficients:
#   ar1      ma1     ma2     ma3
# 0.8171  -1.3518  0.3087  0.0575
# s.e.  0.0406   0.0490  0.0407  0.0326
# 
# sigma^2 estimated as 0.01613:  log likelihood = 1158.94,  aic = -2307.88

arima4 <- arima(Global.ts,order = c(1,1,4))
arima4
# Call:
#   arima(x = Global.ts, order = c(1, 1, 4))
# 
# Coefficients:
#   ar1      ma1     ma2     ma3     ma4
# 0.8704  -1.4030  0.3478  0.0053  0.0600
# s.e.  0.0335   0.0411  0.0438  0.0401  0.0273
# 
# sigma^2 estimated as 0.01609:  log likelihood = 1161.2,  aic = -2310.39

```

>7. Con base en los resultados anteriores, puede concluirse que el modelo ARIMA 4 tiene el menor AIC (-2310.39). En seguida, se realizará el ajuste del modelo:
```r
fit <- arima(Global.ts, order = c(1, 1, 4))
fit
# Call:
#   arima(x = Global.ts, order = c(1, 1, 4))
# 
# Coefficients:
#   ar1      ma1     ma2     ma3     ma4
# 0.8704  -1.4030  0.3478  0.0053  0.0600
# s.e.  0.0335   0.0411  0.0438  0.0401  0.0273
# 
# sigma^2 estimated as 0.01609:  log likelihood = 1161.2,  aic = -2310.39
```

>La ecuación del modelo es la siguiente:
```
 Δx^_t = 0.8704*Δx_t-1 - 1.4030*u_t-1 + 0.3478*u_t-2 + 0.0053*u_t-3  + 0.0600*u_t-4
```

>8. Finalmente se realizarán los pronósticos para el año 2006 y su respectivo gráfico:

```r
pr <- predict(fit,12)$pred
pr
# Jan       Feb       Mar       Apr       May       Jun       Jul       Aug       Sep
# 2006 0.3944191 0.4109006 0.4241679 0.4249647 0.4256583 0.4262620 0.4267875 0.4272449 0.4276430
# Oct       Nov       Dec
# 2006 0.4279895 0.4282911 0.4285537

ts.plot(cbind(window(Global.ts, start = 2000), pr), col = c("blue", "red"), xlab = "")
title(main = "Pronósticos para el año 2006 de la serie de los datos de Global",
      xlab = "Mes",
      ylab = "Mediciones de Global")
```
<p align="center">
  <img src="img/pw7img5.png" alt="Graph" width="480" height="357">
</p>

#### Ir al archivo de código fuente
- [PostWork 7](https://github.com/alsolisc/Postworks/tree/main/src/PostWork7.R)
