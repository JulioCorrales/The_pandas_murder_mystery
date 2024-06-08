<div align="center">

# 🔍 Investigación de Crimen en Pandas City 🕵️‍♂️

</div>

Importación de Librerías y Carga de Datos
El primer paso es importar la librería pandas y ajustar la configuración para permitir la visualización completa del texto en las columnas. Luego, se cargan los conjuntos de datos relevantes para la investigación.

import pandas as pd
pd.set_option('display.max_colwidth', None) # Esto es para poder ver todo el texto en una columna

# Cargamos los datasets que utilizaremos durante la investigación
crime_scene_report     = pd.read_csv("./datasets/Crime Scene report.csv")
drivers_license        = pd.read_csv("./datasets/Drivers license.csv")
facebook_event_checkin = pd.read_csv("./datasets/facebook_event_checkin.csv")
getfitnow_checkin      = pd.read_csv("./datasets/GetFitNow check in.csv")
getfitnow_members      = pd.read_csv("./datasets/GetFitNow members.csv")
income                 = pd.read_csv("./datasets/income.csv")
interview              = pd.read_csv("./datasets/Interviews.csv")
person                 = pd.read_csv("./datasets/Person.csv")


Análisis Inicial de los DataFrames
Se utiliza el método .info() para obtener una descripción general del dataset crime_scene_report, seguido por la visualización de las primeras filas utilizando el método .head().

# Usamos .info() para ver una descripción general del dataset, como sus tipos de datos en cada columna y si presenta nulos
crime_scene_report.info()

# Con la función .head(n:int default=5) podemos visualizar las primeras n filas 
crime_scene_report.head(3)


Funciones Útiles
A continuación, se describen algunas funciones útiles que podrían ayudar durante el proceso:

1. .sort_values(): Esta función se utiliza para ordenar un DataFrame o una Serie según los valores de una o más columnas. Se puede especificar el orden ascendente o descendente. Es útil para organizar los datos de manera coherente y facilitar la búsqueda o el análisis de patrones.

2. .str.contains(): Esta función se utiliza para buscar patrones de texto en una Serie. Permite verificar si una cadena de caracteres contiene un patrón específico. Es útil para realizar filtrados basados en patrones de texto, como buscar palabras clave en un conjunto de datos.

3. .groupby(): Esta función se utiliza para agrupar los datos según una o más columnas en un DataFrame. Se puede combinar con otras funciones de agregación, como .sum(), .mean(), .count(), etc., para realizar cálculos en los grupos resultantes. Es útil para realizar análisis estadísticos o resúmenes de datos basados en categorías.

4. .merge(): Esta función se utiliza para combinar dos DataFrames en función de una o más columnas comunes. Puede realizar una unión interna (inner join), una unión externa (outer join), una unión izquierda (left join) o una unión derecha (right join), según los datos que se deseen mantener. Es útil para combinar conjuntos de datos relacionados en un solo DataFrame para su análisis.

5. .isin(): Esta función se utiliza para verificar si los valores de una columna o Serie están presentes en una lista de valores específica.


Comenzamos la Investigación
Un Crimen ha Ocurrido y el Detective Necesita tu Ayuda
Diagrama de Esquema
Antes de comenzar, veamos un diagrama de esquema para entender cómo están conectados todos los DataFrames.

Digrama imagen 

Recuperación del Informe del Lugar del Crimen
Te entregaron el informe del lugar del crimen, pero de alguna manera lo perdiste. Recuerdas vagamente que el crimen fue un asesinato que ocurrió el 15 de enero de 2018 en Pandas City. Se recomienda comenzar por recuperar el informe correspondiente de su respectivo DataFrame.

Vamos a empezar viendo los asesinatos (murder) que ocurrieron en la fecha 2018-01-15 en el dataset crime_scene_report.

# Seleccionamos los asesinatos que ocurrieron en la fecha 2018-01-15
informe = crime_scene_report[(crime_scene_report["Date"] == "2018-01-15") & 
                            (crime_scene_report["Type"] == "murder")]
informe


Podemos ver que ocurrieron 3 asesinatos en la misma fecha pero en distintas ciudades. Modificamos el código para mostrar solamente el informe que nos interesa.

# Ubicación exacta del reporte del asesinato en Pandas City
informe = crime_scene_report[(crime_scene_report["Date"] == "2018-01-15") & 
                            (crime_scene_report["Type"] == "murder") & 
                            (crime_scene_report["City"] == "Pandas City")]
informe


Identificación de Testigos
Una vez que tenemos el informe, leemos la descripción y vemos que hay 2 testigos. Utilizamos el dataset person y la función .str.contains() para encontrar a los testigos.

# Visualizamos las primeras filas del DataFrame 'person'
person.head(3)

# Determinamos el segundo testigo de nombre Annabel y que vive en Franklin Ave
person[(person["Name"].str.contains("Annabel")) & (person["Address_Street_Name"]=="Franklin Ave")]

# Determinamos el primer testigo que vive en la última casa de Northwestern Dr
person[(person["Address_Street_Name"]=="Northwestern Dr")].sort_values("Address_Number", ascending=False).head(1)


Una vez que encontramos a los testigos en la base de datos de personas, buscamos sus entrevistas en el dataset interviews.

# Visualizamos las primeras filas del DataFrame 'interview'
interview.head(5)

# Obtenemos la declaración de los dos testigos con Id 14887 (Primer Testigo) y 16371 (Segundo Testigo)
interview[(interview["Person_Id"].isin([16371, 14887]))]


Análisis Adicional
Realizamos un análisis adicional, incluyendo la búsqueda de sospechosos basados en diferentes criterios y la comparación con información de eventos de Facebook y registros de licencias de conducir.

# Filtramos el check-in en GetFitNow del 9 de enero que tiene una membresía con "48Z"
getfitnow_checkin[(getfitnow_checkin["Check_In_Date"].str.contains("01-09")) & (getfitnow_checkin["Membership_Id"].str.contains("48Z"))]

# Visualizamos las primeras filas del DataFrame 'getfitnow_members'
getfitnow_members.head(5)

# Renombramos la columna 'Id' a 'Membership_Id' en el DataFrame 'getfitnow_members'
getfitnow_members.rename(columns={"Id":"Membership_Id"}, inplace=True)

# Filtramos los miembros de GetFitNow con membresía "48Z" y estado de membresía "Gold"
getfitnow_members[(getfitnow_members["Membership_Id"].str.contains("48Z")) & (getfitnow_members["Membership_Status"]=="Gold")]

# Visualizamos las primeras filas del DataFrame 'drivers_license'
drivers_license.head(5)

# Renombramos la columna 'Id' a 'License_Id' en el DataFrame 'drivers_license'
drivers_license.rename(columns={"Id":"License_Id"}, inplace=True)

# Filtramos las licencias de conducir con el número de placa "H42W"
drivers_license[(drivers_license["Plate_Number"].str.contains("H42W"))]

# Visualizamos las primeras filas del DataFrame 'person'
person.head(3)

# Combinamos los DataFrames 'person' y 'drivers_license' en la columna 'License_Id'
df_sospechoso = person.merge(drivers_license[["License_Id", "Plate_Number"]], on=["License_Id"])

# Renombramos la columna 'Id' a 'Person_Id' en el DataFrame 'df_sospechoso'
df_sospechoso.rename(columns={"Id":"Person_Id"}, inplace=True)

# Filtramos el DataFrame 'df_sospechoso' con el número de placa "H42W"
df_sospechoso = df_sospechoso[(df_sospechoso["Plate_Number"].str.contains("H42W"))]
df_sospechoso

# Combinamos los DataFrames 'df_sospechoso' y 'getfitnow_members' en la columna 'Person_Id'
df_sospechoso = df_sospechoso.merge(getfitnow_members[["Membership_Id", "Person_Id", "Membership_Status"]], on=["Person_Id"])
df_sospechoso

# Obtenemos la entrevista de la persona con Id 67318
interview[(interview["Person_Id"].isin([67318]))]

# Visualizamos la información y las primeras filas del DataFrame 'facebook_event_checkin'
# facebook_event_checkin.info()
# facebook_event_checkin.head(3)

# Filtramos el DataFrame 'facebook_event_checkin' por la fecha y el evento "SQL Symphony Concert"
df_facebook = facebook_event_checkin[(facebook_event_checkin["date"]>=20171201) & (facebook_event_checkin["date"]<=20171231) & (facebook_event_checkin["event_name"]=="SQL Symphony Concert")]
df_facebook

# Filtramos las licencias de conducir con el color de cabello "Red" y el tipo de coche "Tesla"
df_license = drivers_license[(drivers_license["Hair_Color"]=="Red") & (drivers_license["Car_Make"]=="Tesla")]
df_license

# Combinamos los DataFrames 'person' y 'df_license' en la columna 'License_Id'
df_sospechoso_p = person.merge(df_license, on="License_Id")
df_sospechoso_p

# Combinamos los DataFrames 'df_sospechoso_p' y 'df_facebook' en las columnas 'Id' y 'person_id'
df_sospechoso_p = df_sospechoso_p.merge(df_facebook, left_on="Id", right_on="person_id")
df_sospechoso_p


Identificación del Sospechoso
Si crees que has encontrado al asesino, ejecuta el siguiente código con el posible sospechoso. Ten en cuenta que es recomendable no acceder al archivo asesino.py para evitar spoilers.

from asesino import solucion

# Ejecuta la función 'solucion()' con el nombre del sospechoso
# solucion("Colocar aca el nombre del sospechoso")
# solucion("Jeremy Bowers")
solucion("Miranda Priestly")
