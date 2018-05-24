/***************************************************************
*                    ARQUIVOS DE DEFINIÇÕES                    *
***************************************************************/


#INCLUDE   <18F4550.h>         //MICROCONTROLADOR UTILIZADO


#FUSES XT,HS,NOLVP,NOWDT,PUT
                               //CONFIGURAÇÕES
                               //OSCILADOR: XT
                               //LVP: OFF
                               //WDT: OFF

#DEVICE ADC=10    //CONFIGURA CANAL ANALOGICO PARA 10 BITS


#USE DELAY(CLOCK=15M)      
                               // CONFIGURA O CLOCK DO
                               //CRISTAL
                               
                            
#INCLUDE <LCD.C>                          
                               
                               
#DEFINE LCD_ENABLE_PIN  PIN_D0  
#DEFINE LCD_RS_PIN      PIN_D1 
#DEFINE LCD_RW_PIN      PIN_D2 
#DEFINE LCD_DATA4       PIN_D4  
#DEFINE LCD_DATA5       PIN_D5
#DEFINE LCD_DATA6       PIN_D6   
#DEFINE LCD_DATA7       PIN_D7 
 

/***************************************************************
*          VARIÁVEIS/CONSTANTES/ENTRADAS/SAIDAS                *
***************************************************************/
/*DEFINIÇÃO DOS NOMES E ENDEREÇOS DE TODAS AS VARIÁVEIS,       *
*CONSTANTES,ENTRADAS E SAIDAS UTILIZADAS PELO SISTEMA.         */


FLOAT TEN=0, COR=0, POT=0, TARIFA=0, POTSOMA=0, tarifasoma=0, bana=0, banver=0, banver2=0, T=0, X5=0, X4=0, X3=0, X2=0, X1=0; 
INT16 segundo=0, minuto=0, hora=0, dia=1, mes=1, TELA=0, aux=0, aux1=0, BANDEIRAS=0, EA=0, a1=0, a2=0, a3=0, a4=0, a5=0, Q=0;




/***************************************************************
*                          FUNÇÕES                             *
***************************************************************/







#INT_TIMER1              
void temp1s(void)        
{


//========================== relógio e data ===========================
    
    Q++;
    TELA++;
    if (Q==10)
    {
   segundo++;
   Q=0;
    }
   if (segundo==60)
   {
      minuto++;
      segundo=0;
      POTSOMA=POTSOMA+POT;     // Soma o valor de potencia medido a cada minuto
      BANDEIRAS=BANDEIRAS+POT;    /* muda variável bandeiras para a 
                                soma da tarifa extra a cada minuto*/
      
   }
   
   if (minuto==60)
   {
  
   minuto=0;
   hora++;
  
   }
   
   if (hora>24)
   {
   hora=0;
   dia++;
   
   }
   
   if (dia>30)
   {
   dia=1;
   mes++;
   POTSOMA=0;   //zera a variável POTSOMA quando completa 1 mês
   
   LCD_GOTOXY(1,9);      
   PRINTF(LCD_PUTC,"   ");  
   }
   
   if (mes>12)
   {
   mes=1; 
   }
   
   IF (TELA==160)
   {
   TELA=0;
   }


   
   set_timer1 (3036);    //Preload do Timer1
}


/***************************************************************
*                        ROTINA PRINCIPAL                      *
***************************************************************/

VOID MAIN(VOID)
{

SET_TRIS_A(0B00000000);
SET_TRIS_B(0B10000000);
SET_TRIS_C(0B0000111);
SET_TRIS_D(0B00000000);    
SET_TRIS_E(0B000);    

lcd_init(); 


    
   SETUP_ADC_PORTS(AN0_TO_AN1);  
   SETUP_ADC(ADC_CLOCK_DIV_8);


      
DELAY_MS(5);

//====================== timer config ========================


   
   setup_timer_1 (T1_INTERNAL | T1_DIV_BY_8);   
   set_timer1 (3036);                           
   enable_interrupts(INT_TIMER1);               
   enable_interrupts(global); 


lcd_gotoxy(1,1);
printf(lcd_putc, "TAR:");

WHILE(1)
{

//===================== DEFINIÇÃO TARIFA ================

IF (POTSOMA>=0 && POTSOMA<30)
{
TARIFA=0.003;                     //kwh=0.18 kw/min=0.003
}
IF (POTSOMA>31 && POTSOMA<50)     
{     
TARIFA=0.0053;                     //kwh=0.32 kw/min=0.0053
}
IF (POTSOMA>51 && POTSOMA<100)
{
TARIFA=0.0065;                     //kwh=0.39 kw/min=0,0065
}
IF (POTSOMA>101 && POTSOMA<220)
{
TARIFA=0.0098;                      //Kwh=0.59 kw/min=0.0098
}

IF (POTSOMA>=220)
{
TARIFA=0.011;                        //Kwh=0.66 Kw/min=0.011
}

//===================== CALCULOS DE TARIFA =====================


//========================= TENSÃO =================================
   SET_ADC_CHANNEL(0);                             //le entrada adc 0
     DELAY_MS(1);             


     EA=READ_ADC();
     T=(5.0/1023.0)*EA;   
     
     a1=T;
     a2=T*T;
     a3=a2*T;
     a4=a2*a2;
     a5=a3*a2;
    
     
     X5=(0.34*a5);
     
     X4=(-4.19*a4);
    
     X3=(19.39*a3);

     X2=(-40.14*a2);
   
     X1=(71.53*a1);
   
     
     TEN=(X5+X4+X3+X2+X1);
     
     
//========================CORRENTE======================================     
      
     SET_ADC_CHANNEL(1);                           //Le entrada adc 1 
     DELAY_MS(1); 
  
  
     
     COR= (READ_ADC() - 512.0) * (30.0/512.0);
      
IF (COR<=0)
{
COR=0;
}
  
     POT= TEN*COR/1000;
     
     tarifasoma= POTSOMA * TARIFA;
 
 
 

   IF (BANDEIRAS>0)
   
   {
     bana=tarifasoma+  0.00033 * BANDEIRAS;           //CALC BANDEIRA AMARELA
     banver=tarifasoma+ 0.0005 * BANDEIRAS;          //CALC BANDEIRA VERMELHA I
     banver2=tarifasoma+ 0.00058 * BANDEIRAS;       //CALC BANDEIRA VERMELHA II
     BANDEIRAS=0;
   
   
   /*
  acrécimo 1kw/min = 0.02   acrécimo 1kw/min = 0.00033

  acrécimo 1kw/min = 0.03   acrécimo 1kw/min = 0.0005

  acrécimo 1kw/min  =0.035   acrécimo 1kw/min = 0.00058
   
   */
   
   
   }
//=========================== IMPRIME NA TELA =======================


   IF (TELA<40)
   {
// BANDEIRA VERDE
     LCD_GOTOXY(12,1);
     PRINTF(LCD_PUTC, "$");
     
     LCD_GOTOXY(5,1);      
     PRINTF(LCD_PUTC,"%4.2F", tarifasoma);   
     LCD_GOTOXY(13,1);      
     PRINTF(LCD_PUTC,"VERD");
   } 
//BANDEIRA AMARELA
   IF (TELA>40 && TELA<=80)
   {
    

     LCD_GOTOXY(12,1);
     PRINTF(LCD_PUTC, "$");
     
     LCD_GOTOXY(5,1);      
     PRINTF(LCD_PUTC,"%4.2F",bana);   
     LCD_GOTOXY(13,1);      
     PRINTF(LCD_PUTC,"AMAR");
      
    
  }
  
//BANDEIRA VERMELHA I
  

     IF (TELA>80 && TELA<=120)
   {
    

     LCD_GOTOXY(12,1);
     PRINTF(LCD_PUTC, "$");
     
     LCD_GOTOXY(5,1);      
     PRINTF(LCD_PUTC,"%4.2F",banver);   
     LCD_GOTOXY(13,1);      
     PRINTF(LCD_PUTC,"VER1");
        
   }
  
//BANDEIRA VERMELHA II
  

     IF (TELA>120 && TELA<=160)
   {
    

     LCD_GOTOXY(12,1);
     PRINTF(LCD_PUTC, "$");
     
     LCD_GOTOXY(5,1);      
     PRINTF(LCD_PUTC,"%4.2F",banver2);   
     LCD_GOTOXY(13,1);      
     PRINTF(LCD_PUTC,"VER2");
     
   } 
  
  
  
//===========================Botões=========================

  
  IF (INPUT(PIN_C0)==1)
 {
  aux=1;                         //mudar
  
 }
  IF (aux==1 && aux1==0)            // mudar hora
{
 
     LCD_GOTOXY(1,2);                         
     PRINTF(LCD_PUTC,"%02LU:", hora);
     delay_ms(50);
     LCD_GOTOXY(1,2);                         
     PRINTF(LCD_PUTC,"  " );
     delay_ms(50);
    
 IF (INPUT(PIN_C1)==1)             //inc hora
 {
    
 hora++;
 delay_ms(100);
 }
 IF (INPUT(PIN_C2)==1)             //dec hora
 {
 hora--;
 delay_ms(100);
 }
 IF (hora>24)
 {
 hora=0;
 }
 IF (INPUT(PIN_B7)==1)              // OK 
  {
  aux1=1;
  delay_ms(100);
  }
 
}

IF (aux==1 && aux1==1)               //mudar minuto

{
 
  
     LCD_GOTOXY(4,2);                         
     PRINTF(LCD_PUTC,"%02LU:", minuto);
     delay_ms(50);
     LCD_GOTOXY(4,2);                         
     PRINTF(LCD_PUTC,"  " );
     delay_ms(50);
     
 
 IF (INPUT(PIN_C1)==1)             //inc minuto
 {
 minuto++;
 delay_ms(100);
 }
  IF (INPUT(PIN_C2)==1)            //dec minuto
 {
 minuto--;
 delay_ms(100);
 }
 IF (minuto>59)
 {
 minuto=0;
 }
 
 IF (INPUT(PIN_B7)==1)              // OK 
  {
  aux1=2;
  delay_ms(100);
  }

}
IF (aux==1 && aux1==2)                //mudar segundo

{

 
     LCD_GOTOXY(7,2);                         
     PRINTF(LCD_PUTC,"%02LU", segundo);
     delay_ms(50);
     LCD_GOTOXY(7,2);                         
     PRINTF(LCD_PUTC,"  " );
     delay_ms(50);

 
 IF (INPUT(PIN_C1)==1)             //inc segundo
 {
 segundo++;
 delay_ms(100);
 }
   IF (INPUT(PIN_C2)==1)            //dec segundo
 {
 segundo--;
 delay_ms(100);
 }
 IF (segundo>59)
 {
 segundo=0;
 }
  IF (INPUT(PIN_B7)==1)              // OK 
  {
  aux1=3;
  delay_ms(100);
  }
 
 
}

IF (aux==1 && aux1==3)              //mudar segundo

{

 
     LCD_GOTOXY(11,2);                      
     PRINTF(LCD_PUTC,"%02LU", dia);
     delay_ms(50);
     LCD_GOTOXY(11,2);                      
     PRINTF(LCD_PUTC,"  " );
     delay_ms(50);

 IF (INPUT(PIN_C1)==1)             //inc dia
 {
 dia++;
 delay_ms(100);
 }
 
   IF (INPUT(PIN_C2)==1)          //dec dia
 {
 dia--;
 delay_ms(100);
 }
 IF (INPUT(PIN_C2)==1)         //dec dia
 {
 dia--;
 delay_ms(100);
 }
 
 IF (dia>31)
 {
 dia=0;
 }
  IF (INPUT(PIN_B7)==1)              // OK 
  {
  aux1=4;
  delay_ms(100);
  }
}
IF (aux==1 && aux1==4)             //mudar mes

{
 
     LCD_GOTOXY(14,2);                       
     PRINTF(LCD_PUTC,"%02LU", mes);
     delay_ms(50);
     LCD_GOTOXY(14,2);                         
     PRINTF(LCD_PUTC,"  " );
     delay_ms(50);



 IF (INPUT(PIN_C1)==1)           //inc mes
 {
 mes++;
 delay_ms(100);
 }
   IF (INPUT(PIN_C2)==1)         //dec mes
 {
 mes--;
 delay_ms(100);
 }
 IF (mes>31)
 {
 mes=0;
 }

IF (INPUT(PIN_B7)==1)              // OK 
  {
  
  aux=0;
  aux1=0;
  delay_ms(100);  
  }

}
 
//===================data====================
     
     lcd_gotoxy(1,2);                         
     printf(lcd_putc,"%02LU:", hora);
     
     lcd_gotoxy(4,2);                         
     printf(lcd_putc,"%02LU:", minuto);
     
     lcd_gotoxy(7,2);                         
     printf(lcd_putc,"%02LU", segundo);
     
     lcd_gotoxy(11,2);                         
     printf(lcd_putc,"%02LU/", dia);
     
     lcd_gotoxy(14,2);                         
     printf(lcd_putc,"%02LU", mes);
     
     
}

}
/***************************************************************
*                       FIM DO PROGRAMA                     *
***************************************************************/
