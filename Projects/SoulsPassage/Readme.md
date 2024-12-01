# Soul's Passage Process

|       Prototype       |         Alpha        |        Beta          | Gold |
|-----------------------|----------------------|----------------------|------|
| ![Prototype](Images/SoulsPassageProto.png) | ![Alpha](Images/SoulsPassageAlpha.png) | ![Beta](Images/SoulsPassageBeta.png) | ![Gold](Images/SoulsPassageGold.png) |

# Index

## Introduction
This 

## Team

| Laura Tuxans | Producer / Artist |
|-|-|
| Grazielly San | Designer / Artist |
|-|-|
| Paola Pica | Designer / Artist |
|-|-|

## Systems and Features

### Character Controller with FSM
- **Descripción:** 
- **Detalles técnicos:** Breve descripción del enfoque usado (por ejemplo: Raycasts o cambio de materiales).
- **Uso:** Dónde se aplica esta habilidad en el juego.

#### LedgeGrab
Describe cómo funciona el sistema de **LedgeGrab**:  
- ¿Qué es? (Por ejemplo: Permite al personaje agarrarse a bordes y moverse con fluidez).
- Cómo se implementó. (Breve explicación del uso de la FSM y los estados involucrados).
- Retos y soluciones. (Si hubo problemas, describe cómo los resolviste).

#### Character Abilities
##### Ghost View
- **Descripción:** Explica en qué consiste esta habilidad (por ejemplo: "Permite al jugador ver a través de objetos para planificar su siguiente movimiento").
- **Detalles técnicos:** Breve descripción del enfoque usado (por ejemplo: Raycasts o cambio de materiales).
- **Uso:** Dónde se aplica esta habilidad en el juego.

##### Poltergeist
- **Descripción:** Define la habilidad (por ejemplo: "Permite mover objetos a distancia").
- **Detalles técnicos:** Describe la lógica detrás (como fuerzas físicas o interacciones específicas con colliders).
- **Casos de uso:** Ejemplos de cómo mejora la jugabilidad.

---

### Camera Controller
- **Propósito:** Explica el objetivo del sistema (por ejemplo: "Proveer una cámara fluida que siga al jugador y se adapte a diferentes escenarios").  
- **Características principales:**  
  - Adaptación a cambios de estado del jugador.  
  - Uso de Cinemachine o lógica personalizada.
- **Retos superados:** Describe cualquier desafío técnico y cómo lo solucionaste.

---

### Event System / Mechanism System
- **Propósito:** Breve introducción (por ejemplo: "Gestión de eventos interactivos como puertas, trampas o puzzles").  
- **Arquitectura:** Cómo está estructurado el sistema (por ejemplo, uso de un patrón Observer).  
- **Ejemplo:** Describe un caso práctico (como abrir una puerta al activar un interruptor).  

---

### Shaders
#### Transparency Shader
- **Descripción:** Qué hace el shader (por ejemplo: "Permite que ciertos objetos se vuelvan parcialmente transparentes al bloquear la vista del jugador").  
- **Detalles técnicos:** Breve explicación del enfoque (como máscaras de profundidad o cambio de alfa).  

#### Poltergeist Sphere Shader
- **Descripción:** Define el propósito (por ejemplo: "Crea un efecto visual cuando un objeto es manipulado con la habilidad Poltergeist").  
- **Detalles técnicos:** Incluye una breve descripción de los nodos o funciones principales usadas en el shader.  

---

## Conclusion
- Resume brevemente cómo estas características juntas hacen que el juego sea único.  
- Menciona cualquier aprendizaje o mejora futura que te gustaría implementar.
