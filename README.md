import streamlit as st

# Configuración de la página
st.set_page_config(page_title="Cuestionario AUDIT - Concientización", page_icon="🍷")

st.title("Cuestionario de Detección de Alcohol (AUDIT)")
st.write("""
El consumo de alcohol puede afectar su salud. Conteste las siguientes preguntas con sinceridad. 
**Sus respuestas son totalmente privadas y anónimas.**
""")

# Definición de equivalencias (Imagen de referencia)
st.info("""
**Un trago equivale a:**
* 🍺 **12 oz de cerveza**
* 🍷 **5 oz de vino**
* 🍸 **1.5 oz de licor (una copita)**
""")

# --- Preguntas y Opciones ---
preguntas = [
    "1. ¿Con qué frecuencia bebe un trago con contenido de alcohol?",
    "2. ¿Cuántos tragos que contengan alcohol consume en un día típico cuando está bebiendo?",
    "3. ¿Con qué frecuencia bebe cuatro o más tragos en una ocasión?",
    "4. ¿Con qué frecuencia durante el último año se dio cuenta de que no pudo dejar de beber una vez que había empezado?",
    "5. ¿Con qué frecuencia durante el último año ha dejado de hacer lo que normalmente se esperaba de usted debido a la bebida?",
    "6. ¿Con qué frecuencia durante el último año ha necesitado un primer trago en la mañana para ponerse en acción después de una sesión de beber abundantemente?",
    "7. ¿Con qué frecuencia durante el último año ha tenido una sensación de culpa o remordimiento después de beber?",
    "8. ¿Con qué frecuencia durante el último año ha sido incapaz de recordar lo que pasó la noche anterior debido a su forma de beber?",
    "9. ¿Usted o alguien más han resultado heridos debido a su forma de beber?",
    "10. ¿Ha estado preocupado por su forma de beber o le ha sugerido que beba menos algún pariente, amigo, médico u otro trabajador de la atención a la salud?"
]

opciones = [
    ["Nunca", "Una vez al mes o menos", "2-4 veces al mes", "2-3 veces por semana", "4 o más veces por semana"],
    ["0-2", "3 o 4", "5 o 6", "7-9", "10 o más"],
    ["Nunca", "Menos de una vez al mes", "Mensualmente", "Semanalmente", "Diario o casi a diario"],
    ["Nunca", "Menos de una vez al mes", "Mensualmente", "Semanalmente", "Diario o casi a diario"],
    ["Nunca", "Menos de una vez al mes", "Mensualmente", "Semanalmente", "Diario o casi a diario"],
    ["Nunca", "Menos de una vez al mes", "Mensualmente", "Semanalmente", "Diario o casi a diario"],
    ["Nunca", "Menos de una vez al mes", "Mensualmente", "Semanalmente", "Diario o casi a diario"],
    ["Nunca", "Menos de una vez al mes", "Mensualmente", "Semanalmente", "Diario o casi a diario"],
    ["No", "", "Sí, pero no en el último año", "", "Sí, en el último año"],
    ["No", "", "Sí, pero no en el último año", "", "Sí, en el último año"]
]

# --- Lógica de la Encuesta ---
respuestas = []

with st.form("audit_form"):
    for i in range(len(preguntas)):
        # Para las preguntas 9 y 10, los valores de puntos saltan (0, 2, 4) según la imagen
        if i >= 8:
            res = st.radio(preguntas[i], [op for op in opciones[i] if op != ""], index=0)
            puntos = 0 if res == "No" else (2 if "pero no en el último año" in res else 4)
        else:
            res = st.radio(preguntas[i], opciones[i], index=0)
            puntos = opciones[i].index(res)
        
        respuestas.append(puntos)
    
    # Campo adicional informativo
    tratamiento = st.selectbox("¿Ha estado alguna vez en tratamiento por un problema de alcohol?", ["Nunca", "Actualmente", "En el pasado"])
    genero = st.radio("Género (para interpretación de resultados):", ["Masculino", "Femenino"])

    enviado = st.form_submit_button("Ver mi resultado")

# --- Resultados ---
if enviado:
    puntaje_total = sum(respuestas)
    st.subheader(f"Tu puntaje total es: {puntaje_total}")

    # Lógica de interpretación basada en la imagen (M: Hombre, W: Mujer)
    if genero == "Masculino":
        if puntaje_total <= 4: nivel = "I - Riesgo bajo"
        elif puntaje_total <= 14: nivel = "II - Consumo de riesgo"
        elif puntaje_total <= 19: nivel = "III - Consumo perjudicial"
        else: nivel = "IV - Posible dependencia"
    else:
        if puntaje_total <= 3: nivel = "I - Riesgo bajo"
        elif puntaje_total <= 12: nivel = "II - Consumo de riesgo"
        elif puntaje_total <= 18: nivel = "III - Consumo perjudicial"
        else: nivel = "IV - Posible dependencia"

    st.markdown(f"### Nivel detectado: **{nivel}**")
    
    if puntaje_total >= 8:
        st.warning("Se recomienda consultar con un profesional de salud para una evaluación más detallada.")
    else:
        st.success("Tu consumo se encuentra en niveles de bajo riesgo.")

    st.caption("Nota: Este test es una herramienta de tamizaje, no un diagnóstico médico definitivo.")
