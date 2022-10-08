#include <MKL25Z4.h> //20.97MHz
#include <stdio.h>
#include <stdlib.h>
#include <math.h>


#define IMAGE_ARR_MAX 16000
//#define MAX_RIGHT_STEP 120 //////FIND THIS!!!!!!! 1300
#define UNIT_DOWN_STEP_AMOUNT 5 //////FIND THIS!!!!!!!
#define UNIT_RIGHT_STEP_AMOUNT 5

#define RIGHT_XY 0
#define LEFT_XY 1
#define UP_XY 2
#define DOWN_XY 3

#define UP_Z 1
#define DOWN_Z 0


void delay(volatile int n);

void initUART0(void);
void txUART(const char* str);
void txUARTL(const char* str, int strLen);
void txUARTN(int n);
void rxUARTConfig(char* configArr);
void rxUARTImage(char* configArr, char* imageArr);

void initPORTB(void);
void stepZMotor (int n, int dir); // 0: UP(CW), 1: (DOWN)CCW
void stepXYMotors (int n, int dir); // 0: RIGHT(CCW|CCW), 1: LEFT(CW|CW), 2: UP(CCW|CW), 3: DOWN(CW|CCW)
void moveinZ(int targetBoldness, int* curStepLvl);
void MotionLoop(char* imageArr, char* configArr, int* curZLvl); // int* restart);
	
int charsToint(char a, char b, char c);

char image[IMAGE_ARR_MAX];

int main()
{
	
	const char* strr = "Standby\n\r_";

	char config[9];
	
	int Nrestart = 0;
	int currentZLevel = 0;
	
	initUART0();
	initPORTB();
	
	
	//////////////////////Code for Movement Test ////////////////////
	
//	for (int i = 0; i < 100; i++)
//	{
//		moveinZ(0, &currentZLevel);
//		delay(5000);
//		stepXYMotors(10, RIGHT_XY);
//		delay(5000);
//		moveinZ(200, &currentZLevel);
//		delay(5000);
//		stepXYMotors(10, RIGHT_XY);
//		delay(5000);
//	}
//		
//	while(1);
	

	
	
//	moveinZ(100, &currentZLevel);
//	delay(5000);
//	moveinZ(125, &currentZLevel);
//	delay(5000);
//	moveinZ(150, &currentZLevel);
//	delay(5000);
//	moveinZ(175, &currentZLevel);
//	delay(5000);
//	moveinZ(200, &currentZLevel);
//	delay(5000);
//	moveinZ(250, &currentZLevel);
//	delay(5000);
//	moveinZ(225, &currentZLevel);
//	delay(5000);
//	moveinZ(250, &currentZLevel);
//	delay(50000);
//	
//	while(1);
//	
//	while (1)
//	{
////		stepXYMotors(400, UP_XY);
////		delay(5000);
////		stepXYMotors(200, RIGHT_XY);
////		delay(5000);
////		stepXYMotors(400, DOWN_XY);
////		delay(5000);
////		stepXYMotors(200, LEFT_XY);
////		delay(5000);
////		stepZMotor(256, UP_Z);
////		delay(5000);
////		stepZMotor(256, DOWN_Z);
////		delay(5000);
//	}
	
	//////////////////////////////////////////////////////////////
  
  
	
	txUART("Initialised UART\n\r_");
	delay(5000000);
	
	RESTART:
	
	txUART("Waiting for configs...\n\r_");
	delay(5000000);
	
	
	rxUARTConfig(config); //config[0]: Verticel Side, config[1]: Horizontal Side
	delay(10000000);
	txUART(config);
	txUART("\n\r_");
	delay(5000000);
	//txUARTN(charsToint((char)48, config[6], config[7]));
	
	txUART("Waiting for image...\n\r_");
	

	rxUARTImage(config, image);
	delay(5000000);
	txUART("Image Received\n\r_");
	delay(5000000);
	
	
	//txUART(image);
	//txUART("\n\r_");
	//delay(5000000);
	
	
//	Nrestart = charsToint("0", config[6], config[7]);
//	txUARTN(Nrestart);
	
	txUART("Starting to print...\n\r_");
	delay(5000000);
	MotionLoop(image, config, &currentZLevel);   //, &Nrestart);
	
	
	goto RESTART;
							
}



void delay(volatile int n)
{
	for (int i = 0; i < n; i++);
}

//

void initUART0()
{
	SIM->SCGC4 |= SIM_SCGC4_UART0(1);
	SIM->SOPT2 |= SIM_SOPT2_UART0SRC(1);
	
	UART0->C1  = 0x00; //8bit, no parity, 1 stop bit
	UART0->C2  = 0x00;
	UART0->C4 = 0x0F; // 15 over sampling
	
	UART0->BDL = 0x11; //4800 baudrate, for 15 OverSamplingRate
  UART0->BDH = 0x01;

	
	SIM->SCGC5 |= SIM_SCGC5_PORTA(1);
	PORTA_PCR1 |=  PORT_PCR_MUX(2); 
  PORTA_PCR2 |=  PORT_PCR_MUX(2); 
	
	
	UART0->C2  |= UART_C2_TE(1);
	UART0->C2  |= UART_C2_RE(1);
}



void initPORTB()
{
	
	SIM->SCGC5 |= SIM_SCGC5_PORTB_MASK;
	
	PORTB->PCR[1] &= ~PORT_PCR_MUX_MASK;
	PORTB->PCR[1] |= PORT_PCR_MUX(1); // Z-Axis Motor Step
	PORTB->PCR[2] &= ~PORT_PCR_MUX_MASK;
	PORTB->PCR[2] |= PORT_PCR_MUX(1); // Dir
	
	PORTB->PCR[3] &= ~PORT_PCR_MUX_MASK;
	PORTB->PCR[3] |= PORT_PCR_MUX(1); // Right Motor Step
	PORTB->PCR[8] &= ~PORT_PCR_MUX_MASK;
	PORTB->PCR[8] |= PORT_PCR_MUX(1); // Dir
	
	PORTB->PCR[9] &= ~PORT_PCR_MUX_MASK;
	PORTB->PCR[9] |= PORT_PCR_MUX(1); // Left Motor Step
	PORTB->PCR[10] &= ~PORT_PCR_MUX_MASK;
	PORTB->PCR[10] |= PORT_PCR_MUX(1);// Dir
	
	PTB->PDDR |= (1 << 1) | (1 << 2) | (1 << 3) | (1 << 8) | (1 << 9) | (1 << 10);
	
	PTB->PCOR |= (1 << 1) | (1 << 2) | (1 << 3) | (1 << 8) | (1 << 9) | (1 << 10);
}


void stepZMotor (int n, int dir) // 0: CW, 1: CCW
{
	
	if (dir)
		PTB->PCOR |= (1 << 2);
	else 
		PTB->PSOR |= (1 << 2);
	
	for (int i = 0; i < n; i++)
	{
		PTB->PSOR |= (1 << 1);	
		delay(5000);
	
		PTB->PCOR |= (1 << 1);
		delay(5000);
		
	}
}

void stepXYMotors (volatile int n, int dir) // 0: RIGHT(CCW|CCW), 1: LEFT(CW|CW), 2: UP(CCW|CW), 3: DOWN(CW|CCW)
{
	
	if (dir == 0)
	{
		PTB->PCOR |= (1 << 10);
		PTB->PCOR |= (1 << 8);
	}
	else if (dir == 1)
	{
		PTB->PSOR |= (1 << 10);
		PTB->PSOR |= (1 << 8);
	}
	else if (dir == 2)
	{
		PTB->PCOR |= (1 << 10);
		PTB->PSOR |= (1 << 8);
	}
	else if (dir == 3)
	{
		PTB->PSOR |= (1 << 10);
		PTB->PCOR |= (1 << 8);
	}
		
	
	for (int i = 0; i < n; i++)
	{
		PTB->PSOR |= (1 << 3);	
		PTB->PSOR |= (1 << 9);
		delay(2500);
	
		PTB->PCOR |= (1 << 3);	
		PTB->PCOR |= (1 << 9);
		delay(2500);
		
	}
}

void moveinZ(int targetBoldness, int* curStepLvl)
{
	
	//txUARTN(targetBoldness);
	
	if (targetBoldness == 1)
		targetBoldness = 200;
	else targetBoldness = 0;
	
	int targetStepLvl = round(((double)targetBoldness/255)*100); //30 is max steplvl
	//txUARTN(targetStepLvl);
	
	if (targetStepLvl == *curStepLvl)
		return;
	
	stepZMotor(abs(targetStepLvl-*curStepLvl), ((targetStepLvl>*curStepLvl)?0:1));
	
	*curStepLvl = targetStepLvl;
}

int charsToint(char a, char b, char c) 
{
	int result = ((int)a-48)*100 + ((int)b-48)*10 + ((int)c-48)*1;
	
	return result;
}


void MotionLoop(char* imageArr, char* configArr, int* curZLvl) //, int* restart)
{
	
	
	
	for (int i = 0; i < charsToint(configArr[0], configArr[1], configArr[2]); i++)
	{
		int l = charsToint(configArr[3], configArr[4], configArr[5]);
		for (int j = 0; j < l; j++)//for (int j = 0; j < ; j++)
		{
			
					
			moveinZ(((int)imageArr[i*l + j]-48), curZLvl);
			
			
			stepXYMotors(UNIT_RIGHT_STEP_AMOUNT, RIGHT_XY);
					
		}
	
		
		moveinZ(0, curZLvl);
	
		stepXYMotors(charsToint(configArr[3], configArr[4], configArr[5])*UNIT_RIGHT_STEP_AMOUNT, LEFT_XY);
		
		stepXYMotors(UNIT_DOWN_STEP_AMOUNT, DOWN_XY);
	
	}
	
	if (charsToint((char)48, configArr[6], configArr[7]))
	{
		return;
	}
	
	txUART("Completed the print\n\r_");
	
	while(1);
		
}


void txUARTL(const char* str, int strLen)
{
	for (int i = 0; i < strLen; i++)
	{
		UART0->D = str[i];
		
		while (!(UART0->S1 & UART_S1_TDRE_MASK));
   
	}
	
	while (!(UART0->S1 & UART_S1_TC_MASK));
	
	txUART("\n\r_");
}


void txUART(const char* str)
{
	for (int i = 0; str[i] != '_'; i++)
	{
		
		UART0->D = str[i];
		
		while (!(UART0->S1 & UART_S1_TDRE_MASK));
   
	}
	
	while (!(UART0->S1 & UART_S1_TC_MASK));
	
}

void txUARTN(int n)
{
	
	char str[11] = "_____";
	sprintf(str,"%ld", n);
	
	for (int i = 0; i < 11; i++)
	{
		
		UART0->D = str[i];
		
		while (!(UART0->S1 & UART_S1_TDRE_MASK));
   
	}
	
	while (!(UART0->S1 & UART_S1_TC_MASK));
	
	txUART("\n\r_");
	
}

void rxUARTConfig(char* configArr)
{
	
	for (int i = 0; i < 8; i++)
	{
		
		while (!(UART0->S1 & UART_S1_RDRF_MASK));
		
		configArr[i] = UART0->D;
		
	}
	
	configArr[8] = '_';
	
}

void rxUARTImage(char* configArr, char* imageArr)
{

	int size = charsToint(configArr[0], configArr[1], configArr[2]) * charsToint(configArr[3], configArr[4], configArr[5]);
	
	
	
//	static char* image[IMAGE_ARR_MAX]; // = malloc(size);
	
	for (int i = 0; i < size; i++)
	{
		while (!(UART0->S1 & UART_S1_RDRF_MASK));
		
		
		imageArr[i] = UART0->D;
		
			
	}
	
	//imageArr[size] = '_';
	
}
