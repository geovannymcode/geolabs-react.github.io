# 📝 Manejo de Formularios y Estados Derivados en React

## 📋 Índice
1. [Introducción a formularios en React](#introduccion)
2. [Formularios controlados vs no controlados](#controlados-vs-no-controlados)
3. [Manejo de diferentes tipos de inputs](#tipos-inputs)
4. [Validación de formularios](#validacion)
5. [Estados derivados explicados](#estados-derivados)
6. [Optimización y mejores prácticas](#optimizacion)
7. [Errores comunes y soluciones](#errores-comunes)

---

## 🎯 Introducción a Formularios en React <a name="introduccion"></a>

### ¿Por qué los formularios son especiales en React?

En HTML tradicional, los formularios mantienen su propio estado interno. En React, preferimos que el estado sea manejado por componentes.

```jsx
// HTML tradicional - El DOM controla el valor
<input type="text" name="nombre" />

// React - El componente controla el valor
<input type="text" value={nombre}

### Cuándo usar estados derivados

#### ✅ USA estado derivado cuando:

1. **El valor puede calcularse de otro estado**
```jsx
// En lugar de almacenar el total
const total = items.reduce((sum, item) => sum + item.precio, 0)
```

2. **Filtros y búsquedas**
```jsx
// No guardes los resultados filtrados
const resultados = datos.filter(item => item.nombre.includes(busqueda))
```

3. **Validaciones**
```jsx
// Calcula errores basados en los valores
const esValido = email.includes('@') && password.length >= 8
```

4. **Transformaciones de datos**
```jsx
// Formateo, ordenamiento, agrupación
const datosOrdenados = [...datos].sort((a, b) => a.nombre.localeCompare(b.nombre))
```

#### ❌ NO uses estado derivado cuando:

1. **El cálculo es muy costoso** (usa useMemo en su lugar)
2. **Los datos vienen de una API**
3. **Necesitas persistir el valor**
4. **El valor se actualiza independientemente**

---

## ⚡ Optimización y Mejores Prácticas <a name="optimizacion"></a>

### 1. Optimización con useMemo

Cuando los cálculos derivados son costosos:

```jsx
import { useMemo } from 'react'

function TablaGrande({ datos, filtros }) {
  // ❌ Se recalcula en cada render
  const datosProcesados = procesarDatosComplejos(datos, filtros)
  
  // ✅ Solo se recalcula cuando cambian las dependencias
  const datosProcesados = useMemo(
    () => procesarDatosComplejos(datos, filtros),
    [datos, filtros]
  )
  
  return <Tabla datos={datosProcesados} />
}
```

### 2. Debounce en formularios

Para evitar demasiadas actualizaciones:

```jsx
function BuscadorOptimizado() {
  const [busqueda, setBusqueda] = useState('')
  const [resultados, setResultados] = useState([])
  
  // Debounce la búsqueda
  useEffect(() => {
    const timer = setTimeout(() => {
      if (busqueda) {
        buscarEnAPI(busqueda).then(setResultados)
      }
    }, 300)
    
    return () => clearTimeout(timer)
  }, [busqueda])
  
  return (
    <input
      value={busqueda}
      onChange={(e) => setBusqueda(e.target.value)}
      placeholder="Buscar..."
    />
  )
}
```

### 3. Formateo automático

Mejorar UX con formateo en tiempo real:

```jsx
function InputTelefono({ value, onChange }) {
  const formatearTelefono = (valor) => {
    // Eliminar todo excepto números
    const numeros = valor.replace(/\D/g, '')
    
    // Aplicar formato (XXX) XXX-XXXX
    if (numeros.length <= 3) return numeros
    if (numeros.length <= 6) {
      return `(${numeros.slice(0, 3)}) ${numeros.slice(3)}`
    }
    return `(${numeros.slice(0, 3)}) ${numeros.slice(3, 6)}-${numeros.slice(6, 10)}`
  }
  
  const handleChange = (e) => {
    const valorFormateado = formatearTelefono(e.target.value)
    onChange(valorFormateado)
  }
  
  return (
    <input
      type="tel"
      value={value}
      onChange={handleChange}
      placeholder="(555) 123-4567"
      maxLength="14"
    />
  )
}
```

### 4. Prevención de envíos múltiples

```jsx
function FormularioConProteccion() {
  const [enviando, setEnviando] = useState(false)
  const [intentosEnvio, setIntentosEnvio] = useState(0)
  
  const handleSubmit = async (e) => {
    e.preventDefault()
    
    // Prevenir envíos múltiples
    if (enviando) return
    
    // Limitar intentos
    if (intentosEnvio >= 3) {
      alert('Demasiados intentos. Intenta más tarde.')
      return
    }
    
    setEnviando(true)
    setIntentosEnvio(prev => prev + 1)
    
    try {
      await enviarFormulario()
      // Éxito
    } catch (error) {
      // Error
    } finally {
      setEnviando(false)
    }
  }
  
  return (
    <form onSubmit={handleSubmit}>
      {/* campos */}
      <button type="submit" disabled={enviando || intentosEnvio >= 3}>
        {enviando ? 'Enviando...' : 'Enviar'}
      </button>
    </form>
  )
}
```

### 5. Composición de componentes de formulario

```jsx
// Componentes reutilizables
function Campo({ label, error, children, required }) {
  return (
    <div className={`campo ${error ? 'campo-error' : ''}`}>
      <label>
        {label} {required && <span className="requerido">*</span>}
      </label>
      {children}
      {error && <span className="mensaje-error">{error}</span>}
    </div>
  )
}

function InputValidado({ type = 'text', validacion, ...props }) {
  const [error, setError] = useState('')
  const [tocado, setTocado] = useState(false)
  
  const handleBlur = (e) => {
    setTocado(true)
    if (validacion) {
      setError(validacion(e.target.value))
    }
    props.onBlur?.(e)
  }
  
  return (
    <>
      <input
        type={type}
        onBlur={handleBlur}
        {...props}
      />
      {error && tocado && <span className="error">{error}</span>}
    </>
  )
}

// Uso
function MiFormulario() {
  return (
    <form>
      <Campo label="Email" required>
        <InputValidado
          type="email"
          validacion={(v) => !v.includes('@') ? 'Email inválido' : ''}
        />
      </Campo>
    </form>
  )
}
```

---

## ❌ Errores Comunes y Soluciones <a name="errores-comunes"></a>

### Error 1: Mutar el estado del formulario

```jsx
// ❌ MAL - Mutación directa
const handleChange = (e) => {
  formData[e.target.name] = e.target.value  // Mutación!
  setFormData(formData)  // React no detecta cambios
}

// ✅ BIEN - Crear nuevo objeto
const handleChange = (e) => {
  setFormData({
    ...formData,
    [e.target.name]: e.target.value
  })
}
```

### Error 2: Estado derivado innecesario

```jsx
// ❌ MAL - Estado redundante
const [items, setItems] = useState([])
const [itemCount, setItemCount] = useState(0)  // Redundante!

const addItem = (item) => {
  setItems([...items, item])
  setItemCount(itemCount + 1)  // Fácil de olvidar
}

// ✅ BIEN - Derivar del estado existente
const [items, setItems] = useState([])
const itemCount = items.length  // Siempre sincronizado
```

### Error 3: Validación solo en submit

```jsx
// ❌ MAL - UX pobre
const handleSubmit = (e) => {
  e.preventDefault()
  // Solo validar aquí frustará al usuario
  if (!validarTodo()) {
    alert('Formulario inválido')
  }
}

// ✅ BIEN - Validación progresiva
const handleBlur = (e) => {
  // Validar cuando el usuario termina con el campo
  validarCampo(e.target.name, e.target.value)
}
```

### Error 4: No manejar estados de carga

```jsx
// ❌ MAL - Sin feedback
const handleSubmit = async (e) => {
  e.preventDefault()
  await enviarDatos(formData)
}

// ✅ BIEN - Con estados de UI
const handleSubmit = async (e) => {
  e.preventDefault()
  setEnviando(true)
  setError(null)
  
  try {
    await enviarDatos(formData)
    setExito(true)
  } catch (err) {
    setError(err.message)
  } finally {
    setEnviando(false)
  }
}
```

### Error 5: Re-renders innecesarios

```jsx
// ❌ MAL - Crear funciones en el render
<input onChange={(e) => setValor(e.target.value)} />

// ✅ BIEN - Función estable
const handleChange = useCallback((e) => {
  setValor(e.target.value)
}, [])

<input onChange={handleChange} />
```

---

## 🎯 Resumen y Mejores Prácticas

### Checklist para formularios

✅ **Formularios controlados** para mayor control
✅ **Validación en tiempo real** con feedback visual
✅ **Estados de carga** para mejor UX
✅ **Prevenir envíos múltiples**
✅ **Mensajes de error claros** y específicos
✅ **Formateo automático** cuando sea útil
✅ **Accesibilidad** (labels, ARIA, navegación por teclado)

### Checklist para estados derivados

✅ **Calcular en lugar de almacenar** cuando sea posible
✅ **useMemo para cálculos costosos**
✅ **Evitar duplicación de estado**
✅ **Mantener única fuente de verdad**
✅ **Derivar filtros y transformaciones**

### Patrones recomendados

```jsx
// 1. Hook personalizado para lógica reutilizable
const { valores, errores, handleChange, handleSubmit } = useForm(inicial, validaciones)

// 2. Estados derivados para UI
const puedeEnviar = formularioValido && !enviando

// 3. Composición de componentes
<Campo label="Email" error={errores.email}>
  <Input {...campEmail} />
</Campo>

// 4. Validación progresiva
onBlur={validarCampo} // No solo en submit
```

### Recursos adicionales

1. **React Hook Form**: Librería para formularios complejos
2. **Formik**: Alternativa popular
3. **Yup**: Esquemas de validación
4. **React Select**: Selects avanzados
5. **React Datepicker**: Selectores de fecha

¡Con estos conocimientos puedes crear formularios robustos y experiencias de usuario fluidas! onChange={handleChange} />
```

### El flujo de datos en formularios React

```
Usuario escribe → onChange → setState → Re-render → value actualizado
        ↑                                                    ↓
        ←──────────────── UI actualizada ←──────────────────
```

---

## 🎮 Formularios Controlados vs No Controlados <a name="controlados-vs-no-controlados"></a>

### Formularios Controlados (Recomendado)

En un componente controlado, React controla el valor del input en todo momento:

```jsx
function FormularioControlado() {
  const [nombre, setNombre] = useState('')
  
  // React controla el valor
  return (
    <input 
      type="text"
      value={nombre}                        // Valor viene del estado
      onChange={(e) => setNombre(e.target.value)}  // Estado se actualiza
    />
  )
}
```

**Ventajas:**
- ✅ Single source of truth
- ✅ Validación en tiempo real
- ✅ Formateo instantáneo
- ✅ Control total sobre el input

### Formularios No Controlados

El DOM mantiene el estado, React lo lee cuando lo necesita:

```jsx
function FormularioNoControlado() {
  const inputRef = useRef()
  
  const handleSubmit = (e) => {
    e.preventDefault()
    console.log(inputRef.current.value)  // Lee el valor del DOM
  }
  
  // React NO controla el valor
  return (
    <form onSubmit={handleSubmit}>
      <input type="text" ref={inputRef} />
      <button type="submit">Enviar</button>
    </form>
  )
}
```

**Cuándo usar no controlados:**
- Integración con librerías no-React
- Formularios muy simples
- Archivos (file input)

### Comparación visual

```
CONTROLADO:
Estado React: "Hola"
     ↓ (value)
   Input
     ↓ (onChange)
Estado React: "Hola M"

NO CONTROLADO:
Estado DOM: "Hola"
   Input
Estado React: null (hasta que lo leas)
```

---

## 📋 Manejo de Diferentes Tipos de Inputs <a name="tipos-inputs"></a>

### 1. Input de texto

```jsx
function FormularioCompleto() {
  const [formData, setFormData] = useState({
    nombre: '',
    email: '',
    edad: '',
    biografia: '',
    pais: '',
    genero: '',
    suscrito: false,
    plan: 'basico',
    intereses: []
  })
  
  // Manejador genérico para inputs de texto
  const handleInputChange = (e) => {
    const { name, value } = e.target
    setFormData(prev => ({
      ...prev,
      [name]: value
    }))
  }
  
  return (
    <form>
      {/* Input de texto simple */}
      <input
        type="text"
        name="nombre"
        value={formData.nombre}
        onChange={handleInputChange}
        placeholder="Tu nombre"
      />
      
      {/* Input de email con validación HTML5 */}
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleInputChange}
        placeholder="tu@email.com"
        required
      />
      
      {/* Input numérico */}
      <input
        type="number"
        name="edad"
        value={formData.edad}
        onChange={handleInputChange}
        min="18"
        max="100"
      />
    </form>
  )
}
```

### 2. Textarea

```jsx
{/* Textarea para texto largo */}
<textarea
  name="biografia"
  value={formData.biografia}
  onChange={handleInputChange}
  rows="4"
  placeholder="Cuéntanos sobre ti..."
  maxLength="500"
/>
<span>{formData.biografia.length}/500</span>
```

### 3. Select (Dropdown)

```jsx
{/* Select simple */}
<select 
  name="pais" 
  value={formData.pais} 
  onChange={handleInputChange}
>
  <option value="">Selecciona un país</option>
  <option value="mx">México</option>
  <option value="es">España</option>
  <option value="ar">Argentina</option>
</select>

{/* Select con grupos */}
<select name="idioma" value={formData.idioma} onChange={handleInputChange}>
  <optgroup label="Más comunes">
    <option value="es">Español</option>
    <option value="en">Inglés</option>
  </optgroup>
  <optgroup label="Otros">
    <option value="fr">Francés</option>
    <option value="de">Alemán</option>
  </optgroup>
</select>
```

### 4. Radio Buttons

```jsx
{/* Radio buttons - solo uno puede estar seleccionado */}
<fieldset>
  <legend>Género:</legend>
  
  <label>
    <input
      type="radio"
      name="genero"
      value="masculino"
      checked={formData.genero === 'masculino'}
      onChange={handleInputChange}
    />
    Masculino
  </label>
  
  <label>
    <input
      type="radio"
      name="genero"
      value="femenino"
      checked={formData.genero === 'femenino'}
      onChange={handleInputChange}
    />
    Femenino
  </label>
  
  <label>
    <input
      type="radio"
      name="genero"
      value="otro"
      checked={formData.genero === 'otro'}
      onChange={handleInputChange}
    />
    Otro
  </label>
</fieldset>
```

### 5. Checkboxes

```jsx
// Checkbox simple (booleano)
const handleCheckboxChange = (e) => {
  const { name, checked } = e.target
  setFormData(prev => ({
    ...prev,
    [name]: checked
  }))
}

<label>
  <input
    type="checkbox"
    name="suscrito"
    checked={formData.suscrito}
    onChange={handleCheckboxChange}
  />
  Suscribirme al newsletter
</label>

// Checkbox múltiple (array)
const handleInteresesChange = (e) => {
  const { value, checked } = e.target
  setFormData(prev => ({
    ...prev,
    intereses: checked
      ? [...prev.intereses, value]
      : prev.intereses.filter(i => i !== value)
  }))
}

<fieldset>
  <legend>Intereses:</legend>
  {['tecnologia', 'deportes', 'musica', 'lectura'].map(interes => (
    <label key={interes}>
      <input
        type="checkbox"
        value={interes}
        checked={formData.intereses.includes(interes)}
        onChange={handleInteresesChange}
      />
      {interes}
    </label>
  ))}
</fieldset>
```

### 6. File Input (caso especial)

```jsx
// File input es siempre no controlado
const [archivo, setArchivo] = useState(null)
const [preview, setPreview] = useState(null)

const handleFileChange = (e) => {
  const file = e.target.files[0]
  if (file) {
    setArchivo(file)
    
    // Preview para imágenes
    if (file.type.startsWith('image/')) {
      const reader = new FileReader()
      reader.onloadend = () => {
        setPreview(reader.result)
      }
      reader.readAsDataURL(file)
    }
  }
}

<div>
  <input
    type="file"
    onChange={handleFileChange}
    accept="image/*"
  />
  {preview && <img src={preview} alt="Preview" style={{width: 200}} />}
  {archivo && <p>Archivo: {archivo.name} ({archivo.size} bytes)</p>}
</div>
```

### Manejador universal para todos los tipos

```jsx
const handleChange = (e) => {
  const { name, value, type, checked, files } = e.target
  
  let newValue
  
  switch (type) {
    case 'checkbox':
      newValue = checked
      break
    case 'file':
      newValue = files[0]
      break
    case 'number':
      newValue = parseInt(value) || 0
      break
    default:
      newValue = value
  }
  
  setFormData(prev => ({
    ...prev,
    [name]: newValue
  }))
}
```

---

## ✅ Validación de Formularios <a name="validacion"></a>

### Validación en tiempo real

```jsx
function FormularioConValidacion() {
  const [valores, setValores] = useState({
    email: '',
    password: '',
    confirmPassword: ''
  })
  
  const [errores, setErrores] = useState({})
  const [tocados, setTocados] = useState({})
  
  // Reglas de validación
  const validaciones = {
    email: (valor) => {
      if (!valor) return 'El email es requerido'
      if (!/\S+@\S+\.\S+/.test(valor)) return 'Email inválido'
      return ''
    },
    password: (valor) => {
      if (!valor) return 'La contraseña es requerida'
      if (valor.length < 8) return 'Mínimo 8 caracteres'
      if (!/(?=.*[a-z])/.test(valor)) return 'Debe tener minúsculas'
      if (!/(?=.*[A-Z])/.test(valor)) return 'Debe tener mayúsculas'
      if (!/(?=.*\d)/.test(valor)) return 'Debe tener números'
      return ''
    },
    confirmPassword: (valor) => {
      if (!valor) return 'Confirma tu contraseña'
      if (valor !== valores.password) return 'Las contraseñas no coinciden'
      return ''
    }
  }
  
  // Validar un campo
  const validarCampo = (nombre, valor) => {
    const error = validaciones[nombre](valor)
    setErrores(prev => ({
      ...prev,
      [nombre]: error
    }))
    return error
  }
  
  // Manejar cambios
  const handleChange = (e) => {
    const { name, value } = e.target
    
    setValores(prev => ({
      ...prev,
      [name]: value
    }))
    
    // Validar si el campo ha sido tocado
    if (tocados[name]) {
      validarCampo(name, value)
    }
  }
  
  // Manejar blur (campo pierde foco)
  const handleBlur = (e) => {
    const { name, value } = e.target
    
    setTocados(prev => ({
      ...prev,
      [name]: true
    }))
    
    validarCampo(name, value)
  }
  
  // Validar todo el formulario
  const validarFormulario = () => {
    const nuevosErrores = {}
    let esValido = true
    
    Object.keys(valores).forEach(campo => {
      const error = validaciones[campo](valores[campo])
      if (error) {
        nuevosErrores[campo] = error
        esValido = false
      }
    })
    
    setErrores(nuevosErrores)
    setTocados(Object.keys(valores).reduce((acc, campo) => ({
      ...acc,
      [campo]: true
    }), {}))
    
    return esValido
  }
  
  // Manejar submit
  const handleSubmit = (e) => {
    e.preventDefault()
    
    if (validarFormulario()) {
      console.log('Formulario válido:', valores)
      // Enviar datos
    } else {
      console.log('Formulario con errores')
    }
  }
  
  return (
    <form onSubmit={handleSubmit}>
      <div className="campo-form">
        <input
          type="email"
          name="email"
          placeholder="Email"
          value={valores.email}
          onChange={handleChange}
          onBlur={handleBlur}
          className={errores.email && tocados.email ? 'error' : ''}
        />
        {errores.email && tocados.email && (
          <span className="mensaje-error">{errores.email}</span>
        )}
      </div>
      
      <div className="campo-form">
        <input
          type="password"
          name="password"
          placeholder="Contraseña"
          value={valores.password}
          onChange={handleChange}
          onBlur={handleBlur}
          className={errores.password && tocados.password ? 'error' : ''}
        />
        {errores.password && tocados.password && (
          <span className="mensaje-error">{errores.password}</span>
        )}
        
        {/* Indicador de fuerza de contraseña */}
        {valores.password && (
          <IndicadorFuerza password={valores.password} />
        )}
      </div>
      
      <div className="campo-form">
        <input
          type="password"
          name="confirmPassword"
          placeholder="Confirmar contraseña"
          value={valores.confirmPassword}
          onChange={handleChange}
          onBlur={handleBlur}
          className={errores.confirmPassword && tocados.confirmPassword ? 'error' : ''}
        />
        {errores.confirmPassword && tocados.confirmPassword && (
          <span className="mensaje-error">{errores.confirmPassword}</span>
        )}
      </div>
      
      <button type="submit">Registrarse</button>
    </form>
  )
}

// Componente indicador de fuerza
function IndicadorFuerza({ password }) {
  const calcularFuerza = () => {
    let fuerza = 0
    if (password.length >= 8) fuerza++
    if (password.length >= 12) fuerza++
    if (/[a-z]/.test(password) && /[A-Z]/.test(password)) fuerza++
    if (/\d/.test(password)) fuerza++
    if (/[^A-Za-z0-9]/.test(password)) fuerza++
    return fuerza
  }
  
  const fuerza = calcularFuerza()
  const niveles = ['Muy débil', 'Débil', 'Media', 'Fuerte', 'Muy fuerte']
  const colores = ['#e74c3c', '#e67e22', '#f39c12', '#2ecc71', '#27ae60']
  
  return (
    <div className="indicador-fuerza">
      <div className="barras">
        {[...Array(5)].map((_, i) => (
          <div
            key={i}
            className="barra"
            style={{
              backgroundColor: i < fuerza ? colores[fuerza - 1] : '#ecf0f1'
            }}
          />
        ))}
      </div>
      <span style={{ color: colores[Math.max(0, fuerza - 1)] }}>
        {niveles[Math.max(0, fuerza - 1)]}
      </span>
    </div>
  )
}
```

### Hook personalizado para formularios

```jsx
// useForm.js - Hook reutilizable
function useForm(valoresIniciales, validaciones) {
  const [valores, setValores] = useState(valoresIniciales)
  const [errores, setErrores] = useState({})
  const [tocados, setTocados] = useState({})
  const [enviando, setEnviando] = useState(false)
  
  const handleChange = (e) => {
    const { name, value, type, checked } = e.target
    const nuevoValor = type === 'checkbox' ? checked : value
    
    setValores(prev => ({
      ...prev,
      [name]: nuevoValor
    }))
    
    // Validar si ya fue tocado
    if (tocados[name] && validaciones[name]) {
      const error = validaciones[name](nuevoValor, valores)
      setErrores(prev => ({
        ...prev,
        [name]: error
      }))
    }
  }
  
  const handleBlur = (e) => {
    const { name } = e.target
    
    setTocados(prev => ({
      ...prev,
      [name]: true
    }))
    
    if (validaciones[name]) {
      const error = validaciones[name](valores[name], valores)
      setErrores(prev => ({
        ...prev,
        [name]: error
      }))
    }
  }
  
  const validarTodo = () => {
    const nuevosErrores = {}
    let esValido = true
    
    Object.keys(validaciones).forEach(campo => {
      const error = validaciones[campo](valores[campo], valores)
      if (error) {
        nuevosErrores[campo] = error
        esValido = false
      }
    })
    
    setErrores(nuevosErrores)
    return esValido
  }
  
  const resetForm = () => {
    setValores(valoresIniciales)
    setErrores({})
    setTocados({})
    setEnviando(false)
  }
  
  return {
    valores,
    errores,
    tocados,
    enviando,
    handleChange,
    handleBlur,
    validarTodo,
    resetForm,
    setEnviando
  }
}

// Uso del hook
function FormularioContacto() {
  const validaciones = {
    nombre: (valor) => {
      if (!valor) return 'El nombre es requerido'
      if (valor.length < 3) return 'Mínimo 3 caracteres'
      return ''
    },
    email: (valor) => {
      if (!valor) return 'El email es requerido'
      if (!/\S+@\S+\.\S+/.test(valor)) return 'Email inválido'
      return ''
    },
    mensaje: (valor) => {
      if (!valor) return 'El mensaje es requerido'
      if (valor.length < 10) return 'Mínimo 10 caracteres'
      return ''
    }
  }
  
  const {
    valores,
    errores,
    tocados,
    enviando,
    handleChange,
    handleBlur,
    validarTodo,
    resetForm,
    setEnviando
  } = useForm(
    { nombre: '', email: '', mensaje: '' },
    validaciones
  )
  
  const handleSubmit = async (e) => {
    e.preventDefault()
    
    if (validarTodo()) {
      setEnviando(true)
      
      try {
        // Simular envío
        await new Promise(resolve => setTimeout(resolve, 2000))
        console.log('Enviado:', valores)
        resetForm()
        alert('¡Mensaje enviado!')
      } catch (error) {
        alert('Error al enviar')
      } finally {
        setEnviando(false)
      }
    }
  }
  
  return (
    <form onSubmit={handleSubmit}>
      {/* Campos del formulario usando el hook */}
      <input
        name="nombre"
        value={valores.nombre}
        onChange={handleChange}
        onBlur={handleBlur}
      />
      {errores.nombre && tocados.nombre && <span>{errores.nombre}</span>}
      
      {/* Más campos... */}
      
      <button type="submit" disabled={enviando}>
        {enviando ? 'Enviando...' : 'Enviar'}
      </button>
    </form>
  )
}
```

---

## 📊 Estados Derivados <a name="estados-derivados"></a>

### ¿Qué es un estado derivado?

Estado derivado es información que puede ser **calculada** a partir de otro estado existente, en lugar de almacenarla separadamente.

```jsx
// ❌ MAL - Estado redundante
function ListaCompras() {
  const [items, setItems] = useState([])
  const [total, setTotal] = useState(0)  // ¡Estado redundante!
  
  const agregarItem = (item) => {
    setItems([...items, item])
    // Fácil de olvidar actualizar el total
    setTotal(total + item.precio)  // Puede desincronizarse
  }
}

// ✅ BIEN - Estado derivado
function ListaCompras() {
  const [items, setItems] = useState([])
  
  // Calculado cuando se necesita
  const total = items.reduce((sum, item) => sum + item.precio, 0)
  
  const agregarItem = (item) => {
    setItems([...items, item])
    // No necesitas actualizar el total manualmente
  }
}
```

### Ejemplos de estados derivados

#### 1. Filtrado y búsqueda

```jsx
function ListaUsuarios() {
  // Estados primarios
  const [usuarios, setUsuarios] = useState([
    { id: 1, nombre: 'Ana', activo: true, rol: 'admin' },
    { id: 2, nombre: 'Carlos', activo: false, rol: 'user' },
    { id: 3, nombre: 'Beatriz', activo: true, rol: 'user' }
  ])
  const [busqueda, setBusqueda] = useState('')
  const [filtroRol, setFiltroRol] = useState('todos')
  const [soloActivos, setSoloActivos] = useState(false)
  
  // Estados derivados - calculados a partir de los primarios
  const usuariosFiltrados = usuarios
    .filter(usuario => {
      // Filtro por búsqueda
      if (busqueda && !usuario.nombre.toLowerCase().includes(busqueda.toLowerCase())) {
        return false
      }
      // Filtro por rol
      if (filtroRol !== 'todos' && usuario.rol !== filtroRol) {
        return false
      }
      // Filtro por estado
      if (soloActivos && !usuario.activo) {
        return false
      }
      return true
    })
  
  // Más estados derivados
  const estadisticas = {
    total: usuarios.length,
    activos: usuarios.filter(u => u.activo).length,
    inactivos: usuarios.filter(u => !u.activo).length,
    admins: usuarios.filter(u => u.rol === 'admin').length,
    mostrados: usuariosFiltrados.length
  }
  
  return (
    <div>
      <div className="filtros">
        <input
          type="text"
          placeholder="Buscar por nombre..."
          value={busqueda}
          onChange={(e) => setBusqueda(e.target.value)}
        />
        
        <select value={filtroRol} onChange={(e) => setFiltroRol(e.target.value)}>
          <option value="todos">Todos los roles</option>
          <option value="admin">Admin</option>
          <option value="user">Usuario</option>
        </select>
        
        <label>
          <input
            type="checkbox"
            checked={soloActivos}
            onChange={(e) => setSoloActivos(e.target.checked)}
          />
          Solo activos
        </label>
      </div>
      
      <div className="estadisticas">
        <p>Mostrando {estadisticas.mostrados} de {estadisticas.total} usuarios</p>
        <p>Activos: {estadisticas.activos} | Inactivos: {estadisticas.inactivos}</p>
      </div>
      
      <ul>
        {usuariosFiltrados.map(usuario => (
          <li key={usuario.id}>
            {usuario.nombre} - {usuario.rol} 
            {usuario.activo ? '✅' : '❌'}
          </li>
        ))}
      </ul>
    </div>
  )
}
```

#### 2. Validación y estado del formulario

```jsx
function FormularioAvanzado() {
  const [campos, setCampos] = useState({
    nombre: '',
    email: '',
    telefono: '',
    mensaje: ''
  })
  
  // Estados derivados de validación
  const errores = {
    nombre: campos.nombre.length < 3 ? 'Mínimo 3 caracteres' : '',
    email: !campos.email.includes('@') ? 'Email inválido' : '',
    telefono: campos.telefono && !/^\d{10}$/.test(campos.telefono) ? 'Debe ser 10 dígitos' : '',
    mensaje: campos.mensaje.length < 10 ? 'Mínimo 10 caracteres' : ''
  }
  
  // Estado derivado: ¿El formulario es válido?
  const formularioValido = Object.values(errores).every(error => error === '')
  
  // Estado derivado: ¿Qué campos están completos?
  const camposCompletos = Object.entries(campos)
    .filter(([_, valor]) => valor !== '')
    .map(([campo]) => campo)
  
  // Estado derivado: Progreso del formulario
  const progreso = (camposCompletos.length / Object.keys(campos).length) * 100
  
  return (
    <form>
      {/* Barra de progreso */}
      <div className="progreso">
        <div 
          className="progreso-barra" 
          style={{ width: `${progreso}%` }}
        />
        <span>{progreso.toFixed(0)}% completado</span>
      </div>
      
      {/* Campos del formulario */}
      {Object.keys(campos).map(campo => (
        <div key={campo}>
          <input
            name={campo}
            placeholder={campo}
            value={campos[campo]}
            onChange={(e) => setCampos({
              ...campos,
              [e.target.name]: e.target.value
            })}
          />
          {errores[campo] && <span className="error">{errores[campo]}</span>}
        </div>
      ))}
      
      <button disabled={!formularioValido}>
        Enviar
      </button>
    </form>
  )
}
```