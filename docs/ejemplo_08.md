# 🎯 Ejercicio Práctico: Sistema de Registro con Formularios Avanzados

## 📋 Objetivo del ejercicio

Construir un sistema completo de registro de usuarios que implemente:
- Formulario multi-paso con validación
- Estados derivados para progreso y validación
- Formateo automático de campos
- Prevención de envíos duplicados
- Feedback visual en tiempo real

---

## 📁 Estructura del proyecto

```
registro-avanzado/
├── src/
│   ├── App.jsx
│   ├── components/
│   │   ├── FormularioRegistro.jsx
│   │   ├── PasoInformacionPersonal.jsx
│   │   ├── PasoContacto.jsx
│   │   ├── PasoSeguridad.jsx
│   │   ├── ResumenRegistro.jsx
│   │   └── common/
│   │       ├── Campo.jsx
│   │       ├── IndicadorProgreso.jsx
│   │       └── IndicadorFuerza.jsx
│   ├── hooks/
│   │   ├── useFormulario.js
│   │   └── useValidacion.js
│   └── utils/
│       ├── validaciones.js
│       └── formatters.js
```

---

## 🔧 Implementación del sistema

### 1. Hook personalizado para formularios (useFormulario.js)

```jsx
import { useState, useCallback, useMemo } from 'react'

export function useFormulario(valoresIniciales, validaciones = {}) {
  const [valores, setValores] = useState(valoresIniciales)
  const [tocados, setTocados] = useState({})
  const [enviando, setEnviando] = useState(false)
  
  // Manejar cambio de valores
  const handleChange = useCallback((e) => {
    const { name, value, type, checked, files } = e.target
    
    setValores(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : 
               type === 'file' ? files[0] : 
               value
    }))
  }, [])
  
  // Manejar blur
  const handleBlur = useCallback((e) => {
    const { name } = e.target
    setTocados(prev => ({
      ...prev,
      [name]: true
    }))
  }, [])
  
  // Establecer valor programáticamente
  const setValue = useCallback((name, value) => {
    setValores(prev => ({
      ...prev,
      [name]: value
    }))
  }, [])
  
  // Establecer múltiples valores
  const setValues = useCallback((newValues) => {
    setValores(prev => ({
      ...prev,
      ...newValues
    }))
  }, [])
  
  // Calcular errores (estado derivado)
  const errores = useMemo(() => {
    const erroresCalculados = {}
    
    Object.keys(validaciones).forEach(campo => {
      const validacion = validaciones[campo]
      const valor = valores[campo]
      
      if (typeof validacion === 'function') {
        erroresCalculados[campo] = validacion(valor, valores)
      } else if (Array.isArray(validacion)) {
        // Múltiples validaciones
        for (const fn of validacion) {
          const error = fn(valor, valores)
          if (error) {
            erroresCalculados[campo] = error
            break
          }
        }
      }
    })
    
    return erroresCalculados
  }, [valores, validaciones])
  
  // Estados derivados útiles
  const camposConError = Object.keys(errores).filter(campo => errores[campo])
  const formularioValido = camposConError.length === 0
  const porcentajeCompletado = (
    Object.keys(valores).filter(campo => valores[campo] !== '').length / 
    Object.keys(valores).length * 100
  )
  
  // Resetear formulario
  const reset = useCallback(() => {
    setValores(valoresIniciales)
    setTocados({})
    setEnviando(false)
  }, [valoresIniciales])
  
  // Validar todo
  const validarTodo = useCallback(() => {
    const todosTocados = Object.keys(valores).reduce((acc, campo) => ({
      ...acc,
      [campo]: true
    }), {})
    setTocados(todosTocados)
    return formularioValido
  }, [valores, formularioValido])
  
  return {
    valores,
    errores,
    tocados,
    enviando,
    formularioValido,
    porcentajeCompletado,
    camposConError,
    handleChange,
    handleBlur,
    setValue,
    setValues,
    setEnviando,
    reset,
    validarTodo
  }
}
```

### 2. Utilidades de validación (validaciones.js)

```jsx
// Validaciones reutilizables
export const requerido = (mensaje = 'Este campo es requerido') => 
  (valor) => !valor ? mensaje : ''

export const minLength = (min, mensaje) => 
  (valor) => valor && valor.length < min 
    ? mensaje || `Mínimo ${min} caracteres` 
    : ''

export const maxLength = (max, mensaje) => 
  (valor) => valor && valor.length > max 
    ? mensaje || `Máximo ${max} caracteres` 
    : ''

export const email = (mensaje = 'Email inválido') => 
  (valor) => valor && !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(valor) 
    ? mensaje 
    : ''

export const telefono = (mensaje = 'Teléfono inválido') => 
  (valor) => {
    const soloNumeros = valor.replace(/\D/g, '')
    return soloNumeros.length !== 10 ? mensaje : ''
  }

export const contrasena = (valor) => {
  if (!valor) return 'La contraseña es requerida'
  if (valor.length < 8) return 'Mínimo 8 caracteres'
  if (!/[a-z]/.test(valor)) return 'Debe contener minúsculas'
  if (!/[A-Z]/.test(valor)) return 'Debe contener mayúsculas'
  if (!/\d/.test(valor)) return 'Debe contener números'
  if (!/[!@#$%^&*]/.test(valor)) return 'Debe contener caracteres especiales'
  return ''
}

export const confirmarContrasena = (valor, valores) => {
  if (!valor) return 'Confirma tu contraseña'
  if (valor !== valores.contrasena) return 'Las contraseñas no coinciden'
  return ''
}

export const fechaNacimiento = (valor) => {
  if (!valor) return 'La fecha es requerida'
  
  const fecha = new Date(valor)
  const hoy = new Date()
  const edad = Math.floor((hoy - fecha) / (365.25 * 24 * 60 * 60 * 1000))
  
  if (edad < 18) return 'Debes ser mayor de 18 años'
  if (edad > 120) return 'Fecha inválida'
  
  return ''
}

// Combinar validaciones
export const combinar = (...validaciones) => (valor, valores) => {
  for (const validacion of validaciones) {
    const error = validacion(valor, valores)
    if (error) return error
  }
  return ''
}
```

### 3. Formatters (formatters.js)

```jsx
// Formatear teléfono: (555) 123-4567
export const formatearTelefono = (valor) => {
  const numeros = valor.replace(/\D/g, '')
  
  if (numeros.length <= 3) return numeros
  if (numeros.length <= 6) {
    return `(${numeros.slice(0, 3)}) ${numeros.slice(3)}`
  }
  return `(${numeros.slice(0, 3)}) ${numeros.slice(3, 6)}-${numeros.slice(6, 10)}`
}

// Formatear tarjeta: 1234 5678 9012 3456
export const formatearTarjeta = (valor) => {
  const numeros = valor.replace(/\D/g, '')
  const grupos = numeros.match(/.{1,4}/g) || []
  return grupos.join(' ').substring(0, 19)
}

// Formatear fecha: MM/DD/YYYY
export const formatearFecha = (valor) => {
  const numeros = valor.replace(/\D/g, '')
  
  if (numeros.length <= 2) return numeros
  if (numeros.length <= 4) {
    return `${numeros.slice(0, 2)}/${numeros.slice(2)}`
  }
  return `${numeros.slice(0, 2)}/${numeros.slice(2, 4)}/${numeros.slice(4, 8)}`
}

// Capitalizar nombre
export const capitalizarNombre = (valor) => {
  return valor
    .split(' ')
    .map(palabra => palabra.charAt(0).toUpperCase() + palabra.slice(1).toLowerCase())
    .join(' ')
}

// Limpiar espacios extras
export const limpiarEspacios = (valor) => {
  return valor.replace(/\s+/g, ' ').trim()
}
```

### 4. Componente Campo reutilizable (Campo.jsx)

```jsx
import { useState } from 'react'

function Campo({ 
  label, 
  tipo = 'text',
  nombre,
  valor,
  onChange,
  onBlur,
  error,
  tocado,
  requerido,
  ayuda,
  formatter,
  ...props 
}) {
  const [mostrarPassword, setMostrarPassword] = useState(false)
  
  const handleChange = (e) => {
    let nuevoValor = e.target.value
    
    // Aplicar formatter si existe
    if (formatter) {
      nuevoValor = formatter(nuevoValor)
    }
    
    // Crear evento sintético con el valor formateado
    const eventoSintetico = {
      target: {
        name: nombre,
        value: nuevoValor,
        type: tipo
      }
    }
    
    onChange(eventoSintetico)
  }
  
  const tipoInput = tipo === 'password' && mostrarPassword ? 'text' : tipo
  const mostrarError = error && tocado
  
  return (
    <div className={`campo ${mostrarError ? 'campo-error' : ''}`}>
      <label htmlFor={nombre}>
        {label}
        {requerido && <span className="asterisco">*</span>}
      </label>
      
      <div className="campo-input-wrapper">
        <input
          id={nombre}
          name={nombre}
          type={tipoInput}
          value={valor}
          onChange={handleChange}
          onBlur={onBlur}
          className={mostrarError ? 'input-error' : ''}
          aria-invalid={mostrarError}
          aria-describedby={mostrarError ? `${nombre}-error` : undefined}
          {...props}
        />
        
        {tipo === 'password' && (
          <button
            type="button"
            className="toggle-password"
            onClick={() => setMostrarPassword(!mostrarPassword)}
            aria-label={mostrarPassword ? 'Ocultar contraseña' : 'Mostrar contraseña'}
          >
            {mostrarPassword ? '👁️' : '👁️‍🗨️'}
          </button>
        )}
      </div>
      
      {ayuda && !mostrarError && (
        <span className="campo-ayuda">{ayuda}</span>
      )}
      
      {mostrarError && (
        <span id={`${nombre}-error`} className="campo-error-mensaje">
          {error}
        </span>
      )}
    </div>
  )
}

export default Campo
```

### 5. Formulario multi-paso principal (FormularioRegistro.jsx)

```jsx
import { useState } from 'react'
import { useFormulario } from '../hooks/useFormulario'
import PasoInformacionPersonal from './PasoInformacionPersonal'
import PasoContacto from './PasoContacto'
import PasoSeguridad from './PasoSeguridad'
import ResumenRegistro from './ResumenRegistro'
import IndicadorProgreso from './common/IndicadorProgreso'
import * as validaciones from '../utils/validaciones'

const PASOS = [
  { id: 1, nombre: 'Información Personal', componente: PasoInformacionPersonal },
  { id: 2, nombre: 'Contacto', componente: PasoContacto },
  { id: 3, nombre: 'Seguridad', componente: PasoSeguridad },
  { id: 4, nombre: 'Resumen', componente: ResumenRegistro }
]

function FormularioRegistro() {
  const [pasoActual, setPasoActual] = useState(1)
  const [datosGuardados, setDatosGuardados] = useState({})
  
  const formulario = useFormulario(
    {
      // Información personal
      nombre: '',
      apellidos: '',
      fechaNacimiento: '',
      genero: '',
      
      // Contacto
      email: '',
      telefono: '',
      direccion: '',
      ciudad: '',
      codigoPostal: '',
      pais: '',
      
      // Seguridad
      nombreUsuario: '',
      contrasena: '',
      confirmarContrasena: '',
      preguntaSeguridad: '',
      respuestaSeguridad: '',
      aceptaTerminos: false,
      recibirNotificaciones: false
    },
    {
      // Validaciones por campo
      nombre: [validaciones.requerido(), validaciones.minLength(2)],
      apellidos: [validaciones.requerido(), validaciones.minLength(2)],
      fechaNacimiento: validaciones.fechaNacimiento,
      email: [validaciones.requerido(), validaciones.email()],
      telefono: validaciones.telefono(),
      contrasena: validaciones.contrasena,
      confirmarContrasena: validaciones.confirmarContrasena,
      nombreUsuario: [
        validaciones.requerido(),
        validaciones.minLength(4),
        validaciones.maxLength(20)
      ],
      aceptaTerminos: (valor) => !valor ? 'Debes aceptar los términos' : ''
    }
  )
  
  // Estados derivados para validación por pasos
  const validacionPorPasos = {
    1: ['nombre', 'apellidos', 'fechaNacimiento', 'genero'],
    2: ['email', 'telefono', 'direccion', 'ciudad', 'codigoPostal', 'pais'],
    3: ['nombreUsuario', 'contrasena', 'confirmarContrasena', 'aceptaTerminos']
  }
  
  // Estado derivado: ¿El paso actual es válido?
  const pasoValido = validacionPorPasos[pasoActual]?.every(
    campo => !formulario.errores[campo] && formulario.valores[campo] !== ''
  ) ?? true
  
  // Navegación entre pasos
  const siguientePaso = () => {
    if (pasoActual < PASOS.length) {
      // Guardar datos del paso actual
      const camposPaso = validacionPorPasos[pasoActual] || []
      const datosPaso = camposPaso.reduce((acc, campo) => ({
        ...acc,
        [campo]: formulario.valores[campo]
      }), {})
      
      setDatosGuardados(prev => ({
        ...prev,
        ...datosPaso
      }))
      
      setPasoActual(pasoActual + 1)
    }
  }
  
  const pasoAnterior = () => {
    if (pasoActual > 1) {
      setPasoActual(pasoActual - 1)
    }
  }
  
  // Enviar formulario
  const handleSubmit = async (e) => {
    e.preventDefault()
    
    if (!formulario.validarTodo()) {
      alert('Por favor corrige los errores antes de continuar')
      return
    }
    
    formulario.setEnviando(true)
    
    try {
      // Simular envío a API
      await new Promise(resolve => setTimeout(resolve, 2000))
      
      console.log('Datos enviados:', {
        ...datosGuardados,
        ...formulario.valores
      })
      
      alert('¡Registro exitoso!')
      formulario.reset()
      setPasoActual(1)
      setDatosGuardados({})
      
    } catch (error) {
      alert('Error al registrar. Intenta nuevamente.')
    } finally {
      formulario.setEnviando(false)

    try {
      await new Promise(resolve => setTimeout(resolve, 2000))
      console.log('Datos enviados:', {
        ...datosGuardados,
        ...formulario.valores
      })
      alert('¡Registro exitoso!')
      formulario.reset()
      setPasoActual(1)
      setDatosGuardados({})
    } catch (error) {
      alert('Error al registrar. Intenta nuevamente.')
    } finally {
      formulario.setEnviando(false)
    }
  }

  const PasoComponente = PASOS.find(p => p.id === pasoActual)?.componente

  return (
    <form onSubmit={handleSubmit}>
      <IndicadorProgreso pasoActual={pasoActual} totalPasos={PASOS.length} />
      
      {PasoComponente && (
        <PasoComponente
          valores={formulario.valores}
          errores={formulario.errores}
          actualizarCampo={formulario.actualizarCampo}
        />
      )}

      <div className="acciones">
        {pasoActual > 1 && (
          <button type="button" onClick={pasoAnterior}>
            Anterior
          </button>
        )}

        {pasoActual < PASOS.length ? (
          <button type="button" onClick={siguientePaso} disabled={!pasoValido}>
            Siguiente
          </button>
        ) : (
          <button type="submit" disabled={formulario.enviando}>
            {formulario.enviando ? 'Enviando...' : 'Enviar'}
          </button>
        )}
      </div>
    </form>
  )
}

export default FormularioRegistro
```

### 6. Paso 1 - Información Personal (PasoInformacionPersonal.jsx)

```jsx
import Campo from './common/Campo'
import { capitalizarNombre, formatearFecha } from '../utils/formatters'

function PasoInformacionPersonal({ formulario }) {
  const { valores, errores, tocados, handleChange, handleBlur } = formulario
  
  // Estado derivado: edad calculada
  const edad = valores.fechaNacimiento ? 
    Math.floor((new Date() - new Date(valores.fechaNacimiento)) / (365.25 * 24 * 60 * 60 * 1000)) : 0
  
  return (
    <div className="paso-formulario">
      <h2>Información Personal</h2>
      <p>Completa tus datos personales básicos</p>
      
      <div className="grid-2-columnas">
        <Campo
          label="Nombre"
          nombre="nombre"
          valor={valores.nombre}
          onChange={handleChange}
          onBlur={handleBlur}
          error={errores.nombre}
          tocado={tocados.nombre}
          requerido
          formatter={capitalizarNombre}
          placeholder="Juan"
        />
        
        <Campo
          label="Apellidos"
          nombre="apellidos"
          valor={valores.apellidos}
          onChange={handleChange}
          onBlur={handleBlur}
          error={errores.apellidos}
          tocado={tocados.apellidos}
          requerido
          formatter={capitalizarNombre}
          placeholder="Pérez García"
        />
      </div>
      
      <Campo
        label="Fecha de nacimiento"
        tipo="date"
        nombre="fechaNacimiento"
        valor={valores.fechaNacimiento}
        onChange={handleChange}
        onBlur={handleBlur}
        error={errores.fechaNacimiento}
        tocado={tocados.fechaNacimiento}
        requerido
        max={new Date().toISOString().split('T')[0]}
        ayuda={edad > 0 ? `Edad: ${edad} años` : ''}
      />
      
      <fieldset className="grupo-radios">
        <legend>Género</legend>
        
        <label className="radio-label">
          <input
            type="radio"
            name="genero"
            value="masculino"
            checked={valores.genero === 'masculino'}
            onChange={handleChange}
          />
          <span>Masculino</span>
        </label>
        
        <label className="radio-label">
          <input
            type="radio"
            name="genero"
            value="femenino"
            checked={valores.genero === 'femenino'}
            onChange={handleChange}
          />
          <span>Femenino</span>
        </label>
        
        <label className="radio-label">
          <input
            type="radio"
            name="genero"
            value="otro"
            checked={valores.genero === 'otro'}
            onChange={handleChange}
          />
          <span>Otro</span>
        </label>
        
        <label className="radio-label">
          <input
            type="radio"
            name="genero"
            value="prefiero-no-decir"
            checked={valores.genero === 'prefiero-no-decir'}
            onChange={handleChange}
          />
          <span>Prefiero no decir</span>
        </label>
      </fieldset>
    </div>
  )
}

export default PasoInformacionPersonal
```

### 7. Paso 2 - Información de Contacto (PasoContacto.jsx)

```jsx
import Campo from './common/Campo'
import { formatearTelefono } from '../utils/formatters'

function PasoContacto({ formulario }) {
  const { valores, errores, tocados, handleChange, handleBlur } = formulario
  
  // Países disponibles
  const paises = [
    { codigo: '', nombre: 'Selecciona un país' },
    { codigo: 'MX', nombre: 'México' },
    { codigo: 'ES', nombre: 'España' },
    { codigo: 'AR', nombre: 'Argentina' },
    { codigo: 'CO', nombre: 'Colombia' },
    { codigo: 'CL', nombre: 'Chile' },
    { codigo: 'PE', nombre: 'Perú' }
  ]
  
  return (
    <div className="paso-formulario">
      <h2>Información de Contacto</h2>
      <p>¿Cómo podemos contactarte?</p>
      
      <Campo
        label="Correo electrónico"
        tipo="email"
        nombre="email"
        valor={valores.email}
        onChange={handleChange}
        onBlur={handleBlur}
        error={errores.email}
        tocado={tocados.email}
        requerido
        placeholder="juan@ejemplo.com"
        ayuda="Usaremos este email para tu cuenta"
      />
      
      <Campo
        label="Teléfono"
        tipo="tel"
        nombre="telefono"
        valor={valores.telefono}
        onChange={handleChange}
        onBlur={handleBlur}
        error={errores.telefono}
        tocado={tocados.telefono}
        formatter={formatearTelefono}
        placeholder="(555) 123-4567"
        ayuda="10 dígitos, incluye código de área"
      />
      
      <Campo
        label="Dirección"
        nombre="direccion"
        valor={valores.direccion}
        onChange={handleChange}
        onBlur={handleBlur}
        placeholder="Calle Principal 123"
      />
      
      <div className="grid-3-columnas">
        <Campo
          label="Ciudad"
          nombre="ciudad"
          valor={valores.ciudad}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Ciudad de México"
        />
        
        <Campo
          label="Código Postal"
          nombre="codigoPostal"
          valor={valores.codigoPostal}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="12345"
          maxLength="5"
        />
        
        <div className="campo">
          <label htmlFor="pais">País</label>
          <select
            id="pais"
            name="pais"
            value={valores.pais}
            onChange={handleChange}
            onBlur={handleBlur}
          >
            {paises.map(pais => (
              <option key={pais.codigo} value={pais.codigo}>
                {pais.nombre}
              </option>
            ))}
          </select>
        </div>
      </div>
    </div>
  )
}

export default PasoContacto
```

### 8. Paso 3 - Seguridad (PasoSeguridad.jsx)

```jsx
import { useState } from 'react'
import Campo from './common/Campo'
import IndicadorFuerza from './common/IndicadorFuerza'

function PasoSeguridad({ formulario }) {
  const { valores, errores, tocados, handleChange, handleBlur } = formulario
  const [verificandoUsuario, setVerificandoUsuario] = useState(false)
  
  // Verificar disponibilidad de usuario (simulado)
  const verificarUsuario = async (nombreUsuario) => {
    if (nombreUsuario.length < 4) return
    
    setVerificandoUsuario(true)
    
    // Simular llamada API
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    const usuariosExistentes = ['admin', 'usuario', 'test']
    const disponible = !usuariosExistentes.includes(nombreUsuario.toLowerCase())
    
    setVerificandoUsuario(false)
    
    if (!disponible) {
      formulario.setValue('nombreUsuario', '')
      alert('Este nombre de usuario ya está en uso')
    }
  }
  
  const preguntasSeguridad = [
    'Selecciona una pregunta de seguridad',
    '¿Cuál es el nombre de tu primera mascota?',
    '¿En qué ciudad naciste?',
    '¿Cuál es el apellido de soltera de tu madre?',
    '¿Cuál es tu película favorita?',
    '¿Cuál fue tu primera escuela?'
  ]
  
  return (
    <div className="paso-formulario">
      <h2>Configuración de Seguridad</h2>
      <p>Protege tu cuenta con una contraseña segura</p>
      
      <Campo
        label="Nombre de usuario"
        nombre="nombreUsuario"
        valor={valores.nombreUsuario}
        onChange={handleChange}
        onBlur={(e) => {
          handleBlur(e)
          verificarUsuario(e.target.value)
        }}
        error={errores.nombreUsuario}
        tocado={tocados.nombreUsuario}
        requerido
        placeholder="usuario123"
        ayuda={verificandoUsuario ? 'Verificando disponibilidad...' : 
               'Entre 4 y 20 caracteres, sin espacios'}
      />
      
      <Campo
        label="Contraseña"
        tipo="password"
        nombre="contrasena"
        valor={valores.contrasena}
        onChange={handleChange}
        onBlur={handleBlur}
        error={errores.contrasena}
        tocado={tocados.contrasena}
        requerido
        ayuda="Mínimo 8 caracteres con mayúsculas, minúsculas, números y símbolos"
      />
      
      {valores.contrasena && (
        <IndicadorFuerza password={valores.contrasena} />
      )}
      
      <Campo
        label="Confirmar contraseña"
        tipo="password"
        nombre="confirmarContrasena"
        valor={valores.confirmarContrasena}
        onChange={handleChange}
        onBlur={handleBlur}
        error={errores.confirmarContrasena}
        tocado={tocados.confirmarContrasena}
        requerido
      />
      
      <div className="campo">
        <label htmlFor="preguntaSeguridad">Pregunta de seguridad</label>
        <select
          id="preguntaSeguridad"
          name="preguntaSeguridad"
          value={valores.preguntaSeguridad}
          onChange={handleChange}
          onBlur={handleBlur}
        >
          {preguntasSeguridad.map((pregunta, index) => (
            <option key={index} value={index === 0 ? '' : pregunta}>
              {pregunta}
            </option>
          ))}
        </select>
      </div>
      
      {valores.preguntaSeguridad && (
        <Campo
          label="Respuesta"
          nombre="respuestaSeguridad"
          valor={valores.respuestaSeguridad}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Tu respuesta"
          ayuda="Guarda esta respuesta, la necesitarás para recuperar tu cuenta"
        />
      )}
      
      <div className="checkboxes">
        <label className="checkbox-label">
          <input
            type="checkbox"
            name="aceptaTerminos"
            checked={valores.aceptaTerminos}
            onChange={handleChange}
          />
          <span>
            Acepto los <a href="/terminos" target="_blank">términos y condiciones</a>
          </span>
        </label>
        {errores.aceptaTerminos && tocados.aceptaTerminos && (
          <span className="error">{errores.aceptaTerminos}</span>
        )}
        
        <label className="checkbox-label">
          <input
            type="checkbox"
            name="recibirNotificaciones"
            checked={valores.recibirNotificaciones}
            onChange={handleChange}
          />
          <span>Deseo recibir notificaciones y ofertas por email</span>
        </label>
      </div>
    </div>
  )
}

export default PasoSeguridad
```

### 9. Paso 4 - Resumen (ResumenRegistro.jsx)

```jsx
function ResumenRegistro({ formulario, datosGuardados }) {
  const todosLosDatos = { ...datosGuardados, ...formulario.valores }
  
  // Agrupar datos por sección
  const secciones = {
    'Información Personal': {
      'Nombre completo': `${todosLosDatos.nombre} ${todosLosDatos.apellidos}`,
      'Fecha de nacimiento': new Date(todosLosDatos.fechaNacimiento).toLocaleDateString(),
      'Género': todosLosDatos.genero || 'No especificado'
    },
    'Contacto': {
      'Email': todosLosDatos.email,
      'Teléfono': todosLosDatos.telefono,
      'Dirección': todosLosDatos.direccion,
      'Ciudad': todosLosDatos.ciudad,
      'Código Postal': todosLosDatos.codigoPostal,
      'País': todosLosDatos.pais
    },
    'Cuenta': {
      'Nombre de usuario': todosLosDatos.nombreUsuario,
      'Notificaciones': todosLosDatos.recibirNotificaciones ? 'Sí' : 'No',
      'Términos aceptados': todosLosDatos.aceptaTerminos ? 'Sí' : 'No'
    }
  }
  
  return (
    <div className="paso-formulario resumen">
      <h2>Resumen de tu registro</h2>
      <p>Revisa que toda la información sea correcta antes de continuar</p>
      
      {Object.entries(secciones).map(([titulo, campos]) => (
        <div key={titulo} className="seccion-resumen">
          <h3>{titulo}</h3>
          <dl>
            {Object.entries(campos).map(([campo, valor]) => (
              valor && (
                <div key={campo} className="campo-resumen">
                  <dt>{campo}:</dt>
                  <dd>{valor}</dd>
                </div>
              )
            ))}
          </dl>
        </div>
      ))}
      
      <div className="mensaje-confirmacion">
        <p>
          <strong>⚠️ Importante:</strong> Una vez completado el registro, 
          recibirás un correo de confirmación en <strong>{todosLosDatos.email}</strong>
        </p>
      </div>
    </div>
  )
}

export default ResumenRegistro
```

### 10. Indicador de Progreso (IndicadorProgreso.jsx)

```jsx
function IndicadorProgreso({ pasos, pasoActual, completados }) {
  const porcentaje = ((pasoActual - 1) / (pasos.length - 1)) * 100
  
  return (
    <div className="indicador-progreso">
      <div className="barra-progreso">
        <div 
          className="progreso-fill"
          style={{ width: `${porcentaje}%` }}
        />
      </div>
      
      <div className="pasos">
        {pasos.map((paso, index) => {
          const numeroPaso = index + 1
          const estaActivo = numeroPaso === pasoActual
          const estaCompletado = numeroPaso < pasoActual
          
          return (
            <div 
              key={paso.id}
              className={`
                paso 
                ${estaActivo ? 'activo' : ''} 
                ${estaCompletado ? 'completado' : ''}
              `}
            >
              <div className="paso-circulo">
                {estaCompletado ? '✓' : numeroPaso}
              </div>
              <span className="paso-nombre">{paso.nombre}</span>
            </div>
          )
        })}
      </div>
    </div>
  )
}

export default IndicadorProgreso
```

### 11. Indicador de Fuerza de Contraseña (IndicadorFuerza.jsx)

```jsx
function IndicadorFuerza({ password }) {
  // Calcular fuerza de la contraseña
  const calcularFuerza = () => {
    let puntos = 0
    
    // Longitud
    if (password.length >= 8) puntos++
    if (password.length >= 12) puntos++
    if (password.length >= 16) puntos++
    
    // Complejidad
    if (/[a-z]/.test(password)) puntos++
    if (/[A-Z]/.test(password)) puntos++
    if (/\d/.test(password)) puntos++
    if (/[!@#$%^&*(),.?":{}|<>]/.test(password)) puntos++
    
    // Patrones comunes (resta puntos)
    if (/12345|password|qwerty/i.test(password)) puntos -= 2
    
    return Math.max(0, Math.min(5, Math.floor(puntos * 5 / 7)))
  }
  
  const fuerza = calcularFuerza()
  const niveles = ['Muy débil', 'Débil', 'Aceptable', 'Buena', 'Fuerte', 'Muy fuerte']
  const colores = ['#e74c3c', '#e67e22', '#f39c12', '#f1c40f', '#2ecc71', '#27ae60']
  
  // Sugerencias basadas en lo que falta
  const sugerencias = []
  if (password.length < 8) sugerencias.push('Usa al menos 8 caracteres')
  if (!/[a-z]/.test(password)) sugerencias.push('Agrega letras minúsculas')
  if (!/[A-Z]/.test(password)) sugerencias.push('Agrega letras mayúsculas')
  if (!/\d/.test(password)) sugerencias.push('Agrega números')
  if (!/[!@#$%^&*(),.?":{}|<>]/.test(password)) sugerencias.push('Agrega símbolos especiales')
  
  return (
    <div className="indicador-fuerza">
      <div className="barras-fuerza">
        {[...Array(6)].map((_, i) => (
          <div
            key={i}
            className="barra"
            style={{
              backgroundColor: i <= fuerza ? colores[fuerza] : '#ecf0f1',
              height: `${10 + i * 2}px`
            }}
          />
        ))}
      </div>
      
      <div className="fuerza-info">
        <span 
          className="nivel"
          style={{ color: colores[fuerza] }}
        >
          {niveles[fuerza]}
        </span>
        
        {sugerencias.length > 0 && (
          <ul className="sugerencias">
            {sugerencias.map((sugerencia, index) => (
              <li key={index}>{sugerencia}</li>
            ))}
          </ul>
        )}
      </div>
    </div>
  )
}

export default IndicadorFuerza
```

### 12. App principal (App.jsx)

```jsx
import FormularioRegistro from './components/FormularioRegistro'
import './App.css'

function App() {
  return (
    <div className="app">
      <div className="container">
        <FormularioRegistro />
      </div>
    </div>
  )
}

export default App
```

### 13. Estilos CSS (App.css)

```css
/* Variables */
:root {
  --color-primario: #3498db;
  --color-primario-hover: #2980b9;
  --color-secundario: #95a5a6;
  --color-exito: #2ecc71;
  --color-error: #e74c3c;
  --color-advertencia: #f39c12;
  --color-fondo: #f8f9fa;
  --color-borde: #dee2e6;
  --sombra-suave: 0 2px 4px rgba(0,0,0,0.1);
  --sombra-media: 0 4px 6px rgba(0,0,0,0.1);
}

/* Reset y base */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background-color: var(--color-fondo);
  color: #333;
  line-height: 1.6;
}

.app {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem;
}

.container {
  width: 100%;
  max-width: 800px;
}

/* Formulario principal */
.formulario-registro {
  background: white;
  border-radius: 8px;
  box-shadow: var(--sombra-media);
  padding: 2rem;
}

.formulario-registro h1 {
  text-align: center;
  margin-bottom: 2rem;
  color: var(--color-primario);
}

/* Indicador de progreso */
.indicador-progreso {
  margin-bottom: 3rem;
}

.barra-progreso {
  height: 4px;
  background-color: var(--color-borde);
  border-radius: 2px;
  overflow: hidden;
  margin-bottom: 2rem;
}

.progreso-fill {
  height: 100%;
  background-color: var(--color-primario);
  transition: width 0.3s ease;
}

.pasos {
  display: flex;
  justify-content: space-between;
}

.paso {
  display: flex;
  flex-direction: column;
  align-items: center;
  opacity: 0.5;
  transition: opacity 0.3s;
}

.paso.activo,
.paso.completado {
  opacity: 1;
}

.paso-circulo {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: var(--color-borde);
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  margin-bottom: 0.5rem;
  transition: all 0.3s;
}

.paso.activo .paso-circulo {
  background-color: var(--color-primario);
  color: white;
  transform: scale(1.1);
}

.paso.completado .paso-circulo {
  background-color: var(--color-exito);
  color: white;
}

.paso-nombre {
  font-size: 0.875rem;
  text-align: center;
}

/* Pasos del formulario */
.paso-formulario {
  min-height: 400px;
}

.paso-formulario h2 {
  margin-bottom: 0.5rem;
  color: #2c3e50;
}

.paso-formulario > p {
  color: #7f8c8d;
  margin-bottom: 2rem;
}

/* Campos del formulario */
.campo {
  margin-bottom: 1.5rem;
}

.campo label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: 500;
  color: #495057;
}

.asterisco {
  color: var(--color-error);
  margin-left: 0.25rem;
}

.campo-input-wrapper {
  position: relative;
}

.campo input,
.campo select,
.campo textarea {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid var(--color-borde);
  border-radius: 4px;
  font-size: 1rem;
  transition: border-color 0.2s;
}

.campo input:focus,
.campo select:focus,
.campo textarea:focus {
  outline: none;
  border-color: var(--color-primario);
  box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.1);
}

.campo-error input {
  border-color: var(--color-error);
}

.campo-error input:focus {
  box-shadow: 0 0 0 3px rgba(231, 76, 60, 0.1);
}

.campo-ayuda {
  display: block;
  margin-top: 0.25rem;
  font-size: 0.875rem;
  color: #6c757d;
}

.campo-error-mensaje {
  display: block;
  margin-top: 0.25rem;
  font-size: 0.875rem;
  color: var(--color-error);
}

/* Toggle password */
.toggle-password {
  position: absolute;
  right: 0.75rem;
  top: 50%;
  transform: translateY(-50%);
  background: none;
  border: none;
  cursor: pointer;
  font-size: 1.25rem;
  opacity: 0.6;
  transition: opacity 0.2s;
}

.toggle-password:hover {
  opacity: 1;
}

/* Grids */
.grid-2-columnas {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}

.grid-3-columnas {
  display: grid;
  grid-template-columns: 2fr 1fr 1fr;
  gap: 1rem;
}

/* Radio buttons */
.grupo-radios {
  border: 1px solid var(--color-borde);
  border-radius: 4px;
  padding: 1rem;
  margin-bottom: 1.5rem;
}

.grupo-radios legend {
  font-weight: 500;
  color: #495057;
  padding: 0 0.5rem;
}

.radio-label {
  display: flex;
  align-items: center;
  margin-bottom: 0.5rem;
  cursor: pointer;
}

.radio-label:last-child {
  margin-bottom: 0;
}

.radio-label input {
  margin-right: 0.5rem;
}

/* Checkboxes */
.checkboxes {
  margin-bottom: 1.5rem;
}

.checkbox-label {
  display: flex;
  align-items: flex-start;
  margin-bottom: 1rem;
  cursor: pointer;
}

.checkbox-label input {
  margin-right: 0.5rem;
  margin-top: 0.25rem;
}

.checkbox-label a {
  color: var(--color-primario);
  text-decoration: none;
}

.checkbox-label a:hover {
  text-decoration: underline;
}

/* Indicador de fuerza */
.indicador-fuerza {
  margin-top: -1rem;
  margin-bottom: 1.5rem;
  padding: 1rem;
  background-color: #f8f9fa;
  border-radius: 4px;
}

.barras-fuerza {
  display: flex;
  gap: 4px;
  align-items: flex-end;
  height: 30px;
  margin-bottom: 0.5rem;
}

.barras-fuerza .barra {
  flex: 1;
  border-radius: 2px;
  transition: all 0.3s;
}

.fuerza-info .nivel {
  font-weight: 500;
  font-size: 0.875rem;
}

.sugerencias {
  margin-top: 0.5rem;
  padding-left: 1.25rem;
  font-size: 0.875rem;
  color: #6c757d;
}

/* Resumen */
.resumen .seccion-resumen {
  background-color: #f8f9fa;
  border-radius: 4px;
  padding: 1rem;
  margin-bottom: 1rem;
}

.seccion-resumen h3 {
  margin-bottom: 1rem;
  color: #495057;
  font-size: 1.1rem;
}

.campo-resumen {
  display: flex;
  margin-bottom: 0.5rem;
}

.campo-resumen dt {
  font-weight: 500;
  margin-right: 1rem;
  min-width: 150px;
}

.campo-resumen dd {
  color: #6c757d;
}

.mensaje-confirmacion {
  background-color: #fff3cd;
  border: 1px solid #ffeaa7;
  border-radius: 4px;
  padding: 1rem;
  margin-top: 1.5rem;
}

.mensaje-confirmacion p {
  margin: 0;
  color: #856404;
}

/* Botones */
.botones-navegacion {
  display: flex;
  justify-content: space-between;
  margin-top: 2rem;
  padding-top: 2rem;
  border-top: 1px solid var(--color-borde);
}

.btn-primario,
.btn-secundario {
  padding: 0.75rem 2rem;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s;
}

.btn-primario {
  background-color: var(--color-primario);
  color: white;
}

.btn-primario:hover:not(:disabled) {
  background-color: var(--color-primario-hover);
  transform: translateY(-1px);
  box-shadow: var(--sombra-media);
}

.btn-primario:disabled {
  background-color: #cbd5e0;
  cursor: not-allowed;
  opacity: 0.6;
}

.btn-secundario {
  background-color: white;
  color: var(--color-primario);
  border: 1px solid var(--color-primario);
}

.btn-secundario:hover {
  background-color: #f8f9fa;
}

/* Estados de loading */
@keyframes spin {
  to { transform: rotate(360deg); }
}

.spinner {
  display: inline-block;
  width: 16px;
  height: 16px;
  border: 2px solid #fff;
  border-top-color: transparent;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
  margin-right: 0.5rem;
}

/* Responsive */
@media (max-width: 768px) {
  .app {
    padding: 1rem;
  }
  
  .formulario-registro {
    padding: 1.5rem;
  }
  
  .grid-2-columnas,
  .grid-3-columnas {
    grid-template-columns: 1fr;
  }
  
  .pasos {
    flex-wrap: wrap;
  }
  
  .paso-nombre {
    font-size: 0.75rem;
  }
  
  .campo-resumen {
    flex-direction: column;
  }
  
  .campo-resumen dt {
    margin-bottom: 0.25rem;
  }
}

/* Animaciones */
.campo {
  animation: fadeIn 0.3s ease-out;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Transiciones suaves entre pasos */
.paso-formulario {
  animation: slideIn 0.3s ease-out;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateX(20px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}
```

---

## 🧪 Conceptos aplicados en el ejercicio

### Estados derivados implementados:

1. **Validación en tiempo real**
   - Errores calculados automáticamente basados en valores
   - No se almacenan, se derivan de los valores actuales

2. **Progreso del formulario**
   - Porcentaje completado calculado dinámicamente
   - Estado del paso (válido/inválido) derivado

3. **Información contextual**
   - Edad calculada de la fecha de nacimiento
   - Fuerza de la contraseña derivada de su contenido
   - Disponibilidad del botón basada en validación

### Formateo automático:

1. **Teléfono**: (555) 123-4567
2. **Nombres**: Capitalización automática
3. **Espacios**: Limpieza de espacios extras

### Optimizaciones:

1. **useMemo** para cálculos costosos (validaciones)
2. **useCallback** para funciones estables
3. **Debounce** en verificación de usuario
4. **Lazy evaluation** de errores

### UX mejorada:

1. **Navegación intuitiva** entre pasos
2. **Validación progresiva** (no todo al final)
3. **Feedback visual** inmediato
4. **Mensajes de ayuda** contextuales
5. **Indicadores de progreso** claros

---

## 🎯 Características destacadas

### 1. **Formulario multi-paso**
- Separación lógica de información
- Validación por pasos
- Navegación con estado persistente

### 2. **Hook reutilizable**
- `useFormulario` maneja toda la lógica
- Fácil de extender para otros formularios
- Separación de responsabilidades

### 3. **Validaciones componibles**
- Funciones de validación reutilizables
- Combinación de múltiples validaciones
- Mensajes personalizables

### 4. **Estados derivados eficientes**
- Sin duplicación de estado
- Cálculos solo cuando cambian dependencias
- Fuente única de verdad

### 5. **Accesibilidad**
- Labels asociados correctamente
- ARIA attributes
- Navegación por teclado
- Mensajes de error descriptivos

---

## 📚 Extensiones sugeridas

1. **Guardar progreso**
   ```jsx
   // Guardar en localStorage
   useEffect(() => {
     localStorage.setItem('registro-draft', JSON.stringify(valores))
   }, [valores])
   ```

2. **Validación asíncrona**
   ```jsx
   // Verificar email único
   const validarEmailUnico = async (email) => {
     const response = await fetch(`/api/check-email?email=${email}`)
     const { existe } = await response.json()
     return existe ? 'Este email ya está registrado' : ''
   }
   ```

3. **Subida de avatar**
   ```jsx
   // Agregar campo de foto
   <input
     type="file"
     accept="image/*"
     onChange={handleImageUpload}
   />
   ```

4. **Integración con API real**
   ```jsx
   const enviarRegistro = async (datos) => {
     const response = await fetch('/api/registro', {
       method: 'POST',
       headers: { 'Content-Type': 'application/json' },
       body: JSON.stringify(datos)
     })
     return response.json()
   }
   ```

---

## 🎓 Lecciones aprendidas

1. **Estados derivados** reducen complejidad y bugs
2. **Formateo automático** mejora UX significativamente
3. **Validación progresiva** es menos frustrante
4. **Hooks personalizados** facilitan reutilización
5. **Separación de responsabilidades** hace el código mantenible

¡Con este ejercicio has aplicado conceptos avanzados de formularios y estados derivados en un escenario real y complejo!# 🎯 Ejercicio Práctico: Sistema de Registro con Formularios Avanzados

## 📋 Objetivo del ejercicio

Construir un sistema completo de registro de usuarios que implemente:
- Formulario multi-paso con validación
- Estados derivados para progreso y validación
- Formateo automático de campos
- Prevención de envíos duplicados
- Feedback visual en tiempo real

---

## 📁 Estructura del proyecto

```
registro-avanzado/
├── src/
│   ├── App.jsx
│   ├── components/
│   │   ├── FormularioRegistro.jsx
│   │   ├── PasoInformacionPersonal.jsx
│   │   ├── PasoContacto.jsx
│   │   ├── PasoSeguridad.jsx
│   │   ├── ResumenRegistro.jsx
│   │   └── common/
│   │       ├── Campo.jsx
│   │       ├── IndicadorProgreso.jsx
│   │       └── IndicadorFuerza.jsx
│   ├── hooks/
│   │   ├── useFormulario.js
│   │   └── useValidacion.js
│   └── utils/
│       ├── validaciones.js
│       └── formatters.js
```

---

## 🔧 Implementación del sistema

### 1. Hook personalizado para formularios (useFormulario.js)

```jsx
import { useState, useCallback, useMemo } from 'react'

export function useFormulario(valoresIniciales, validaciones = {}) {
  const [valores, setValores] = useState(valoresIniciales)
  const [tocados, setTocados] = useState({})
  const [enviando, setEnviando] = useState(false)
  
  // Manejar cambio de valores
  const handleChange = useCallback((e) => {
    const { name, value, type, checked, files } = e.target
    
    setValores(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : 
               type === 'file' ? files[0] : 
               value
    }))
  }, [])
  
  // Manejar blur
  const handleBlur = useCallback((e) => {
    const { name } = e.target
    setTocados(prev => ({
      ...prev,
      [name]: true
    }))
  }, [])
  
  // Establecer valor programáticamente
  const setValue = useCallback((name, value) => {
    setValores(prev => ({
      ...prev,
      [name]: value
    }))
  }, [])
  
  // Establecer múltiples valores
  const setValues = useCallback((newValues) => {
    setValores(prev => ({
      ...prev,
      ...newValues
    }))
  }, [])
  
  // Calcular errores (estado derivado)
  const errores = useMemo(() => {
    const erroresCalculados = {}
    
    Object.keys(validaciones).forEach(campo => {
      const validacion = validaciones[campo]
      const valor = valores[campo]
      
      if (typeof validacion === 'function') {
        erroresCalculados[campo] = validacion(valor, valores)
      } else if (Array.isArray(validacion)) {
        // Múltiples validaciones
        for (const fn of validacion) {
          const error = fn(valor, valores)
          if (error) {
            erroresCalculados[campo] = error
            break
          }
        }
      }
    })
    
    return erroresCalculados
  }, [valores, validaciones])
  
  // Estados derivados útiles
  const camposConError = Object.keys(errores).filter(campo => errores[campo])
  const formularioValido = camposConError.length === 0
  const porcentajeCompletado = (
    Object.keys(valores).filter(campo => valores[campo] !== '').length / 
    Object.keys(valores).length * 100
  )
  
  // Resetear formulario
  const reset = useCallback(() => {
    setValores(valoresIniciales)
    setTocados({})
    setEnviando(false)
  }, [valoresIniciales])
  
  // Validar todo
  const validarTodo = useCallback(() => {
    const todosTocados = Object.keys(valores).reduce((acc, campo) => ({
      ...acc,
      [campo]: true
    }), {})
    setTocados(todosTocados)
    return formularioValido
  }, [valores, formularioValido])
  
  return {
    valores,
    errores,
    tocados,
    enviando,
    formularioValido,
    porcentajeCompletado,
    camposConError,
    handleChange,
    handleBlur,
    setValue,
    setValues,
    setEnviando,
    reset,
    validarTodo
  }
}
```

### 2. Utilidades de validación (validaciones.js)

```jsx
// Validaciones reutilizables
export const requerido = (mensaje = 'Este campo es requerido') => 
  (valor) => !valor ? mensaje : ''

export const minLength = (min, mensaje) => 
  (valor) => valor && valor.length < min 
    ? mensaje || `Mínimo ${min} caracteres` 
    : ''

export const maxLength = (max, mensaje) => 
  (valor) => valor && valor.length > max 
    ? mensaje || `Máximo ${max} caracteres` 
    : ''

export const email = (mensaje = 'Email inválido') => 
  (valor) => valor && !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(valor) 
    ? mensaje 
    : ''

export const telefono = (mensaje = 'Teléfono inválido') => 
  (valor) => {
    const soloNumeros = valor.replace(/\D/g, '')
    return soloNumeros.length !== 10 ? mensaje : ''
  }

export const contrasena = (valor) => {
  if (!valor) return 'La contraseña es requerida'
  if (valor.length < 8) return 'Mínimo 8 caracteres'
  if (!/[a-z]/.test(valor)) return 'Debe contener minúsculas'
  if (!/[A-Z]/.test(valor)) return 'Debe contener mayúsculas'
  if (!/\d/.test(valor)) return 'Debe contener números'
  if (!/[!@#$%^&*]/.test(valor)) return 'Debe contener caracteres especiales'
  return ''
}

export const confirmarContrasena = (valor, valores) => {
  if (!valor) return 'Confirma tu contraseña'
  if (valor !== valores.contrasena) return 'Las contraseñas no coinciden'
  return ''
}

export const fechaNacimiento = (valor) => {
  if (!valor) return 'La fecha es requerida'
  
  const fecha = new Date(valor)
  const hoy = new Date()
  const edad = Math.floor((hoy - fecha) / (365.25 * 24 * 60 * 60 * 1000))
  
  if (edad < 18) return 'Debes ser mayor de 18 años'
  if (edad > 120) return 'Fecha inválida'
  
  return ''
}

// Combinar validaciones
export const combinar = (...validaciones) => (valor, valores) => {
  for (const validacion of validaciones) {
    const error = validacion(valor, valores)
    if (error) return error
  }
  return ''
}
```

### 3. Formatters (formatters.js)

```jsx
// Formatear teléfono: (555) 123-4567
export const formatearTelefono = (valor) => {
  const numeros = valor.replace(/\D/g, '')
  
  if (numeros.length <= 3) return numeros
  if (numeros.length <= 6) {
    return `(${numeros.slice(0, 3)}) ${numeros.slice(3)}`
  }
  return `(${numeros.slice(0, 3)}) ${numeros.slice(3, 6)}-${numeros.slice(6, 10)}`
}

// Formatear tarjeta: 1234 5678 9012 3456
export const formatearTarjeta = (valor) => {
  const numeros = valor.replace(/\D/g, '')
  const grupos = numeros.match(/.{1,4}/g) || []
  return grupos.join(' ').substring(0, 19)
}

// Formatear fecha: MM/DD/YYYY
export const formatearFecha = (valor) => {
  const numeros = valor.replace(/\D/g, '')
  
  if (numeros.length <= 2) return numeros
  if (numeros.length <= 4) {
    return `${numeros.slice(0, 2)}/${numeros.slice(2)}`
  }
  return `${numeros.slice(0, 2)}/${numeros.slice(2, 4)}/${numeros.slice(4, 8)}`
}

// Capitalizar nombre
export const capitalizarNombre = (valor) => {
  return valor
    .split(' ')
    .map(palabra => palabra.charAt(0).toUpperCase() + palabra.slice(1).toLowerCase())
    .join(' ')
}

// Limpiar espacios extras
export const limpiarEspacios = (valor) => {
  return valor.replace(/\s+/g, ' ').trim()
}
```

### 4. Componente Campo reutilizable (Campo.jsx)

```jsx
import { useState } from 'react'

function Campo({ 
  label, 
  tipo = 'text',
  nombre,
  valor,
  onChange,
  onBlur,
  error,
  tocado,
  requerido,
  ayuda,
  formatter,
  ...props 
}) {
  const [mostrarPassword, setMostrarPassword] = useState(false)
  
  const handleChange = (e) => {
    let nuevoValor = e.target.value
    
    // Aplicar formatter si existe
    if (formatter) {
      nuevoValor = formatter(nuevoValor)
    }
    
    // Crear evento sintético con el valor formateado
    const eventoSintetico = {
      target: {
        name: nombre,
        value: nuevoValor,
        type: tipo
      }
    }
    
    onChange(eventoSintetico)
  }
  
  const tipoInput = tipo === 'password' && mostrarPassword ? 'text' : tipo
  const mostrarError = error && tocado
  
  return (
    <div className={`campo ${mostrarError ? 'campo-error' : ''}`}>
      <label htmlFor={nombre}>
        {label}
        {requerido && <span className="asterisco">*</span>}
      </label>
      
      <div className="campo-input-wrapper">
        <input
          id={nombre}
          name={nombre}
          type={tipoInput}
          value={valor}
          onChange={handleChange}
          onBlur={onBlur}
          className={mostrarError ? 'input-error' : ''}
          aria-invalid={mostrarError}
          aria-describedby={mostrarError ? `${nombre}-error` : undefined}
          {...props}
        />
        
        {tipo === 'password' && (
          <button
            type="button"
            className="toggle-password"
            onClick={() => setMostrarPassword(!mostrarPassword)}
            aria-label={mostrarPassword ? 'Ocultar contraseña' : 'Mostrar contraseña'}
          >
            {mostrarPassword ? '👁️' : '👁️‍🗨️'}
          </button>
        )}
      </div>
      
      {ayuda && !mostrarError && (
        <span className="campo-ayuda">{ayuda}</span>
      )}
      
      {mostrarError && (
        <span id={`${nombre}-error`} className="campo-error-mensaje">
          {error}
        </span>
      )}
    </div>
  )
}

export default Campo
```

### 5. Formulario multi-paso principal (FormularioRegistro.jsx)

```jsx
import { useState } from 'react'
import { useFormulario } from '../hooks/useFormulario'
import PasoInformacionPersonal from './PasoInformacionPersonal'
import PasoContacto from './PasoContacto'
import PasoSeguridad from './PasoSeguridad'
import ResumenRegistro from './ResumenRegistro'
import IndicadorProgreso from './common/IndicadorProgreso'
import * as validaciones from '../utils/validaciones'

const PASOS = [
  { id: 1, nombre: 'Información Personal', componente: PasoInformacionPersonal },
  { id: 2, nombre: 'Contacto', componente: PasoContacto },
  { id: 3, nombre: 'Seguridad', componente: PasoSeguridad },
  { id: 4, nombre: 'Resumen', componente: ResumenRegistro }
]

function FormularioRegistro() {
  const [pasoActual, setPasoActual] = useState(1)
  const [datosGuardados, setDatosGuardados] = useState({})
  
  const formulario = useFormulario(
    {
      // Información personal
      nombre: '',
      apellidos: '',
      fechaNacimiento: '',
      genero: '',
      
      // Contacto
      email: '',
      telefono: '',
      direccion: '',
      ciudad: '',
      codigoPostal: '',
      pais: '',
      
      // Seguridad
      nombreUsuario: '',
      contrasena: '',
      confirmarContrasena: '',
      preguntaSeguridad: '',
      respuestaSeguridad: '',
      aceptaTerminos: false,
      recibirNotificaciones: false
    },
    {
      // Validaciones por campo
      nombre: [validaciones.requerido(), validaciones.minLength(2)],
      apellidos: [validaciones.requerido(), validaciones.minLength(2)],
      fechaNacimiento: validaciones.fechaNacimiento,
      email: [validaciones.requerido(), validaciones.email()],
      telefono: validaciones.telefono(),
      contrasena: validaciones.contrasena,
      confirmarContrasena: validaciones.confirmarContrasena,
      nombreUsuario: [
        validaciones.requerido(),
        validaciones.minLength(4),
        validaciones.maxLength(20)
      ],
      aceptaTerminos: (valor) => !valor ? 'Debes aceptar los términos' : ''
    }
  )
  
  // Estados derivados para validación por pasos
  const validacionPorPasos = {
    1: ['nombre', 'apellidos', 'fechaNacimiento', 'genero'],
    2: ['email', 'telefono', 'direccion', 'ciudad', 'codigoPostal', 'pais'],
    3: ['nombreUsuario', 'contrasena', 'confirmarContrasena', 'aceptaTerminos']
  }
  
  // Estado derivado: ¿El paso actual es válido?
  const pasoValido = validacionPorPasos[pasoActual]?.every(
    campo => !formulario.errores[campo] && formulario.valores[campo] !== ''
  ) ?? true
  
  // Navegación entre pasos
  const siguientePaso = () => {
    if (pasoActual < PASOS.length) {
      // Guardar datos del paso actual
      const camposPaso = validacionPorPasos[pasoActual] || []
      const datosPaso = camposPaso.reduce((acc, campo) => ({
        ...acc,
        [campo]: formulario.valores[campo]
      }), {})
      
      setDatosGuardados(prev => ({
        ...prev,
        ...datosPaso
      }))
      
      setPasoActual(pasoActual + 1)
    }
  }
  
  const pasoAnterior = () => {
    if (pasoActual > 1) {
      setPasoActual(pasoActual - 1)
    }
  }
  
  // Enviar formulario
  const handleSubmit = async (e) => {
    e.preventDefault()
    
    if (!formulario.validarTodo()) {
      alert('Por favor corrige los errores antes de continuar')
      return
    }
    
    formulario.setEnviando(true)
    
    try {
      // Simular envío a API
      await new Promise(resolve => setTimeout(resolve, 2000))
      
      console.log('Datos enviados:', {
        ...datosGuardados,
        ...formulario.valores
      })
      
      alert('¡Registro exitoso!')
      formulario.reset()
      setPasoActual(1)
      setDatosGuardados({})
      
    } catch (error) {
      alert('Error al registrar. Intenta nuevamente.')
    } finally {
      formulario.setEnviando(false)
    }
  }
  
  const PasoComponente = PASOS[pasoActual - 1].componente
  
  return (
    <div className="formulario-registro">
      <h1>Crear cuenta</h1>
      
      <IndicadorProgreso 
        pasos={PASOS}
        pasoActual={pasoActual}
        completados={Object.keys(datosGuardados)}
      />
      
      <form onSubmit={handleSubmit}>
        <PasoComponente 
          formulario={formulario}
          datosGuardados={datosGuardados}
        />
        
        <div className="botones-navegacion">
          {pasoActual > 1 && (
            <button
              type="button"
              onClick={pasoAnterior}
              className="btn-secundario"
            >
              Anterior
            </button>
          )}
          
          {pasoActual < PASOS.length ? (
            <button
              type="button"
              onClick={siguientePaso}
              disabled={!pasoValido}
              className="btn-primario"
            >
              Siguiente
            </button>
          ) : (
            <button
              type="submit"
              disabled={!formulario.formularioValido || formulario.enviando}
              className="btn-primario"
            >
              {formulario.enviando ? 'Registrando...' : 'Completar registro'}
            </button>
          )}
        </div>
      </form>
      
      {/* Debug info en desarrollo */}
      {process.env.NODE_ENV === 'development' && (
        <details style={{ marginTop: '2rem' }}>
          <summary>Debug Info</summary>
          <pre>{JSON.stringify({
            pasoActual,
            pasoValido,
            errores: formulario.errores,
            valores: formulario.valores,
            porcentajeCompletado: formulario.porcentajeCompletado
          }, null, 2)}</pre>
        </details>
      )}
    </div>
  )
}

export default FormularioRegistro

```