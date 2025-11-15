APP.PY
from flask import Flask, render_template, request, flash
import webbrowser
from threading import Timer

app = Flask(__name__)
app.secret_key = "123"  # necesario para flash

# Página principal con dos botones
@app.route("/")
def index():
    return render_template("index.html")

# Ejercicio 1
@app.route("/ejercicio1", methods=["GET", "POST"])
def ejercicio1():
    if request.method == "POST":
        # Obtengo los valores del formulario
        nota1 = request.form.get("nota1", "").strip()
        nota2 = request.form.get("nota2", "").strip()
        nota3 = request.form.get("nota3", "").strip()
        asistencia = request.form.get("asistencia", "").strip()

        errores = []

        # Validaciones: que sean números y dentro de los rangos indicados
        try:
            n1 = float(nota1)
            if not (10 <= n1 <= 70):
                errores.append("Nota 1 debe estar entre 10 y 70.")
        except ValueError:
            errores.append("Nota 1 debe ser un número.")

        try:
            n2 = float(nota2)
            if not (10 <= n2 <= 70):
                errores.append("Nota 2 debe estar entre 10 y 70.")
        except ValueError:
            errores.append("Nota 2 debe ser un número.")

        try:
            n3 = float(nota3)
            if not (10 <= n3 <= 70):
                errores.append("Nota 3 debe estar entre 10 y 70.")
        except ValueError:
            errores.append("Nota 3 debe ser un número.")

        try:
            a = float(asistencia)
            if not (0 <= a <= 100):
                errores.append("Asistencia (%) debe estar entre 0 y 100.")
        except ValueError:
            errores.append("Asistencia debe ser un número.")

        if errores:
            for e in errores:
                flash(e, "danger")
            # re-renderizar el formulario con los valores ingresados
            return render_template("ejercicio1.html",
                                   nota1=nota1, nota2=nota2, nota3=nota3, asistencia=asistencia)

        # Cálculo del promedio
        promedio = (n1 + n2 + n3) / 3.0

        # Condición de aprobado
        estado = "APROBADO" if (promedio >= 40.0 and a >= 75.0) else "REPROBADO"

        # Renderizo la misma página con los resultados
        return render_template("ejercicio1.html",
                               nota1=nota1, nota2=nota2, nota3=nota3, asistencia=asistencia,
                               promedio=round(promedio, 1), estado=estado)

    # GET
    return render_template("ejercicio1.html")

# Ejercicio 2 (con la validación y la lógica solicitada)
@app.route("/ejercicio2", methods=["GET", "POST"])
def ejercicio2():
    if request.method == "POST":
        nombre1 = request.form.get("nombre1", "").strip()
        nombre2 = request.form.get("nombre2", "").strip()
        nombre3 = request.form.get("nombre3", "").strip()

        errores = []
        # Validaciones básicas
        if not nombre1:
            errores.append("Nombre 1 no puede estar vacío.")
        if not nombre2:
            errores.append("Nombre 2 no puede estar vacío.")
        if not nombre3:
            errores.append("Nombre 3 no puede estar vacío.")

        # Validar que los 3 nombres sean diferentes
        nombres_ingresados = [nombre1, nombre2, nombre3]
        if len({nombre1, nombre2, nombre3}) != 3:
            errores.append("Los tres nombres deben ser distintos entre sí.")

        if errores:
            for e in errores:
                flash(e, "danger")
            return render_template("ejercicio2.html", nombre1=nombre1, nombre2=nombre2, nombre3=nombre3)

        # Calcular longitud de cada nombre (número de caracteres)
        longitudes = {nombre1: len(nombre1), nombre2: len(nombre2), nombre3: len(nombre3)}

        # Obtener la máxima longitud
        max_len = max(longitudes.values())

        # Obtener todos los nombres que tengan esa longitud (por si hay empate)
        nombres_mas_largos = [n for n, l in longitudes.items() if l == max_len]

        # Formato de salida: si hay varios, los unimos con coma y espacio
        if len(nombres_mas_largos) == 1:
            nombre_mayor = nombres_mas_largos[0]
        else:
            nombre_mayor = ", ".join(nombres_mas_largos)

        # Pasamos al template: nombre_mayor (string) y max_len (int)
        return render_template("ejercicio2.html",
                               nombres=nombres_ingresados,
                               combinado=f"{nombre1} - {nombre2} - {nombre3}",
                               nombre1=nombre1, nombre2=nombre2, nombre3=nombre3,
                               nombre_mayor=nombre_mayor, longitud_max=max_len)

    # GET
    return render_template("ejercicio2.html")


# ----------------------------
# Código para abrir el navegador automáticamente
# ----------------------------
def abrir_navegador(url="http://127.0.0.1:5000/"):
    """Abre el navegador en la URL dada."""
    webbrowser.open_new(url)


if __name__ == "__main__":




INDEX
<!doctype html>
<html lang="es">
<head>
    <meta charset="utf-8">
    <title>Menú principal</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <!-- Bootstrap CDN -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

    <!-- ESTILOS PERSONALIZADOS -->
    <style>
        body {
            background: linear-gradient(135deg, #1f2933, #0e2a32);
            color: #f3f8f9;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        .card-custom {
            background-color: #24343c;
            border: none;
            border-radius: 20px;
            padding: 30px;
            width: 340px;
            box-shadow: 0px 10px 25px rgba(0, 0, 0, 0.35);
            transition: transform 0.25s ease, box-shadow 0.25s ease;
        }

        .card-custom:hover {
            transform: translateY(-5px);
            box-shadow: 0px 15px 35px rgba(0, 0, 0, 0.45);
        }

        .btn-custom {
            background-color: #0db4b9;
            border: none;
            color: white;
            font-size: 18px;
            padding: 12px;
            border-radius: 50px;
            transition: background-color 0.25s ease, transform 0.15s ease;
            font-weight: 600;
        }

        .btn-custom:hover {
            background-color: #0a8f92;
            transform: scale(1.03);
        }

        .btn-custom:active {
            transform: scale(0.97);
        }

        h1 {
            font-weight: 700;
            text-align: center;
            color: #e0f7fa;
            margin-bottom: 25px;
            letter-spacing: 1px;
        }
    </style>
</head>

<body>

    <div class="container d-flex flex-column align-items-center justify-content-center" style="min-height: 90vh;">

        <h1>Menú Principal</h1>

        <div class="card-custom text-center">

            <div class="d-grid gap-3">
                <a href="{{ url_for('ejercicio1') }}" class="btn btn-custom">Ejercicio 1</a>
                <a href="{{ url_for('ejercicio2') }}" class="btn btn-custom">Ejercicio 2</a>
            </div>

        </div>
    </div>


    EJERCICIO 1
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <title>Ejercicio 1 - Formulario de Notas</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <!-- Bootstrap -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

  <style>
    /* ==== PALETA DE COLORES ==== */
    :root {
      --turquesa-oscuro: #0a7373;
      --gris-oscuro: #2e2e2e;
      --gris-claro: #f5f5f5;
      --blanco: #ffffff;
    }

    body {
      background: var(--gris-claro);
      font-family: 'Segoe UI', sans-serif;
    }

    .card {
      border: none;
      border-radius: 18px;
      box-shadow: 0 4px 18px rgba(0,0,0,0.15);
      background: var(--blanco);
    }

    h3.card-title {
      color: var(--turquesa-oscuro);
      font-weight: 700;
      text-align: center;
      margin-bottom: 20px;
    }

    label.form-label {
      font-weight: 600;
      color: var(--gris-oscuro);
    }

    input.form-control {
      border-radius: 10px;
      border: 2px solid #dcdcdc;
    }

    input.form-control:focus {
      border-color: var(--turquesa-oscuro);
      box-shadow: 0 0 4px rgba(10, 115, 115, 0.4);
    }

    .btn-primary {
      background: var(--turquesa-oscuro) !important;
      border: none;
      border-radius: 50px;
      font-size: 1.1rem;
      padding: 10px 0;
      transition: 0.3s;
    }

    .btn-primary:hover {
      background: #065c5c !important;
      transform: translateY(-2px);
    }

    .alert {
      border-radius: 10px;
      font-size: 0.9rem;
    }

    /* Resultado */
    .resultado {
      background: var(--turquesa-oscuro);
      color: white;
      padding: 12px;
      border-radius: 12px;
      margin-top: 20px;
    }
  </style>
</head>

<body>
  <div class="container py-5">
    <div class="card mx-auto p-4" style="max-width:520px;">
      <div class="card-body">

        <h3 class="card-title">Formulario de Notas</h3>

        <!-- ALERTAS FLASH -->
        {% with messages = get_flashed_messages(with_categories=true) %}
          {% if messages %}
            {% for category, msg in messages %}
              <div class="alert alert-{{ 'danger' if category=='danger' else 'info' }} py-2">
                {{ msg }}
              </div>
            {% endfor %}
          {% endif %}
        {% endwith %}

        <!-- FORMULARIO -->
        <form method="post" novalidate>

          <div class="mb-3">
            <label class="form-label">Nota 1:</label>
            <input name="nota1" class="form-control" value="{{ nota1 or '' }}">
            <div class="form-text">Valor entre 10 y 70.</div>
          </div>

          <div class="mb-3">
            <label class="form-label">Nota 2:</label>
            <input name="nota2" class="form-control" value="{{ nota2 or '' }}">
          </div>

          <div class="mb-3">
            <label class="form-label">Nota 3:</label>
            <input name="nota3" class="form-control" value="{{ nota3 or '' }}">
          </div>

          <div class="mb-3">
            <label class="form-label">Asistencia (%):</label>
            <input name="asistencia" class="form-control" value="{{ asistencia or '' }}">
            <div class="form-text">Valor entre 0 y 100.</div>
          </div>

          <button type="submit" class="btn btn-primary w-100">Enviar</button>
        </form>

        {% if promedio is defined %}
          <div class="resultado">
            <p><strong>Promedio:</strong> {{ promedio }}</p>
            <p><strong>Estado:</strong> {{ estado }}</p>
          </div>
        {% endif %}

      </div>
    </div>
  </div>
</body>
</html>



EJERCICIO 2

<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <title>Ejercicio 2 - Nombres</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <!-- Bootstrap -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

  <style>
    /* ==== PALETA DE COLORES ==== */
    :root {
      --turquesa-oscuro: #0a7373;
      --gris-oscuro: #2e2e2e;
      --gris-claro: #f5f5f5;
      --blanco: #ffffff;
    }

    body {
      background: var(--gris-claro);
      font-family: 'Segoe UI', sans-serif;
    }

    .card {
      border: none;
      border-radius: 18px;
      box-shadow: 0 4px 18px rgba(0,0,0,0.15);
      background: var(--blanco);
    }

    h5 {
      color: var(--turquesa-oscuro);
      font-weight: 700;
      margin-bottom: 20px;
    }

    label.form-label {
      font-weight: 600;
      color: var(--gris-oscuro);
    }

    input.form-control {
      border-radius: 10px;
      border: 2px solid #dcdcdc;
    }

    input.form-control:focus {
      border-color: var(--turquesa-oscuro);
      box-shadow: 0 0 4px rgba(10, 115, 115, 0.4);
    }

    .btn-primary {
      background: var(--turquesa-oscuro) !important;
      border: none;
      width: 100%;
      border-radius: 50px;
      font-size: 1.1rem;
      padding: 10px 0;
      transition: 0.3s;
    }

    .btn-primary:hover {
      background: #065c5c !important;
      transform: translateY(-2px);
    }

    .alert {
      border-radius: 10px;
      font-size: 0.9rem;
    }

    .resultado {
      background: var(--turquesa-oscuro);
      color: white;
      padding: 12px;
      border-radius: 12px;
      margin-top: 20px;
    }
  </style>
</head>

<body>
  <div class="container py-5">
    <div class="card mx-auto p-4" style="max-width:420px;">
      <div class="card-body text-center">

        <h5>Ingrese 3 nombres</h5>

        <!-- ALERTAS -->
        {% with messages = get_flashed_messages(with_categories=true) %}
          {% if messages %}
            {% for category, msg in messages %}
              <div class="alert alert-{{ 'danger' if category=='danger' else 'info' }} py-2">{{ msg }}</div>
            {% endfor %}
          {% endif %}
        {% endwith %}

        <!-- FORMULARIO -->
        <form method="post">
          <div class="mb-3 text-start">
            <label class="form-label">Nombre 1:</label>
            <input class="form-control" name="nombre1" value="{{ nombre1 or '' }}">
          </div>

          <div class="mb-3 text-start">
            <label class="form-label">Nombre 2:</label>
            <input class="form-control" name="nombre2" value="{{ nombre2 or '' }}">
          </div>

          <div class="mb-3 text-start">
            <label class="form-label">Nombre 3:</label>
            <input class="form-control" name="nombre3" value="{{ nombre3 or '' }}">
          </div>

          <button class="btn btn-primary">Enviar</button>
        </form>

        {% if nombres is defined %}
        <div class="resultado text-start mt-4">
          <p><strong>Lista de nombres:</strong></p>
          <ol class="mb-2">
            {% for n in nombres %}
              <li>{{ n }}</li>
            {% endfor %}
          </ol>

          <p><strong>Combinado:</strong> {{ combinado }}</p>

          <hr style="border-color: rgba(255,255,255,0.4);">

          <p>Nombre(s) con mayor cantidad de caracteres:<br>
            <strong>{{ nombre_mayor }}</strong></p>

          <p>Total de caracteres:<br>
            <strong>{{ longitud_max }}</strong></p>
        </div>
        {% endif %}

      </div>
    </div>
  </div>
</body>
</html>

    

</body>
</html>

    # el bloque __main__ dos veces (lo que abriría el navegador dos veces).
    Timer(1, abrir_navegador).start()
    app.run(debug=True, use_reloader=False)
