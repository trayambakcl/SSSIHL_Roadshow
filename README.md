
# SSIHL ROADSHOW
***Repository on RISC-V and VLSI Chip Design Roadshow at SSSIHL***
***

# OVERVIEW OF THE WORKSHOP

***The one-day workshop provided a comprehensive introduction to VLSI and RISC-V, covering the chip design process and various microprocessor types. It enhanced our understanding of semiconductor technologies and opened new career opportunities in chip design and microelectronics.***
# PROCEDURE

 ***The workshop introduced us to VSD (VLSI System Design) Quadron and VirtualBox. We explored how VSD Quadron supports chip design and simulation***
 ***
# Installed gedit successfully, made a file vsd.c and ran a code to add numbers from 1 to 10 




![image (2)](https://github.com/user-attachments/assets/ded3e87f-8b6a-4657-848e-656e02e00a1d)



   
    ```sudo apt install gedit``` ***WAS THE CODE USED***
```
#include<stdio.h>
  int main(){
    int sum = 0, i, n;
    printf("Enter the value of n = ");
    scanf("%d",&n);
    for(i = 1;i <= n;i++){
       sum = sum + i;
    }
    printf("The Sum of numbers from 1 to %d is %d\n",n,sum);
    return 0;
}
```
***
# Compiling the C program for RISC-V architecture using riscv64-unknown-elf-gcc
    
    
    
  
  ```riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o {filename}.o {filename}.c` and iscv64-unknown-elf-objdump -d {filename}.o ```
   
 ***was used and following result was obtained***
![image (3)](https://github.com/user-attachments/assets/88f42ee6-3983-4c16-952b-a191b25c64da)

***
# Now Openlane, Docker and ./flow.tcl -interactive was used
  
  (```openlane,docker,./flow.tcl -interactive```)
  
<img width="737" alt="i6" src="https://github.com/user-attachments/assets/fcea22ec-815d-424b-9a11-6df9a005b363" />

***
# RAN PACKAGES

<img width="526" alt="i11 openlane" src="https://github.com/user-attachments/assets/22794576-67ef-42c3-bf1e-8d64a0680adb" />



(Ran a synthesis"run_synthesis" and was successful)


<img width="548" alt="i12 synthesis succesful" src="https://github.com/user-attachments/assets/3ee34a2e-6fa8-4379-9dca-1a8d8648a9ed" />

   

***
# Ran a code run_floorplane 
```picorv32a.floorplan.def.png ```

<img width="561" alt="i13 floorplan" src="https://github.com/user-attachments/assets/02a430fc-b4d6-4137-bb22-46f3b21d7888" />

<img width="581" alt="i16 floorplanimage" src="https://github.com/user-attachments/assets/44b55546-3b46-4422-8c0b-831e74c32341" />

***
# Ran code "run_placement" and got png


***![i17 placement](https://github.com/user-attachments/assets/5d040d00-31a6-4870-a10c-f85007dd043d)

<img width="553" alt="i20 placementimage" src="https://github.com/user-attachments/assets/4107d160-c4a0-4011-bc97-ce7fb46d9b86" />


# Ran_cts
<img width="635" alt="i21 clk" src="https://github.com/user-attachments/assets/14b592de-d483-4686-ac4d-52e687f7ada3" />

***

# Finished routing with its image


<img width="647" alt="i22 run routing" src="https://github.com/user-attachments/assets/d9e9d1c9-932a-4bcd-898d-8df672d7e1f5" />
<img width="671" alt="i24 routingimage" src="https://github.com/user-attachments/assets/96353bab-aecf-4979-a104-245b9cf0c2ef" />

***
``` NOW WE PROGRAMMED THE BOARD ```
# BLINK  VIDEO


https://github.com/user-attachments/assets/cbc69537-5723-40bd-abf3-b93868268787



# FADE VIDEO




https://github.com/user-attachments/assets/9543f3b3-2cf9-4e15-94b3-43106be175e6

# BLINK CODE

``` #include <ch32v00x.h>
#include <debug.h>

#define BLINKY_GPIO_PORT GPIOD
#define BLINKY_GPIO_PIN GPIO_Pin_6
#define BLINKY_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)

void NMI_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void HardFault_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void Delay_Init(void);
void Delay_Ms(uint32_t n);

int main(void)
{
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);
	SystemCoreClockUpdate();
	Delay_Init();

	GPIO_InitTypeDef GPIO_InitStructure = {0};

	BLINKY_CLOCK_ENABLE;
	GPIO_InitStructure.GPIO_Pin = BLINKY_GPIO_PIN;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(BLINKY_GPIO_PORT, &GPIO_InitStructure);

	uint8_t ledState = 0;
	while (1)
	{
		GPIO_WriteBit(BLINKY_GPIO_PORT, BLINKY_GPIO_PIN, ledState);
		ledState ^= 1; // invert for the next run
		Delay_Ms(100);
	}
}

void NMI_Handler(void) {}
void HardFault_Handler(void)
{
	while (1)
	{
	}
}
 ```
AND 

``` #include "debug.h"
#define TIME_PERIOD 1000
#define PRESC       0
#define PULSE       632
#define STEP_SIZE   10
volatile u16 val;
volatile u8 dir;
void TIM1_PWMOut_Init(void){
    GPIO_InitTypeDef GPIO_InitStructure={0};
    TIM_OCInitTypeDef TIM_OCInitStructure={0};
    TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure={0};
    //RCC_APB2PeriphClockCmd( RCC_APB2Periph_GPIOC | RCC_APB2Periph_TIM1, ENABLE );
    RCC_APB2PeriphClockCmd( RCC_APB2Periph_GPIOD | RCC_APB2Periph_AFIO, ENABLE );
    RCC_APB1PeriphClockCmd( RCC_APB1Periph_TIM2, ENABLE );
    GPIO_PinRemapConfig(GPIO_FullRemap_TIM2, ENABLE);
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init( GPIOD, &GPIO_InitStructure);
    TIM_TimeBaseInitStructure.TIM_Period = TIME_PERIOD;
    TIM_TimeBaseInitStructure.TIM_Prescaler = PRESC;
    TIM_TimeBaseInitStructure.TIM_ClockDivision = TIM_CKD_DIV1;
    TIM_TimeBaseInitStructure.TIM_CounterMode = TIM_CounterMode_Up;
    TIM_TimeBaseInit( TIM2, &TIM_TimeBaseInitStructure);
    TIM_OCInitStructure.TIM_OCMode = TIM_OCMode_PWM2;
    TIM_OCInitStructure.TIM_OutputState = TIM_OutputState_Enable;
    TIM_OCInitStructure.TIM_Pulse = PULSE;
    TIM_OCInitStructure.TIM_OCPolarity = TIM_OCPolarity_High;
    TIM_OC3Init( TIM2, &TIM_OCInitStructure );
    TIM_CtrlPWMOutputs(TIM2, ENABLE );
    //TIM_OC3PreloadConfig( TIM1, TIM_OCPreload_Disable );
    //TIM_ARRPreloadConfig( TIM1, ENABLE );
    TIM_Cmd( TIM2, ENABLE );
}
int main(void)
{
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);
    SystemCoreClockUpdate();
    Delay_Init();
    TIM1_PWMOut_Init();
    val = 0;
    dir = 0;
    // Loop
    while(1){
        val += (dir) ? -STEP_SIZE : STEP_SIZE;
        TIM_SetCompare3(TIM2, val);
        dir ^= (val == 1000 || val == 0) ? 1 : 0;
        Delay_Ms(15);
    }
}
```
***


___

**I would like to express my sincere gratitude for the insightful workshop on VLSI and RISC-V,
which significantly deepened my understanding of chip design and related technologies. The session on VSD Quadron and VirtualBox was particularly valuable,
and it has further fueled my interest in pursuing a career in VLSI and microelectronics.**
<hr style="border: 3px solid black;">







  
  

 

  
