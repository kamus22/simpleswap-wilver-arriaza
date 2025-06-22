# SimpleSwap Project

Este proyecto implementa un intercambiador automatizado de mercado (AMM) simple que replica las funcionalidades básicas de Uniswap V2.

## Descripción

SimpleSwap permite:
- ✅ Agregar liquidez a pools de tokens
- ✅ Remover liquidez de pools de tokens  
- ✅ Intercambiar tokens usando la fórmula de producto constante
- ✅ Obtener precios de tokens en tiempo real
- ✅ Calcular cantidades de salida para intercambios

## Estructura del Proyecto

```
tp-modulo3/
├── TokenA.sol          # Token ERC20 de prueba
├── TokenB.sol          # Token ERC20 de prueba
├── SimpleSwap.sol      # Contrato principal del AMM
├── Verifier.sol        # Contrato de verificación (proporcionado)
└── README.md          # Este archivo
```

## Contratos

### TokenA y TokenB
- Tokens ERC20 estándar con funcionalidad de mint
- Suministro inicial configurable
- Funciones: transfer, approve, transferFrom, mint

### SimpleSwap
- Implementa interfaz ISimpleSwap requerida por Verifier.sol
- Gestiona pools de liquidez para pares de tokens
- Utiliza fórmula de producto constante (x * y = k)
- Sin fees para compatibilidad con verificador

## Funciones Principales

### `addLiquidity()`
Agrega liquidez a un pool de tokens y emite tokens de liquidez proporcionales.

### `removeLiquidity()`
Remueve liquidez de un pool y retorna los tokens correspondientes.

### `swapExactTokensForTokens()`
Intercambia una cantidad exacta de tokens de entrada por tokens de salida.

### `getPrice()`
Retorna el precio de tokenA en términos de tokenB (escalado por 1e18).

### `getAmountOut()`
Calcula la cantidad de tokens de salida para una entrada dada.

## Instrucciones de Despliegue en Remix IDE

### Paso 1: Configuración Inicial

1. **Abrir Remix IDE**
   - Ir a [https://remix.ethereum.org](https://remix.ethereum.org)
   - Crear un nuevo workspace o usar el default

2. **Crear los archivos del proyecto**
   - Crear una nueva carpeta llamada `SimpleSwap`
   - Subir o copiar los siguientes archivos:
     - `TokenA.sol`
     - `TokenB.sol` 
     - `SimpleSwap.sol`
     - `Verifier.sol`

### Paso 2: Configuración del Compilador

1. **Ir a la pestaña "Solidity Compiler"**
2. **Configurar la versión del compilador:**
   - Seleccionar versión: `0.8.19` o superior
   - Optimización: Activada (200 runs)
3. **Compilar todos los contratos:**
   - Compilar `TokenA.sol` ✅
   - Compilar `TokenB.sol` ✅
   - Compilar `SimpleSwap.sol` ✅
   - Compilar `Verifier.sol` ✅

### Paso 3: Despliegue de Tokens

1. **Ir a la pestaña "Deploy & Run Transactions"**
2. **Configurar el entorno:**
   - Environment: `Remix VM (Cancun)` o `Injected Provider - MetaMask`
   - Account: Seleccionar la cuenta principal
   - Gas Limit: `3000000`

3. **Desplegar TokenA:**
   - Seleccionar contrato: `TokenA`
   - Constructor parameter: `1000000` (1 millón de tokens)
   - Click "Deploy"
   - **Copiar la dirección del contrato TokenA**

4. **Desplegar TokenB:**
   - Seleccionar contrato: `TokenB` 
   - Constructor parameter: `1000000` (1 millón de tokens)
   - Click "Deploy"
   - **Copiar la dirección del contrato TokenB**

### Paso 4: Despliegue de SimpleSwap

1. **Desplegar SimpleSwap:**
   - Seleccionar contrato: `SimpleSwap`
   - No requiere parámetros de constructor
   - Click "Deploy"
   - **Copiar la dirección del contrato SimpleSwap**

### Paso 5: Despliegue del Verifier

1. **Desplegar Verifier:**
   - Seleccionar contrato: `SwapVerifier`
   - No requiere parámetros de constructor
   - Click "Deploy"
   - **Copiar la dirección del contrato Verifier**

### Paso 6: Preparación para Verificación

1. **Transferir tokens al Verifier:**
   
   **Para TokenA:**
   - En la sección "Deployed Contracts", expandir TokenA
   - Llamar función `transfer`:
     - `_to`: [DIRECCIÓN_DEL_VERIFIER]
     - `_value`: `500000000000000000000000` (500,000 tokens con 18 decimales)
   - Click "transact"

   **Para TokenB:**
   - En la sección "Deployed Contracts", expandir TokenB  
   - Llamar función `transfer`:
     - `_to`: [DIRECCIÓN_DEL_VERIFIER]
     - `_value`: `500000000000000000000000` (500,000 tokens con 18 decimales)
   - Click "transact"

### Paso 7: Ejecutar Verificación

1. **Ejecutar el verificador:**
   - En la sección "Deployed Contracts", expandir SwapVerifier
   - Llamar función `verify` con los siguientes parámetros:
     - `swapContract`: [DIRECCIÓN_DEL_SIMPLESWAP]
     - `tokenA`: [DIRECCIÓN_DEL_TOKENA]
     - `tokenB`: [DIRECCIÓN_DEL_TOKENB]
     - `amountA`: `1000000000000000000000` (1,000 tokens)
     - `amountB`: `2000000000000000000000` (2,000 tokens)
     - `amountIn`: `100000000000000000000` (100 tokens)
     - `author`: `"Tu Nombre Aquí"`
   - Click "transact"

### Paso 8: Pruebas Adicionales (Opcional)

**Agregar Liquidez:**
```javascript
// En SimpleSwap contract
addLiquidity(
  tokenA_address,
  tokenB_address, 
  "1000000000000000000000", // 1000 tokenA
  "2000000000000000000000", // 2000 tokenB
  "950000000000000000000",  // min 950 tokenA
  "1900000000000000000000", // min 1900 tokenB
  your_address,
  Math.floor(Date.now()/1000) + 300 // deadline 5 min
)
```

## Solución de Problemas

**Error: "Insufficient balance"**
- Verificar que el Verifier tenga suficientes tokens
- Llamar `mint()` en los tokens si es necesario

**Error: "EXPIRED"**
- Usar un deadline futuro (timestamp actual + 300 segundos)

**Error: "INSUFFICIENT_LIQUIDITY"**
- Asegurar que el pool tenga liquidez antes de hacer swaps

---

**Author: Wilver Arriaza**
