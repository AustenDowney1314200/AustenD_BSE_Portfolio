# Unity 3D Fighting Game
I am making a 3D fighting game in Unity.

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Austen D | Browning | Electrical/Software Engineering | 9th 

![Headstone Image]

# Presentation
<iframe width="560" height="315" src="https://www.youtube.com/embed/V6Q-Igty3Uk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

# Second Milestone
My second milestone mostly consisted of coding. I ran into many errors but none of them lasted too long. The first thing that I did in the second milestone was make movment. I made multible diffrent movment scripts but i kept on needing to delete them because they didnt do everything that I wanted them to. Once I finaly got the right code I needed to start on combining my animations to my movment. I would say that combining my animations to my movment was the most difficult part of my project. I needed to make an intirely diffrent movment code just so the the animations would work with the movment. Whenever I tried to combine the animation code with my movement code i previosly had, an error message would pop up. After I got the movment and animation code working together I dicided to make my terrain look more presentable.

[![Second Milestone]

# First Milestone
For my first milestone I made a character for my game in Blender. Blender is a free software that makes 3d modles and animations. I created a swordsman that consisted of multible edited cubes. I then put an exoskeleton in my character to be able to create animations for my character. To animate my caracter I needed to create keyframes. Keyframes are basicaly specific frames that show your character posing in diffrent positions. After I created my character and the animations for my character, I exported it in an fbx file to unity. Then it was time to move to my second mile stone.

<iframe width="560" height="315" src="https://www.youtube.com/embed/yAgt9f4GZ9s" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

# Reflection
I really enjoyed game dev

# Character
![ezgif com-gif-maker](https://user-images.githubusercontent.com/88197067/128192517-3516dcad-cafe-4bc8-8efd-9650616c9dc8.gif)
![ezgif com-gif-maker (1)](https://user-images.githubusercontent.com/88197067/128192937-34e16dd2-4422-406c-98dc-78498e1d7a5f.gif)

# Animation Code
```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
//using UnityEngine.InputSystem;

public class Move : MonoBehaviour
{
    PlayerInput playerInput;
    CharacterController characterController;
    Animator animator;
    public Transform cam;
    Vector2 currentMovementInput;
    Vector3 currentMovement;
    bool isMovementPressed;
    float rotationFactorPerFrame = 5.0f;
    public float turnSmoothTime = 0.1f;
    float turnSmoothVelocity;
    public float speed = 20f;
    public CharacterController controller;
    void Awake()
    {

        playerInput = new PlayerInput();
        characterController = GetComponent<CharacterController>();
        animator = GetComponent<Animator>();

        playerInput.CharacterControls.Move.started += context => {
            currentMovementInput = context.ReadValue<Vector2>();
            currentMovement.x = currentMovementInput.x;
            currentMovement.z = currentMovementInput.y;
            isMovementPressed = currentMovementInput.x != 0 || currentMovementInput.y != 0;
        };
        playerInput.CharacterControls.Move.canceled += context => {
            currentMovementInput = context.ReadValue<Vector2>();
            currentMovement.x = currentMovementInput.x;
            currentMovement.z = currentMovementInput.y;
            isMovementPressed = currentMovementInput.x != 0 || currentMovementInput.y != 0;
        };
        playerInput.CharacterControls.Move.performed += context => {
            currentMovementInput = context.ReadValue<Vector2>();
            currentMovement.x = currentMovementInput.x;
            currentMovement.z = currentMovementInput.y;
            isMovementPressed = currentMovementInput.x != 0 || currentMovementInput.y != 0;
        };
    }

    void handleRotation()
    {
        Vector3 positionToLookAt;

        positionToLookAt.x = -currentMovement.x;
        positionToLookAt.y = 0.0f;
        positionToLookAt.z = -currentMovement.z;

        Quaternion currentRotation = transform.rotation;

        if (isMovementPressed){

            Quaternion targetRotaion = Quaternion.LookRotation(positionToLookAt);
            transform.rotation = Quaternion.Slerp(currentRotation, targetRotaion, rotationFactorPerFrame * Time.deltaTime + cam.eulerAngles.y);
        }
    }

    // void onMovmentInput (InputAction.CallbackContext context)
    // {
    //   currentMovementInput = context.ReadValue<Vector2>();
    //   currentMovement.x = currentMovementInput.x;
    //  currentMovement.z = currentMovementInput.y;
    //  isMovementPressed = currentMovementInput.x != 0 || currentMovementInput.y != 0;
    // }

    void handleAnimation()
    {
        // bool isWalking = animator.GetBool("isWalking");
        bool isRunning = animator.GetBool("isRunning");

        if (isMovementPressed && !isRunning)
        {
            animator.SetBool("isRunning", true);
        }

        else if (!isMovementPressed && isRunning)
        {
            animator.SetBool("isRunning", false);
        }
    }

    // Update is called once per frame
    void Update()
    {
        handleRotation();
        handleAnimation();
        characterController.Move(currentMovement * Time.deltaTime * speed);
        float horizontal = Input.GetAxisRaw("Horizontal");
        float vertical = Input.GetAxisRaw("Vertical");
        Vector3 direction = new Vector3(horizontal, 0f, vertical).normalized;

        if (direction.magnitude >= 0.1f)
        {
            float targetAngle = Mathf.Atan2(-direction.x, -direction.z) * Mathf.Rad2Deg + cam.eulerAngles.y;
            float angle = Mathf.SmoothDampAngle(transform.eulerAngles.y, targetAngle, ref turnSmoothVelocity, turnSmoothTime);
            transform.rotation = Quaternion.Euler(0f, angle, 0f);

            Vector3 moveDiri = Quaternion.Euler(0f, targetAngle, 0f) * Vector3.forward;
            controller.Move(-moveDiri.normalized * speed * Time.deltaTime);

        }
    }

    void OnEnable()
    {
        playerInput.CharacterControls.Enable();
    }

    void OnDisable()
    {
        playerInput.CharacterControls.Disable();
    }
}
```

# Movment Code
```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Movement2 : MonoBehaviour
{
    public CharacterController controller;
    public Transform cam;
    Animator animator;
    public float speed = 12f;
    bool isMovementPressed;
    PlayerInput playerInput;
    Vector2 currentMovementInput;
    Vector3 currentMovement;


    public float turnSmoothTime = 0.1f;
    float turnSmoothVelocity;
    // Update is called once per frame
    void Update()
    {
        float horizontal = Input.GetAxisRaw("Horizontal");
        float vertical = Input.GetAxisRaw("Vertical");
        Vector3 direction = new Vector3(horizontal, 0f, vertical).normalized;
        animator = GetComponent<Animator>();


        if (direction.magnitude >= 0.1f)
        {
            float targetAngle = Mathf.Atan2(-direction.x, -direction.z) * Mathf.Rad2Deg + cam.eulerAngles.y;
            float angle = Mathf.SmoothDampAngle(transform.eulerAngles.y, targetAngle, ref turnSmoothVelocity, turnSmoothTime);
            transform.rotation = Quaternion.Euler(0f, angle, 0f);

            Vector3 moveDir = Quaternion.Euler(0f, targetAngle, 0f) * Vector3.forward;
            controller.Move(-moveDir.normalized * speed * Time.deltaTime);

        }
    }

    void handleAnimation()
    {
        // bool isWalking = animator.GetBool("isWalking");
        bool isRunning = animator.GetBool("isRunning");

        if (isMovementPressed && !isRunning)
        {
            animator.SetBool("isRunning", true);
        }

        else if (!isMovementPressed && isRunning)
        {
            animator.SetBool("isRunning", false);
        }
    }
}
```
