//#define _LEGACY_HEADERS
#define _XTAL_FREQ 8000000

#define REDLED      PORTAbits.RA3   //REDLED_output
#define BLUELED     PORTAbits.RA4   //BLUELED_output
#define GLEENLRD    PORTAbits.RA2   //GLEENLED_output
#define REDBUTTON   PORTBbits.RB1   //REDLED_input
#define BLUEBUTTON  PORTBbits.RB2   //BLUELED_input
#define GLEENBUTTON PORTBbits.RB0   //GLEENLED_input
#define BRIGHT      PORTAbits.RA1   //PWM_+
#define DIM         PORTAbits.RA0   //PWM_-
/*
* File:   main02.c
* Author: c-hiy_000
*
* Created on 2014/04/20, 13:27
*/
#include <xc.h>
#include <pwm.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#pragma config CP = OFF //プログラムコードプロテクションOFF
#pragma config CCPMX = RB0
#pragma config CPD = OFF //データーコードプロテクションOFF
#pragma config LVP = OFF //低電圧プログラム書き込みOFF
#pragma config BOREN = OFF //4VブラウンアウトリセットOFF
#pragma config PWRTE = ON //パワーアップタイマーON
#pragma config IESO = ON
#pragma config FCMEN = ON
#pragma config FOSC = INTOSCIO //内部発振使用
#pragma config MCLRE = OFF //マスタークリアー機能OFF(RA5入出力として使用)
#pragma config WDTE = OFF //ウォッチドッグタイマーOFF


/*
概要：PWM機能でLEDの明るさ制御

使用マイコン：16F88

発振器： Clock: 8.0MHz 内蔵発振

Device Flags:
_CP_OFF    _CCP1_BR0   _DEBUG_OFF  _WRT_ENABLE_OFF
_CPD_OFF _LVP_OFF    _BODEN_OFF _MCLRE_OFF
_PWRTE_ON _WDT_OFF    _INTRC_IO
IESO_ON__CFG2   _FCMEN_ON__CFG2

MPLABX
xc8 v1.31
*/


void main() {

	unsigned short int i;
	int gleen, red, blue = 0;

	OSCCON = 0b01110000; //内蔵発振器 8MHz使用に設定

	// A/D Pref // ANALOG=1, DIGITAL=0
	ANSEL = 0b00000000;

	//１インプット,０アウトプット
	TRISA = 0b00000011;  //RA1_bright,RA0_dim
	TRISB = 0b00000111;  //RB0_green,RB1_red,RB2_blue

	//ポートの初期化
	PORTA = 0b00000000;
	PORTB = 0b00000000;

	//PWM mode//
	//initPWM1(5000);   //Initialize PWM module at 5KHz:
	initPWM1(5000);
	setPWM1Duty(127);
	while (1) {
		if (GLEENBUTTON)
			//         GLEENLRD = (gleen = ~gleen);
			GLEENLRD = (gleen = gleen ^ 1);
		else if (REDBUTTON)
			//         REDLED = (red = ~red);
			REDLED = (red = red ^ 1);
		else if (BLUEBUTTON)
			//         BLUELED = (blue = ~blue);
			BLUELED = (blue = blue ^ 1);
		else;

		if ((BRIGHT)&(i<255)){
			i++;
			setPWM1Duty(i);
			__delay_me(50);
		}
		else if ((DIM)&(i>0)){
			i--;
			setPWM1Duty(i);
			__delay_me(50);
		}
		else;
	}
}
