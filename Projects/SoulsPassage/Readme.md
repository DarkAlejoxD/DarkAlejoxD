# Soul's Passage Process

|       Prototype       |         Alpha        |        Beta          | Gold |
|-----------------------|----------------------|----------------------|------|
| ![Prototype](Images/SoulsPassageProto.png) | ![Alpha](Images/SoulsPassageAlpha.png) | ![Beta](Images/SoulsPassageBeta.png) | ![Gold](Images/SoulsPassageGold.png) |

# Index

## 1. Introduction
This project was developed as a university project by a team of 5 people in a period of time of 3 month.

Play as Chiara, a little girl trapped in a magical train. Your mission is to escape the train with the skills she has learned. 

- **Repository:** [Souls Passage Repository](https://github.com/DarkAlejoxD/SoulsPassage_Code/tree/main/Assets/Scripts)

- **Genre:**
  - 3D platformer game developed in Unity.  
- **Contribution:**  
  - Developed and tested the player controller using an FSM to handle player states.  
  - Conducted functional, negative, and exploratory tests for player abilities.  
  - Created a tool to save and teleport to specific map locations for optimized level testing.  
- **Insights:**  
  - Integrated FMOD for sound design.  
  - Explored Cinemachine for improved camera composition.

## 2. Team
- Laura Tuxans - Producer | Artist
- Grazielly Sanchez - Designer | Artist
- Paola Pica - Designer | Artist
- Gerard Martínez - Designer
- Camilo Londoño (Me) - Programmer

## 3. Systems and Features

### Character Controller with FSM
#### Description
I developed a Character Controller using a State Pattern to manage which actions can be triggered at any given time. Prior to implementing this system, I relied on a series of if-statements. For every new action added to the game, I had to create additional if-statements to determine which actions were allowed in each scenario, which quickly became complex.

Although this State Pattern-based system is relatively simple, refactoring the entire Character Controller to use it significantly streamlined testing and made it easier to modify values. For instance, during production, if designers wanted to enable an action in a state that previously did not allow it, or vice versa, this system facilitated those changes with minimal effort.

#### Technical Details

In this system, there are various components acting:

| Image | Description |
|-|-|
| ![Character Scripts](Images/CharacterScripts.png) | **Player Scripts:** <br><br>All the scripts used for the CharacterController are designed to encapsulate each feature into separate files. |
| ![Character Actions](Images/CharacterActions.png) | **Player Actions:** <br><br>All actions are controlled by the observer pattern. If the corresponding component is not subscribed, it will not interrupt the execution flow. <br>An example of this is the PlayerJump.cs component, which has access to PlayerController.OnJump and subscribes to it to handle jump-related events. |
| ![PlayerFSM](Images/CharacterFSMStates.png) | **Player FSM & PlayerStates:** <br><br>The FSM takes advantage of a system I previously developed "_(GitHub Page in Progress)_", with a slight modification to its constructor to accept inputs through a class named _InputValues.cs_, which contains all actions performed during the current frame.|
| ![PlayerFSMInit](Images/CharacterFSMInit.png) | **Player FSM Initialization:** <br><br>FSM initialization. |



Every PlayerState contains information relevant to what is going on in the player and the information of the animations relative to this state. For example: 
##### PlayerState_DefaultMovement
```csharp
using InputController;
using UnityEngine;

namespace AvatarController.PlayerFSM
{
    /// <summary>
    /// FreeMove, the regular state of the player
    /// </summary>
    public class PlayerState_DefaultMovement : PlayerState
    {
        private const string MOVEMENT_VALUE = "Speed";
        private const string ONGROUND_ANIM = "OnGround";
        private const string AFK_ON_TRIGGER = "AFKon";
        private const string AFK_OFF_TRIGGER = "AFKoff";
        private const float SMOOTH = 0.3f;

        private float _animControl = 0;

        private float _timeToIdle = 0;
        private float _timeControl = 0;
        private float _jumpTimeControl = 0;

        private bool _poltergeistActivated;
        private bool _isAFK;
        private bool _canJump;

        public override string Name => "Default Movement";
        private bool IsAFK {...}
        private bool IsTrigger {...}

        public PlayerState_DefaultMovement(PlayerController playerController) : base(playerController)
        {
            _poltergeistActivated = false;
        }

        public override void OnEnter()
        {
            _playerController.UnBlockMovement();
            //If necessary change the playerMovementData
            _poltergeistActivated = false;

            if (Anim)
                Anim.SetBool(ONGROUND_ANIM, true);

            _isAFK = false;
            _timeControl = 0;
            _timeToIdle = Data.DefOtherValues.TimeBreakIdle;
            _jumpTimeControl = Time.time;
        }

        public override void OnPlayerStay(InputValues inputs)
        {
            if (_timeControl > _timeToIdle)
            {
                _timeControl = 0;
                _timeToIdle = Data.DefOtherValues.TimeBreakIdle;
                if (Anim)
                    Anim.SetTrigger(AFK_ON_TRIGGER);
            }

            //if (_playerController.Velocity.magnitude > Data.DefaultMovement.MinSpeedToMove)
            if (inputs.MoveInput.magnitude > 0)
            {
                _timeControl = 0;
                if (Anim && IsAFK)
                    Anim.SetTrigger(AFK_OFF_TRIGGER);
                if (!IsAFK && Anim)
                    Anim.ResetTrigger(AFK_OFF_TRIGGER);
            }
            _timeControl += Time.deltaTime;

            //Debug.Log($"BreakTime: {_timeControl}");

            //Anim Logic
            if (Anim)
            {
                // 0----min(x)----max(y)
                float speed = _playerController.Velocity.magnitude;
                float minSpeed = Data.DefaultMovement.MinSpeedToMove;
                float maxSpeed = Data.DefaultMovement.MaxSpeed;
                float value;

                //min(0) ---- max(y-x)
                maxSpeed -= minSpeed;
                speed -= minSpeed;
                value = speed / maxSpeed;
                value = Mathf.Clamp01(value);
                _animControl = Mathf.Lerp(_animControl, value, SMOOTH);

                Anim.SetFloat(MOVEMENT_VALUE, _animControl);
            }

            //Inputs Logic
            _playerController.OnMovement?.Invoke(inputs.MoveInput);
            if (Time.time > _jumpTimeControl + Data.DefaultJumpValues.JumpCD)
                _playerController.OnJump?.Invoke(inputs.JumpInput);

            //_playerController.OnDive?.Invoke(inputs.CrounchDiveInput);
            //_playerController.OnInteract?.Invoke(inputs.InteractInput);

            if (Data.Powers.HasGhostView)
                _playerController.OnGhostView?.Invoke(inputs.GhostViewInput);

            if (!Data.Powers.HasPoltergeist)
                return;

            if (inputs.Poltergeist && !_poltergeistActivated &&
                _playerController._canActivatePoltergeist)
            {
                _poltergeistActivated = true;
                _playerController.RequestChangeState(PlayerStates.OnPoltergeist);
            }
        }

        public override void OnExit()
        {
            base.OnExit();

            if (Anim)
            {
                Anim.SetBool(ONGROUND_ANIM, false);
                Anim.ResetTrigger(AFK_ON_TRIGGER);
                Anim.ResetTrigger(AFK_OFF_TRIGGER);
            }
            _isAFK = false;
        }
    }
}
```

### LedgeGrab
Describe cómo funciona el sistema de **LedgeGrab**:  
- ¿Qué es? (Por ejemplo: Permite al personaje agarrarse a bordes y moverse con fluidez).
- Cómo se implementó. (Breve explicación del uso de la FSM y los estados involucrados).
- Retos y soluciones. (Si hubo problemas, describe cómo los resolviste).

### Character Abilities
#### Ghost View
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
