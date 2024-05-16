# Optimización de lectura de grandes datos usando Pandas
Tanto Python como `Pandas` están bien optimizados para leer millones de registros de forma rápida y eficaz. Sin embargo, muchas veces es posible optimizar aún más. 

La siguiente imagen es un caso real de una base de datos de más de 20GB cuya lectura a partir de CSV toma 20 mins y gracias a la siguiente guia logró disminuirse a casi 2 mins.




Existen ciertas librerias para ello. Puedes comenzar usando estás pero desde mi experiencia antes de usarlas debes saber qué quieres hacer después de la lectura ya que algunas tienen limitaciones o son muy estrictas con los tipos de datos, lo que puede dificultar los pasos siguientes.

Por tanto, a continuación se muestra una guía de cómo optimizar la lectura de grandes datos **usando únicamente `Pandas`**.

---
En un primer instante es necesario leer los datos en crudo para iniciar con la optimización.

1. Realiza un análisis exploratorio haciendo enfasis en identificar los mejores tipos de datos para cada campo. Este paso es sumamente importante ya que a partir de aquí es posible determinar qué campos son optimizables. 
   * Por ejemplo, si una variable con __1s y 0s__ `Pandas` la identificó como [float](https://pandas.pydata.org/docs/reference/api/pandas.Float64Dtype.html) (suele pasar) es muchísimo mejor cambiar el tipo a [boolean](https://pandas.pydata.org/docs/reference/api/pandas.BooleanDtype.html)
   * Si existe el campo _edad_ o algún otro cuyos valores no son tan altos cambialo a [`np.int8`](https://numpy.org/doc/stable/reference/arrays.scalars.html#numpy.int8) o [`np.int16`](https://numpy.org/doc/stable/reference/arrays.scalars.html#numpy.int16).
   * Identica los campos de fecha y cambialos a `datetime` o `timedelta` según sea el caso
   * En caso que un campo de tipo string cumpla con las reglas de [categorical](https://pandas.pydata.org/docs/user_guide/categorical.html) (similar al factor en R), cambíalo. Este paso optimiza muchísimos procesos y es muy recomedable analizar si [aplica](https://stackoverflow.com/questions/30601830/when-to-use-category-rather-than-object).

**Los casos más sensibles son los númericos, pues la optimización de los campos depende del rango de valores qué tiene cada campo y si esta bien que haya `NA`. Justo para eso es el análisis exploratorio.**

2. Corrige errores en los campos de tipo `object`. Por ejemplo, el siguiente bloque de código muestra como es posible hacer algunos cambios en un campo de tipo `object` para posteriormente convertirlo a [boolean](https://pandas.pydata.org/docs/user_guide/boolean.html)
   
```python
df['Casado'] = df['Casado'].replace({1:True,0:False,'VERDADERO':True,'FALSO':False,'TRUE':True,'FALSE':False,'True':True,'False':False,'false':False,'true':True,'1':True,'0':False},regex=True)
df['Casado'] = df.Casado.astype("boolean")
```
3. Después de haber optimizado los campos debes guardar el dataset usando la extensión **[FEATHER](https://arrow.apache.org/docs/python/feather.html)** en caso que solo lo vayas a usar en Python o R. En caso que no sea así podrías usar **[alguna de estas](https://towardsdatascience.com/the-best-format-to-save-pandas-data-414dca023e0d)**.
___



Listo. Puedes comprobar cómo el tamaño en memoría disminuye drasticamente y el tiempo de lectura se reduce significativamente.


