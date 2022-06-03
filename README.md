# FL-ScaleMamba

La privacidad es una de las mayores prioridades de la sociedad actual y a menudo se ve comprometida por la centralización de datos necesaria para el entrenamiento de *Machine Learning (ML)*. *Federated Learning (FL)* es una técnica ampliamente utilizada para evitar esta centralización, pero se han encontrado limitaciones en cuanto a la privacidad de los datos y la seguridad del modelo.

En este repositorio se recoge parte del código utilizado para la implementación de un sistema completo capaz de abordar los problemas de privacidad y seguridad simultánemante. Dicho sistema utiliza *Secure Multiparty Computation* para abordar el problema de privacidad, realizando la agregación mediante SCALE-MAMBA$^1$, y de entre las técnicas para proteger la seguridad utiliza *Krum*$^2$, por su adaptabilidad a la herramienta seleccionada y al caso de uso que se quiere responder. 

## Caso de uso
Un servidor, $P_0$, dispone de un modelo de *ML* que desea entrenar. El resto de participantes, $P_1,...,P_n$, son los clientes que disponen de datos para entrenar dicho modelo. Para mantener los datos privados se realiza un entrenamiento descentralizado basado en la técnica de *FL*.   El proceso se divide en múltiples rondas donde el servidor central se encarga de actualizar el vector de parámetros global $\omega \in \mathbb{R}^d$. Para proteger el modelo de hasta $f < \frac{n-2}{2}$ *byzantine adversaries* , en lugar de realizar la agregación mediante la media ponderada se realiza utilizando la técnica de *Krum*, mientras se mantiene la privacidad de los datos realizándolo mediante *Secure Multiparty Computation*. Se utiliza el *framework* de SCALE-MAMBA.

Cuando el caso de uso particular requiere un cambio de los parámetros $n$, $f$ y $d$ se debe modificar directamente el códgio **krum.mpc** donde así se indica. Por defecto el programa toma: 
- $n = 7$ 
- $f = 2$ 
- $d = 5$


## Esquema de implementación

El entrenamiento federado se realiza mediante un proceso iterativo de $T$ rondas, donde $t=1,...,T$ es la variable indicativa de la ronda actual. El proceso se resume en los siguientes pasos:


1. El servidor propone un entrenamiento y se pone en contacto con los clientes para iniciar el proceso de *FL* y crear la red de SCALE-MAMBA.
2. Comienza el proceso iterativo. El servidor envía el vector de parámetros global de la ronda actual, $\omega^t$, a los clientes.
3. Cada cliente $P_i$, para todo $i\in$ {$1,...,n$}, entrena  localmente el modelo en base al vector de parámetros recibido, $\omega^t$, y a la porción de datos de la que dispone para la ronda $t$, $D_i^t$ . El resultado es el vector de parámetros local de dicho cliente en la ronda $t$: $\omega_i^t$. 
4. Una vez finalizado el entrenamiento local, comienza la fase de la agregación mediante SCALE-MAMBA. Los clientes aportan la entrada de datos al programa de *SMPC*, los parámetros locales $\omega_1^t,...,\omega_n^t$, y el servidor es el único que recibe el resultado de la función, es decir, el vector de parámetros global resultante tras aplicar *Krum* sobre los parámetros locales: $\omega^{t+1}$. Para ello se ejecuta el programa **krum.mpc$^3$** desde **SCALE-MAMBA**.
5. Se vuelve al paso 2, hasta que $t=T$ y finaliza el proceso.

-----------------------------

$^1$ https://github.com/KULeuven-COSIC/SCALE-MAMBA.git

$^2$ https://www.scopus.com/inward/record.uri?eid=2-s2.0-85046992847&partnerID=40&md5=f27d842300e465dc1c6cd95864b21cfc


$^3$ El código escrito en el lenguaje Mamba que se debe ejecutar esta disponible en este repositorio dentro del archivo **krum.mpc**. 
